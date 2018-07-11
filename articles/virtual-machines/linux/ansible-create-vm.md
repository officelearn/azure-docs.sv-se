---
title: Använda Ansible till att skapa en grundläggande Linux-VM i Azure | Microsoft Docs
description: Lär dig hur du använder Ansible för att skapa och hantera en grundläggande Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 35dfe8348718e0edf8683f7eeddf286831697d89
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931437"
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Skapa en grundläggande virtuell dator i Azure med Ansible
Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella datorer (VM) i Azure, samma precis som alla andra resurser. Den här artikeln visar hur du skapar en grundläggande virtuell dator med Ansible. Du kan också lära dig hur du [skapa en fullständig miljö för virtuella datorer med Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Förutsättningar
För att hantera Azure-resurser med Ansible, behöver du följande:

- Ansible och Azure Python SDK-moduler som installerats på värdsystemet.
    - Installera Ansible på [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), och [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Azure-autentiseringsuppgifter och Ansible som konfigurerats för att använda dem för.
    - [Skapa Azure-autentiseringsuppgifter och konfigurera Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. 
    - Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). Du kan också använda den [Azure Cloud Shell](/azure/cloud-shell/quickstart) från webbläsaren.


## <a name="create-supporting-azure-resources"></a>Skapa stöd för Azure-resurser
I det här exemplet skapar du en runbook som distribuerar en virtuell dator i en befintlig infrastruktur. Börja med att skapa resursgruppen med [az gruppen skapa](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa ett virtuellt nätverk för den virtuella datorn med [az network vnet skapa](/cli/azure/network/vnet#az-network-vnet-create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* och ett undernät med namnet *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Skapa och köra Ansible-spelbok
Skapa en Ansible-spelbok med namnet *azure_create_vm.yml* och klistra in följande innehåll. Det här exemplet skapar en enskild virtuell dator och konfigurerar SSH-autentiseringsuppgifter. Ange din egen fullständiga offentliga viktiga data i den *key_data* para ihop på följande sätt:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

Om du vill skapa den virtuella datorn med Ansible, kör du spelboken enligt följande:

```bash
ansible-playbook azure_create_vm.yml
```

Utdata liknar följande exempel som visar den virtuella datorn har skapats:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>Nästa steg
Det här exemplet skapar en virtuell dator i en befintlig resursgrupp och med ett virtuellt nätverk som redan har distribuerats. Ett mer detaljerat exempel på hur du använder Ansible för att skapa resurser, till exempel ett virtuellt nätverk och regler för Nätverkssäkerhetsgruppen finns [skapa en fullständig miljö för virtuella datorer med Ansible](ansible-create-complete-vm.md).
