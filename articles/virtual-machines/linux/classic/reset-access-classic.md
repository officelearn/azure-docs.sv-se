---
title: Återställ Linux VM lösenord och SSH-nyckeln från CLI | Microsoft Docs
description: Hur du använder VMAccess-tillägget från Azure kommandoradsgränssnittet (CLI) för att återställa Linux VM lösenord eller SSH-nyckel, åtgärda SSH-konfigurationen och kontrollera disk konsekvenskontroll
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
ms.openlocfilehash: c36498d2f4fef506dc7047fe91666aceec73c13d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Hur du återställer en Linux VM lösenord eller SSH-nyckel, åtgärda SSH-konfigurationen och kontrollera disk överensstämmelse med hjälp av VMAccess-tillägget
Om du inte ansluta till en virtuell Linux-dator på Azure på grund av ett nytt lösenord, en felaktigt SSH (Secure Shell) nyckel eller ett problem med SSH-konfigurationen använder VMAccessForLinux-tillägget med Azure CLI för att återställa lösenord eller SSH-nyckel, åtgärda SSH-konfigurationen och kontrollera disk konsekvenskontroll. 

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Med Azure CLI, använder du den **azure vm-tillägget set** kommandot från din kommandoradsgränssnittet (Bash, Terminal, Kommandotolken) för att få åtkomst till kommandon. Kör **azure vm-tillägget hjälpfunktion** för tillägg för detaljerad användning.

Med Azure CLI, kan du göra följande:

* [Återställa lösenordet](#pwresetcli)
* [Återställ SSH-nyckeln](#sshkeyresetcli)
* [Återställa lösenordet och SSH-nyckeln](#resetbothcli)
* [Skapa ett nytt användarkonto sudo](#createnewsudocli)
* [Återställ SSH-konfigurationen](#sshconfigresetcli)
* [Tar bort en användare](#deletecli)
* [Visa status för VMAccess-tillägget](#statuscli)
* [Kontrollera konsekvensen för tillagda diskar](#checkdisk)
* [Reparera tillagda diskar på Linux-VM](#repairdisk)

## <a name="prerequisites"></a>Krav
Du måste göra följande:

* Du behöver [installerar Azure CLI](../../../cli-install-nodejs.md) och [ansluta till din prenumeration](/cli/azure/authenticate-azure-cli) att använda Azure-resurser som är kopplade till ditt konto.
* Ange rätt läge för den klassiska distributionsmodellen genom att skriva följande kommando i Kommandotolken:
    ``` 
        azure config mode asm
    ```
* Har ett nytt lösenord eller SSH-nycklar, om du vill återställa någon. Du behöver inte dessa om du vill återställa SSH-konfigurationen.

## <a name="pwresetcli"></a>Återställa lösenordet
1. Skapa en fil på den lokala datorn med namnet PrivateConf.json med dessa rader. Ersätt **MittAnvändarnamn** och **myP@ssW0rd** med användarnamn och lösenord och Ställ in datum för förfallodatum.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Kör det här kommandot ersätter namnet på den virtuella datorn för **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Återställ SSH-nyckeln
1. Skapa en fil som heter PrivateConf.json med innehållet. Ersätt den **MittAnvändarnamn** och **mySSHKey** värden med din egen information.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Kör det här kommandot ersätter namnet på den virtuella datorn för **myVM**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Återställa lösenordet och SSH-nyckeln
1. Skapa en fil som heter PrivateConf.json med innehållet. Ersätt den **MittAnvändarnamn**, **mySSHKey** och **myP@ssW0rd** värden med din egen information.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Kör det här kommandot ersätter namnet på den virtuella datorn för **myVM**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Skapa ett nytt användarkonto sudo

Om du glömmer ditt användarnamn kan använda du VMAccess för att skapa en ny med sudo-behörighet. I det här fallet kommer befintliga användarnamn och lösenord inte att ändras.

Om du vill skapa en ny användare med sudo med lösenordsåtkomst, använder du skriptet i [återställa lösenordet](#pwresetcli) och ange det nya namnet.

Om du vill skapa en ny sudo-användare med SSH-nyckel åtkomst, använder du skriptet i [återställa SSH-nyckeln](#sshkeyresetcli) och ange det nya namnet.

Du kan också använda [återställa lösenordet och SSH-nyckeln](#resetbothcli) att skapa en ny användare med både lösenord och SSH-nyckel åtkomst.

## <a name="sshconfigresetcli"></a>Återställ SSH-konfigurationen
Om SSH-konfigurationen finns i ett oönskat tillstånd, kan du också förlora åtkomst till den virtuella datorn. Du kan använda VMAccess-tillägget för att återställa konfigurationen till sitt ursprungsläge. Om du vill göra det, behöver du bara anger nyckeln ”reset_ssh” till ”True”. Tillägget kommer att starta om SSH-server, öppna SSH-porten på den virtuella datorn och återställa SSH-konfigurationen till standardvärden. Användarkonto (namn, lösenord eller SSH-nycklar) ändras inte.

> [!NOTE]
> SSH-konfigurationsfilen som hämtar återställa finns i /etc/ssh/sshd_config.
> 
> 

1. Skapa en fil som heter PrivateConf.json med det här innehållet.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Kör det här kommandot ersätter namnet på den virtuella datorn för **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Tar bort en användare
Om du vill ta bort ett användarkonto utan att logga in till den virtuella datorn direkt kan du använda det här skriptet.

1. Skapa en fil med namnet PrivateConf.json med det här innehållet ersätter användarnamn för att ta bort för **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Kör det här kommandot ersätter namnet på den virtuella datorn för **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>Visa status för VMAccess-tillägget
Om du vill visa status för VMAccess-tillägget, kör du kommandot.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Kontrollera konsekvensen för tillagda diskar
Om du vill köra fsck på alla diskar i Linux-dator, behöver du göra följande:

1. Skapa en fil som heter PublicConf.json med det här innehållet. Kontrollera tar ett booleskt värde för om du vill kontrollera diskar som är anslutna till den virtuella datorn eller inte. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Kör det här kommandot ska köras, ersätter namnet på den virtuella datorn för **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Reparera diskar
Använd VMAccess-tillägget för att återställa mount-konfigurationen på den virtuella Linux-datorn för att reparera diskar som inte montera eller har montera konfigurationsfel. Ersätt namnet på disken för **myDisk**.

1. Skapa en fil som heter PublicConf.json med det här innehållet. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Kör det här kommandot ska köras, ersätter namnet på den virtuella datorn för **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Nästa steg
* Om du vill använda Azure PowerShell-cmdlets eller Azure Resource Manager-mallar för att återställa lösenord eller SSH-nyckel, åtgärda SSH konfiguration och kontrollera disk konsekvenskontroll finns i [VMAccess-tillägget dokumentation på GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Du kan också använda den [Azure-portalen](https://portal.azure.com) återställa lösenord eller SSH-nyckel för en Linux-VM som distribuerats i den klassiska distributionsmodellen. Det går inte att du använder portalen gör detta för en Linux-VM distribuerats i Resource Manager-distributionsmodellen.
* Se [om virtuella datortillägg och funktioner](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer information om användning av VM-tillägg för virtuella Azure-datorer.

