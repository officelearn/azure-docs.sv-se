---
title: Gränser och konfiguration – Azure Logic Apps | Microsoft Docs
description: Tjänsten begränsningar och konfigurationsvärden för Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 7726d7eae2d9b3a37ffc141e4a4cbdc9a1af459c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145045"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Begränsningar och konfigurationsinformation för Azure Logic Apps

Den här artikeln beskriver gränser och konfigureringsinformation för att skapa och köra automatiska arbetsflöden med Azure Logic Apps. Microsoft Flow finns [gränser och konfigurering i Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definitionsgränser

Här följer begränsningarna för en enda logic app-definition:

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Åtgärder per arbetsflöde | 500 | Du kan lägga till kapslade arbetsflöden efter behov för att utöka den här gränsen. |
| Tillåtna kapslingsdjup för åtgärder | 8 | Du kan lägga till kapslade arbetsflöden efter behov för att utöka den här gränsen. | 
| Arbetsflöden per region per prenumeration | 1,000 | | 
| Utlösare per arbetsflöde | 10 | När du arbetar i kodvyn inte designer | 
| Växeln omfång fall gräns | 25 | | 
| Variabler per arbetsflöde | 250 | | 
| Tecken per uttryck | 8 192 | | 
| Maximal storlek för `trackedProperties` | 16 000 tecken | 
| Namn på för `action` eller `trigger` | 80 tecken | | 
| Längden på `description` | 256 tecken | | 
| Maximalt `parameters` | 50 | | 
| Maximalt `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Kör begränsningar för varaktighet och kvarhållning

Här följer begränsningarna för en enkel logikapp-körningen:

| Namn | Gräns | Anteckningar | 
|------|-------|-------| 
| Varaktighet för körning | 90 dagar | Om du vill ändra den här gränsen, se [ändra körningens varaktighet](#change-duration). | 
| Kvarhållning | Starttid för 90 dagar från kör | Om du vill ändra den här gränsen, se [ändra kvarhållning](#change-retention). | 
| Minsta Upprepningsintervall | 1 sekund | | 
| Maximal Upprepningsintervall | 500 dagar | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Ändra omgången varaktigheten och lagringen

Du kan ändra den här gränsen till ett värde mellan 7 dagar och 90 dagar. Att gå över gränsvärdet, [Logic Apps-teamet](mailto://logicappsemail@microsoft.com) hjälp med dina behov.

1. I Azure-portalen på logikappens meny väljer **arbetsflödesinställningarna**. 

2. Under **körningsalternativ**, från den **kör kvarhållning av körningshistorik i dagar** väljer **anpassad**. 

3. Ange eller dra skjutreglaget för hur många dagar som du vill.

<a name="looping-debatching-limits"></a>

## <a name="looping-and-debatching-limits"></a>Loopning och ombatchningsgränser

Här följer begränsningarna för en enkel logikapp-körningen:

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Until-iterationer | 5 000 | | 
| ForEach-objekt | 100 000 | Du kan använda den [frågeåtgärd](../connectors/connectors-native-query.md) att filtrera större matriser efter behov. | 
| ForEach-parallellitet | 50 | Standardvärdet är 20. <p>Du kan ändra den här standardnivån i en ForEach-loop, ange den `runtimeConfiguration` -egenskapen i den `foreach` åtgärd. <p>Om du vill köra sekventiellt en ForEach-loop, den `operationOptions` egenskap ”sekventiellt” i den `foreach` åtgärd. | 
| SplitOn-objekt | 100 000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Här följer begränsningarna för en enkel logikapp-körningen:

| Namn | Gräns | Anteckningar | 
| ----- | ----- | ----- | 
| Åtgärder körningar per 5 minuter | 100 000 | Om du vill höja gränsen till 300 000, du kan köra en logikapp i `High Throughput` läge. Konfigurera hög genomströmning-läge under den `runtimeConfiguration` för resursen i arbetsflödet, den `operationOptions` egenskap `OptimizedForHighThroughput`. <p>**Obs**: läge för hög genomströmning är en förhandsversion. Du kan också distribuera en arbetsbelastning i fler än en app efter behov. | 
| Åtgärder samtidiga utgående samtal | ~2,500 | Minska antalet samtidiga begäranden eller minska varaktigheten efter behov. | 
| Runtime-slutpunkten: samtidiga inkommande samtal | ~1,000 | Minska antalet samtidiga begäranden eller minska varaktigheten efter behov. | 
| Runtime-slutpunkten: läsa anrop per 5 minuter  | 60,000 | Kan fördela belastningen över fler än en app efter behov. | 
| Runtime-slutpunkten: anropa anrop per 5 minuter| 45,000 | Kan fördela belastningen över fler än en app efter behov. | 
|||| 

Att gå över dessa gränser i normala bearbetningen eller köra belastningstest som kan gå över dessa gränser [Logic Apps-teamet](mailto://logicappsemail@microsoft.com) hjälp med dina behov.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>HTTP-begäran gränser

Här följer begränsningarna för en HTTP-begäran eller synkron anslutning anrop:

#### <a name="timeout"></a>Timeout

Vissa åtgärder för anslutningsappen göra asynkrona anrop eller lyssna efter begäranden som webhook, så att tidsgränsen för de här åtgärderna kan vara längre än dessa gränser. Mer information finns i de tekniska detaljerna för specifika anslutningen och även [utlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Utgående begäran | 120 sekunder | Längre åtgärder, använda en [asynkrona avsökningen mönstret](../logic-apps/logic-apps-create-api-app.md#async-pattern) eller en [until-loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Synkron svar | 120 sekunder | För den ursprungliga begäran att hämta svaret måste alla steg i svaret slutförs inom gränsen, såvida inte du anropa en annan logikapp som ett kapslat arbetsflöde. Mer information finns i [anropa, utlösare, eller kapsla logikappar](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Meddelandestorlek

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Meddelandestorlek | 100 MB | Du kan undvika den här gränsen, se [hantera stora meddelanden med storlekar](../logic-apps/logic-apps-handle-large-messages.md). Men kan vissa kopplingar och API: er inte stöd för storlekar eller även Standardgränsen. | 
| Meddelandestorlek med storlekar | 1 GB | Den här gränsen gäller för åtgärder som har inbyggt stöd för storlekar eller kan du aktivera storlekar i sina runtime-konfigurationen. Mer information finns i [hantera stora meddelanden med storlekar](../logic-apps/logic-apps-handle-large-messages.md). | 
| Gräns för uttrycksutvärdering | 131 072 tecken | Den `@concat()`, `@base64()`, `@string()` uttryck får inte vara längre än den här gränsen. | 
|||| 

#### <a name="retry-policy"></a>Återförsöksprincip

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Antal återförsök | 90 | Standardvärdet är 4. Du kan ändra standardinställningen med den [försök principparametern](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Maximal tid innan nytt försök | 1 dag | Du kan ändra standardinställningen med den [försök principparametern](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Min tid innan nytt försök | 5 sekunder | Du kan ändra standardinställningen med den [försök principparametern](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Gränser för anpassad anslutningsapp

Här följer begränsningarna för anpassade anslutningsappar som du kan skapa från web API: er.

| Namn | Gräns | 
| ---- | ----- | 
| Antal anpassade anslutningsappar | 1 000 per Azure-prenumeration | 
| Antal begäranden per minut för varje anslutning som skapats av en anpassad anslutningsapp | 500 begäranden per anslutning |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Begränsningar för integrering

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefakten gränserna per konto

Här finns gränser för antalet artefakter för varje integrationskontot. Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Kostnadsfri nivå*

Använd den kostnadsfria nivån enbart för undersökande scenarier, inte produktionsscenarier. Den här nivån begränsar dataflödet och användning och har inget servicenivåavtal (SLA).

| Artefakt | Gräns | Anteckningar | 
|----------|-------|-------| 
| EDI-affärspartner | 25 | | 
| Handel EDI-avtal | 10 | | 
| Kartor | 25 | | 
| Scheman | 25 | 
| Sammansättningar | 10 | | 
| Batchkonfigurationer | 5 | 
| Certifikat | 25 | | 
|||| 

*Basic-nivån*

| Artefakt | Gräns | Anteckningar | 
|----------|-------|-------| 
| EDI-affärspartner | 2 | | 
| Handel EDI-avtal | 1 | | 
| Kartor | 500 | | 
| Scheman | 500 | 
| Sammansättningar | 25 | | 
| Batchkonfigurationer | 1 | | 
| Certifikat | 2 | | 
|||| 

*Standardnivå*

| Artefakt | Gräns | Anteckningar | 
|----------|-------|-------| 
| EDI-affärspartner | 500 | | 
| Handel EDI-avtal | 500 | | 
| Kartor | 500 | | 
| Scheman | 500 | 
| Sammansättningar | 50 | | 
| Batchkonfigurationer | 5 |  
| Certifikat | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefakten kapacitetsbegränsningar

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| Schema | 8 MB | Om du vill ladda upp filer som är större än 2 MB, använda den [blob-URI: N](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Karta (XSLT-fil) | 2 MB | | 
| Runtime-slutpunkten: läsa anrop per 5 minuter | 60,000 | Du kan distribuera arbetsbelastningen över fler än ett konto som behövs. | 
| Runtime-slutpunkten: anropa anrop per 5 minuter | 45,000 | Du kan distribuera arbetsbelastningen över fler än ett konto som behövs. | 
| Runtime-slutpunkten: spåra anrop per 5 minuter | 45,000 | Du kan distribuera arbetsbelastningen över fler än ett konto som behövs. | 
| Runtime-slutpunkten: blockerar samtidiga anrop | ~1,000 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-protokollet (AS2 X12, EDIFACT) meddelandestorlek

Här följer de begränsningar som gäller för B2B-protokoll:

| Namn | Gräns | Anteckningar | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Gäller för att avkoda och koda | 
| X12 | 50 MB | Gäller för att avkoda och koda | 
| EDIFACT | 50 MB | Gäller för att avkoda och koda | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Konfiguration: IP-adresser

### <a name="azure-logic-apps-service"></a>Azure Logic Apps-tjänsten

Alla logikappar i en region använder samma intervall av IP-adresser. Stöd för de anrop som logic apps gör direkt med [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md), och andra HTTP-begäranden, konfigurera din brandväggskonfigurationer så att de inkluderar dessa utgående och inkommande IP-adresser baserat på där dina logic apps finns:

| Logic Apps-regionen | Utgående IP |
|-------------------|-------------|
| Östra Australien | 13.75.149.4, 104.210.91.55, 104.210.90.241 |
| Sydöstra Australien | 13.73.114.207, 13.77.3.139, 13.70.159.205 |
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

| Logic Apps-regionen | Inkommande IP |
|-------------------|------------|
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

Stöd för anrop som [kopplingar](../connectors/apis-list.md) märke, konfigurera din brandväggskonfigurationer så att de inkluderar dessa utgående IP-adresser baserat på de regioner där dina logic apps finns.

> [!IMPORTANT]
>
> Om du har befintliga konfigurationer kan uppdatera dem **så snart som möjligt före den 1 September 2018** så att de omfattar och matchar IP-adresser i den här listan för de regioner där dina logic apps finns. 

| Logic Apps-regionen | Utgående IP | 
|-------------------|-------------|  
| Östra Australien | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Sydöstra Australien | 13.77.50.240 - 13.77.50.255, 13.70.136.174, 40.127.80.34 | 
| Södra Brasilien | 191.233.203.192 - 191.233.203.207, 104.41.59.51, 191.232.38.129 | 
| Centrala Kanada | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126, 52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13, 52.233.26.83 | 
| Östra Kanada | 40.69.106.240 - 40.69.106.255, 52.242.35.152, 52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52, 52.229.120.131 | 
| Indien, centrala | 104.211.81.192 - 104.211.81.207, 52.172.211.12, 104.211.98.164 | 
| Centrala USA | 13.89.171.80 - 13.89.171.95, 52.173.245.164, 40.122.49.51 | 
| Östasien | 13.75.36.64 - 13.75.36.79, 52.175.23.169, 23.99.116.181 | 
| Östra USA | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| Östra USA 2 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Östra Japan | 13.78.108.0 - 13.78.108.15, 13.71.153.19, 40.115.186.96 | 
| Västra Japan | 40.74.100.224 - 40.74.100.239, 104.215.61.248, 40.74.130.77 | 
| Norra centrala USA | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Norra Europa | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| Södra centrala USA | 104.214.19.48 - 104.214.19.63, 13.65.86.57, 104.214.70.191 | 
| Södra Indien | 40.78.194.240 - 40.78.194.255, 13.71.125.22, 104.211.227.225 | 
| Sydostasien | 13.67.8.240 - 13.67.8.255, 52.187.68.19, 13.76.231.68 | 
| Västra centrala USA | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 52.161.27.108, 52.161.30.5, 52.161.29.35, 52.161.26.212, 52.161.128 | 
| Västra Europa | 13.69.64.208 - 13.69.64.223, 52.174.88.118, 40.115.50.13 | 
| Indien, västra | 104.211.146.224 - 104.211.146.239, 104.211.189.218, 104.211.161.203 | 
| Västra USA | 40.112.243.160 - 40.112.243.175, 104.42.122.49, 104.40.51.248 | 
| Västra USA 2 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 13.66.225.219, 13.66.218.78, 13.66.220.135, 13.66.219.14, 13.66.221.19 | 
| Storbritannien, södra | 51.140.148.0 - 51.140.148.15, 51.140.80.51 | 
| Storbritannien, västra | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>Nästa steg  

* Lär dig hur du [skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Lär dig mer om [vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
