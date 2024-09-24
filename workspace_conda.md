# System Setup with CUDA, cuDNN, VSCode, Miniconda, Pytorch on Ubuntu 22.04
We assume the system has NVIDIA GPU installed.

## Install NVIDIA drivers

### Update & upgrade
``` shell
sudo apt update && sudo apt upgrade
```

### Remove previous NVIDIA installation (if any)
You should only use this command if it's necessary. It might corrupt your system.
``` shell
!sudo apt autoremove nvidia* --purge
```

### Check Ubuntu devices
``` shell
ubuntu-drivers devices
```
You will install the NVIDIA driver whose version is tagged with __recommended__


### Install Ubuntu drivers
``` shell
sudo ubuntu-drivers autoinstall
```

### Install NVIDIA drivers
My __recommended__ version is 535, adapt to yours

``` shell
sudo apt install nvidia-driver-535
```

### Reboot & Check
``` shell
reboot
```
after restart verify that the following command works
``` shell
nvidia-smi
```

## Install CUDA drivers

### Update & upgrade
``` shell
sudo apt update && sudo apt upgrade
```

### Install CUDA toolkit
``` shell
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.2.2/local_installers/cuda-repo-ubuntu2204-12-2-local_12.2.2-535.104.05-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-2-local_12.2.2-535.104.05-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-2-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
rm cuda-repo-ubuntu2204-12-2-local_12.2.2-535.104.05-1_amd64.deb
```

### Check CUDA install
``` shell
export "PATH=/usr/local/cuda/bin:$PATH"
nvcc --version
```

### Add path to the .bashrc file
You need to edit .bashrc file with administrator privileges so you need not to export local cuda path manually everytime you start terminal.
Please add following line in .bashrc file

export "PATH=/usr/local/cuda/bin:$PATH"

``` shell
sudo nano ./.bashrc
export "PATH=/usr/local/cuda/bin:$PATH"
```

## Install cuDNN

### Download cuDNN .deb file
You can download cuDNN file [here](https://developer.nvidia.com/rdp/cudnn-download). You will need an Nvidia account.
Select the cuDNN version for the appropriate CUDA version, which is the version that appears when you run:
``` shell
nvcc --version
```

### Install cuDNN
``` shell
cd ~/Downloads
sudo apt install ./cuda-repo-ubuntu2204-12-2-local_12.2.2-535.104.05-1_amd64.deb
rm ./Downloads/cudnn-local-repo-ubuntu2204-8.9.5.29_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-8.9.5.29/cudnn-local-275FA572-keyring.gpg /usr/share/keyrings/
```

My cuDNN version is 8, adapt the following to your version:

``` shell
sudo apt update
sudo apt install /var/cudnn-local-repo-ubuntu2204-8.9.5.29/libcudnn8_8.9.5.29-1+cuda12.2_amd64.deb
sudo apt install /var/cudnn-local-repo-ubuntu2204-8.9.5.29/libcudnn8-dev_8.9.5.29-1+cuda12.2_amd64.deb
sudo apt install /var/cudnn-local-repo-ubuntu2204-8.9.5.29/libcudnn8-samples_8.9.5.29-1+cuda12.2_amd64.deb
```

## Setup Personal Workspace

### install Git
``` shell
sudo apt install git-all
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
conda --help
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




