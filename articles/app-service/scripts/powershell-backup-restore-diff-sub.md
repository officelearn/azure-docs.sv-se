---
title: Skriptexempel för Azure PowerShell – Återställa säkerhetskopiering av app till en annan prenumeration | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Återställa en webbapp från en säkerhetskopia i en annan prenumeration
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 845d47af75495a158e56312126c8e246b43f789f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112783"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Återställa en webbapp från en säkerhetskopia i en annan prenumeration med PowerShell

Det här exempelskriptet hämtar en tidigare slutförd säkerhetskopia från en befintlig webbapp och återställer den till en webbapp i en annan prenumeration. 

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure. 

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Om du inte behöver webbappen längre kan du använda följande kommando för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Add-AzAccount](/powershell/module/az.profile/add-azaccount) | Lägger till ett autentiserat konto som ska användas för Azure Resource Manager-cmdlet-begäranden.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Återställer en webbapp från en tidigare slutförd säkerhetskopia. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
