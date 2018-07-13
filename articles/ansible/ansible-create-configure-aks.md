---
title: Skapa och konfigurera Azure Kubernetes Service-kluster i Azure med Ansible
description: Lär dig hur du använder Ansible för att skapa och hantera ett Azure Kubernetes Service-kluster i Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, behållare, Kubernetes
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 6d7c5f961256e0ae1831bd76353cadd761f4b8ac
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009205"
---
# <a name="create-and-configure-azure-kubernetes-service-clusters-in-azure-using-ansible"></a>Skapa och konfigurera Azure Kubernetes Service-kluster i Azure med Ansible
Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera Azure Kubernetes Service (AKS). Den här artikeln visar hur du använder Ansible för att skapa och konfigurera ett Azure Kubernetes Service-kluster.

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration** – om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- **Konfigurera Ansible** - [skapa Azure-autentiseringsuppgifter och konfigurera Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible och Azure Python SDK-moduler** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)
- **Azure-tjänstens huvudnamn** – när [skapar tjänstens huvudnamn](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal), Observera följande värden: **appId**, **displayName**, **lösenord** , och **klient**.

> [!Note]
> Ansible 2.6 krävs för att köra följande exempel spelböcker i den här självstudien. 

## <a name="create-a-managed-aks-cluster"></a>Skapa ett hanterat AKS-kluster
I följande exempel Ansible-spelbok skapas en resursgrupp och ett AKS-kluster som finns i resursgruppen:

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

Följande punkter hjälpa till att förklara föregående Ansible spelbok kod:
- Det första avsnittet i **uppgifter** definierar en resursgrupp med namnet **myResourceGroup** inom den **eastus** plats. 
- Det andra avsnittet inom **uppgifter** definierar ett AKS-kluster med namnet **myAKSCluster** inom den **myResourceGroup** resursgrupp. 

Skapa AKS-kluster med Ansible genom att spara föregående exempel spelboken som `azure_create_aks.yml`, och kör spelboken med följande kommando:

  ```bash
  ansible-playbook azure_create_aks.yml
  ```

Utdata från den **ansible-spelbok* kommandot liknar det följande som visar att AKS-klustret har skapats:

  ```bash
  PLAY [Create AKS] ****************************************************************************************

  TASK [Gathering Facts] ********************************************************************************************
  ok: [localhost]

  TASK [Create resource group] **************************************************************************************
  changed: [localhost]

  TASK [Create a Azure Container Services (AKS) cluster] ***************************************************
  changed: [localhost]

  PLAY RECAP *********************************************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0
  ```

## <a name="scale-aks-nodes"></a>Skala AKS-noder

Spelboken exemplet i föregående avsnitt definierar två noder. Om du behöver fler eller färre arbetsbelastningar i ditt kluster kan justera du enkelt antalet noder. Exempel-spelbok i det här avsnittet ökar antalet noder från två noder till tre. Ändra antalet noder görs genom att ändra den **antal** värde i den **agent_pool_profiles** block. 

Ange ett eget `ssh_key`, `client_id`, och `client_secret` i den **service_principal** block:

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

Spara föregående spelboken som skalar Azure Kubernetes Service-kluster med Ansible kan *azure_configure_aks.yml*, och kör spelboken på följande sätt:

  ```bash
  ansible-playbook azure_configure_aks.yml
  ```

Följande utdata visar att AKS-klustret har skapats:

  ```bash
  PLAY [Scale AKS cluster] ***************************************************************

  TASK [Gathering Facts] ******************************************************************
  ok: [localhost]

  TASK [Scaling an existed AKS cluster] **************************************************
  changed: [localhost]

  PLAY RECAP ******************************************************************************
  localhost                  : ok=2    changed=1    unreachable=0    failed=0
  ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Självstudie: Skala ut ett program i Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-scale)