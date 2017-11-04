---
title: "Använd Ansible för att skapa en fullständig Linux VM i Azure | Microsoft Docs"
description: "Lär dig hur du använder Ansible för att skapa och hantera en fullständig miljö med Linux virtuella datorer i Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: f5dfecd1f5aafc1b7117433ef1bdb1805be2c57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Skapa en miljö med fullständiga Linux virtuella datorer i Azure med Ansible
Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella datorer (VM) i Azure, samma som någon annan resurs. Den här artikeln visar hur du skapar en komplett Linux-miljö och ge support för resurser med Ansible. Du kan också lära dig hur du [skapa en grundläggande virtuell dator med Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Krav
Om du vill hantera Azure-resurser med Ansible, behöver du följande:

- Ansible och Azure SDK för Python-moduler som har installerats på värdsystemet.
    - Installera Ansible på [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73), och [SLES 12,2 SP2](ansible-install-configure.md#sles-122-sp2)
- Konfigurera om du använder dessa autentiseringsuppgifter för Azure och Ansible.
    - [Skapa autentiseringsuppgifter för Azure och konfigurera Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. 
    - Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). Du kan också använda [moln Shell](/azure/cloud-shell/quickstart) från din webbläsare.


## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
Följande avsnitt i en Ansible playbook skapar ett virtuellt nätverk med namnet *myVnet* i den *10.0.0.0/16* adressutrymmet:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Om du vill lägga till ett undernät, nedan skapar ett undernät med namnet *mySubnet* i den *myVnet* virtuellt nätverk:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Skapa offentlig IP-adress
Skapa och tilldela en offentlig IP-adress till den virtuella datorn kan komma åt resurser via Internet. Följande avsnitt i en Ansible playbook skapar en offentlig IP-adress med namnet *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Skapa Nätverkssäkerhetsgrupp
Nätverkssäkerhetsgrupper styra flödet i nätverkstrafiken till och från den virtuella datorn. Följande avsnitt i en Ansible playbook skapar en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* och definierar en regel för att tillåta SSH-trafik på TCP-port 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Skapa virtuella nätverkskort
Ett virtuellt nätverkskort (NIC) ansluter den virtuella datorn till ett givet virtuellt nätverk, offentlig IP-adress och nätverkssäkerhetsgruppen. Följande avsnitt i en Ansible playbook skapar ett virtuellt nätverkskort med namnet *myNIC* anslutna till de virtuella nätverksresurser som du har skapat:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Det sista steget är att skapa en virtuell dator och använda de resurser som skapades. Följande avsnitt i en Ansible playbook skapar en virtuell dator med namnet *myVM* och bifogar det virtuella nätverkskortet med namnet *myNIC*. Ange dina egna offentliga nyckel data i den *key_data* koppla på följande sätt:

```yaml
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
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Slutföra Ansible playbook
Skapa en Ansible playbook med namnet för att samordna dessa avsnitt *azure_create_complete_vm.yml* och klistra in följande innehåll:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
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
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Ansible måste distribuera alla resurser i en resursgrupp. Skapa en resursgrupp med [az group create](/cli/azure/vm#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats:

```azurecli
az group create --name myResourceGroup --location eastus
```

För att skapa fullständiga Virtuella miljön med Ansible, kör du playbook på följande sätt:

```bash
ansible-playbook azure_create_complete_vm.yml
```

Utdata liknar följande exempel som visar den virtuella datorn har skapats:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Nästa steg
Det här exemplet skapas en fullständig VM-miljö inklusive de nödvändiga resurserna för virtuella nätverk. En mer direkt exempel skapa en virtuell dator i befintliga nätverksresurser med standardalternativen finns [skapa en virtuell dator](ansible-create-vm.md).