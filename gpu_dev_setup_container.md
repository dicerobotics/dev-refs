# System Setup for containerized development on Ubuntu 22.04
We assume the system has NVIDIA GPU installed.


Everybody hates installing NVIDIA drivers, you have to manually download them, then install cuda, be sure to have the correct version of everything, and change them from time to time to be updated.

Today we will see how to:

install NVIDIA drivers
install docker & nvidia docker
run gpu accelerated containers with PyTorch
develop with VS Code within the container
From Ubuntu 20.02, the drivers will be automatically installed by the OS. That's great, but you lose control over them. Maybe you need a specific version, or your code only works with cuda 10. In that case, well things may get dirty.

What if we use Docker and VS Code to directly develop from inside it?

Not only will we ensure all the correct versions of cuda and cudnn are there, we can easily change them and we will make our code effortless to deploy since we know it worked from inside a specific container.

Well, today we will see how to develop machine learning models like a pro with Nvidia + Docker + VS Code + PyTorch. Let's dive into it!

## Install Docker
The first step is to install Docker. In a nutshell, docker is an open source platform for building, deploying and managing containerized applications. You can see all the installation options from the official website, we will install everything on Ubuntu 20.04. We need the engine, not Docker desktop.

``` shell
# dependencies
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
# add docker official GPG key
sudo mkdir -p /etc/apt/keyrings
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg-
# then add their repo
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
# finally install it
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
# create the docker group
sudo groupadd docker
# add your user to the docker group
sudo usermod -aG docker $USER
```

You can test your docker installation by running the hello-world container:
``` shell
docker run --rm hello-world
```
The --rm flag tells docker to remove the container once done.
``` shell
Hello from Docker!
This message shows that your installation appears to be working correctly.
```



## Install NVIDIA drivers
Without drivers, your super cool brand-new Nvidia GPU won't work. From Ubuntu 20.02, the Nvidia drivers can be installed in the Ubuntu OS installation process. However, you may want to be in total control of what goes inside your machine.
To manually install the drivers, go to the official driver page. In my case, RTX 3090 and Linux 64bit. If you have my same system, you can run the following command:

``` shell
wget "https://us.download.nvidia.com/XFree86/Linux-x86_64/515.57/NVIDIA-Linux-x86_64-515.57.run"
```
Once you have downloaded it, you can run the installer:
``` shell
# cd to the folder containing the drivers
chmod +x ./NVIDIA-Linux-x86_64-515.57.run # the name may change from yours
sudo /NVIDIA-Linux-x86_64-515.57.run
```
Follow the installation steps. Reboot and you should be able to run nvidia-smi, the output should look similar to:
``` shell
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 515.57       Driver Version: 515.57       CUDA Version: 11.7     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:1D:00.0 Off |                  N/A |
|  0%   38C    P8    15W / 350W |   2759MiB / 24576MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
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










## Setup Personal Workspace

### install Git
``` shell
sudo apt install git
```

### Install VS Code
``` shell
sudo dpkg -i ./Downloads/code_1.83.1-1696982868_amd64.deb 
rm ./Downloads/code_1.83.1-1696982868_amd64.deb
```
### Install pip and check installation directory
``` shell
sudo apt install python3-pip
which -a pip
```

### Install Miniconda
Download the Miniconda installer from the web. https://docs.conda.io/projects/miniconda/en/latest/index.html
``` shell
sha256sum ~/Downloads/Miniconda3-latest-Linux-x86_64.sh
sudo bash ~/Downloads/Miniconda3-latest-Linux-x86_64.sh
rm ~/Downloads/Miniconda3-latest-Linux-x86_64.sh
conda config --set auto_activate_base false
conda deactivate
conda update conda
```

### Create a Miniconda virtual environment and activate it
``` shell
conda info --env
conda create --name env_torch
conda activate env_torch
conda config --append channels conda-forge # adds the new channel to the bottom, making it the lowest priority
conda config --append channels annaconda
conda config --append channels nvidia
conda config --append channels pytorch
```
Interested readers may read about channel priority by running the following command
``` shell
conda config --describe channel_priority
```

### Install PyTorch
``` shell
conda install pytorch torchvision torchaudio pytorch-cuda=12.1 -c pytorch -c nvidia
```

### Open the Python kernel and execute the following to test your installation
``` python
import torch
print(torch.cuda.is_available()) # should be True

t = torch.rand(10, 10).cuda()
print(t.device) # should be CUDA
```
### Optional Installations
Though we are done with the necessary installations, some users might like installing the following modules. Before you install the following, make sure you are in miniconda virtual environment of your choice. If are not, activate it by typing "conda activate ENV_NAME". In my case, my ENV_NAME is env_torch.
``` shell
conda install -c conda-forge tqdm
conda install -c conda-forge matplotlib
conda install -c anaconda scipy
conda install -c anaconda seaborn
conda install -c conda-forge lpips # Takes long time
conda install -c anaconda scikit-image

```
In case you can't install optional packages using conda because of the long time for solving environments. You might consider install pip locally and install packages using locally installed version of pip.
``` shell
conda install -c anaconda pip
which -a pip # Used to see all installed versions of a package pip
python -m pip install PACKAGE_NAME # install package with locally install package manager pip
```
In our case, we successfully installed all packages but lpips with conda package manager. All conda packages may alternatively be installed with single command.
``` shell
condal insall tqdm matplotlib scipy seaborn scikit-image -c conda-forge -c anaconda
```


## Mix Environment: Advanced
In case you are expected to work on a single project that uses libraries designed for frameworks other than PyTorch (e.g. scikit-learn, keras), we recommend creating a new clone of env_torch and installing extra packages in a new environment specially designed to work on advanced projects.

``` shell
conda deactivate
conda create --name env_mix --clone env_torch
conda info --env # Check the newly created environment
conda activate env_mix
conda install -c anaconda pip
```
Now you may use the following commands to install new packages (as per your need) in a newly generated virtual environment.

``` shell
conda install -c CHANNAL_NAME PACKAGE_NAME
```
or
``` shell
python -m pip install PACKAGE_NAME
```

We installed the following packages as per our needs.

``` shell
python -m pip install opencv-python
python -m pip install grad-cam
conda install -c conda-forge datasets
conda install -c conda-forge transformers
```





