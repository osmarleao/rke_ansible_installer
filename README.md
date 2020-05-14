### RKE Installer on Enterprise Linux

- Expects Enterprise Linux (CentOS, RHEL) 8 or higher hosts

These playbooks deploy a RKE version 2.x on top of hosts/VMs.
To use them, first edit the `hosts` inventory file to contain the hostnames of the machines on which you want RKE deployed.

You need to add at least a worker and a master node. These nodes may be the same.

There is an example of `hosts` file:

    # OLCNE API Server
    api-server  ansible_host=api-server.inat.leao.pro.br

    # Kubernetes Master
    [masters]
    master      ansible_host=master.inat.leao.pro.br

    # Kubernetes Workers
    [workers]
    worker01    ansible_host=worker01.inat.leao.pro.br
    worker02    ansible_host=worker02.inat.leao.pro.br

    # Kubernetes hosts
    [kubernetes:children]
    masters
    workers

Then edit `group_vars/api_servers` file to edit self-signed certification data as below (do not forget to add all FQDN of hostnames of your installation on nodes variables) and RSA key file with root password for API-Server:

    # Certificate information
    org_unit: Labs
    org: Leao Pro
    locality: Salvador
    state: Bahia
    country: BR
    common_name: cloud.inat.leao.pro.br

    # All nodes to be installed (FQDN)
    master_ip_addr: 192.168.86.23
    master: master.inat.leao.pro.br:8090
    workers: worker01.inat.leao.pro.br:8090,worker02.inat.leao.pro.br:8090
    all_nodes: api-server.inat.leao.pro.br,master.inat.leao.pro.br,worker01.inat.leao.pro.br,worker02.inat.leao.pro.br

    # For use with SSH Public Key Managenment
    id_rsa_file: /root/.ssh/id_rsa
    root_password: "PASSWORD"

And last, edit `group_vars/all` to setup proxy information of your installation and the Kubernetes environment information:

    # Use Proxy?
    use_proxy: False

    # Proxy URL and Rules
    proxy_host: "http://proxy.inat.leao.pro.br:80"
    no_proxy: ".inat.leao.pro.br,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16"

    # Configure an Environment?
    config_env: True
    env_name: myenv
    cluster: mycluster

    # Configure Istio module?
    use_istio: True
    istio_module: myistio

    # Helm is required to deploy Istio
    use_helm: True 
    helm_module: myhelm

Note: edit `roles/common/tasks/main.yml` and uncomment the yum update lines to update your hosts in the same process of installs OLCNE (much slower):

    # Configure general environment
    -
      name: Update all hosts
      yum:
        name: '*'
        state: latest

 ### Deploy Oracle Linux Cloud Native Environment

 The site.yml may be used to deploy a full Oracle Linux Cloud Native Environment.

 Run the playbook using:

    ansible-playbook -i hosts site.yml

 ### Creating a Kubernetes Cluster and Other Modules

 After playbook is completed, you can setup a Kubernetes cluster, others modules and a user to control K8s following this doc: https://docs.oracle.com/en/operating-systems/olcne/start/deploy-kube.html
