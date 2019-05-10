---
title: Självstudie – Konfigurera nätverk i Azure Kubernetes Service (AKS) med Ansible kubenet | Microsoft Docs
description: Lär dig hur du använder Ansible för att konfigurera kubenet nätverk i klustret i Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: cd32347f9de87ea6272be922d0359f1cc7f6f758
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231313"
---
# <a name="tutorial-configure-kubenet-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Självstudier: Konfigurera kubenet nätverk i Azure Kubernetes Service (AKS) med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Med AKS kan distribuera du ett kluster med följande nätverk modeller:

- [Kubenet nätverk](/azure/aks/configure-kubenet) -nätverksresurser vanligtvis skapas och konfigureras enligt AKS-klustret distribueras.
- [Azure Container nätverk gränssnitt (CNI) nätverk](/azure/aks/configure-azure-cni) -AKS-kluster är ansluten till befintliga virtuella nätverksresurser och konfigurationer.

Läs mer om nätverk till dina program i AKS [Network begrepp för program i AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett AKS-kluster
> * Konfigurera Azure kubenet nätverk

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Spelboken koden i det här avsnittet skapar följande Azure-resurser:

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

Spelboken koden i det här avsnittet skapar ett AKS-kluster i ett virtuellt nätverk. 

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

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Använd `azure_rm_aks_version` modul för att hitta versionen som stöds.
- Den `vnet_subnet_id` är det undernät som skapats i föregående avsnitt.
- Den `network_profile` definierar egenskaperna för plugin-programmet för kubenet nätverk.
- Den `service_cidr` används för att tilldela en IP-adress interna tjänster i AKS-kluster. Den här IP-adressintervallet ska vara ett adressutrymme som inte används någon annanstans i ditt nätverk. 
- Den `dns_service_ip` adress ska vara den ”. 10”-adressen för ditt service IP-adressintervall.
- Den `pod_cidr` ska vara ett stort adressutrymme som inte används i din nätverksmiljö. Adressintervallet måste vara tillräckligt stor för att anpassa antalet noder som du förväntar dig att skala upp till. Du kan inte ändra den här adressintervall när klustret distribueras.
- Pod IP-adressintervall som används för att tilldela ett/24-adressutrymme till varje nod i klustret. I följande exempel visas den `pod_cidr` tilldelar den första noden 192.168.0.0/24, den andra noden 192.168.1.0/24 och den tredje noden 192.168.2.0/24 av 192.168.0.0/16.
- Azure fortsätter att tilldela en pod IP-adressintervall till varje ny nod som klustret skalas eller uppgraderingar.
- Spelboken har lästs in `ssh_key` från `~/.ssh/id_rsa.pub`. Om du ändrar den använda enradigt format - börjar med ”ssh-rsa” (utan citattecken).
- Den `client_id` och `client_secret` värden har lästs in från `~/.azure/credentials`, vilket är standardfilen för autentiseringsuppgifter. Du kan ange dessa värden till tjänstens huvudnamn eller läsa in dessa värden från miljövariabler:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="associate-the-network-resources"></a>Associera till nätverksresurser

När du skapar ett AKS-kluster, skapas en network security group och dirigera tabell. De här resurserna hanteras av AKS och uppdateras när du skapar och exponera tjänster. Associera tabellen network security group och dirigera med undernät för virtuellt nätverk på följande sätt. 

Spara följande spelboken som `associate.yml`.

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

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Den `node_resource_group` är resursgruppens namn som AKS-nodernas skapas.
- Den `vnet_subnet_id` är det undernät som skapades i föregående avsnitt.


## <a name="run-the-sample-playbook"></a>Köra exemplet spelbok

Det här avsnittet innehåller komplett exempel spelboken som anropar aktiviteterna i den här artikeln. 

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

I den `vars` avsnittet, gör följande ändringar:

- För den `resource_group` nyckel, ändra den `aksansibletest` värde som namn på resursgruppen.
- För den `name` nyckel, ändra den `aksansibletest` värde till ditt AKS-namn.
- För den `Location` nyckel, ändra den `eastus` värde till din plats för resursgruppen.

Kör en fullständig spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook aks-kubenet.yml
```

Strategiboken visar resultat som liknar följande utdata:

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

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

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

I den `vars` avsnittet, ersätter den `{{ resource_group_name }}` platshållare med namnet på resursgruppen.

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie – konfigurerar du Azure behållare nätverk gränssnitt (CNI) nätverk i AKS med Ansible](./ansible-aks-configure-cni-networking.md)