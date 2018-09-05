---
title: Använd Ansible för att hantera dina Azure dynamiska lager
description: Lär dig hur du använder Ansible för att hantera dina Azure dynamiska lager
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamisk lager
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: article
ms.openlocfilehash: 1b8c1ba80b4c69f36e8304cbe978452a359ac911
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698087"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Använd Ansible för att hantera dina Azure dynamiska lager
Ansible kan användas för att hämta inventeringsinformation från olika källor (inklusive molnkällor, till exempel Azure) i en *dynamisk lager*. I den här artikeln använder du den [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) tagga en av de virtuella datorerna för att konfigurera en Ansible Azure dynamisk lager där du skapar två virtuella datorer och installera Nginx på de taggade virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** – Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.

- **Autentiseringsuppgifter för Azure** - [skapa Azure-autentiseringsuppgifter och konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Skapa en Azure-resursgrupp för att lagra de virtuella datorerna för den här självstudien.

    > [!IMPORTANT]  
    > Azure-resursgrupp som du skapar i det här steget måste ha ett namn som är helt och hållet gemener. I annat fall misslyckas generering av dynamisk lager.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Skapa två virtuella Linux-datorer på Azure med någon av följande metoder:

    - **Ansible-spelbok** -artikeln [skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm) illustrerar hur du skapar en virtuell dator från en Ansible-spelbok. Om du använder en spelbok för att definiera en eller båda av de virtuella datorerna kan du se till att SSH-anslutning används i stället för ett lösenord.

    - **Azure CLI** -problem som var och en av följande kommandon i Cloud Shell för att skapa två virtuella datorer:

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
Du kan [använda taggar för att organisera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) av användardefinierade kategorier. 

Ange följande [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) kommando för att tagga den virtuella datorn `ansible-inventory-test-vm1` med nyckeln `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generera ett dynamisk lager
När du har dina virtuella datorer definierade (och taggade), är det dags att generera den dynamiska inventeringen. Ansible innehåller ett pythonskript som heter [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) som genererar ett dynamisk lager för dina Azure-resurser genom att göra API-begäranden till Azure Resource Manager. I följande steg beskriver hur du använder den `azure_rm.py` skript för att ansluta till två testa Azure-datorer:

1. Använd GNU `wget` kommando för att hämta den `azure_rm.py` skript:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Använd den `chmod` kommando för att ändra behörigheter för åtkomst till den `azure_rm.py` skript. Följande kommando använder de `+x` parametern så att körningen (körs) av den angivna filen (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Använd den [ansible kommandot](https://docs.ansible.com/ansible/2.4/ansible.html) att ansluta till din resursgrupp: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. När du är ansluten, kan du se resultat som liknar följande utdata:

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

## <a name="enable-the-virtual-machine-tag"></a>Aktivera VM-tagg
När du har ställt in den önskade taggen, måste du ”aktivera” taggen. Ett sätt att aktivera en tagg genom att exportera taggen till en miljövariabel kallas `AZURE_TAGS` via den **exportera** kommando:

```azurecli-interactive
export AZURE_TAGS=nginx
```

När taggen har exporterats, försöker du att den `ansible` -kommandot på nytt:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Nu visas enbart en virtuell dator (det som vars taggen matchar värdet som exporteras till den **AZURE_TAGS** miljövariabeln):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurera Nginx på de taggade virtuella datorn
Syftet med taggar är att göra möjligheten att snabbt och enkelt arbeta med undergrupper för dina virtuella datorer. Exempel: Anta att du vill installera Nginx bara på virtuella datorer som du har tilldelat en tagg för `nginx`. Följande steg illustrerar hur enkelt det är att utföra:

1. Skapa en fil (för att innehålla din spelbok) med namnet `nginx.yml` på följande sätt:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Infoga följande kod i den nyligen skapade `nginx.yml` fil:

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

1. Kör den `nginx.yml` spelbok:

    ```azurecli-interactive
    ansible-playbook -i azure_rm.py nginx.yml
    ```

1. När du har kört spelboken kan se du resultat som liknar följande utdata:

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

1. Använd den [az vm list-ip-adresser](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) kommando för att hämta IP-adressen för den `ansible-inventory-test-vm1` virtuell dator. Värdet som returneras (den virtuella datorns IP-adress) används sedan som parameter för SSH-kommandot för att ansluta till den virtuella datorn.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. När du är ansluten till den `ansible-inventory-test-vm1` virtuell dator, kör den [nginx - v](https://nginx.org/en/docs/switches.html) kommando för att avgöra om Nginx har installerats.

    ```azurecli-interactive
    nginx -v
    ```

1. När du kör den `nginx -v` kommandot du ser Nginx-version (andra raden) som anger att Nginx är installerat.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Tryck på den  **&lt;Ctrl > D** tangentbord kombination att koppla från SSH-sessionen.

1. Utför föregående steg för den `ansible-inventory-test-vm2` virtuella datorer ger ett informationsmeddelande som anger var du hittar Nginx (vilket förutsätter att du inte har installerats i det här läget):

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
