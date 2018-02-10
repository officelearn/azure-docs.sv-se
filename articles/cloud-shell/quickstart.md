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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 69431979769a03b62a7f9fd7760e6eb614e37cd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Snabbstart för Bash i Azure-molnet Shell

Det här dokumentet beskriver hur du använder Bash i Azure Cloud-gränssnittet i den [Azure-portalen](https://ms.portal.azure.com/).

> [!NOTE]
> En [PowerShell Azure Cloud Shell](quickstart-powershell.md) Quickstart är också tillgänglig.

## <a name="start-cloud-shell"></a>Starta molnet Shell
1. Starta **moln Shell** från det övre navigeringsfältet i Azure-portalen <br>
![](media/quickstart/shell-icon.png)
2. Välj en prenumeration för att skapa ett lagringskonto och dela filer som Microsoft Azure
3. Välj ”Skapa lagring”

> [!TIP]
> Du autentiseras automatiskt i varje sesssion för Azure CLI 2.0.

### <a name="select-the-bash-environment"></a>Välj Bash-miljö
1. Välj den miljö som nedrullningsbara från den till vänster i fönstret shell <br>
![](media/quickstart/env-selector.png)
2. Välj Bash

### <a name="set-your-subscription"></a>Ange din prenumeration
1. Lista över prenumerationer som du har åtkomst till: <br>
`az account list`
2. Ange din önskade prenumeration: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> Prenumerationen kommer att sparas för framtida sessioner med hjälp av `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en ny resursgrupp i WestUS med namnet ”MyRG”: <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Skapa en virtuell Linux-dator
Skapa en Ubuntu VM i din nya resursgrupp. Azure CLI 2.0 skapar SSH-nycklar och konfigurera den virtuella datorn med dem. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Offentliga och privata nycklar som används för att autentisera den virtuella datorn placeras i `/home/<user>/.ssh/id_rsa` och `/home/<user>/.ssh/id_rsa.pub` av Azure CLI 2.0 som standard. .Ssh-mappen är kvar i din bifogade filer för Azure-resursens 5 GB avbildningen.

Ditt användarnamn på den här virtuella datorn kommer att ditt användarnamn som används i molnet Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH till den virtuella Linux-datorn
1. Sök efter ditt namn på virtuell dator i Azure portal sökfältet
2. Klicka på ”Anslut” och kör:`ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

Du bör se Ubuntu Välkommen prompt vid upprätta SSH-anslutningen. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Rensa 
Ta bort resursgruppen och alla resurser i den: <br>
Kör `az group delete -n MyRG`

## <a name="next-steps"></a>Nästa steg
[Läs mer om spara filer för Bash i molnet Shell](persisting-shell-storage.md) <br>
[Lär dig mer om Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Lär dig mer om Azure File storage](../storage/files/storage-files-introduction.md) <br>
