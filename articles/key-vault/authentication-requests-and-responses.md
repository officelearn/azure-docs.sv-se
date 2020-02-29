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
ms.openlocfilehash: b84084dafe79a8caa9cdb70bc201f68834079845
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196084"
---
# <a name="authentication-requests-and-responses"></a>Autentisering, begäranden och svar

Azure Key Vault stöder JSON-formaterade begär Anden och svar. Begär anden till Azure Key Vault dirigeras till en giltig Azure Key Vault-URL med hjälp av HTTPS med vissa URL-parametrar och JSON-kodade begär Anden och svars texter.

Det här avsnittet beskriver information om Azure Key Vault tjänsten. Allmän information om hur du använder Azure REST-gränssnitt, inklusive autentisering/auktorisering och hur du hämtar en åtkomsttoken finns i [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure).

## <a name="request-url"></a>URL för begäran  
 Nyckel hanterings åtgärder använder http-post, GET, PATCH, skicka och HTTP POST och kryptografiska åtgärder mot befintliga nyckel objekt Använd HTTP POST. Klienter som inte stöder vissa HTTP-verb kan också använda HTTP POST med rubriken X-HTTP-REQUEST för att ange avsett verb. begär Anden som normalt inte kräver en brödtext bör innehålla en tom brödtext när HTTP POST används, till exempel när POST används i stället för DELETE.  

 Följande är exempel på webb adresser för att arbeta med objekt i Azure Key Vault:  

- Så här skapar du en nyckel med namnet TESTKEY i en Key Vault use-`PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Importera en nyckel med namnet IMPORTEDKEY till en Key Vault use-`POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Så här får du en hemlighet som heter hemlig i en Key Vault use-`GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- SIGNERA en sammanfattning med hjälp av en nyckel med namnet TESTKEY i en Key Vault use-`POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

  Auktoriteten för en begäran till en Key Vault är alltid enligt följande `https://{keyvault-name}.vault.azure.net/`  

  Nycklar lagras alltid under/Keys sökväg, hemligheter lagras alltid under/Secrets-sökvägen.  

## <a name="api-version"></a>API-version  
 Tjänsten Azure Key Vault stöder protokoll versioner för att ge kompatibilitet med äldre klienter, även om alla funktioner inte är tillgängliga för dessa klienter. Klienter måste använda parametern `api-version` frågesträng för att ange vilken version av protokollet som de stöder eftersom det inte finns något standardvärde.  

 Azure Key Vault protokoll versioner följer ett schema för datum numrering med hjälp av {ÅÅÅÅ}. {MM}. {DD}-format.  

## <a name="request-body"></a>Begärandetext  
 Som per HTTP-specifikation får GET-åtgärder inte ha en begär ande text, och POST-och PLACERINGs åtgärder måste ha en begär ande text. Texten i DELETE-åtgärder är valfri i HTTP.  

 Om inget annat anges i åtgärds beskrivningen måste innehålls typen för begär ande texten vara Application/JSON och måste innehålla ett serialiserat JSON-objekt som överensstämmer med innehålls typen.  

 Om inget annat anges i åtgärds beskrivningen, måste rubriken för accept-begäran innehålla medie typen application/JSON.  

## <a name="response-body"></a>Svarsinnehåll  
 Om inget annat anges i åtgärds beskrivningen kommer svars bröd textens innehålls typ för både lyckade och misslyckade åtgärder att vara Application/JSON och innehåller detaljerad fel information.  

## <a name="using-http-post"></a>Använda HTTP POST  
 Vissa klienter kanske inte kan använda vissa HTTP-verb, till exempel PATCH eller ta bort. Azure Key Vault stöder HTTP POST som ett alternativ för dessa klienter förutsatt att klienten även inkluderar huvudet "X-HTTP-METHOD" för att ange det ursprungliga HTTP-verbet. Stöd för HTTP POST anges för var och en av de API: er som definierats i det här dokumentet.  

## <a name="error-responses"></a>Fel svar  
 Fel hanteringen kommer att använda HTTP-statuskod. Typiska resultat:  

- 2xx – lyckades: används för normal drift. Svars texten kommer att innehålla det förväntade resultatet  

- 3xx – omdirigering: 304 "inte ändrade" kan returneras för att uppfylla ett villkorligt GET-uttryck. Andra 3xx-koder kan användas i framtiden för att indikera DNS-och Sök vägs ändringar.  

- 4xx – klient fel: används för felaktiga begär Anden, saknade nycklar, syntaxfel, ogiltiga parametrar, autentiseringsfel osv. Svars texten kommer att innehålla detaljerad fel förklaring.  

- 5xx – Server fel: används för interna Server fel. Svars texten kommer att innehålla sammanfattad fel information.  

  Systemet är utformat för att fungera bakom en proxy eller brand vägg. Därför kan en klient få andra felkoder.  

  Azure Key Vault returnerar också fel information i svars texten när ett problem uppstår. Svars texten är JSON-formaterad och använder formuläret:  

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
 Alla begär anden till Azure Key Vault måste autentiseras. Azure Key Vault stöder Azure Active Directory åtkomsttoken som kan erhållas med hjälp av OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)]. 
 
 Mer information om hur du registrerar ditt program och autentiserar dig för att använda Azure Key Vault finns i [Registrera ditt klient program med Azure AD](https://docs.microsoft.com/rest/api/azure/index#register-your-client-application-with-azure-ad).
 
 Åtkomsttoken måste skickas till tjänsten med HTTP-Authorization-huvudet:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 När en åtkomsttoken inte anges, eller när en token inte accepteras av tjänsten, returneras ett HTTP 401-fel till klienten och inkluderar WWW-autentisera-sidhuvudet, till exempel:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametrarna i WWW-autentisera-huvudet är:  

-   auktorisering: adressen till OAuth2 som kan användas för att hämta en åtkomsttoken för begäran.  

-   resurs: namnet på resursen (https://vault.azure.net) som ska användas i auktoriseringsbegäran.  

## <a name="see-also"></a>Se även  
 [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
