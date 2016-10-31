Ansible spin up of AWS/EC2 instance with go environment, qor installed.   
The playbook.yml file and much of the roles/ directory originally came from https://github.com/samuell/devbox-golang.git
###############################################################################
# Contents
###############################################################################
    README.md:      The file you're currently reading.
    launch.yml:     Start ec2-go instance from scratch.
    halt.yml:       Stop ec2-go instance.
    destroy.yml:    Destroy ec2-go instance (no coming back).
    restart.yml:    Restart a stopped ec2-go instance.
    playbook.yml:   Provision ec2-go instance for qor-example development.
    ansible.cfg:    Ansible configuration file pointing to our hosts.ini.
    hosts.ini:      Define host groups.
    group_vars/
        local/
            vars:   Variables for local host group.
            vault:  Encrypted variables for local host group (aws_secret_access_key is in here).
        ec2/
            vars:   Variables for ec2 host group.
            vault:  Encrypted variables for ec2 host group (database passwords are in here).
    roles/
        dotfiles/
            files/
                .bash_aliases:
                .bashrc_mods:
                .gitignore:
                .tmux.conf:
                .vim:
                .vimrc:
                .vimrc_vundle:
            tasks/
                main.yml:
        golang/
            tasks/
                main.yml:
            vars/
                main.yml:
        mysql/
            tasks/
                main.yml:
                mysql.yml:
                sqlite.yml:
            templates/
                my.cnf.j2:
                mysql_root.my.cnf.j2:
        mesos/
            tasks/
                main.yml:
        youcompleteme/
            tasks/
                main.yml:
###############################################################################
# Instructions
###############################################################################
These instructions assume that you have an AWS account, that you know your AWS access key ID and secret access key, and that you have a valid SSH keypair configured in your AWS account.

1. Edit group_vars/local/vars and replace the variable values with appropriate ones:

    ###############################################################################
    ### Access to AWS. Needed by ec2_remote_facts and ec2 modules.
    ###############################################################################
    
    access_key_id: AKIAI7PTRVXWCMQ7TFHA   
    secret_access_key: "{{ vault_secret_access_key }}"   
    
    ###############################################################################
    ### EC2 specific parameters, region, security group, keyname, etc.
    ###############################################################################
    
    instance_type:    t2.micro   
    region:           ap-southeast-1   
    image:            ami-1c2e887f   
    user:             ubuntu   
    security_group:   vagrantbox   
    keypair:          sg-ec2-vagrant   

2. Run `ansible-vault edit group_vars/local/vault` from your playbook root to edit your AWS_SECRET_ACCESS_KEY. Of course, you won't have the password so it'll be best to replace this file with your own. I.e.
    ```
    ---
      vault_secret_access_key: YAI8euhEGsEC*SntE*sOT#YOUbetterNOTbelievIT
    ```
Then encrypt it with `ansible-vault encrypt path/to/vault`   

3. Edit `group_vars/ec2/vars` and replace the variable values with appropriate ones:

4. Repeat step 2 for the vault file in `group_vars/ec2/` 

5. Create new instance with `ansible-playbook launch.yml --ask-vault-pass`, destroy it with `ansible-playbook destroy.yml --ask-vault-pass`, etc.
