---
title: Logga Analytics HTTP datainsamlaren API | Microsoft Docs
description: 'Du kan använda Log Analytics HTTP Data Collector API för att lägga till POST JSON-data i logganalys-databasen från klienter som kan anropa REST-API. Den här artikeln beskriver hur du använder API: et och har exempel på hur du publicerar data med hjälp av olika programmeringsspråk.'
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
ms.topic: article
ms.date: 05/03/2018
ms.author: bwren
ms.openlocfilehash: d42069e8ed72a834973b56df55488955d62e71f2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Skicka data till logganalys med HTTP-Data Collector API (förhandsversion)
Den här artikeln visar hur du använder HTTP-Data Collector API för att skicka data till logganalys från en REST API-klient.  Det beskriver hur du formatera data som samlas in av skript eller program, inkludera den i en begäran och har den begäran som auktoriserad genom logganalys.  Exempel för för PowerShell, C# och Python.

> [!NOTE]
> Log Analytics HTTP Data Collector API är tillgänglig som förhandsversion.

## <a name="concepts"></a>Begrepp
Du kan använda HTTP Data Collector-API: et för att skicka data till logganalys från klienter som kan anropa REST-API.  Detta kan bero på en runbook i Azure Automation som samlar in hantering av data från Azure eller ett annat moln, eller så kan vara ett alternativ-system som använder Log Analytics för att sammanställa och analysera data.

Alla data i logganalys-databasen lagras som en post med en viss posttyp.  Du kan formatera dina data ska skickas till http-Data Collector API som flera poster i JSON.  När du skickar data, skapas en enskild post i databasen för varje post i nyttolasten i begäran.


![Översikt över HTTP-datainsamlare](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Skapa en förfrågan
Om du vill använda HTTP Data Collector-API: et kan du skapa en POST-begäran som innehåller data som ska skickas i JavaScript Object Notation (JSON).  I följande tre tabeller anges de attribut som krävs för varje begäran. Vi beskriver varje attribut i detalj senare i artikeln.

### <a name="request-uri"></a>Förfrågans URI
| Attribut | Egenskap |
|:--- |:--- |
| Metod |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Innehållstyp |application/json |

### <a name="request-uri-parameters"></a>URI-parametrar för begäran
| Parameter | Beskrivning |
|:--- |:--- |
| CustomerID |Den unika identifieraren för logganalys-arbetsytan. |
| Resurs |Resursnamnet API: / api/logs. |
| API-Version |Versionen av API: et för användning med denna begäran. Det är för närvarande 2016-04-01. |

### <a name="request-headers"></a>Begärandehuvud
| Sidhuvud | Beskrivning |
|:--- |:--- |
| Auktorisering |Signaturen för auktorisering. Senare i artikeln kan du läsa om hur du skapar ett HMAC SHA256-huvud. |
| Typ |Ange posttyp för de data som skickas. Loggtyp stöder för närvarande endast alfanumeriska tecken. Stöder inte siffror eller specialtecken. Storleksgränsen för den här parametern är 100 tecken. |
| x-ms-date |Det datum då begäran bearbetades i RFC 1123 format. |
| tid genererade fält |Namnet på ett fält i de data som innehåller dataobjektet tidsstämpel. Om du anger ett fält och dess innehåll används för **TimeGenerated**. Om det här fältet har inte angetts, standard för **TimeGenerated** är den tid som meddelandet inhämtas. Innehållet i fältet meddelande bör följa ISO 8601-formatet ÅÅÅÅ-MM-ddTHH. |

## <a name="authorization"></a>Auktorisering
Alla förfrågningar till Log Analytics HTTP Data Collector API måste innehålla ett authorization-huvud. För att autentisera en begäran, måste du registrera begäran med primärt eller den sekundära nyckeln för den arbetsyta som begäran kommer ifrån. Sedan överföra signaturen som en del av begäran.   

Här är formatet för authorization-huvud:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* är den unika identifieraren för logganalys-arbetsytan. *Signaturen* är en [hashbaserad meddelandeautentiseringskod (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) som skapas från begäran och sedan beräknas med hjälp av den [SHA256-algoritmen](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Sedan, koda den med hjälp av Base64-kodning.

Använd följande format för att koda den **SharedKey** signatur sträng:

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

När du har en signatur-sträng, koda med hjälp av HMAC SHA256-algoritmen på UTF-8-kodad sträng och koda resultatet som Base64. Använd följande format:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Exemplen i nästa avsnitt har exempelkod för att skapa ett authorization-huvud.

## <a name="request-body"></a>Begärandetext
Innehållet i meddelandet måste vara i JSON. Det måste innehålla en eller flera poster med egenskapen namn och värdepar i det här formatet:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Du kan batch flera poster tillsammans i en enskild begäran med hjälp av följande format. Alla poster måste vara samma posttyp.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Posttyp och egenskaper
Du kan definiera en anpassad posttyp när du skickar data via Log Analytics HTTP Data Collector API. För närvarande skrivning inte av data till befintliga-posttyper som har skapats av andra typer av data och lösningar. Logganalys läser inkommande data och skapar sedan egenskaper som matchar datatyper med värden som du anger.

Varje begäran Log Analytics-API: et måste innehålla en **loggtyp** huvud med namnet för typ av post. Suffixet **_CL** läggs automatiskt till namnet du anger för att skilja den från andra typer av loggen som en anpassad logg. Om du anger namnet till exempel **MyNewRecordType**, logganalys skapas en post med typen **MyNewRecordType_CL**. Detta säkerställer att det inte finns några konflikter mellan användarskapade typnamn och de levereras i aktuellt eller framtida Microsoft solutions.

Lägger till ett suffix till egenskapsnamnet för att identifiera en egenskapens datatyp logganalys. Om en egenskap innehåller ett null-värde, ingår inte egenskapen i posten. Den här tabellen innehåller egenskapsdatatyp och motsvarande suffix:

| Datatypen för egenskapen | Suffix |
|:--- |:--- |
| Sträng |_S |
| Boolesk |_b |
| Dubbel |_D |
| Datum/tid |_Tätt |
| GUID |_g |

Datatypen som Log Analytics använder för varje egenskap beror på om posttypen för den nya posten redan finns.

* Om typ av post inte finns, skapar en ny logganalys. Log Analytics använder JSON-typhärledning för att fastställa datatypen för varje egenskap för den nya posten.
* Om typ av post finns försöker logganalys skapa en ny post baserat på befintliga egenskaper. Om datatypen för en egenskap i den nya posten matchar inte och kan inte konverteras till den befintliga typen, eller om posten innehåller en egenskap som inte finns, Log Analytics skapar en ny egenskap som har suffixet relevanta.

Skicka posten skulle till exempel skapa en post med tre egenskaper **number_d**, **boolean_b**, och **string_s**:

![Exempelpost 1](media/log-analytics-data-collector-api/record-01.png)

Om du sedan skickat den här nästa post med alla värden som är formaterade som strängar, skulle det inte att ändra egenskaperna. Dessa värden kan konverteras till befintliga datatyper:

![Exempelpost 2](media/log-analytics-data-collector-api/record-02.png)

Men om du har gjort den här nästa skickas sedan Log Analytics skapar nya egenskaper **boolean_d** och **string_d**. Dessa värden kan inte konverteras:

![Exempelpost 3](media/log-analytics-data-collector-api/record-03.png)

Om du sedan skickat följande post innan typ av post skapades logganalys skulle skapa en post med tre egenskaper **antal_l**, **boolean_s**, och **string_s**. I den här posten formateras var och en av de initiala värdena som en sträng:

![Exempelpost 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Databegränsningar
Det finns vissa begränsningar runt data som skickats till samlingen Log Analytics-Data API.

* Högst 30 MB per post till Log Analytics Data Collector API: et. Det här är en storleksgräns för en enskild post. Om data från en enda bokför som överskrider 30 MB, bör du dela upp data till mindre storlek segment och skicka dem samtidigt.
* Högst 32 KB-gränsen för fältvärden. Om värdet är större än 32 KB, kommer data att trunkeras.
* Rekommenderade maximala antalet fält för en viss typ är 50. Det här är en praktisk gräns från en användbarhet och Sök upplevelse perspektiv.  

## <a name="return-codes"></a>Returkoder
HTTP-statuskod 200 innebär att begäran har tagits emot för bearbetning. Detta anger att åtgärden har slutförts.

Den här tabellen innehåller en fullständig uppsättning statuskoder som tjänsten kan returnera:

| Kod | Status | Felkod | Beskrivning |
|:--- |:--- |:--- |:--- |
| 200 |Ok | |Begäran har accepterats. |
| 400 |Felaktig förfrågan |InactiveCustomer |Arbetsytan har stängts. |
| 400 |Felaktig förfrågan |InvalidApiVersion |API-version som du angett kunde inte identifieras av tjänsten. |
| 400 |Felaktig förfrågan |InvalidCustomerId |Arbetsyte-ID som angetts är ogiltigt. |
| 400 |Felaktig förfrågan |InvalidDataFormat |Ogiltigt JSON har skickats. Svarstexten kan innehålla mer information om hur du åtgärdar felet. |
| 400 |Felaktig förfrågan |InvalidLogType |Loggtypen som angetts innehåller specialtecken eller siffror. |
| 400 |Felaktig förfrågan |MissingApiVersion |API-versionen har inte angetts. |
| 400 |Felaktig förfrågan |MissingContentType |Content-type har inte angetts. |
| 400 |Felaktig förfrågan |MissingLogType |Loggtyp obligatoriskt värde har angetts. |
| 400 |Felaktig förfrågan |UnsupportedContentType |Content-type har inte angetts **application/json**. |
| 403 |Förbjudna |InvalidAuthorization |Det gick inte att autentisera begäran. Kontrollera att arbetsytans ID och anslutningen är giltig. |
| 404 |Kunde inte hittas | | Antingen den URL som är felaktig eller begäran är för stor. |
| 429 |För många begäranden | | En stor mängd data från ditt konto har uppstått med tjänsten. Försök begäran senare. |
| 500 |Internt serverfel |UnspecifiedError |Tjänsten påträffade ett internt fel. Försök att utföra begäran. |
| 503 |Tjänsten är inte tillgänglig |ServiceUnavailable |Tjänsten är för närvarande inte ta emot begäranden. Försök att utföra din begäran. |

## <a name="query-data"></a>Frågedata
Att fråga efter data skickas av Log Analytics HTTP Data Collector API, söka efter poster med **typen** som är lika med den **LogType** värde som du angav läggas till med **_CL**. Om du använde exempelvis **MyCustomLog**, och du vill returnera alla poster med **typ = MyCustomLog_CL**.

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), sedan frågan ovan skulle ändra till följande.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Exempel begäranden
I följande avsnitt hittar du exempel på hur du skickar data till Log Analytics HTTP Data Collector API med hjälp av olika programmeringsspråk.

För varje prov gör du dessa steg för att ange variabler för authorization-huvud:

1. Leta upp logganalys-arbetsytan i Azure-portalen.
2. Välj **avancerade inställningar** och sedan **anslutna källor**.
2. Till höger om **arbetsyte-ID**kopiera-ikonen och välj sedan klistra in ID som värde för den **kund-ID** variabeln.
3. Till höger om **primärnyckel**kopiera-ikonen och välj sedan klistra in ID som värde för den **delad nyckel** variabeln.

Du kan också ändra variablerna för den typ av och JSON-data.

### <a name="powershell-sample"></a>PowerShell-exempel
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


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
        -fileName $fileName `
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
```
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

### <a name="python-sample"></a>Python-exempel
```
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
- Använd den [loggen Sök API](log-analytics-log-search-api.md) att hämta data från logganalys-databasen.
