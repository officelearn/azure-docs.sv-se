---
title: Azure PowerShell skriptexempel - ta bort en säkerhetskopia för en webbapp | Microsoft Docs
description: Azure PowerShell skriptexempel - ta bort en säkerhetskopia för en webbapp
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f4204cbb4aefe590b87d0a72675823321f280f33
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
ms.locfileid: "23941749"
---
# <a name="delete-a-backup-for-a-web-app"></a>Ta bort en säkerhetskopia för en webbapp

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och skapar sedan en enstaka säkerhetskopia för den. 

Om du vill köra skriptet behöver du en befintlig säkerhetskopia för en webbapp. Om du vill skapa en finns [säkerhetskopiering konfigurera ett webbprogram](app-service-powershell-backup-onetime.md) eller [skapa en schemalagd säkerhetskopiering för en webbapp](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Följande kommando kan användas för att ta bort resursgruppen, webbprogram och alla relaterade resurser efter skriptexempel har körts.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Hämtar en lista över säkerhetskopior för ett webbprogram. |
| [Ta bort AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Tar bort den angivna säkerhetskopian på en webbapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure App Service Web Apps finns i den [Azure PowerShell-exempel](../app-service-powershell-samples.md).
