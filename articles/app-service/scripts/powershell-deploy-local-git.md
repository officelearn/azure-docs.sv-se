---
title: Skriptexempel för Azure PowerShell – Skapa en app och distribuera från en lokal Git-lagringsplats | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Skapa en webbapp och distribuera kod från en lokal Git-lagringsplats
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8103777b85d8e11416811c694103c58755f1a23a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114441"
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

| Kommando | Anteckningar |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp med nödvändig resursgrupp och App Service-grupp. När den aktuella katalogen innehåller en Git-lagringsplats kan du också lägga till en fjärr-`azure`. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
