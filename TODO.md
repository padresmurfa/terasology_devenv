# Improvements

## Overlay filesystem
It would be great to figure out a way have the src/Terasology directory
be mounted into the docker container as the lower directory of an overlay
filesystem. This seems like it may be impossible to do under Docker Desktop
for Mac. The upper directory would then be stored on a docker volume, with
the merged directory being exposed in the container e.g. as ~/src/Terasology.

pros: faster builds. build artifacts would not sully the workspace.

cons: perhaps not posssible. perhaps intellij and the dev need those artifacts
      in the workspace.
