---
title: "Gränser och -konfiguration – Azure Logic Apps | Microsoft Docs"
description: "Tjänsten gränser och konfigurationsvärden för Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 22d0ee242d18d73d1d5825567fd61638fd22cc68
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-limits-and-configuration"></a>Logic Apps gränser och konfiguration

Det här avsnittet beskrivs de aktuella gränser och konfigurationsinformation för Logikappar i Azure.

## <a name="limits"></a>Begränsningar

### <a name="http-request-limits"></a>HTTP-begäran gränser

Dessa begränsningar gäller för en enskild HTTP-begäran eller ett anrop för anslutningen.

#### <a name="timeout"></a>Timeout

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Timeout för begäran | 120 sekunder | En [asynk.mönster](../logic-apps/logic-apps-create-api-app.md) eller [tills loop](logic-apps-loops-and-scopes.md) kan kompensera efter behov |
|||| 

#### <a name="message-size"></a>Meddelandestorlek

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Meddelandestorlek | 100 MB | Vissa kopplingar och API: er stöder inte 100 MB. | 
| Gränsen för utvärdering av uttryck | 131,072 tecken | `@concat()`, `@base64()`, `string` får inte vara längre än den här gränsen. | 
|||| 

#### <a name="retry-policy"></a>Återförsöksprincip

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Antal återförsök | 90 | Standardvärdet är 4. Du kan konfigurera med den [försök parametern](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Högsta antal försök | 1 dag | Du kan konfigurera med den [försök parametern](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Min tid innan nytt försök | 5 SEK. | Du kan konfigurera med den [försök parametern](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Kör varaktighet och lagring

Dessa begränsningar gäller för en enkel logikapp som körs.

| Namn | Gräns | 
| ---- | ----- | 
| Kör varaktighet | 90 dagar | 
| Kvarhållning av lagring | 90 dagar från kör starttid | 
| Min upprepningsintervallet | 1 sekund </br>För logic apps med en App Service-Plan: 15 sekunder | 
| Max upprepningsintervallet | 500 dagar | 
||| 

Att överskrida för kör varaktighet eller lagring kvarhållning i flödet för normala bearbetningen [kontaktar du oss](mailto://logicappsemail@microsoft.com) så att vi kan hjälpa dig med dina krav.

### <a name="looping-and-debatching-limits"></a>Slingor och debatching gränser

Dessa begränsningar gäller för en enkel logikapp som körs.

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| ForEach-objekt | 100,000 | Du kan använda den [fråga åtgärd](../connectors/connectors-native-query.md) att filtrera större matriser efter behov. | 
| Tills iterationer | 5,000 | | 
| SplitOn objekt | 100,000 | | 
| ForEach parallellitet | 50 | Standardvärdet är 20. <p>Att ställa in en viss nivå av parallellitet i en ForEach-loop i `runtimeConfiguration` egenskap i den `foreach` åtgärd. <p>Om du vill köra en ForEach-loop sekventiellt, den `operationOptions` egenskapen till ”sekventiella” i den `foreach` åtgärd. | 
|||| 

### <a name="throughput-limits"></a>Genomströmning gränser

Dessa begränsningar gäller för en enskild logik app-instansen.

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Åtgärder körningar per 5 minuter | 100,000 | Kan fördela belastningen över flera appar efter behov. | 
| Åtgärder samtidiga utgående anrop | ~2,500 | Minska antalet samtidiga begäranden eller minska tidsåtgången efter behov. | 
| Runtime-slutpunkten: samtidiga inkommande samtal | ~1,000 | Minska antalet samtidiga begäranden eller minska tidsåtgången efter behov. | 
| Runtime-slutpunkten: läsa anrop per 5 minuter | 60,000 | Kan fördela belastningen över flera appar efter behov. | 
| Runtime-slutpunkten: anropa anrop per 5 minuter | 45,000 | Kan fördela belastningen över flera appar efter behov. | 
|||| 

Överskrider gränserna i normala bearbetningen, eller kör belastningen testning som eventuellt överskrider gränserna, [kontaktar du oss](mailto://logicappsemail@microsoft.com) så att vi kan hjälpa dig med dina krav.

### <a name="logic-app-definition-limits"></a>Logik app definition gränser

Dessa begränsningar gäller för en enskild logik app definition.

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Åtgärder per arbetsflöde | 500 | Du kan lägga till inkapslade arbetsflöden som behövs för att utöka den här gränsen. |
| Tillåtna åtgärden inkapslingsdjup | 8 | Du kan lägga till inkapslade arbetsflöden som behövs för att utöka den här gränsen. | 
| Arbetsflöden per region per prenumeration | 1000 | | 
| Utlösare per arbetsflöde | 10 | | 
| Växeln scope fall gränsen | 25 | | 
| Antalet variabler per arbetsflöde | 250 | | 
| Maximalt antal tecken per uttryck | 8 192 | | 
| Max `trackedProperties` storlek i antal tecken | 16,000 | 
| `action`/`trigger`gräns för | 80 | | 
| `description`Maxlängden | 256 | | 
| `parameters`gränsen | 50 | | 
| `outputs`gränsen | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Anpassad koppling gränser

Dessa begränsningar gäller för anpassade kopplingar som du kan skapa från web API: er.

| Namn | Gräns | 
| ---- | ----- | 
| Antal anpassade anslutningsappar som du kan skapa | 1 000 per Azure-prenumeration | 
| Antal begäranden per minut för varje anslutning som skapats av en anpassad anslutningsapp | 500 begäranden för varje anslutning som skapats av anslutningsappen |
||| 

### <a name="integration-account-limits"></a>Gränser för integrering

Dessa begränsningar gäller för artefakter som du kan lägga till ett konto för integrering.

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Schema | 8 MB | Du kan använda [blob-URI](../logic-apps/logic-apps-enterprise-integration-schemas.md) att överföra filer som är större än 2 MB. | 
| Karta (XSLT-fil) | 2 MB | | 
| Runtime-slutpunkten: läsa anrop per 5 minuter | 60,000 | Distribuera arbetsbelastningen över flera konton efter behov. | 
| Runtime-slutpunkten: anropa anrop per 5 minuter | 45,000 | Distribuera arbetsbelastningen över flera konton efter behov. | 
| Runtime-slutpunkten: spåra anrop per 5 minuter | 45,000 | Distribuera arbetsbelastningen över flera konton efter behov. | 
| Runtime-slutpunkten: blockerar samtidiga anrop | ~1,000 | Minska antalet samtidiga begäranden eller minska tidsåtgången efter behov. | 
|||| 

Dessa begränsningar gäller för antalet artefakter som du kan lägga till ett konto för integrering.

#### <a name="free-pricing-tier"></a>Gratis prisnivån

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Avtal | 10 | | 
| Andra typer av artefakt | 25 |Artefakt typer är partners, scheman, certifikat och kartor. Varje typ kan ha upp till maximalt antal artefakter. | 
|||| 

#### <a name="standard-pricing-tier"></a>Standard prisnivå

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Alla typer av artefakt | 500 | Artefakt typer är avtal, partner, scheman, certifikat och kartor. Varje typ kan ha upp till maximalt antal artefakter. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Meddelandestorlek B2B-protokoll (AS2 X12 EDIFACT)

Dessa begränsningar gäller för B2B-protokollen.

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Gäller för att avkoda och koda | 
| X12 | 50 MB | Gäller för att avkoda och koda | 
| EDIFACT | 50 MB | Gäller för att avkoda och koda | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfiguration: IP-adresser

### <a name="logic-apps-service"></a>Logic Apps service

Anrop som en logikapp direkt gör, det vill säga via [HTTP](../connectors/connectors-native-http.md) eller [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) eller andra HTTP-begäranden som kommer från IP-adresser i den här listan.

|Logic Apps region|Utgående IP|
|-----------------|-----------|
|Östra Australien|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Sydöstra Australien|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Södra Brasilien|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Centrala Kanada|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Östra Kanada|52.232.128.155, 52.229.120.45, 52.229.126.25|
|Indien, centrala|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Centrala USA|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Östasien|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Östra USA|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Östra USA 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Östra Japan|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Västra Japan|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Norra centrala USA|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Norra Europa|40.113.12.95, 52.178.165.215, 52.178.166.21|
|Södra centrala USA|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Sydostasien|13.76.133.155, 52.163.228.93, 52.163.230.166|
|Södra Indien|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Västra centrala USA|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Västra Europa|40.68.222.65, 40.68.209.23, 13.95.147.65|
|Indien, västra|104.211.164.80, 104.211.162.205, 104.211.164.136|
|Västra USA|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Västra USA 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Storbritannien, södra|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Storbritannien, västra|51.141.54.185, 51.141.45.238, 51.141.47.136|
| | |

### <a name="connectors"></a>Anslutningar

Anropen som [kopplingar](../connectors/apis-list.md) Se kommer från IP-adresserna i den här listan.

|Logic Apps region|Utgående IP|
|-----------------|-----------|
|Östra Australien|40.126.251.213|
|Sydöstra Australien|40.127.80.34|
|Södra Brasilien|191.232.38.129|
|Centrala Kanada|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Östra Kanada|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|Indien, centrala|104.211.98.164|
|Centrala USA|40.122.49.51|
|Östasien|23.99.116.181|
|Östra USA|191.237.41.52|
|Östra USA 2|104.208.233.100|
|Östra Japan|40.115.186.96|
|Västra Japan|40.74.130.77|
|Norra centrala USA|65.52.218.230|
|Norra Europa|104.45.93.9|
|Södra centrala USA|104.214.70.191|
|Sydostasien|13.76.231.68|
|Södra Indien|104.211.227.225|
|Västra Europa|40.115.50.13|
|Indien, västra|104.211.161.203|
|Västra USA|104.40.51.248|
|Storbritannien, södra|51.140.80.51|
|Storbritannien, västra|51.141.47.105|
| | | 

## <a name="next-steps"></a>Nästa steg  

* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Video: Automatisera affärsprocesser med Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Video: Integrera dina system med Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
