# notebooks
Resources for notebooks (Jupyter &amp; Zeppelin)

## Zeppelin
[Zepplein 0.9.0](http://zeppelin.apache.org/docs/0.9.0/)

### Installation
On this page are instructions to help you get started.

#### Requirements
Apache Zeppelin officially supports and is tested on the following environments:
|Name   | Value  |
|:------|-------:|
|OpenJDK or Oracle JDK | 1.8 |
|OS                    | MacOSX & Ubuntu 16.x|

#### Downloading Binary Package
Two binary packages are available on the [download page](http://zeppelin.apache.org/download.html). Only difference between these two binaries is interpreters are included in the package file.
* all interpreter package: unpack it in a directory of your choice and you're ready to go.
* net-install interpreter package: unpack and follow install additional interpreters to install interpreters. If you're unsure, just run ./bin/install-interpreter.sh --all and install all interpreters.

#### Building Zeppelin from source
  Follow the instructions [How to Build ](http://zeppelin.apache.org/docs/0.9.0/setup/basics/how_to_build.html), If you want to build from source instead of using binary package.
  
#### Starting Apache Zeppelin
##### Starting Apache Zeppelin from the Command Line
On all unix like platforms:
```
bin/zeppelin-daemon.sh start
```
After Zeppelin has started successfully, go to http://localhost:8080 with your web browser.

##### Stopping Zeppelin
```
bin/zeppelin-daemon.sh stop
```

### Docker
Start by creating the needed voulumes:
```
docker volume create zeppelin-notebook
docker volume create zeppelin-logs
docker volume create zeppelin-interpreter
```
Then start the docker container:
```
docker run -d --restart always -p 8080:8080  -v zeppelin-notebook:/opt/zeppelin/notebook -v zeppelin-logs:/opt/zeppelin/logs -v zeppelin-interpreter:/opt/zeppelin/interpreter apache/zeppelin:0.9.0
```

*Note: the official image of zeppelin uses anaconda (miniconda) as virtual environment for R and Python. 
To build an image that does not use anaconda use the Dockerfile found in this repository*

## Jupyter
### Install Python 3
To install pyhton 3 in your environment OS please follow the instruction found in this page: https://www.python.org/downloads/

### Install jupyter-lab
After python has been installed you can isntall jupyter-lap
```
pip install jupyterlab
```
### Starting jupyter-lab
Just execue this code in powershell or terminal inside your workspace folder
```
jupyter-lab
```
To add R kernel into the python notebook you have to install R-base on your machine
Install IRKernel package in R using this command inside r shell:
```
install.packages('IRkernel')
IRkernel::installspec()  # to register the kernel in the current R installation
```

### Start with docker
There are planty of official docker [https://jupyter-docker-stacks.readthedocs.io/en/latest/using/selecting.html](images) to choose from. The below example shows how to start jupyter with r kernel:
Create the volume
```
docker volume create zeppelin-notebook
```
Run the container:
```
docker run --rm -p 8888:8888 -e JUPYTER_ENABLE_LAB=yes -v jupyter-notebook:/home/jovyan/work jupyter/r-notebook
```
Visiting ``` http://localhost:8888/?token=<token> ``` in a browser loads JupyterLab, where token is the secret token printed in the console.
Docker destroys the container after notebook server exit, but any files written to ~/work in the container remain intact on the host.
