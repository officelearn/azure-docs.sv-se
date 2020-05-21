---
title: Snabb start – skapa en Azure Analysis Services server resurs genom att använda Azure Resource Manager mall
description: Snabb start visar hur du Azure Analysis Services server resurser genom att använda en Azure Resource Manager-mall.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: 6f0d5f084c5cd48abf399e351c627d64951ce719
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697441"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Snabb start: skapa en mall för Server Azure Resource Manager

I den här snabb starten beskrivs hur du skapar en Analysis Services server resurs i din Azure-prenumeration med hjälp av en Resource Manager-mall.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Gå till [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) för att skapa ett konto.
* **Azure Active Directory**: Prenumerationen måste vara kopplad till en Azure Active Directory-klientorganisation. Och du måste vara inloggad på Azure med ett konto i den Azure Active Directory. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

## <a name="create-a-server"></a>Skapa en server

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json" range="1-79" highlight="63-78":::

En enkel [Microsoft. AnalysisServices/servers-](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) resurs med en brand Väggs regel definieras i mallen. 

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande distribution till Azure-länk för att logga in på Azure och öppna en mall. Mallen används för att skapa en Analysis Services server resurs och ange obligatoriska och valfria egenskaper.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    Om inget annat anges använder du standardvärdena.

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: Klicka på **Skapa ny**och ange sedan ett unikt namn för den nya resurs gruppen.
    * **Plats**: Välj en standard plats för resurser som skapats i resurs gruppen.
    * **Server namn**: Ange ett namn för Server resursen. 
    * **Plats**: ignorera för Analysis Services. Platsen anges på Server platsen.
    * **Server plats**: Ange Analysis Services serverns plats. Detta är ofta samma region som standard platsen som angetts för resurs gruppen, men krävs inte. Till exempel **norra centrala USA**. Information om regioner som stöds finns i [Analysis Services tillgänglighet per region](analysis-services-overview.md#availability-by-region).
    * **SKU-namn**: Ange SKU-namnet för den Analysis Services-server som ska skapas. Välj mellan: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. SKU-tillgänglighet är beroende av region. S0 eller D1 rekommenderas för utvärdering och testning.
    * **Kapacitet**: Ange det totala antalet instanser av en utskalning av frågans replik. Det finns endast stöd för att skala ut fler än en instans i SELECT-regioner.
    * **Brand Väggs inställningar**: Ange regler för inkommande brand vägg som ska definieras för-servern. Om inget anges inaktive ras brand väggen.
    * **Säkerhetskopiera BLOB container-URI**: Ange SAS-URI: n till en privat Azure Blob Storage-behållare med Läs-, skriv-och list behörigheter. Krävs endast om du avser att använda [säkerhets kopiering/återställning](analysis-services-backup.md).
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När servern har distribuerats visas ett meddelande:

   ![Resource Manager-mall, distribuera Portal meddelande](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Verifiera distributionen

Använd Azure Portal eller Azure PowerShell för att kontrol lera att resurs gruppen och Server resursen har skapats.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du Azure Portal, Azure CLI eller Azure PowerShell för att ta bort resurs gruppen och Server resursen.

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

---

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du en Azure Resource Manager-mall för att skapa en ny resurs grupp och en Azure Analysis Services server-resurs. När du har skapat en server resurs med hjälp av mallen bör du tänka på följande:
- [Snabbstart: Skapa en server – PowerShell](analysis-services-create-powershell.md)
- [Lägga till en exempelmodell från portalen](analysis-services-create-sample-model.md)
- [Konfigurera roller för serveradministratör och användare](tutorials/analysis-services-tutorial-roles.md)
