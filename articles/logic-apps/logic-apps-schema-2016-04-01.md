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
ms.openlocfilehash: 6df29543df2b7b2609582f7e8dd9a0629182760c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995833"
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

## <a name="conditions-and-loops-changes"></a>Villkor och slingor ändringar

I föregående schemat var versioner, villkor och loopar du parametrar som är associerade med en enda åtgärd. Det här schemat hissar denna begränsning, så villkor och slingor är nu tillgängligt som Åtgärdstyper. Läs mer om [loopar och scope](../logic-apps/logic-apps-loops-and-scopes.md), [villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md), eller gå igenom det här grundläggande exemplet som visar en condition-åtgärd:

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

## <a name="runafter-property"></a>Egenskapen 'runAfter'

Den `runAfter` egenskapen ersätter `dependsOn`, tillhandahåller mer precision när du anger den kör ordningen för åtgärder baserat på status för tidigare åtgärder. Den `dependsOn` egenskapen anges om ”åtgärden kördes och lyckades”, baserat på om den första åtgärden lyckades, misslyckades, eller som hoppades över - inte antalet gånger som du vill köra instruktionen. Den `runAfter` egenskapen tillhandahåller flexibilitet som ett objekt som anger åtgärden som har namn efter som objektet körs. Den här egenskapen definierar också en matris med statusar som accepteras som utlösare. Till exempel om du vill att en åtgärd som ska köras när åtgärden A fungerar och även efter åtgärden B lyckas eller misslyckas, ställa in den här `runAfter` egenskapen:

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

Uppgradera till den [senaste schemat](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), du behöver bara vidta några åtgärder. Uppgraderingsprocessen innehåller kör uppgraderingsskriptet, sparar som en ny logikapp och du kan eventuellt skriver över tidigare logikappen.

1. Öppna logikappen i Azure-portalen.

2. Gå till **översikt**. I verktygsfältet logic app välja **uppdatera Schema**.
   
   ![Välj Uppdatera Schema][1]
   
   Uppgraderade definitionen returneras som du kan kopiera och klistra in i en resursdefinition om det behövs. 

   > [!IMPORTANT]
   > *Se till att* du väljer **Spara som** så att alla anslutningsreferenser fortfarande är giltiga för den uppgraderade logikappen.

3. I verktygsfältet uppgradera bladet väljer **Spara som**.

4. Ange den logik namn och status. Om du vill distribuera uppgraderade logikappen, Välj **skapa**.

5. Bekräfta att logikappen uppgraderade fungerar som förväntat.
   
   > [!NOTE]
   > Om du använder en manuell eller begäran-utlösare kan ändras Motringnings-URL i den nya logikappen. Testa den nya URL: en för att se till att slutpunkt till slutpunkt-upplevelsen fungerar. För att bevara tidigare URL: er, kan du klona över din befintliga logikapp.

6. *Valfritt* om du vill skriva över tidigare logikappen med den nya schemaversionen i verktygsfältet väljer **klona**, bredvid **uppdatera Schema**. Det här steget krävs bara om du vill behålla samma resurs-ID eller begära utlösaren URL: en för din logikapp.

## <a name="upgrade-tool-notes"></a>Uppgradering av verktyget

### <a name="mapping-conditions"></a>Mappningsvillkoren

I definitionen av uppgraderade gör verktyget yttersta för att gruppera true och false gren åtgärder som omfattning. Mer specifikt designer mönstret för `@equals(actions('a').status, 'Skipped')` visas som en `else` åtgärd. Om verktyget identifierar okänt mönster, kan verktyget Skapa olika villkor för både true och false gren. Du kan mappa om åtgärder efter uppgraderingen, om det behövs.

#### <a name="foreach-loop-with-condition"></a>”foreach”-loop med villkor

I det nya schemat kan du använda Filtreringsåtgärden för att replikera mönstret som använder en **för var och en** -loop med ett villkor per artikel. Ändringen sker dock automatiskt när du uppgraderar. Villkoret blir en filteråtgärd som visas före den **för var och en** loop, returnerar en matris med objekt som matchar villkoret och för att skicka den matrisen **för var och en** åtgärd. Ett exempel finns i [loopar och scope](../logic-apps/logic-apps-loops-and-scopes.md).

### <a name="resource-tags"></a>Resurstaggar

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
* [Skapa arbetsflödesdefinitioner för logic apps](../logic-apps/logic-apps-author-definitions.md)
* [Skapa mallar för distribution för logic apps](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
