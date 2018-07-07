---
title: Hur du använder en Azure VM Managed Service Identity för att hämta en åtkomsttoken
description: Stegvisa åtkomsttoken anvisningar och exempel för att använda en virtuell MSI-dator för Azure för att skaffa ett OAuth.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: e564f48b4b90cfcaa72ed51d5f210a71a4980360
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902953"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Hur du använder en Azure VM hanterad tjänstidentitet (MSI) för tokenförvärv 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

Den här artikeln innehåller olika exempel på kod och skript för tokenförvärv samt vägledning om viktiga ämnen som hantering av token upphör att gälla och HTTP-fel. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell-exempel i den här artikeln, måste du installera den senaste versionen av [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter att klienten körs på en virtuell dator med en hanterad tjänstidentitet. Använd funktionen ”ansluta” virtuell dator i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns i [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md), eller någon av varianten artiklar (med PowerShell, CLI, en mall eller ett Azure SDK). 

> [!IMPORTANT]
> - Säkerhetsgräns för en hanterad tjänstidentitet, är den resurs som används på. Alla kod/skript som körs på en virtuell dator kan begära och hämta token för alla hanterade tjänstidentiteten tillgängliga på den. 

## <a name="overview"></a>Översikt

Ett klientprogram kan begära en hanterad tjänstidentitet [appspecifika åtkomsttoken](../develop/active-directory-dev-glossary.md#access-token) för att komma åt en viss resurs. Token är [baserat på MSI-tjänstobjektet](overview.md#how-does-it-work). Därför finns inget behov för klienten att registrera sig för att hämta en åtkomsttoken under eget huvudnamn för tjänsten. Token är lämpliga för en ägartoken i [tjänst-till-tjänst-anrop som kräver klientautentiseringsuppgifter](../develop/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Hämta en token via HTTP](#get-a-token-using-http) | Information om protokoll för MSI tokenslutpunkten |
| [Hämta en token med C#](#get-a-token-using-c) | Exempel på att använda MSI REST-slutpunkt från en C#-klient |
| [Hämta en token med hjälp av Go](#get-a-token-using-go) | Exempel på att använda MSI REST-slutpunkt från en Go-klient |
| [Hämta en token med Azure PowerShell](#get-a-token-using-azure-powershell) | Exempel på att använda MSI REST-slutpunkt från en PowerShell-klient |
| [Hämta en token med CURL](#get-a-token-using-curl) | Exempel på att använda MSI REST-slutpunkt från en Bash/CURL-klient |
| [Hantering av tokencachelagring](#handling-token-caching) | Vägledning för hantering av åtkomsttoken har upphört att gälla |
| [Felhantering](#error-handling) | Vägledning för att hantera HTTP-fel som returnerades från tokenslutpunkten MSI |
| [Resurs-ID: N för Azure-tjänster](#resource-ids-for-azure-services) | Hämta resurs-ID för Azure-tjänster som stöds |

## <a name="get-a-token-using-http"></a>Hämta en token via HTTP 

Det grundläggande gränssnittet för att hämta en åtkomsttoken baseras på REST, vilket gör den tillgänglig för valfritt program som körs på den virtuella datorn som kan göra HTTP-REST-anrop. Detta liknar den Azure AD-programmeringsmodellen förutom klienten använder en slutpunkt på den virtuella datorn (jämfört med en Azure AD-slutpunkten).

Exempel på begäran med hjälp av Azure Instance Metadata Service (IMDS) slutpunkt *(rekommenderas)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, vilket innebär att du vill hämta data från slutpunkten. I det här fallet en OAuth åtkomsttoken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | MSI-slutpunkt för Instance Metadata Service. |
| `api-version`  | En frågesträngsparameter som anger API-version för IMDS-slutpunkten. Använd API-versionen `2018-02-01` eller större. |
| `resource` | En frågesträngsparameter som anger App-ID-URI för målresursen. Den visas också i de `aud` (målgrupp) anspråk i utfärdade token. Det här exemplet begär en token för att få åtkomst till Azure Resource Manager, som har en App-ID-URI för https://management.azure.com/. |
| `Metadata` | En HTTP-begäran med huvud anger krävs av MSI som en minskning mot angrepp Server sida begäran förfalskning (SSRF). Det här värdet måste anges till ”true”, i gemener.

Exempel på begäran med hjälp av hanterad tjänstidentitet (MSI) VM-tillägget slutpunkt *(för att bli inaktuell)*:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, vilket innebär att du vill hämta data från slutpunkten. I det här fallet en OAuth åtkomsttoken. | 
| `http://localhost:50342/oauth2/token` | MSI slutpunkten, där 50342 är standardporten och kan konfigureras. |
| `resource` | En frågesträngsparameter som anger App-ID-URI för målresursen. Den visas också i de `aud` (målgrupp) anspråk i utfärdade token. Det här exemplet begär en token för att få åtkomst till Azure Resource Manager, som har en App-ID-URI för https://management.azure.com/. |
| `Metadata` | En HTTP-begäran med huvud anger krävs av MSI som en minskning mot angrepp Server sida begäran förfalskning (SSRF). Det här värdet måste anges till ”true”, i gemener.


Exempelsvaret:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beskrivning |
| ------- | ----------- |
| `access_token` | Den begärda åtkomst-token. När du anropar en REST API för säker token är inbäddad i den `Authorization` begäran rubrik fältet som en ”ägartoken”, vilket gör att API för att autentisera anroparen. | 
| `refresh_token` | Används inte av MSI. |
| `expires_in` | Antal sekunder som den åtkomst-token fortsätter att vara giltig, innan du går ut från tidpunkten för utfärdande. Tid för utfärdande finns i token `iat` anspråk. |
| `expires_on` | Timespan när åtkomsttoken upphör att gälla. Det datum då representeras som hur många sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `exp` anspråk). |
| `not_before` | Timespan när åtkomsttoken träder i kraft och kan godkännas. Det datum då representeras som hur många sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `nbf` anspråk). |
| `resource` | Resursen åtkomsttoken har begärts för som matchar den `resource` frågesträngparametern för begäran. |
| `token_type` | Typ av token, som är en ”ägar” åtkomsttoken, vilket innebär att resursen kan ge åtkomst till innehavare av denna token. |

## <a name="get-a-token-using-c"></a>Hämta en token med C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Hämta en token med hjälp av Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Hämta en token med Azure PowerShell

I följande exempel visar hur du använder MSI REST-slutpunkt från en PowerShell-klient till:

1. Hämta en åtkomsttoken.
2. Använd åtkomsttoken att anropa en Azure Resource Manager REST API och få information om den virtuella datorn. Se till att ersätta din prenumerations-ID, resursgruppnamn och namn på virtuell dator för `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, och `<VM-NAME>`respektive.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exempel på hur du Parsar åtkomsttoken från svaret:
```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Hämta en token med CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exempel på hur du Parsar åtkomsttoken från svaret:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="token-caching"></a>Tokencachelagring

Medan undersystemet hanterad tjänstidentitet (MSI) som används (IMDS/MSI VM-tillägg) cachelagra token, rekommenderar vi också för att implementera tokencachelagring i din kod. Därför bör du förbereda för scenarier där resursen indikerar att token har upphört att gälla. 

Enbart resultat i wire-anrop till Azure AD när:
- cache-miss inträffar på grund av ingen token i cacheminnet för MSI-undersystem
- den cachelagra token har upphört att gälla

## <a name="error-handling"></a>Felhantering

Hanterad tjänstidentitet slutpunkten signalerar fel via fältet status kod för meddelandehuvudet för HTTP-svar som 4xx eller 5xx-fel:

| Statuskod | Felorsak | Hur du hanterar |
| ----------- | ------------ | ------------- |
| 404 hittades inte. | Uppdaterar IMDS slutpunkt. | Försök igen med Expontential Backoff. Se information nedan. |
| 429 för många begäranden. |  Begränsa IMDS gränsen har nåtts. | Försök med exponentiell Backoff. Se information nedan. |
| 4xx-fel i begäran. | En eller flera av parametrarna var fel. | Försök inte igen.  Granska felinformationen för mer information.  4xx-fel uppstår fel i designläge.|
| 5xx tillfälligt fel från tjänsten. | MSI-undersystem eller Azure Active Directory returnerade ett tillfälligt fel. | Det är bra att försöka igen efter att ha väntat i minst 1 sekund.  Om du gör för snabbt eller alltför ofta kan IMDS och/eller Azure AD returnera ett pris gräns-fel (429).|
| timeout | Uppdaterar IMDS slutpunkt. | Försök igen med Expontential Backoff. Se information nedan. |

Om ett fel inträffar, innehåller motsvarande HTTP-svarstext JSON med felinformation:

| Element | Beskrivning |
| ------- | ----------- |
| fel   | Fel-ID. |
| error_description | Utförlig beskrivning av fel. **Felbeskrivningar kan ändra när som helst. Inte att skriva kod som grenar baserat på värdena i felbeskrivningen.**|

### <a name="http-response-reference"></a>Referens för HTTP-svar

Det här avsnittet beskrivs möjliga felsvar. En ”200 OK” status är ett lyckat svar och åtkomst-token som finns i svarstexten JSON, i elementet access_token.

| Statuskod | Fel | Felbeskrivning | Lösning |
| ----------- | ----- | ----------------- | -------- |
| 400 Felaktig förfrågan | invalid_resource | AADSTS50001: Programmet med namnet *\<URI\>* hittades inte i klientorganisationen med namnet  *\<TENANT-ID\>*. Detta kan inträffa om programmet inte har installerats av administratör för klienten eller godkänts av någon användare i klienten. Du kanske har skickat din begäran om autentisering till fel klient. \ | (Endast Linux) |
| 400 Felaktig förfrågan | bad_request_102 | Metadata som krävs-huvud har inte angetts | Antingen den `Metadata` begäran rubrik fält saknas i din begäran eller är felaktigt formaterad. Värdet måste anges som `true`, i gemener. Se ”Sample-begäran” i den [föregående REST avsnittet](#rest) ett exempel.|
| 401 Ej behörig | unknown_source | Okänd källa  *\<URI\>* | Kontrollera att din HTTP GET-begäran URI har formaterats korrekt. Den `scheme:host/resource-path` del måste anges som `http://localhost:50342/oauth2/token`. Se ”Sample-begäran” i den [föregående REST avsnittet](#rest) ett exempel.|
|           | invalid_request | Begäran saknar en obligatorisk parameter, innehåller ett ogiltigt parametervärde, innehåller en parameter mer än en gång eller annars har fel format. |  |
|           | unauthorized_client | Klienten har inte behörighet att begära ett åtkomsttoken med hjälp av den här metoden. | På en begäran som inte använder lokal loopback för att anropa tillägget, eller på en virtuell dator som inte har en MSI som har konfigurerats korrekt. Se [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration. |
|           | ACCESS_DENIED | Resursägaren eller auktoriseringsservern nekade begäran. |  |
|           | unsupported_response_type | Auktoriseringsservern har inte stöd för att hämta en åtkomsttoken med hjälp av den här metoden. |  |
|           | invalid_scope | Det begärda omfånget är ogiltig, okänt eller felaktigt. |  |
| 500 Internt serverfel | okänt | Det gick inte att hämta token från Active directory. Mer information finns i loggarna i  *\<filsökväg\>* | Kontrollera att MSI har aktiverats på den virtuella datorn. Se [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.<br><br>Kontrollera också att din HTTP GET-begäran URI har formaterats korrekt, särskilt den resurs som URI angavs i frågesträngen. Se ”Sample-begäran” i den [föregående REST avsnittet](#rest) exempelvis eller [Azure-tjänster som stöder Azure AD-autentisering](services-support-msi.md) en lista över tjänster och deras respektive resurs-ID.

## <a name="retry-guidance"></a>Riktlinjer för återförsök 

Vi rekommenderar att försöka igen om du får ett 404, 429 och 5xx felkod (se [felhantering](#error-handling) ovan).

Begränsning begränsningar gäller antalet anrop som görs till IMDS-slutpunkten. När begränsningen tröskelvärdet överskrids begränsar IMDS endpoint eventuella ytterligare begäranden medan begränsningen är aktiverat. Under denna tid kan IMDS slutpunkten returnerar statuskoden HTTP 429 (”för många begäranden”), och förfrågningarna som misslyckas. 

Vi rekommenderar följande strategin för återförsök: 

| **Återförsöksstrategi** | **Inställningar** | **Värden** | **Hur det fungerar** |
| --- | --- | --- | --- |
|ExponentialBackoff |Antal återförsök<br />Min. backoff<br />Max. backoff<br />Deltabackoff<br />Första snabba återförsöket |5<br />0 sek.<br />60 sek.<br />2 sek.<br />false |Försök 1 – 0 sek. fördröjning<br />Försök 2 – ~2 sek. fördröjning<br />Försök 3 – ~6 sek. fördröjning<br />Försök 4 – ~14 sek. fördröjning<br />Försök 5 – ~30 sek. fördröjning |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID: N för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](services-support-msi.md) en lista över resurser som stöder Azure AD och har testats med MSI och deras respektive resurs-ID.


## <a name="related-content"></a>Relaterat innehåll

- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med Azure portal](qs-configure-portal-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.








