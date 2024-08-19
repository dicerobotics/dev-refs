# Development Workspace
Source: https://blog.roboflow.com/nvidia-docker-vscode-pytorch/

__Hardware__
- CPU: 
- GPU: NVIDIA GeForce RTX 4070
- Memory: 
- Storage: 

## Install Operating Systems (OS)
__Recommendations__
- Configuration: Dual Drive Dual Boot (One drive connected at a time during installations)
  - Virtual Machines (VM), Windows Subsystem for Linux (WSL), etc. not recommended
- Linux: Ubuntu 22.04 LTS (Jammy Jellyfish)
- Window: Windows 11
- Update both operating systems
- [Customize](ubuntu_custom.md) OS (optional)


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
```



## Install NVIDIA drivers
From Ubuntu 20.02, the Nvidia drivers can be installed in the Ubuntu OS installation process using `sudo ubuntu-drivers install` or `sudo ubuntu-drivers install nvidia:535`. However, We recommend installing it manually to control (fully) what goes inside the machine. To manually install the drivers, go to the [official driver page](https://www.nvidia.com/download/index.aspx?ref=blog.roboflow.com) for NVIDIA GPUs and download the relevant driver package. The following instructions are for __NVIDIA GeForce RTX 4070__ and __Linux 64-bit__.

``` shell
wget "https://us.download.nvidia.com/XFree86/Linux-x86_64/550.107.02/NVIDIA-Linux-x86_64-550.107.02.run"
```
Once you have downloaded it, you can run the installer:
``` shell
# Change to the directory containing the drivers
chmod +x ./NVIDIA-Linux-x86_64-550.107.02.run
sudo ./NVIDIA-Linux-x86_64-550.107.02.run
```
Follow the installation steps. `reboot` and you should be able to run `nvidia-smi` in the Terminal. The output should look similar to the following.

``` shell
Mon Aug 19 14:48:06 2024       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 550.107.02             Driver Version: 550.107.02                          |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA GeForce RTX 4070        Off | 00000000:01:00.0  On |                  N/A |
|  0%   41C    P8              17W / 200W |    659MiB / 12282MiB |      3%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
```

## Install NVIDIA Container Toolkit
Super! Unfortunately, Docker has no idea how to use your GPU(s), we need the NVIDIA Container Toolkit. It allows users to build and run GPU-accelerated containers.
To install it (on Ubuntu, other installations can be found on the official guide):

``` shell
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

Then

``` shell
sudo apt-get update
sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker
```
Let's test it:
``` shell
docker run --rm --gpus all nvidia/cuda:11.0.3-base-ubuntu20.04 nvidia-smi
```
And you should see the correct output from nvidia-smi inside the container. In my case:

``` shell
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 515.57       Driver Version: 515.57       CUDA Version: 11.7     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:1D:00.0 Off |                  N/A |
|  0%   38C    P8    15W / 350W |   2761MiB / 24576MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+

```

--gpus is used to specify which GPU the container should see, all means "all of them". If you want to expose only one you can pass its id --gpus 1. You can also specify a list of GPUs to use, --gpus "device=1,2"



## Run GPU Accelerated Containers with PyTorch
We all know and love PyTorch. For the ones who have never used it, PyTorch is an open source machine learning python framework, widely used in the industry and academia.

Nvidia provides different docker images with different cuda, cudnn and Pytorch versions. The official catalog is here. They are part of the NGC Catalog, a curated set of GPU-optimized software for AI, HPC, and Visualization containers.

To decide which version you want to use you can head over to the releases list. We will use the latest one, 22.07

Additional information about the catalog can be found in the official guide

Nvidia also provides documentation showcasing how to run these containers.

Okay, let's run it!

``` shell
docker run --gpus all -it --rm nvcr.io/nvidia/pytorch:22.07-py3
```

-it means to run the container in interactive mode, so attached to the current shell. --rm tells docker to destroy the container after we are done with it.

After pulling the image, docker will run the container and you will have access to bash from inside it.

Nvidia is suggesting running the container with additional flags to improve performance, let's kill the container (ctrl + c) and re-run it with the suggested flags.

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it --rm nvcr.io/nvidia/pytorch:22.07-py3
```
We can check everything is working by hopping inside the python console:

``` python
import torch
torch.cuda.is_available()
torch.backends.cudnn.version()
```
We can see cuda is there and the cudnn version is the correct one.

But, why did we need the additional flags? Let's decompose them:

--ipc IPC (POSIX/SysV IPC) provides a way to speed up inter-process communication. Since PyTorch Dataloaders with num_workers > 1 will use different processes, we need to set it to host.

--ulimit memlock=1 calls the ulimit linux command and set memlock=-1 means no limit for memory lock.

--ulimit stack=67108864 this sets the stack size and it's specific to my host machine.


## Running Code within container
We have our shiny new Nvidia container, but how can we run code from inside it?

Let's see an example. Assuming you have a folder project with a file train.py.

For simplicity, train.py contains the famous mnist example from pytorch.

You can make project available to your container by:

``` shell
docker run ... -v /<your_path>/project:/workspace/project ...
```

Remember that volumes need an absolute path.

-v creates a volume mapping from the host to the container. By default, the Nvidia container uses the /workspace directory. Let's see an example. Assuming you have a folder project with a file train.py.

Putting everything together:

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it -v $(pwd)/project:/workspace/project --rm nvcr.io/nvidia/pytorch:22.07-py3
```

You will see your code inside /workspace/project. We can directly run the code by calling python
``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it -v $(pwd)/project:/workspace/project --rm nvcr.io/nvidia/pytorch:22.07-py3 python ./project/train.py
```

You'll see something like:

```
Downloading http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz
...
Extracting ../data/MNIST/raw/t10k-labels-idx1-ubyte.gz to ../data/MNIST/raw
Train Epoch: 1 [0/60000 (0%)]   Loss: 2.283439
Train Epoch: 1 [640/60000 (1%)] Loss: 1.827201
```

## Run code within the Container with VS Code
We have seen how to run our train.py from the terminal, but that's not a good development environment, is it? Well, we can use VS Code directly from within the container! Let's see how.

First of all, we need to install it. You can follow the official installation guide.

Next, you need to install the remote container extension. It allows us to run VS Code from inside any container.

Ok, we are almost there. Let's fire up our Nvidia container and connect to it with VS Code. At this point, we may want to run a container without --rm to persist data, e.g. python modules.

``` shell
docker run --gpus all --ipc=host --ulimit memlock=-1 --ulimit stack=67108864 -it -v $(pwd)/project:/workspace/project nvcr.io/nvidia/pytorch:22.07-py3
```
Head over to the docker extension panel (whale on the left), right-click on the running container, and select "Attach Visual Studio Code". VS Code will attach itself to your container and a new window will popup from which you can code as you do normally. The following video shows this process.


## Deployment with Your Machine Learning Environment
Since we are here, we can talk about deployment. So, at some point you may want to place your code somewhere, e.g. a server. This is very easy to do for us, since we already know our train.py will work on nvcr.io/nvidia/pytorch:22.07-py3 image. So, we can create a Dockerfile from that base image and add everything we need.

```
FROM  nvcr.io/nvidia/pytorch:22.07-py3
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

