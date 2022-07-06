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

