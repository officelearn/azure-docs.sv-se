---
title: Skapa VM-skalningsuppsättningar i Azure med Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera en skalningsuppsättning för virtuell dator i Azure
ms.service: azure
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/24/2018
ms.openlocfilehash: 1176987ab318a97a7db6a12e619e7b7db06ad2da
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58097897"
---
# <a name="create-virtual-machine-scale-sets-in-azure-using-ansible"></a>Skapa VM-skalningsuppsättningar i Azure med Ansible
Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina VM-skalningsuppsättningar (VMSS) i Azure på samma sätt som för alla andra Azure-resurser. I den här artikeln får du läsa om hur du använder Ansible för att skapa och skala ut en skalningsuppsättning för virtuell dator. 

## <a name="prerequisites"></a>Förutsättningar
- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> Ansible 2.6 krävs för att köra följande exempelspelböcker i den här självstudien. 

## <a name="create-a-vmss"></a>Skapa en VMSS
I den här delen finns en Ansible-exempelspelbok som definierar följande resurser:
- **Resursgrupp** dit alla dina resurser distribueras
- **Virtuellt nätverk** i adressutrymmet 10.0.0.0/16
- **Undernät** i det virtuella nätverket
- **Offentlig IP-adress** som gör att du kan få åtkomst till resurser via Internet
- **Nätverkssäkerhetsgrupp** som styr flödet av nätverkstrafik in till och ut från VM-skalningsuppsättningen
- **Lastbalanseraren** som distribuerar trafik över en uppsättning definierade virtuella datorer med hjälp av regler för lastbalanseraren
- **Skalningsuppsättning för virtuell dator** som använder alla resurser som har skapats

Ange ett eget lösenord för värdet *admin_password*.

  ```yml
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

Om du vill skapa en miljö för VM-skalningsuppsättningar med Ansible sparar du den föregående spelboken som `vmss-create.yml` eller [laddar ned Ansible-exempelspelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml).

Med kommandot **ansible-playbook** kör du Ansible-spelboken enligt följande:

  ```bash
  ansible-playbook vmss-create.yml
  ```

När du har kört spelboken visar utdata som liknar följande exempel att VM-skalningsuppsättningen har skapats:

  ```Output
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
VM-skalningsuppsättningen som har skapats har två instanser. Om du går till VM-skalningsuppsättningen i Azure-portalen ser du **Standard_DS1_v2 (2 instanser)**. Du kan även använda [Azure Cloud Shell](https://shell.azure.com/) genom att köra följande kommando i Cloud Shell:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Du ser resultat som liknar följande utdata:

  ```bash
  {
    "capacity": 2,
  }
  ```

Nu ska vi skala från två till tre instanser. Följande Ansible-spelbokskod hämtar information om VM-skalningsuppsättningen och ändrar sin kapacitet från två till tre. 

  ```yml
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

Om du vill skala ut en VM-skalningsuppsättning som du har skapat sparar du den föregående spelboken som `vmss-scale-out.yml` eller [laddar ned Ansible-exempelspelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml)). 

Spelboken körs med följande kommando:

  ```bash
  ansible-playbook vmss-scale-out.yml
  ```

Utdata efter körningen av Ansible-spelboken visar att VM-skalningsuppsättningen har skalats ut:

  ```Output
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

Om du går till VM-skalningsuppsättningen du konfigurerade i Azure-portalen ser du **Standard_DS1_v2 (3 instanser)**. Du kan även verifiera ändringen med [Azure Cloud Shell](https://shell.azure.com/) genom att köra följande kommando:

  ```azurecli-interactive
  az vmss show -n myVMSS -g myResourceGroup --query '{"capacity":sku.capacity}' 
  ```

Resultatet av körningen av kommandot i Cloud Shell visar att det nu finns tre instanser. 

  ```bash
  {
    "capacity": 3,
  }
  ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Distribuera program till VM-skalningsuppsättningar med hjälp av Ansible](https://docs.microsoft.com/azure/ansible/ansible-deploy-app-vmss)
> 
> [Skala en VM-skalningsuppsättning automatiskt med hjälp av Ansible](https://docs.microsoft.com/azure/ansible/ansible-auto-scale-vmss)