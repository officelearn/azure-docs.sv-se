---
title: Snabbstart – konfigurera Linux-datorer i Azure med Ansible | Microsoft Docs
description: I den här snabbstarten lär du dig hur du skapar en Linux-dator i Azure med Ansible
keywords: ansible, azure, devops, virtual machine
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 4bf7d43b682a2a42d9909f9cd33aa9542a1a9330
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711638"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Snabbstart: Konfigurera Linux-datorer i Azure med Ansible

Med hjälp av ett deklarativ språk gör Ansible att du kan automatisera skapande, konfiguration och distribution av Azure-resurser via Ansible-*spelböcker*. Den här artikeln beskriver vi en Ansible-spelbok för exemplet för att konfigurera Linux-datorer. Den [fullständiga Ansible-spelboken](#complete-sample-ansible-playbook) anges i slutet av den här artikeln.

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Ansible behöver en resursgrupp där dina resurser distribueras. I följande avsnitt i ett Ansible-spelboksexempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

När du skapar en virtuell Azure-dator måste du skapa ett [virtuellt nätverk](/azure/virtual-network/virtual-networks-overview) eller använda ett befintligt virtuellt nätverk. Du måste även bestämma hur dina virtuella datorer är avsedda att användas på det virtuella nätverket. I följande avsnitt i ett Ansible-spelboksexempel skapas ett virtuellt nätverk med namnet `myVnet` i adressutrymmet `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Alla Azure-resurser som distribueras till ett virtuellt nätverk distribueras till ett [undernät](/azure/virtual-network/virtual-network-manage-subnet) i ett virtuellt nätverk. 

I följande avsnitt i ett Ansible-spelboksexempel skapas ett undernät med namnet `mySubnet` i det virtuella nätverket `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress





[Offentliga IP-adresser](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) tillåter att Internet-resurser kommunicerar inkommande till Azure-resurser. Offentliga IP-adresser kan också aktivera Azure-resurser kan kommunicera utgående till offentliga Azure-tjänster. I båda scenarierna måste tilldelas en IP-adress till den resurs som används. Adressen är dedikerad till resursen tills du ta bort den. Om en offentlig IP-adress inte är tilldelad till en resurs kan resursen ändå kommunicera utgående till Internet. Anslutningen görs av Azure tilldelar dynamiskt en tillgänglig IP-adress. Den dynamiskt tilldelade adressen inte är dedikerad till resursen.

I följande avsnitt i ett Ansible-spelboksexempel skapas en offentlig IP-adress med namnet `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

[Nätverkssäkerhetsgrupper](/azure/virtual-network/security-overview) filtrera nätverkstrafik mellan Azure-resurser i ett virtuellt nätverk. Säkerhetsregler definieras som styr inkommande och utgående trafik till och från Azure-resurser. Läs mer om Azure-resurser och nätverkssäkerhetsgrupper [integrering av virtuella nätverk för Azure-tjänster](/azure/virtual-network/virtual-network-for-azure-services)

Följande spelboken skapar en nätverkssäkerhetsgrupp med namnet `myNetworkSecurityGroup`. Nätverkssäkerhetsgruppen innehåller en regel som tillåter SSH-trafik på TCP-port 22.

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

## <a name="create-a-virtual-network-interface-card"></a>Skapa ett kort för virtuellt nätverksgränssnitt

Ett kort för virtuellt nätverksgränssnitt ansluter din virtuella dator till ett angivet virtuellt nätverk, en offentlig IP-adress och en nätverkssäkerhetsgrupp. 

Följande avsnitt i en Exempelavsnitt Ansible spelbok skapar ett virtuellt nätverkskort med namnet `myNIC` är anslutna till de virtuella nätverksresurser som du har skapat:

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

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Det sista steget är att skapa en virtuell dator som använder alla resurser som du skapade i föregående avsnitt i den här artikeln. 

I det avsnitt i ett Ansible-spelboksexempel som presenteras i det här avsnittet skapas en virtuell dator med namnet `myVM`, och kortet för virtuellt nätverksgränssnitt med namnet `myNIC` kopplas. Ersätt platshållaren &lt;your-key-data> med dina egna kompletta data för offentlig nyckel.

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
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Fullständigt Ansible-spelboksexempel

Det här avsnittet innehåller det Ansible-spelboksexempel som du har byggt under loppet av den här artikeln. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
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
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
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
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Köra Ansible-spelboksexemplet

Det här avsnittet beskriver hur du kör det Ansible-spelboksexempel som presenteras i den här artikeln.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil (som ska innehålla din spelbok) med namnet `azure_create_complete_vm.yml` och öppna den i VI-redigeringsprogrammet på följande sätt:

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. Starta infogningsläget genom att trycka på tangenten **I**.

1. Klistra in [det fullständiga Ansible-spelboksexemplet](#complete-sample-ansible-playbook) i redigeraren.

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Kör Ansible-spelboksexemplet.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. Utdata liknar följande, där du kan se att en virtuell dator har skapats:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network inteface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. SSH-kommandot används för att få åtkomst till din virtuella Linux-dator. Ersätt platshållaren &lt;ip-address> med IP-adressen från föregående steg.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Snabbstart: Hantera en Linux-dator i Azure med Ansible](./ansible-manage-linux-vm.md)