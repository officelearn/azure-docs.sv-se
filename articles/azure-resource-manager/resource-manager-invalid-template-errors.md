---
title: Fel med Azure ogiltiga mallar | Microsoft Docs
description: Beskriver hur du löser fel med ogiltiga mallar.
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
ms.openlocfilehash: ee4bce38e0fcde93ba0417617ae90dab2eefda67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061399"
---
# <a name="resolve-errors-for-invalid-template"></a>Åtgärda fel för ogiltig

Den här artikeln beskriver hur du löser fel med ogiltiga mallar.

## <a name="symptom"></a>Symtom

När du distribuerar en mall kan få du fel som indikerar:

```
Code=InvalidTemplate
Message=<varies>
```

Felmeddelandet beror på vilken typ av fel.

## <a name="cause"></a>Orsak

Det här felet kan bero på flera olika typer av fel. De inkluderar vanligtvis ett syntax- eller strukturellt fel i mallen.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Lösning 1 – syntaxfel

Om du får ett felmeddelande som anger att verifieringen av mallen misslyckades kan du ha ett syntax-problem i mallen.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Det här felet är lätt att göra eftersom malluttryck kan vara komplicerade. Till exempel har följande namntilldelning för ett lagringskonto en uppsättning av hakparenteser, tre funktioner, tre uppsättningar med parenteser, en uppsättning enkla citattecken och en egenskap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Om du inte anger den matchande syntaxen, ger ett värde som skiljer sig från din avsikt i mallen.

När du får den här typen av fel kan du noga igenom syntax för uttryck. Överväg att använda en JSON-redigerare såsom [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) eller [Visual Studio Code](resource-manager-vs-code.md), vilket kan varna dig om syntaxfel.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Lösning 2 – felaktiga längder

En annan ogiltig felet uppstår när resursnamnet finns inte i rätt format.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

En rot på resurs måste ha ett mindre segment i namn än i resurstypen. Varje segment differentierade med ett snedstreck. I följande exempel typen har två segment och namnet har ett segment, vilket ger en **giltigt namn**.

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

Ha samma antal segment för underordnade resurser, typ och namn. Det här antalet segment är naturligt eftersom fullständiga namn och typ för underordnat innehåller överordnade namn och typ. Därför har det fullständiga namnet fortfarande ett mindre segment än fullständig typen.

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

Hämta segmenten rätt kan vara svårt med Resource Manager-typer som används för resursprovidrar. Till exempel kräver att använda en resurslås till en webbplats en typ med fyra segment. Namnet är därför tre segment:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Lösning 3 - parametern är inte giltig

Om du anger ett parametervärde som inte är en av de tillåtna värdena visas ett meddelande som liknar följande fel:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Dubbla kontrollera de tillåtna värdena i mallen och ange under distributionen. Mer information om tillåtna parametervärden finns i [Parameters-avsnittet av Azure Resource Manager-mallar](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Lösningen 4 – för många target-resursgrupper

Om du anger fler än fem target-resursgrupper i samma distribution, får du detta felmeddelande. Överväg att konsolidera antalet resursgrupper i din distribution eller distribuera några av mallarna som separata distributioner. Mer information finns i [distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Lösningen 5 – cirkulärt beroende har identifierats

Du får detta felmeddelande när resurser är beroende av varandra på ett sätt som förhindrar distribution från att starta. En kombination av beroenden gör två eller flera resource vänta på andra resurser som också väntar på. Till exempel resource1 beror på resource3 resource2 beror på resource1 och resource3 beror på resource2. Du kan vanligtvis lösa detta problem genom att ta bort onödiga beroenden.

Du kan lösa ett cirkulärt beroende:

1. I din mall för att hitta den resurs som identifieras i cirkulärt beroende. 
2. Resursen måste undersöka den **dependsOn** egenskap och några användningsområden för den **referens** funktionen för att se vilka resurser som den är beroende av. 
3. Granska dessa resurser om du vill se vilka resurser som de förlitar sig på. Följ beroenden tills du ser en resurs som är beroende av den ursprungliga resursen.
5. För resurser som ingår i det cirkulära beroendet noggrant granska all användning av den **dependsOn** egenskapen att identifiera eventuella beroenden som inte behövs. Ta bort dessa beroenden. Om du är osäker på att ett beroende krävs, tar du bort den. 
6. Distribuerar om mallen.

Ta bort värden från den **dependsOn** egenskapen kan orsaka sådana fel när du distribuerar mallen. Lägg till beroende tillbaka till mallen om du får ett felmeddelande. 

Om denna metod inte löser cirkulärt beroende, Överväg att flytta en del av din logik för distribution till underordnade resurser (till exempel tillägg eller konfigurationsinställningar). Konfigurera de underordnade resurserna att distribuera efter att resurserna som ingår i cirkulärt beroende. Anta exempelvis att du distribuerar två virtuella datorer men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. vm2
3. Tillägget på vm1 är beroende av vm1 och vm2. Tillägget anger värden för vm1 får från vm2.
4. Tillägget på vm2 beror på vm1 och vm2. Tillägget anger värden för vm2 blir det från vm1.

Samma metod som fungerar för App Service-appar. Överväg att flytta konfigurationsvärden till en underordnad resurs av app-resurs. Du kan distribuera två webbappar i följande ordning:

1. webapp1
2. webapp2
3. konfigurationen för webapp1 beror på webapp1 och webapp2. Den innehåller appinställningar med värden från webapp2.
4. konfigurationen för webapp2 beror på webapp1 och webapp2. Den innehåller appinställningar med värden från webapp1.
