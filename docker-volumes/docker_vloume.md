# Docker Storage 

Categories of data storage:
* Non-persistent
    * Local storage
    * Data that is ephemeral
    * Every container has it
    * Tied to the lifecycle of the contain
* Persistent
    * Volumes
        * Volumes are decoupled from containers

## Non-persistent Data

Non-persistent data:

* By default all container use local storage
* Storage locations:
    * Linux: /var/lib/docker/[STORAGE-DRIVER]/
    * Windows: C:\ProgramData\Docker\windowsfilter\
* Storage Drivers:
    * RHEL uses overlay2.
    * Ubuntu uses overlay2 or aufs.
    * SUSE uses btrfs.
    * Windows uses its own.

## Persistent Data Using Volumes

Volumes:
* Use a volume for persistent data:
    * Create the volume first, then create your container.
* Mounted to a directory in the container
* Data is written to the volume
* Deleting a container does not delete the volume
* First-class citizens
* Uses the local driver
* Third party drivers:
    * Block storage
    * File storage
    * Object storage
* Storage locations:
    * Linux: /var/lib/docker/volumes/
    * Windows: C:\ProgramData\Docker\volumes