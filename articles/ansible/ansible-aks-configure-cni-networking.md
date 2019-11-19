---
title: Självstudie – konfigurera Azure CNI Networking i Azure Kubernetes service (AKS) med hjälp av Ansible
description: Lär dig hur du använder Ansible för att konfigurera Kubernetes-nätverk i Azure Kubernetes service (AKS)-kluster
keywords: Ansible, Azure, DevOps, bash, cloudshell, Spelbok, AKS, container, AKS, Kubernetes
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e3667ad7a561f56d5fddaacad705c53d1de9ac36
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156909"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Självstudie: Konfigurera Azure CNI Networking i Azure Kubernetes service (AKS) med hjälp av Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Med AKS kan du distribuera ett kluster med hjälp av följande nätverks modeller:

- [Kubernetes nätverk](/azure/aks/configure-kubenet) – nätverks resurser skapas och konfigureras vanligt vis när AKS-klustret distribueras.
- [Azure cni Network](/azure/aks/configure-azure-cni) – AKS-klustret är anslutet till befintliga virtuella nätverks resurser och konfigurationer.

Mer information om nätverk till dina program i AKS finns i [nätverks begrepp för program i AKS](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett AKS-kluster
> * Konfigurera Azure CNI-nätverk

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

Exempel koden Spelbok i det här avsnittet används för att:

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

Exempel koden Spelbok i det här avsnittet används för att:

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

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- Använd `azure_rm_aks_version`-modulen för att hitta den version som stöds.
- `vnet_subnet_id` är det undernät som skapades i föregående avsnitt.
- Spelbok läser in `ssh_key` från `~/.ssh/id_rsa.pub`. Om du ändrar det använder du det enkla formatet – från och med "SSH-RSA" (utan citationstecken).
- Värdena för `client_id` och `client_secret` läses in från `~/.azure/credentials`, vilket är standard filen för autentiseringsuppgifter. Du kan ange dessa värden till tjänstens huvud namn eller läsa in dessa värden från miljövariablerna:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>Kör exemplet Spelbok

Exempel koden för Spelbok i det här avsnittet används för att testa olika funktioner som visas i den här självstudien.

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

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- Ändra `aksansibletest` värde till resurs gruppens namn.
- Ändra `aksansibletest` värde till ditt AKS-namn.
- Ändra `eastus` värde till din resurs grupps plats.

Kör Spelbok med kommandot Ansible-Spelbok:

```bash
ansible-playbook aks-azure-cni.yml
```

När du har kört Spelbok visas utdata som liknar följande resultat:

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

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs. 

Exempel koden Spelbok i det här avsnittet används för att:

- Ta bort en resurs grupp som refereras till i avsnittet `vars`.

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

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- Ersätt `{{ resource_group_name }}` plats hållaren med namnet på din resurs grupp.
- Alla resurser i den angivna resurs gruppen tas bort.

Kör Spelbok med kommandot Ansible-Spelbok:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera Azure Active Directory i AKS med Ansible](./ansible-aks-configure-rbac.md)