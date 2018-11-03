---
title: Logga Analytics http-API för datainsamling | Microsoft Docs
description: 'Du kan använda Log Analytics HTTP Data Collector API för att lägga till POST JSON-data i Log Analytics-databasen från alla klienter som kan anropa REST-API. Den här artikeln beskriver hur du använder API: et och har exempel på hur du publicerar data med hjälp av olika programmeringsspråk.'
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: cedebbe1ba0dcf40af0f2f001f022ffaad175c70
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959638"
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Skicka data till Log Analytics med HTTP Data Collector API (förhandsversion)
Den här artikeln visar hur du använder HTTP Data Collector API för att skicka data till Log Analytics från en REST API-klient.  Den beskriver hur du formatera data som samlas in från dina skript eller ett program, inkludera den i en begäran och få den begäran som auktoriserats av Log Analytics.  Exempel tillhandahålls för PowerShell, C# och Python.

> [!NOTE]
> Log Analytics HTTP Data Collector API finns i offentlig förhandsversion.

## <a name="concepts"></a>Begrepp
Du kan använda HTTP Data Collector API för att skicka data till Log Analytics från alla klienter som kan anropa en REST-API.  Det kan vara en runbook i Azure Automation som samlar in hantering av data från Azure eller ett annat moln, eller så kanske ett alternativt system som använder Log Analytics att konsolidera och analysera data.

Alla data i Log Analytics-databasen lagras som en post med en viss posttyp.  Du kan formatera dina data att skicka till HTTP Data Collector API som flera poster i JSON.  När du skickar in data, skapas en enskild post i databasen för varje post i nyttolasten för begäran.


![HTTP Data Collector översikt](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Skapa en förfrågan
Om du vill använda HTTP Data Collector API måste skapa du en POST-begäran som innehåller data som skickas i JavaScript Object Notation (JSON).  I följande tre tabeller anges de attribut som krävs för varje begäran. Vi beskriver varje attribut i detalj senare i artikeln.

### <a name="request-uri"></a>Förfrågans URI
| Attribut | Egenskap  |
|:--- |:--- |
| Metod |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Innehållstyp |application/json |

### <a name="request-uri-parameters"></a>Begäran-URI-parametrar
| Parameter | Beskrivning |
|:--- |:--- |
| CustomerID |Den unika identifieraren för Log Analytics-arbetsytan. |
| Resurs |Resursnamnet API: / api/logs. |
| API-version |Versionen av API för användning med den här begäran. Det är för närvarande 2016-04-01. |

### <a name="request-headers"></a>Begärandehuvud
| Huvud | Beskrivning |
|:--- |:--- |
| Auktorisering |Signatur för auktorisering. Senare i artikeln kan du läsa om hur du skapar en HMAC-SHA256-rubrik. |
| Loggtyp |Ange posttypen för de data som skickas. Loggtypen stöder för närvarande endast alfanumeriska tecken. Det stöder inte numeriska värden eller specialtecken. Storleksgränsen för den här parametern är 100 tecken. |
| x-ms-date |Det datum då begäran bearbetades i RFC 1123 format. |
| tid genererade fält |Namnet på ett fält i de data som innehåller tidsstämpeln för dataobjektet. Om du anger ett fält så att innehållet används för **TimeGenerated**. Om det här fältet har inte angetts standard för **TimeGenerated** är den tid som matas in meddelandet. Innehållet i fältet meddelande bör följa ISO 8601-formatet ÅÅÅÅ-MM-: ssZ. |

## <a name="authorization"></a>Auktorisering
Varje Log Analytics HTTP Data Collector API-begäran måste innehålla en auktoriseringsrubrik. För att autentisera en begäran, måste du logga på begäran med antingen primärt eller sekundära nyckeln för den arbetsyta som gör begäran. Skicka sedan den signaturen som en del av begäran.   

Här är formatet för auktoriseringsrubriken:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* är den unika identifieraren för Log Analytics-arbetsytan. *Signaturen* är en [hashbaserad meddelandeautentiseringskod (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) som skapas från begäran och sedan beräknas med hjälp av den [SHA256-algoritmen](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Sedan kan kodar du den med hjälp av Base64-kodning.

Använd det här formatet för att koda den **SharedKey** signatur sträng:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Här är ett exempel på en signatur-sträng:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

När du har signatur-strängen kan koda med hjälp av HMAC-SHA256-algoritmen på UTF-8-kodad sträng och koda resultatet som Base64. Använd det här formatet:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Exemplen i nästa avsnitt har exempelkod för att skapa en auktoriseringsrubrik.

## <a name="request-body"></a>Begärandetext
Meddelandets brödtext måste vara i JSON. Det måste innehålla en eller flera poster med egenskapen namn och värdepar i följande format:

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

Du kan batch flera poster i en enskild begäran med hjälp av följande format. Alla poster måste vara samma posttyp.

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
Du kan definiera en anpassad posttyp när du skickar data med hjälp av Log Analytics HTTP Data Collector API. Du kan inte för närvarande kan skriva data till befintliga posttyper som har skapats av andra datatyper och lösningar. Log Analytics läser inkommande data och skapar sedan egenskaper som matchar datatyperna för de värden som du anger.

Varje begäran till Log Analytics-API måste innehålla en **loggtyp** rubrik med namnet för posttypen. Suffixet **_CL** läggs automatiskt till namnet som du anger för att skilja den från andra loggtyper som en anpassad logg. Exempel: Om du anger namnet **MyNewRecordType**, Log Analytics skapas en post med typen **MyNewRecordType_CL**. Detta hjälper till att säkerställa att det inte finns några konflikter mellan användarskapade namn och de levereras i aktuellt eller framtida Microsoft-lösningar.

Lägger till ett suffix till egenskapsnamnet för att identifiera datatypen för en egenskap, Log Analytics. Om en egenskap innehåller ett null-värde, ingår inte egenskapen i posten. Den här tabellen anger typ av egenskapsdata och motsvarande suffix:

| Typ av egenskapsdata | Suffix |
|:--- |:--- |
| Sträng |_S |
| Boolesk |_b |
| Double-värde |_D |
| Datum/tid |_Tätt |
| GUID |_g |

Datatypen som Log Analytics använder för varje egenskap beror på om posttypen för den nya posten redan finns.

* Om posttypen inte finns, skapar en ny Log Analytics. Log Analytics använder inferens för JSON-typ för att fastställa datatypen av för varje egenskap för den nya posten.
* Om typ av post finns försöker Log Analytics skapa en ny post baserat på befintliga egenskaper. Om datatypen för en egenskap i den nya posten matchar inte och kan inte konverteras till den befintliga typen, eller om posten innehåller en egenskap som inte finns, Log Analytics skapar en ny egenskap som har det relevanta suffixet.

Skicka posten skulle till exempel skapa en post med tre egenskaper **number_d**, **boolean_b**, och **string_s**:

![Exempelpost 1](media/log-analytics-data-collector-api/record-01.png)

Om du sedan skickat den här nästa post med alla värden som är formaterade som strängar, skulle det inte att ändra egenskaperna. Dessa värden kan konverteras till befintliga datatyper:

![Exempelpost 2](media/log-analytics-data-collector-api/record-02.png)

Men om du har gjort den här nästa skickas sedan Log Analytics skapar de nya egenskaperna **boolean_d** och **string_d**. Dessa värden kan inte konverteras:

![Exempelpost 3](media/log-analytics-data-collector-api/record-03.png)

Om du skickat sedan följande post innan typ av post skapades, Log Analytics skulle skapa en post med tre egenskaper **antal_l**, **boolean_s**, och **string_s**. I den här posten formateras var och en av de initiala värdena som en sträng:

![Exempelpost 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Databegränsningar
Det finns vissa begränsningar kring data som skickats till Log Analytics-Data samling API: et.

* Högst 30 MB per post till Log Analytics Data Collector API. Det här är en storleksgräns för ett enskilt inlägg. Om data från en enda bokför som överskrider 30 MB, bör du dela upp data till mindre storlek segment och skicka dem samtidigt.
* Högst 32 KB-gränsen för fältvärden. Om fältets värde är större än 32 KB trunkeras data.
* Rekommenderade maximala antalet fält för en viss typ är 50. Det här är en praktisk gräns från en användbarhet och Sök upplevelse perspektiv.  

## <a name="return-codes"></a>Returkoder
HTTP-statuskod 200 innebär att förfrågan har tagits emot för bearbetning. Detta anger att åtgärden har slutförts.

Den här tabellen innehåller en fullständig uppsättning med statuskoder som kan returnera tjänsten:

| Kod | Status | Felkod | Beskrivning |
|:--- |:--- |:--- |:--- |
| 200 |Ok | |Begäran har accepterats. |
| 400 |Felaktig förfrågan |InactiveCustomer |Arbetsytan har stängts. |
| 400 |Felaktig förfrågan |InvalidApiVersion |API-version som du angav kändes inte av tjänsten. |
| 400 |Felaktig förfrågan |InvalidCustomerId |Arbetsyte-ID som angetts är ogiltig. |
| 400 |Felaktig förfrågan |InvalidDataFormat |Ogiltig JSON har skickats. Svarstexten kan innehålla mer information om hur du åtgärdar felet. |
| 400 |Felaktig förfrågan |InvalidLogType |Loggtypen anges inneslutna specialtecken eller siffror. |
| 400 |Felaktig förfrågan |MissingApiVersion |API-versionen har inte angetts. |
| 400 |Felaktig förfrågan |MissingContentType |Innehållstypen har inte angetts. |
| 400 |Felaktig förfrågan |MissingLogType |Loggtyp obligatoriskt värde har inte angetts. |
| 400 |Felaktig förfrågan |UnsupportedContentType |Innehållstyp angavs inte **application/json**. |
| 403 |Förbjudna |InvalidAuthorization |Tjänsten kunde inte autentisera begäran. Kontrollera att arbetsytenyckeln-ID och anslutningen är giltiga. |
| 404 |Kunde inte hittas | | Antingen av Webbadressen som tillhandahålls är felaktig eller begäran är för stor. |
| 429 |För många begäranden | | En stor mängd data från ditt konto har uppstått med tjänsten. Försök begäran igen senare. |
| 500 |Internt serverfel |UnspecifiedError |Ett internt fel inträffade i tjänsten. Försök med förfrågan. |
| 503 |Tjänsten är inte tillgänglig |ServiceUnavailable |Tjänsten är för närvarande inte ta emot begäranden. Försök igen. |

## <a name="query-data"></a>Söka i data
Att köra frågor mot data som skickats av den Log Analytics HTTP Data Collector API, söka efter poster med **typ** som är lika med den **LogType** värde som du angav läggas till med **_CL**. Exempel: Om du använde **MyCustomLog**, och du kommer att returnera alla poster med **typ = MyCustomLog_CL**.

>[!NOTE]
> Om din arbetsyta har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search.md), och sedan frågan ovan skulle ändras till följande.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Exempelförfrågan
I nästa avsnitt hittar du exempel på hur du skickar data till Log Analytics HTTP Data Collector API med hjälp av olika programmeringsspråk.

För varje prov, gör du dessa steg för att ange variabler för auktoriseringsrubriken:

1. Leta upp din Log Analytics-arbetsyta i Azure-portalen.
2. Välj **avancerade inställningar** och sedan **anslutna källor**.
2. Till höger om **arbetsyte-ID**, väljer du ikonen för kopiera och klistra in ID: T som värde för den **kund-ID** variabeln.
3. Till höger om **primärnyckel**, väljer du ikonen för kopiera och klistra in ID: T som värde för den **delad nyckel** variabeln.

Du kan också ändra variablerna för loggtyp och JSON-data.

### <a name="powershell-sample"></a>PowerShell-exempel
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
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

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
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

## <a name="next-steps"></a>Nästa steg
- Använd den [Loggsöknings-API](log-analytics-log-search.md) att hämta data från Log Analytics-databasen.

- Läs mer om hur [skapa en datapipeline med Data Collector API](log-analytics-create-pipeline-datacollector-api.md) med Logic Apps-arbetsflöde till Log Analytics.
