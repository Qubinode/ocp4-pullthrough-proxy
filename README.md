Role Name
=========

Creates a Pod running docker registries to function as a pull-through cache for OpenShift installs.

Requirements
------------

You this script [qubinode_ocp4_registries_conf.sh](https://raw.githubusercontent.com/Qubinode/qubinode-installer/dev/lib/qubinode_ocp4_registries_conf.sh) generate the *registry_pods* vars file.

Example
```
bash scripts/qubinode_ocp4_registries_conf.sh > playbooks/vars/registries.yml
```

This role expects the system to already be configured to support rootless podman.

Role Variables
--------------

| Variable | Required | Description |
| --- | --- | --- |
| pull_secret | yes | Your OpenShift pull secret |
| container_user | yes | Define if you want rootless containers |
| container_group | yes | Define if you want rootless containers |
| container_dir | yes | Local for container file storage |
| container_status | no | container started or stopped |
| registry_domain | yes | Needed to generate proper self-signed certs |
| registry_hostname | yes | Needed to generate proper self-signed certs |
| registry_ip | yes | Needed to generate proper self-signed certs |
| registry_pods | yes | See the requirements section. |


Dependencies
------------

Install them using the included requirements.yml
```
ansible-galaxy role install -r requirements.yml
ansible-galaxy collection install -r requirements.yml
```

Example Playbook
----------------

```
- name: Create a pull through registry pod
  hosts: localhost
  gather_facts: yes
  become: no

  vars_files:
    - vars/registries.yml

  vars:
    pull_secret: "{{ playbook_dir }}/files/redhat/pull-secret.json"
    container_user: "{{ admin_user }}"
    container_group: "{{ admin_user_group }}"
    container_dir: /opt/podman/containers/ocp_registry
    container_status: 'started'
    registry_domain: "{{ domain }}"
    registry_hostname: ocp-registry
    registry_ip: 172.23.11.5


  tasks:
  - name: create required self-signed certs
    import_role:
      name: ocp4-mirror-registry
```


