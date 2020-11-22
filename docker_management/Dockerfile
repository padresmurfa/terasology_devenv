FROM --platform=linux/amd64 openjdk:8

# the maintainer of this file is David Brandt, aka Padre Smurfa
LABEL maintainer="padresmurfa@gmail.com"

############################################################################################
# HOME
#   user home directories should not be accessible to other users by default.
#   not important really, just a principle
RUN sed -i 's/DIR_MODE=0755/DIR_MODE=0750/g' /etc/adduser.conf

############################################################################################
# APT
#   we would like apt to be up-to-date at the time
#   of creating the container
############################################################################################

RUN apt-get update

############################################################################################
# SYSLOG
#   we would like to have syslog functioning properly,
#   and would like our logs to be clean of errors
#   from the get-go, thus we disable the kernel log
############################################################################################

RUN apt-get --assume-yes install rsyslog \
 && sed -i '/imklog/s/^/#/' /etc/rsyslog.conf \
 && service rsyslog start

############################################################################################
# ROOT
#   root would like to use /bin/bash as the default shell,
#   be able to create sudoers, have less installed
############################################################################################

RUN chsh --shell /bin/bash \
 && apt-get install less \
 && apt-get install --assume-yes sudo

############################################################################################
# USER
#  we do not wish to run everything as root, thus we create a user for our purposes. Some
#  interesting properties of this user:
#  ... has the same username, uid and gid as the user that runs this script
#  ... is a sudoer, and thus does will not be prompted for a password when calling sudo
#  ... has a home directory at /home/<username>
#  ... has /bin/bash as the default shell
############################################################################################

ARG USERNAME
ARG UID
ARG GID
RUN addgroup $USERNAME \
 && adduser --shell /bin/bash --uid $UID --gid $GID $USERNAME \
 && usermod -aG sudo $USERNAME \
 && echo "$USERNAME     ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
 && chsh --shell /bin/bash
USER $UID:$GID

############################################################################################
# SDKMAN
#   gradle is installed via sdkman
############################################################################################

# sdkman requires unzip and zip to be installed
RUN sudo apt-get install unzip \
 && sudo apt-get install --assume-yes zip

# install sdkman, as per instructions: https://sdkman.io/install
# note that 'sdk' and 'SDKMAN_DIR' are installed via .bashrc, thus
# we need to run bash in interactive mode
RUN ["/bin/bash", "-ci", \
    "curl -s \"https://get.sdkman.io\" | sudo -i bash", \
    "&&", \
    "sudo -i source \"${SDKMAN_DIR}/bin/sdkman-init.sh\"", \
    "&&", \
    "sdk version"]

############################################################################################
# GRADLE
#   Terasology is built via gradle
############################################################################################

# install gradle, as per https://gradle.org/install/#helpful-information
ARG GRADLE_VERSION="6.7.1"
# note that we are using the sdk command again, so we also need to run this
# command using an interactive bash shell
RUN ["/bin/bash", "-ci", "sudo -i sdk install gradle ${GRADLE_VERSION}"]

############################################################################################
# GIT
#   Terasology is stored in git
############################################################################################

RUN sudo apt-get install git

############################################################################################
# SCRIPTS
#   install a few scripts to get things started
############################################################################################

COPY --chown=$UID:$GID copy_to_container/ /home/$USERNAME/copied_from_host/

############################################################################################
# INITIAL STATE
############################################################################################

# the initial directory will always be the user's home
# directory
WORKDIR /home/$USERNAME

# Note that some actions taken in this Dockerfile, such as starting the rsyslogd service,
# are not persistent and thus need to be performed upon launch, akin to as if they were
# registered in init.d.
# To facilitate that, any necessary start-up actions for the container that are not part of
# the docker image will be orchestrated from .start_docker_container.
ENTRYPOINT ["/bin/bash", "-ci", "./copied_from_host/.start_docker_container"]
