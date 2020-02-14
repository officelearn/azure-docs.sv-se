---
title: Snabb start – hantera virtuella Linux-datorer i Azure med Ansible
description: I den här snabb starten lär du dig att hantera en virtuell Linux-dator i Azure med Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 90113373535c835e5cddf707bcb520789e596aec
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202405"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Snabb start: hantera virtuella Linux-datorer i Azure med Ansible

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. I den här artikeln använder du en Ansible-Spelbok för att starta och stoppa en virtuell Linux-dator. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Stoppa en virtuell dator

I det här avsnittet använder du Ansible för att frigöra (stoppa) en virtuell Azure-dator.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil med namnet `azure-vm-stop.yml`och öppna den i redigeraren:

    ```azurecli-interactive
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

1. Ersätt plats hållarna `{{ resource_group_name }}` och `{{ vm_name }}` med dina värden.

1. Spara filen och avsluta redigeraren.

1. Kör Spelbok med kommandot `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. När du har kört Spelbok visas utdata som liknar följande resultat:

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

I det här avsnittet använder du Ansible för att starta en avallokerad virtuell Azure-dator (Stoppad).

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil med namnet `azure-vm-start.yml`och öppna den i redigeraren:

    ```azurecli-interactive
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

1. Ersätt plats hållarna `{{ resource_group_name }}` och `{{ vm_name }}` med dina värden.

1. Spara filen och avsluta redigeraren.

1. Kör Spelbok med kommandot `ansible-playbook`:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. När du har kört Spelbok visas utdata som liknar följande resultat:

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
> [Självstudie: hantera Azures dynamiska lager med Ansible](./ansible-manage-azure-dynamic-inventories.md)