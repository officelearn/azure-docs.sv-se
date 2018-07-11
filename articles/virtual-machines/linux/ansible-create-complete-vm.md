---
title: Använda Ansible till att skapa en fullständig Linux-VM i Azure | Microsoft Docs
description: Lär dig hur du använder Ansible för att skapa och hantera en komplett Linux virtuell dator i Azure
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
ms.openlocfilehash: 63228f8bf8729f1bf3796a77516490ae7088d5ed
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930852"
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Skapa en fullständig miljö för Linux-dator i Azure med Ansible
Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella datorer (VM) i Azure, samma precis som alla andra resurser. Den här artikeln visar hur du skapar en fullständig Linux-miljö och stödresurser med Ansible. Du kan också lära dig hur du [skapa en grundläggande virtuell dator med Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Förutsättningar
För att hantera Azure-resurser med Ansible, behöver du följande:

- Ansible och Azure Python SDK-moduler som installerats på värdsystemet.
    - Installera Ansible på [CentOS 7.4](ansible-install-configure.md#centos-74), [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), och [SLES 12 SP2](ansible-install-configure.md#sles-12-sp2)
- Azure-autentiseringsuppgifter och Ansible som konfigurerats för att använda dem för.
    - [Skapa Azure-autentiseringsuppgifter och konfigurera Ansible](ansible-install-configure.md#create-azure-credentials)
- Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. 
    - Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). Du kan också använda [Cloud Shell](/azure/cloud-shell/quickstart) från din webbläsare.


## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
Nu ska vi titta på varje avsnitt i en Ansible-spelbok och skapa enskilda Azure-resurser. Fullständig spelbok kan se [det här avsnittet av artikeln](#complete-ansible-playbook).

Följande avsnitt i en Ansible-spelbok skapar ett virtuellt nätverk med namnet *myVnet* i den *10.0.0.0/16* adressutrymme:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Om du vill lägga till ett undernät, avsnittet nedan skapar ett undernät med namnet *mySubnet* i den *myVnet* virtuellt nätverk:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Skapa offentlig IP-adress
Skapa och tilldela en offentlig IP-adress till den virtuella datorn för att komma åt resurser via Internet. Följande avsnitt i en Ansible-spelbok skapar en offentlig IP-adress med namnet *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Skapa Nätverkssäkerhetsgrupp
Nätverkssäkerhetsgrupper styra flödet av nätverkstrafik och från den virtuella datorn. Följande avsnitt i en Ansible-spelbok skapar en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* och definierar en regel för att tillåta SSH-trafik på TCP-port 22:

```yaml
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
```


## <a name="create-virtual-network-interface-card"></a>Skapa virtuella nätverkskort
Ett virtuellt nätverkskort (NIC) ansluter den virtuella datorn till ett givet virtuellt nätverk, offentliga IP-adressen och nätverkssäkerhetsgruppen. Följande avsnitt i en Ansible-spelbok skapar ett virtuellt nätverkskort med namnet *myNIC* är anslutna till de virtuella nätverksresurser som du har skapat:

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
Det sista steget är att skapa en virtuell dator och använda de resurser som skapades. Följande avsnitt i en Ansible-spelbok skapar en virtuell dator med namnet *myVM* och ansluter det virtuella nätverkskortet med namnet *myNIC*. Ange din egen fullständiga offentliga viktiga data i den *key_data* para ihop på följande sätt:

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
      sku: '7.5'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Slutföra Ansible-spelbok
Skapa en Ansible-spelbok med namnet för att samordna alla dessa avsnitt *azure_create_complete_vm.yml* och klistra in följande innehåll. Ange din egen fullständiga offentliga viktiga data i den *key_data* par:

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
        sku: '7.5'
        version: latest
```

Ansible behöver en resursgrupp att distribuera dina resurser i. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Om du vill skapa en fullständig miljö för virtuella datorer med Ansible, kör du spelboken enligt följande:

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
Det här exemplet skapas en fullständig miljö för virtuella datorer, inklusive de nödvändiga virtuella nätverksresurser. En mer direkt exempel att skapa en virtuell dator i befintliga nätverksresurser med standardalternativen finns i [skapa en virtuell dator](ansible-create-vm.md).
