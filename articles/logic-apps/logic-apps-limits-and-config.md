---
title: Gränser och konfiguration – Azure Logic Apps | Microsoft Docs
description: Tjänsten begränsningar och konfigurationsvärden för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/16/2018
ms.openlocfilehash: f371376a7c801eecb6231d551546b13dbc68dd26
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916816"
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
| Minsta Upprepningsintervall | 1 sekund | |
| Maximal Upprepningsintervall | 500 dagar | |
| Kvarhållning | Starttid för 90 dagar från kör | Om du vill ändra den här gränsen till ett värde mellan 7 dagar och 90 dagar, se [ändra kvarhållning](#change-retention). |
||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Ändra omgången varaktigheten och lagringen

Följ dessa steg om du vill ändra Standardgränsen för mellan 7 dagar och 90 dagar. Om du vill gå över gränsvärdet, [Logic Apps-teamet](mailto://logicappsemail@microsoft.com) hjälp med dina behov.

1. I Azure-portalen på logikappens meny väljer **arbetsflödesinställningarna**.

2. Under **körningsalternativ**, från den **kör kvarhållning av körningshistorik i dagar** väljer **anpassad**.

3. Ange eller dra skjutreglaget för hur många dagar som du vill.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Samtidighet, loopning och ombatchningsgränser

Här följer begränsningarna för en enkel logikapp-körningen:

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Utlösaren samtidighet | * Obegränsade när samtidighetskontrollen är avstängd <p><p>* 25 är Standardgränsen när samtidighetskontrollen aktiveras, vilket går inte att ångra när du har aktiverat kontrollen. Du kan ändra standardvärdet till ett värde mellan 1 och 50 portintervallet. | Den här gränsen beskriver det högsta antalet för logic app-instanser som kan köras samtidigt eller parallellt. <p><p>Om du vill ändra Standardgränsen till ett värde mellan 1 och 50 portintervallet [ändra utlösaren samtidighetsgräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) eller [utlösa instanser sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Den maximala väntetiden körningar | När samtidighetskontrollen är påslagen, är det minsta antalet körningar för att vänta 10 plus antalet samtidiga körningar (utlösare samtidighet). Du kan ändra det maximala antalet upp till 100 portintervallet. | Den här gränsen beskriver det högsta antalet för logic app-instanser som kan vänta med att köra när logikappen körs redan det högsta antalet samtidiga instanser. <p><p>Om du vill ändra Standardgränsen [ändring väntar körningar begränsa](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Foreach-matrisobjekt | 100 000 | Den här gränsen beskriver det högsta antalet matrisobjekt som en ”för var och en”-loop kan bearbeta. <p><p>Du kan använda för att filtrera större matriser, den [frågeåtgärd](../connectors/connectors-native-query.md). |
| Foreach-samtidighet | 20 är Standardgränsen när samtidighetskontrollen är avstängd. Du kan ändra standardvärdet till ett värde mellan 1 och 50 portintervallet. | Den här gränsen är högsta antalet för ”för var och en” loop iterationer som kan köras samtidigt eller parallellt. <p><p>Om du vill ändra Standardgränsen till ett värde mellan 1 och 50 portintervallet [ändra ”för var och en” samtidighet gränsen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) eller [kör ”för var och en” loopar sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| SplitOn-objekt | 100 000 | Du kan ange ett uttryck som använder en ”SplitOn-egenskapen för utlösare som returnerar en matris, som [delar eller debatches matrisobjekt i flera arbetsflödesinstanser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) för bearbetning i stället använda en” Foreach ”loop. Det här uttrycket refererar till matrisen ska användas för att skapa och köra en arbetsflödesinstans för varje objekt i matrisen. |
| Until-iterationer | 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Här följer begränsningarna för en enkel logikapp-körningen:

### <a name="global-logic-apps-service"></a>Tjänsten för global Logic Apps

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Åtgärd: Körningar per 5 minuter | 100 000 är Standardgränsen, men 300 000 är den maximala gränsen. | Om du vill ändra Standardgränsen [kör logikappen i ”hög” genomflödesläge](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode), vilket är i förhandsversion. Eller du kan distribuera arbetsbelastningen över flera logikapp efter behov. |
| Åtgärd: Samtidiga utgående samtal | ~2,500 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
| Runtime-slutpunkt: Samtidiga inkommande samtal | ~1,000 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
| Runtime-slutpunkt: Läs anrop per 5 minuter  | 60,000 | Du kan fördela belastningen över fler än en app efter behov. |
| Runtime-slutpunkt: Anropa anrop per 5 minuter | 45,000 | Du kan fördela belastningen över fler än en app efter behov. |
| Innehåll dataflöde per 5 minuter | 600 MB | Du kan fördela belastningen över fler än en app efter behov. |
||||

### <a name="integration-service-environment-ise"></a>Integreringstjänstmiljön (ISE)

| Namn | Gräns | Anteckningar |
|------|-------|-------|
| Gräns för utförande av basenhet | 10 000 körningar per 5 minuter <br>vilket är ~ 80 miljoner körningar per månad | |
| Skalningsgräns enhet körning | 5 000 körningar per 5 minuter <br>vilket är ~ 40 miljoner körningar per månad | |
| Maximal skala enheter som du kan lägga till | 3 | |
||||

Att gå över dessa gränser i normala bearbetningen eller köra belastningstest som kan gå över dessa gränser [Logic Apps-teamet](mailto://logicappsemail@microsoft.com) hjälp med dina behov.

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-gränser

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
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Hanterade identiteter

| Namn | Gräns |
| ---- | ----- |
| Antalet logikappar med systemtilldelade hanterade identiteter per Azure-prenumeration | 10 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Begränsningar för integrering

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefakten gränserna per konto

Här finns gränser för antalet artefakter för varje integrationskontot. Mer information finns i [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!NOTE] 
> Använd den kostnadsfria nivån enbart för undersökande scenarier, inte produktionsscenarier. Den här nivån begränsar dataflödet och användning och har inget servicenivåavtal (SLA).

| Artefakt | Kostnadsfri | Basic | Standard |
|----------|------|-------|----------|
| Handel EDI-avtal | 10 | 1 | 500 |
| EDI-affärspartner | 25 | 2 | 500 |
| Kartor | 25 | 500 | 1,000 |
| Scheman | 25 | 500 | 1,000 |
| Sammansättningar | 10 | 25 | 50 |
| Certifikat | 25 | 2 | 500 |
| Batchkonfigurationer | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Artefakten kapacitetsbegränsningar

| Artefakt | Gräns | Anteckningar |
| -------- | ----- | ----- |
| Sammansättning | 8 MB | Om du vill ladda upp filer som är större än 2 MB, använda en [Azure storage-konto och blob-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Karta (XSLT-fil) | 8 MB | Om du vill ladda upp filer som är större än 2 MB, använda den [Azure Logic Apps REST API - mappar](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| Schema | 8 MB | Om du vill ladda upp filer som är större än 2 MB, använda en [Azure storage-konto och blob-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Runtime-slutpunkt | Gräns | Anteckningar |
|------------------|-------|-------|
| Läs anrop per 5 minuter | 60,000 | Du kan distribuera arbetsbelastningen över fler än ett konto som behövs. |
| Anropa anrop per 5 minuter | 45,000 | Du kan distribuera arbetsbelastningen över fler än ett konto som behövs. |
| Spåra anrop per 5 minuter | 45,000 | Du kan distribuera arbetsbelastningen över fler än ett konto som behövs. |
| Blockerande samtidiga anrop | ~1,000 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
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

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Inaktivera eller ta bort logikappar

När du inaktiverar en logikapp kan instansieras inga nya körningar. Alla pågående och fortsätta förrän de är klara, vilket kan ta tid att slutföra väntande körningar.

När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Brandväggskonfiguration: IP-adresser

Alla logikappar i samma region använder samma IP-adressintervall. Stöd för de anrop som dina logic apps gör direkt med [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md), och andra HTTP-begäranden, ställa in brandväggarna med *alla* den [ inkommande](#inbound) *och* [utgående](#outbound) IP-adresser som används av Logic Apps-tjänsten, baserat på de regioner där dina logic apps finns. De här adresserna visas under den **inkommande** och **utgående** rubriker i det här avsnittet och sorteras efter region.

Stöd för anrop som [Microsoft-hanterade anslutningsappar](../connectors/apis-list.md) märke, ställa in brandväggen med *alla* den [utgående](#outbound) IP-adresser som används av dessa anslutningar baserat på regionerna var finns dina logic apps. De här adresserna visas under den **utgående** rubrik i det här avsnittet och sorteras efter region.

För [Azure Government](../azure-government/documentation-government-overview.md) och [Azure Kina 21Vianet](/azure/china/china-welcome), reserverade IP-adresser för kopplingar är inte tillgänglig för tillfället.

> [!IMPORTANT]
>
> Om du har befintliga konfigurationer kan uppdatera dem **så snart som möjligt före den 1 September 2018** så att de omfattar och matchar IP-adresser i listorna för de regioner där dina logic apps finns.

Logic Apps stöder inte att ansluta direkt till Azure storage-konton genom brandväggar. För att få åtkomst till dessa konton måste du använda något av alternativen här:

* Skapa en [integreringstjänstmiljön](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), som kan ansluta till resurser i Azure-nätverk.

* Om du redan använder API Management kan du använda den här tjänsten för det här scenariot. Mer information finns i [enkel integrering företagsarkitektur](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Inkommande IP-adresser – Logic Apps-tjänsten

| Region | IP-adress |
|--------|----|
| Östra Australien | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Sydöstra Australien | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Södra Brasilien | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Centrala Kanada | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Östra Kanada | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| Indien, centrala | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Centrala USA | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Östasien | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| Östra USA | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| USA, östra 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Östra Japan | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Västra Japan | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Norra centrala USA | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Norra Europa | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Södra centrala USA | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Södra Indien | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sydostasien | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Västra centrala USA | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Västra Europa | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| Indien, västra | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Västra USA | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Västra USA 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Storbritannien, södra | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Storbritannien, västra | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Utgående IP-adresser – Logic Apps-tjänsten och hanterade anslutningsappar

| Region | Logic Apps-IP | Hanterade anslutningsappar IP |
|--------|---------------|-----------------------|
| Östra Australien | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 |
| Sydöstra Australien | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 |
| Södra Brasilien | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Centrala Kanada | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 |
| Östra Kanada | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 |
| Indien, centrala | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 |
| Centrala USA | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 |
| Östasien | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 |
| Östra USA | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| USA, östra 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 |
| Östra Japan | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 |
| Västra Japan | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 |
| Norra centrala USA | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 |
| Norra Europa | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 |
| Södra centrala USA | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 |
| Södra Indien | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 |
| Sydostasien | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 |
| Västra centrala USA | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 |
| Västra Europa | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 |
| Indien, västra | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 |
| Västra USA | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 |
| Västra USA 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 |
| Storbritannien, södra | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15 |
| Storbritannien, västra | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Nästa steg  

* Lär dig hur du [skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Lär dig mer om [vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
