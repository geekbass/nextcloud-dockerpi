# Steps to quickly create NextCloud with Docker on Pi
This provides a quick overview of the necessary steps to create a NextCloud environment using docker on a raspberry pi. We will be attaching an additional mount in docker for [external storage](https://docs.nextcloud.com/server/14/admin_manual/configuration_files/external_storage_configuration_gui.html) which will store the bulk of the data. In our case is an external drive connected to the pi via USB. 

We will end the with being able to access your NextCloud via the internet.

# Let's Go
1) When setting up the Pi ensure that you created it with static IP. 

2) Attach the external drive and partition it. I used `btrfs`. 

3) Create the users and groups:
```
uid=113(redis) gid=120(redis) groups=120(redis)
uid=33(www-data) gid=33(www-data) groups=33(www-data),120(redis)
```

4) Set the permissions on your new drive to be own completely by `www-data`.

5) Install Docker.
```
sudo curl -sSL get.docker.com | sh
sudo usermod -aG docker pi
sudo newgrp docker

sudo systemctl daemon-reload
sudo systemctl restart docker

```

6) Pull image and mount the external volume created in step 2 (`-v` argument). In the below example we mount it in the container under `/mnt/myCloudDrive`. You will need this in a later step.

```
export DOMAIN=yourdomain.com

docker run -d -p 4443:4443 -p 443:443 -p 80:80 -v ncdata:/data -v /media/myCloudDrive:/mnt/myCloudDrive --name nextcloudpi ownyourbits/nextcloudpi-armhf $DOMAIN
```

Run docker logs to watch logs to see when its finished.
```
docker logs -t nextcloudpi
```

7) Finish configuration from the Browser by going to port 443. Be sure to save the creds to a file.

8) Setup Let's Encrypt. And any other config in the setup you would like.

9) Once done with setup, Go the the actual NextCloud App with the 'ncp' creds and add yourself as a user to the admin group. 

10) Add the external storage per the docs [here](https://docs.nextcloud.com/server/14/admin_manual/configuration_files/external_storage_configuration_gui.html). Type Local and location of `/mnt/myCloudDrive`.

11) Setup External connections. On your router setup port forwarding for 443 and 80 to the IP of your Pi.

12) Setup your DNS and Domain via your domain provider. I use Namecheap.com. 

# Backups
WIP... will update soon