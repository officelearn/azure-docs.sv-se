---
title: Snabbstart för Azure Cloud Shell - Bash
description: Lär dig hur du använder bash-kommandoraden i din webbläsare med Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458077"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Snabbstart för Bash i Azure Cloud Shell

Det här dokumentet beskriver hur du använder Bash i Azure Cloud Shell i [Azure-portalen](https://ms.portal.azure.com/).

> [!NOTE]
> En [PowerShell i Azure Cloud Shell](quickstart-powershell.md) Quickstart är också tillgänglig.

## <a name="start-cloud-shell"></a>Starta Cloud Shell
1. Starta **Cloud Shell** från den övre navigeringen i Azure-portalen. <br>
![](media/quickstart/shell-icon.png)

2. Välj en prenumeration för att skapa ett lagringskonto och Microsoft Azure Files-resurs.
3. Välj "Skapa lagring"

> [!TIP]
> Du autentiseras automatiskt för Azure CLI i varje session.

### <a name="select-the-bash-environment"></a>Välj Bash-miljön
Kontrollera att miljön rullgardinsmenyn från vänster sida `Bash`av skalfönstret säger . <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ställ in din prenumeration
1. Lista prenumerationer som du har åtkomst till.
   ```azurecli-interactive
   az account list
   ```

2. Ställ in önskad prenumeration: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Din prenumeration kommer att bli `/home/<user>/.azure/azureProfile.json`ihågkommen för framtida sessioner med .

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en ny resursgrupp i WestUS med namnet "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Skapa en virtuell Linux-dator
Skapa en virtuell Ubuntu-dator i den nya resursgruppen. Azure CLI skapar SSH-nycklar och konfigurerar den virtuella datorn med dem. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Med `--generate-ssh-keys` hjälp av instruerar Azure CLI att skapa och `$Home` konfigurera offentliga och privata nycklar i din virtuella dator och katalog. Som standard placeras nycklar i `/home/<user>/.ssh/id_rsa` `/home/<user>/.ssh/id_rsa.pub`Cloud Shell at och . Mappen `.ssh` sparas i den bifogade filresursens 5 GB-avbildning som användes för att spara `$Home`.

Ditt användarnamn på den här virtuella datorn ärUser@Azure:ditt användarnamn som används i Cloud Shell ($ ).

### <a name="ssh-into-your-linux-vm"></a>SSH i din Virtuella Linux-dator
1. Sök efter ditt VM-namn i sökfältet i Azure Portal.
2. Klicka på "Anslut" för att få ditt VM-namn och offentliga IP-adress. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH i din `ssh` virtuella dator med cmd.
   ```
   ssh username@ipaddress
   ```

När du upprättar SSH-anslutningen bör du se Ubuntu välkomstprompt. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Rensa 
1. Avsluta din ssh session.
   ```
   exit
   ```

2. Ta bort resursgruppen och eventuella resurser i den.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Nästa steg
[Läs mer om beständiga filer för Bash i Cloud Shell](persisting-shell-storage.md) <br>
[Läs mer om Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Lär dig mer om lagring av Azure-filer](../storage/files/storage-files-introduction.md) <br>
