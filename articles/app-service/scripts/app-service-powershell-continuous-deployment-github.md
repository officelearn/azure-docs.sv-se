---
title: "Azure PowerShell-skript Sample - skapa en webbapp med kontinuerlig distribution från GitHub | Microsoft Docs"
description: "Azure PowerShell-skript Sample - skapa en webbapp med kontinuerlig distribution från GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f0574c8c549c45682206624ffe38a4a3919cb1c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Skapa en webbapp med kontinuerlig distribution från GitHub

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och sedan konfigurerar kontinuerlig distribution från en GitHub-databas. GitHub distribution utan kontinuerlig distribution finns [skapa en webbapp och distribuera kod från GitHub](app-service-powershell-deploy-github.md).

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview), och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure. Se också till att:

- En anslutning med Azure har skapats med hjälp av den `az login` kommando.
- Programkoden är i ett offentligt eller privat GitHub-databas som du äger.
- Du har [skapas en åtkomst-token i GitHub-konto](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Följande kommando kan användas för att ta bort resursgruppen, webbprogram och alla relaterade resurser efter skriptexempel har körts.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Ny AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Skapar en App Service-plan. |
| [Ny AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar ett webbprogram. |
| [Ange AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Ändrar en resurs i en resursgrupp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure App Service Web Apps finns i den [Azure PowerShell-exempel](../app-service-powershell-samples.md).
