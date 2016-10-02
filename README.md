# Jupyer Notebooks Server on Digital Ocean

### Pre-ansible

Create a droplet, get the ip and password from the confirmation email and connect to the server as root to change the password:
```
ssh root@138.68.137.72
```

## Ansible

Place the server ip in hosts:
```
echo "138.68.137.72" > hosts
```

### Get passwords, certificates and whatnots

Get hashed passwords for root and the jupyter user, place them in setup.yml:
```
python -c "from passlib.hash import sha512_crypt; import getpass; print sha512_crypt.encrypt(getpass.getpass())"
```
that is ROOT_PASS and JUPYTER_PASS.

Generate openssl certificate for Jupyter ssl, they will be copied in the steps below:
```
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout jupyter.key -out jupyter.pem
```

Password for Jupyter website, place hash in jupyter_notebook_config.py:
```
ipython -c "from IPython.lib import passwd; passwd()" 
```
that is WEB_PASS.

### Apply playbooks

Setup users and pip (as root):
```
ansible-playbook setup.yml -i hosts -u root --ask-pass
```

Configure the Jupyter Notebooks server:
```
ansible-playbook jupyter.yml -i hosts -u jupyter --ask-pass
```

## All ready

Connect to the server on:
```
https://138.68.137.72:9999/jupyter
```

