---
title: Skriptexempel för Azure PowerShell – Återställa en webbapp från en säkerhetskopia i en annan prenumeration | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Återställa en webbapp från en säkerhetskopia i en annan prenumeration
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 7ed593e408b57246ef155ff8e36f054aacb2e063
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291617"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription"></a>Återställa en webbapp från en säkerhetskopia i en annan prenumeration

Det här exempelskriptet hämtar en tidigare slutförd säkerhetskopia från en befintlig webbapp och återställer den till en webbapp i en annan prenumeration. 

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Om du inte behöver webbappen längre kan du använda följande kommando för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) | Lägger till ett autentiserat konto som ska användas för Azure Resource Manager-cmdlet-begäranden.  |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar en webbapp |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Återställer en webbapp från en tidigare slutförd säkerhetskopia. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../app-service-powershell-samples.md).
