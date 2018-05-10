# Oracle database using Docker on VmWare Workstation

## Install Ubuntu 16.04 Server
1. Download
ubuntu-16.04.3-server-amd64.iso from \\\nipedadata\Programmes\Systeme Exploitation\Linux\Ubuntu
2. Create a new VirtualMachine in VMware Workstation
    * Memory : 3Go
    * Hard Disk : 40Go
    * Network: NAT (or Bridge)
3. Install Ubuntu

## Install SSH
    $ sudo apt-get install ssh
    $ ip a # to get the ip of this virtual machine
  
Using Putty, connect to the virtual machine using this ip. Now you can enjoy copy and paste, which will make your life easier.

## Install VMware tools
    $ sudo apt-get upgrade
    $ sudo apt-get install open-vm-tools

## Share a folder
In the virtual machine settings add a shared folder then in the command line type :
    $ sudo nano /etc/fstab
Insert the following line at the end of this file :
.host:/ /mnt/hgfs fuse.vmhgfs-fuse allow_other,auto_unmount,defaults 0 0

Reboot the virtual machine. The shared folder should now be seen here : /mnt/hgfs

## Install Docker CE
Please refer to the detailed instructions below while typing the following commands.

    $ sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    $ sudo add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"
    $ sudo apt-get update
    $ sudo apt-get install docker-ce=17.12.0~ce-0~ubuntu
    $ docker -v
    $ sudo docker run hello-world

Detailed instructions : 
https://docs.docker.com/install/linux/docker-ce/ubuntu/#set-up-the-repository

Post-installation

    $ sudo groupadd docker
    $ sudo usermod -aG docker $USER
    $ exit

Log out and log back in so that your group membership is re-evaluated.

Detailed instructions :
https://docs.docker.com/install/linux/linux-postinstall/

## Install Docker Compose
Please refer to the detailed instructions below while typing the following commands.

    $ sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    $ sudo chmod +x /usr/local/bin/docker-compose
    $ docker-compose -v

Detailed instructions : https://docs.docker.com/compose/install/#install-compose

## Install Oracle database
On your physical machine download linuxx64_12201_database.zip from http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html and move into to the shared folder, without extracting it.

Type the following commands in a command line (assuming your are in your home folder) :

    $ wget https://github.com/oracle/docker-images/archive/master.zip
    $ unzip master.zip
    $ cd docker-images-master/OracleDatabase/dockerfiles/12.2.0.1/
    $ mv /mnt/hgfs/<Name of your shared folder>/linuxx64_12201_database.zip .
    $ cd ..
    $ ./buildDockerImage.sh -v 12.2.0.1 -e -i
    $ docker run --name oracle -p 1521:1521 -p 5500:5500 -e ORACLE_PWD=Passw0rd -d oracle/database:12.2.0.1-ee # Warning : don't use @ in the password
    $ docker container logs oracle -f # Wait until you see "DATABASE IS READY TO USE!"
    $ docker exec -it oracle sqlplus sys/Passw0rd@localhost:1521/ORCLCDB as sysdba

You are now connected to Oracle database.

You can also visit https://192.168.80.128:5500/em/login to access Oracle Entreprise Manager.

Detailed instructions : https://github.com/oracle/docker-images/tree/master/OracleDatabase/SingleInstance
