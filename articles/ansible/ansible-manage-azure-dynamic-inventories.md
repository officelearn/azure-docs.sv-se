---
title: Självstudiekurs - Konfigurera dynamiska inventeringar av dina Azure-resurser med Ansible
description: Lär dig hur du använder Ansible för att hantera dina dynamiska Azure-lager
keywords: ansible, azure, devops, bash, cloudshell, dynamic inventory
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247859"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Självstudiekurs: Konfigurera dynamiska inventeringar av dina Azure-resurser med Ansible

Ansible kan användas för att hämta lagerinformation från olika källor (däribland molnkällor såsom Azure) till ett *dynamiskt lager*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurera två virtuella testdatorer. 
> * Tagga en av de virtuella datorerna
> * Installera Nginx på de taggade virtuella datorerna
> * Konfigurera ett dynamiskt lager som innehåller konfigurerade Azure-resurser

## <a name="prerequisites"></a>Krav

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Skapa virtuella test-datorer

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Skapa en Azure-resursgrupp för att lagra de virtuella datorerna för den här självstudien.

    > [!IMPORTANT]  
    > Den Azure-resursgrupp som du skapar i det här steget måste ha ett namn som helt består av gemener. Annars misslyckas genereringen av det dynamiska lagret.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Skapa två virtuella Linux-datorer på Azure med någon av följande metoder:

    - **Ansible-spelbok** – Artikeln [Skapa en grundläggande virtuell dator i Azure med Ansible](./ansible-create-vm.md) illustrerar hur du skapar en virtuell dator från en Ansible-spelbok. Om du använder en spelbok för att definiera en eller båda av de virtuella datorerna kontrollerar du att SSH-anslutningen används i stället för ett lösenord.

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

### <a name="using-ansible-version--28"></a>Använda Ansible version < 2.8
Ange följande [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag)-kommando för att tagga den virtuella datorn `ansible-inventory-test-vm1` med nyckeln `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Använda Ansible version >= 2,8
Ange följande [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag)-kommando för att tagga den virtuella datorn `ansible-inventory-test-vm1` med nyckeln `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generera ett dynamiskt lager

När du dina virtuella datorer har definierats (och taggats) är det dags att generera det dynamiska lagret.

### <a name="using-ansible-version--28"></a>Använda Ansible version < 2.8

Ansible tillhandahåller ett Python-skript med namnet [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) som genererar en dynamisk inventering av dina Azure-resurser. I följande steg vägleds du genom användningen av `azure_rm.py`-skript för att ansluta till dina två virtuella Azure-testdatorer:

1. Använd GNU-kommandot `wget` för att hämta skriptet `azure_rm.py`:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Använd kommandot `chmod` för att ändra åtkomstbehörigheterna till skriptet `azure_rm.py`. Följande kommando använder parametern `+x` för att tillåta körning av den angivna filen (`azure_rm.py`):

    ```python
    chmod +x azure_rm.py
    ```

1. Använd [ansible-kommandot](https://docs.ansible.com/ansible/2.4/ansible.html) för att ansluta till din resursgrupp: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. När anslutningen har upprättats ser du resultat som liknar följande utdata:

    ```output
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

### <a name="ansible-version--28"></a>Ansible version >= 2,8

Från och med Ansible 2.8 tillhandahåller Ansible ett [plugin-program för azure dynamic-inventory](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Följande steg går igenom med hjälp av plugin-programmet:

1. Plugin-programmet för lager kräver en konfigurationsfil. Konfigurationsfilen måste `azure_rm` sluta och ha `yml` ett `yaml`tillägg av antingen eller . I det här självstudieexemplet sparar du följande spelbok som: `myazure_rm.yml`

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Kör följande kommando för att pinga virtuella datorer i resursgruppen:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. När du kör föregående kommando kan följande felmeddelande visas:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Om felet "värdnyckelverifiering" visas lägger du till följande rad i konfigurationsfilen Ansible. Konfigurationsfilen Ansible finns `/etc/ansible/ansible.cfg` `~/.ansible.cfg`på eller .

    ```bash
    host_key_checking = False
    ```

1. När du kör spelboken visas resultat som liknar följande utdata:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Aktivera VM-taggen

### <a name="if-youre-using-ansible--28"></a>Om du använder Ansible < 2.8,

- När du har angett en tagg måste du "aktivera" taggen. Ett sätt att aktivera en tagg är att `AZURE_TAGS` exportera `export` taggen till en miljövariabel via kommandot:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Kör följande kommando:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Nu ser du bara en virtuell dator (den vars `AZURE_TAGS` tagg matchar värdet som exporteras till miljövariabeln):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Om du använder Ansible >= 2,8

- Kör kommandot `ansible-inventory -i myazure_rm.yml --graph` för att få följande utdata:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Du kan också köra följande kommando för att testa anslutningen till den virtuella datorn för Nginx:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Konfigurera Nginx på den taggade virtuella datorn

Syftet med taggar är att göra det möjligt att snabbt och enkelt arbeta med undergrupper för dina virtuella datorer. Anta till exempel att du vill installera Nginx endast på virtuella datorer som du har tilldelat taggen `nginx`. Följande steg illustrerar hur enkelt det är att utföra:

1. Skapa en `nginx.yml`fil med namnet:

   ```console
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
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Spara filen och avsluta redigeringsprogrammet.

1. Kör spelboken med `ansible-playbook` kommandot:

   - Ansible < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. När du har kört spelboken visas utdata som liknar följande resultat:

    ```output
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

    ```console
    nginx -v
    ```

1. När du kör kommandot `nginx -v` ser du den Nginx-version (andra raden) som anger att Nginx är installerat.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Klicka `<Ctrl>D` på tangentbordskombinationen om du vill koppla från SSH-sessionen.

1. Om du gör föregående `ansible-inventory-test-vm2` steg för den virtuella datorn får du ett informationsmeddelande som anger var du kan få Nginx (vilket innebär att du inte har det installerat just nu):

    ```output
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
> [Snabbstart: Konfigurera virtuella Linux-datorer i Azure med Ansible](./ansible-create-vm.md)