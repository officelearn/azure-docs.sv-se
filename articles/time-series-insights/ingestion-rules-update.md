---
title: Kommande ändringar i inmatnings-och förenklings reglerna i Azure Time Series Insights Gen2 | Microsoft Docs
description: Ändringar i inmatnings regeln
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: f667ca5ad82182fcf40d5c1fbb325f2ea99a7e08
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495116"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Kommande ändringar av JSON-förenkling och undantags regler för nya miljöer

**Ändringarna tillämpas på *nyligen skapade* Azure Time Series Insights Gen2-miljöer. Dessa ändringar gäller inte för gen1-miljöer.**

Din Azure Time Series Insights Gen2-miljö skapar dynamiskt dina lagrings kolumner genom att följa en viss uppsättning namngivnings konventioner. När en händelse matas in används en uppsättning regler för JSON-nyttolasten och egenskaps namnen. Ändringar av hur JSON-data förenklas och lagras börjar gälla för nya Azure Time Series Insights Gen2-miljöer i juli 2020. Den här ändringen påverkar dig i följande fall:

* Om JSON-nyttolasten innehåller kapslade objekt
*  Om JSON-nyttolasten innehåller matriser
*  Om du använder något av följande fyra specialtecken i ett JSON-egenskaps namn: [\. '
*  Om en eller flera av egenskaperna för ditt TS-ID är i ett kapslat objekt.

Om du skapar en ny miljö och en eller flera av de fall som gäller ovan gäller din händelse nytto Last, ser du dina data som utplattas och lagras på olika sätt. Nedan visas en sammanfattning av ändringarna:

| Aktuell regel | Ny regel | Exempel-JSON | Föregående kolumn namn | Nytt kolumn namn
|---|---| ---| ---|  ---|
| Kapslad JSON förenklas med ett under streck som avgränsare |Kapslad JSON förenklas med en punkt som avgränsare  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Specialtecken undantas inte | JSON-egenskaps namn som innehåller specialtecknen. [\ och "föregås av [" och "]. I ["och"] finns det ytterligare undantag av enkla citat tecken och omvänt snedstreck. Ett enkelt citat sätt skrivs som \' och ett omvänt snedstreck skrivs som\\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| Matriser med primitiver lagras som en sträng | Matriser med primitiva typer lagras som en dynamisk typ  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Objekts mat ris förenklas alltid och genererar flera händelser | Om objekten i en matris inte har något av de TS-ID: n eller tidsstämpeln som är giltiga (får), lagras matrisen med objekt som en dynamisk typ | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Rekommenderade ändringar för nya miljöer

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Om ditt TS-ID och/eller timestamp-egenskapen kapslas i ett objekt:

*  Alla nya distributioner måste matcha de nya inmatnings reglerna. Om ditt TS-ID t. ex. är `telemetry_tagId` måste du uppdatera alla ARM-mallar eller automatiserade distributions skript för att konfigurera `telemetry.tagId` som TS-ID för miljön. Den här ändringen krävs även för tids stämplingar för händelse källan i kapslad JSON.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Om nytto lasten innehåller kapslad JSON eller specialtecken och du automatiserar redigering av [tids serie modell](.\time-series-insights-update-tsm.md) variabel uttryck

*  Uppdatera din klient kod som kör [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput) för att matcha de nya inmatnings reglerna. Till exempel ska ett tidigare [tids serie uttryck](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` uppdateras till ett av följande alternativ:
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`


## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights Gen2-lagring och ingress](./time-series-insights-update-storage-ingress.md).

- Läs mer om hur du frågar dina data med [API: er för Time Series-frågor](./concepts-query-overview.md).

- Läs mer om den [nya syntaxen för Time Series-uttryck](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax).

