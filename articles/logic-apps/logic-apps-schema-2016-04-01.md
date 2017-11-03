---
title: Schemat uppdateras juni 1 2016 - Azure Logic Apps | Microsoft Docs
description: "Skapa JSON-definitioner för Logic Apps i Azure med schemaversionen 2016-06-01"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/25/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 43df04d6478e44c82c88b17d916cfc9fe4afc03e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schema-uppdateringar för Logikappar i Azure - 1 juni 2016

Den här nya schemat och API-version för Logikappar i Azure innehåller viktiga förbättringar som gör logikappar mer tillförlitlig och enklare att använda:

* [Scope](#scopes) låter dig kapsla åtgärder som en samling åtgärder.
* [Villkor och slingor](#conditions-loops) är nu förstklassigt åtgärder.
* Mer exakta ordning för åtgärder med körs den `runAfter` egenskapen ersätta`dependsOn`

Att uppgradera dina logic apps från 1 augusti 2015 preview schemat till den 1 juni 2016-schemat [kolla avsnittet Uppgradera](##upgrade-your-schema).

<a name="scopes"></a>
## <a name="scopes"></a>Scope

Det här schemat innehåller scope, så att du kan gruppera tillsammans eller kapslade åtgärder i varandra. Ett villkor kan exempelvis innehålla ytterligare villkor. Lär dig mer om [omfång syntax](../logic-apps/logic-apps-loops-and-scopes.md), eller läsa det här exemplet grundläggande omfång:

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

I tidigare schemat har versioner, villkor och slingor parametrar som är associerade med en enda åtgärd. Det här schemat lyfter denna begränsning så villkor och slingor visas nu som Åtgärdstyper. Lär dig mer om [slingor och scope](../logic-apps/logic-apps-loops-and-scopes.md), eller läsa det här enkla exemplet för en åtgärd som villkoret:

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

Den `runAfter` egenskapen ersätter `dependsOn`, ger högre precision när du anger kör ordning för åtgärder baserat på status för tidigare åtgärder.

Den `dependsOn` egenskapen var synonym med ”åtgärden kördes och lyckades”, oavsett hur många gånger som du vill utföra en åtgärd, baserat på om den föregående åtgärden lyckades, misslyckades eller hoppas över. Den `runAfter` egenskapen ger den flexibiliteten som ett objekt som anger alla åtgärdsnamn efter vilken objektet körs. Den här egenskapen definierar också en matris med statuslägen som är godkända som utlösare. Till exempel om du vill köra när steget A lyckas och även efter steg B lyckas eller misslyckas kan du skapa detta `runAfter` egenskapen:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uppgradera schemat

Uppgradera till det nya schemat tar bara några steg. Uppgraderingsprocessen innehåller kör uppgraderingsskriptet, spara i en ny logikapp och du kan eventuellt att skriva över tidigare logikappen.

1. Öppna logikappen i Azure-portalen.

2. Gå till **översikt**. I verktygsfältet logik app välja **uppdatera Schema**.
   
    ![Välj Uppdatera Schema][1]
   
    Definitionen för uppgraderade returneras som du kan kopiera och klistra in i en resursdefinition om det behövs. 
    Men vi **rekommenderar** du väljer **Spara som** att se till att alla referenser för anslutningen är giltig i uppgraderade logikappen.

3. I verktygsfältet uppgradera bladet väljer **Spara som**.

4. Ange logiken namn och status. För att distribuera din uppgraderade logikapp, Välj **skapa**.

5. Bekräfta att logikappen uppgraderade fungerar som förväntat.
   
   > [!NOTE]
   > Om du använder en manuell eller begäran utlösare ändrar återanrop URL i den nya logikappen. Testa den nya URL Om du vill kontrollera slutpunkt till slutpunkt experience fungerar. För att bevara tidigare URL: er, kan du klona via din befintliga logikapp.

6. *Valfria* om du vill skriva över tidigare logikappen med den nya schemaversionen i verktygsfältet väljer **klona**, bredvid **uppdatera Schema**. Det här steget krävs endast om du vill behålla samma resurs-ID eller begära utlösaren URL för din logikapp.

### <a name="upgrade-tool-notes"></a>Uppgradering av verktyget

#### <a name="mapping-conditions"></a>Mappningsvillkoren

I den uppgraderade definitionen gör verktyget bästa prestanda på Gruppera true och false gren åtgärder som omfattning. Mer specifikt designer mönstret för `@equals(actions('a').status, 'Skipped')` ska visas som en `else` åtgärd. Om verktyget upptäcker inte kan tolkas mönster, kan verktyget Skapa olika villkor för både på true och false grenen. Du kan mappa om åtgärder efter uppgraderingen, om det behövs.

#### <a name="foreach-loop-with-condition"></a>'foreach-loop med villkoret

I det nya schemat kan du använda filteråtgärd för att replikera mönstret för en `foreach` med ett villkor per artikel, men den här ändringen ska ske automatiskt när du uppgraderar. Villkoret blir en filteråtgärd innan foreach-slinga för att returnera en matris med objekt som matchar villkoret och den matrisen skickades till foreach-åtgärd. Ett exempel finns [slingor och scope](../logic-apps/logic-apps-loops-and-scopes.md).

#### <a name="resource-tags"></a>Resurstaggar

När du har uppgraderat tas resurstaggar bort, så måste du återställa dem för det uppgraderade arbetsflödet.

## <a name="other-changes"></a>Andra ändringar

### <a name="renamed-manual-trigger-to-request-trigger"></a>Har bytt namn till ”manuell” utlösaren till ”begäran” utlösare

Den `manual` utlösartypen var föråldrad och bytt namn till `request` med typen `http`. Den här ändringen skapar mer konsekvens för typ av mönster som utlösaren används för att skapa.

### <a name="new-filter-action"></a>Ny ”filter”-åtgärd

Filtrera en stor matris till en mindre uppsättning objekt som den nya `filter` accepterar en matris och ett villkor, utvärderar villkor för varje objekt och returnerar en matris med objekt som uppfyller villkoret.

### <a name="restrictions-for-foreach-and-until-actions"></a>Begränsningar för 'foreach ”och” till ”-åtgärder

Den `foreach` och `until` loop är begränsade till en enda åtgärd.

### <a name="new-trackedproperties-for-actions"></a>Ny 'trackedProperties' för åtgärder

Åtgärder kan nu använda en annan egenskap som kallas `trackedProperties`, som är på samma nivå i `runAfter` och `type` egenskaper. Det här objektet anger vissa åtgärd indata eller utdata som du vill inkludera i Azure diagnostisk telemetri, orsakat som en del av ett arbetsflöde. Exempel:

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
