---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ae29451e3f7ec263f296e69656a5c66045334687
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227333"
---
1. Logga in på Azure-prenumerationen med hjälp av anvisningarna i [Anslut till Azure från den klassiska Azure CLI](/cli/azure/authenticate-azure-cli).

2. Se till att du använder läget för klassisk distribution:

    ```azurecli
    azure config mode asm
    ```

3. Ta reda på den Linux-avbildning som du vill läsa in från de tillgängliga avbildningarna på följande sätt:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    I ett kommandotolksfönster i Windows använder du **söka** i stället för grep.
   
4. Använd `azure vm create` för att skapa en virtuell dator med Linux-avbildningen från föregående lista. I det här steget skapas en molntjänst och ett lagringskonto. Du kan också ansluta den virtuella datorn till en befintlig molntjänst med alternativet `-c`. Skapa en SSH-slutpunkt för att logga in på den virtuella Linux-datorn med alternativet `-e`. I följande exempel skapas en virtuell dator med namnet `myVM` med hjälp av avbildningen `Ubuntu-14_04_4-LTS` på platsen `West US`, och lägger till ett användarnamn `ops`:
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > För en virtuell Linux-dator måste du ange alternativet `-e` i `vm create`. Det går inte att aktivera SSH när den virtuella datorn har skapats. Mer information om SSH finns i [Så här använder du SSH med Linux på Azure](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Du kan verifiera attribut för den virtuella datorn med hjälp av kommandot `azure vm show`. I följande exempel visas information för den virtuella datorn med namnet `myVM`:

    ```azurecli   
    azure vm show myVM
    ```

6. Starta den virtuella datorn med kommandot `azure vm start` enligt följande:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om alla dessa Azure klassiska CLI kommandon för virtuella datorer finns den [med hjälp av Azure klassiska CLI med klassisk distribution API](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

