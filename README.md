# About

An OSX development environment for Terasology that is run in a Docker container, isolated from the host machine.

## Setup

1. install `Docker Desktop` for Mac
2. clone this repo via `git@github.com:padresmurfa/terasology_devenv.git`
3. run `./docker_management/create_docker_image`
4. run `./docker_management/start_docker_container`
    * you now have a running docker container, within a virtual machine running on your Mac
    
## Use

1. run `./docker_management/login_to_docker_container`
    * you are now located in a bash shell in the docker container
2. run `./copied_from_container/build_terasology`
    * you have now built Terasology

## Edit Code

1. intall `IntelliJ IDEA` for Mac
2. open IntelliJ project found at `src/Terasology`
    * this folder is shared with your docker container, thus anything you change will
    be immediately available in the container
3. when you change code, buildit in the docker container, not in the host

 
