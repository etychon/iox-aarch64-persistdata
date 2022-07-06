# iox-aarch64-persistdata
IOx application to show how to persist and share data between the host and running IOx apps

# Introduction 

Cisco IOx application are running in a Docker environement which allow very controlled execution and sandboxing. When an IOx application is deleted, all associated data is also removed. This can cause issues when IOx applications are storing data that should persist past the life of the application, or be shared with other IOx applications running on the same host.

The technique explained here might be platform specific and only applicable to IOS-XE running gateways (not Classic IOS)

# Getting Started

Clone the current repo to start experimenting:

```
git clone git@github.com:etychon/iox-aarch64-persistdata.git
````

# Identify Shareable Directories

Each platform family may apply different policies as to which host directories can be used with IOx application. By using the ioxclient utility, you can connect to your platform of choice and list the capabilities:

```
[etychon@squeeze]$ ioxclient platform cap 
Currently active profile :  IR1800_FCWXXXXP8JC
Command Name:  plt-capability
-------------Platform Capability----------------
{
 "compute_nodes": [
  { 
  (...)
  "supported_host_mount_paths": [
    "/flash1/iox/iox_data_share",
    "/flash1/iox/iox_host_data_share",
    "/flash1/iox/iox/repo-docker/docker-data/",
    "/local/local1/core_dir/",
    "/bootflash/SHARED-IOX",
    "/tmp/xml",
    "/tmp/binos-IOX",
    "/tmp/HTX-IOX"
   ], 
   (...)
````

In this case, on can see a directory called `/bootflash/SHARED-IOX` that is also present on the platform bootflash:

````
IR1800_FCW2445P8JC#dir
Directory of bootflash:/

259073  drwx            86016   Jul 6 2022 11:28:24 +00:00  tracelogs
259139  drwx             4096   Jul 6 2022 11:22:27 +00:00  SHARED-IOX
11      drwx             4096   Jul 6 2022 09:31:11 +00:00  .installer
259196  drwx             4096   Jul 6 2022 09:29:39 +00:00  iox
259124  drwx             4096   Jul 6 2022 09:29:32 +00:00  license_evlog
````

# Ask Directory Mounting

This needs to be requested as part of the resource profile `package.yaml` file, by adding the following information:

````
app:
  resources:
  host_mounts:
    -
      host_mount_path: "/bootflash/SHARED-IOX"
      target_mount: "/mnt/SHARED-IOX"
      description: "used to share IOx persistent data"
````

* host_mount_path is the host path - this is coming from the output of the previous step ioxclient command 
* target_mount is where you want to directory to show up in your IOx application

# Repackage and Get Rolling

Package the IOx application with the modified package.yaml:

````
sh ./build.sh
````

Upload on the platform and start the app.

You should not be able to write things in the app's /mnt/SHARED-IOX and they will show up on the host "bootflash:SHARED-IOX"



