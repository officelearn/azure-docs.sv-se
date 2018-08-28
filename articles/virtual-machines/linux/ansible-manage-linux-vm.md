---
title: Använda Ansible för att hantera en virtuell Linux-dator i Azure
description: Lär dig hur du använder Ansible för att hantera en virtuell Linux-dator i Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: 66106346b298fae22cce47081916a6c8eec8fd40
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250689"
---
# <a name="use-ansible-to-manage-a-linux-virtual-machine-in-azure"></a>Använda Ansible för att hantera en virtuell Linux-dator i Azure
Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. Du kan använda Ansible för att hantera dina virtuella Azure-datorer på samma sätt som för alla andra resurser. Den här artikeln visar hur du använder en Ansible-spelbok för att starta och stoppa en virtuell Linux-dator. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Konfigurera Azure Cloud Shell** eller **Installera och konfigurera Ansible på en virtuell Linux-dator**

  **Konfigurera Azure Cloud Shell**

  1. **Konfigurera Azure Cloud Shell** – Om du inte har använt Azure Cloud Shell tidigare rekommenderar vi att du läser artikeln [Snabbstart för Bash i Azure Cloud Shell](/azure/cloud-shell/quickstart), som beskriver hur du startar och konfigurerar Cloud Shell. 

  1. **Virtuell Linux-dator** – om du inte har tillgång till en virtuell Linux-dator kan du [skapa en virtuell Linux-dator med Ansible](ansible-create-vm.md).

  **Eller**

  **Installera och konfigurera Ansible på en virtuell Linux-dator**

  1. **Installera Ansible** – Installera Ansible på en [Linux-plattform som stöds](/azure/virtual-machines/linux/ansible-install-configure#install-ansible-on-an-azure-linux-virtual-machine).

  1. **Konfigurera Ansible** - [Skapa Azure-autentiseringsuppgifter och konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="use-ansible-to-deallocate-stop-an-azure-virtual-machine"></a>Använda Ansible för att frigöra (stoppa) en virtuell Azure-dator
Det här avsnittet visar hur du använder Ansible för att frigöra (stoppa) en virtuell Azure-dator

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil (som ska innehålla din spelbok) med namnet `azure_vm_stop.yml` och öppna den i VI-redigeringsprogrammet på följande sätt:

  ```azurecli-interactive
  vi azure_vm_stop.yml
  ```

1. Starta infogningsläget genom att trycka på tangenten **I**.

1. Klistra in följande exempelkod i redigeringsprogrammet:

    ```yaml
    - name: Stop Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Deallocate the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
            allocated: no 
    ```

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Kör Ansible-spelboksexemplet.

  ```bash
  ansible-playbook azure_vm_stop.yml
  ```

1. Utdata liknar följande exempel som visar att den virtuella datorn har frigjorts (stoppats):

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Deallocate the Virtual Machine] ***************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="use-ansible-to-start-a-deallocated-stopped-azure-virtual-machine"></a>Använda Ansible för att starta en frigjord (stoppad) virtuell Azure-dator
Det här avsnittet visar hur du använder Ansible för att starta en frigjord (stoppad) virtuell Azure-dator

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil (som ska innehålla din spelbok) med namnet `azure_vm_start.yml` och öppna den i VI-redigeringsprogrammet på följande sätt:

  ```azurecli-interactive
  vi azure_vm_start.yml
  ```

1. Starta infogningsläget genom att trycka på tangenten **I**.

1. Klistra in följande exempelkod i redigeringsprogrammet:

    ```yaml
    - name: Start Azure VM
    hosts: localhost
    connection: local
    tasks:
    - name: Start the virtual machine
        azure_rm_virtualmachine:
            resource_group: myResourceGroup
            name: myVM
    ```

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Kör Ansible-spelboksexemplet.

  ```bash
  ansible-playbook azure_vm_start.yml
  ```

1. Utdata liknar följande exempel som visar att den virtuella datorn har startats:

    Utdata liknar följande exempel som visar att den virtuella datorn har startats:

    ```bash
    PLAY [Stop Azure VM] ********************************************************

    TASK [Gathering Facts] ******************************************************
    ok: [localhost]

    TASK [Start the Virtual Machine] ********************************************
    changed: [localhost]

    PLAY RECAP ******************************************************************
    localhost                  : ok=2    changed=1    unreachable=0    failed=0
    ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Använda Ansible för att hantera dina dynamiska Azure-lager](../../ansible/ansible-manage-azure-dynamic-inventories.md)