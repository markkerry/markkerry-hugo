---
title: "Ansible Part 3: Variables, Vault and Roles"
date: 2022-03-11T14:30:10Z
draft: true
tags: ["Linux", "Ubuntu", "VirtualBox", "Ansible", "Apache"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "Learn about using variables in your playbooks, Ansible Vault and Ansible Roles"
    relative: false
---

Write about previous posts. List them

1. Post 1
2. Post 2

## Variables and Debug

Ansible allows you to create local variables in your playbooks. We can use **vars** to create key/value pairs or you can use variable files and import them into the playbook. Variables uses the Jinger2 syntax.

In the example below I have...

```yaml
---
  - hosts: webservers
    become: true  
    
    vars:
      app_path: "/var/www/html"
      
    tasks:
      - name: Copy app files
        copy:
          src: ../index.php
          dest: {{ app_path }}
          mode: 0755
        notify: restart apache
          
      - name: Delete index.html
        ansible.builtin.file:
          path: "{{ app_path }}/index.html"
          state: absent
        notify: restart apache
```

During the **TASK [Gathering Facts]** step, Ansible will create a variable for us called **ansible_facts**, which holds metadata about the host we are interacting with. This information, such as the hosts IP address, from the **ansible_facts** variable can be injected into future tasks.

Using the **status** module, we can see the information gathered in the **TASK [Gathering Facts]** step.

There is also a handy module call **setup** which will return a lot of information about a host which can be used as variables in playbooks.

```terminal
ansible -m setup web1
```

Ansible allows you to use the **debug** module to see STDOUT information from the playbook, such as variables set and tasks that are ran. The example below shows a playbook which runs a command to list the contents of a directory and "registers" it as **dir_contents** variable. The debug module then returns the captured information in the variable to STDOUT.

Below I am ....

```yaml
---
  vars:
    path_to_app: "/var/www/html"
    
  tasks:
    - name: see directory contents
      command: ls -al {{ path_to_app }}
      register: dir_contents
      
    - name: debug directory contents
      debug:
        msg: "{{ dir_contents }}"
```

I have updated the `app-setup.yaml` file to include the variables and debug module, which now looks as follows:

```yaml
# app-setup.yaml
---
  - hosts: webservers
    become: true

    vars:
      app_path: "/var/www/html"

    tasks:
      - name: Copy app files
        copy:
          src: ../index.php
          dest: "{{ app_path }}"
          mode: 0755
        notify: restart apache

      - name: Delete index.html
        ansible.builtin.file:
          path: "{{ app_path }}/index.html"
          state: absent
        notify: restart apache

      - name: gather directory contents
        command: ls -al {{ app_path }}
        register: dir_content

      - name: show directory contents
        debug:
          msg: "{{ dir_content }}"

    handlers:
      - name: restart apache
        service: name=apache2 state=restarted
```

GATHER GIF OF IT RUNNING

## Ansible Vault

Ansible Vault allows us to keep sensitive information, such as passwords or variables, in encrypted files rather than in plan text in a playbook.

The vault is password protected with the default cipher of AES256.

```teminal
ansible-vault create vault/secret-vars.yaml
```

enter the password twice.

![vaultCreate](images/vaultCreate.png)

You will be to VIM to fill in the variables in your vault;

```yaml
tenant_id: "000aaa-111bbb-222ccc-333ddd"
tenant_name: "examplename.com"
```

Exit VIM

To edit the vault again use

```yaml
ansible-vault edit vault/secret-vars.yaml
```

Then you can reference this vault in a playbook by specifing `vars_files` and then can referenec it using as `task` follows

```terminal
vim playbooks/vault-example.yaml
```

Fill as follows:

```yaml
# vault-example.yaml
---
  - hosts: loadbalancers
      
    vars_files:
      - ../vault/secret-vars.yaml
      
    tasks:
      - name: show tenant_id var
        debug:
          msg: "{{ tenant_id }}"
      - name: show tenant var
        debug:
          msg: "{{ tenant_name }}"
```

Run it

```terminal
ansible-playbook playbooks/vault-example.yaml --ask-vault-pass
```

![ask-vault-pass](images/ask-vault-pass.png)

## Ansible Roles

Ansible Roles allow you to create a framework to separate out (make more modular) each part of the configuration. E.g. variables, tasks, and templates in their self contained directory. It break-up the configuration into files which can be re-used and easier to modify.

create a new directory

```terminal
mkdir ~/ansible-roles
```

### Create the Role

We can create a role with the following command:

```terminal
ansible-galaxy role init ansible-roles/webservers
```

I then copied the following three files I've been using to configure the webservers to the following directories:

* `ansible-roles/webservers/app-setup.yaml`
* `ansible-roles/webservers/hosts-dev`
* `ansible-roles/webservers/files/index.php`

The directory structure looks as follows:

```terminal
├── app-setup.yaml
├── hosts-dev
└── webservers
    ├── defaults
    │   └── main.yml
    ├── files
    │   └── index.php
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── README.md
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml

9 directories, 12 files
```

### Configure the Role

With the app-setup.yaml, hosts-dev and index.php in the correct place I can now configure the rest of the files in the webservers role.

Copy the `vars` content from `app-setup.yaml` into `ansible-roles/webservers/vars/main.yaml`

The `vars/main.yaml` file will look as follows:

```yaml
---
# vars file for ansible-roles/webservers
app_path: "/var/www/html"
```

Then, copy the `handlers` section from `app-setup.yaml` into `ansible-roles/webservers/handlers/main.yaml`

The `handlers/main.yaml` file will look as follows:

```yaml
---
# handlers file for ansible-roles/webservers
- name: restart apache
  service: name=apache2 state=restarted
```

Then, copy the `tasks` section from `app-setup.yaml` into `ansible-roles/webservers/tasks/main.yaml`

The `tasks/main.yaml` file will look as follows:

```yaml
---
# tasks file for ansible-roles/webservers
- name: Copy app files
  copy:
    src: ../files/index.php
    dest: "{{ app_path }}"
    mode: 0755
  notify: restart apache
    
- name: Delete index.html
  ansible.builtin.file:
    path: "{{ app_path }}/index.html"
    state: absent
  notify: restart apache
  
- name: gather directory contents
  command: ls -al {{ app_path }}
  register: dir_content
  
- name: show directory contents
  debug:
    msg: "{{ dir_content }}"
```

And finally, replace the content of the `ansible-roles/webservers/app-setup.yaml` with the following:

```yaml
# app-setup.yaml
---
  - hosts: webservers
    become: true
    roles:
      - webservers
```

### Run the Role Playbook

Now it's time to run the new playbook from the `~/ansible-roles` directory

```terminal
ansible-playbook ansible-roles/app-setup.yaml -K
```