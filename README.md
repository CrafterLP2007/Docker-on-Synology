# Docker on Synology Diskstation


This tutorial shows how to set up [Docker](https://www.docker.com) with [Portainer](https://www.portainer.io) on a Docker-unsupported Synology Diskstation. 
This tutorial should work on all Synology Diskstations. I tested it on the DS118.

## Before you start

Make sure that:
- the Diskstation have an internet connection
- [the nano command in linux for the Diskstation](https://think.unblog.ch/nano-auf-synology-nas-installieren/)
- you have access and administrator permissions to the DSM
- [the ssh service is enabled](https://kb.synology.com/de-de/DSM/tutorial/How_to_login_to_DSM_with_root_permission_via_SSH_Telnet)

## Part 1 | Installation of Docker

⚠️ Make sure you are logging into ssh as an admin user ⚠️

Get the latest docker version: [here](https://download.docker.com/linux/static/stable/aarch64/) *for example: docker-24.0.7.tgz*

1. Execute the following command in the terminal to download docker:

   ```Bash
    sudo wget https://download.docker.com/linux/static/stable/aarch64/[version]
   ```
   
   > Replace **[version]** to the newest version

<p></p>
2. Unpack the tgz file:
   ```Bash
    sudo tar xzvf [file]
   ```

   > Replace **[file]** to the file name

<p></p>
3. You need to copy all directories and files of the unpacked directory to the linux commands:
   ```Bash
    sudo cp [name]/* /usr/bin/
   ```

      > Replace **[name]** to the name of the unpacked docker file

<p></p>
4. Create the /etc/docker/daemon.json configuration file with the following configuration:
   ```json
    {
     "storage-driver": "vfs",
     "iptables": false,
     "bridge": "none"
   }
   ```

<p></p>
5. You can try to run docker with the following command:
   ```bash
    sudo dockerd
   ```
   > If it doesn't work, please check whether you have done everything correctly according to the instructions. If it's just a permission error, you don't have enough permissions to the Synology user!

## Part 2 | The Docker Runner

To run docker on the startup, you need to create a new execute script that run the command on startup:

1. Go to **Control Panel** > **Task Scheduler**, click **Triggered Task** and **Custom Task**.
2. It opens a new window. The Task is the name of the Task. I named it **Docker Runner**.
3. As **User**, you need to choose a administrator user!
4. The **Event** is triggered when the diskstation **boots up**.
5. Go to **Task Settings** and enter following script/commands as **Command execute**:

   ```Bash
   echo -n "[password]" | sudo -S rm -rf /var/run/docker.pid
   echo -n "[password]" | sudo -S killall containerd
   echo -n "[password]" | sudo -S dockerd
   ```
   
   > Replace **[password]** to the password of your user

<p></p>
6. Click **Ok** to create the new Task!

## Part 3 | Installation of Portainer

1. Go in your SSH-Client and type following command:

   ```Bash
   docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
   ```
   
   > It will create a new docker container with portainer.

<p></p>
2. Go in your browser on http://[ip_of_diskstation]:9000

   > Replace **[ip_of_diskstation]** to the IP-Address of you Diskstation!

## Part 4 | Installation Completed

If you did everything right, it should work. If it doesn't work, you can write me an email <a href="mailto:crafterlp2007@gmail.com">crafterlp2007@gmail.com</a>
