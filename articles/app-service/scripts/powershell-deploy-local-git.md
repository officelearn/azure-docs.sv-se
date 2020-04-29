---
title: 'PowerShell: Distribuera från lokal git-lagrings platsen'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du distribuerar kod från en lokal git-lagringsplats.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 7355f2730cc54ca027e20c48e989e9f28dac7487
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "74684830"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och distribuerar sedan webbappkoden från en lokal Git-lagringsplats.

Om det behövs uppdaterar du till senaste Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview). Kör sedan `Connect-AzAccount` för att skapa en anslutning med Azure. Dessutom måste din programkod vara allokerad till en lokal Git-lagringsplats.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Obs! |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp med nödvändig resursgrupp och App Service-grupp. När den aktuella katalogen innehåller en Git-lagringsplats kan du också lägga till en fjärr-`azure`. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
