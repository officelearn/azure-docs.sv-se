---
title: Azure Storage referens för övervaknings data | Microsoft Docs
description: Logg och statistik referens för övervakning av data från Azure Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.openlocfilehash: 7ba66441a87e3e02483ae27400f9900d2d052af4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118166"
---
# <a name="azure-storage-monitoring-data-reference"></a>Azure Storage övervaknings data referens

Mer information om hur du samlar in och analyserar övervaknings data för Azure Storage finns i [övervaknings Azure Storage](monitor-storage.md) .

## <a name="metrics"></a>Mått

I följande tabeller visas de plattforms mått som samlas in för Azure Storage. 

### <a name="capacity-metrics"></a>Kapacitetsmått

Värdena för kapacitets värden skickas till Azure Monitor varje timme. Värdena uppdateras varje dag. Tids kornig het definierar tidsintervallet för vilka mått värden presenteras. Tids kornig het för alla kapacitets mått är en timme (PT1H).

Azure Storage tillhandahåller följande kapacitets mått i Azure Monitor.

#### <a name="account-level"></a>Konto nivå

| Mått | Beskrivning |
| ------------------- | ----------------- |
| UsedCapacity | Mängden lagrings utrymme som används av lagrings kontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium-lagringskonton och Blob Storage-konton är det samma sak som BlobCapacity. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

#### <a name="blob-storage"></a>Blob Storage

| Mått | Beskrivning |
| ------------------- | ----------------- |
| BlobCapacity | Summan av Blob Storage som används i lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 <br/> Dimensioner: **BlobType**och **BlobTier** ([definition](#metrics-dimensions)) |
| BlobCount    | Antalet BLOB-objekt som lagras i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 <br/> Dimensioner: **BlobType**och **BlobTier** ([definition](#metrics-dimensions)) |
| ContainerCount    | Antalet behållare i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| IndexCapacity     | Mängden lagrings utrymme som används av ADLS Gen2 hierarkiskt index <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

#### <a name="table-storage"></a>Table Storage

| Mått | Beskrivning |
| ------------------- | ----------------- |
| TableCapacity | Mängden tabell lagring som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| TableCount   | Antalet tabeller i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| TableEntityCount | Antalet tabell enheter i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

#### <a name="queue-storage"></a>Queue Storage

| Mått | Beskrivning |
| ------------------- | ----------------- |
| QueueCapacity | Mängden Queue Storage som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| QueueCount   | Antalet köer i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| QueueMessageCount | Antalet meddelanden som inte har förfallit i lagrings kontot. <br/><br/>Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

#### <a name="file-storage"></a>File Storage

| Mått | Beskrivning |
| ------------------- | ----------------- |
| FileCapacity | Mängden fil lagring som används av lagrings kontot. <br/><br/> Enhet: byte <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileCount   | Antalet filer i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |
| FileShareCount | Antalet fil resurser i lagrings kontot. <br/><br/> Enhet: antal <br/> Sammansättnings typ: genomsnitt <br/> Värde exempel: 1024 |

### <a name="transaction-metrics"></a>Transaktionsmått

Transaktions mått genereras på varje begäran till ett lagrings konto från Azure Storage till Azure Monitor. Om det inte finns någon aktivitet på ditt lagrings konto kommer det inte att finnas några data om transaktions måtten under perioden. Alla transaktions mått är tillgängliga både på konto-och service nivå (Blob Storage, Table Storage, Azure Files och Queue Storage). Tids kornig het definierar tidsintervallet som Mät värden presenteras. Tids korn som stöds för alla transaktions mått är PT1H och PT1M.

Azure Storage tillhandahåller följande transaktions mått i Azure Monitor.

| Mått | Beskrivning |
| ------------------- | ----------------- |
| Transaktioner | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. <br/><br/> Enhet: antal <br/> Sammansättnings typ: totalt <br/> Tillämpliga dimensioner: ResponseType, typ av typ, ApiName och autentisering ([definition](#metrics-dimensions))<br/> Värde exempel: 1024 |
| Ingress | Mängden inkommande data. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. <br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| Utgående | Mängden utgående data. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. <br/><br/> Enhet: byte <br/> Sammansättnings typ: totalt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| SuccessServerLatency | Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency. <br/><br/> Enhet: millisekunder <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| SuccessE2ELatency | Den genomsnittliga svarstiden från slutpunkt till slutpunkt för lyckade begäranden som gjorts till en lagringstjänst eller för en angiven API-åtgärd. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. <br/><br/> Enhet: millisekunder <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 1024 |
| Tillgänglighet | Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även förfrågningar som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. <br/><br/> Enhet: procent <br/> Sammansättnings typ: genomsnitt <br/> Tillämpliga dimensioner: typ av typ, ApiName och autentisering ([definition](#metrics-dimensions)) <br/> Värde exempel: 99,99 |

<a id="metrics-dimensions" />

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Storage stöder följande dimensioner för mått i Azure Monitor.

| Dimensions namn | Beskrivning |
| ------------------- | ----------------- |
| **BlobType** | Typ av BLOB för BLOB-mått. De värden som stöds är **BlockBlob**, **PageBlob**och **Azure Data Lake Storage**. Lägg till BLOB ingår i BlockBlob. |
| **BlobTier** | Azure Storage erbjuder olika åtkomst nivåer, vilket gör att du kan lagra BLOB-Datadata på det mest kostnads effektiva sättet. Läs mer i [Azure Storage BLOB-nivå](../blobs/storage-blob-storage-tiers.md). De värden som stöds är: <br/> <li>Frekvent **: frekvent**nivå</li> <li>Låg **frekvent: låg**frekvent nivå</li> <li>**Arkiv**: Arkiv lag ring</li> <li>**Premium**: Premium-nivån för Block-Blob</li> <li>**P4/P6/P10/p15/P20/P30/P40/P50/P60**: nivå typer för Premium Page-BLOB</li> <li>**Standard**: nivå typ för standard sid-BLOB</li> <li>Inte i **nivå**: nivå typ för generell användning v1 lagrings konto</li> |
| **Typ av typ** | Transaktion från primärt eller sekundärt kluster. De tillgängliga värdena är **primär** och **sekundär**. Den gäller för läsning av Geo-redundant lagring med Läs behörighet (RA-GRS) vid läsning av objekt från en sekundär klient. |
| **ResponseType** | Typ av transaktions svar. Tillgängliga värden är: <br/><br/> <li>**ServerOtherError**: alla andra fel på Server sidan förutom de som beskrivs </li> <li>**ServerBusyError**: autentiserad begäran som returnerade en HTTP 503-status kod. </li> <li>**ServerTimeoutError**: en timeout-autentiserad begäran som returnerade en status kod för HTTP 500. Tidsgränsen överskreds på grund av ett serverfel. </li> <li>**AuthorizationError**: autentiserad begäran som misslyckades på grund av obehörig åtkomst till data eller ett auktoriseringsfel. </li> <li>**NetworkError**: autentiserad begäran som misslyckades på grund av nätverks fel. Inträffar vanligen när klienten stänger en anslutning för tidigt innan tidsgränsen. </li><li>**ClientAccountBandwidthThrottlingError**: begäran begränsas på bandbredden för att överskrida [skalbarhets gränserna för lagrings kontot](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: begäran begränsas enligt begär ande frekvens för överskridning av [skalbarhets gränser för lagrings kontot](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: andra begränsnings fel på klient sidan. ClientAccountBandwidthThrottlingError och ClientAccountRequestThrottlingError är undantagna.</li> <li>**ClientTimeoutError**: en timeout-autentiserad begäran som returnerade en status kod för HTTP 500. Om klientens tidsgränser för nätverket eller förfrågningar är inställda på lägre värden än vad lagringstjänsten förväntar sig är det en förväntad timeout. Annars rapporteras den som ett ServerTimeoutError. </li> <li>**ClientOtherError**: alla andra fel på klient sidan, förutom de som beskrivs. </li> <li>**Lyckades**: lyckad begäran</li> <li> **SuccessWithThrottling**: lyckad begäran när en SMB-klient får en begränsning i de första försöken (erna) men lyckas efter återförsök.</li> |
| **ApiName** | Åtgärdens namn. Ett exempel: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> För alla åtgärds namn, se [dokument](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autentisering** | Autentiseringstyp som används i transaktioner. Tillgängliga värden är: <br/> <li>**AccountKey**: transaktionen autentiseras med lagrings konto nyckeln.</li> <li>**SAS**: transaktionen autentiseras med signaturer för delad åtkomst.</li> <li>**OAuth**: transaktionen autentiseras med OAuth-åtkomsttoken.</li> <li>**Anonym**: transaktionen begärs anonymt. Den omfattar inte preflight-begäranden.</li> <li>**AnonymousPreflight**: transaktionen är en preflight-begäran.</li> |

För mått som stöder dimensioner måste du ange dimension svärdet för att se motsvarande mått värden. Om du till exempel tittar på **transaktions** värde för lyckade svar måste du filtrera **ResponseType** -dimensionen med **lyckat resultat**. Eller om du tittar på **BlobCount** -värdet för Block-Blob måste du filtrera **BlobType** -dimensionen med **BlockBlob**.

## <a name="resource-logs-preview"></a>Resurs loggar (förhands granskning)

> [!NOTE]
> Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. För att registrera dig i för hands versionen, se [den här sidan](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Den här för hands versionen aktiverar loggar för blobbar (inklusive Azure Data Lake Storage Gen2), filer, köer, tabeller, Premium Storage-konton i generell användning v1-och General-Purpose v2-lagrings konton. Klassiska lagrings konton stöds inte.

I följande tabell visas egenskaperna för Azure Storage resurs loggar när de samlas in i Azure Monitor loggar eller Azure Storage. Egenskaperna beskriver åtgärden, tjänsten och typen av auktorisering som användes för att utföra åtgärden.

### <a name="fields-that-describe-the-operation"></a>Fält som beskriver åtgärden

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| Egenskap | Beskrivning |
|:--- |:---|
|**tid** | UTC-tid (Universal Time Coordinated) när begäran togs emot av lagrings platsen. Till exempel: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | Resurs-ID för lagrings kontot. Exempelvis: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategori** | Kategorin för den begärda åtgärden. Till exempel: `StorageRead` , `StorageWrite` , eller `StorageDelete` .|
|**operationName** | Den typ av REST-åtgärd som utfördes. <br> En fullständig lista över åtgärder finns i [avsnittet Lagringsanalys loggade åtgärder och status meddelanden](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Den lagrings tjänst version som angavs när begäran gjordes. Detta motsvarar värdet för huvudet **x-MS-version** . Till exempel: `2017-04-17`.|
|**Schema** | Loggens schema version. Till exempel: `1.0`.|
|**statusCode** | HTTP-statuskod för begäran. Om begäran avbryts kan det här värdet vara inställt på `Unknown` . <br> Exempelvis: `206` |
|**Status text** | Status för den begärda åtgärden.  En fullständig lista över status meddelanden finns i [avsnittet Lagringsanalys loggade åtgärder och status meddelanden](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). I version 2017-04-17 och senare används inte status meddelandet `ClientOtherError` . I stället innehåller det här fältet en felkod. Exempelvis: `SASSuccess`  |
|**durationMs** | Den totala tiden, uttryckt i millisekunder, för att utföra den begärda åtgärden. Detta omfattar tiden att läsa den inkommande begäran och att skicka svaret till beställaren. Till exempel: `12`.|
|**callerIpAddress** | IP-adressen för beställaren, inklusive Port numret. Till exempel: `192.100.0.102:4362`. |
|**correlationId** | Det ID som används för att korrelera loggar över resurser. Till exempel: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**sökvägen** | Lagrings kontots plats. Till exempel: `North Europe`. |
|**protokollhanterare**|Det protokoll som används i åtgärden. Till exempel: `HTTP` , `HTTPS` , `SMB` eller`NFS`|
| **URI** | Enhetligt resurs-ID som begärs. Till exempel: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Fält som beskriver hur åtgärden autentiserades

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| Egenskap | Beskrivning |
|:--- |:---|
|**identitet/typ** | Den typ av autentisering som användes för att utföra begäran. Till exempel: `OAuth` , `SAS Key` , `Account Key` eller`Anonymous` |
|**identitets-tokenHash**|Det här fältet är reserverat för internt bruk. |
|**auktorisering/åtgärd** | Den åtgärd som har tilldelats begäran. Exempelvis: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**auktoriserings-roleAssignmentId** | Roll tilldelnings-ID: t. Till exempel: `4e2521b7-13be-4363-aeda-111111111111`.|
|**auktoriserings-roleDefinitionId** | Roll Definitions-ID: t. Till exempel: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**huvud namn/ID** | Säkerhets objektets ID. Till exempel: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekt/typ** | Typ av säkerhets objekt. Till exempel: `ServicePrincipal`. |
|**beställare/appID** | Det Open Authorization (OAuth) program-ID som används som beställare. <br> Till exempel: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**beställare/mål grupp** | OAuth-mål för begäran. Till exempel: `https://storage.azure.com`. |
|**beställare/objectId** | OAuth-objekt-ID för beställaren. I händelse av Kerberos-autentisering representerar objekt identifieraren för Kerberos-autentiserad användare. Till exempel: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**beställare/tenantId** | Identitet för OAuth-klient-ID. Till exempel: `72f988bf-86f1-41af-91ab-222222222222`.|
|**beställare/tokenIssuer** | Utfärdaren av OAuth-token. Till exempel: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**beställare/UPN** | Användarens huvud namn (UPN) för begär Ande. Till exempel: `someone@contoso.com`. |
|**beställare/användar namn** | Det här fältet är reserverat för internt bruk.|

### <a name="fields-that-describe-the-service"></a>Fält som beskriver tjänsten

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| Egenskap | Beskrivning |
|:--- |:---|
|**Konto** | Namnet på lagrings kontot. Till exempel: `mystorageaccount`.  |
|**requestUrl** | Den URL som begärs. Till exempel: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | Värdet för **User-Agent-huvudet** , inom citat tecken. Till exempel: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Värdet för **referent** -huvudet. Till exempel: `http://contoso.com/about.html`.|
|**clientRequestId** | Huvudet **x-MS-client-Request-ID** för begäran. Till exempel: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | ETag-identifieraren för det returnerade objektet, inom citat tecken. Till exempel: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Den totala tiden uttryckt i millisekunder för att utföra den begärda åtgärden. Det här värdet omfattar inte nätverks svars tiden (tiden för att läsa inkommande begäran och skicka svaret till beställaren). Till exempel: `22`. |
|**serviceType** | Tjänsten som är kopplad till den här begäran. Till exempel: `blob` , `table` , `files` eller `queue` . |
|**operationCount** | Antalet varje loggad åtgärd som ingår i begäran. Antalet börjar med ett index av `0` . Vissa begär Anden kräver mer än en åtgärd, till exempel en begäran om att kopiera en blob. De flesta förfrågningar utför bara en åtgärd. Till exempel: `1`. |
|**requestHeaderSize** | Storleken på begär ande huvudet uttryckt i byte. Till exempel: `578`. <br>Om en begäran Miss lyckas kan det här värdet vara tomt. |
|**requestBodySize** | Storleken på begär ande paket, uttryckt i byte, som läses av lagrings tjänsten. <br> Till exempel: `0`. <br>Om en begäran Miss lyckas kan det här värdet vara tomt.  |
|**responseHeaderSize** | Storleken på svars huvudet uttryckt i byte. Till exempel: `216`. <br>Om en begäran Miss lyckas kan det här värdet vara tomt.  |
|**responseBodySize** | Storleken på de svars paket som skrivs av lagrings tjänsten, i byte. Om en begäran Miss lyckas kan det här värdet vara tomt. Till exempel: `216`.  |
|**requestMd5** | Värdet för antingen **Content-MD5** -sidhuvudet eller **x-MS-Content-MD5-** huvudet i begäran. Det MD5-hash-värde som anges i det här fältet representerar innehållet i begäran. Till exempel: `788815fd0198be0d275ad329cafd1830`. <br>Det här fältet kan vara tomt.  |
|**serverMd5** | Värdet för den MD5-hash som beräknas av lagrings tjänsten. Till exempel: `3228b3cf1069a5489b298446321f8521`. <br>Det här fältet kan vara tomt.  |
|**lastModifiedTime** | Senaste ändrings tid (LMT) för det returnerade objektet.  Till exempel: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Det här fältet är tomt för åtgärder som kan returnera flera objekt. |
|**conditionsUsed** | En semikolonavgränsad lista med nyckel/värde-par som representerar ett villkor. Villkoren kan vara något av följande: <li> If-Modified-sedan <li> If-Modified-sedan <li> If-Match <li> If-None-Match  <br> Till exempel: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Värdet för Content-Length-huvudet för begäran som skickas till lagrings tjänsten. Om begäran lyckades är värdet lika med requestBodySize. Om en begäran Miss lyckas kan det här värdet inte vara lika med requestBodySize, eller så kan det vara tomt. |
|**tlsVersion** | TLS-versionen som används i begäran om anslutning. Till exempel: `TLS 1.2`. |
|**smbTreeConnectID** | SMB- **treeConnectId** (Server Message Block) upprättades vid Tree Connect-tiden. Exempelvis: `0x3` |
|**smbPersistentHandleID** | Beständigt referens-ID från en SMB2 CREATE-begäran som överleva nätverks åter anslutning.  Refereras till i [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 som **SMB2_FILEID. Beständig**. Exempelvis: `0x6003f` |
|**smbVolatileHandleID** | Beständigt referens-ID från en SMB2 CREATE-begäran som återvinns i nätverket återansluter.  Refereras till i [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 som **SMB2_FILEID. Volatile**. Exempelvis: `0xFFFFFFFF00000065` |
|**smbMessageID** | Anslutningens relativa **messageid**. Exempelvis: `0x3b165` |
|**smbCreditsConsumed** | Ingångs-eller utgångs förbrukad av begäran, i enheter om 64 kB. Exempelvis: `0x3` |
|**smbCommandDetail** | Mer information om den här begäran snarare än allmän typ av begäran. Exempelvis: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | **FileId** som är associerad med filen eller katalogen.  Ungefär detsamma som en NTFS-FileId. Exempelvis: `0x9223442405598953` |
|**smbSessionID** | SMB2 **SessionID** upprättades vid konfigurationen av sessionen. Exempelvis: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Värde i **SMB2_HEADER.-kommandot**. För närvarande är detta ett tal mellan 0 och 18. Exempelvis: `0x6` |
|**smbCommandMinor** | Underklassen för **SmbCommandMajor**, där det är lämpligt. Exempelvis: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Se även

- En beskrivning av övervaknings Azure Storage finns i [övervaknings Azure Storage](monitor-storage.md) .
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) .