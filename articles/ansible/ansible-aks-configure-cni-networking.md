---
title: Självstudie – konfigurera Azure CNI nätverk i Azure Kubernetes Service (AKS) med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att konfigurera kubenet nätverk i klustret i Azure Kubernetes Service (AKS)
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: e4d5e68d13f00b79a309fab5e315747e934a0577
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767224"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Självstudier: Konfigurera Azure CNI nätverk i Azure Kubernetes Service (AKS) med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Med AKS kan distribuera du ett kluster med följande nätverk modeller:

- [Kubenet nätverk](/azure/aks/configure-kubenet) -nätverksresurser vanligtvis skapas och konfigureras enligt AKS-klustret distribueras.
- [Azure CNI nätverk](/azure/aks/configure-azure-cni) -AKS-kluster är ansluten till befintliga resurser för virtuella nätverk (VNET) och konfigurationer.

Läs mer om nätverk till dina program i AKS [Network begrepp för program i AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett AKS-kluster
> * Konfigurera Azure CNI-nätverk

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Exempelkod för spelbok i det här avsnittet används för att:

- Skapa ett virtuellt nätverk
- Skapa ett undernät i det virtuella nätverket

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

Exempelkod för spelbok i det här avsnittet används för att:

- Skapa ett AKS-kluster i ett virtuellt nätverk.

Spara följande spelbok som `aks.yml`:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
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
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Använd den `azure_rm_aks_version` modul för att hitta versionen som stöds.
- Den `vnet_subnet_id` är det undernät som skapats i föregående avsnitt.
- Spelboken har lästs in `ssh_key` från `~/.ssh/id_rsa.pub`. Om du ändrar den använda enradigt format - börjar med ”ssh-rsa” (utan citattecken).
- Den `client_id` och `client_secret` värden har lästs in från `~/.azure/credentials`, vilket är standardfilen för autentiseringsuppgifter. Du kan ange dessa värden till tjänstens huvudnamn eller läsa in dessa värden från miljövariabler:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Köra exemplet spelbok

Exempelkod för spelbok i det här avsnittet används för att testa olika funktioner som visas i den här självstudien.

Spara följande spelbok som `aks-azure-cni.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Ändra den `aksansibletest` värde som namn på resursgruppen.
- Ändra den `aksansibletest` värde till ditt AKS-namn.
- Ändra den `eastus` värde till din plats för resursgruppen.

Kör spelbok med hjälp av kommandot ansible-spelbok:

```bash
ansible-playbook aks-azure-cni.yml
```

När strategiboken, kan du se utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

Exempelkod för spelbok i det här avsnittet används för att:

- Ta bort en resursgrupp som anges i den `vars` avsnittet.

Spara följande spelbok som `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Här följer kommentarer att tänka på när du arbetar med exempel-strategibok:

- Ersätt den `{{ resource_group_name }}` platshållare med namnet på resursgruppen.
- Alla resurser inom den angivna resursgruppen tas bort.

Kör spelbok med hjälp av kommandot ansible-spelbok:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera Azure Active Directory i AKS med Ansible](./ansible-aks-configure-rbac.md)