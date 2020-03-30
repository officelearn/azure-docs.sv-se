---
title: HTTP-datainsamlare för Azure-övervakare | Microsoft-dokument
description: Du kan använda AZURE Monitor HTTP Data Collector API för att lägga till POST JSON-data i en Log Analytics-arbetsyta från alla klienter som kan anropa REST API. I den här artikeln beskrivs hur du använder API:et och exempel på hur du publicerar data med hjälp av olika programmeringsspråk.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/01/2019
ms.openlocfilehash: f12e9e90b99a055945c34398ff5351334c344253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666760"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Skicka loggdata till Azure Monitor med HTTP Data Collector API (offentlig förhandsversion)
Den här artikeln visar hur du använder HTTP Data Collector API för att skicka loggdata till Azure Monitor från en REST API-klient.  Den beskriver hur du formaterar data som samlas in av skriptet eller programmet, inkluderar dem i en begäran och har den begäran som auktoriserats av Azure Monitor.  Exempel finns för PowerShell, C#och Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> HTTP-datainsamlare-API:et för Azure Monitor är i offentlig förhandsversion.

## <a name="concepts"></a>Begrepp
Du kan använda HTTP Data Collector API för att skicka loggdata till en Log Analytics-arbetsyta i Azure Monitor från alla klienter som kan anropa ett REST API.  Detta kan vara en runbook i Azure Automation som samlar in hanteringsdata från Azure eller ett annat moln, eller det kan vara ett alternativt hanteringssystem som använder Azure Monitor för att konsolidera och analysera loggdata.

Alla data på log analytics-arbetsytan lagras som en post med en viss posttyp.  Du formaterar dina data så att de skickas till HTTP-datainsamlare-API:et som flera poster i JSON.  När du skickar data skapas en enskild post i databasen för varje post i nyttolasten för begäran.


![Översikt över HTTP-datainsamlare](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Skapa en begäran
Om du vill använda API:et för HTTP-datainsamlare skapar du en POST-begäran som innehåller data som ska skickas i JSON (JavaScript Object Notation).  I de följande tre tabellerna visas de attribut som krävs för varje begäran. Vi beskriver varje attribut mer i detalj senare i artikeln.

### <a name="request-uri"></a>URI för förfrågan
| Attribut | Egenskap |
|:--- |:--- |
| Metod |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Innehållstyp |application/json |

### <a name="request-uri-parameters"></a>Begär URI-parametrar
| Parameter | Beskrivning |
|:--- |:--- |
| CustomerID |Den unika identifieraren för log analytics-arbetsytan. |
| Resurs |API-resursnamn: /api/logs. |
| API-version |Den version av API:et som ska användas med den här begäran. För närvarande är det 2016-04-01. |

### <a name="request-headers"></a>Begärandehuvuden
| Huvud | Beskrivning |
|:--- |:--- |
| Auktorisering |Auktoriseringssignaturen. Senare i artikeln kan du läsa om hur du skapar en HMAC-SHA256 header. |
| Loggtyp |Ange posttypen för de data som skickas. Det går bara att innehålla bokstäver, siffror och understreck (_), och får inte överstiga 100 tecken. |
| x-ms-datum |Det datum då begäran bearbetades i RFC 1123-format. |
| x-ms-AzureResourceId | Resurs-ID för Azure-resursen som data ska associeras med. Detta fyller i [egenskapen _ResourceId](log-standard-properties.md#_resourceid) och gör att data kan inkluderas i [resurskontextfrågor.](design-logs-deployment.md#access-mode) Om det här fältet inte anges inkluderas inte data i resurskontextfrågor. |
| tidsgenererat fält | Namnet på ett fält i data som innehåller tidsstämpeln för dataobjektet. Om du anger ett fält används dess innehåll för **TimeGenerated**. Om det här fältet inte anges är standardinställningen för **TimeGenerated** den tid som meddelandet förtärs. Innehållet i meddelandefältet ska följa ISO 8601-formatet YYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Auktorisering
Alla förfrågningar till HTTP-datainsamlare-API:et för Azure Monitor måste innehålla ett auktoriseringshuvud. Om du vill autentisera en begäran måste du signera begäran med antingen den primära eller den sekundära nyckeln för arbetsytan som gör begäran. Skicka sedan signaturen som en del av begäran.   

Här är formatet för auktoriseringshuvudet:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* är den unika identifieraren för Log Analytics-arbetsytan. *Signatur* är en [Hash-baserad meddelandeautentiseringskod (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) som konstrueras från begäran och sedan beräknas med hjälp av [SHA256-algoritmen](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Sedan kodar du den med Base64-kodning.

Använd det här formatet för att koda Signature String för **SharedKey:**

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Här är ett exempel på en signatursträng:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

När du har signatursträngen kodar du den med HMAC-SHA256-algoritmen på den UTF-8-kodade strängen och kodar sedan resultatet som Base64. Använd det här formatet:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Exemplen i nästa avsnitt har exempelkod som hjälper dig att skapa ett auktoriseringshuvud.

## <a name="request-body"></a>Begärandetext
Meddelandets brödtext måste finnas i JSON. Den måste innehålla en eller flera poster med egenskapsnamnet och värdeparen i följande format. Egenskapsnamnet kan bara innehålla bokstäver, siffror och understreck (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Du kan gruppera flera poster tillsammans i en enda begäran med hjälp av följande format. Alla poster måste vara samma posttyp.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Posttyp och egenskaper
Du definierar en anpassad posttyp när du skickar data via AZURE Monitor HTTP Data Collector API. För närvarande kan du inte skriva data till befintliga posttyper som har skapats av andra datatyper och lösningar. Azure Monitor läser inkommande data och skapar sedan egenskaper som matchar datatyperna för de värden som du anger.

Varje begäran till datainsamlare-API:et måste innehålla ett **loggtypshuvud** med namnet på posttypen. Suffixet **_CL** läggs automatiskt till det namn du anger för att skilja det från andra loggtyper som en anpassad logg. Om du till exempel anger namnet **MyNewRecordType**skapas en post med typen **MyNewRecordType_CL**. Detta säkerställer att det inte finns några konflikter mellan användarskapade typnamn och de som levereras i aktuella eller framtida Microsoft-lösningar.

Azure Monitor lägger till ett suffix i egenskapsnamnet för att identifiera en egenskaps datatyp. Om en egenskap innehåller ett null-värde inkluderas inte egenskapen i posten. I den här tabellen visas egenskapsdatatypen och motsvarande suffix:

| Egenskapsdatatyp | Suffix |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Datum/tid |_t |
| GUID (lagras som en sträng) |_g |

Vilken datatyp som Azure Monitor använder för varje egenskap beror på om posttypen för den nya posten redan finns.

* Om posttypen inte finns skapar Azure Monitor en ny med hjälp av JSON-typens slutsats för att bestämma datatypen för varje egenskap för den nya posten.
* Om posttypen finns försöker Azure Monitor skapa en ny post baserat på befintliga egenskaper. Om datatypen för en egenskap i den nya posten inte matchar och inte kan konverteras till den befintliga typen, eller om posten innehåller en egenskap som inte finns, skapar Azure Monitor en ny egenskap som har relevant suffix.

Den här inlämningsposten skapar till exempel en post med tre egenskaper, **number_d**, **boolean_b**och **string_s:**

![Exempelpost 1](media/data-collector-api/record-01.png)

Om du sedan skickade den här nästa posten, med alla värden formaterade som strängar, ändras inte egenskaperna. Dessa värden kan konverteras till befintliga datatyper:

![Exempelpost 2](media/data-collector-api/record-02.png)

Men om du sedan gjorde den här nästa inlämning, skulle Azure Monitor skapa de nya egenskaperna **boolean_d** och **string_d**. Dessa värden kan inte konverteras:

![Exempelpost 3](media/data-collector-api/record-03.png)

Om du sedan skickade följande post, innan posttypen skapades, skulle Azure Monitor skapa en post med tre egenskaper, **number_s,** **boolean_s**och **string_s**. I den här posten formateras vart och ett av de ursprungliga värdena som en sträng:

![Exempelpost 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Reserverade egenskaper
Följande egenskaper är reserverade och bör inte användas i en anpassad posttyp. Du får ett felmeddelande om din nyttolast innehåller något av dessa egenskapsnamn.

- tenant

## <a name="data-limits"></a>Databegränsningar
Det finns vissa begränsningar kring data som publiceras på Azure Monitor Data collection API.

* Maximalt 30 MB per inlägg i Azure Monitor Data Collector API. Detta är en storleksgräns för ett enda inlägg. Om data från ett enda inlägg som överstiger 30 MB bör du dela upp data till mindre segment och skicka dem samtidigt.
* Max 32 KB-gräns för fältvärden. Om fältvärdet är större än 32 kB trunkeras data.
* Rekommenderat maximalt antal fält för en viss typ är 50. Detta är en praktisk gräns ur ett användbarhets- och sökupplevelseperspektiv.  
* En tabell på en Log Analytics-arbetsyta stöder bara upp till 500 kolumner (kallas ett fält i den här artikeln). 
* Det maximala antalet tecken för kolumnnamnet är 500.

## <a name="return-codes"></a>Returkoder
HTTP-statuskoden 200 innebär att begäran har tagits emot för bearbetning. Detta indikerar att åtgärden har slutförts.

I den här tabellen visas den fullständiga uppsättningen statuskoder som tjänsten kan returnera:

| Kod | Status | Felkod | Beskrivning |
|:--- |:--- |:--- |:--- |
| 200 |OK | |Begäran har accepterats. |
| 400 |Felaktig begäran |Inaktiv kund |Arbetsytan har stängts. |
| 400 |Felaktig begäran |Ogiltigt ApiVersion |API-versionen som du angav kändes inte igen av tjänsten. |
| 400 |Felaktig begäran |InvalidCustomerId |Det angivna arbetsyte-ID:et är ogiltigt. |
| 400 |Felaktig begäran |Ogiltigdataformat |Ogiltig JSON har skickats in. Svarstexten kan innehålla mer information om hur du löser felet. |
| 400 |Felaktig begäran |Ogiltiglogtyp |Den angivna loggtypen innehöll specialtecken eller numeriska tecken. |
| 400 |Felaktig begäran |SaknasApiVersion |API-versionen har inte angetts. |
| 400 |Felaktig begäran |SaknadInnehållstyp |Innehållstypen angavs inte. |
| 400 |Felaktig begäran |SaknadLogTyp |Det gick inte att ange den nödvändiga värdeloggtypen. |
| 400 |Felaktig begäran |Innehållstyp som inte stöds |Innehållstypen angavs inte till **application/json**. |
| 403 |Förbjudet |Ogiltiga myndigheter |Det gick inte att autentisera begäran. Kontrollera att arbetsyte-ID och anslutningsnyckel är giltiga. |
| 404 |Hittades inte | | Antingen är webbadressen felaktig eller så är begäran för stor. |
| 429 |För många förfrågningar | | Tjänsten upplever en stor mängd data från ditt konto. Försök igen begäran senare. |
| 500 |Internt serverfel |Ospecificeradare |Tjänsten påträffade ett internt fel. Försök igen förfrågan. |
| 503 |Tjänsten är inte tillgänglig |Serviceovailable |Tjänsten är inte tillgänglig för att ta emot begäranden. Försök igen med din begäran. |

## <a name="query-data"></a>Söka i data
Om du vill fråga data som skickas av HTTP-datainsamlare-API:et för Azure Monitor söker du efter poster med **typ** som är lika med det **LogType-värde** som du angav, bifogat med **_CL**. Om du till exempel använde **MyCustomLog**skulle du `MyCustomLog_CL`returnera alla poster med .

## <a name="sample-requests"></a>Exempel på begäranden
I nästa avsnitt hittar du exempel på hur du skickar data till AZURE Monitor HTTP Data Collector API med hjälp av olika programmeringsspråk.

För varje exempel gör du så här för att ange variablerna för auktoriseringshuvudet:

1. Leta reda på arbetsytan Log Analytics i Azure-portalen.
2. Välj **Avancerade inställningar** och sedan anslutna **källor**.
2. Till höger om **arbetsyte-ID**markerar du kopieringsikonen och klistrar sedan in ID:et som värdet för **variabeln Kund-ID.**
3. Till höger om **Primärnyckel**markerar du kopieringsikonen och klistrar sedan in ID:et som värde för variabeln **Delad nyckel.**

Du kan också ändra variablerna för loggtypen och JSON-data.

### <a name="powershell-sample"></a>PowerShell-exempel
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C#-exempel
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2-exempel
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>Alternativ och överväganden
Medan DATA Collector API bör täcka de flesta av dina behov för att samla in frihandsdata i Azure Logs, det finns fall där ett alternativ kan krävas för att övervinna några av begränsningarna i API. Alla dina alternativ är följande, viktiga överväganden ingår:

| Alternativ | Beskrivning | Passar bäst för |
|---|---|---|
| [Anpassade händelser:](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties)Inbyggt SDK-baserat intag i Application Insights | Application Insights, vanligtvis instrumenterad via en SDK i ditt program, erbjuder möjligheten för dig att skicka anpassade data via anpassade händelser. | <ul><li> Data som genereras i ditt program, men inte plockas upp av SDK via en av standarddatatyperna (begäranden, beroenden, undantag och så vidare).</li><li> Data som oftast är korrelerade med andra programdata i Application Insights </li></ul> |
| API för datainsamlare i Azure Monitor-loggar | DatainsamlareNS API i Azure Monitor Logs är ett helt öppet sätt att få in data. Alla data som är formaterade i ett JSON-objekt kan skickas hit. När den har skickats kommer den att bearbetas och vara tillgänglig i Loggar för att korreleras med andra data i Loggar eller mot andra Application Insights-data. <br/><br/> Det är ganska enkelt att ladda upp data som filer till en Azure Blob-blob, varifrån dessa filer kommer att bearbetas och överföras till Log Analytics. Se [den här](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) artikeln för ett exempel på implementering av en sådan pipeline. | <ul><li> Data som inte nödvändigtvis genereras i ett program som instrumenteras i Application Insights.</li><li> Exempel på detta är uppslags- och faktatabeller, referensdata, föraggregerad statistik och så vidare. </li><li> Avsedd för data som kommer att korsrefereras mot andra Azure Monitor-data (Application Insights, andra loggar datatyper, Security Center, Azure Monitor för behållare/virtuella datorer och så vidare). </li></ul> |
| [Azure-datautforskaren](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (ADX) är den dataplattform som driver Application Insights Analytics och Azure Monitor Logs. Nu allmänt tillgänglig ("GA"), med hjälp av dataplattformen i sin råa form ger dig fullständig flexibilitet (men kräver omkostnader för hantering) över klustret (RBAC, kvarhållningshastighet, schema och så vidare). ADX innehåller många [inmatningsalternativ,](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) inklusive [CSV-, TSV- och JSON-filer.](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) | <ul><li> Data som inte kommer att korreleras till andra data under Application Insights eller Logs. </li><li> Data som kräver avancerade inmatnings- eller bearbetningsfunktioner som inte är tillgängliga i dag i Azure Monitor Logs. </li></ul> |


## <a name="next-steps"></a>Nästa steg
- Använd [loggsök-API:et](../log-query/log-query-overview.md) för att hämta data från log analytics-arbetsytan.

- Läs mer om hur [du skapar en datapipeline med datainsamlare-API:et](create-pipeline-datacollector-api.md) med hjälp av Logic Apps-arbetsflödet till Azure Monitor.
