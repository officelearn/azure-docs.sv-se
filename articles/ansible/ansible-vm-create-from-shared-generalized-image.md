---
title: Självstudie – Skapa en skalnings uppsättning för virtuella datorer eller virtuella datorer från Azure-galleriet för delad avbildning med Ansible
description: Lär dig hur du använder Ansible för att skapa en virtuell dator eller skal uppsättning för virtuella datorer baserat på en generaliserad avbildning i det delade avbildnings galleriet.
keywords: Ansible, Azure, DevOps, bash, Spelbok, virtuell dator, skal uppsättning för virtuell dator, delad avbildnings Galleri
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 10/14/2019
ms.openlocfilehash: 814be49c972e444f2a4e4a703501e88fa1272b89
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392147"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Självstudie: skapa en virtuell dator eller en skalnings uppsättning för virtuella datorer från Azures Galleri för delad avbildning med Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Delade avbildnings Galleri](/azure/virtual-machines/windows/shared-image-galleries) är en tjänst som gör att du enkelt kan hantera, dela och organisera anpassade, hanterade avbildningar. Den här funktionen är fördelaktig för scenarier där många bilder upprätthålls och delas. Anpassade avbildningar kan delas mellan prenumerationer och mellan Azure Active Directory klienter. Avbildningar kan också replikeras till flera regioner för snabbare distributions skalning.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en generaliserad virtuell dator och anpassad avbildning
> * Skapa ett galleri för delad avbildning
> * Skapa en delad avbildning och avbildnings version
> * Skapa en virtuell dator med hjälp av den generaliserade avbildningen
> * Skapa en skalnings uppsättning för virtuell dator med hjälp av den generaliserade avbildningen
> * Hämta information om det delade avbildnings galleriet, avbildningen och versionen.

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Hämta exempel spel böcker

Det finns två sätt att hämta en fullständig uppsättning exempel spel böcker:

- [Ladda ned sig-mappen](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) och spara den på din lokala dator.
- Skapa en ny fil för varje avsnitt och kopiera exempel Spelbok i det.

@No__t-0-filen innehåller de variabler som används av alla exempel-spel böcker i den här självstudien. Du kan redigera filen för att tillhandahålla unika namn och värden.

Det första exemplet Spelbok `00-prerequisites.yml` skapar vad som krävs för att slutföra den här självstudien:
- En resurs grupp, som är en logisk behållare där Azure-resurser distribueras och hanteras.
- Ett virtuellt nätverk; delnät offentlig IP-adress och nätverkskort för den virtuella datorn.
- En virtuell käll dator som används för att skapa den generaliserade avbildningen.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 00-prerequisites.yml
```

I [Azure Portal](https://portol.azure.com)markerar du den resurs grupp som du angav i `vars.yml` för att se den nya virtuella datorn och de olika resurser som du har skapat.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalisera den virtuella datorn och skapa en anpassad avbildning

Nästa Spelbok, `01a-create-generalized-image.yml`, generaliserar den virtuella käll datorn som skapades i föregående steg och skapar sedan en anpassad avbildning baserat på den.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Kontrol lera din resurs grupp och se till att `testimagea` visas.

## <a name="create-the-shared-image-gallery"></a>Skapa galleriet för delad avbildning

Avbildnings galleriet är lagrings platsen för att dela och hantera avbildningar. Exempel koden Spelbok i `02-create-shared-image-gallery.yml` skapar ett delat avbildnings galleri i din resurs grupp.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Nu ser du ett nytt Galleri, `myGallery`, i din resurs grupp.

## <a name="create-a-shared-image-and-image-version"></a>Skapa en delad avbildning och avbildnings version

Nästa Spelbok, `03a-create-shared-image-generalized.yml`, skapar en avbildnings definition och en avbildnings version.

Bild definitionerna omfattar avbildnings typen (Windows eller Linux), viktig information och lägsta och högsta minnes krav. Avbildnings versionen är avbildningens version. Galleri, bild definition och avbildnings version hjälper dig att ordna bilder i logiska grupper. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Din resurs grupp har nu en bild definition och en avbildnings version för ditt Galleri.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Skapa en virtuell dator baserat på den generaliserade avbildningen

Kör slutligen `04a-create-vm-using-generalized-image.yml` om du vill skapa en virtuell dator baserat på den generaliserade avbildning som du skapade i föregående steg.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Skapa en skalnings uppsättning för virtuella datorer baserat på den generaliserade avbildningen

Du kan också skapa en skalnings uppsättning för virtuella datorer baserat på den generaliserade avbildningen. Kör `05a-create-vmss-using-generalized-image.yml` för att göra det.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Hämta information om galleriet

Du kan få information om galleriet, bild definitionen och versionen genom att köra `06-get-info.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Ta bort den delade avbildningen

Om du vill ta bort Galleri resurserna läser du exempel Spelbok `07-delete-gallery.yml`. Ta bort resurser i omvänd ordning. Börja med att ta bort avbildnings versionen. När du har tagit bort alla avbildnings versioner kan du ta bort avbildnings definitionen. När du har tagit bort alla avbildnings definitioner kan du ta bort galleriet.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs. 

Exempel koden Spelbok i det här avsnittet används för att:

- Ta bort de två resurs grupper som skapades tidigare

Spara följande spelbok som `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Här följer några viktiga kommentarer att tänka på när du arbetar med exemplet Spelbok:

- Ersätt plats hållaren `{{ resource_group_name }}` med namnet på din resurs grupp.
- Alla resurser inom de två angivna resurs grupperna kommer att tas bort.

Kör Spelbok med kommandot `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)