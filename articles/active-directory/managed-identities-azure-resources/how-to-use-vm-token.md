---
title: Använda hanterade identiteter på en virtuell dator för att hämta åtkomsttoken - Azure AD
description: Steg för steg instruktioner och exempel för att använda hanterade identiteter för Azure-resurser på en virtuell dator för att hämta en OAuth-åtkomsttoken.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58103bad3914bd0c0c6e70f8e3d2882271e1070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049195"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Så här använder du hanterade identiteter för Azure-resurser på en Azure-virtuell dator för att hämta en åtkomsttoken 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

Den här artikeln innehåller olika kod- och skriptexempel för tokeninsamling, samt vägledning om viktiga ämnen som hantering av tokenförfallodatum och HTTP-fel. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda Azure PowerShell-exemplen i den här artikeln måste du installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Alla exempelkod/skript i den här artikeln förutsätter att klienten körs på en virtuell dator med hanterade identiteter för Azure-resurser. Använd funktionen "Anslut" på den virtuella datorn i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar hanterade identiteter för Azure-resurser på en virtuell dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](qs-configure-portal-windows-vm.md)eller en av variantartiklarna (med PowerShell, CLI, en mall eller en Azure SDK). 

> [!IMPORTANT]
> - Säkerhetsgränsen för hanterade identiteter för Azure-resurser är den resurs som används på. Alla kod-/skript som körs på en virtuell dator kan begära och hämta token för alla hanterade identiteter som är tillgängliga på den. 

## <a name="overview"></a>Översikt

Ett klientprogram kan begära hanterade identiteter för [Azure-resurser app-only åtkomsttoken](../develop/developer-glossary.md#access-token) för åtkomst till en viss resurs. Token [baseras på hanterade identiteter för Azure Resources-tjänstens huvudnamn](overview.md#how-does-the-managed-identities-for-azure-resources-work). Därför finns det inget behov för klienten att registrera sig för att få en åtkomsttoken under sitt eget tjänsthuvudnamn. Token är lämplig för användning som innehavartoken i [service-to-service-anrop som kräver klientautentiseringsuppgifter](../develop/v2-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Hämta en token med HTTP](#get-a-token-using-http) | Protokollinformation för hanterade identiteter för Azure-resurstokenslutpunkt |
| [Hämta en token med microsoft.Azure.Services.AppAuthentication-biblioteket för .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Exempel på hur du använder microsoft.Azure.Services.AppAuthentication-biblioteket från en .NET-klient
| [Skaffa en token med C #](#get-a-token-using-c) | Exempel på hur du använder hanterade identiteter för AZURE-resurser REST-slutpunkt från en C#-klient |
| [Skaffa en token med Java](#get-a-token-using-java) | Exempel på hur du använder hanterade identiteter för AZURE-resurser REST-slutpunkt från en Java-klient |
| [Skaffa en token med Go](#get-a-token-using-go) | Exempel på hur du använder hanterade identiteter för AZURE-resurser REST-slutpunkt från en Go-klient |
| [Skaffa en token med Azure PowerShell](#get-a-token-using-azure-powershell) | Exempel på hur du använder hanterade identiteter för AZURE-resurser REST-slutpunkt från en PowerShell-klient |
| [Skaffa en token med CURL](#get-a-token-using-curl) | Exempel på hur du använder hanterade identiteter för Azure-resurser REST-slutpunkt från en Bash/CURL-klient |
| Hantering av tokencacheing | Vägledning för hantering av utgångna åtkomsttoken |
| [Felhantering](#error-handling) | Vägledning för hantering av HTTP-fel som returnerats från de hanterade identiteterna för Azure-resurstokenslutpunkt |
| [Resurs-ID:er för Azure-tjänster](#resource-ids-for-azure-services) | Här kan du hämta resurs-ID:er för Azure-tjänster som stöds |

## <a name="get-a-token-using-http"></a>Hämta en token med HTTP 

Det grundläggande gränssnittet för att hämta en åtkomsttoken baseras på REST, vilket gör den tillgänglig för alla klientprogram som körs på den virtuella datorn som kan ringa HTTP REST-anrop. Detta liknar Azure AD-programmeringsmodellen, förutom att klienten använder en slutpunkt på den virtuella datorn (jämfört med en Azure AD-slutpunkt).

Exempelbegäran med slutpunkten för Azure Instance Metadata Service (IMDS) *(rekommenderas):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, som anger att du vill hämta data från slutpunkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Den hanterade identitetspunkten för Azure-resurser slutpunkt för instansmetadatatjänsten. |
| `api-version`  | En frågesträngparameter som anger API-versionen för IMDS-slutpunkten. Använd API-version `2018-02-01` eller mer. |
| `resource` | En frågesträngparameter som anger målresursens app-ID-IURI. Det visas också `aud` i (publiken) anspråk på den utfärdade token. I det här exemplet begärs en token för att komma `https://management.azure.com/`åt Azure Resource Manager, som har en App ID URI av . |
| `Metadata` | Ett SIDFÄLT för HTTP-begäran, som krävs av hanterade identiteter för Azure-resurser som en begränsning mot SSRF-attack (Server Side Request Forgery). Det här värdet måste anges till "true", i alla gemener. |
| `object_id` | (Valfritt) En frågesträngparameter som anger object_id för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter.|
| `client_id` | (Valfritt) En frågesträngparameter som anger client_id för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter.|
| `mi_res_id` | (Valfritt) En frågesträngparameter som anger mi_res_id (Azure Resource ID) för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter. |

Exempelbegäran med de hanterade identiteterna för Azure resources VM Extension Endpoint *(planerad för utfasning i januari 2019):*

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, som anger att du vill hämta data från slutpunkten. I det här fallet en OAuth-åtkomsttoken. | 
| `http://localhost:50342/oauth2/token` | Den hanterade identitetspunkten för Azure-resurser slutpunkt, där 50342 är standardporten och kan konfigureras. |
| `resource` | En frågesträngparameter som anger målresursens app-ID-IURI. Det visas också `aud` i (publiken) anspråk på den utfärdade token. I det här exemplet begärs en token för att komma `https://management.azure.com/`åt Azure Resource Manager, som har en App ID URI av . |
| `Metadata` | Ett SIDFÄLT för HTTP-begäran, som krävs av hanterade identiteter för Azure-resurser som en begränsning mot SSRF-attack (Server Side Request Forgery). Det här värdet måste anges till "true", i alla gemener.|
| `object_id` | (Valfritt) En frågesträngparameter som anger object_id för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter.|
| `client_id` | (Valfritt) En frågesträngparameter som anger client_id för den hanterade identitet som du vill ha token för. Obligatoriskt om den virtuella datorn har flera användartilldelade hanterade identiteter.|

Exempelsvar:

```json
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
| `access_token` | Den begärda åtkomsttoken. När du anropar ett skyddat REST `Authorization` API bäddas token in i fältet för begäranhuvud som en "bärare"-token, vilket gör att API:et kan autentisera anroparen. | 
| `refresh_token` | Används inte av hanterade identiteter för Azure-resurser. |
| `expires_in` | Antalet sekunder som åtkomsttoken fortsätter att vara giltigt, innan den upphör att gälla, från tidpunkten för utfärdandet. Tidpunkten för utfärdandet finns i tokens `iat` anspråk. |
| `expires_on` | Tidsintervallet när åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `exp` anspråk). |
| `not_before` | Tidsintervallet när åtkomsttoken börjar gälla och kan accepteras. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `nbf` anspråk). |
| `resource` | Resursen som åtkomsttoken begärdes `resource` för, vilket matchar frågesträngparametern för begäran. |
| `token_type` | Typen av token, som är en "Bärare" åtkomsttoken, vilket innebär att resursen kan ge åtkomst till innehavaren av den här token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Hämta en token med microsoft.Azure.Services.AppAuthentication-biblioteket för .NET

För .NET-program och funktioner är det enklaste sättet att arbeta med hanterade identiteter för Azure-resurser via Microsoft.Azure.Services.AppAuthentication-paketet. Med det här biblioteket kan du också testa koden lokalt på utvecklingsmaskinen med hjälp av ditt användarkonto från Visual Studio, [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)eller Active Directory Integrated Authentication. Mer information om lokala utvecklingsalternativ med det här biblioteket finns i [referensen Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication). I det här avsnittet visas hur du kommer igång med biblioteket i koden.

1. Lägg till referenser till [microsoft.Azure.Services.AppAuthentication-](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-paketen till ditt program.

2.  Lägg till följande kod i ditt program:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Mer information om Microsoft.Azure.Services.AppAuthentication och de åtgärder som exponeras finns i [microsoft.Azure.Services.AppAuthentication-referensen](/azure/key-vault/service-to-service-authentication) och [apptjänsten och KeyVault med hanterade identiteter för Azure-resurser .NET-exempel](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Skaffa en token med C #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
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

## <a name="get-a-token-using-java"></a>Skaffa en token med Java

Använd det här [JSON-biblioteket](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) för att hämta en token med Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Skaffa en token med Go

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
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
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

    // Call managed services for Azure resources token endpoint
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

## <a name="get-a-token-using-azure-powershell"></a>Skaffa en token med Azure PowerShell

I följande exempel visas hur du använder de hanterade identiteterna för AZURE-resurser REST-slutpunkten från en PowerShell-klient till:

1. Hämta en åtkomsttoken.
2. Använd åtkomsttoken för att anropa ett REST-API för Azure Resource Manager och få information om den virtuella datorn. Var noga med att ersätta ditt prenumerations-ID, `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>`resursgruppsnamn och namn på virtuella datorer för , och `<VM-NAME>`, respektive.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Exempel på hur du tolkar åtkomsttoken från svaret:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Skaffa en token med CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Exempel på hur du tolkar åtkomsttoken från svaret:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Cachelagring av token

Medan de hanterade identiteterna för Azure-resurser undersystem som används (IMDS/hanterade identiteter för Azure resources VM Extension) cachelagrar token, rekommenderar vi också att implementera tokencacheing i din kod. Därför bör du förbereda för scenarier där resursen anger att token har upphört att gälla. 

On-the-wire-anrop till Azure AD-resultat endast när:
- cachemissträffar på grund av ingen token i de hanterade identiteterna för Azure resources-undersystemcachen
- den cachelagrade token har upphört att gälla

## <a name="error-handling"></a>Felhantering

De hanterade identiteterna för Slutpunkten för Azure-resurser signalerar fel via statuskodfältet i HTTP-svarsmeddelandehuvudet, som antingen 4xx- eller 5xx-fel:

| Statuskod | Felorsak | Hur man hanterar |
| ----------- | ------------ | ------------- |
| 404 Hittades inte. | IMDS-slutpunkten uppdateras. | Försök igen med Expontential Backoff. Se vägledning nedan. |
| 429 För många förfrågningar. |  IMDS Throttle-gränsen har nåtts. | Försök igen med exponentiell backoff. Se vägledning nedan. |
| 4xx Fel i begäran. | En eller flera av parametrarna för begäran var felaktiga. | Försök inte igen.  Mer information finns i felinformationen.  4xx fel är design-tid fel.|
| 5xx Övergående fel från tjänsten. | Hanterade identiteter för Azure-resurser undersystem eller Azure Active Directory returnerade ett tillfälligt fel. | Det är säkert att försöka igen efter att ha väntat i minst 1 sekund.  Om du försöker igen för snabbt eller för ofta kan IMDS och/eller Azure AD returnera ett hastighetsbegränsningsfel (429).|
| timeout | IMDS-slutpunkten uppdateras. | Försök igen med Expontential Backoff. Se vägledning nedan. |

Om ett fel uppstår innehåller motsvarande HTTP-svarstext JSON med felinformation:

| Element | Beskrivning |
| ------- | ----------- |
| fel   | Felidentifierare. |
| error_description | Utförlig beskrivning av fel. **Felbeskrivningar kan ändras när som helst. Skriv inte kod som förgrenar sig baserat på värden i felbeskrivningen.**|

### <a name="http-response-reference"></a>HTTP-svarsreferens

I det här avsnittet dokumenteras möjliga felsvar. Statusen "200 OK" är ett lyckat svar och åtkomsttoken finns i svarstexten JSON i det access_token elementet.

| Statuskod | Fel | Felbeskrivning | Lösning |
| ----------- | ----- | ----------------- | -------- |
| 400 dålig begäran | invalid_resource | AADSTS50001: Programmet * \<URI\> * hittades inte i klienten med namnet * \<TENANT-ID\>*. Detta kan inträffa om programmet inte har installerats av klientens administratör eller medgivande från någon användare i klienten. Du kan ha skickat din autentiseringsbegäran till fel klient.\ | (Endast Linux) |
| 400 dålig begäran | bad_request_102 | Det har inte angetts för att metadatahuvudet har angetts | `Metadata` Antingen saknas begäran om huvudfält i din begäran eller formateras felaktigt. Värdet måste anges som `true`, i alla gemener. Se "Exempelbegäran" i föregående REST-avsnitt för ett exempel.|
| 401 Obehörig | unknown_source | Okänd * \<källa URI\>* | Kontrollera att HTTP GET-begäran URI är korrekt formaterad. Delen `scheme:host/resource-path` måste anges som `http://localhost:50342/oauth2/token`. Se "Exempelbegäran" i föregående REST-avsnitt för ett exempel.|
|           | invalid_request | Begäran saknar en obligatorisk parameter, innehåller ett ogiltigt parametervärde, innehåller en parameter mer än en gång eller är på annat sätt felaktig. |  |
|           | unauthorized_client | Klienten har inte behörighet att begära en åtkomsttoken med den här metoden. | Orsakas av en begäran som inte använde accessloopback för att anropa tillägget, eller på en virtuell dator som inte har hanterade identiteter för Azure-resurser som konfigurerats korrekt. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Hjälp av Azure-portalen](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration. |
|           | access_denied | Resursägaren eller auktoriseringsservern nekade begäran. |  |
|           | unsupported_response_type | Auktoriseringsservern stöder inte att du hämtar en åtkomsttoken med den här metoden. |  |
|           | invalid_scope | Det begärda scopet är ogiltigt, okänt eller felaktigt. |  |
| 500 Internt serverfel | okänd | Det gick inte att hämta token från Active Directory. Mer information finns i loggar i * \<filsökvägen\>* | Kontrollera att hanterade identiteter för Azure-resurser har aktiverats på den virtuella datorn. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Hjälp av Azure-portalen](qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.<br><br>Kontrollera också att HTTP GET-begäran URI är korrekt formaterad, särskilt resursen URI som anges i frågesträngen. Se "Exempelbegäran" i föregående REST-avsnitt för ett exempel, eller [Azure-tjänster som stöder Azure AD-autentisering](services-support-msi.md) för en lista över tjänster och deras respektive resurs-ID.

## <a name="retry-guidance"></a>Vägledning för återförsök 

Vi rekommenderar att du försöker igen om du får en felkod på 404, 429 eller 5xx (se [Felhantering](#error-handling) ovan).

Begränsningsgränser gäller för antalet samtal som görs till IMDS-slutpunkten. När begränsningströskeln överskrids begränsar IMDS-slutpunkten alla ytterligare begäranden medan begränsningen gäller. Under den här perioden returnerar IMDS-slutpunkten HTTP-statuskoden 429 ("För många begäranden") och begäranden misslyckas. 

För återförsök rekommenderar vi följande strategi: 

| **Återförsöksstrategi** | **Inställningar** | **Värden** | **Så här fungerar det** |
| --- | --- | --- | --- |
|ExponentialBackoff |Antal återförsök<br />Min. backoff<br />Max. backoff<br />Deltabackoff<br />Första snabba återförsöket |5<br />0 sek.<br />60 sek.<br />2 sek.<br />false |Försök 1 – 0 sek. fördröjning<br />Försök 2 – ~2 sek. fördröjning<br />Försök 3 – ~6 sek. fördröjning<br />Försök 4 – ~14 sek. fördröjning<br />Försök 5 – ~30 sek. fördröjning |

## <a name="resource-ids-for-azure-services"></a>Resurs-ID:er för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](services-support-msi.md) för en lista över resurser som stöder Azure AD och har testats med hanterade identiteter för Azure-resurser och deras respektive resurs-ID.


## <a name="next-steps"></a>Nästa steg

- Information om hur du aktiverar hanterade identiteter för Azure-resurser på en Virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](qs-configure-portal-windows-vm.md).








