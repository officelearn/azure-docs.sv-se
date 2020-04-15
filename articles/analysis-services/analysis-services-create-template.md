---
title: Snabbstart – Skapa en Serverresurs för Azure Analysis Services med hjälp av Azure Resource Manager-mall
description: Snabbstart som visar hur du använder en Azure Resource Manager-serverresurs med hjälp av en Azure Resource Manager-mall.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384259"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Snabbstart: Skapa en server - Azure Resource Manager-mall

Den här snabbstarten beskriver hur du skapar en Analysis Services-serverresurs i din Azure-prenumeration med hjälp av en Resource Manager-mall.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Gå till [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation. Och du måste vara inloggad på Azure med ett konto i den Azure Active Directory. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

## <a name="create-a-server"></a>Skapa en server

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

En enda [Microsoft.AnalysisServices/servers-resurs](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) med en brandväggsregel definieras i mallen. 

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande distribution till Azure-länk för att logga in på Azure och öppna en mall. Mallen används för att skapa en Analysis Services-serverresurs och ange obligatoriska och valfria egenskaper.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.

    Om inget annat anges använder du standardvärden.

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resursgrupp**: Klicka på **Skapa ny**och ange sedan ett unikt namn för den nya resursgruppen.
    * **Plats**: Välj en standardplats för resurser som skapats i resursgruppen.
    * **Servernamn:** Ange ett namn på serverresursen. 
    * **Plats**: Ignorera för Analystjänster. Plats anges på Serverplats.
    * **Serverplats**: Ange platsen för Analysis Services-servern. Detta är ofta samma region som standardplatsen som angetts för resursgruppen, men krävs inte. Till exempel **Norra centrala USA**. För regioner som stöds finns i [Analysis Services-tillgänglighet efter region](analysis-services-overview.md#availability-by-region).
    * **Sku Namn:** Ange sku-namnet för Analysis Services-servern att skapa. Välj mellan: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Sku-tillgängligheten beror på region. S0 eller D1 rekommenderas för utvärdering och testning.
    * **Kapacitet**: Ange det totala antalet utskalningsinstanser för frågerepliker. Utskalning av mer än en instans stöds endast i vissa regioner.
    * **Brandväggsinställningar:** Ange regler för inkommande brandvägg som ska definieras för servern. Om det inte anges är brandväggen inaktiverad.
    * **Backup Blob Container Uri**: Ange SAS URI till en privat Azure Blob Storage-behållare med läs-, skriv- och listbehörigheter. Krävs endast om du tänker använda [Säkerhetskopiering/återställning](analysis-services-backup.md).
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När servern har distribuerats får du ett meddelande:

   ![Resource Manager-mall, distribuera portalmeddelande](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Verifiera distributionen

Använd Azure-portalen eller Azure PowerShell för att verifiera att resursgruppen och serverresursen har skapats.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda Azure-portalen, Azure CLI eller Azure PowerShell för att ta bort resursgruppen och serverresursen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du en Azure Resource Manager-mall för att skapa en ny resursgrupp och en Azure Analysis Services-serverresurs. När du har skapat en serverresurs med hjälp av mallen bör du tänka på följande:
- [Snabbstart: Skapa en server – PowerShell](analysis-services-create-powershell.md)
- [Lägga till en exempelmodell från portalen](analysis-services-create-sample-model.md)
- [Konfigurera roller för serveradministratör och användare](tutorials/analysis-services-tutorial-roles.md)