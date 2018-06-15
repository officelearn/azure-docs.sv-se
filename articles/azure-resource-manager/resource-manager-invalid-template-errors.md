---
title: Fel ogiltig Azure-mall | Microsoft Docs
description: Beskriver hur du löser fel med ogiltig mall.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 59f07b9ba8116cb1a4b5ab50382d89d01a78853b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357698"
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

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Lösning 1 - syntaxfel

Om du får ett felmeddelande som anger att valideringen av mallen misslyckades kanske syntax problem i mallen.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Det här felet är lätt att göra eftersom malluttryck kan vara komplicerade. Till exempel har följande namntilldelning för ett lagringskonto en uppsättning av hakparenteser, tre funktioner, tre uppsättningar av parenteser, en uppsättning av enkla citattecken och en egenskap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Om du inte anger matchande syntaxen genererar mallen ett värde som skiljer sig från din avsikt.

När du får den här typen av fel, granska noggrant syntax för uttryck. Överväg att använda en JSON-redigerare som [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) eller [Visual Studio Code](resource-manager-vs-code.md), vilket kan varna dig om syntaxfel.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Lösning 2 - felaktiga längder

En annan ogiltig mall-fel uppstår när resursnamnet är inte i rätt format.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

En nivå rotresurs måste ha ett mindre segment i namn än i resurstypen. Varje segment differentierade med ett snedstreck. I följande exempel har två segment och namnet har ett segment så att den har en **giltigt namn**.

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

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Lösning 3 - parametern är inte giltig

Om du anger ett parametervärde som inte är ett av de tillåtna värdena får ett meddelande som liknar följande fel:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Dubbla kontrollera de tillåtna värdena i mallen och ange en under distributionen. Läs mer om tillåtna parametervärden [parametrar avsnitt i Azure Resource Manager-mallar](resource-manager-templates-parameters.md).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Lösning 4 - för många target-resursgrupper

Om du anger fler än fem target-resursgrupper i en enkel distribution, får du detta felmeddelande. Överväg att konsolidera antalet resursgrupper i din distribution eller distribuera några av mallarna som separata distributioner. Mer information finns i [distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Lösning 5 - cirkelberoende upptäcktes

Du får detta felmeddelande när resurser beroende av varandra på ett sätt som förhindrar distribution från att starta. En kombination av beroenden gör två eller flera resurs vänta tills andra resurser som väntar på också. Till exempel resource1 beror på resource3 resource2 beror på resource1 och resource3 beror på resource2. Vanligtvis kan du lösa problemet genom att ta bort onödiga beroenden.

Du löser ett cirkulärt beroende:

1. Hitta den resurs som identifieras i cirkulärt beroende i mallen. 
2. Resursen måste undersöka den **dependsOn** egenskap och några användningsområden för den **referens** funktion för att se vilka resurser som den är beroende av. 
3. Granska dessa resurser om du vill se vilka resurser de beroende av. Följ beroenden tills du ser en resurs som är beroende av den ursprungliga resursen.
5. För resurserna som ingår i cirkulärt beroende noggrant undersöka all användning av den **dependsOn** egenskapen för att identifiera eventuella beroenden som inte behövs. Ta bort dessa beroenden. Om du inte vet att ett beroende behövs tar du bort den. 
6. Distribuera mallen.

Ta bort värden från den **dependsOn** egenskapen kan orsaka fel när du distribuerar mallen. Om du får ett felmeddelande, lägger du till beroende tillbaka till mallen. 

Om den metoden inte löser cirkulärt beroende, Överväg att flytta en del av logik för distribution till underordnade resurser (till exempel tillägg eller konfigurationsinställningar). Konfigurera de underordnade resurserna att distribuera efter resurser som är inblandade i cirkulärt beroende. Anta exempelvis att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. vm2
3. Tillägg på vm1 beror på vm1 och vm2. Tillägget anger värden för vm1 får från vm2.
4. Tillägg på vm2 beror på vm1 och vm2. Tillägget anger värden för vm2 får från vm1.

Samma metod fungerar för Apptjänst-appar. Överväg att flytta konfigurationsvärden till en underordnad resurs för app-resurs. Du kan distribuera två web apps i följande ordning:

1. webapp1
2. webapp2
3. konfigurationen för webapp1 beror på webapp1 och webapp2. Den innehåller app-inställningar med värden från webapp2.
4. konfigurationen för webapp2 beror på webapp1 och webapp2. Den innehåller app-inställningar med värden från webapp1.
