---
title: Fel ogiltig Azure-mall | Microsoft Docs
description: "Beskriver hur du löser fel med ogiltig mall."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-invalid-template"></a>Åtgärda fel för ogiltig mall

Den här artikeln beskriver hur du löser fel med ogiltig mall.

## <a name="symptom"></a>Symtom

När du distribuerar en mall, visas ett felmeddelande som anger:

```
Code=InvalidTemplate
Message=<varies>
```

Felmeddelandet beror på vilken typ av fel.

## <a name="cause"></a>Orsak

Det här felet kan bero på flera olika typer av fel. De omfattar vanligtvis ett syntax eller strukturella fel i mallen.

## <a name="solution"></a>Lösning

### <a name="solution-1---syntax-error"></a>Lösning 1 - syntaxfel

Om du får ett felmeddelande som anger att valideringen av mallen misslyckades kanske syntax problem i mallen.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Det här felet är lätt att göra eftersom malluttryck kan vara komplicerade. Till exempel innehåller följande namntilldelning för ett lagringskonto en uppsättning av hakparenteser, tre funktioner, tre uppsättningar av parenteser, en uppsättning av enkla citattecken och en egenskap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Om du inte anger matchande syntaxen genererar mallen ett värde som skiljer sig från din avsikt.

När du får den här typen av fel, granska noggrant syntax för uttryck. Överväg att använda en JSON-redigerare som [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) eller [Visual Studio Code](resource-manager-vs-code.md), vilket kan varna dig om syntaxfel.

### <a name="solution-2---incorrect-segment-lengths"></a>Lösning 2 - felaktiga längder

En annan ogiltig mall-fel uppstår när resursnamnet inte är i rätt format.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

En nivå rotresurs måste ha ett mindre segment i namn än i resurstypen. Varje segment differentierade med ett snedstreck. I följande exempel har två segment och namnet har ett segment så att den är en **giltigt namn**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Men i nästa exempel är **inte ett giltigt namn** eftersom den har samma antal segment som typen.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Ha samma antal segment för underordnade resurser, typ och namn. Det här antalet segment är meningsfullt eftersom fullständiga namn och typ för underordnat inkluderar överordnade namn och typ. Det fullständiga namnet måste därför fortfarande ett mindre segment än fullständig typen.

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

Hämta segment rätt kan vara svårt med Resource Manager-typer som tillämpas på resursleverantörer. Till exempel kräver tillämpa ett resurslås på en webbplats en typ med fyra segment. Namnet är därför tre segment:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>Lösning 3 - parametern är inte giltig

Om mallen anger tillåtna värden för en parameter och du anger ett värde som inte är ett av dessa värden, visas ett meddelande som liknar följande fel:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Dubbla kontrollera de tillåtna värdena i mallen och ange en under distributionen.

### <a name="solution-4---circular-dependency-detected"></a>Lösning 4 - cirkelberoende upptäcktes

Du får detta felmeddelande när resurser beroende av varandra på ett sätt som förhindrar distribution från att starta. En kombination av beroenden gör två eller flera resurs vänta tills andra resurser som väntar på också. Till exempel resource1 beror på resource3 resource2 beror på resource1 och resource3 beror på resource2. Vanligtvis kan du lösa problemet genom att ta bort onödiga beroenden.
