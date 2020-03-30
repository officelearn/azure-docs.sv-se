---
title: Ogiltiga mallfel
description: Beskriver hur du löser ogiltiga mallfel vid distribution av Azure Resource Manager-mallar.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 65cd69d67933d117b51f37b587b276aec2bd635a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154065"
---
# <a name="resolve-errors-for-invalid-template"></a>Åtgärda fel med en ogiltig mall

I den här artikeln beskrivs hur du löser ogiltiga mallfel.

## <a name="symptom"></a>Symptom

När du distribuerar en mall visas ett felmeddelande som anger:

```
Code=InvalidTemplate
Message=<varies>
```

Felmeddelandet beror på typen av fel.

## <a name="cause"></a>Orsak

Det här felet kan bero på flera olika typer av fel. De innebär vanligtvis en syntax eller ett strukturellt fel i mallen.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Lösning 1 - syntaxfel

Om du får ett felmeddelande som anger att mallen misslyckades med valideringen kan du ha ett syntaxproblem i mallen.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Det här felet är enkelt att göra eftersom malluttryck kan vara invecklade. Följande namntilldelning för ett lagringskonto har till exempel en uppsättning parenteser, tre funktioner, tre uppsättningar parenteser, en uppsättning enstaka offerter och en egenskap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Om du inte anger den matchande syntaxen ger mallen ett värde som skiljer sig från din avsikt.

När du får den här typen av fel granskar du noggrant uttryckssyntaxen. Överväg att använda en JSON-redigerare som [Visual Studio](create-visual-studio-deployment-project.md) eller Visual [Studio Code](use-vs-code-to-create-template.md), som kan varna dig om syntaxfel.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Lösning 2 - felaktiga segmentlängder

Ett annat ogiltigt mallfel uppstår när resursnamnet inte är i rätt format.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

En rotnivåresurs måste ha ett mindre segment i namnet än i resurstypen. Varje segment differentieras med ett snedstreck. I följande exempel har typen två segment och namnet har ett segment, så det är ett **giltigt namn**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Men nästa exempel är **inte ett giltigt namn** eftersom det har samma antal segment som typen.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

För underordnade resurser har typen och namnet samma antal segment. Det här antalet segment är meningsfullt eftersom det fullständiga namnet och typen för det underordnade inkluderar det överordnade namnet och typen. Därför har det fullständiga namnet fortfarande ett mindre segment än den fullständiga typen.

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

Det kan vara svårt att få rätt segment med Resource Manager-typer som tillämpas över resursleverantörer. Om du till exempel använder ett resurslås på en webbplats krävs en typ med fyra segment. Därför är namnet tre segment:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Lösning 3 - parametern är ogiltig

Om du anger ett parametervärde som inte är ett av de tillåtna värdena visas ett meddelande som liknar följande fel:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Dubbelkolla de tillåtna värdena i mallen och ange en under distributionen. Mer information om tillåtna parametervärden finns i [avsnittet Parametrar i Azure Resource Manager-mallar](template-syntax.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Lösning 4 - För många målgrupper

Om du anger fler än fem målresursgrupper i en enda distribution visas det här felet. Överväg att antingen konsolidera antalet resursgrupper i distributionen eller distribuera några av mallarna som separata distributioner. Mer information finns i [Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Lösning 5 - cirkulärt beroende har upptäckts

Det här felet visas när resurser är beroende av varandra på ett sätt som förhindrar att distributionen startar. En kombination av ömsesidigt beroenden gör att två eller flera resurser väntar på andra resurser som också väntar. Resurs1 är till exempel beroende av resurs3, resurs2 är beroende av resurs1 och resurs3 är beroende av resurs2. Du kan vanligtvis lösa det här problemet genom att ta bort onödiga beroenden.

Så här löser du ett cirkulärt beroende:

1. Leta reda på den resurs som identifierats i det cirkulära beroendet i mallen.
2. För den resursen undersöker du egenskapen **dependsOn** och eventuell användning av **referensfunktionen** för att se vilka resurser den är beroende av.
3. Undersök dessa resurser för att se vilka resurser de är beroende av. Följ beroendena tills du märker en resurs som är beroende av den ursprungliga resursen.
5. För de resurser som ingår i det cirkulära beroendet undersöker du noggrant all användning av egenskapen **dependsOn** för att identifiera eventuella beroenden som inte behövs. Ta bort dessa beroenden. Om du är osäker på om ett beroende behövs kan du prova att ta bort det.
6. Distribuera om mallen.

Om du tar bort värden från egenskapen **dependsOn** kan det orsaka fel när du distribuerar mallen. Om du får ett felmeddelande lägger du till beroendet i mallen igen.

Om den metoden inte löser det cirkulära beroendet bör du överväga att flytta en del av distributionslogiken till underordnade resurser (till exempel tillägg eller konfigurationsinställningar). Konfigurera dessa underordnade resurser för att distribuera efter de resurser som ingår i det cirkulära beroendet. Anta till exempel att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1 (vm1)
2. vm2 (vm2)
3. Tillägg på vm1 beror på vm1 och vm2. Tillägget anger värden på vm1 som det får från vm2.
4. Tillägg på VM2 beror på vm1 och vm2. Tillägget anger värden på vm2 som det får från vm1.

Samma metod fungerar för App Service-appar. Överväg att flytta konfigurationsvärden till en underordnad resurs för appresursen. Du kan distribuera två webbappar i följande ordning:

1. webapp1 (webbapp1)
2. webapp2 (på nytt)
3. config för webapp1 beror på webapp1 och webapp2. Den innehåller appinställningar med värden från webapp2.
4. config för webapp2 beror på webapp1 och webapp2. Den innehåller appinställningar med värden från webapp1.
