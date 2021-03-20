Fedora NFS Mount Role
=========

An ansible role that mountis an NFS file share at a designated mount point on Fedora (v33 or newer) using /etc/fstab and systemd. The NFS client package is installed if not present.

Requirements
------------

An existing NFS share on a remote NFS server with appropriate security configuration. The first implementation of this role explicitly assumes a v4.x NFS server.

Role Variables
--------------

The following three (3) variables are required parameters that need to be defined as viables when this role is instantiated in a play or task. The are also listed but not defined in `.\defaults\main.yaml`:

| Variable       | Default Value | Notes      |
| -------        | ------------- | ----       |
| fnm_server | blank      | NFS server host name or IP |
| fnm_export | blank      | Export path |
| fnm_mnt_path | blank      | Mount point |

The following two (2) variables are optional parameters with default values that can alse be passed to the role:

| Variable       | Default Value | Notes      |
| -------        | ------------- | ----       |
| fnm_options |see ./var/main.yaml  | See https://www.freedesktop.org/software/systemd/man/systemd.mount.html |
| fnm_state | mounted      | options are absent, mounted, present, unmounted, remounted. See https://docs.ansible.com/ansible/latest/collections/ansible/posix/mount_module.html |
 
The following variables are defined in `.\vars\main.yaml` and are not intended to be configurable at runtime:


| Variable       | Default Value | Notes      |
| -------        | ------------- | ----       |
| fnm_package  | nfs_utils      | NFS client rpm package in Fedora |

Dependencies
------------

None at this time.

Example Playbook
----------------

TBD
Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

Technical Notes
---------------

The systemd-mount man file (https://www.freedesktop.org/software/systemd/man/systemd.mount.html) outlines the steps to configure a file system mount in systemd noting the integration between `/etc/fstab` entries and systemd. The web page notes that "In general, configuring mount points through /etc/fstab is the preferred approach" which is the curent approach used in this role. I suspect that users will find more NFS examples using fstab options online than examples that include systemd mount and automount unit files. 

The four parameters used to create the NFS mount (the server hostname, the server NFS export, the client host mount point, and the fstab options string) are all required for each use of this role in a playbook or task.

This role is very basic, designed to provide the needed flexibility for configuring NFS clients on a small fleet of machines powering a local Kubernetes cluster (e.g. multiple VMs or Raspberry Pis) with a Fedora OS (currently Fedora 33). At somepoint, this may be extended to also support Fedora CoreOS. It is not designed as a general purpose NFS client so please keep my goals in mind when evaluating it for your use.

The initial use case is to create and configure NFS mounts for DNF (using the local repository plugin) and to support NFS volume mounts for applications hosted in the clus.

License
-------

BSD

Author Information
------------------

Author: Brad Smith
email: bradley.g.smith@gmail.com
