### RKE Installer on Enterprise Linux

- Expects Enterprise Linux (CentOS, RHEL) 8 or higher hosts

These playbooks deploy a RKE version 2.x on top of hosts/VMs.
To use them, first edit the `hosts` inventory file to contain the hostnames of the machines on which you want RKE deployed.

You need to add at least a worker and a master node. These nodes may be the same.

There is an example of `hosts` file:

    # Kubernetes Master
    [masters]
    node01      ansible_host=node01.in.leao.pro.br ansible_user=root

    # Kubernetes Workers
    [workers]
    node02      ansible_host=node02.in.leao.pro.br ansible_user=root
    node03      ansible_host=node03.in.leao.pro.br ansible_user=root

    # Kubernetes hosts
    [kubernetes:children]
    masters
    workers

Note: edit `roles/common/tasks/main.yml` and uncomment the yum update lines to update your hosts in the same process of installs RKE (much slower):

    # Configure general environment
    -
      name: Update all hosts
      yum:
        name: '*'
        state: latest

 ### Deploy RKE

 The site.yml may be used to deploy a full RKE.

 Run the playbook using:

    ansible-playbook -i hosts site.yml
