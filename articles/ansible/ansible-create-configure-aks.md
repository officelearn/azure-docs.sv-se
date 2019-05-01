---
title: Självstudie – konfigurera Azure Kubernetes Service (AKS)-kluster i Azure med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att skapa och hantera ett Azure Kubernetes Service-kluster i Azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 64b8eb12c755f41cde28067b5c145c4e95066886
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688448"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Självstudier: Konfigurera Azure Kubernetes Service (AKS)-kluster i Azure med Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan konfigureras för att använda [Azure Active Directory (AD)](/azure/active-directory/) för autentisering av användare. När du konfigurerat använder din Azure AD-Autentiseringstoken för att logga in på AKS-klustret. RBAC kan baseras på en användares identitet eller gruppmedlemskap för katalogen.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett AKS-kluster
> * Konfigurera ett AKS-kluster

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Skapa ett hanterat AKS-kluster

Spelboken exemplet skapar en resursgrupp och ett AKS-kluster i resursgruppen.

Spara följande spelbok som `azure_create_aks.yml`:

```yml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Se följande information innan du kör spelboken:

- Det första avsnittet i `tasks` definierar en resursgrupp med namnet `myResourceGroup` inom den `eastus` plats.
- Det andra avsnittet inom `tasks` definierar ett AKS-kluster med namnet `myAKSCluster` inom den `myResourceGroup` resursgrupp.
- För platshållaren `your_ssh_key` anger du din offentliga RSA-nyckel i enradigt format – som ska börja med ”ssh-rsa” (utan citattecken).

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook azure_create_aks.yml
```

Strategiboken visar resultat som liknar följande utdata:

```Output
PLAY [Create AKS] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create resource group] 
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="scale-aks-nodes"></a>Skala AKS-noder

Exempelspelboken i föregående avsnitt definierar två noder. Du justerar antalet noder genom att ändra den `count` värde i den `agent_pool_profiles` block.

Spara följande spelbok som `azure_configure_aks.yml`:

```yml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Se följande information innan du kör spelboken:

- För platshållaren `your_ssh_key` anger du din offentliga RSA-nyckel i enradigt format – som ska börja med ”ssh-rsa” (utan citattecken).

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook azure_configure_aks.yml
```

Strategiboken visar resultat som liknar följande utdata:

```Output
PLAY [Scale AKS cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Scaling an existed AKS cluster] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="delete-a-managed-aks-cluster"></a>Ta bort ett hanterat AKS-kluster

Spelboken exemplet tar bort ett AKS-kluster.

Spara följande spelbok som `azure_delete_aks.yml`:


```yml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook azure_delete_aks.yml
```

Strategiboken visar resultat som liknar följande utdata:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [azure_rm_aks] 

PLAY RECAP 
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Skala ett program i Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)