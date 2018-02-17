#### Terminal commands

* 'docker images' : see available images as list
* 'docker ps –a' : see history of containers
* 'docker start [container name]'
* 'docker attach [container name]' - hit enter  
* 'docker stop [container name]'
* 'docker cd [from] [containername]:[path to]' - note that ~ doesn't work here. Need full paths
* 'docker pull'  
*  stop - Stops a running container.
*  start - Starts a stopped container.
* commit - Creates a new image from a container’s changes.
* rm - Removes one or more containers.
* rmi - Removes one or more images.
* ps -a - Lists containers.
* images - Lists images.
* exec - Runs a command in a running container.
* To remove dangling images: ' docker rmi $(docker images -f "dangling=true" -q) '

####Installation
* Follow instructions here: https://docs.docker.com/engine/installation/linux/ubuntu/#install-using-the-repository  
* Add user to group: https://docs.docker.com/engine/installation/linux/linux-postinstall/  

run_framework.sh

#### Helpful links  

* High-level overview: https://www.toptal.com/devops/getting-started-with-docker-simplifying-devops  
* Makefiles: https://www.itnotes.de/docker/development/tools/2014/08/31/speed-up-your-docker-workflow-with-a-makefile/  
* Other command line stuff: https://docs.docker.com/engine/reference/commandline/docker/#child-commands  
* <none>:<none> images: http://www.projectatomic.io/blog/2015/07/what-are-docker-none-none-images/  
* Best practices for Dockerfiles: https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#onbuild  
* Backing up a container: <https://bobcares.com/blog/docker-backup/>

##### Old notes
New docker image setup (this is automated in docker setup now)
```
In catkin_ws: catkin_make, source devel/setup.bash, catkin_make again
echo -e "\nsource /home/icubuser/catkin_ws/devel/setup.bash" >> ~/.bashrc
Install atom
sudo add-apt-repository ppa:webupd8team/atom
sudo apt-get update
sudo apt-get install atom
Open atom, remove ~/.atom, docker cp .atom from local
Install nvidia driver (if not already done)
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install -y software-properties-common
sudo apt-get update
sudo apt-get install -y mesa-utils
sudo apt-get update
sudo apt-get install -y xserver-xorg-video-nouveau
sudo apt-get install -y nvidia-370
sudo apt-get update
```

Brice's images have terminator installed on them.\
To update 'docker run' script (ex. Run_framework.sh) and remake container:
'docker rm [container name]'.\
If there's an error, 'docker stop [container name]' first, then rm\
Run script again\
Add stuff (mounting folders) to run_framework.sh, change container name and run it again.  
