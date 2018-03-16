---
title: Bash i Azure-molnet Shell Quickstart | Microsoft Docs
description: "Snabbstart för Bash i molnet Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: e48c54216c5c4ae8e53d4802aafce8883ee97c11
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Snabbstart för Bash i Azure-molnet Shell

Det här dokumentet beskriver hur du använder Bash i Azure Cloud-gränssnittet i den [Azure-portalen](https://ms.portal.azure.com/).

> [!NOTE]
> En [PowerShell Azure Cloud Shell](quickstart-powershell.md) Quickstart är också tillgänglig.

## <a name="start-cloud-shell"></a>Starta molnet Shell
1. Starta **moln Shell** från det övre navigeringsfältet i Azure-portalen. <br>
![](media/quickstart/shell-icon.png)

2. Välj en prenumeration för att skapa ett lagringskonto och dela Microsoft Azure-filer.
3. Välj ”Skapa lagring”

> [!TIP]
> Du autentiseras automatiskt för Azure CLI 2.0 i varje session.

### <a name="select-the-bash-environment"></a>Välj Bash-miljö
Kontrollera att miljön nedrullningsbara från den vänstra sidan av fönstret shell `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Ange din prenumeration
1. Lista över prenumerationer som du har åtkomst till.
```azurecli-interactive
az account list
```

2. Ange din önskade prenumeration: <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> Prenumerationen kommer att sparas för framtida sessioner med hjälp av `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en ny resursgrupp i WestUS med namnet ”MyRG”.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Skapa en virtuell Linux-dator
Skapa en Ubuntu VM i din nya resursgrupp. Azure CLI 2.0 skapar SSH-nycklar och konfigurera den virtuella datorn med dem. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Med hjälp av `--generate-ssh-keys` instruerar Azure CLI 2.0 för att skapa och konfigurera offentliga och privata nycklar i den virtuella datorn och `$Home` directory. Som standard nycklarna är placerade i molnet Shell på `/home/<user>/.ssh/id_rsa` och `/home/<user>/.ssh/id_rsa.pub`. Din `.ssh` mappen kvar i din bifogade filresurs 5 GB bild som används för att bevara `$Home`.

Ditt användarnamn på den här virtuella datorn kommer att ditt användarnamn som används i molnet Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH till den virtuella Linux-datorn
1. Sök efter ditt namn på virtuell dator i Azure portal sökfältet.
2. Klicka på ”Anslut” för att få ditt VM-namn och en offentlig IP-adress. <br>
![](media/quickstart/sshcmd-copy.png)

3. SSH till den virtuella datorn med den `ssh` cmd.
```
ssh username@ipaddress
```

Du bör se Ubuntu Välkommen prompt vid upprätta SSH-anslutningen. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Rensa 
1. Avsluta din ssh session.
```azurecli-interactive
exit
```

2. Ta bort resursgruppen och alla resurser i den.
```azurecli-interactive
Run `az group delete -n MyRG`
```

## <a name="next-steps"></a>Nästa steg
[Läs mer om spara filer för Bash i molnet Shell](persisting-shell-storage.md) <br>
[Lär dig mer om Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Lär dig mer om Azure File storage](../storage/files/storage-files-introduction.md) <br>
