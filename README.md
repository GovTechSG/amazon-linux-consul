# `amazon-linux-consul`

Contains Ansible playbook role for installation of Consul client agent on
Amazon Linux based AMI.

Useful for setting up Consul on [AWS EMR](https://aws.amazon.com/emr/), which
only accept Amazon Linux based AMI at the moment.

This is generally a split-off repository from
[terraform-modules](https://github.com/GovTechSG/terraform-modules), which was
catered more for `Ubuntu` based AMI.

## How to use

Assuming your current set-up already uses Ansible playbook as one of the
`provisioners` to build the custom AMI, to include installation of `consul` into
the image, add the following playbook configuration value as one of your
`tasks`:

```yml
- name: Install Consul
  include_role:
    name: "{{ path_to_dir_before_submod }}/amazon-linux-consul/modules/core/packer/roles/consul-yum"
```

The above also assumes that `amazon-linux-consul` is a submodule within your
main repository.

## How to get Consul agent running

For Amazon Linux based AMI, one of the ways to get `consul` running is to add
the line to start it under `/etc/rc.local`. The below example (Ansible playbook
for `packer`) demonstrates adding a line to start `consul` in `rc.local`, with
some commonly used parameters.

```yml
- name: Set Consul agent to run at startup
  lineinfile:
    path: /etc/rc.local
    line: "/opt/consul/bin/run-consul --client --cluster-tag-key {{ cluster_tag_key }} --cluster-tag-value {{ cluster_tag_value }}"
    state: present
  become: yes
```

Starting / rebooting the instance using the custom AMI with all the above items
should get it working / very close to working, allowing it to communicate with
`consul` servers.
