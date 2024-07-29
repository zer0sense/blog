---
title: "Docker Containers With NFS"
date: 2024-07-26T02:35:47Z
description: "Using NFS for Docker Containers"
tags: [Docker,NFS,Linux]
categories: [Tutorials, Projects,Linux]
comments: false
draft: true
---

Containerization is such an amazing tool, and will be a part of enterprise infrastructure going forward. This technology has peaked my interest and curiosity for a few years now. One thing that I was curious about, was persistence in these micro services. How would I create persistence if I was to deploy a service with a database in an enterprise. I would probably have access to some kind of storage server, and would have access to network shares. That is when I settled looking into deploying a NFS server and using that for my docker persistence. 

## NFS
The network file system (NFS) offers the flexibility, security, and customization I was looking for. I was so thankful for the [digital ocean](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-20-04) article. This was such a clear and very beginner friendly.  

What does this give our containers? It doesn't matter if my container gets deleted, migrated, or updated the key data is saved. I do not run my docker container on the same server I am using for my NFS server. This gives me more flexibility to experiment with different docker deployments. For instance, I have ran nextcloud and my own wiki on different VM's and hardware. They both started on a raspberry pi, but now live in a proxmox VM off my old gaming server. I can feel comfortable jumping back and forth between servers because my data is persistent. If i had to re-deploy my wiki from scratch every time I re-deployed the container I would be very discouraged to maintain it. 

### Using NFS via Docker Compose

You can integrate a NFS share as a docker volume. Don't be like me and remember to create the folder on your share __before__ you try deploying your container. You want to create your folder path on your share, and make sure your permissions/owner is set up correctly. 

I typically define my volume in the beginning of my docker compose to reference later.

```yaml
volumes:
  config:
    driver_opts:
      type: "nfs"
      o: "addr=<ip address>,nolock,soft,rw"
      device: ":/<NFS share path>
```
I am going to break a couple of things down from this example. To refer to this later in your config the name of this volume is ```config```. This means later in the volumes section of your docker container you're going to define ```- config:/config```. This will tell the docker container to use that NFS share as the docker containers config directory. 

Under the ```driver_opts``` there is a couple of takeaways from that as well. The ```o:``` is the NFS server with the nolock, soft, and rw options that I picked up from the digital ocean article. The ```device``` is the share path of that NFS server. Breaking down the typical NFS file share as ```nfs://192.168.1.100:/share/docker``` is just broken up in two different options. The ```o: "192.168.1.100,nolock,soft,rw"``` and ```device: ":/share/docker```. 

### Using NFS with System Mount

This method is creating a mount point in the VM itself for the NFS share. The same warning for the NFS share folder applies to this as well. You want to make sure the folder path on the NFS server is created, and that you create a folder on your docker VM to mount your NFS share to. Also, make sure your owner/permissions are set correctly.

Lets use the same NFS share example of ```nfs://192.168.1.100:/share/docker``` to use for this method. We will use the VM folder name of nfs.

In your terminal lets do a test mount ```sudo mount -t nfs 192.16.1.100:/share/docker nfs```. Once this is done go ahead and change directory into your nfs folder. Create a test file ```touch test.txt```. Go to your NFS server and check that the file was created __on the server__. Once you have verified the NFS share is working correctly you can edit your fstab on your linux VM that you are running your docker container on. Use your favorite text editor and edit the ```/etc/fstab``` to add the following.

```bash
192.168.1.100:/share/docker /home/user/nfs nfs rw,hard,intr,rsize=8192,wsize=8192,timeo=14 0 0
```

The break down of this gets a little more interesting. How this really breaks down is ```ipaddress:/sharepath /directoryonserver filesystemtype options```. At the very minimum for options I recommend rw for read/write. The other options I have listed were from some tireless troubleshooting research on NFS. ```rsize``` and ```wsize``` loosely translate to read and write size and the ```timeo``` is timeout. 

Now your mount will happen automatically everytime your system boots. This could be an issue if your path or there is an issue with the share. This is why the test mount in crucial or your VM can stop booting. 


## Lessons Learned

I have come back to using this method and figuring out failures for a while now. The biggest thing I discovered recently is that NFS and docker can be very very picky when it comes to folder permissions and ownership. My general rule of thumb going forward is to use the docker compose method for folders like config folders and to use the system mount if you are running something like a database/apache server that might require particular permission schemes in order to function. I have had to start my container and data from scratch multiple times when it came to my nextcloud and wiki container because of this. I have been running without an issue for the past few months since figuring this out, and I had to share the experience with you all.



