---
title: Självstudiekurs - Konfigurera skalningsuppsättningar för virtuella datorer i Azure med Ansible
description: Lär dig hur du använder Ansible för att skapa och konfigurera skaluppsättningar för virtuella datorer i Azure
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: e1cc40459988fb9bc38e3dbbcde563cebb531e3d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156547"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Självstudiekurs: Konfigurera skalningsuppsättningar för virtuella datorer i Azure med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurera resurserna för en virtuell dator
> * Konfigurera en skalningsuppsättning
> * Skala skalan som har angetts genom att öka dess VM-instanser 

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Konfigurera en skalningsuppsättning

Spelbokskoden i det här avsnittet definierar följande resurser:

* **Resursgrupp** som alla resurser ska distribueras till.
* **Virtuellt nätverk** i adressutrymmet 10.0.0.0/16
* **Undernät** i det virtuella nätverket
* **Offentlig IP-adress** som gör att du kan få åtkomst till resurser via Internet
* **Nätverkssäkerhetsgrupp** som styr flödet av nätverkstrafik in och ut ur skalningsuppsättningen
* **Lastbalanseraren** som distribuerar trafik över en uppsättning definierade virtuella datorer med hjälp av regler för lastbalanseraren
* **Skalningsuppsättning för virtuell dator** som använder alla resurser som har skapats

Det finns två sätt att hämta exempelspelboken:

* [Ladda ner spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) och `vmss-create.yml`spara den på .
* Skapa en ny `vmss-create.yml` fil med namnet och kopiera följande innehåll till den:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
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

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
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

Innan du kör spelboken läser du följande anmärkningar:

* I `vars` avsnittet ersätter `{{ admin_password }}` du platshållaren med ditt eget lösenord.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook vmss-create.yml
```

När du har kört spelboken visas utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Visa antalet VM-instanser

Den [konfigurerade skalningsuppsättningen](#configure-a-scale-set) har för närvarande två instanser. Följande steg används för att bekräfta det värdet:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navigera till den skalningsuppsättning som du har konfigurerat.

1. Du ser skalningsuppsättningens namn med antalet instanser inom parentes:`Standard_DS1_v2 (2 instances)`

1. Du kan också verifiera antalet instanser med [Azure Cloud Shell](https://shell.azure.com/) genom att köra följande kommando:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Resultatet av att köra Azure CLI-kommandot i Cloud Shell visar att det nu finns tre instanser: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Skala ut en skalningsuppsättning

Spelbokskoden i det här avsnittet hämtar information om skalningsuppsättningen och ändrar dess kapacitet från två till tre.

Det finns två sätt att hämta exempelspelboken:

* [Ladda ner spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) och `vmss-scale-out.yml`spara den på .
* Skapa en ny `vmss-scale-out.yml` fil med namnet och kopiera följande innehåll till den:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
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

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook vmss-scale-out.yml
```

När du har kört spelboken visas utdata som liknar följande resultat:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
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

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Verifiera resultaten

Verifiera dina resultat av ditt arbete via Azure-portalen:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navigera till den skalningsuppsättning som du har konfigurerat.

1. Du ser skalningsuppsättningens namn med antalet instanser inom parentes:`Standard_DS1_v2 (3 instances)` 

1. Du kan även verifiera ändringen med [Azure Cloud Shell](https://shell.azure.com/) genom att köra följande kommando:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Resultatet av att köra Azure CLI-kommandot i Cloud Shell visar att det nu finns tre instanser: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Självstudiekurs: Distribuera appar till skalningsuppsättningar för virtuella datorer i Azure med Ansible](./ansible-deploy-app-vmss.md)