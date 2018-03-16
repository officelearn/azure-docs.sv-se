---
title: "Hur du använder en Användartilldelad hanterade tjänstidentiteten för att skaffa en åtkomst-token på en virtuell dator."
description: "Stegvisa åtkomst anvisningar och exempel för att använda en Användartilldelad MSI från en Azure VM för att skaffa en OAuth-token."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 68454d3f3880df82ca895d1c5f140ebdb6030e77
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>Skaffa en åtkomst-token för en virtuell dator Användartilldelad hanteras Service identitet (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Den här artikeln innehåller olika exempel på kod och skript för token förvärv, samt information om viktiga ämnen, till exempel hantering token upphör att gälla och HTTP-fel.

## <a name="prerequisites"></a>Förutsättningar
[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]
Om du planerar att använda Azure PowerShell-exemplen i den här artikeln bör du installera den senaste versionen av [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter klienten körs på en MSI-aktiverad virtuell dator. Använd funktionen ”ansluta” VM i Azure-portalen för att fjärransluta till den virtuella datorn. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en Användartilldelad hanteras Service identitet (MSI) för en virtuell dator med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md), eller en variant artiklar (med portalen, PowerShell, en mall eller en Azure SDK). 

## <a name="overview"></a>Översikt

Ett klientprogram kan begära en MSI [endast app-åtkomst-token](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token) för att komma åt en viss resurs. Token är [baserat på MSI tjänstens huvudnamn](msi-overview.md#how-does-it-work). Därför behövs det ingen för klienten att registrera sig att hämta en åtkomst-token under ett eget huvudnamn för tjänsten. Token är lämpliga för användning som en ägartoken i [tjänst-till-tjänst anropar kräver klientens autentiseringsuppgifter](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Hämta en token med HTTP](#get-a-token-using-http) | Protokollet information för slutpunkten för MSI-token |
| [Hämta en token med CURL](#get-a-token-using-curl) | Exempel på användning av MSI REST-slutpunkt från en Bash/CURL-klient |
| [Hantera token upphör att gälla](#handling-token-expiration) | Riktlinjer för att hantera åtkomsttoken har upphört att gälla |
| [Felhantering](#error-handling) | Riktlinjer för hantering av HTTP-fel som returneras från slutpunkten för MSI-token |
| [Resurs-ID för Azure-tjänster](#resource-ids-for-azure-services) | Var du kan hämta resurs-ID för stöds Azure-tjänster |

## <a name="get-a-token-using-http"></a>Hämta en token med HTTP 

Det grundläggande gränssnittet för att erhålla en åtkomst-token baseras på REST, vilket gör den tillgänglig för alla klientprogram som körs på den virtuella datorn som kan göra HTTP-REST-anrop. Detta liknar Azure AD-programmodellen, men klienten använder en slutpunkt på den virtuella datorn (jämfört med en Azure AD-slutpunkten).

Exempel på begäran med hjälp av slutpunkten instans Metadata Service (IMDS):

```
GET http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1 Metadata: true
```

Exempel på begäran med hjälp av MSI VM-tillägget slutpunkten (kommande utfasningen):

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1 Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, vilket innebär att du vill hämta data från slutpunkten. I det här fallet en OAuth åtkomst-token. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | MSI-slutpunkten för tjänsten instans Metadata. |
| `http://localhost:50342/oauth2/token` | MSI-slutpunkt för VM-tillägget, där 50342 är standardporten och kan konfigureras. |
| `api-version`  | En frågesträngsparameter som anger API-version för IMDS slutpunkten.  |
| `resource` | En frågesträngsparameter som anger App-ID-URI för målresursen. Den visas också i den `aud` (målgrupp) anspråk för Utfärdad token. Det här exemplet begär en token för åtkomst till Azure Resource Manager, som har en App-ID-URI för https://management.azure.com/. |
| `client_id` |  En *valfria* frågesträngparametern, som anger klient-ID (även kallat App-ID) för tjänstens huvudnamn som representerar en Användartilldelad MSI. Den här parametern är inte obligatoriskt om du använder automatiskt tilldelade MSI. Detta värde returneras den `clientId` egenskapen under skapandet av en Användartilldelad MSI. Det här exemplet begär en token för klient-ID ”712eac09-e943-418c-9be6-9fd5c91078bl”. |
| `Metadata` | En HTTP-begäran huvudfältet, krävs av MSI som en lösning mot angrepp Server Side begäran förfalskning (SSRF). Det här värdet måste anges till ”true”, alla med gemener.

Exempelsvar:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| Element | Beskrivning |
| ------- | ----------- |
| `access_token` | Den begärda åtkomst-token. När du anropar en skyddad REST-API, token är inbäddad i den `Authorization` begäran huvudfältet som ett ”ägartoken”, vilket gör att API för att autentisera anroparen. | 
| `expires_in` | Antal sekunder som åtkomsttoken fortsätter att vara giltig innan det förfaller från utfärdandet. Tid för utfärdande kan hittas i token `iat` anspråk. |
| `expires_on` | Timespan när den åtkomst-token upphör att gälla. Representeras som antalet sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `exp` anspråk). |
| `not_before` | Timespan när åtkomsttoken träder i kraft och kan accepteras. Representeras som antalet sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `nbf` anspråk). |
| `resource` | Resursen åtkomsttoken har begärts för som matchar den `resource` frågesträngparametern för begäran. |
| `token_type` | Typ av token som är en åtkomsttoken ”ägar”, vilket innebär att resursen kan ge åtkomst till innehavare av denna token. |
| `client_id` | Klient-ID (även kallat App-ID) för tjänstens huvudnamn som representerar Användartilldelad-MSI för vilka token som begärdes. |

## <a name="get-a-token-using-curl"></a>Hämta en token med CURL

Se till att ersätta klient-ID (även kallat App-ID) för din Användartilldelad MSI-tjänstens huvudnamn, för den <MSI CLIENT ID> värdet för den `client_id` parameter. Detta värde returneras den `clientId` egenskapen under skapandet av en Användartilldelad MSI.
  
Exempel på begäran med hjälp av slutpunkten instans Metadata Service (IMDS):

   ```bash
   response=$(curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>")
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```
   
Exempel på begäran med hjälp av MSI VM-tillägget slutpunkten (kommande utfasningen):

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   Exempel på svar:

   ```bash
   user@vmLinux:~$ response=$(curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl")
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>Hantera token upphör att gälla

MSI-undersystemet cachelagrar token. Därför kan anropa du den så ofta du vill och ett under överföring anrop till Azure AD resultatet bara om:
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
| 400 Felaktig förfrågan | bad_request_102 | Metadata som krävs huvud har inte angetts | Antingen den `Metadata` begäran huvudfältet saknas från begäran, eller är felaktigt formaterad. Värdet måste anges som `true`, alla med gemener. Se ”exempelbegäran” i den [hämta en token som använder HTTP](#get-a-token-using-http) avsnittet ett exempel.|
| 401 obehörig | unknown_source | Okänd källa  *\<URI\>* | Kontrollera att din HTTP GET-begäran URI är korrekt formaterad. Den `scheme:host/resource-path` del måste anges som `http://169.254.169.254/metadata/identity/oath2/token` eller `http://localhost:50342/oauth2/token`. Se ”exempelbegäran” i den [hämta en token som använder HTTP](#get-a-token-using-http) avsnittet ett exempel.|
|           | invalid_request | Begäran saknar en obligatorisk parameter, innehåller ett ogiltigt parametervärde, innehåller en parameter mer än en gång eller på annat sätt är felaktig. |  |
|           | unauthorized_client | Klienten har inte behörighet att begära en åtkomst-token med den här metoden. | På grund av en begäran som inte använder lokal loopback för att anropa tillägget eller på en virtuell dator som inte har en MSI som konfigurerats på rätt sätt. Se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration. |
|           | access_denied | Resursägare eller auktorisering servern nekade begäran. |  |
|           | unsupported_response_type | Auktorisering servern stöder inte att erhålla en åtkomst-token med den här metoden. |  |
|           | invalid_scope | Det begärda omfånget är ogiltigt, okänt eller felaktigt format. |  |
| 500 Internt serverfel | okänt | Det gick inte att hämta token från Active directory. Mer information finns i loggarna i  *\<filsökväg\>* | Kontrollera att MSI har aktiverats på den virtuella datorn. Se [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md) om du behöver hjälp med VM-konfiguration.<br><br>Kontrollera också att din HTTP GET URI-begäran har formaterats korrekt, särskilt resursen URI som angetts i frågesträngen. Finns i ”exempelbegäran” i den [hämta en token som använder HTTP](#get-a-token-using-http) avsnittet exempelvis eller [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över tjänster och deras respektive resurs-ID.

## <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över resurser som stöder Azure AD och har testats med MSI och deras respektive resurs-ID.


## <a name="next-steps"></a>Nästa steg

- För att aktivera MSI på en Azure VM, se [konfigurera en Användartilldelad hanteras Service identitet (MSI) för en virtuell dator med hjälp av Azure CLI](msi-qs-configure-cli-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.








