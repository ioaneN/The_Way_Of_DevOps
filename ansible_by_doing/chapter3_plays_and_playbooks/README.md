# Intro to playbooks

### Ansible Playbooks offer a repeatable, re-usable, simple configuration management and  multi-machine deployment system.

## playbook syntax
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

<br/>

# Error handling in playbooks

### By default Ansible stops executing tasks on a host when a task fails on that host. You can use ignore_errors to continue on in spite of the failure.

~~~YML
- name: Do not count this as a failure
  command: /bin/false
  ignore_errors: yes
~~~

<br/>

### You can ignore a task failure due to the host instance being 'UNREACHABLE'
with the ***ignore_unrichable***

~~~yaml
- hosts: all
  ignore_unreachable: yes
  tasks: 
  - name: This executes, fail, and the failure is ignored
    command: /bin/true
~~~

## Handlres and failure

### If a task notifies a handler but another task fails later in the play, by default the handler does not run on that host, which may leave the host in an unexpected state.

### You can change this behavior with the ***--force-handlers*** command-line option, by including **force_handlers: True** in a play, or by adding **force_handlers = True** to ansible.cfg

<br/>

## Definine failure 

### Ansible lets you define what “failure” means in each task using the **failed_when** conditional. As with all conditionals in Ansible, lists of multiple **failed_when** conditions are joined with an implicit and, meaning the task only fails when all conditions are met

~~~yml
- name: Fail task when the command error output prints FAILED
  ansible.builtin.command: /usr/bin/example-command -x -y -z
  register: command_result
  failed_when: "'FAILED' in command_result.stderr"
~~~


### or based on the return code

~~~yml
- name: Fail task when both files are identical
  raw: diff foo/file1 bar/file2
  register: diff_cmd
  failed_when: diff_cmd.rc == 0 or diff_cmd.rc >= 2
~~~
