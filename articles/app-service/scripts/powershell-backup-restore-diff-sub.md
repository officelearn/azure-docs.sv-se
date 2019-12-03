---
title: 'PowerShell: återställa säkerhets kopian till en annan prenumeration'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du återställer en säkerhets kopia i en annan prenumeration.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8168fbedd1a639dce0ab8bd076e14ab24e2f6d1e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685826"
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
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Lägger till ett autentiserat konto som ska användas för Azure Resource Manager-cmdlet-begäranden.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Återställer en webbapp från en tidigare slutförd säkerhetskopia. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
