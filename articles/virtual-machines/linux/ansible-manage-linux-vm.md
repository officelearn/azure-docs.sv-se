---
title: Snabbstart – hantera Linux-datorer i Azure med Ansible | Microsoft Docs
description: I den här snabbstarten lär du dig hur du hanterar en Linux-dator i Azure med Ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: cab179980f6093bf259556fd690e55c99a817c79
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760651"
---
# <a name="quickstart-manage-linux-virtual-machines-in-azure-using-ansible"></a>Snabbstart: Hantera Linux-datorer i Azure med Ansible

Med Ansible kan du automatisera distributionen och konfigurationen av resurser i din miljö. I den här artikeln använder du en Ansible-spelbok för att starta och stoppa en virtuell Linux-dator. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="stop-a-virtual-machine"></a>Stoppa en virtuell dator

I det här avsnittet ska använda du Ansible till Frigör (stop) en Azure virtuell dator.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil med namnet `azure-vm-stop.yml`, och öppna den i redigeraren:

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

1. Ersätt den `{{ resource_group_name }}` och `{{ vm_name }}` platshållarna med dina värden.

1. Spara filen och avsluta redigeraren.

1. Kör en spelbok med hjälp av den `ansible-playbook` kommando:

    ```bash
    ansible-playbook azure-vm-stop.yml
    ```

1. När strategiboken, kan du se utdata som liknar följande resultat:

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

Du använder Ansible för att starta en deallocated (stoppad) Azure-dator i det här avsnittet.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](/azure/cloud-shell/overview).

1. Skapa en fil med namnet `azure-vm-start.yml`, och öppna den i redigeraren:

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

1. Ersätt den `{{ resource_group_name }}` och `{{ vm_name }}` platshållarna med dina värden.

1. Spara filen och avsluta redigeraren.

1. Kör en spelbok med hjälp av den `ansible-playbook` kommando:

    ```bash
    ansible-playbook azure-vm-start.yml
    ```

1. När strategiboken, kan du se utdata som liknar följande resultat:

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
> [Självstudie: Hantera Azure dynamiska lager med Ansible](~/articles/ansible/ansible-manage-azure-dynamic-inventories.md)