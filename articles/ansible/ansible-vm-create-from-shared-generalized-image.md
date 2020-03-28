---
title: Självstudiekurs - Skapa en skala för virtuella datorer eller virtuella datorer från Azure Shared Image Gallery med Ansible
description: Lär dig hur du använder Ansible för att skapa vm- eller virtuell datorskalauppsättning baserat på en generaliserad avbildning i det delade bildgalleriet.
keywords: ansible, azure, devops, bash, playbook, virtuell maskin, virtuell dator skala uppsättning, delad bild galleri
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155837"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Självstudiekurs: Skapa en skalningsuppsättning för virtuella datorer eller virtuella datorer från Azure Shared Image Gallery med Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Delat bildgalleri](/azure/virtual-machines/windows/shared-image-galleries) är en tjänst som gör att du enkelt kan hantera, dela och ordna anpassade hanterade bilder. Den här funktionen är användbar för scenarier där många bilder underhålls och delas. Anpassade avbildningar kan delas mellan prenumerationer och mellan Azure Active Directory-klienter. Avbildningar kan också replikeras till flera regioner för snabbare distributionskalning.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Skapa en generaliserad virtuell dator och anpassad avbildning
> * Skapa ett delat bildgalleri
> * Skapa en delad bild- och bildversion
> * Skapa en virtuell dator med den generaliserade avbildningen
> * Skapa en skalauppsättning för den virtuella datorn med den generaliserade avbildningen
> * Få information om ditt delade bildgalleri, bild och version.

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Hämta exempelspelböckerna

Det finns två sätt att få den kompletta uppsättningen exempel spelböcker:

- [Ladda ner SIG-mappen](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) och spara den på din lokala dator.
- Skapa en ny fil för varje avsnitt och kopiera exempelspelboken i den.

Filen `vars.yml` innehåller de variabler som används av alla exempel spelböcker för den här självstudien. Du kan redigera filen för att ange unika namn och värden.

Det första exempeluppspelningsboken `00-prerequisites.yml` skapar vad som krävs för att slutföra den här självstudien:
- En resursgrupp, som är en logisk behållare där Azure-resurser distribueras och hanteras.
- Ett virtuellt nätverk; undernät. ip-adress och nätverkskort för den virtuella datorn.
- En virtuell dator som används för att skapa den generaliserade avbildningen.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 00-prerequisites.yml
```

I [Azure-portalen](https://portal.azure.com)kontrollerar du resursgruppen som du angav i `vars.yml` för att se den nya virtuella datorn och olika resurser som du har skapat.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalisera den virtuella datorn och skapa en anpassad avbildning

Nästa spelbok, `01a-create-generalized-image.yml`generaliserar käll-VM som skapats i föregående steg och sedan skapa en anpassad avbildning baserat på den.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Kontrollera din resursgrupp `testimagea` och se till att den visas.

## <a name="create-the-shared-image-gallery"></a>Skapa galleriet Delad bild

Bildgalleriet är en databas för att dela och hantera bilder. Exempelspelskoden i `02-create-shared-image-gallery.yml` skapar ett delat bildgalleri i resursgruppen.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Nu visas ett nytt `myGallery`galleri i resursgruppen.

## <a name="create-a-shared-image-and-image-version"></a>Skapa en delad bild- och bildversion

Nästa spelbok, `03a-create-shared-image-generalized.yml` skapar en bilddefinition och en bildversion.

Avbildningsdefinitioner omfattar avbildningstypen (Windows eller Linux), viktig information och lägsta och högsta minneskrav. Bildversionen är den version av bilden. Galleri, bilddefinition och bildversion hjälper dig att ordna bilder i logiska grupper. 

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Resursgruppen har nu en bilddefinition och en bildversion för galleriet.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Skapa en virtuell dator baserat på den generaliserade avbildningen

Kör `04a-create-vm-using-generalized-image.yml` slutligen för att skapa en virtuell dator baserat på den generaliserade avbildning som du skapade i föregående steg.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Skapa skaluppsättningar för virtuella datorer baserat på den generaliserade avbildningen

Du kan också skapa en skalauppsättning för den virtuella datorn baserat på den generaliserade avbildningen. Spring `05a-create-vmss-using-generalized-image.yml` för att göra det.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Få information om galleriet

Du kan få information om galleriet, bilddefinitionen och versionen genom att köra `06-get-info.yml`.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Ta bort den delade bilden

Om du vill ta bort galleriresurserna läser du exempelspelsboken `07-delete-gallery.yml`. Ta bort resurser i omvänd ordning. Börja med att ta bort bildversionen. När du har tagit bort alla bildversioner kan du ta bort bilddefinitionen. När du har tagit bort alla bilddefinitioner kan du ta bort galleriet.

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

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln. 

Exempelspelbokskoden i det här avsnittet används för att:

- Ta bort de två resursgrupper som skapats tidigare

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

Här är några viktiga anteckningar att tänka på när du arbetar med exempelspelsboken:

- Ersätt `{{ resource_group_name }}` platshållaren med namnet på resursgruppen.
- Alla resurser inom de två angivna resursgrupperna tas bort.

Kör spelboken med `ansible-playbook` kommandot:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Ansible i Azure](/azure/ansible/)