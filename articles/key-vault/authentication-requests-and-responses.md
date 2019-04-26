---
title: Autentisering, begäranden och svar
description: Autentisera till AD för att använda Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ca486768cf56059328801b1b4b1036bb8aeece8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201354"
---
# <a name="authentication-requests-and-responses"></a>Autentisering, begäranden och svar

Azure Key Vault har stöd för JSON-formaterade begäranden och svar. Begäranden till Azure Key Vault dirigeras till en giltig Azure Key Vault-URL med HTTPS med vissa URL-parametrar och JSON-kodad begäranden och svar organ.

Det här avsnittet beskriver specifika för Azure Key Vault-tjänsten. Allmän information om hur du använder Azure REST-gränssnitt, inklusive autentisering/auktorisering och hur du skaffar ett åtkomsttoken, finns i [Azure REST API-referens](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL för begäran  
 Nyckelhanteringsåtgärder använda HTTP DELETE, GET, korrigering, PUT- och HTTP POST och kryptografiska åtgärder mot befintliga viktiga objekt använder HTTP POST. Klienter som inte stöder specifika HTTP-verb använder också HTTP POST med hjälp av huvudet för X-HTTP-begäran för att ange det avsedda verbet. begäranden som normalt inte kräver en brödtext ska inkludera en brödtext när du använder HTTP-POST, till exempel när du använder POST i stället för att ta bort.  

 Om du vill arbeta med objekt i Azure Key Vault följer exempel URL: er:  

- Att skapa en nyckel som heter TESTKEY i användning av Key Vault- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Att importera en nyckel som heter IMPORTEDKEY till användning av Key Vault- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Att hämta en hemlighet som heter MINHEMLIGHET i användning av Key Vault- `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Om du vill registrera en sammanfattad kallas med hjälp av en nyckel TESTKEY Key Vault används – `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Utfärdaren av en begäran om att ett Key Vault är alltid på följande sätt,  `https://{keyvault-name}.vault.azure.net/`  

  Nycklar måste alltid lagras under /keys-sökvägen, hemligheter måste alltid lagras under /secrets-sökvägen.  

## <a name="api-version"></a>API-version  
 Azure Key Vault-tjänsten stöder protokoll versionshantering för att tillhandahålla kompatibilitet med äldre klienter, men inte alla funktioner blir tillgänglig för klienterna. Klienter måste använda den `api-version` frågesträngparametern om du vill ange versionen av det protokoll som de stöder eftersom det finns inget standardvärde.  

 Azure Key Vault-protokollversioner följer ett datum som numrering schemat med {YYYY}. {MM}. Formatet för {DD}.  

## <a name="request-body"></a>Begärandetext  
 GET-operationer får inte ha en brödtext i begäran enligt HTTP-specifikationen och post- och PUT-åtgärder måste ha en brödtext i begäran. Brödtexten i DELETE-åtgärder är valfritt i HTTP.  

 Om inget annat anges i beskrivningen för åtgärden, begäran brödtext innehållstyp måste vara application/json och måste innehålla en serialiserade JSON-objektet kompatibel till innehållstyp.  

 Om inget annat anges i beskrivningen för åtgärden, måste Accept-rubriken för begäran innehålla medietyp för application/json.  

## <a name="response-body"></a>Svarstext  
 Om inget annat anges i beskrivningen för åtgärden, blir application/json svar brödtext innehållstypen för både lyckade och misslyckade åtgärder och innehåller detaljerad felinformation.  

## <a name="using-http-post"></a>Med HTTP POST  
 Vissa klienter kanske inte kan använda vissa HTTP-verb, till exempel KORRIGERA eller ta bort. Azure Key Vault stöder HTTP POST som ett alternativ för dessa klienter, förutsatt att klienten även rubriken ”X-HTTP-METHOD” till specifika ursprungliga HTTP-verb. Stöd för HTTP POST som anges för var och en av API: et som definieras i detta dokument.  

## <a name="error-responses"></a>Felsvar  
 Felhantering använder HTTP-statuskoder. Typiska resultat är:  

- 2xx – lyckades: Används för normal drift. Svarstexten innehåller förväntat resultat  

- 3xx – Omdirigering: 304 ”ändras inte” kan returneras för att utföra en villkorlig GET. Andra 3xx koder kan användas i framtiden för att ange DNS-och sökväg.  

- 4xx – klientfel: Används för ogiltiga förfrågningar, saknade nycklar, syntaxfel, ogiltiga parametrar, autentiseringsfel, osv. Svarstexten innehåller detaljerade felet förklaring.  

- 5xx – Serverfel: Används för interna serverfel. Svarstexten innehåller sammanfattade felinformation.  

  Systemet fungerar bakom en proxyserver eller brandvägg. Därför kan en klient kan ta emot andra felkoder.  

  Azure Key Vault returnerar även information om fel i svarstexten när ett problem uppstår. Svarstexten är JSON-formaterad och har formatet:  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>Autentisering  
 Alla förfrågningar till Azure Key Vault måste autentiseras. Azure Key Vault stöder Azure Active Directory-åtkomsttoken som kan hämtas med OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Mer information om att registrera ditt program och autentisering för att använda Azure Key Vault finns i [registrera ditt klientprogram med Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Åtkomsttoken måste skickas till tjänsten med hjälp av HTTP-auktoriseringsrubriken:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 När en åtkomst-token inte har angetts, eller när en token inte accepteras av tjänsten, ett HTTP 401-fel returneras till klienten och innehåller rubriken WWW – autentisera till exempel:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametrarna i WWW-autentisera-huvudet är:  

-   Auktorisering: Adressen till auktorisering OAuth2-tjänsten som kan användas för att hämta en åtkomsttoken för begäran.  

-   resursen: Namnet på resursen som ska användas i auktoriseringsbegäran.  

## <a name="see-also"></a>Se även  
 [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
