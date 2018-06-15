---
title: Kör Ansible med Bash i Azure-molnet Shell
description: Lär dig hur du utför olika uppgifter för Ansible med Bash i Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150387"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Kör Ansible med Bash i Azure-molnet Shell

I kursen får lära du att utföra olika uppgifter för Ansible från Bash i molnet Shell. Dessa omfattar att ansluta till en virtuell dator och skapa Ansible playbooks för att skapa och ta bort en Azure-resursgrupp.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** - om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Autentiseringsuppgifter för Azure** - [skapa Azure-autentiseringsuppgifter och konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Konfigurera Azure Cloud Shell** - om du är nybörjare på Azure-molnet Shell, artikeln [Snabbstart för Bash i Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) visar hur du kan starta och konfigurera molnet Shell. Starta en dedikerad webbplats för molnet Shell här:

[![Starta molnet Shell](https://shell.azure.com/images/launchcloudshell.png "starta molnet Shell")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Använd Ansible för att ansluta till en virtuell dator
Ansible har skapat en Python-skript som heter [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) som genererar en dynamisk inventering av Azure-resurser genom att göra API-begäranden till Azure Resource Manager. I följande steg beskriver hur du använder den `azure_rm.py` skript för att ansluta till en virtuell Azure-dator:

1. Öppna Bash i molnet Shell. Shell-typen markeras på vänster sida av fönstret molnet Shell.

1. Om du inte har en virtuell dator att använda anger du följande kommandon i molnet-Shell för att skapa en virtuell dator som du vill testa:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Använd GNU `wget` kommando för att hämta den `azure_rm.py` skript:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Använd den `chmod` kommando för att ändra behörigheter för åtkomst till den `azure_rm.py` skript. Följande kommando använder den `+x` parameter för fjärrkörning av (kör) för den angivna filen (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Använd den [ansible kommandot](https://docs.ansible.com/ansible/2.4/ansible.html) att ansluta till den virtuella datorn: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  När du är ansluten, bör du se resultat som liknar utdata:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Om du har skapat en resursgrupp och den virtuella datorn i det här avsnittet

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Kör en spelbok i Cloud Shell
Den [ansible playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) körs kommandot Ansible playbooks, körs aktiviteterna på den aktuella värddatorer. Det här avsnittet vägleder dig genom att använda molnet Shell att skapa och köra två playbooks - en för att skapa en resursgrupp och en andra att ta bort resursgruppen. 

1. Skapa en fil med namnet `rg.yml` på följande sätt:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Kopiera följande innehåll i fönstret molnet Shell (nu värd för en instans av redigeraren VI):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. Spara filen och avsluta redigeraren VI genom att ange `:wq` och trycka på &lt;RETUR >.

1. Använd den `ansible-playbook` kommando för att köra den `rg.yml` playbook:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Du bör se resultat som liknar följande utdata:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Kontrollera distributionen:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Nu när du har skapat resursgruppen kan du skapa en andra Ansible playbook för att ta bort resursgruppen:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Kopiera följande innehåll i fönstret molnet Shell (nu värd för en instans av redigeraren VI):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. Spara filen och avsluta redigeraren VI genom att ange `:wq` och trycka på &lt;RETUR >.

1. Använd den `ansible-playbook` kommando för att köra den `rg2.yml` playbook:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Du bör se resultat som liknar följande utdata:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)
