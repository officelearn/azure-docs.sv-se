---
title: Skapa och konfigurera Azure Kubernetes Service-kluster i Azure med Ansible
description: Lär dig hur du använder Ansible för att skapa och hantera ett Azure Kubernetes Service-kluster i Azure
ms.service: azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, Kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/23/2018
ms.openlocfilehash: 2270a9225d26329f3d78d78895223aaa6ccc855f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176403"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Skapa och konfigurera Azure Kubernetes Service-kluster i Azure med Ansible
Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera Azure Kubernetes Service (AKS). Den här artikeln beskriver hur du använder Ansible för att skapa och konfigurera ett Azure Kubernetes Service-kluster.

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- **Azure-tjänstens huvudnamn** – Observera följande värden när du [skapar tjänstens huvudnamn](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest): **appId**, **displayName**, **password** och **tenant**.

- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.6 krävs för att köra följande exempelspelböcker i den här självstudien.

## <a name="create-a-managed-aks-cluster"></a>Skapa ett hanterat AKS-kluster
Koden i det här avsnittet presenterar en Ansible-exempelspelbok som skapas en resursgrupp och ett AKS-kluster som finns i resursgruppen.

> [!Tip]
> För platshållaren `your_ssh_key` anger du din offentliga RSA-nyckel i enradigt format – som ska börja med ”ssh-rsa” (utan citattecken).

  ```yaml
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

Följande punkter förklarar Ansible-spelbokskoden ovan:
- Det första avsnittet i **tasks** definierar en resursgrupp med namnet **myResourceGroup** på platsen **eastus**.
- Det andra avsnittet i **tasks** definierar ett AKS-kluster med namnet **myAKSCluster** i resursgruppen **myResourceGroup**.

För att skapa AKS-klustret med Ansible sparar du föregående exempelspelbok som `azure_create_aks.yml` och kör spelboken med följande kommando:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Utdataresultatet från kommandot **ansible-playbook* liknar följande utada som anger att AKS-klustret har skapats:

  ```Output
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create an Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Skala AKS-noder

Exempelspelboken i föregående avsnitt definierar två noder. Om du behöver fler eller färre containerarbetsbelastningar i ditt kluster kan du enkelt justera antalet noder. Exempelspelboken i det här avsnittet ökar antalet noder från två noder till tre. Du ändrar antalet noder genom att ändra **count**-värdet i blocket **agent_pool_profiles**.

> [!Tip]
> För platshållaren `your_ssh_key` anger du din offentliga RSA-nyckel i enradigt format – som ska börja med ”ssh-rsa” (utan citattecken).

```yaml
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

Om du vill skala Azure Kubernetes Service-klustret med Ansible sparar du den föregående spelboken som *azure_configure_aks.yml* och kör spelboken så här:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Följande utdata visar att AKS-klustret har skapats:

  ```Output
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```
## <a name="delete-a-managed-aks-cluster"></a>Ta bort ett hanterat AKS-kluster

Följande avsnitt för Ansible-exempelspelboken beskriver hur du tar bort ett AKS-kluster:

  ```yaml
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

Om du vill ta bort Azure Kubernetes Service-klustret med Ansible sparar du den föregående spelboken som *azure_delete_aks.yml* och kör spelboken på så här:

  ```bash
  ansible-playbook azure_delete_aks.yml
  ```

Följande utdata anger att AKS-klustret har tagits bort:
  ```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Självstudie: Skala ett program i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/tutorial-kubernetes-scale)
