Role Name
=========

Ansible role accumulates another roles for spin up server for staging purposes including consul server,
prometheus server, grafana all in separate docker containers and all UIs behind nginx with basic authorization.
Tested on:
  - Ubuntu 14.04 Trusty
  - Ubuntu 16.04 Xenial

Requirements
------------

Installed OS:
 - Ubuntu 14.04 Trusty
 - Ubuntu 16.04 Xenial

Role Variables
--------------

All variables defined in corresponding roles and it can be overwrited by adding variables with the same name in
playbook `main.yml` vars section. Example:

```yaml
---
vars:
  GRAFANA_PORT: 3001 # This variable will overwrite default variable `GRAFANA_PORT: 3000` in role `winmasta.grafana`
```

Dependencies
------------

Depends on:
 - winmasta.docker-latest
 - winmasta.nginx
 - winmasta.consul
 - winmasta.prometheus
 - winmasta.grafana

 Example Playbook
 ----------------

 To use this role:

   - create folder (in user $HOME folder in example below) and install role with dependencies from ansible-galaxy

 ```bash
 cd ~/
 mkdir staging-server
 cd staging-server
 ansible-galaxy install winmasta.consul-server --roles-path .
 ```

   - as soon as ansible-galaxy doesn't install role dependencies yet, you should do it manually

 ```bash
 ansible-galaxy install -r winmasta.consul-server/requirements.yml --roles-path .
 ```

   - create file `hosts`, containing hostname(s) or IP address(es) of host(s), where you want to deploy staging-server

 ```bash
 echo "ENTER HOSTNAME OR IP" > hosts
 ```

   - create file `ansible.cfg` in current folder

 ```bash
 cat > ansible.cfg << EOF
 [defaults]
 remote_user = root
 host_key_checking = False
 EOF
 ```

   - create playbook in current folder `main.yml` with content

 ```bash
 cat > main.yml << EOF
 ---
 - hosts: all
   gather_facts: no

   pre_tasks:

   - name: Install required packages
     raw: sudo apt-get update -y && sudo apt-get -y install python-simplejson python-pip
     changed_when: False

   - setup:

   roles:
     - winmasta.docker-latest
     - winmasta.nginx
     - winmasta.consul-server
     - winmasta.prometheus
     - winmasta.grafana
 EOF
 ```

   - execute playbook `main.yml`

 ```bash
 ansible-playbook -i hosts main.yml
 ```


License
-------

MIT
