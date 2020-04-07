---
title: Importera och exportera ritningar med PowerShell
description: Lär dig hur du arbetar med dina skissdefinitioner som kod. Dela, källkontroll och hantera dem med hjälp av export- och importkommandona.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: 98bd21aad944346a17d8bdce7fb74c0eb8be2ed7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677141"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importera och exportera skissdefinitioner med PowerShell

Azure Blueprints kan hanteras fullständigt via Azure-portalen. När organisationer avancerar i sin användning av Azure Blueprints bör de börja tänka på skissdefinitioner som hanterad kod. Detta begrepp kallas ofta infrastruktur som kod (IaC). Att behandla dina skissdefinitioner som kod ger ytterligare fördelar utöver vad Azure Portal erbjuder. Dessa fördelar inkluderar:

- Dela ritningsdefinitioner
- Säkerhetskopiera dina ritningsdefinitioner
- Återanvända skissdefinitioner i olika klienter eller prenumerationer
- Placera ritningsdefinitionerna i källkontrollen
  - Automatiserad testning av skissdefinitioner i testmiljöer
  - Stöd för kontinuerlig integrering och kontinuerlig utbyggnad (CI/CD) rörledningar

Oavsett dina skäl, hantera din skiss definitioner som kod har fördelar. Den här artikeln visar `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` hur du använder kommandona och i modulen [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter en måttlig arbetskunskap om Azure Blueprints. Om du inte har gjort det ännu kan du gå igenom följande artiklar:

- [Skapa en skiss i portalen](../create-blueprint-portal.md)
- Läs om [distributionsfaser](../concepts/deployment-stages.md) och [skisslivscykeln](../concepts/lifecycle.md)
- [Skapa](../create-blueprint-powershell.md) och [hantera](./manage-assignments-ps.md) skissdefinitioner och tilldelningar med PowerShell

Om den inte redan är installerad följer du instruktionerna i [Lägg till az.blueprint-modulen](./manage-assignments-ps.md#add-the-azblueprint-module) för att installera och validera **Az.Blueprint-modulen** från PowerShell-galleriet.

## <a name="folder-structure-of-a-blueprint-definition"></a>Mappstruktur för en skissdefinition

Innan du tittar på att exportera och importera ritningar, låt oss titta på hur de filer som utgör skissdefinitionen är strukturerade. En skissdefinition bör lagras i en egen mapp.

> [!IMPORTANT]
> Om inget värde skickas till parametern **Name** för `Import-AzBlueprintWithArtifact` cmdleten används namnet på mappen som skissdefinitionen lagras i.

Tillsammans med skissdefinitionen, som `blueprint.json`måste namnges, är de artefakter som skissdefinitionen består av. Varje artefakt måste finnas `artifacts`i undermappen .
Tillsammans bör strukturen för din skiss definition som JSON filer i mappar ser ut så här:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Exportera ritningsdefinitionen

Stegen för att exportera ritningsdefinitionen är enkla. Exportera skissdefinitionen kan vara användbart för delning, säkerhetskopiering eller placering i källkontroll.

- **Skiss** [krävs]
  - Anger skissdefinitionen
  - Används `Get-AzBlueprint` för att hämta referensobjektet
- **OutputPath** [obligatoriskt]
  - Anger sökvägen för att spara JSON-filerna för skissdefinitionen
  - Utdatafilerna är i en undermapp med namnet på skissdefinitionen
- **Version** (valfritt)
  - Anger den version som **Blueprint** ska matas ut om skissreferensobjektet innehåller referenser till mer än en version.

1. Hämta en referens till skissdefinitionen för att `{subId}`exportera från prenumerationen representerad som :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Använd `Export-AzBlueprintWithArtifact` cmdlet för att exportera den angivna skissdefinitionen:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importera ritningsdefinitionen

När du har antingen en [exporterad skissdefinition](#export-your-blueprint-definition) eller har en manuellt skapad skissdefinition i den [mappstruktur som krävs](#folder-structure-of-a-blueprint-definition)kan du importera skissdefinitionen till en annan hanteringsgrupp eller prenumeration.

Exempel på inbyggda skissdefinitioner finns i [Azure Blueprint GitHub repo](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Namn** [obligatoriskt]
  - Anger namnet på den nya skissdefinitionen
- **InputPath** [obligatoriskt]
  - Anger sökvägen för att skapa skissdefinitionen från
  - Måste matcha den [mappstruktur som krävs](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (valfritt)
  - Hanteringsgrupp-ID för att spara skissdefinitionen till om inte den aktuella kontexten är standard
  - Antingen **ManagementGroupId** eller **SubscriptionId** måste anges
- **SubscriptionId** (valfritt)
  - Prenumerations-ID för att spara skissdefinitionen till om inte den aktuella kontexten standard
  - Antingen **ManagementGroupId** eller **SubscriptionId** måste anges

1. Använd `Import-AzBlueprintWithArtifact` cmdlet för att importera den angivna skissdefinitionen:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

När skissdefinitionen har importerats [tilldelar du den med PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Information om hur du skapar avancerade skissdefinitioner finns i följande artiklar:

- Använd [statiska och dynamiska parametrar](../concepts/parameters.md).
- Anpassa [ordningsföljningen för skisssekvensering](../concepts/sequencing-order.md).
- Skydda distributioner med [skissresurslåsning](../concepts/resource-locking.md).
- [Hantera ritningar som kod](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [skisslivscykeln](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../concepts/resource-locking.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).