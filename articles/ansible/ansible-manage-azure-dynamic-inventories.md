---
title: Använda Ansible för att hantera dina dynamiska Azure-lager
description: Lär dig hur du använder Ansible för att hantera dina dynamiska Azure-lager
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: tutorial
ms.openlocfilehash: cf404b84377a37f9a97f7e7c9c9f86402fe999e0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359671"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Använda Ansible för att hantera dina dynamiska Azure-lager
Ansible kan användas för att hämta lagerinformation från olika källor (däribland molnkällor såsom Azure) till ett *dynamiskt lager*. I den här artikeln använder du [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) för att konfigurera ett dynamiskt Ansible Azure-lager där du skapar två virtuella datorer, taggar en av de virtuella datorerna samt installerar Nginx på den taggade virtuella datorn.

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.

- **Azure-autentiseringsuppgifter** - [Skapa Azure-autentiseringsuppgifter och konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Skapa en Azure-resursgrupp för att lagra de virtuella datorerna för den här självstudien.

    > [!IMPORTANT]  
    > Den Azure-resursgrupp som du skapar i det här steget måste ha ett namn som helt består av gemener. Annars misslyckas genereringen av det dynamiska lagret.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Skapa två virtuella Linux-datorer på Azure med någon av följande metoder:

    - **Ansible-spelbok** – Artikeln [Skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm) illustrerar hur du skapar en virtuell dator från en Ansible-spelbok. Om du använder en spelbok för att definiera en eller båda av de virtuella datorerna kontrollerar du att SSH-anslutningen används i stället för ett lösenord.

    - **Azure CLI** – Ange vart och ett av följande kommandon i Cloud Shell för att skapa de två virtuella datorerna:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Tagga en virtuell dator
Du kan [använda taggar för att organisera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) efter användardefinierade kategorier. 

Ange följande [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag)-kommando för att tagga den virtuella datorn `ansible-inventory-test-vm1` med nyckeln `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generera ett dynamiskt lager
När du dina virtuella datorer har definierats (och taggats) är det dags att generera det dynamiska lagret. Ansible innehåller ett Python-skript som heter [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) som genererar ett dynamiskt lager för dina Azure-resurser genom att göra API-begäranden till Azure Resource Manager. I följande steg vägleds du genom användningen av `azure_rm.py`-skript för att ansluta till dina två virtuella Azure-testdatorer:

1. Använd GNU-kommandot `wget` för att hämta skriptet `azure_rm.py`:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Använd kommandot `chmod` för att ändra åtkomstbehörigheterna till skriptet `azure_rm.py`. Följande kommando använder parametern `+x` för att tillåta körning av den angivna filen (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Använd [ansible-kommandot](https://docs.ansible.com/ansible/2.4/ansible.html) för att ansluta till din resursgrupp: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. När anslutningen har upprättats ser du resultat som liknar följande utdata:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Aktivera taggen för virtuell dator
När du har angett den önskade taggen behöver du ”aktivera” taggen. Ett sätt att aktivera en tagg är att exportera taggen till en miljövariabel som heter `AZURE_TAGS` via kommandot **exportera**:

```azurecli-interactive
export AZURE_TAGS=nginx
```

När taggen har exporterats kan du prova kommandot `ansible` igen:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Nu visas enbart en virtuell dator (den vars tagg matchar det värde som exporteras till miljövariabeln **AZURE_TAGS**):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurera Nginx på den taggade virtuella datorn
Syftet med taggar är att göra det möjligt att snabbt och enkelt arbeta med undergrupper för dina virtuella datorer. Anta till exempel att du vill installera Nginx endast på virtuella datorer som du har tilldelat taggen `nginx`. Följande steg illustrerar hur enkelt det är att utföra:

1. Skapa en fil (som ska innehålla din spelbok) med namnet `nginx.yml` på följande sätt:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Infoga följande kod i den nyligen skapade filen `nginx.yml`:

    ```yml
    ---
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
      apt: pkg=nginx state=installed
      notify:
      - start nginx

    handlers:
    - name: start nginx
      service: name=nginx state=started
    ```

1. Kör spelboken `nginx.yml`:

    ```azurecli-interactive
    ansible-playbook -i azure_rm.py nginx.yml
    ```

1. När du kör spelboken ser du resultat som liknar följande utdata:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testa Nginx-installation
Det här avsnittet illustrerar en metod för att testa att Nginx är installerat på den virtuella datorn.

1. Använd kommandot [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) för att hämta IP-adressen för den virtuella datorn `ansible-inventory-test-vm1`. Det returnerade värdet (den virtuella datorns IP-adress) används sedan som parameter för SSH-kommandot för anslutning till den virtuella datorn.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. När du är ansluten till den virtuella datorn `ansible-inventory-test-vm1` kör du kommandot [nginx -v](https://nginx.org/en/docs/switches.html) för att avgöra huruvida Nginx är installerat.

    ```azurecli-interactive
    nginx -v
    ```

1. När du kör kommandot `nginx -v` ser du den Nginx-version (andra raden) som anger att Nginx är installerat.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Tryck på tangentbordskombinationen **&lt;Ctrl+D** för att koppla från SSH-sessionen.

1. När föregående steg för den virtuella datorn `ansible-inventory-test-vm2` utförs visas ett informationsmeddelande som anger var du kan hämta Nginx (vilket tyder på att du inte har det installerat i det här läget):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)
