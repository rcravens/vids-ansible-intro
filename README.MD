# ⚡️ ANSIBLE CODE

## LINKS
- <a href="https://www.ansible.com/">Ansible Home Page</a>
- <a href="https://docs.ansible.com/ansible/latest/getting_started/index.html">Terminology</a>
- <a href="https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html">Basic Concepts</a>
- <a href="https://docs.ansible.com/ansible/latest/command_guide/intro_adhoc.html">Ad-Hoc Commands</a>
- <a href="https://docs.ansible.com/ansible/2.9/modules/list_of_all_modules.html">Built-in Modules</a>
  - <a href="https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html">Ping Module</a>
  - <a href="https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html">Service Module</a>
- <a href="https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_templating.html">Jinja2 Templating</a>
- <a href="https://docs.ansible.com/ansible/latest/collections/amazon/aws/index.html">AWS Collection</a>
  - <a href="https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_instance_module.html">EC2 Instance Module</a>

## Background

> Ansible automates the management of remote systems (computers) and their desired state.

With Ansible you can create Infrastructure as Code that automates the provisioning and updates of your development environments.

### Terminology

<a href="https://docs.ansible.com/ansible/latest/getting_started/index.html">Terminology</a>

- ansible runs on the **control node**
- remotes systems are **managed nodes**
- **managed nodes** are cataloged into an **inventory**
- **managed nodes** in the **inventory** are organized into **groups**
- ansible runs **tasks** on **groups** of **managed nodes** in the **inventory**
- **tasks** can be organized into **plays**
- **plays** can be organized into **playbooks**
- **playbooks** can orchestrate larger changes

> [!IMPORTANT]
> ***Idempotent*** - Any process that can be executed many times without changing the final result beyond the first iterations.

### Under The Hood

<a href="https://docs.ansible.com/ansible/latest/getting_started/basic_concepts.html">Basic Concepts</a>

When you run a **playbook** ansible does the following:
1. Creates a python "module" that represents the desired system state.
2. Uses SSH to execute the "module" on each **managed node** in the **group**.
3. Deletes the "module" from the **managed node**

### Requirements

| managed nodes                | control node           |
|------------------------------|------------------------|
| python                       | python                 |
| user account with ssh access | ansible python package |

A lot of other Infrastructure as Code / configuration management tools require more complex setups. Especially those that require "agents" installed on each **managed node**.

## Examples
The following introduce some basic building blocks and concepts. While this are simplistic in nature, you can springboard into your more complex configurations from here.
> [!IMPORTANT]
> The IP ADDRESSES in the `iventory.ini` and `inventory.yml` files **WILL NOT WORK** if you run these on your own. You will need to update those IP ADDRESSES to computers that you manage.



### Command Line and Ad-hoc Examples
Get the version
- `ansible --version`

Get a list of *inventory* (**managed nodes**)
- `ansible-inventory -i inventory.ini --list`

Using the ping module
- `ansible jump_box -m ping -i inventory.ini` (NOTE: this will fail because no credentials supplied)
- `ansible -u ubuntu --key-file="~/Desktop/laravel_demo/laravel_demo.pem" jump_box -m ping -i inventory.ini`

Using the `inventory.yml` file with variables
- `ansible-inventory -i inventory.yml --list`
- `ansible jump_box -m ping -i inventory.yml`
- `ansible all -m ping -i inventory.yml`

Running ad-hoc commands on **managed nodes**
- `ansible all -a "lsb_release -ac" -i inventory.yml`
- `ansible jump_box -m ansible.builtin.setup -i inventory.yml`
- `ansible jump_box -m ansible.builtin.apt -a "name=nginx state=present" -i inventory.yml`
- `ansible jump_box -m ansible.builtin.service -a "name=nginx state=started enabled=true" -i inventory.yml`

### Playbook #1
In the `playbook1` directory, there are the following examples:

`01_ad_hoc_examples.yml` - Recreates some of the ad-hoc examples but using a playbook.
- `ansible-playbook -i inventory.yml playbook1/01_ad_hoc_examples.yml`
- `ansible-playbook playbook1/01_ad_hoc_examples.yml` (can shorten because of `ansible.cfg`)

`02_inspection.yml` - Introduces: `hosts`, `gather_facts`, `groups`, `hostvars`, `run_once`, `with_items` and a few other concepts.
- `ansible-playbook playbook2/playbook.yml`

`03_variables` - Introduces the use of variables.
- `ansible-playbook playbook3/playbook.yml`
- `ansible encrypt playbook3/secrets.yml`
- `ansible decrypt playbook3/secrets.yml`
- `ansible decrypt playbook3/secrets.yml`
- `ansible-playbook --ask-vault-pass playbook3/playbook.yml`
