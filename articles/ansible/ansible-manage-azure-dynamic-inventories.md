---
title: "Använd Ansible för att hantera dina Azure dynamiska inventeringar"
description: "Lär dig hur du använder Ansible för att hantera dina Azure dynamiska inventeringar"
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamiska inventering
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 799be6d2bb521de38af952376bf8ee14a18846de
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Använd Ansible för att hantera dina Azure dynamiska inventeringar
Ansible kan användas för att hämta inventeringsinformation från olika källor (inklusive molnet källor, till exempel Azure) till en *dynamiska inventering*. I den här artikeln använder du den [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) tagga en av de virtuella datorerna för att konfigurera en Ansible Azure dynamiska inventering som du skapar två virtuella datorer och installera Nginx på taggade virtuell dator.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** - om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Autentiseringsuppgifter för Azure** - [skapa Azure-autentiseringsuppgifter och konfigurera Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Skapa virtuella testdatorer

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [molnet Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

1. Skapa en Azure-resursgrupp för att lagra de virtuella datorerna i den här självstudiekursen.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Skapa två Linux virtuella datorer på Azure med hjälp av en av följande metoder:

    - **Ansible playbook** -artikel, [skapa en grundläggande virtuell dator i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm) illustrerar hur du skapar en virtuell dator från en Ansible playbook. Se till att SSH-anslutningen används i stället för ett lösenord om du använder en playbook för att definiera en eller båda av de virtuella datorerna.

    - **Azure CLI** -problemet var och en av följande kommandon i molnet-gränssnittet för att skapa två virtuella datorer:

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
Du kan [ordna dina Azure-resurser med hjälp av taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) av användardefinierade kategorier. 

Ange följande [az Resurstagg](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) kommando för att tagga den virtuella datorn `ansible-inventory-test-vm1` med nyckel `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generera en dynamisk inventering
När du har de virtuella datorerna definierade (och taggade), är det dags att generera den dynamiska inventeringen. Ansible innehåller en Python-skript som heter [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) som genererar en dynamisk inventering av Azure-resurser genom att göra API-begäranden till Azure Resource Manager. I följande steg beskriver hur du använder den `azure_rm.py` skript för att ansluta till två testa virtuella Azure-datorer:

1. Använd GNU `wget` kommando för att hämta den `azure_rm.py` skript:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Använd den `chmod` kommando för att ändra behörigheter för åtkomst till den `azure_rm.py` skript. Följande kommando använder den `+x` parameter för fjärrkörning av (kör) för den angivna filen (`azure_rm.py`):

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

## <a name="enable-the-virtual-machine-tag"></a>Aktivera virtuella etikett
När du har ställt in den önskade taggen måste du ”aktivera” taggen. Ett sätt att aktivera en tagg genom att exportera taggen till en miljövariabel kallas `AZURE_TAGS` via den **exportera** kommando:

```azurecli-interactive
export AZURE_TAGS=nginx
```

När taggen har exporterats kan du prova den `ansible` kommando igen:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Nu visas bara en virtuell dator (det vars taggen matchar värdet som har exporterat till den **AZURE_TAGS** miljövariabeln):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Ställ in Nginx på taggade VM
Syftet med taggar är att aktivera möjlighet att snabbt och enkelt arbeta med undergrupper av virtuella datorer. Till exempel anta att du vill installera Nginx bara på virtuella datorer som har tilldelats en tagg av `nginx`. Följande steg visar hur enkelt det är att utföra:

1. Skapa en fil (för att innehålla din playbook) med namnet `nginx.yml` på följande sätt:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Infoga följande kod i den nyligen skapade `nginx.yml` fil:

    ```yml
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

1. Kör den `nginx.yml` playbook:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. När du kör playbook finns resultat som liknar följande utdata:

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
Det här avsnittet beskriver en metod för att testa att Nginx är installerad på den virtuella datorn.

1. Använd den [az vm lista-ip-adresser](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) kommando för att hämta IP-adressen för den `ansible-inventory-test-vm1` virtuella datorn. Värdet som returneras (den virtuella IP-adress) används sedan som parameter för SSH-kommandot för att ansluta till den virtuella datorn.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Den [nginx - v](https://nginx.org/en/docs/switches.html) kommandot används vanligtvis för att skriva ut Nginx-version. Det kan dock också användas om Nginx är installerat. Ange den när du är ansluten till den `ansible-inventory-test-vm1` virtuella datorn.

    ```azurecli-interactive
    nginx -v
    ```

1. När du kör den `nginx -v` kommando du ser Nginx-version (andra raden) som anger att Nginx är installerad.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Tryck på den  **&lt;Ctrl > D** tangentbord kombination att koppla från SSH-session.

1. Utför den föregående steg för den `ansible-inventory-test-vm2` virtuella ger ett informationsmeddelande som anger var du kan få Nginx (vilket förutsätter att du inte har nu installerats):

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
