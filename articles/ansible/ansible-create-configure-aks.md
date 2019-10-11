---
title: Självstudie – konfigurera Azure Kubernetes service-kluster (AKS) i Azure med hjälp av Ansible
description: Lär dig hur du använder Ansible för att skapa och hantera ett Azure Kubernetes Service-kluster i Azure
keywords: Ansible, Azure, DevOps, bash, cloudshell, Spelbok, AKS, container, AKS, Kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 9b70a9c364768322a3eae6ef5b92c87b6839c540
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242094"
---
# <a name="tutorial-configure-azure-kubernetes-service-aks-clusters-in-azure-using-ansible"></a>Självstudie: Konfigurera Azure Kubernetes service-kluster (AKS) i Azure med hjälp av Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

AKS kan konfigureras för att använda [Azure Active Directory (AD)](/azure/active-directory/) för användarautentisering. När du har konfigurerat använder du Azure AD-autentiseringstoken för att logga in på AKS-klustret. RBAC kan baseras på användarens identitet eller katalog grupp medlemskap.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa ett AKS-kluster
> * Konfigurera ett AKS-kluster

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-managed-aks-cluster"></a>Skapa ett hanterat AKS-kluster

Exemplet Spelbok skapar en resurs grupp och ett AKS-kluster i resurs gruppen.

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

Se följande anmärkningar innan du kör Spelbok:

- Det första avsnittet i `tasks` definierar en resurs grupp med namnet `myResourceGroup` på platsen `eastus`.
- Det andra avsnittet i `tasks` definierar ett AKS-kluster med namnet `myAKSCluster` i resurs gruppen `myResourceGroup`.
- För platshållaren `your_ssh_key` anger du din offentliga RSA-nyckel i enradigt format – som ska börja med ”ssh-rsa” (utan citattecken).

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook azure_create_aks.yml
```

Om du kör Spelbok visas resultat som liknar följande utdata:

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

Exempelspelboken i föregående avsnitt definierar två noder. Du justerar antalet noder genom att ändra värdet `count` i `agent_pool_profiles`-blocket.

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

Se följande anmärkningar innan du kör Spelbok:

- För platshållaren `your_ssh_key` anger du din offentliga RSA-nyckel i enradigt format – som ska börja med ”ssh-rsa” (utan citattecken).

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook azure_configure_aks.yml
```

Om du kör Spelbok visas resultat som liknar följande utdata:

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

Exemplet Spelbok tar bort ett AKS-kluster.

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

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook azure_delete_aks.yml
```

Om du kör Spelbok visas resultat som liknar följande utdata:

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