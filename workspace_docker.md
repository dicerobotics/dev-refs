# Workspace for AI/ML Development
This document guides on setting up a workspace environment for containerized AI/ML development.

__Recommendations__ (as of June 2024)
1. Hardware
  - CPU: 13th Gen Intel(R) Core(TM) i7-13700K
  - GPU: NVIDIA GeForce RTX 4070
  - Memory: 32GB

2. Operating System
  - Configuration: Dual Drive Dual Boot (One drive connected at a time during OS installation)
    - Virtual Machines (VM), Windows Subsystem for Linux (WSL), etc. not recommended
  - Linux: Ubuntu 22.04 LTS (Jammy Jellyfish)
  - Window: Windows 11

## Install NVIDIA drivers

__Manual Installation__

From Ubuntu 20.02, the Nvidia drivers can be installed in the Ubuntu OS installation process using `sudo ubuntu-drivers install` or `sudo ubuntu-drivers install nvidia:535`. However, We recommend installing it manually to control (fully) what goes inside the machine. To manually install the drivers, go to the [official driver page](https://www.nvidia.com/download/index.aspx?ref=blog.roboflow.com) for NVIDIA GPUs and download the relevant driver package. The following instructions are for __NVIDIA GeForce RTX 4070__ and __Linux 64-bit__.

``` shell
# Download the relevant driver
wget "https://us.download.nvidia.com/XFree86/Linux-x86_64/550.107.02/NVIDIA-Linux-x86_64-550.107.02.run"
```
Once you have downloaded it, you can run the installer:
``` shell
# Change to the directory containing the drivers
chmod +x ./NVIDIA-Linux-x86_64-550.107.02.run
sudo ./NVIDIA-Linux-x86_64-550.107.02.run
```
You'll need to fulfill dependency requirements depending on the current configuration of your system (e.g. gcc, make, etc.).
Follow the installation steps. `reboot` and you should be able to run `nvidia-smi` in the terminal to see the output with driver information.



__Installation with Package Manager__

In case we need to install with the Ubuntu package manager `apt`, sse the following script

Remove previous NVIDIA installation (if any)
You should only use this command if it's necessary. It might corrupt your system.
``` shell
!sudo apt autoremove nvidia* --purge
```

Check Ubuntu devices
``` shell
ubuntu-drivers devices
```
You will install the NVIDIA driver whose version is tagged with __recommended__


Install NVIDIA drivers
My __recommended__ version is 535, adapt to yours

``` shell
sudo apt install nvidia-driver-535
```

Reboot & Check
``` shell
reboot
```
after restarting verify that the following command works
``` shell
nvidia-smi
```

```
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.183.01             Driver Version: 535.183.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 4070        Off | 00000000:01:00.0  On |                  N/A |
|  0%   43C    P8              16W / 200W |    337MiB / 12282MiB |      0%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      1844      G   /usr/lib/xorg/Xorg                          126MiB |
|    0   N/A  N/A      1979      G   /usr/bin/gnome-shell                        161MiB |
|    0   N/A  N/A      3941      G   ...seed-version=20240819-050153.535000       41MiB |
+---------------------------------------------------------------------------------------+
```


## CUDA, cuDNN for Machine Learning (Not required)
- It's recommended to use isolation techniques (e.g. Virtual Machines, Packaging and environments with conda, containerization with Docker, etc.) for development.
- We do not need to install CUDA and cuDNN on the local machine if we work under containerized development environments using Docker for NVIDIA GPUs.
- We'll install __NVIDIA Container Toolkit__ instead and use container images provided by NVIDIA.
- The container images provided by NVIDIA already contain CUDA and cuDNN libraries.



## Install [Docker (Engine)](https://docs.docker.com/engine/install/ubuntu/)
- We need the Docker Engine, not the Docker Desktop
- Distro maintainers provide unofficial distributions of Docker packages in APT. We must uninstall these packages before we can install the official version of Docker Engine. The unofficial packages to uninstall are `docker.io`, `docker-compose`, `docker-compose-v2`, `docker-doc`, and `podman-docker`.
- Docker Engine depends on `containerd` and `runc`. Docker Engine bundles these dependencies as one bundle in `containerd.io`.
- If the `containerd` or `runc` were installed previously, we need to uninstall them to avoid conflicts with the versions bundled with Docker Engine.

__Uninstall conflicting packages__

``` shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
`apt-get` might report that you have none of these packages installed.

Images, containers, volumes, and networks stored in `/var/lib/docker/` aren't automatically removed when we uninstall Docker. For a clean installation, we suggest to clean up any existing data.

```
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```
We have to delete any edited configuration files manually.

__Set up Docker's `apt` repository__

``` shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
__Install Docker Packages__ 
  1. Latest:
``` shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
  2. Specific Version:
``` shell
# List the available versions:
apt-cache madison docker-ce | awk '{ print $3 }'

5:27.1.1-1~ubuntu.24.04~noble
5:27.1.0-1~ubuntu.24.04~noble
...
```
Select the desired version and install it:
``` shell
VERSION_STRING=5:27.1.1-1~ubuntu.24.04~noble
sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

__Verify Docker Installation__

You can test your docker installation by running the `hello-world` container:
``` shell
sudo docker run --rm hello-world
```
The `--rm` flag tells docker to remove the container once done. Our output should look like below.

``` shell
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

__Create Docker group__ (to manage USER access)

``` shell
# Create Docker group
sudo groupadd docker

# Add USER to the Docker group
sudo usermod -aG docker $USER

# Restart docker
sudo systemctl restart docker #or
reboot
```

__Additional Installations__

Install Git
``` shell
sudo apt-get update
sudo apt install git-all
git --version #Check installation and version
```

Install Miniconda (Optional):
Though we mostly use docker for development, we sometimes need a conda-based environment for temporary installations or temporary development.

``` shell
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh

# Initialize newly-installed Miniconda
~/miniconda3/bin/conda init bash


# Create Environment
conda info --env
conda create --name ENV_NAME
conda activate ENV_NAME
conda config --append channels CHANNEL_NAME # adds the new channel (e.g. 'conda-forge', 'anaconda', 'nvidia', etc.) to the bottom, making it the lowest priority

# Set channel priority (optional)
conda config --describe channel_priority
```

## Install NVIDIA Container Toolkit
It allows users to build and run GPU-accelerated containers. Please take a look at the installation instructions below. Please have a look [here](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) for further reference, if needed.

``` shell
# Configure the production repository:
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```
Optionally, configure the repository to use experimental packages:
``` shell
sed -i -e '/experimental/ s/^#//g' /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

Then

``` shell
# Update the packages list from the repository:
sudo apt-get update

# Install the NVIDIA Container Toolkit packages:
sudo apt-get install -y nvidia-container-toolkit

# Configure the container runtime by using the `nvidia-ctk` command
sudo nvidia-ctk runtime configure --runtime=docker

# Restart the Docker Daemon
sudo systemctl restart docker
```
The `nvidia-ctk` command modifies the `/etc/docker/daemon.json` file on the host. The file is updated so that Docker can use the NVIDIA Container Runtime.



__Verfiy Installation__
``` shell
docker run --rm --gpus all nvidia/cuda:11.0.3-base-ubuntu20.04 nvidia-smi
```
And you should see the correct output from nvidia-smi inside the container. In my case:

``` shell
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 535.183.01             Driver Version: 535.183.01   CUDA Version: 12.2     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 4070        Off | 00000000:01:00.0  On |                  N/A |
|  0%   42C    P8              16W / 200W |    329MiB / 12282MiB |      1%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
+---------------------------------------------------------------------------------------+
```

`--gpus` is used to specify which GPU the container should see, `all` means "all of them". If you want to expose only one you can pass its `id` `--gpus 1`. You can also specify a list of GPUs to use, `--gpus` "device=1,2"





## Run GPU-accelerated containers

- Nvidia provides Docker images with different [versions](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/pytorch?ref=blog.roboflow.com) of cuda, cuDNN, and Pytorch. For a complete view of the supported software and specific versions that are packaged with the frameworks based on the container image, see the [Frameworks Support Matrix](https://docs.nvidia.com/deeplearning/frameworks/support-matrix/index.html). They are part of the [NGC Catalog](https://docs.nvidia.com/ngc/gpu-cloud/ngc-catalog-user-guide/index.html?ref=blog.roboflow.com#what-is-nvidia-ngc), a curated set of GPU-optimized software for AI, HPC, and Visualization containers.

Furhter information about a specific release of container can be found at the [releases list](https://docs.nvidia.com/deeplearning/frameworks/pytorch-release-notes/index.html?ref=blog.roboflow.com). We'll use Version 23.08 in this guide.

Please consult the NVIDIA conatiner [documentation](https://docs.nvidia.com/deeplearning/frameworks/user-guide/index.html?ref=blog.roboflow.com#runcont) and [NGC Catalog User Guide](https://docs.nvidia.com/ngc/gpu-cloud/ngc-catalog-user-guide/index.html?ref=blog.roboflow.com) for more details.

``` shell
docker run --gpus all -it --rm nvcr.io/nvidia/pytorch:23.08-py3
```

`-it` runs the container in interactive mode and attaches it to the current shell. `--rm` tells docker to destroy the container after we are done with it.

After pulling the image, docker will run the container and you will have access to bash from inside it.

Nvidia is suggesting running the container with additional flags to improve performance, let's kill the container (ctrl + c) and re-run it with the suggested flags.

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it --rm nvcr.io/nvidia/pytorch:23.08-py3
```
We can check everything is working by hopping inside the `python` console:

``` python
import torch
torch.cuda.is_available()
torch.backends.cudnn.version()
```
We can see CUDA is available and the cuDNN version is the correct one.

`--ipc` IPC (POSIX/SysV IPC) provides a way to speed up inter-process communication. Since PyTorch Dataloaders with `num_workers > 1` will use different processes, we need to set it to host.

`--ulimit memlock=1` calls the `ulimit` linux command and set `memlock=-1` means no limit for memory lock.

`--ulimit stack=67108864`  sets the stack size specific to the host machine.


## Run Code (using bash)
Assuming you have a folder project with a file train.py
```
|-- project
    |-- train.py
```
For example, train.py contains the famous [mnist](https://github.com/pytorch/examples/blob/main/mnist/main.py?ref=blog.roboflow.com) example from PyTorch.

We can make project available to our container by:

``` shell
docker run ... -v /<our_dir>/project:/workspace/project ...
```

Remember that volumes need an absolute path.

`-v` creates a [volume](https://docs.docker.com/storage/volumes/?ref=blog.roboflow.com) mapping from the host to the container. By default, the Nvidia container uses the /workspace directory. Assuming you have a folder project with a file train.py.

Putting everything together:

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it -v $(pwd)/project:/workspace/project --rm nvcr.io/nvidia/pytorch:23.08-py3
```

We can see our code inside `/workspace/project` and we can directly run the code by calling `python`

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it -v $(pwd)/project:/workspace/project --rm nvcr.io/nvidia/pytorch:23.08-py3 python ./project/train.py
```

We'll see something like:

```
Downloading http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz
...
Extracting ../data/MNIST/raw/t10k-labels-idx1-ubyte.gz to ../data/MNIST/raw
Train Epoch: 1 [0/60000 (0%)]   Loss: 2.283439
Train Epoch: 1 [640/60000 (1%)] Loss: 1.827201
```



## Install VS Code

First of all, we need to install it. You can follow the [official installation guide](https://code.visualstudio.com/docs/setup/linux?ref=blog.roboflow.com) or follow following script.

Install repository and key
``` shell
sudo apt-get install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" |sudo tee /etc/apt/sources.list.d/vscode.list > /dev/null
rm -f packages.microsoft.gpg
```

Update the package cache and install packages
``` shell

sudo apt install apt-transport-https
sudo apt update
sudo apt install code # or code-insiders
```

Next, We need to install the Docker and [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers&ref=blog.roboflow.com) extension inside VS Code. It allows us to run VS Code from inside any container.

Extensions
- (Must) Docker, Dev Containers
- (Optional) vscode-pdf, Python, Python Debugger, Pylance, Jupyter, Jupyter Cell Tags, Jupyter Keymap, Jupyter Notebook Renderers, Jupyter Slide Show, Github Actions


## Run GPU accelerated container and code (using VS Code)
We can now use VS Code to run code directly from within the container.
We'll fire up our Nvidia container and connect to it from within VS Code. At this point, we may want to run a container without `--rm` to persist data, e.g. python modules.

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it -v $(pwd)/project:/workspace/project nvcr.io/nvidia/pytorch:23.08-py3
```
Head over to the docker extension panel (whale on the left), right-click on the running container, and select "Attach Visual Studio Code". VS Code will attach itself to your container and a new window will pop up from which you can code as you do normally.


## Deployment with Machine Learning Environment
Since we are here, we can talk about deployment. So, at some point you may want to place your code somewhere, e.g. a server. This is very easy to do for us, since we already know our train.py will work on nvcr.io/nvidia/pytorch:23.08-py3 image. So, we can create a Dockerfile from that base image and add everything we need.

```
FROM  nvcr.io/nvidia/pytorch:23.08-py3
# maybe we also have a requirements.txt file
# COPY ./requirements.txt /workspace/requirements.txt
# RUN pip install -r requirements.txt
COPY ./project /workspace/project
ENTRYPOINT ["python"]
CMD ["/workspace/project/train.py"]
```

Time to build it:

```
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it pytorch-project
```
To run it:
```
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it pytorch-project
```
And the model will start training.

We completely skip the painful step to make sure our code works in a specific container, since we develop directly inside it!

