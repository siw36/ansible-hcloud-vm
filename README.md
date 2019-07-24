ansible-hcloud-vm  
=========

![Ansible Role](https://img.shields.io/ansible/role/42259.svg?color=blue) ![Ansible Quality Score](https://img.shields.io/ansible/quality/42259.svg?color=blue) ![Ansible Role Downloads](https://img.shields.io/ansible/role/d/42259.svg?color=blue)

<img src="https://www.hetzner.de/themes/hetzner/images/logo/logo-hetzner-online.svg" alt="Hetzner Cloud" height="50"/>

This role deploys one or more virtual machines with additional disks in the hetzner cloud.  

What happens:  
- The specified SSH public key will be created in your account
- The list of servers will get created with access for the specified SSH key as root (as long as you use the hetzner images)
- The list of volumes for each server will get created and the volumes will get attached to the matching server

__Current module/API problems__:  

When you delete a server and instantly after that delete the volumes that were attached to the server, the API reports that these volumes are still attached to a server and can not be deleted.  
This happens when you run this role and set the state of a server to `absend` and also set the state of the volume that was/is attached to the server to `absent`.  

Get this role
------------
```bash
ansible-galaxy install --roles-path ./roles/ siw36.ansible_hcloud_vm
```

Requirements
------------

- Python module: `hcloud` (This role will install it to the users python module directory automatically)


Role Variables
--------------
| Name | Description | Default value |
|---|---|---|
| getInfoOnly | Get possible values for server type, image and location. Only the information will get printed when this variable is set to true. | false |
| hcloudBearer | Your Hetzner Cloud API key. Can be found in your project in the SSH key tab. | <none - __required__ to be set> |
| sshKey | Dictionary containing information about the root SSH key| <none - __required__ to be set> |
| deployments | List of deployments and their disks | <none - __required__ to be set> |

*See the below example for details on how to specify the variables*

Example Playbook
----------------

playbook.yml:  
```yaml
- hosts: localhost
  become: false
  gather_facts: false
  roles:
    - siw36.ansible_hcloud_vm
```
roles/siw36.ansible_hcloud_vm/vars/main.yml  
```yaml
#getInfoOnly: false

hcloudBearer: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

sshKey:
  name: root
  value: ssh-rsa xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx root
  state: present

deployments:
  - name: test0.nodes.replicas.io
    state: absent # (present|started|stopped|restarted|absent|rebuild)
    size: cx11
    location: hel1
    image: centos-7
    backup: false
    sshKeys:
      - siw
    labels:
        project: replicas.io
        stage: prod
        location: helsinki
    volumes:
      - name: test0.nodes.replicas.io-data0
        state: absent # (present|absent)
        size: 10
        automount: true
        format: xfs # (xfs|ext4)
```

License
-------

GNU General Public License v3.0

Author Information
------------------

Created by Robin 'siw36' Klussmann (07/2019)
