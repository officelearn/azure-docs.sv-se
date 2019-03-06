---
title: Flytta Azure-resurser till ny resursgrupp | Microsoft Docs
description: Lär dig hur du flyttar en Azure-resurs från en resursgrupp till en annan resursgrupp eller från en prenumeration till en annan.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5bc8a408ce247ad2980983c3eb32807074af599f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820832"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group"></a>Självstudier: Flytta Azure-resurser till en annan resursgrupp

Lär dig hur du flyttar en Azure-resurs från en resursgrupp till en annan resursgrupp. Du kan också flytta Azure-resurser från en Azure-prenumeration till en annan Azure-prenumeration. I den här självstudien använder du en Resource Manager-mall för att distribuera två resursgrupper och ett lagringskonto. Du flytta lagringskontot från en resursgrupp till en annan.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbered resurserna.
> * Kontrollera att resursen kan flyttas.
> * Checklista för att flytta resurser.
> * Verifiera flyttåtgärden.
> * Flytta resursen.
> * Rensa resurser.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prepare-the-resources"></a>Förbered resurserna

En mall har laddats upp och placerats i ett [delat lagringskonto](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). Mallen definierar två resursgrupper och ett lagringskonto. När du distribuerar mallen kan behöva du ange ett projektnamn. Projektnamnet används för att generera unika resursnamn.  Följande JSON ska extraheras från mallen:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Lägg märke till att de två platser som definierats i json finns i USA, östra och USA, västra. Lagringskontot finns i USA, östra. När du flyttar en resurs till en annan resursgrupp med en annan plats ändras inte åtgärden för att flytta platsen för resursen.

Välj **Try it** (Prova) för att öppna Cloudshell kör sedan PowerShell-skriptet i Cloudshell:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Vänta tills skriptet har slutförts och öppna sedan [Azure-portalen](https://portal.azure.com) och kontrollera att resursgrupperna och lagringskontot har distribuerats som förväntat.

> [!NOTE]
> Eftersom mallen definierar två resursgrupper betraktas den här distributionen som en distribution på prenumerationsnivå. Mallen för distribution stöder inte distributioner på prenumerationsnivå. Därför används Azure PowerShell i den här självstudien. Azure CLI har även stöd för distributioner på prenumerationsnivå. Se [Create resource groups and resources for an Azure subscription](./deploy-to-subscription.md) (Skapa resursgrupper och resurser för en Azure-prenumeration).

## <a name="verify-the-resource-can-be-moved"></a>Kontrollera att resursen kan flyttas

Alla resurser går inte att flytta. Resursen som används i den här självstudien är ett lagringskonto som kan flyttas. Du kontrollerar om en resurs kan flyttas i [Tjänster som kan flyttas](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Checklista för att flytta resurser

Det här steget är valfritt för den här självstudien eftersom det har gjorts.

Några viktiga steg måste utföras innan en resurs flyttas. Se [Checklista för att flytta resurser](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Verifiera flytten

Att verifiera flytten är valfritt för den här självstudien eftersom det har gjorts.

Med åtgärden för att verifiera flytt kan du testa ditt flyttscenario utan att faktiskt flytta resurserna. Använd den här åtgärden för att avgöra om flytten kommer att lyckas. Mer information finns i [Verifiera flytten](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Flytta resursen

Lagringskontot är i källresursgruppen (rg1). Kör följande PowerShell-skript för att flytta resursen till målresursgruppen (rg2). Se till att använda samma projektnamn som du använde när du distribuerade resurser.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Öppna [Azure-portalen](https://portal.azure.com), kontrollera att lagringskontot har flyttats till den andra resursgruppen och kontrollera också att platsen för lagringskontot fortfarande är USA, östra.

När du flyttar resurser är både källgruppen och målgruppen låsta under åtgärden. Skriv- och borttagningsåtgärder blockeras för resursgrupperna tills flytten är klar. Låset innebär att du inte kan lägga till, uppdatera eller ta bort resurser i resursgrupper, men det innebär inte att resurserna är låsta. Om du till exempel flyttar en SQL Server och dess databas till en ny resursgrupp sker inga avbrott för programmet som använder databasen. Det kan fortfarande läsa och skriva till databasen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj namnet på källresursgruppen.  
4. Välj **Ta bort resursgrupp** från menyn längst upp.
5. Välj målet för källresursgruppens namn.  
6. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lär dig att flytta ett lagringskonto från en resursgrupp till en annan resursgrupp. Hittills har du hanterat ett lagringskonto eller flera instanser av ett lagringskonto. I nästa självstudie utvecklar du en mall med flera resurser och flera resurstyper. Några av resurserna har beroende resurser.

> [!div class="nextstepaction"]
> [Skapa beroende resurser](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
