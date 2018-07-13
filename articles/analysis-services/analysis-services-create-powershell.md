---
title: Snabbstart – Skapa en Azure Analysis Services-server med hjälp av PowerShell | Microsoft Docs
description: Lär dig hur du skapar en Azure Analysis Services-server med PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9149f15d0503b9a39ac67d9c3f6fc1ddde7e03bd
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952774"
---
# <a name="quickstart-create-a-server---powershell"></a>Snabbstart: Skapa en server – PowerShell

I den här snabbstarten beskrivs hur du använder PowerShell från kommandoraden för att skapa en Azure Analysis Services-server i din Azure-prenumeration.

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Gå till [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
- **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation och du måste ha ett konto i den katalogen. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).
- **Azure PowerShell-modul version 4.0 eller senare**. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Information om att installera och uppgradera finns i [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="import-azurermanalysisservices-module"></a>Importera AzureRm.AnalysisServices modul

Använd komponentmodulen [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) när du skapar en server i prenumerationen. Läs in AzureRm.AnalysisServices-modulen i PowerShell-sessionen.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. När du skapar servern måste du ange en resursgrupp i prenumerationen. Om du inte redan har en resursgrupp, skapa en med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i regionen västra USA.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Skapa en server

Skapa en ny server med kommandot [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). I följande exempel skapas en server med namnet myServer i myResourceGroup, i regionen västra USA på nivå D1 (kostnadsfritt) och med philipc@adventureworks.com som serveradministratör.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort servern från prenumerationen med kommandot [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Ta inte bort servern om du fortsätter med andra snabbstartsguider och självstudier i den här samlingen. I följande exempel tar vi bort servern som skapades i föregående steg.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en server i Azure-prenumerationen med hjälp av PowerShell. Nu när du har en server kan du skydda den genom att konfigurera en serverbrandvägg (valfritt). Du kan även lägga till en grundläggande exempeldatamodell till servern direkt från portalen. Att använda en exempelmodell är en bra idé om du vill lära dig mer om hur man konfigurerar modelldatabasroller och testar klientanslutningar. Fortsätt till och lägg till en exempelmodell om du vill lära dig mer.

> [!div class="nextstepaction"]
> [Snabbstart: Konfigurera serverbrandvägg – portalen](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Självstudier: Lägg till en exempelmodell till servern](analysis-services-create-sample-model.md)