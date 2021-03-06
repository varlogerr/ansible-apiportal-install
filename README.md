## Requirements

### Ansible control machine

* ansible ([installation instructions][ansible installation])
* (optional) if you plan to use `gen-vars.sh` tool `python` or `python3` should be in the PATH
* (optional) if you plan to configure ansible to connect to target machines with username and password (not ssh keys), `sshpass` must be installed

### Target machines

* CentOS 7 or version 8.* of CentOS / Alma Linux / Rocky Linux
* ssh server running (`sudo dnf install -y openssh-server && sudo systemctl enable --now sshd`)
* python
  * python3 for CentOS 8 and its successors (`dnf install python3`). It will work without any additional configurations
  * python (version 2) for CentOS 7 (`yum install python`). You will have to set the interpreter path with `ansible_python_interpreter` variable for individual or group of hosts
* create sudo user (optional, if you want to offload ansible tasks to a system user):

  ```bash
  # create a control user, for example "ansible"
  sudo useradd -rms /bin/bash ansible
  # make control user sudoer
  sudo usermod -aG wheel ansible
  # give him a password
  sudo passwd ansible
  ```

## Usage

**Note**: all the code snippets and instructions below are meant for UNIX-like OSes. In case you use something else translate them to your OS commands

Login to your ansible control machine.

1) Clone the repo:

    ```bash
    # replace <ansible-apiportal repo url> with the actual
    # repo url, clone the repo to some directory (for 
    # example portalbook) and cd to there
    git clone <ansible-apiportal repo url> portalbook
    cd portalbook
    ```

2) Install required collections:

    ```bash
    ansible-galaxy collection install -r ./requirements.yml -p ./requirements
    ```

3) Initialize configuration directory with `init-conf.sh` script:

    ```bash
    # read init-conf.sh script help
    ./bin/init-conf.sh -h
    # initialize configuration directory with copying
    # API Portal installation package there
    ./bin/init-conf.sh ~/Downloads/apiporta-install.tgz
    ```

    Or manually:

    ```bash
    mkdir -p conf/{group_vars,host_vars,secrets}
    cp samples/inv.yml conf
    cp samples/conf.gitignore conf/.gitignore
    touch conf/group_vars/.gitignore
    touch conf/host_vars/.gitignore
    touch conf/secrets/.gitignore
    cp ~/Downloads/apiporta-install.tgz ./conf
    ```

4) Configure deployment inventory:

    ```bash
    # edit inventory file
    vim conf/inv.yml
    ```

5) Generate host vars file with `gen-vars.sh` script:

    ```bash
    # read gen-vars.sh script help
    ./bin/gen-vars.sh -h
    # generate vars file (given you ansible target
    # host name is "apiportal")
    ./bin/gen-vars.sh conf/host_vars/apiportal.yml
    ```

    Or manually:

    ```bash
    cp samples/vars.yml conf/host_vars/apiportal.yml
    ```

6) Configure installation settings:

    ```bash
    # edit variables file
    vim conf/host_vars/apiportal.yml
    ```

7) Run ansible playbook

    ```bash
    # run the playbook using the inventory file
    ansible-playbook -i conf/inv.yml apiportal.yml
    ```

[ansible installation]: https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
