---
title: Autentisering, begäranden och svar
description: Autentisera till AD för att använda Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 8cb68b1a5363889e6ea7c49cf0cdda6ed5f0e027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294563"
---
# <a name="authentication-requests-and-responses"></a>Autentisering, begäranden och svar

Azure Key Vault stöder JSON-formaterade begäranden och svar. Begäranden till Azure Key Vault dirigeras till en giltig Azure Key Vault-URL med HTTPS med vissa URL-parametrar och JSON-kodade begärande- och svarsorgan.

Det här avsnittet innehåller detaljer för Azure Key Vault-tjänsten. Allmän information om hur du använder Azure REST-gränssnitt, inklusive autentisering/auktorisering och hur du hämtar en åtkomsttoken, finns i [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL för begäran  
 Nyckelhanteringsåtgärder använder HTTP DELETE, GET, PATCH, PUT och HTTP POST och kryptografiska åtgärder mot befintliga nyckelobjekt använder HTTP POST. Klienter som inte stöder specifika HTTP-verb kan också använda HTTP POST med x-HTTP-REQUEST-huvudet för att ange det avsedda verbet. Begäranden som normalt inte kräver en kropp bör innehålla en tom kropp när du använder HTTP POST, till exempel när du använder POST i stället för DELETE.  

 Om du vill arbeta med objekt i Azure Key Vault är följande exempel på WEBBADRESSER:  

- Om du vill skapa en nyckel som kallas TESTKEY i ett key vault-`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Om du vill importera en nyckel som kallas IMPORTEDKEY till ett key vault-`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- För att få en hemlighet som heter MYSECRET i en Key Vault användning -`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Om du vill signera en sammanfattning med hjälp av en nyckel som kallas TESTKEY i ett Key Vault-`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Myndigheten för en begäran till ett nyckelvalv är alltid följande,`https://{keyvault-name}.vault.azure.net/`  

  Nycklar lagras alltid under sökvägen /keys, Hemligheter lagras alltid under sökvägen /secrets.  

## <a name="api-version"></a>API-version  
 Azure Key Vault-tjänsten stöder protokollversionering för att ge kompatibilitet med klienter på lägre nivå, även om inte alla funktioner är tillgängliga för dessa klienter. Klienter måste `api-version` använda frågesträngparametern för att ange den version av protokollet som de stöder eftersom det inte finns någon standard.  

 Azure Key Vault-protokollversioner följer ett datumnumreringsschema med hjälp av ett {YYYY}. {MM}. {DD}-format.  

## <a name="request-body"></a>Begärandetext  
 Enligt HTTP-specifikationen får GET-åtgärder INTE ha ett begärandeorgan, och POST- och PUT-åtgärder måste ha ett förfrågningsorgan. Brödtexten i DELETE-åtgärder är valfri i HTTP.  

 Om inte annat anges i åtgärdsbeskrivningen måste typen för brödtextinnehåll vara application/json och innehålla ett serialiserat JSON-objekt som överensstämmer med innehållstypen.  

 Om inget annat anges i åtgärdsbeskrivningen måste huvudet acceptera begäran innehålla medietypen application/json.  

## <a name="response-body"></a>Svarskropp  
 Om inget annat anges i åtgärdsbeskrivningen kommer svarstextinnehållstypen för både lyckade och misslyckade åtgärder att vara program/json och innehåller detaljerad felinformation.  

## <a name="using-http-post"></a>Använda HTTP POST  
 Vissa klienter kanske inte kan använda vissa HTTP-verb, till exempel PATCH eller DELETE. Azure Key Vault stöder HTTP POST som ett alternativ för dessa klienter, förutsatt att klienten även innehåller "X-HTTP-METOD"-huvudet för specifika det ursprungliga HTTP-verbet. Stöd för HTTP POST noteras för var och en av de API som definieras i det här dokumentet.  

## <a name="error-responses"></a>Fel svar  
 Om du hanterar fel används HTTP-statuskoder. Typiska resultat är:  

- 2xx – Framgång: Används för normal drift. Svarskroppen kommer att innehålla det förväntade resultatet  

- 3xx – Omdirigering: 304"Inte ändrad" kan returneras för att uppfylla en villkorlig GET. Andra 3xx-koder kan användas i framtiden för att ange DNS- och sökvägsändringar.  

- 4xx – Klientfel: Används för felaktiga begäranden, saknade nycklar, syntaxfel, ogiltiga parametrar, autentiseringsfel etc. Svarstexten kommer att innehålla detaljerad felförklaring.  

- 5xx – Serverfel: Används för interna serverfel. Svarstexten innehåller sammanfattad felinformation.  

  Systemet är utformat för att fungera bakom en proxy eller brandvägg. Därför kan en klient få andra felkoder.  

  Azure Key Vault returnerar också felinformation i svarstexten när ett problem uppstår. Svarstexten är JSON formaterad och har formen:  

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
 Alla begäranden till Azure Key Vault måste autentiseras. Azure Key Vault stöder Azure Active Directory-åtkomsttoken som kan erhållas med OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Mer information om hur du registrerar ditt program och autentiserar för att använda Azure Key Vault finns i [Registrera ditt klientprogram med Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Åtkomsttoken måste skickas till tjänsten med http-auktoriseringshuvudet:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 När en åtkomsttoken inte anges, eller när en token inte accepteras av tjänsten, returneras ett HTTP 401-fel till klienten och kommer att innehålla WWW-Autentisera-huvudet, till exempel:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametrarna i WWW-Autentisera-huvudet är:  

-   auktorisering: Adressen till OAuth2-auktoriseringstjänsten som kan användas för att hämta en åtkomsttoken för begäran.  

-   resurs: Namnet på den`https://vault.azure.net`resurs ( ) som ska användas i auktoriseringsbegäran.  

## <a name="see-also"></a>Se även  
 [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
