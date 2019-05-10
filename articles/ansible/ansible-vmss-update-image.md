---
title: Självstudie – uppdatera den anpassade avbildningen på Azure VM scale sets med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att uppdatera VM-skalningsuppsättningar i Azure med anpassad avbildning
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3eedc5b83190af46669b9b5df8643f3c80e9bb1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230848"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Självstudier: Uppdatera den anpassade avbildningen på Azure VM scale sets med Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

När en virtuell dator har distribuerats kan du konfigurera den virtuella datorn med programvaran behoven för dina appar. I stället för att göra konfigurationsåtgärden för varje virtuell dator, kan du skapa en anpassad avbildning. En anpassad avbildning är en ögonblicksbild av en befintlig virtuell dator som innehåller alla installerade program. När du [konfigurera en skalningsuppsättning](./ansible-create-configure-vmss.md), du anger avbildningen som ska användas för virtuella datorer som skalningsuppsättningen. Genom att använda en anpassad avbildning kan konfigureras identiskt varje VM-instans för din app. Ibland kan du behöva uppdatera din skalningsuppsättning anpassad avbildning. Aktiviteten är fokus i den här självstudien.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurera två virtuella datorer med HTTPD
> * Skapa en anpassad avbildning från en befintlig virtuell dator
> * Skapa en skalningsuppsättning från en avbildning
> * Uppdatera den anpassade avbildningen

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Konfigurera två virtuella datorer

Spelboken koden i det här avsnittet skapar två virtuella datorer med installerade på både HTTPD. 

Den `index.html` sidan för varje virtuell dator visas en test-sträng:

* Första virtuella datorn visar värdet `Image A`
* Andra virtuella datorn visar värdet `Image B`

Den här strängen är avsedd att efterlikna konfigurera varje virtuell dator med annan programvara.

Det finns två sätt att hämta exemplet spelboken:

* [Ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) och spara den i `create_vms.yml`.
* Skapa en ny fil med namnet `create_vms.yml` och kopiera in följande innehåll:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando, ersätta `myrg` med namn på resursgruppen:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Eftersom den `debug` avsnitt på strategibok den `ansible-playbook` kommando skriver ut IP-adressen för varje virtuell dator. Kopiera dessa IP-adresser för senare användning.

![Virtuella IP-adresser](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Ansluta till de två virtuella datorerna

I det här avsnittet kan du ansluta till varje virtuell dator. Som vi nämnde i det föregående avsnittet strängarna `Image A` och `Image B` efterlikna har två olika virtuella datorer med olika konfigurationer.

Anslut till båda virtuella datorerna med IP-adresser från föregående avsnitt:

![Skärmbild från virtuell dator A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Skärmbild från virtuell dator B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Skapa avbildningar från varje virtuell dator

Nu har du två virtuella datorer med lite olika konfigurationer (sina `index.html` filer).

Spelboken koden i det här avsnittet skapar en anpassad avbildning för varje virtuell dator:

* `image_vmforimageA` – Anpassad avbildning som har skapats för den virtuella datorn som visar `Image A` på dess startsida.
* `image_vmforimageB` – Anpassad avbildning som har skapats för den virtuella datorn som visar `Image B` på dess startsida.

Det finns två sätt att hämta exemplet spelboken:

* [Ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) och spara den i `capture-images.yml`.
* Skapa en ny fil med namnet `capture-images.yml` och kopiera in följande innehåll:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando, ersätta `myrg` med namn på resursgruppen:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Skapa en skalningsuppsättning med avbildningen A

I det här avsnittet används en spelbok för att konfigurera följande Azure-resurser:

* Offentlig IP-adress
* Lastbalanserare
* Skalningsuppsättning som refererar till `image_vmforimageA`

Det finns två sätt att hämta exemplet spelboken:

* [Ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) och spara den i `create-vmss.yml`.
* Skapa en ny fil med namnet `create-vmss.yml` och kopiera in följande innehåll ”:

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando, ersätta `myrg` med namn på resursgruppen:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Eftersom den `debug` avsnitt på strategibok den `ansible-playbook` kommando skriver ut IP-adressen för skalningsuppsättningen. Kopiera den här IP-adressen för senare användning.

![Offentlig IP-adress](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Ansluta till skalningsuppsättningen

I det här avsnittet kan du ansluta till skalningsuppsättningen. 

Anslut till skalningsuppsättningen med IP-adressen i föregående avsnitt.

Som vi nämnde i det föregående avsnittet strängarna `Image A` och `Image B` efterlikna har två olika virtuella datorer med olika konfigurationer.

Skaluppsättningen refererar till den anpassade avbildningen med namnet `image_vmforimageA`. Anpassad avbildning `image_vmforimageA` skapades från den virtuella datorn vars startsidan visar `Image A`.

Därför kan du se en sidan som visar `Image A`:

![Skalningsuppsättningen är associerad med den första virtuella datorn.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Lämna webbläsarfönstret öppen när du fortsätter till nästa avsnitt.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Ändra en anpassad avbildning i skala ange och uppgradera instanser

Spelboken-kod i det här avsnittet ändrar den skalningsuppsättning bild - från `image_vmforimageA` till `image_vmforimageB`. Dessutom uppdateras alla aktuella virtuella datorer som distribueras av skalningsuppsättningen.

Det finns två sätt att hämta exemplet spelboken:

* [Ladda ned spelboken](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) och spara den i `update-vmss-image.yml`.
* Skapa en ny fil med namnet `update-vmss-image.yml` och kopiera in följande innehåll:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando, ersätta `myrg` med namn på resursgruppen:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Gå tillbaka till webbläsaren och uppdatera sidan. 

Du ser att den virtuella datorns underliggande anpassad avbildning uppdateras.

![Skalningsuppsättningen är associerad med den andra virtuella datorn](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort de resurser som skapades i den här artikeln. 

Spara följande kod som `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Kör en spelbok med hjälp av den `ansible-playbook` kommando:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible)