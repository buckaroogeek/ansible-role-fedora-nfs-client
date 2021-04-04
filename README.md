Fedora NFS Client Ansible Role
=========

An ansible role that attaches an NFS file share at a designated filesystem location on Fedora (v33 or newer) using /etc/fstab (via ansible.posix.mount) and systemd. The NFS client package is installed if not present. The target client ecosystem is Fedora running on hardware or in VMs. This role is not designed for attaching NFS directly to a container-based application.

Requirements
------------

An existing NFS share on a remote NFS server with appropriate security configuration. The first implementation of this role implicitly assumes a v4.x NFS server. As noted above, Fedora machines (hardware or VM) comprised the target clientele.

Role Variables
--------------

The following three (3) variables are required parameters that need to be defined as `vars` when this role is instantiated in a play or task. They are also listed, but not defined, in `./defaults/main.yaml`:

| Variable       | Notes      |
| -------        | ----       |
| fnm_server | NFS server host name or IP |
| fnm_export | Export path on NFS server |
| fnm_mnt_path | Client filesystem location |

The following two (2) variables are optional parameters with default values that can alse be passed to the role (see `./defaults/main.yaml`):

| Variable       | Default Value | Notes      |
| -------        | ------------- | ----       |
| fnm_options |see ./defaults/main.yaml  | See https://www.freedesktop.org/software/systemd/man/systemd.mount.html for detailed explanations of all options. |
| fnm_state | `mounted`      | options are absent, mounted, present, unmounted, remounted. See https://docs.ansible.com/ansible/latest/collections/ansible/posix/mount_module.html for definitions of each state. |
 
The following variables are defined in `.\vars\main.yaml` and are not intended to be configurable at runtime:


| Variable       | Default Value | Notes      |
| -------        | ------------- | ----       |
| fnm_package  | `nfs_utils`      | NFS client rpm package in Fedora |

Dependencies
------------

None at this time.

Example Playbooks
----------------
The following playbook attaches the `/volume1/nfs-test` NFS share on the client at the `/mnt/test` location. Systemd unit files to persist the attachment across rebootsare automatically created and enabled. By default, the NFS share is only attached when in use. The three mandatory parameters are defined but not the two optional parameters.

```
- name: Attach NFS share
  hosts: pi02
  become: true
  tasks:
    - name: "Include fedora_nfs_mount"
      include_role:
        name: "fedora_nfs_mount"
      vars:
        fnm_server: quga.lan
        fnm_export: /volume1/nfs-test
        fnm_mnt_path: /mnt/test
```

The following playbook removes the NFS share from the client.

```
- name: Remove NFS share
  hosts: pi02
  become: true
  tasks:
    - name: "Include fedora_nfs_mount"
      include_role:
        name: "fedora_nfs_mount"
      vars:
        fnm_server: quga.lan
        fnm_export: /volume1/nfs-test
        fnm_mnt_path: /mnt/test
        fnm_state: absent
```

Technical Notes
---------------

This role uses both fstab and systemd. Unit mount and automount files are automatically generated for systemd if the default options are used. The unit files enable the NFS share attachment to persist across system reboots.

The systemd-mount man file (https://www.freedesktop.org/software/systemd/man/systemd.mount.html) outlines the steps to configure a file system mount in systemd noting the integration between `/etc/fstab` entries and systemd. The web page notes that "In general, configuring mount points through /etc/fstab is the preferred approach". This role follows that recommendation. I also suspect that users of this role will find more NFS examples that use fstab options online than examples that include systemd mount and automount unit files. 

The three parameters used to create the NFS mount (the server hostname, the server NFS export, and the client host mount point) are all required for each use of this role in a playbook or task.

This role is very basic, designed to provide the needed flexibility for configuring NFS clients on a small fleet of machines powering a local Kubernetes cluster (e.g. multiple VMs or Raspberry Pis) with a Fedora OS (currently Fedora 33). At somepoint, this may be extended to also support Fedora CoreOS. It is not designed as a general purpose NFS client so please keep my goals in mind when evaluating it for your use.

The initial use case is to create and configure an NFS file share for the DNF local repository plugin and to attach NFS shares to nodes that can be used for applications hosted in a kubernetes cluster.

License
-------

BSD

Author Information
------------------

Author: Brad Smith

email: bradley.g.smith@gmail.com
