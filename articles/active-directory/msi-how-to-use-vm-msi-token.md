---
title: "Hur du använder hanterade tjänstidentiteten en Azure VM för att skaffa en åtkomst-token"
description: "Stegvisa åtkomst anvisningar och exempel för att använda en Azure VM-MSI för att erhålla en OAuth-token."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 6a02b52e7103c9b6e60b09617026fbf6010e76c8
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Hur du använder en Azure VM hanterade tjänsten identitet (MSI) för token 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Den här artikeln innehåller olika exempel på kod och skript för token förvärv, samt information om viktiga ämnen, till exempel hantering token upphör att gälla och HTTP-fel.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell-exemplen i den här artikeln bör du installera den senaste versionen av [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” VM i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md), eller en variant artiklar (med PowerShell, CLI, en mall eller en Azure SDK). 

## <a name="overview"></a>Översikt

Ett klientprogram kan begära en MSI [endast app-åtkomst-token](develop/active-directory-dev-glossary.md#access-token) för att komma åt en viss resurs. Token är [baserat på MSI tjänstens huvudnamn](msi-overview.md#how-does-it-work). Därför behövs det ingen för klienten att registrera sig att hämta en åtkomst-token under ett eget huvudnamn för tjänsten. Token är lämpliga för användning som en ägartoken i [tjänst-till-tjänst anropar kräver klientens autentiseringsuppgifter](active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Hämta en token med HTTP](#get-a-token-using-http) | Protokollet information för slutpunkten för MSI-token |
| [Hämta en token med C#](#get-a-token-using-c) | Exempel på användning av MSI REST-slutpunkt från en C#-klient |
| [Hämta en token med gå](#get-a-token-using-go) | Exempel på användning av MSI REST-slutpunkt från en Gå-klient |
| [Hämta en token med hjälp av Azure PowerShell](#get-a-token-using-azure-powershell) | Exempel på användning av MSI REST-slutpunkt från en PowerShell-klient |
| [Hämta en token med CURL](#get-a-token-using-curl) | Exempel på användning av MSI REST-slutpunkt från en Bash/CURL-klient |
| [Hantera token upphör att gälla](#handling-token-expiration) | Riktlinjer för att hantera åtkomsttoken har upphört att gälla |
| [Felhantering](#error-handling) | Riktlinjer för hantering av HTTP-fel som returneras från slutpunkten för MSI-token |
| [Resurs-ID för Azure-tjänster](#resource-ids-for-azure-services) | Var du kan hämta resurs-ID för stöds Azure-tjänster |

## <a name="get-a-token-using-http"></a>Hämta en token med HTTP 

Det grundläggande gränssnittet för att erhålla en åtkomst-token baseras på REST, vilket gör den tillgänglig för alla klientprogram som körs på den virtuella datorn som kan göra HTTP-REST-anrop. Detta liknar Azure AD-programmodellen, men klienten använder en localhost-slutpunkt på den virtuella datorn (jämfört med en Azure AD-slutpunkten).

Exempel på begäran:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, vilket innebär att du vill hämta data från slutpunkten. I det här fallet en OAuth åtkomst-token. | 
| `http://localhost:50342/oauth2/token` | MSI slutpunkten, där 50342 är standardporten och kan konfigureras. |
| `resource` | En frågesträngsparameter som anger App-ID-URI för målresursen. Den visas också i den `aud` (målgrupp) anspråk för Utfärdad token. Det här exemplet begär en token för åtkomst till Azure Resource Manager, som har en https://management.azure.com/ App-ID-URI. |
| `Metadata` | En HTTP-begäran huvudfältet, krävs av MSI som en lösning mot angrepp Server Side begäran förfalskning (SSRF). Det här värdet måste anges till ”true”, alla med gemener.

Exempelsvar:

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
| `access_token` | Den begärda åtkomst-token. När du anropar en skyddad REST-API, token är inbäddad i den `Authorization` begäran huvudfältet som ett ”ägartoken”, vilket gör att API för att autentisera anroparen. | 
| `refresh_token` | Inte används av MSI. |
| `expires_in` | Antal sekunder som åtkomsttoken fortsätter att vara giltig innan det förfaller från utfärdandet. Tid för utfärdande kan hittas i token `iat` anspråk. |
| `expires_on` | Timespan när den åtkomst-token upphör att gälla. Representeras som antalet sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `exp` anspråk). |
| `not_before` | Timespan när åtkomsttoken träder i kraft och kan accepteras. Representeras som antalet sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `nbf` anspråk). |
| `resource` | Resursen åtkomsttoken har begärts för som matchar den `resource` frågesträngparametern för begäran. |
| `token_type` | Typ av token som är en åtkomsttoken ”ägar”, vilket innebär att resursen kan ge åtkomst till innehavare av denna token. |

## <a name="get-a-token-using-c"></a>Hämta en token med C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
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

## <a name="get-a-token-using-go"></a>Hämta en token med gå

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
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
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

## <a name="get-a-token-using-azure-powershell"></a>Hämta en token med hjälp av Azure PowerShell

I följande exempel visar hur du använder MSI REST-slutpunkt från en PowerShell-klient till:

1. Hämta en åtkomst-token.
2. Använd åtkomsttoken att anropa en Azure Resource Manager REST-API och få information om den virtuella datorn. Se till att ersätta ditt prenumerations-ID, resursgruppens namn och namn på virtuell dator för `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, och `<VM-NAME>`respektive.

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
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
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Hantera token upphör att gälla

Lokala MSI-undersystemet cachelagrar token. Därför kan anropa du den så ofta du vill och ett under överföring anrop till Azure AD resultatet bara om:
- en cache-miss inträffar på grund av att inga token i cacheminnet
- token har upphört att gälla

Om du Cachelagra token i koden, bör du vara beredd på att hantera scenarier där resursen anger att token har upphört att gälla.

## <a name="error-handling"></a>Felhantering 

MSI-slutpunkten signalerar fel via fältet status kod i meddelandehuvudet för HTTP-svar som 4xx eller 5xx-fel:

| Statuskod | Fel orsak | Hur du hanterar |
| ----------- | ------------ | ------------- |
| 4xx fel i begäran. | En eller flera parametrar för begäran var felaktig. | Försök inte.  Granska felinformationen för mer information.  4xx fel uppstår fel i designläge.|
| 5xx tillfälligt fel från tjänsten. | MSI-undersystem eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen efter att ha väntat i minst 1 sekund.  Om du gör för snabbt eller för ofta, kan Azure AD returnera ett gränsen hastighet (429).|

Om ett fel inträffar innehåller motsvarande HTTP svarstexten JSON med felinformation:

| Element | Beskrivning |
| ------- | ----------- |
| fel   | Fel-ID. |
| error_description | Detaljerad beskrivning av felet. **Felbeskrivningar kan ändra när som helst. Skriv inte kod som filialer baserat på värdena i felbeskrivningen.**|

### <a name="http-response-reference"></a>Referens för HTTP-svar

Det här avsnittet beskrivs möjliga felsvar. En ”200 OK” status är ett lyckat svar och åtkomst-token som finns i svarstexten JSON, i elementet access_token.

| Statuskod | Fel | Felbeskrivning | Lösning |
| ----------- | ----- | ----------------- | -------- |
| 400 Felaktig förfrågan | invalid_resource | AADSTS50001: Programmet heter  *\<URI\>*  hittades inte i klient med namnet  *\<klient-ID\>*. Detta kan inträffa om programmet inte har installerats av administratör för klienten eller godkänt för av alla användare i klienten. Du har kanske skickar din begäran om autentisering fel klienten. \ | (Endast Linux) |
| 400 Felaktig förfrågan | bad_request_102 | Metadata som krävs huvud har inte angetts | Antingen den `Metadata` begäran huvudfältet saknas från begäran, eller är felaktigt formaterad. Värdet måste anges som `true`, alla med gemener. Se ”exempelbegäran” i den [föregående REST-avsnittet](#rest) ett exempel.|
| 401 obehörig | unknown_source | Okänd källa  *\<URI\>* | Kontrollera att din HTTP GET-begäran URI är korrekt formaterad. Den `scheme:host/resource-path` del måste anges som `http://localhost:50342/oauth2/token`. Se ”exempelbegäran” i den [föregående REST-avsnittet](#rest) ett exempel.|
|           | invalid_request | Begäran saknar en obligatorisk parameter, innehåller ett ogiltigt parametervärde, innehåller en parameter mer än en gång eller på annat sätt är felaktig. |  |
|           | unauthorized_client | Klienten har inte behörighet att begära en åtkomst-token med den här metoden. | På grund av en begäran som inte använder lokal loopback för att anropa tillägget eller på en virtuell dator som inte har en MSI som konfigurerats på rätt sätt. Se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration. |
|           | Om ACCESS_DENIED | Resursägare eller auktorisering servern nekade begäran. |  |
|           | unsupported_response_type | Auktorisering servern stöder inte att erhålla en åtkomst-token med den här metoden. |  |
|           | invalid_scope | Det begärda omfånget är ogiltigt, okänt eller felaktigt format. |  |
| 500 Internt serverfel | okänt | Det gick inte att hämta token från Active directory. Mer information finns i loggarna i  *\<filsökväg\>* | Kontrollera att MSI har aktiverats på den virtuella datorn. Se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.<br><br>Kontrollera också att din HTTP GET URI-begäran har formaterats korrekt, särskilt resursen URI som angetts i frågesträngen. Finns i ”exempelbegäran” i den [föregående REST avsnittet](#rest) exempelvis eller [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över tjänster och deras respektive resurs-ID.

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över resurser som stöder Azure AD och har testats med MSI och deras respektive resurs-ID.


## <a name="related-content"></a>Relaterat innehåll

- För att aktivera MSI på en Azure VM, se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.








