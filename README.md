# docker-compose-ignition-robotics

## Usage

Start

```shell
$ docker-compose -f <local or remote>/<intel or nvidia>/docker-compose.yml up
```

End

```shell
$ docker-compose -f <local or remote>/<intel or nvidia>/docker-compose.yml down
```

Removal

```shell
$ docker-compose -f <local or remote>/<intel or nvidia>/docker-compose.yml down -v --rmi all
```

#### Example

How to run it locally and with Intel graphics.

```shell
$ docker-compose -f local/intel/docker-compose.yml up
```

## Troubleshooting

This troubleshooting is for the following environment versions or higher.
More information is available from [docker docs](https://docs.docker.com).

| | version |
| :---: | :---: | 
| docker | 19.03 |
| docker-compose | 1.19 |

### 1. docker-compose

If an error occurs while executing docker-compose.

**Error message**
```shell
Unknown runtime specified nvidia
```
Make sure that the following commands can be executed.

```shell
$ docker run -it --rm --gpus all ubuntu nvidia-smi
```
If you can't run it, install **nvidia-container-runtime** by [script](https://github.com/NaokiTakahashi12/setup-scripts-for-docker).
If the same error occurs after installation, please make sure the following settings are correct.

```shell
$ cat /etc/docker/daemon.json 
{
    "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}
```
If this **path** is not set correctly to **nvidia-container-runtime**, you cannot read the **runtime** settings.

When you change these settings, please restart daemon as follows.

```shell
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker.service
```

### 2. xwindow

Unable rendering window.

**Error message**
```shell
qt.qpa.screen: QXcbConnection: Could not connect to display :0
```

Use the following command to check if the GUI is displayed.

```shell
$ xhost + local:root
$ docker-compose -f <path to docker-compose.yml> up
$ xhost - local:root
```
**Note** : This method is not recommended because it may cause security problems.

When the GUI is displayed by this command, the reason is that the following files have not been generated normally.

```shell
/tmp/.docker.xauth
```
Generate the xauth file manually using the following command.

```shell
$ xauth nlist $DISPLAY | sed -e 's/^..../ffff/' | xauth -f /tmp/.docker.xauth nmerge -
```

### 3. Rendering

The window is displayed, but it is completely black or white.

If the nvidia GPU driver is installed, make sure you are using the ```nvidia/docker-compose.yml```.

Change the ```command``` of the docker-compose.yml file as follows.

```yaml
command: /bin/sh -c "ign gazebo -v4 tunnel.sdf"
```
to
```yaml
command: /bin/sh -c "ign gazebo -v4 lights.sdf"
```

If the window appears normally in the above operation, the version of OpenGL used to display the screen may be older.

You can check the version of OpenGL with the following command.

```shell
$ glxinfo | grep :
```

The **ogre2** rendering engine used in ignition gazebo requires OpenGL version **3.3** or higher.

If the OpenGL version is lower than **3.3**, you can change the rendering engine to **ogre**.
