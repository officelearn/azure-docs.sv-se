---
title: Gränser och -konfiguration – Azure Logic Apps | Microsoft Docs
description: Tjänsten gränser och konfigurationsvärden för Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: jeffhollan
manager: anneta
editor: ''
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 524a2dc7a1a5ae4f0747af03d1b9e69d512f0f00
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Gränser och konfigurationsinformation för Logikappar i Azure

Den här artikeln beskriver gränser och konfigurationsinformation för att skapa och köra automatiska arbetsflöden med Azure Logikappar. Microsoft Flow finns [gränser och konfigurationen i Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definition av gränser

Här följer gränser för en enskild logik app definition:

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Åtgärder per arbetsflöde | 500 | Du kan lägga till inkapslade arbetsflöden som behövs för att utöka den här gränsen. |
| Tillåtna inkapslingsdjup för åtgärder | 8 | Du kan lägga till inkapslade arbetsflöden som behövs för att utöka den här gränsen. | 
| Arbetsflöden per region per prenumeration | 1,000 | | 
| Utlösare per arbetsflöde | 10 | När du arbetar i kodvy inte designer | 
| Växeln scope fall gränsen | 25 | | 
| Variabler per arbetsflöde | 250 | | 
| Tecken per uttryck | 8 192 | | 
| Maximal storlek för `trackedProperties` | 16 000 tecken | 
| Namn för `action` eller `trigger` | 80 tecken | | 
| Längden på `description` | 256 tecken | | 
| Maximalt `parameters` | 50 | | 
| Maximalt `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Kör varaktighet och kvarhållning gränser

Här följer gränser för en enkel logikapp som kör:

| Namn | Gräns | 
| ---- | ----- | 
| Kör varaktighet | 90 dagar | 
| Kvarhållning av lagring | 90 dagar från kör starttid | 
| Minsta intervall | 1 sekund </br>För logic apps med en App Service-Plan: 15 sekunder | 
| Maximalt intervall | 500 dagar | 
||| 

Att överskrida för kör varaktighet eller lagring kvarhållning i flödet för normala bearbetningen [kontakta Logic Apps-teamet](mailto://logicappsemail@microsoft.com) hjälp med dina krav.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Slingor och debatching gränser

Här följer gränser för en enkel logikapp som kör:

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Tills iterationer | 5 000 | | 
| ForEach-objekt | 100,000 | Du kan använda den [fråga åtgärd](../connectors/connectors-native-query.md) att filtrera större matriser efter behov. | 
| ForEach parallellitet | 50 | Standardvärdet är 20. <p>Att ställa in en viss nivå av parallellitet i en ForEach-loop i `runtimeConfiguration` egenskap i den `foreach` åtgärd. <p>Om du vill köra en ForEach-loop sekventiellt, den `operationOptions` egenskapen till ”sekventiella” i den `foreach` åtgärd. | 
| SplitOn objekt | 100,000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Genomströmning gränser

Här följer gränser för en enkel logikapp som kör:

| Namn | Gräns | Anteckningar | 
| ----- | ----- | ----- | 
| Åtgärder körningar per 5 minuter | 100,000 | Om du vill öka gränsen för 300,000, kan du köra en logikapp i `High Throughput` läge. Konfigurera hög genomströmning-läge under den `runtimeConfiguration` av arbetsflödet resurs, ange den `operationOptions` egenskapen `OptimizedForHighThroughput`. <p>**Obs**: hög genomströmning läge är en förhandsversion. Du kan också distribuera en arbetsbelastning över flera appar efter behov. | 
| Åtgärder samtidiga utgående anrop | ~2,500 | Minska antalet samtidiga begäranden eller minska tidsåtgången efter behov. | 
| Runtime-slutpunkten: samtidiga inkommande samtal | ~1,000 | Minska antalet samtidiga begäranden eller minska tidsåtgången efter behov. | 
| Runtime-slutpunkten: läsa anrop per 5 minuter  | 60,000 | Kan fördela belastningen över flera appar efter behov. | 
| Runtime-slutpunkten: anropa anrop per 5 minuter| 45,000 |Kan fördela belastningen över flera appar efter behov. | 
|||| 

Överskrider gränserna i normala bearbetningen, eller kör belastningen testning som eventuellt överskrider gränserna, [kontakta Logic Apps-teamet](mailto://logicappsemail@microsoft.com) hjälp med dina krav.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>HTTP-begäran gränser

Här följer gränser för en enskild HTTP-begäran eller synkron connector anrop:

#### <a name="timeout"></a>Timeout

Vissa åtgärder för kopplingen asynkrona anrop eller lyssna efter begäranden som webhook, så att tidsgränsen för dessa åtgärder kan vara längre än dessa begränsningar. Mer information finns i de tekniska detaljerna för specifika anslutningen och även [arbetsflöde utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Utgående begäran | 120 sekunder | Längre kör åtgärder använder en [asynkron avsökning mönster](../logic-apps/logic-apps-create-api-app.md#async-pattern) eller en [tills loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Synkron svar | 120 sekunder | För den ursprungliga begäranden hämta svaret måste alla steg i svaret slutföras inom den om du anropar en annan logikappen som ett kapslat arbetsflöde. Mer information finns i [anropa utlösare eller kapsla logikappar](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Meddelandestorlek

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Meddelandestorlek | 100 MB | Vissa kopplingar och API: er stöder inte 100 MB. | 
| Gränsen för utvärdering av uttryck | 131,072 tecken | Den `@concat()`, `@base64()`, `@string()` uttryck får inte vara längre än den här gränsen. | 
|||| 

#### <a name="retry-policy"></a>Återförsöksprincip

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Antal återförsök | 90 | Standardvärdet är 4. Om du vill ändra standardvärdet, den [försök parametern](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Högsta antal försök | 1 dag | Om du vill ändra standardvärdet, den [försök parametern](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Min tid innan nytt försök | 5 sekunder | Om du vill ändra standardvärdet, den [försök parametern](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Anpassad koppling gränser

Här följer gränser för anpassade kopplingar som du kan skapa från web API: er.

| Namn | Gräns | 
| ---- | ----- | 
| Antal anpassade kopplingar | 1 000 per Azure-prenumeration | 
| Antal begäranden per minut för varje anslutning som skapats av en anpassad anslutningsapp | 500 förfrågningar per anslutning |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Gränser för integrering

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefakt gränser per integration konto

Här följer gränser för antalet artefakter för varje konto för integrering.

*Gratis prisnivån*

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Avtal | 10 | | 
| Andra typer av artefakt | 25 | Artefakt typer är partners, scheman, certifikat och kartor. Varje typ kan ha upp till maximalt antal artefakter. | 
|||| 

*Standard prisnivå*

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Alla typer av artefakt | 500 | Artefakt typer är avtal, partner, scheman, certifikat och kartor. Varje typ kan ha upp till maximalt antal artefakter. | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefakt kapacitetsbegränsningar

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Schema | 8 MB | Om du vill överföra filer som är större än 2 MB, Använd den [blob-URI](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Karta (XSLT-fil) | 2 MB | | 
| Runtime-slutpunkten: läsa anrop per 5 minuter | 60,000 | Du kan distribuera belastningen över flera konton som behövs. | 
| Runtime-slutpunkten: anropa anrop per 5 minuter | 45,000 | Du kan distribuera belastningen över flera konton som behövs. | 
| Runtime-slutpunkten: spåra anrop per 5 minuter | 45,000 | Du kan distribuera belastningen över flera konton som behövs. | 
| Runtime-slutpunkten: blockerar samtidiga anrop | ~1,000 | Du kan minska antalet samtidiga begäranden eller minska tidsåtgången vid behov. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Meddelandestorlek B2B-protokollet (AS2 X12 EDIFACT)

Här följer gränser som gäller för B2B-protokoll:

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Gäller för att avkoda och koda | 
| X12 | 50 MB | Gäller för att avkoda och koda | 
| EDIFACT | 50 MB | Gäller för att avkoda och koda | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfiguration: IP-adresser

### <a name="azure-logic-apps-service"></a>Logic Apps i Azure-tjänsten

Alla logikappar i en region använder samma uppsättning IP-adresser.
Anrop som logic apps gör direkt med [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) eller andra HTTP-begäranden som kommer från IP-adresser i den här listan. 

| Logic Apps region | Utgående IP |
|-------------------|-------------|
| Australien | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
| Östra Australien | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Södra Brasilien | 191.235.82.221, 191.235.91.7, 191.234.182.26 |
| Centrala Kanada | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Östra Kanada | 52.232.128.155, 52.229.120.45, 52.229.126.25 |
| Indien, centrala | 52.172.154.168, 52.172.186.159, 52.172.185.79 |
| Centrala USA | 13.67.236.125, 104.208.25.27, 40.122.170.198 |
| Östasien | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Östra USA | 13.92.98.111, 40.121.91.41, 40.114.82.191 |
| Östra USA 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Östra Japan | 13.71.158.3, 13.73.4.207, 13.71.158.120 |
| Västra Japan | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Norra centrala USA | 168.62.248.37, 157.55.210.61, 157.55.212.238 |
| Norra Europa | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Södra centrala USA | 104.210.144.48, 13.65.82.17, 13.66.52.232 |
| Södra Indien | 52.172.50.24, 52.172.55.231, 52.172.52.0 |
| Sydostasien | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Västra centrala USA | 52.161.27.190, 52.161.18.218, 52.161.9.108 |
| Västra Europa | 40.68.222.65, 40.68.209.23, 13.95.147.65 |
| Indien, västra | 104.211.164.80, 104.211.162.205, 104.211.164.136 |
| Västra USA | 52.160.92.112, 40.118.244.241, 40.118.241.243 |
| Västra USA 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132 |
| Storbritannien, södra | 51.140.74.14, 51.140.73.85, 51.140.78.44 |
| Storbritannien, västra | 51.141.54.185, 51.141.45.238, 51.141.47.136 |
| | |

| Logic Apps region | Inkommande IP |
|-------------------|-------------|
| Östra Australien | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Sydöstra Australien | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Södra Brasilien | 191.235.86.199, 191.235.95.229, 191.235.94.220 |
| Centrala Kanada | 13.88.249.209, 52.233.30.218, 52.233.29.79 |
| Östra Kanada | 52.232.129.143, 52.229.125.57, 52.232.133.109 |
| Indien, centrala | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Centrala USA | 13.67.236.76, 40.77.111.254, 40.77.31.87 |
| Östasien | 168.63.200.173, 13.75.89.159, 23.97.68.172 |
| Östra USA | 137.135.106.54, 40.117.99.79, 40.117.100.228 |
| Östra USA 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136 |
| Östra Japan | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Västra Japan | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Norra centrala USA | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Norra Europa | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Södra centrala USA | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Södra Indien | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Sydostasien | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Västra centrala USA | 52.161.26.172, 52.161.8.128, 52.161.19.82 |
| Västra Europa | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| Indien, västra | 104.211.164.112, 104.211.165.81, 104.211.164.25 |
| Västra USA | 52.160.90.237, 138.91.188.137, 13.91.252.184 |
| Västra USA 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Storbritannien, södra | 51.140.79.109, 51.140.78.71, 51.140.84.39 |
| Storbritannien, västra | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Anslutningar

Anropen som [kopplingar](../connectors/apis-list.md) Se kommer från IP-adresserna i den här listan.

| Logic Apps region | Utgående IP |
|-------------------|-------------|
| Östra Australien | 40.126.251.213 |
| Sydöstra Australien | 40.127.80.34 |
| Södra Brasilien | 191.232.38.129 |
| Centrala Kanada | 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13 |
| Östra Kanada | 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52 |
| Indien, centrala | 104.211.98.164 |
| Centrala USA | 40.122.49.51 |
| Östasien | 23.99.116.181 |
| Östra USA | 191.237.41.52 |
| Östra USA 2 | 104.208.233.100 |
| Östra Japan | 40.115.186.96 |
| Västra Japan | 40.74.130.77 |
| Norra centrala USA | 65.52.218.230 |
| Norra Europa | 104.45.93.9 |
| Södra centrala USA | 104.214.70.191 |
| Södra Indien | 104.211.227.225 |
| Sydostasien | 13.76.231.68 |
| Västra Europa | 40.115.50.13 |
| Indien, västra | 104.211.161.203 |
| Västra USA | 104.40.51.248 |
| Storbritannien, södra | 51.140.80.51 |
| Storbritannien, västra | 51.141.47.105 |
| | | 

## <a name="next-steps"></a>Nästa steg  

* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Video: Automatisera affärsprocesser med Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Video: Integrera dina system med Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462)
