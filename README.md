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
