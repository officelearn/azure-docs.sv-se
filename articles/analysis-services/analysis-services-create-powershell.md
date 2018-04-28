---
title: Skapa en Azure Analysis Services-server med hjälp av PowerShell | Microsoft Docs
description: Lär dig hur du skapar en Azure Analysis Services-server med PowerShell
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3f0d3ae6786e9f63f0e4eb025118d0d217eced64
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Skapa en Azure Analysis Services-server med PowerShell

I den här snabbstarten beskrivs hur du använder PowerShell från kommandoraden för att skapa en Azure Analysis Services-server i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i Azure-prenumerationen.

Den här uppgiften kräver Azure PowerShell-modul version 4.0 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Information om att installera och uppgradera finns i [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

> [!NOTE]
> Att skapa en server kan resultera i en ny fakturerbar tjänst. Läs mer i [Priser för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="before-you-begin"></a>Innan du börjar
Följande krävs för att slutföra den här snabbstarten:

* **Azure-prenumeration**: Gå till [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation och du måste ha ett konto i den katalogen. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Importera AzureRm.AnalysisServices modul
Använd komponentmodulen [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) när du skapar en server i prenumerationen. Läs in AzureRm.AnalysisServices-modulen i PowerShell-sessionen.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med hjälp av den [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) kommando. Följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. När du skapar servern måste du ange en resursgrupp i prenumerationen. Om du inte redan har en resursgrupp, skapa en med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i regionen västra USA.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>Skapa en server

Skapa en ny server med kommandot [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). I följande exempel skapas en server med namnet myServer i myResourceGroup, i regionen västra USA på nivå D1 och med philipc@adventureworks.com som serveradministratör.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort servern från prenumerationen med kommandot [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Ta inte bort servern om du fortsätter med andra snabbstartsguider och självstudier i den här samlingen. I följande exempel tar vi bort servern som skapades i föregående steg.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg
[Hantera Azure Analysis Services med PowerShell](analysis-services-powershell.md)
[distribuera en modell från SSDT](analysis-services-deploy.md)
[skapa en modell i Azure-portalen](analysis-services-create-model-portal.md)
