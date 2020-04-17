---
title: Gränser och konfigurering
description: Tjänstgränser, till exempel varaktighet, dataflöde och kapacitet, plus konfigurationsvärden, till exempel IP-adresser att tillåta, för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: 40950be2e5caeb17d20086720a7b65c15147c2f5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535119"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Information om begränsningar och konfiguration för Azure Logic Apps

I den här artikeln beskrivs begränsningar och konfigurationsinformation för att skapa och köra automatiserade arbetsflöden med Azure Logic Apps. För Power Automate, se [Gränser och konfiguration i Power Automate](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Definitionsgränser

Här är gränserna för en enda logikappdefinition:

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Åtgärder per arbetsflöde | 500 | Om du vill förlänga den här gränsen kan du lägga till kapslade arbetsflöden efter behov. |
| Tillåtet kapslingsdjup för åtgärder | 8 | Om du vill förlänga den här gränsen kan du lägga till kapslade arbetsflöden efter behov. |
| Arbetsflöden per region per prenumeration | 1,000 | |
| Utlösare per arbetsflöde | 10 | När du arbetar i kodvyn visas inte designern |
| Gräns för byte av scopefall | 25 | |
| Variabler per arbetsflöde | 250 | |
| Tecken per uttryck | 8 192 | |
| Maximal storlek för`trackedProperties` | 16 000 tecken |
| Namn `action` på eller`trigger` | 80 tecken | |
| Längd på`description` | 256 tecken | |
| Maximal`parameters` | 50 | |
| Maximal`outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Kör varaktighets- och kvarhållningsgränser

Här är gränserna för en enkel logikappkörning:

| Namn | Gräns för flera innehavare | Miljögräns för integrationstjänster | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Körningens varaktighet | 90 dagar | 366 dagar | Kör varaktighet beräknas med hjälp av en körnings starttid och den gräns som anges *vid starttid* av arbetsflödesinställningen, [**Kör historikkvarhållning i dagar**](#change-duration). <p><p>Om du vill ändra standardgränsen, som är 90 dagar, finns [i ändringskörningsvaraktighet](#change-duration). |
| Kör kvarhållning i lagring | 90 dagar | 366 dagar | Kör kvarhållning beräknas med hjälp av en körnings starttid och den gräns som anges *vid den aktuella tidpunkten* av arbetsflödesinställningen, Kör [**historikkvarhållning i dagar**](#change-retention). Oavsett om en körning slutförs eller time out används alltid körningens starttid i bevarandeberäkningen. När en körnings varaktighet överskrider den *aktuella* kvarhållningsgränsen tas körningen bort från körningarhistoriken. <p><p>Om du ändrar den här inställningen används alltid den aktuella gränsen för beräkning av kvarhållning, oavsett föregående gräns. Om du till exempel minskar kvarhållningsgränsen från 90 dagar till 30 dagar tas en körning som är 60 dagar gammal bort från körningarhistoriken. Om du ökar kvarhållningsperioden från 30 dagar till 60 dagar, stannar en körning som är 20 dagar gammal i körningar historia i ytterligare 40 dagar. <p><p>Information om hur du ändrar standardgränsen, som är 90 dagar, finns [i lagringskörningen för ändringskörning](#change-retention). |
| Minsta upprepningsintervall | 1 sekund | 1 sekund ||
| Maximalt återkommande intervall | 500 dagar | 500 dagar ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>Varaktighet för ändring av körning och kör kvarhållning i lagring

Så här ändrar du standardgränsen för körningslängd och kör kvarhållning i lagring. Om du vill öka maxgränsen [kontaktar du Logic Apps-teamet](mailto://logicappsemail@microsoft.com) för att få hjälp med dina krav.

> [!NOTE]
> För logikappar i Azure med flera innehavare är standardgränsen på 90 dagar samma som den maximala gränsen. Du kan bara minska det här värdet.
> För logikappar i en integrationstjänstmiljö kan du minska eller öka standardgränsen på 90 dagar.

1. Gå till [Azure-portalen](https://portal.azure.com). Leta reda på och välj **Logikappar**i sökrutan för portalen .

1. Markera och öppna logikappen i Logic App Designer.

1. Välj **Arbetsflödesinställningar**på logikappens meny .

1. Under **Körningsalternativ**väljer du **Anpassad**i listan **Körhistorik i dagar** .

1. Dra skjutreglaget om du vill ändra hur många dagar du vill.

1. När du är klar väljer du **Spara**i verktygsfältet **Arbetsflödesinställningar** .

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Samtidighet, looping och debattera gränser

Här är gränserna för en enkel logikappkörning:

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Concurrency | - Obegränsad när samtidighetskontrollen är avstängd <p><p>- 25 är standardgränsen när samtidighetskontrollen är aktiverad, som du inte kan ångra när du har aktiverat samtidighet. Du kan ändra standardvärdet till ett värde mellan 1 och 50. | Den här gränsen beskriver det högsta antalet logikappinstanser som kan köras samtidigt eller parallellt. <p><p>**Obs:** När samtidighet är aktiverat reduceras SplitOn-gränsen till 100 objekt för [att diskutera matriser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>Information om hur du ändrar standardgränsen till ett värde mellan 1 och 50 i följd finns i [Ändra utlösar samtidighetsgräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) eller [utlösa instanser sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximala väntetider | - Utan samtidighet är det minsta antalet väntekörningar 1, medan det maximala antalet är 50. <p><p>- Med samtidighet är det minsta antalet väntekörningar 10 plus antalet samtidiga körningar (trigger samtidighet). Du kan ändra det maximala antalet upp till 100. | Den här gränsen beskriver det högsta antalet logikappinstanser som kan vänta med att köras när logikappen redan kör de maximala samtidiga instanserna. <p><p>Om du vill ändra standardgränsen finns i [Ändra väntekörningsgräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Föra cacheobjekt | 100 000 | Den här gränsen beskriver det högsta antalet matrisobjekt som en "för varje" loop kan bearbeta. <p><p>Om du vill filtrera större matriser kan du använda [frågeåtgärden](logic-apps-perform-data-operations.md#filter-array-action). |
| Förtidskonsensenlighet | 20 är standardgränsen när samtidighetskontrollen är avstängd. Du kan ändra standardvärdet till ett värde mellan 1 och 50. | Den här gränsen är det högsta antalet "för varje" loopiterationer som kan köras samtidigt eller parallellt. <p><p>Information om hur du ändrar standardgränsen till ett värde mellan 1 och 50 i följd finns i [Ändra "för varje" samtidighetsgräns](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) eller [Kör "för varje" loopar sekventiellt](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| SplitOn-objekt | - 100 000 utan triggerkonsensensenlighet <p><p>- 100 med trigger samtidighet | För utlösare som returnerar en matris kan du ange ett uttryck som använder egenskapen "SplitOn" som [delar upp ellerbatserar matrisobjekt i flera arbetsflödesinstanser](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) för bearbetning, i stället för att använda loopen "Foreach". Det här uttrycket refererar till den matris som ska användas för att skapa och köra en arbetsflödesinstans för varje matrisobjekt. <p><p>**Obs:** När samtidighet är aktiverat reduceras SplitOn-gränsen till 100 objekt. |
| Until-iterationer | - Standard: 60 <p><p>- Max: 5 000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Dataflödesbegränsningar

Här är gränserna för en enda logikappdefinition:

### <a name="multi-tenant-logic-apps-service"></a>Logic Apps-tjänst för flera innehavare

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Åtgärd: Avrättningar per 5 minuter | 100 000 är standardgränsen, men 300 000 är den maximala gränsen. | Om du vill ändra standardgränsen finns [i Kör logikappen i läget "högt dataflöde",](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)som är i förhandsgranskning. Du kan också distribuera arbetsbelastningen över mer än en logikapp om det behövs. |
| Åtgärd: Samtidiga utgående samtal | ~ 2 500 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
| Slutpunkt för körning: Samtidiga inkommande samtal | ~1 000 | Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
| Slutpunkt för körning: Läs samtal per 5 minuter  | 60 000 | Du kan distribuera arbetsbelastning över mer än en app om det behövs. |
| Slutpunkt för körning: Anropa samtal per 5 minuter | 45 000 | Du kan distribuera arbetsbelastning över mer än en app om det behövs. |
| Innehållsdataflöde per 5 minuter | 600 MB | Du kan distribuera arbetsbelastning över mer än en app om det behövs. |
||||

### <a name="integration-service-environment-ise"></a>Integrationstjänstmiljö (ISE)

Här är genomströmningsgränserna för [Premium ISE SKU:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

| Namn | Gräns | Anteckningar |
|------|-------|-------|
| Gräns för körning av basenhet | Systemstrypande när infrastrukturkapaciteten når 80 % | Ger ~ 4.000 åtgärd avrättningar per minut, vilket är ~ 160 miljoner åtgärd avrättningar per månad | |
| Gräns för körning av enhet | Systemstrypande när infrastrukturkapaciteten når 80 % | Varje skalningsenhet kan tillhandahålla ~ 2.000 ytterligare åtgärd avrättningar per minut, vilket är ~ 80 miljoner fler åtgärder avrättningar per månad | |
| Maximala skalenheter som du kan lägga till | 10 | |
||||

Om du vill gå över dessa gränser vid normal bearbetning eller köra belastningstester som kan gå över dessa gränser [kontaktar du Logic Apps-teamet](mailto://logicappsemail@microsoft.com) för att få hjälp med dina krav.

> [!NOTE]
> [Developer ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) har inga publicerade gränser, inga funktioner för uppskalning och inget servicenivåavtal (SLA). Använd den här SKU:n endast för att experimentera, utveckla och testa, inte produktion eller prestandatestning.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Gateway gränser

Azure Logic Apps stöder skrivåtgärder, inklusive infogningar och uppdateringar, via gatewayen. Dessa åtgärder har dock [gränser för deras nyttolaststorlek.](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)

<a name="request-limits"></a>

## <a name="http-limits"></a>HTTP-begränsningar

Här är gränserna för ett enda utgående eller inkommande HTTP-samtal:

#### <a name="timeout"></a>Timeout

Vissa anslutningsåtgärder gör asynkrona anrop eller lyssnar efter webhook-begäranden, så tidsgränsen för dessa åtgärder kan vara längre än dessa gränser. Mer information finns i den tekniska informationen för den specifika kopplingen och även [arbetsflödesutlösare och åtgärder](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Namn | Gräns för flera innehavare | Miljögräns för integrationstjänster | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Utgående begäran | 120 sekunder <br>(2 minuter) | 240 sekunder <br>(4 minuter) | Exempel på utgående begäranden är samtal från HTTP-utlösare. <p><p>**Tips:** Använd ett [asynkront avsökningsmönster](../logic-apps/logic-apps-create-api-app.md#async-pattern) eller en [tillslinga](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)för längre drift. |
| Begäran om inkommande information | 120 sekunder <br>(2 minuter) | 240 sekunder <br>(4 minuter) | Exempel på inkommande begäranden är samtal som tas emot av utlösare för begäran och webhook-utlösare. <p><p>**För**att den ursprungliga anroparen ska få svaret måste alla steg i svaret avslutas inom gränsen om du inte anropar en annan logikapp som ett kapslat arbetsflöde. Mer information finns i [Anropa, utlösa eller kapsla logikappar](../logic-apps/logic-apps-http-endpoint.md). |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Meddelandestorlek

| Namn | Gräns för flera innehavare | Miljögräns för integrationstjänster | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Meddelandestorlek | 100 MB | 200 MB | ISE-märkta kopplingar använder ISE-gränsen, inte deras icke-ISE-anslutningsgränser. <p><p>Mer om du vill arbeta runt den här gränsen finns [i Hantera stora meddelanden med segmentering](../logic-apps/logic-apps-handle-large-messages.md). Vissa kopplingar och API:er kanske inte stöder segmentering eller ens standardgränsen. |
| Meddelandestorlek med segmentering | 1 GB | 5 GB | Den här gränsen gäller för åtgärder som antingen stöder segmentering eller låter dig aktivera segmentering i deras körningskonfiguration. <p><p>För integrationstjänstmiljön stöder Logic Apps-motorn den här gränsen, men kopplingar har sina egna segmentgränser upp till motorgränsen, till exempel, se [Azure Blob Storage-kopplingens API-referens](https://docs.microsoft.com/connectors/azureblob/). Mer information om segmentering finns i [Hantera stora meddelanden med segmentering](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Teckengränser

| Namn | Anteckningar |
|------|-------|
| Gräns för uttrycksutvärdering | 131 072 tecken | `@concat()`Uttrycken `@base64()` `@string()` , kan inte vara längre än den här gränsen. |
| Begränsning av begäran om URL-tecken | 16 384 tecken |
|||

<a name="retry-policy-limits"></a>

#### <a name="retry-policy"></a>Återförsöksprincip

| Namn | Gräns | Anteckningar |
| ---- | ----- | ----- |
| Antal återförsök | 90 | Standardvärdet är 4. Om du vill ändra standardinställningen använder du [principparametern för återförsök](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Maximal tid innan nytt försök | 1 dag | Om du vill ändra standardinställningen använder du [principparametern för återförsök](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Minimal tid innan nytt försök | 5 sekunder | Om du vill ändra standardinställningen använder du [principparametern för återförsök](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Begränsningar för anpassade kopplingar

Här är gränserna för anpassade kopplingar som du kan skapa från webb-API:er.

| Namn | Gräns för flera innehavare | Miljögräns för integrationstjänster | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| Antal anpassade kopplingar | 1 000 per Azure-prenumeration | 1 000 per Azure-prenumeration ||
| Antal begäranden per minut för en anpassad anslutning | 500 begäranden per minut och anslutning | 2 000 begäranden per minut per *anpassad anslutning* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Hanterade identiteter

| Namn | Gräns |
|------|-------|
| Hanterade identiteter per logikapp | Antingen den systemtilldelade identiteten eller en användartilldelad identitet |
| Antal logikappar som har en hanterad identitet i en Azure-prenumeration per region | 250 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Begränsningar för integrationskonto

Varje Azure-prenumeration har följande begränsningar för integrationskonto:

* Ett konto för integrering [på en kostnadsfri nivå](../logic-apps/logic-apps-pricing.md#integration-accounts) per Azure-region

* 1 000 totala integrationskonton, inklusive integrationskonton i alla [integrationstjänstmiljöer (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) i både [utvecklare och Premium-SKU:er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Varje ISE, oavsett om det är [utvecklare eller Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)är begränsad till 5 konton för total integrering:

  | ISE SKU | Begränsningar för integrationskonto |
  |---------|----------------------------|
  | **Premium** | 5 totalt - [Endast standardkonton,](../logic-apps/logic-apps-pricing.md#integration-accounts) inklusive ett standardkonto gratis. Inga kostnadsfria konton eller Basic-konton är tillåtna. |
  | **Developer** | 5 totalt - [Gratis](../logic-apps/logic-apps-pricing.md#integration-accounts) (begränsat till 1 konto) och [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) kombinerade, eller alla standardkonton. Inga grundläggande konton är tillåtna. Använd [Developer SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) för att experimentera, utveckla och testa, men inte för produktions- eller prestandatestning. |
  |||

Ytterligare kostnader gäller för integrationskonton som du lägger till utöver de integrationskonton som ingår i en ISE. Mer information om hur prissättning och fakturering fungerar för ISE finns i [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Artefaktgränser per integrationskonto

Här är gränserna för antalet artefakter för varje integrationskontonivå.
Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/). Mer information om hur prissättning och fakturering fungerar för integrationskonton finns i [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Använd den kostnadsfria nivån endast för utforskande scenarier, inte produktionsscenarier. Den här nivån begränsar dataflöde och användning och har inget servicenivåavtal (SLA).

| Artefakt | Kostnadsfri | Basic | Standard |
|----------|------|-------|----------|
| EDI-handelsavtal | 10 | 1 | 1,000 |
| EDI handelspartner | 25 | 2 | 1,000 |
| Kartor | 25 | 500 | 1,000 |
| Scheman | 25 | 500 | 1,000 |
| Sammansättningar | 10 | 25 | 1,000 |
| Certifikat | 25 | 2 | 1,000 |
| Batchkonfigurationer | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Begränsningar för artefaktkapacitet

| Artefakt | Gräns | Anteckningar |
| -------- | ----- | ----- |
| Sammansättning | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure-lagringskonto och blob-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Karta (XSLT-fil) | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du [AZURE Logic Apps REST API - Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Mängden**data eller poster som en karta kan bearbeta baseras på gränserna för meddelandestorlek och åtgärdstidsgräns i Azure Logic Apps. Om du till exempel använder en HTTP-åtgärd, baserat på [HTTP-meddelandestorlek och tidsgränser,](#request-limits)kan en karta bearbeta data upp till HTTP-meddelandestorleksgränsen om åtgärden slutförs inom HTTP-tidsgränsen för tidsgräns. |
| Schema | 8 MB | Om du vill ladda upp filer som är större än 2 MB använder du ett [Azure-lagringskonto och blob-behållare](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Dataflödesbegränsningar

| Slutpunkt för körning | Kostnadsfri | Basic | Standard | Anteckningar |
|------------------|------|-------|----------|-------|
| Läsa samtal per 5 minuter | 3 000 | 30,000 | 60 000 | Du kan distribuera arbetsbelastningen över mer än ett konto efter behov. |
| Anropa samtal per 5 minuter | 3 000 | 30,000 | 45 000 | Du kan distribuera arbetsbelastningen över mer än ett konto efter behov. |
| Spåra samtal per 5 minuter | 3 000 | 30,000 | 45 000 | Du kan distribuera arbetsbelastningen över mer än ett konto efter behov. |
| Blockera samtidiga samtal | ~1 000 | ~1 000 | ~1 000 | Samma för alla SKU: er. Du kan minska antalet samtidiga begäranden eller minska varaktigheten efter behov. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B-protokoll (AS2, X12, EDIFACT) meddelandestorlek

Här är de begränsningar för meddelandestorlek som gäller för B2B-protokoll:

| Namn | Gräns för flera innehavare | Miljögräns för integrationstjänster | Anteckningar |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 50 MB | v2 - 200 MB <br>v1 - 50 MB | Gäller för avkodning och kodning |
| X12 | 50 MB | 50 MB | Gäller för avkodning och kodning |
| EDIFACT | 50 MB | 50 MB | Gäller för avkodning och kodning |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Inaktivera eller ta bort logikappar

När du inaktiverar en logikapp instansieras inga nya körningar. Alla pågående och väntande körningar fortsätter tills de är klara, vilket kan ta tid att slutföra.

När du tar bort en logikapp instantieras inga nya körningar. Alla pågående och väntande körningar avbryts. Om du har flera tusen körningar kan det ta relativt lång tid att avbryta dem.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Brandväggskonfiguration: IP-adresser och tjänsttaggar

De IP-adresser som Azure Logic Apps använder för inkommande och utgående samtal beror på den region där logikappen finns. *Alla* logikappar i samma region använder samma IP-adressintervall. Vissa [Power Automate-anrop,](https://docs.microsoft.com/power-automate/getting-started) till exempel **HTTP-** och HTTP + **OpenAPI-begäranden,** går direkt via Azure Logic Apps-tjänsten och kommer från IP-adresserna som visas här. Mer information om IP-adresser som används av Power Automate finns [i Gränser och konfiguration i Power Automate](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

> [!TIP]
> Om du vill minska komplexiteten när du skapar säkerhetsregler kan du också använda [tjänsttaggar](../virtual-network/service-tags-overview.md)i stället för att ange LOGIC Apps IP-adresser för varje region som beskrivs senare i det här avsnittet. Dessa taggar fungerar i de regioner där Logic Apps-tjänsten är tillgänglig:
>
> * **LogicAppsManagement**: Representerar inkommande IP-adressprefix för Logic Apps-tjänsten.
> * **LogicApps**: Representerar de utgående IP-adressprefixen för logic apps-tjänsten.

* För [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)är fasta eller reserverade IP-adresser inte tillgängliga för [anpassade kopplingar](../logic-apps/custom-connector-overview.md) och [hanterade anslutningsappar,](../connectors/apis-list.md#managed-api-connectors)till exempel Azure Storage, SQL Server, Office 365 Outlook och så vidare.

* Om du vill stödja de anrop som logikapparna direkt gör med [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)och andra HTTP-begäranden konfigurerar du brandväggen med alla [inkommande](#inbound) *och* [utgående](#outbound) IP-adresser som används av logic apps-tjänsten, baserat på de regioner där logikapparna finns. Dessa adresser visas under rubrikerna **Inkommande** och **Utgående** i det här avsnittet och sorteras efter region.

* Konfigurera brandväggen med *alla* [utgående](#outbound) IP-adresser som används av dessa anslutningsappar för att stödja de anrop som [hanterade anslutningsappar](../connectors/apis-list.md#managed-api-connectors) gör. Dessa adresser visas under rubriken **Utgående** i det här avsnittet och sorteras efter region.

* Om du vill aktivera kommunikation för logikappar som körs i en integrationstjänstmiljö (ISE) kontrollerar du att du [öppnar dessa portar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* Om dina logikappar har problem med att komma åt Azure-lagringskonton som använder [brandväggar och brandväggsregler](../storage/common/storage-network-security.md)har du [olika alternativ för att aktivera åtkomst](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  Logikappar kan till exempel inte komma åt lagringskonton som använder brandväggsregler och finns i samma region. Men om du tillåter [utgående IP-adresser för hanterade kopplingar i din region](../logic-apps/logic-apps-limits-and-config.md#outbound)kan dina logikappar komma åt lagringskonton som finns i en annan region utom när du använder Azure Table Storage- eller Azure Queue Storage-kopplingar. Om du vill komma åt tabelllagringen eller kölagringen kan du använda HTTP-utlösaren och -åtgärderna i stället. Fler alternativ finns i [Access-lagringskonton bakom brandväggar](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Inkommande IP-adresser

I det här avsnittet visas endast inkommande IP-adresser för Azure Logic Apps-tjänsten. För att minska komplexiteten när du skapar säkerhetsregler kan du också använda [servicetag](../virtual-network/service-tags-overview.md), **LogicAppsManagement**, i stället för att ange inkommande IP-adressprefix för Logikappar för varje region. Den här taggen fungerar i de regioner där Logic Apps-tjänsten är tillgänglig. Om du har Azure Government läser du [Azure Government - Inkommande IP-adresser](#azure-government-inbound).

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Azure för flera innehavare – inkommande IP-adresser

| Region med flera innehavare | IP-adress |
|---------------------|----|
| Australien, östra | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australien, sydöstra | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasilien, södra | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Kanada, centrala | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Kanada, östra | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Indien, centrala | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| USA, centrala | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Asien, östra | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| USA, östra | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| USA, östra 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Frankrike, centrala | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Frankrike, södra | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Japan, östra | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan, västra | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Sydkorea, centrala | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Sydkorea, södra | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| USA, norra centrala | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa, norra | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Sydafrika North | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Sydafrika Väst | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| USA, södra centrala | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| Indien, södra | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Sydostasien | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Storbritannien, södra | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Storbritannien, västra | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| USA, västra centrala | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa, västra | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| Indien, västra | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| USA, västra | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| USA, västra 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government - Inkommande IP-adresser

| Region Azure Government | IP-adress |
|-------------------------|----|
| US Gov, Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov, Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov, Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| USA DoD, centrala | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Utgående IP-adresser

I det här avsnittet visas de utgående IP-adresserna för Azure Logic Apps-tjänsten och hanterade kopplingar. För att minska komplexiteten när du skapar säkerhetsregler kan du också använda [tjänsttaggen](../virtual-network/service-tags-overview.md) **LogicApps**i stället för att ange utgående IP-adressprefix för Logic Apps för varje region. Den här taggen fungerar i de regioner där Logic Apps-tjänsten är tillgänglig. Använd IP-adresserna för hanterade kopplingar. Om du har Azure Government läser du [Azure Government - Outbound IP-adresser](#azure-government-outbound).

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Azure för flera innehavare – utgående IP-adresser

| Region | IP för logikappar | IP för hanterade kopplingar |
|--------|---------------|-----------------------|
| Australien, östra | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Australien, sydöstra | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Brasilien, södra | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Kanada, centrala | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Kanada, östra | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| Indien, centrala | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| USA, centrala | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Asien, östra | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| USA, östra | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139 |
| USA, östra 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Frankrike, centrala | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Frankrike, södra | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Japan, östra | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Japan, västra | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Sydkorea, centrala | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Sydkorea, södra | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| USA, norra centrala | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Europa, norra | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Sydafrika North | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Sydafrika Väst | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| USA, södra centrala | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| Indien, södra | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Sydostasien | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Storbritannien, södra | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Storbritannien, västra | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| USA, västra centrala | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Europa, västra | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| Indien, västra | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| USA, västra | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| USA, västra 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government - Utgående IP-adresser

| Region | IP för logikappar | IP för hanterade kopplingar |
|--------|---------------|-----------------------|
| US Gov, Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91 |
| US Gov, Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225 |
| US Gov, Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
| USA DoD, centrala | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136 |
||||

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [skapar din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Läs mer om [vanliga exempel och scenarier](../logic-apps/logic-apps-examples-and-scenarios.md)
