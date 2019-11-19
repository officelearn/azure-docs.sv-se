---
title: Självstudie – konfigurera Kubernetes-nätverk i Azure Kubernetes service (AKS) med hjälp av Ansible
description: Lär dig hur du använder Ansible för att konfigurera Kubernetes-nätverk i Azure Kubernetes service (AKS)-kluster
keywords: Ansible, Azure, DevOps, bash, cloudshell, Spelbok, AKS, container, AKS, Kubernetes
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: bfb19371ad651439c087cebd03023d48852ee2df
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156894"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Självstudie: Konfigurera Kubernetes-nätverk i Azure Kubernetes service (AKS) med hjälp av Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Med AKS kan du distribuera ett kluster med hjälp av följande nätverks modeller:

- [Kubernetes nätverk](/azure/aks/configure-kubenet) – nätverks resurser skapas och konfigureras vanligt vis när AKS-klustret distribueras.
- [Nätverk för cni-AKS (Azure Container Networking Interface)](/azure/aks/configure-azure-cni) är anslutet till befintliga virtuella nätverks resurser och konfigurationer.

Mer information om nätverk till dina program i AKS finns i [nätverks begrepp för program i AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett AKS-kluster
> * Konfigurera Azure Kubernetes Networking

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Spelbok-koden i det här avsnittet skapar följande Azure-resurser:

- Virtuellt nätverk
- Undernät i det virtuella nätverket

Spara följande spelbok som `vnet.yml`:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Skapa ett AKS-kluster i det virtuella nätverket

Spelbok-koden i det här avsnittet skapar ett AKS-kluster i ett virtuellt nätverk. 

Spara följande spelbok som `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with vnet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: kubenet
          pod_cidr: 192.168.0.0/16
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.0.0.10
          service_cidr: 10.0.0.0/16
  register: aks
```

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- Använd `azure_rm_aks_version`-modulen för att hitta den version som stöds.
- `vnet_subnet_id` är det undernät som skapades i föregående avsnitt.
- `network_profile` definierar egenskaperna för plugin-programmet för Kubernetes-nätverk.
- `service_cidr` används för att tilldela interna tjänster i AKS-klustret till en IP-adress. Detta IP-adressintervall ska vara ett adress utrymme som inte används någon annan stans i nätverket. 
- `dns_service_ip` adressen ska vara ". 10"-adressen till tjänstens IP-adressintervall.
- `pod_cidr` bör vara ett stort adress utrymme som inte används någon annan stans i din nätverks miljö. Adress intervallet måste vara tillräckligt stort för att rymma antalet noder som du förväntar dig att skala upp till. Du kan inte ändra det här adress intervallet när klustret har distribuerats.
- IP-adressintervallet Pod används för att tilldela ett/24-adressutrymme till varje nod i klustret. I följande exempel tilldelar `pod_cidr` 192.168.0.0/16 den första noden 192.168.0.0/24, den andra noden 192.168.1.0/24 och den tredje noden 192.168.2.0/24.
- När klustret skalas eller uppgraderas fortsätter Azure att tilldela ett Pod IP-adressintervall till varje ny nod.
- Spelbok läser in `ssh_key` från `~/.ssh/id_rsa.pub`. Om du ändrar det använder du det enkla formatet – från och med "SSH-RSA" (utan citationstecken).
- Värdena för `client_id` och `client_secret` läses in från `~/.azure/credentials`, vilket är standard filen för autentiseringsuppgifter. Du kan ange dessa värden till tjänstens huvud namn eller läsa in dessa värden från miljövariablerna:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associera nätverks resurserna

När du skapar ett AKS-kluster skapas en nätverks säkerhets grupp och en routningstabell. Dessa resurser hanteras av AKS och uppdateras när du skapar och exponerar tjänster. Koppla nätverks säkerhets gruppen och routningstabellen till det virtuella nätverkets undernät enligt följande. 

Spara följande Spelbok som `associate.yml`.

```yml
- name: Get route table
  azure_rm_routetable_facts:
      resource_group: "{{ node_resource_group }}"
  register: routetable

- name: Get network security group
  azure_rm_securitygroup_facts:
      resource_group: "{{ node_resource_group }}"
  register: nsg

- name: Parse subnet id
  set_fact:
      subnet_name: "{{ vnet_subnet_id | regex_search(subnet_regex, '\\1') }}"
      subnet_rg: "{{ vnet_subnet_id | regex_search(rg_regex, '\\1') }}"
      subnet_vn: "{{ vnet_subnet_id | regex_search(vn_regex, '\\1') }}"
  vars:
      subnet_regex: '/subnets/(.+)'
      rg_regex: '/resourceGroups/(.+?)/'
      vn_regex: '/virtualNetworks/(.+?)/'

- name: Associate network resources with the node subnet
  azure_rm_subnet:
      name: "{{ subnet_name[0] }}"
      resource_group: "{{  subnet_rg[0] }}"
      virtual_network_name: "{{ subnet_vn[0] }}"
      security_group: "{{ nsg.ansible_facts.azure_securitygroups[0].id }}"
      route_table: "{{ routetable.route_tables[0].id }}"
```

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- `node_resource_group` är namnet på den resurs grupp som AKS-noderna skapas i.
- `vnet_subnet_id` är det undernät som skapades i föregående avsnitt.


## <a name="run-the-sample-playbook"></a>Kör exemplet Spelbok

I det här avsnittet visas den fullständiga Spelbok som anropar de uppgifter som skapas i den här artikeln. 

Spara följande spelbok som `aks-kubenet.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Associate network resources with the node subnet
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
           node_resource_group: "{{ aks.node_resource_group }}"
       include_tasks: associate.yml

     - name: Get details of the AKS
       azure_rm_aks_facts:
           name: "{{ name }}"
           resource_group: "{{ resource_group }}"
           show_kubeconfig: user
       register: output

     - name: Show AKS cluster detail
       debug:
           var: output.aks[0]
```

Gör följande ändringar i avsnittet `vars`:

- Ändra `aksansibletest` värde till namnet på din resurs grupp för `resource_group` nyckeln.
- Ändra `aksansibletest`-värdet till AKS-namnet för den `name` nyckeln.
- Ändra `eastus` värde till din resurs grupps plats för `Location` nyckeln.

Kör den fullständiga Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook aks-kubenet.yml
```

Om du kör Spelbok visas resultat som liknar följande utdata:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exist] 
ok: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-kubenet/vnet.yml for localhost

TASK [Create vnet] 
ok: [localhost]

TASK [Create subnet] 
ok: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-kubenet/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Associate network resources with the node subnet] 
included: /home/devops/aks-kubenet/associate.yml for localhost

TASK [Get route table] 
ok: [localhost]

TASK [Get network security group] 
ok: [localhost]

TASK [Parse subnet id] 
ok: [localhost]

TASK [Associate network resources with the node subnet] 
changed: [localhost]

TASK [Get details of the AKS] 
ok: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "output.aks[0]": {
        "id": /subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "apiVersion: ...",
        "location": "eastus",
        "name": "aksansibletest",
        "properties": {
            "agentPoolProfiles": [
                {
                    "count": 3,
                    "maxPods": 110,
                    "name": "nodepool1",
                    "osDiskSizeGB": 100,
                    "osType": "Linux",
                    "storageProfile": "ManagedDisks",
                    "vmSize": "Standard_D2_v2",
                    "vnetSubnetID": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
                }
            ],
            "dnsPrefix": "aksansibletest",
            "enableRBAC": false,
            "fqdn": "aksansibletest-cda2b56c.hcp.eastus.azmk8s.io",
            "kubernetesVersion": "1.12.6",
            "linuxProfile": {
                "adminUsername": "azureuser",
                "ssh": {
                    "publicKeys": [
                        {
                            "keyData": "ssh-rsa ..."
                        }
                    ]
                }
            },
            "networkProfile": {
                "dnsServiceIP": "10.0.0.10",
                "dockerBridgeCidr": "172.17.0.1/16",
                "networkPlugin": "kubenet",
                "podCidr": "192.168.0.0/16",
                "serviceCidr": "10.0.0.0/16"
            },
            "nodeResourceGroup": "MC_aksansibletest_pcaksansibletest_eastus",
            "provisioningState": "Succeeded",
            "servicePrincipalProfile": {
                "clientId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
            }
        },
        "type": "Microsoft.ContainerService/ManagedClusters"
    }
}

PLAY RECAP 
localhost                  : ok=15   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs. 

Spara följande kod som `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

I avsnittet `vars` ersätter du plats hållaren `{{ resource_group_name }}` med namnet på din resurs grupp.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie – konfigurera CNI-nätverk (Azure Container Network Interface) i AKS med hjälp av Ansible](./ansible-aks-configure-cni-networking.md)