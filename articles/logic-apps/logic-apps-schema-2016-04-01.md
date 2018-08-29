---
title: Schemat uppdateras juni 1 2016 – Azure Logic Apps | Microsoft Docs
description: Uppdaterade schemaversion 2016-06-01 för logikappsdefinitioner i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 43fd52dd04e679b9756c07e8c6e260323469026a
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126210"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schemauppdateringar för Azure Logic Apps – 1 juni 2016

Den [uppdateras schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) och API-version för Azure Logic Apps innehåller viktiga förbättringar som gör logikappar mer tillförlitlig och enklare att använda:

* [Scope](#scopes) låter dig kapsla åtgärder som en samling av åtgärder.
* [Villkor och slingor](#conditions-loops) är nu förstklassig åtgärder.
* Mer exakt ordning för att köra åtgärder med den `runAfter` egenskapen ersätta `dependsOn`

Uppgradera dina logic apps från den 1 augusti 2015 preview schemat till den 1 juni 2016-schemat [Kolla in avsnittet Uppgradera](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Omfattningar

Det här schemat innehåller scope, som låter dig grupp tillsammans eller kapslade åtgärder i varandra. Ett villkor kan exempelvis innehålla ytterligare ett villkor. Läs mer om [omfång syntax](../logic-apps/logic-apps-loops-and-scopes.md), eller gå igenom det här exemplet grundläggande omfång:

```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Villkor och slingor ändringar

I föregående schemat var versioner, villkor och loopar du parametrar som är associerade med en enda åtgärd. Det här schemat hissar denna begränsning, så villkor och slingor visas nu som Åtgärdstyper. Läs mer om [loopar och scope](../logic-apps/logic-apps-loops-and-scopes.md), eller gå igenom det här grundläggande exemplet för en åtgärd för villkoret:

```
{
    "If_trigger_is_some-trigger": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'some-trigger')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_another-trigger": "..."
        }      
    }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>Egenskapen 'runAfter'

Den `runAfter` egenskapen ersätter `dependsOn`, tillhandahåller mer precision när du anger den kör ordningen för åtgärder baserat på status för tidigare åtgärder.

Den `dependsOn` egenskapen var synonyma med ”åtgärden kördes och lyckades”, oavsett hur många gånger som du vill köra en åtgärd baserat på om den första åtgärden lyckades, misslyckades eller hoppades över. Den `runAfter` egenskapen ger den flexibiliteten som ett objekt som anger alla åtgärdsnamn efter objektet körs. Den här egenskapen definierar också en matris med statusar som accepteras som utlösare. Till exempel om du vill ska köras efter steget A lyckas och även efter steg B lyckas eller misslyckas kan du skapa det här `runAfter` egenskapen:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uppgradera ditt schema

Uppgradera till den [senaste schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), du behöver bara vidta några åtgärder. Uppgraderingsprocessen innehåller kör uppgraderingsskriptet, sparar som en ny logikapp och du kan eventuellt skriver över tidigare logikappen.

1. Öppna logikappen i Azure-portalen.

2. Gå till **översikt**. I verktygsfältet logic app välja **uppdatera Schema**.
   
    ![Välj Uppdatera Schema][1]
   
    Uppgraderade definitionen returneras som du kan kopiera och klistra in i en resursdefinition om det behövs. 
    Men vi **rekommenderar** du väljer **Spara som** att se till att alla anslutningsreferenser är giltiga i den uppgraderade logikappen.

3. I verktygsfältet uppgradera bladet väljer **Spara som**.

4. Ange den logik namn och status. Om du vill distribuera uppgraderade logikappen, Välj **skapa**.

5. Bekräfta att logikappen uppgraderade fungerar som förväntat.
   
   > [!NOTE]
   > Om du använder en manuell eller begäran-utlösare kan ändras Motringnings-URL i den nya logikappen. Testa den nya URL: en för att se till att slutpunkt till slutpunkt-upplevelsen fungerar. För att bevara tidigare URL: er, kan du klona över din befintliga logikapp.

6. *Valfritt* om du vill skriva över tidigare logikappen med den nya schemaversionen i verktygsfältet väljer **klona**, bredvid **uppdatera Schema**. Det här steget krävs bara om du vill behålla samma resurs-ID eller begära utlösaren URL: en för din logikapp.

### <a name="upgrade-tool-notes"></a>Uppgradering av verktyget

#### <a name="mapping-conditions"></a>Mappningsvillkoren

I definitionen av uppgraderade gör verktyget en yttersta för att gruppera true och false gren åtgärder som omfattning. Mer specifikt designer mönstret för `@equals(actions('a').status, 'Skipped')` ska visas som en `else` åtgärd. Om verktyget identifierar okänt mönster, kan verktyget Skapa olika villkor för både true och false gren. Du kan mappa om åtgärder efter uppgraderingen, om det behövs.

#### <a name="foreach-loop-with-condition"></a>”foreach”-loop med villkor

I det nya schemat kan du använda Filtreringsåtgärden för att replikera mönstret för en `foreach` -loop med ett villkor per artikel, men den här ändringen automatiskt ska hända när du uppgraderar. Villkoret blir en filteråtgärd innan foreach-loop för att returnera en matris med objekt som matchar villkoret och matrisen skickas till foreach-åtgärder. Ett exempel finns i [loopar och scope](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Resurstaggar

När du har uppgraderat tas resurstaggar bort, så du måste återställa dem för det uppgraderade arbetsflödet.

## <a name="other-changes"></a>Andra ändringar

### <a name="renamed-manual-trigger-to-request-trigger"></a>Omdöpt ”manuellt” utlösare på-begäransutlösare

Den `manual` Utlösartyp var inaktuellt och bytt namn till `request` med typen `http`. Den här förändringen skapar mer konsekvens för typen av mönster som utlösaren används för att skapa.

### <a name="new-filter-action"></a>Ny ”filtrera”-åtgärd

Filtrera en stor matris till en mindre uppsättning objekt, den nya `filter` typ accepterar en matris och ett villkor, utvärderar villkoret för varje objekt och returnerar en matris med objekt som uppfyller villkoret.

### <a name="restrictions-for-foreach-and-until-actions"></a>Begränsningar för ”foreach” och ”till”-åtgärder

Den `foreach` och `until` loop är begränsade till en enda åtgärd.

### <a name="new-trackedproperties-for-actions"></a>Ny ”trackedProperties” för åtgärder

Åtgärder kan nu ha en annan egenskap som kallas `trackedProperties`, vilket är på samma nivå till den `runAfter` och `type` egenskaper. Det här objektet anger vissa åtgärdens indata eller utdata som du vill ska ingå i Azure Diagnostics telemetri, som en del av ett arbetsflöde. Exempel:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg
* [Skapa arbetsflödesdefinitioner för logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Skapa mallar för distribution för logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
