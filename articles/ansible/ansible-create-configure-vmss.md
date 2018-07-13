---
title: Skapa VM-skalningsuppsättningar i Azure med Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera en VM-skalningsuppsättning i Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, playbook, VM, VM-skalningsuppsättning, vmss
author: tomarcher
manager: jpconnock
editor: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 07/11/2018
ms.author: tarcher
ms.openlocfilehash: 5f915f7b1b425a3bd6e5d62eb70bb3f633b7eda8
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009009"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Skapa VM-skalningsuppsättningar i Azure med Ansible
Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera din VM-skalningsuppsättning (VMSS) i Azure, på samma sätt som du skulle hantera andra Azure-resurs. Den här artikeln visar hur du använder Ansible för att skapa och skala ut en skalningsuppsättning för virtuell dator. 

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration** – om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- **Konfigurera Ansible** - [skapa Azure-autentiseringsuppgifter och konfigurera Ansible](../virtual-machines/linux/ansible-install-configure.md#create-azure-credentials)
- **Ansible och Azure Python SDK-moduler** 
  - [CentOS 7.4](../virtual-machines/linux/ansible-install-configure.md#centos-74)
  - [Ubuntu 16.04 LTS](../virtual-machines/linux/ansible-install-configure.md#ubuntu-1604-lts)
  - [SLES 12 SP2](../virtual-machines/linux/ansible-install-configure.md#sles-12-sp2)

> [!Note]
> Ansible 2.6 krävs för att köra följande exempel spelböcker i den här självstudien. 

## <a name="create-a-vmss"></a>Skapa en VMSS
Det här avsnittet presenteras en Ansible-spelbok som definierar följande resurser:
- **Resursgrupp** dit alla dina resurser kommer att distribueras
- **Virtuellt nätverk** i adressutrymmet 10.0.0.0/16
- **Undernät** i det virtuella nätverket
- **Offentlig IP-adress** den wllows du kan komma åt resurser via Internet
- **Nätverkssäkerhetsgrupp** som styr flödet av nätverkstrafik till och från virtual machine scale Sets
- **Belastningsutjämnare** som distribuerar trafik över en uppsättning definierade virtuella datorer med hjälp av regler för belastningsutjämnaren
- **Virtual machine scale Sets** som använder de skapade resurserna

Ange ditt eget lösenord för den *admin_password* värde.

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
      vmss_lb_name: myVMSSlb
      location: eastus
      admin_username: azureuser
      admin_password: "your_password"
    tasks:
      - name: Create a resource group
        azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
      - name: Create virtual network
        azure_rm_virtualnetwork:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefixes: "10.0.0.0/16"
      - name: Add subnet
        azure_rm_subnet:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          address_prefix: "10.0.1.0/24"
          virtual_network: "{{ vmss_name }}"
      - name: Create public IP address
        azure_rm_publicipaddress:
          resource_group: "{{ resource_group }}"
          allocation_method: Static
          name: "{{ vmss_name }}"
      - name: Create Network Security Group that allows SSH
        azure_rm_securitygroup:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          rules:
            - name: SSH
              protocol: Tcp
              destination_port_range: 22
              access: Allow
              priority: 1001
              direction: Inbound

      - name: Create a load balancer
        azure_rm_loadbalancer:
          name: "{{ vmss_lb_name }}"
          location: "{{ location }}"
          resource_group: "{{ resource_group }}"
          public_ip: "{{ vmss_name }}"
          probe_protocol: Tcp
          probe_port: 8080
          probe_interval: 10
          probe_fail_count: 3
          protocol: Tcp
          load_distribution: Default
          frontend_port: 80
          backend_port: 8080
          idle_timeout: 4
          natpool_frontend_port_start: 50000
          natpool_frontend_port_end: 50040
          natpool_backend_port: 22
          natpool_protocol: Tcp

      - name: Create VMSS
        azure_rm_virtualmachine_scaleset:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          vm_size: Standard_DS1_v2
          admin_username: "{{ admin_username }}"
          admin_password: "{{ admin_password }}"
          ssh_password_enabled: true
          capacity: 2
          virtual_network_name: "{{ vmss_name }}"
          subnet_name: "{{ vmss_name }}"
          upgrade_policy: Manual
          tier: Standard
          managed_disk_type: Standard_LRS
          os_disk_caching: ReadWrite
          image:
            offer: UbuntuServer
            publisher: Canonical
            sku: 16.04-LTS
            version: latest
          load_balancer: "{{ vmss_lb_name }}"
          data_disks:
            - lun: 0
              disk_size_gb: 20
              managed_disk_type: Standard_LRS
              caching: ReadOnly
            - lun: 1
              disk_size_gb: 30
              managed_disk_type: Standard_LRS
              caching: ReadOnly
  ```

Om du vill skapa VM scale set-miljö med Ansible, spara den föregående spelboken som `vmss-create.yml`, eller [ladda ned exemplet Ansible-spelbok](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Använd för att köra Ansible-spelbok i **ansible-spelbok** -kommandot enligt följande:

  ```bash
  ansible-playbook vmss-create.yml
  ```

När strategiboken, utdata som liknar följande exempel visar att VM-skalningsuppsättningen har set skapats:

  ```bash
  PLAY [localhost] ***********************************************************

  TASK [Gathering Facts] *****************************************************
  ok: [localhost]

  TASK [Create a resource group] ****************************************************************************
  changed: [localhost]

  TASK [Create virtual network] ****************************************************************************
  changed: [localhost]

  TASK [Add subnet] **********************************************************
  changed: [localhost]

  TASK [Create public IP address] ****************************************************************************
  changed: [localhost]

  TASK [Create Network Security Group that allows SSH] ****************************************************************************
  changed: [localhost]

  TASK [Create a load balancer] ****************************************************************************
  changed: [localhost]

  TASK [Create VMSS] *********************************************************
  changed: [localhost]

  PLAY RECAP *****************************************************************
  localhost                  : ok=8    changed=7    unreachable=0    failed=0

  ```

## <a name="scale-out-a-vmss"></a>Skala ut en VMSS
Skalningsuppsättningen för den skapade virtuella datorn har två instanser. Om du navigerar till skalningsuppsättning för virtuell dator i Azure portal visas **Standard_DS1_v2 (2 instanser)**. Du kan också använda den [Azure Cloud Shell](https://shell.azure.com/) genom att köra följande kommando i Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Resultatet bör likna följande:

  ```bash
  {
    "capacity": 2,
  }
  ```

Nu kan vi skala från två instanser till tre instanser. Följande kod för Ansible-spelbok hämtar information om VM-skalningsuppsättningen och ändrar sin kapacitet från två till tre. 

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      vmss_name: myVMSS
    tasks: 
      - name: Get scaleset info
        azure_rm_virtualmachine_scaleset_facts:
          resource_group: "{{ resource_group }}"
          name: "{{ vmss_name }}"
          format: curated
        register: output_scaleset

      - name: Dump scaleset info
        debug:
          var: output_scaleset

      - name: Modify scaleset (change the capacity to 3)
        set_fact:
          body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

      - name: Update something in that VMSS
        azure_rm_virtualmachine_scaleset: "{{ body }}"
  ```

Om du vill skala ut virtuella datorns skalningsuppsättning som du skapade, spara den föregående spelboken som `vmss-scale-out.yml` eller [ladda ned exemplet Ansible-spelbok](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)). 

Följande kommando körs spelboken:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Utdata från att köra Ansible-spelbok visar att virtuella datorns skalningsuppsättning har har skalats ut:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get scaleset info] ***************************************************************************
  ok: [localhost]

  TASK [Dump scaleset info] ***************************************************************************
  ok: [localhost] => {
      "output_scaleset": {
          "ansible_facts": {
              "azure_vmss": [
                  {
                      ......
                  }
              ]
          },
          "changed": false,
          "failed": false
      }
  }

  TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] ***************************************************************************
  ok: [localhost]

  TASK [Update something in that VMSS] ***************************************************************************
  changed: [localhost]

  PLAY RECAP ****************************************************************
  localhost                  : ok=5    changed=1    unreachable=0    failed=0
  ```

Om navigerar du till virtuella datorns skaluppsättning som du konfigurerade i Azure-portalen kan du se **Standard_DS1_v2 (3 instanser)**. Du kan också kontrollera ändringar som har den [Azure Cloud Shell](https://shell.azure.com/) genom att köra följande kommando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Resultat med följande kommando i Cloud Shell visar att det nu finns tre instanser. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Ansible-spelbok exemplet för VMSS](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)