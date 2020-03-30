---
title: Snabbstart – hantera virtuella Linux-datorer i Azure med Ansible
description: I den här snabbstarten kan du lära dig hur du hanterar en virtuell Linux-dator i Azure med Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d94858391951aaf9387394afeb5ad2ae373fa7b5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80239536"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Snabbstart: Hantera virtuella Linux-datorer i Azure med Ansible

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. I den här artikeln använder du en Ansible-spelbok för att starta och stoppa en virtuell Linux-dator. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Stoppa en virtuell dator

I det här avsnittet använder du Ansible för att frigöra (stoppa) en virtuell Azure-dator.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en `azure-vm-stop.yml`fil med namnet och öppna den i redigeraren:

    ```bash
    code azure-vm-stop.yml
    ```

1. Klistra in följande exempelkod i redigeringsprogrammet:

    ```yaml
    - name: Stop Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Stop virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
            allocated: no
    ```

1. Ersätt `{{ resource_group_name }}` platshållarna och `{{ vm_name }}` platshållarna med dina värden.

1. Spara filen och avsluta redigeringsprogrammet.

1. Kör spelboken med `ansible-playbook` kommandot:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. När du har kört spelboken visas utdata som liknar följande resultat:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="start-a-virtual-machine"></a>Starta en virtuell dator

I det här avsnittet använder du Ansible för att starta en virtuell azure-dator med deallocated (stoppad).

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en `azure-vm-start.yml`fil med namnet och öppna den i redigeraren:

    ```bash
    code azure-vm-start.yml
    ```

1. Klistra in följande exempelkod i redigeringsprogrammet:

    ```yaml
    - name: Start Azure VM
      hosts: localhost
      connection: local
      tasks:
        - name: Start virtual machine
          azure_rm_virtualmachine:
            resource_group: {{ resource_group_name }}
            name: {{ vm_name }}
    ```

1. Ersätt `{{ resource_group_name }}` platshållarna och `{{ vm_name }}` platshållarna med dina värden.

1. Spara filen och avsluta redigeringsprogrammet.

1. Kör spelboken med `ansible-playbook` kommandot:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. När du har kört spelboken visas utdata som liknar följande resultat:

    ```bash
    PLAY [Start Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Självstudiekurs: Hantera dynamiska Azure-inventeringar med Ansible](./ansible-manage-azure-dynamic-inventories.md)