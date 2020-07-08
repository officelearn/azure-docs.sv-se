---
title: Kommande ändringar i inmatnings-och förenklings reglerna i Azure Time Series Insights | Microsoft Docs
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
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919041"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Kommande ändringar av JSON-förenkling och undantags regler för nya miljöer

Dessa ändringar tillämpas endast på *nya* Azure Time Series Insights PAYG-miljöer (betalar per användning). Dessa ändringar gäller inte för standard SKU-miljöer.

Din Azure Time Series Insights-miljö skapar dynamiskt dina lagrings kolumner genom att följa en viss uppsättning namngivnings konventioner. När en händelse matas in används en uppsättning regler för JSON-nyttolasten och egenskaps namnen. Ändringar av hur JSON-data förenklas och lagras börjar gälla för nya Azure Time Series Insights "betala per användning" i juli 2020. Den här ändringen påverkar dig i följande fall:

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

*  Uppdatera din klient kod som kör [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) för att matcha de nya inmatnings reglerna. Till exempel ska ett tidigare [tids serie uttryck](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` uppdateras till ett av följande alternativ:
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>Nästa steg

- Läs [lägga till stöd för lång data typ](./time-series-insights-long-data-type.md).

- Läs [Azure Time Series Insights för hands versions lagring och inkommande](./time-series-insights-update-storage-ingress.md)trafik.

