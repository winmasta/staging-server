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

For dependency roles `winmasta.CA` and `winmasta.ELK` there is one common variabvle `USE_TLS`, which enables `True`
or disables `False` using TLS for consul and logstash beats. If TLS is not required `USE_TLS` can be set to `False`
and in that case, role `winmasta.CA` can be commented in `requirements.yml`. Example:

```yaml
- src: winmasta.docker-latest
- src: winmasta.nginx
# - src: winmasta.CA
- src: winmasta.consul-server
- src: winmasta.prometheus-server
- src: winmasta.grafana
- src: winmasta.ELK
```

All variables defined in corresponding roles and it can be overwritten by adding variables with the same name in
playbook `main.yml` vars section. Example:

```yaml
vars:
  GRAFANA_PORT: 3001 # This variable will overwrite default variable `GRAFANA_PORT: 3000` in role `winmasta.grafana`
  NGINX_DEFAULT_PASSWD: another_password # So as this
```

Also variables can be passed witn ansible `--extra-vars`. Example:
```bash
ansible-playbook -i hosts main.yml --extra-vars "USE_TLS=False"
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
 ansible-galaxy install winmasta.staging-server --roles-path .
 ```

   - as soon as ansible-galaxy doesn't install role dependencies yet, you should do it manually

 ```bash
 ansible-galaxy install -r winmasta.staging-server/requirements.yml --roles-path .
 ```

   - create file `hosts`, containing hostname(s) or IP address(es) of host(s), where you want to deploy role

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
     - winmasta.CA
     - winmasta.consul-server
     - winmasta.prometheus-server
     - winmasta.grafana
     - winmasta.ELK
 EOF
 ```

   - execute playbook `main.yml`

 ```bash
 ansible-playbook -i hosts main.yml
 ```


License
-------

MIT
