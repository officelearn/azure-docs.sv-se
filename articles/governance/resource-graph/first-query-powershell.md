---
title: Köra din första Resource Graph-fråga med hjälp av Azure PowerShell
description: Den här artikeln vägleder dig igenom stegen för att aktivera Resource Graph-modulen för Azure PowerShell och köra din första fråga.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 6bd7ceaeaf0b064ebbd8f069a4741489349bb980
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427513"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Köra din första Resource Graph-fråga med hjälp av Azure PowerShell

Det första steget till att använda Azure Resource Graph är att kontrollera att modulen för Azure PowerShell är installerad. Denna snabbstart vägleder dig genom processen för att lägga till modulen i Azure PowerShell-installationen.

I slutet av den här processen kommer du att ha lagt till modulen till valfri Azure PowerShell-installation och kört din första Resource Graph-fråga.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="add-the-resource-graph-module"></a>Lägga till Resource Graph-modulen

Om du vill aktivera Azure PowerShell för att skicka frågor till Azure Resource Graph, måste du lägga till modulen. Den här modulen kan användas med lokalt installerade Windows PowerShell och PowerShell Core eller med [Azure PowerShell Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Grundläggande krav

Azure Resource Graph-modulen måste ha följande programvara:

- Azure PowerShell 6.3.0 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-azurerm-ps).

  - För PowerShell Core använder du **Az**-versionen av Azure PowerShell-modulen.

  - För Windows PowerShell använder du **AzureRm**-versionen av Azure PowerShell-modulen.

- PowerShellGet 2.0.1 eller högre. Om den inte är installerad eller uppdaterad följer du [de här instruktionerna](/powershell/gallery/installing-psget).

### <a name="cloud-shell"></a>Cloud Shell

För att lägga till Azure Resource Graph-modulen i Cloud Shell följer du instruktionerna nedan för PowerShell Core.

### <a name="powershell-core"></a>PowerShell Core

Resource Graph-modulen för PowerShell Core är **Az.ResourceGraph**.

1. Från en **administrativ** PowerShell Core-kommandotolk kör du följande kommando:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Verifiera att modulen har importerats och har rätt version (0.3.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Aktivera bakåtkompatibilitetsalias för **Az** till **AzureRm** med följande kommando:

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

Resource Graph-modulen för Windows PowerShell är **AzureRm.ResourceGraph**.

1. Från en **administrativ** PowerShell Core-kommandotolk kör du följande kommando:

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Verifiera att modulen har importerats och har rätt version (0.1.1-förhandsversion):

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Nu när Azure PowerShell-modulen har lagts till i din valda miljö är det dags att testa en enkel Resource Graph-fråga. Frågan returnerar de första fem Azure-resurserna med **namn** och **resurstyp** för varje resurs.

1. Kör din första Azure Resource Graph-fråga med hjälp av cmdlet:et `Search-AzureRmGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera frågan till `order by` egenskapen **namn**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Det begränsar först frågeresultaten och sorterar sedan dem.

1. Uppdatera frågan till att först `order by` **Namn**-egenskapen och sedan sätta en `limit` för de fem främsta resultaten:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

När den sista frågan har körts flera gånger, och förutsatt att ingenting i din miljö ändras, kommer resultaten som returneras bli konsekventa och som förväntade – sorterade efter **Namn**-egenskapen men fortfarande begränsade till de fem främsta resultaten.

## <a name="cleanup"></a>Rensa

Om du vill ta bort Resource Graph-modulen från din Azure PowerShell-miljö, kan du göra det med hjälp av följande kommando:

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Detta tar inte bort modulfilen som laddades ned tidigare. Det tar bara bort den från den körda PowerShell-sessionen.

## <a name="next-steps"></a>Nästa steg

- Få mer information om [frågespråket](./concepts/query-language.md)
- Lär dig att [utforska resurser](./concepts/explore-resources.md)
- Kör din första fråga med [Azure CLI](first-query-azurecli.md)
- Se exempel på [startfrågor](./samples/starter.md)
- Se exempel på [avancerade frågor](./samples/advanced.md)
- Ge feedback på [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)