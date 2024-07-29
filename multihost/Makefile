SSH_CONFIG := ../ssh_config
SCP_CMD = scp -F ${SSH_CONFIG} -i ssh_key
SSH_CMD = ssh -F ${SSH_CONFIG}

VM := mycephfs11

ssh: copy_keys
	${SSH_CMD}  -i ssh_key root@${VM}

keygen:
	rm -f ssh_key ssh_key.pub && ssh-keygen -N "" -f ssh_key
sshconfig: vagrantup
	vagrant ssh-config | sed -e 's/IdentityFile .*/IdentityFile ssh_key/g'  > ${SSH_CONFIG} 


vagrantup:
	vagrant up

copy_keys: keygen vagrantup sshconfig
#	 ssh-copy-id -f -F ${SSH_CONFIG} -i ssh_key ${VM}
	${SCP_CMD} ssh_key.pub ${VM}:
	${SSH_CMD} ${VM}  "cat ssh_key.pub >> .ssh/authorized_keys "
#	${SCP_CMD} ssh_key* my${VM}:.ssh/id_rsa
#	${SSH_CMD} ${VM}  "chmod 0700 /.ssh/id_rsa"

	${SSH_CMD} ${VM}  "sudo cp -p .ssh/* /root/.ssh/"

start: vagrantup copy_keys provision

stop:
	vagrant destroy -f
clean: stop
	rm -rf .vagrant ssh_key ssh_key.pub

provision:
	${SCP_CMD} -i ./ssh_key -r  ../resources root@${VM}:
	${SCP_CMD} -i ./ssh_key  provision.sh root@${VM}:/tmp/provision.sh
	${SSH_CMD} -i ./ssh_key root@${VM} "/bin/bash /tmp/provision.sh"

.PHONY: clean ssh keygen vagrantup sshconfig copy_keys start stop provision
