---
title: Bash i snabbstarten för Azure Cloud Shell | Microsoft Docs
description: Snabbstart för Bash i Cloudshell
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 1b6419feef96b57bafd0831531bd8cfa56142cc4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089156"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Snabbstart för Bash i Azure Cloudshell

Det här dokumentet beskriver hur du använder Bash i Azure Cloud Shell i den [Azure-portalen](https://ms.portal.azure.com/).

> [!NOTE]
> En [PowerShell i Azure Cloud Shell](quickstart-powershell.md) Snabbstart finns också.

## <a name="start-cloud-shell"></a>Starta Cloudshell
1. Starta **Cloud Shell** från det övre navigeringsfältet i Azure Portal. <br>
![](media/quickstart/shell-icon.png)

2. Välj en prenumeration för att skapa ett lagringskonto och dela Microsoft Azure Files.
3. Välj ”Skapa storage”

> [!TIP]
> Du autentiseras automatiskt för Azure CLI i varje session.

### <a name="select-the-bash-environment"></a>Välj Bash-miljön
Kontrollera att miljön listrutan till vänster shell-fönstret säger `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ange din prenumeration
1. Lista över prenumerationer som du har åtkomst till.
   ```azurecli-interactive
   az account list
   ```

2. Ställ in din önskade prenumeration: <br>
   ```azurecli-interactive
   az account set --subscription my-subscription-name`
   ```

> [!TIP]
> Din prenumeration kommer att kommas ihåg för framtida sessioner med hjälp av `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en ny resursgrupp i WestUS med namnet ”MyRG”.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Skapa en virtuell Linux-dator
Skapa en Ubuntu-VM i din nya resursgrupp. Azure CLI skapar SSH-nycklar och konfigurera den virtuella datorn med dem. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Med hjälp av `--generate-ssh-keys` instruerar Azure CLI för att skapa och konfigurera offentliga och privata nycklar i den virtuella datorn och `$Home` directory. Som standard nycklar är placerade i Cloud Shell på `/home/<user>/.ssh/id_rsa` och `/home/<user>/.ssh/id_rsa.pub`. Din `.ssh` mappen sparas i dina anslutna filresurs 5 GB avbildning som används för att bevara `$Home`.

Ditt användarnamn på den här virtuella datorn kommer att ditt användarnamn som används i Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH till den virtuella Linux-datorn
1. Sök efter namnet på din virtuella dator i Azure portal sökfältet.
2. Klicka på ”Anslut” för att få dina virtuella datorns namn och en offentlig IP-adress. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH till den virtuella datorn med den `ssh` cmd.
   ```
   ssh username@ipaddress
   ```

Du bör se Ubuntu Välkommen prompten vid upprätta SSH-anslutningen. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Rensa 
1. Avsluta ditt ssh session.
   ```azurecli-interactive
   exit
   ```

2. Ta bort resursgruppen och alla resurser i den.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Nästa steg
[Läs mer om bevara filer för Bash i Cloud Shell](persisting-shell-storage.md) <br>
[Lär dig mer om Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Lär dig mer om Azure File storage](../storage/files/storage-files-introduction.md) <br>
