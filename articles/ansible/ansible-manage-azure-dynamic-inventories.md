---
title: Självstudie – konfigurera dynamiska lager för dina Azure-resurser med Ansible | Microsoft Docs
description: Lär dig hur du använder Ansible för att hantera dina dynamiska Azure-lager
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 46b13fae437a555edf0bdd0b0d4c1496d7596e0f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230689"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Självstudier: Konfigurera dynamiska lager för dina Azure-resurser med Ansible

Ansible kan användas för att hämta lagerinformation från olika källor (däribland molnkällor såsom Azure) till ett *dynamiskt lager*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurera två virtuella datorer för testning. 
> * Tagga en av de virtuella datorerna
> * Installera Nginx på de taggade virtuella datorerna
> * Konfigurera ett dynamisk lager som innehåller de konfigurera Azure-resurserna

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Skapa virtuella datorer för testning

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

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

## <a name="tag-a-vm"></a>Tagga en virtuell dator

Du kan [använda taggar för att organisera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) efter användardefinierade kategorier. 

Ange följande [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag)-kommando för att tagga den virtuella datorn `ansible-inventory-test-vm1` med nyckeln `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```
## <a name="generate-a-dynamic-inventory"></a>Generera ett dynamiskt lager

När du dina virtuella datorer har definierats (och taggats) är det dags att generera det dynamiska lagret.

### <a name="using-ansible-version--28"></a>Med Ansible version < 2.8

Ansible innehåller Python-skriptet [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) som genererar ett dynamisk lager för dina Azure-resurser. I följande steg vägleds du genom användningen av `azure_rm.py`-skript för att ansluta till dina två virtuella Azure-testdatorer:

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

### <a name="ansible-version--28"></a>Ansible-version > = 2.8

Från och med Ansible 2.8, Ansible ger en [plugin-programmet med Azure dynamisk inventering](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Följande steg vägleder dig genom användningen av plugin-programmet:

1. Inventering plugin-programmet kräver en konfigurationsfil. Konfigurationsfilen måste sluta med `azure_rm` och har filnamnstillägget antingen `yml` eller `yaml`. Den här självstudiekursen exempelvis spara följande spelboken som `myazure_rm.yml`:

    ```yml
    plugin: azure_rm
    include_vm_resource_groups:
    - ansible-inventory-test-rg
    auth_source: auto
    ```

1. Kör följande kommando för att pinga virtuella datorer i resursgruppen:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. När du kör föregående kommando, kan du få följande fel:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Om du får ett felmeddelande ”-värdnyckeln verifiering”, lägger du till följande rad i Ansible-konfigurationsfilen. Ansible-konfigurationsfilen finns på `/etc/ansible/ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. När du kör spelboken kan se du resultat som liknar följande utdata:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Aktivera VM-tagg
När du har ställt in en tagg, måste du ”aktivera” taggen i fråga. Ett sätt att aktivera en tagg är genom att exportera taggen till en miljövariabel `AZURE_TAGS` via den `export` kommando:

```azurecli-interactive
export AZURE_TAGS=nginx
```

- Om du använder Ansible < 2.8, kör du följande kommando:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```

- Om du använder Ansible > = 2.8, kör du följande kommando:
  
    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

Nu visas enbart en virtuell dator (det som vars taggen matchar värdet som exporteras till den `AZURE_TAGS` miljövariabeln):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurera Nginx på den taggade virtuella datorn

Syftet med taggar är att göra det möjligt att snabbt och enkelt arbeta med undergrupper för dina virtuella datorer. Anta till exempel att du vill installera Nginx endast på virtuella datorer som du har tilldelat taggen `nginx`. Följande steg illustrerar hur enkelt det är att utföra:

1. Skapa en fil med namnet `nginx.yml`:

   ```azurecli-interactive
   code nginx.yml
   ```

1. Klistra in följande exempelkod i redigeringsprogrammet:

    ```yml
    ---
    - name: Install and start Nginx on an Azure virtual machine
      hosts: all
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

1. Spara filen och avsluta redigeraren.

1. Kör en spelbok med hjälp av den `ansible-playbook` kommando:

   - Ansible < 2.8:

    ```bash
    ansible-playbook -i azure_rm.py nginx.yml
    ```

   - Ansible > = 2.8:

    ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml
    ```

1. När strategiboken, kan du se utdata som liknar följande resultat:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
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

1. Klicka på den `<Ctrl>D` tangentbord kombination att koppla från SSH-sessionen.

1. Gör det föregående steg för den `ansible-inventory-test-vm2` virtuella datorer ger ett informationsmeddelande som anger var du hittar Nginx (vilket förutsätter att du inte har installerats i det här läget):

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
> [Snabbstart: Konfigurera Linux-datorer i Azure med Ansible](/azure/virtual-machines/linux/ansible-create-vm)