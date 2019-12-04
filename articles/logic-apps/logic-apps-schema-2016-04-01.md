---
title: Schema uppdateringar juni – 1-2016
description: Uppdaterad schema version 2016-06-01 för Logic app-definitioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792873"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schema uppdateringar för Azure Logic Apps – 1 juni 2016

Den [uppdaterade schema](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) -och API-versionen för Azure Logic Apps innehåller viktiga förbättringar som gör Logic Apps mer pålitliga och enklare att använda:

* Med [omfattningar](#scopes) kan du gruppera eller kapsla åtgärder som en mängd åtgärder.
* [Villkor och slingor](#conditions-loops) är nu de första klass åtgärderna.
* Mer exakt ordning för att köra åtgärder med egenskapen `runAfter`, ersätter `dependsOn`

Om du vill uppgradera dina Logi Kap par från den 1 augusti 2015-förhands gransknings schema till den 1 juni 2016 schemat, [Se avsnittet Uppgradera](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Omfattningar

Det här schemat innehåller omfång, som gör att du kan gruppera åtgärder tillsammans eller kapsla åtgärder i varandra. Ett villkor kan till exempel innehålla ett annat villkor. Läs mer om [syntax för omfattningar](../logic-apps/logic-apps-loops-and-scopes.md), eller Läs detta exempel på grundläggande omfattning:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
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

## <a name="conditions-and-loops-changes"></a>Villkor och slingor ändras

I tidigare schema versioner var villkor och slingor kopplade till en enda åtgärd. Det här schemat lyfter den här begränsningen, så villkor och slingor är nu tillgängliga som åtgärds typer. Lär dig mer om [slingor och omfattningar](../logic-apps/logic-apps-loops-and-scopes.md), [villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md)eller Läs det här grundläggande exemplet som visar en villkors åtgärd:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>egenskapen ' runAfter '

Egenskapen `runAfter` ersätter `dependsOn`, vilket ger mer precision när du anger körnings ordningen för åtgärder baserat på status för tidigare åtgärder. Egenskapen `dependsOn` anger om åtgärden kördes och lyckades, baserat på om föregående åtgärd lyckades, misslyckades eller hoppades över – inte antalet gånger som du ville köra åtgärden. Egenskapen `runAfter` ger flexibilitet som ett objekt som anger alla åtgärds namn som objektet körs efter. Den här egenskapen definierar också en matris med statusar som är godtagbara som utlösare. Om du till exempel vill att en åtgärd ska köras efter att åtgärden lyckades och även efter att åtgärd B lyckas eller Miss lyckas, ställer du in den här `runAfter` egenskapen:

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uppgradera ditt schema

Om du vill uppgradera till det [senaste schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)behöver du bara utföra några steg. Uppgraderings processen omfattar att köra uppgraderings skriptet, Spara som en ny Logic app, och om du vill kan det bero på att den tidigare Logic-appen skrivs över.

1. Öppna din Logic app i Azure Portal.

2. Gå till **Översikt**. I verktygsfältet Logic app väljer du **Uppdatera schema**.
   
   ![Välj Uppdatera schema][1]
   
   Den uppgraderade definitionen returneras, som du kan kopiera och klistra in i en resurs definition om det behövs. 

   > [!IMPORTANT]
   > *Se till att* du väljer **Spara** så att alla anslutnings referenser fortfarande är giltiga i den uppgraderade Logic-appen.

3. I verktygsfältet uppgradera blad väljer du **Spara som**.

4. Ange logikens namn och status. Om du vill distribuera din uppgraderade Logic-app väljer du **skapa**.

5. Bekräfta att den uppgraderade Logic-appen fungerar som förväntat.
   
   > [!NOTE]
   > Om du använder en manuell eller begär ande utlösare ändras återanrops-URL: en för den nya Logic-appen. Testa den nya URL: en för att se till att slut punkt till slut punkt fungerar. För att bevara tidigare URL: er kan du klona över din befintliga Logic-app.

6. *Valfritt* Om du vill skriva över din tidigare Logic-app med den nya schema versionen väljer du **klona**bredvid **uppdaterings schema**i verktygsfältet. Det här steget är bara nödvändigt om du vill behålla samma resurs-ID eller begär Utlösar-URL för din Logic app.

## <a name="upgrade-tool-notes"></a>Information om uppgraderings verktyget

### <a name="mapping-conditions"></a>Mappnings villkor

I den uppgraderade definitionen gör verktyget det bästa arbetet vid gruppering av sant och falskt gren åtgärder tillsammans som ett omfång. Mer specifikt visas design mönstret för `@equals(actions('a').status, 'Skipped')` som en `else` åtgärd. Men om verktyget identifierar okända mönster kan verktyget skapa separata villkor för både True och false-grenen. Du kan mappa om åtgärder efter uppgraderingen, om det behövs.

#### <a name="foreach-loop-with-condition"></a>"förgrunds"-loop med villkor

I det nya schemat kan du använda filter åtgärden för att replikera mönstret som använder en **for each** -loop med ett villkor per objekt. Ändringen sker dock automatiskt när du uppgraderar. Villkoret blir en filter åtgärd som visas före **för varje** slinga, och som endast returnerar en matris med objekt som matchar villkoret och skickar matrisen till **för varje** åtgärd. Ett exempel finns i [slingor och omfång](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Resurstaggar

När du har uppgraderat tas resurs taggarna bort, så du måste återställa dem för det uppgraderade arbets flödet.

## <a name="other-changes"></a>Andra ändringar

### <a name="renamed-manual-trigger-to-request-trigger"></a>Omdöpt till "Request"-utlösaren

Typen av utlösare för `manual` är inaktuell och har fått ett nytt namn till `request` med typen `http`. Den här ändringen skapar mer konsekvens för den typ av mönster som utlösaren används för att skapa.

### <a name="new-filter-action"></a>Ny filter åtgärd

Om du vill filtrera en stor matris nedåt till en mindre uppsättning objekt, accepterar den nya `filter` typen en matris och ett villkor, utvärderar villkoret för varje objekt och returnerar en matris med objekt som uppfyller villkoret.

### <a name="restrictions-for-foreach-and-until-actions"></a>Begränsningar för åtgärderna "förgrunds åtgärder" och "till"

Loopen `foreach` och `until` är begränsad till en enda åtgärd.

### <a name="new-trackedproperties-for-actions"></a>New ' trackedProperties ' för åtgärder

Åtgärder kan nu ha ytterligare en egenskap med namnet `trackedProperties`, som är på samma nivå som `runAfter` och `type` egenskaper. Det här objektet anger vissa åtgärds indata eller utdata som du vill ta med i Azure Diagnostic-telemetri som genereras som en del av ett arbets flöde. Exempel:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
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

* [Skapa arbets flödes definitioner för Logic Apps](../logic-apps/logic-apps-author-definitions.md)
* [Automatisera Logic app-distribution](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
