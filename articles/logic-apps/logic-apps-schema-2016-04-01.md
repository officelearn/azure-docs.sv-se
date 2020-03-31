---
title: Schemauppdateringar juni-1-2016
description: Uppdaterad schemaversion 2016-06-01 för logikappdefinitioner i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: e2f65f1c52dc7dfb2e4e4bf66f5c7e82f4b802b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792873"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schemauppdateringar för Azure Logic Apps – 1 juni 2016

Den [uppdaterade schema-](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) och API-versionen för Azure Logic Apps innehåller viktiga förbättringar som gör logikappar mer tillförlitliga och enklare att använda:

* [Med scope](#scopes) kan du gruppera eller kapsla åtgärder som en samling åtgärder.
* [Villkor och loopar](#conditions-loops) är nu förstklassiga åtgärder.
* Mer exakt beställning för att `runAfter` köra åtgärder med fastigheten, ersätta`dependsOn`

Om du vill uppgradera logikapparna från förhandsschemat för 1 augusti 2015 till schemat 1 juni 2016 [går du till uppgraderingsavsnittet](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Omfattningar

Det här schemat innehåller scope som gör att du kan gruppera åtgärder tillsammans eller kapsla åtgärder inuti varandra. Ett villkor kan till exempel innehålla ett annat villkor. Läs mer om [scopesyntax](../logic-apps/logic-apps-loops-and-scopes.md)eller granska det här grundläggande scopeexemplet:

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

## <a name="conditions-and-loops-changes"></a>Förhållanden och loopar ändras

I tidigare schemaversioner var villkor och loopar parametrar som var associerade med en enda åtgärd. Det här schemat lyfter den här begränsningen, så villkor och loopar är nu tillgängliga som åtgärdstyper. Läs mer om [loopar och scope,](../logic-apps/logic-apps-loops-and-scopes.md) [villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md)eller granska det här grundläggande exemplet som visar en villkorsåtgärd:

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

## <a name="runafter-property"></a>egenskapen 'runAfter'

Egenskapen `runAfter` ersätter `dependsOn`, vilket ger mer precision när du anger körningsordningen för åtgärder baserat på status för tidigare åtgärder. Egenskapen `dependsOn` angav om "åtgärden kördes och lyckades", baserat på om den tidigare åtgärden lyckades, misslyckades eller som hoppas över - inte hur många gånger du ville köra åtgärden. Egenskapen `runAfter` ger flexibilitet som ett objekt som anger alla åtgärdsnamn som objektet körs efter. Den här egenskapen definierar också en matris med statusar som är godtagbara som utlösare. Om du till exempel vill att en åtgärd ska köras efter att åtgärd A `runAfter` har lyckats och även efter att åtgärd B lyckas eller misslyckas, ställer du in den här egenskapen:

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

Om du vill uppgradera till [det senaste schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)behöver du bara vidta några få steg. Uppgraderingsprocessen inkluderar att köra uppgraderingsskriptet, spara som en ny logikapp och om du vill kan du eventuellt skriva över den tidigare logikappen.

1. Öppna logikappen i Azure-portalen.

2. Gå till **Översikt**. Välj **Uppdatera schema i**verktygsfältet logikapp .
   
   ![Välj Uppdatera schema][1]
   
   Den uppgraderade definitionen returneras, som du kan kopiera och klistra in i en resursdefinition om det behövs. 

   > [!IMPORTANT]
   > *Se till att* du väljer **Spara som** så att alla anslutningsreferenser förblir giltiga i den uppgraderade logikappen.

3. Välj Spara som i verktygsfältet **uppgraderingsblad**.

4. Ange logiknamn och status. Om du vill distribuera den uppgraderade logikappen väljer du **Skapa**.

5. Bekräfta att den uppgraderade logikappen fungerar som förväntat.
   
   > [!NOTE]
   > Om du använder en manuell eller begärandeutlösare ändras motringnings-URL:en i den nya logikappen. Testa den nya webbadressen för att se till att den end-to-end-upplevelsen fungerar. Om du vill bevara tidigare webbadresser kan du klona över din befintliga logikapp.

6. *Valfritt* Om du vill skriva över din tidigare logikapp med den nya schemaversionen väljer du **Klona**i verktygsfältet bredvid **Uppdatera schema**. Det här steget är bara nödvändigt om du vill behålla samma resurs-ID eller begära utlösande URL för logikappen.

## <a name="upgrade-tool-notes"></a>Anteckningar för uppgraderingsverktyg

### <a name="mapping-conditions"></a>Mappningsvillkor

I den uppgraderade definitionen gör verktyget det bästa för att gruppera sanna och falska grenåtgärder tillsammans som ett scope. Specifikt visas designermönstret `@equals(actions('a').status, 'Skipped')` för som `else` en åtgärd. Om verktyget upptäcker oigenkännliga mönster kan verktyget skapa separata villkor för både den sanna och den falska grenen. Du kan mappa om åtgärder efter uppgradering om det behövs.

#### <a name="foreach-loop-with-condition"></a>"foreach" slinga med tillstånd

I det nya schemat kan du använda filteråtgärden för att replikera mönstret som använder en **För varje** slinga med ett villkor per objekt. Ändringen sker dock automatiskt när du uppgraderar. Villkoret blir en filteråtgärd som visas före **för varje** slinga, returnerar endast en matris med objekt som matchar villkoret och skickar matrisen till **För varje** åtgärd. Ett exempel finns i [Loopar och scope](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Resurstaggar

När du har uppgraderat tas resurstaggar bort, så du måste återställa dem för det uppgraderade arbetsflödet.

## <a name="other-changes"></a>Andra ändringar

### <a name="renamed-manual-trigger-to-request-trigger"></a>Omdöpt "manuell" utlösare till "begäran" utlösare

Utlösartypen `manual` var inaktuell och `request` döptes om till med typ `http`. Den här ändringen skapar mer konsekvens för den typ av mönster som utlösaren används för att skapa.

### <a name="new-filter-action"></a>Ny "filter"-åtgärd

Om du vill filtrera en stor matris nedåt `filter` till en mindre uppsättning objekt accepterar den nya typen en matris och ett villkor, utvärderar villkoret för varje objekt och returnerar en matris med objekt som uppfyller villkoret.

### <a name="restrictions-for-foreach-and-until-actions"></a>Begränsningar för "foreach" och "tills" åtgärder

`foreach` Loopen `until` och är begränsad till en enda åtgärd.

### <a name="new-trackedproperties-for-actions"></a>Nya "trackedProperties" för åtgärder

Åtgärder kan nu ha `trackedProperties`en ytterligare egenskap `runAfter` som `type` heter , som är syskon till och egenskaper. Det här objektet anger vissa åtgärdsindata eller utdata som du vill inkludera i Azure Diagnostic telemetri, som skickas ut som en del av ett arbetsflöde. Ett exempel:

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

* [Skapa arbetsflödesdefinitioner för logikappar](../logic-apps/logic-apps-author-definitions.md)
* [Automatisera distributionen av logikappar](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
