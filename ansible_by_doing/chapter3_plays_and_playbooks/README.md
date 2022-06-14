# Intro to playbooks

### Ansible Playbooks offer a repeatable, re-usable, simple configuration management and  multi-machine deployment system.

## playbook synax
### Playbooks are expressed in YAML syntax

<br/>

## Playbook execution

### A playbook runs in order from top to bottom.

### At minimum, each play defines two things:

* the managed nodes to target, using pattern
* at least one task to execute

~~~YAML
---
- name: Update web servers
  hosts: webservers
  remote_user: root

  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest
  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: Update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest
  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
~~~

<br/>

## Task execution

###  By default, Ansible executes each task in order, one at a time, aginst all machines matched by the host pattern.  

###  When you run a playbook, Ansible returns information about coneections, the name lines of all your plays and taks, whether each task has secceeded or failed on each machine , and whether  each task has made a change on each machine.

## Desired state and ‘idempotency

### Most Ansible modules check whether the desired final state has already been achieved, and exit without performing any actions if that state has been achieved, so that repeating the task does not change the final state

## Running playbooks

~~~bash
ansible-playbook playbook.yml -f 10
~~~
