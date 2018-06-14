---
title: Autentisering, begäranden och -svar
description: Autentisera till AD för att använda Key Vault
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 4c321939-8a5b-42ca-83c4-2f5f647ca13e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: 94080fb124478a4b8e196e341c335ca32321ecdf
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012197"
---
# <a name="authentication-requests-and-responses"></a>Autentisering, begäranden och -svar

Azure Key Vault stöder JSON-formaterade begäranden och -svar. Förfrågningar till Azure Key Vault dirigeras till en giltig Azure Key Vault-URL med hjälp av HTTPS med vissa URL-parametrar och JSON-kodade begäran och svar.

Det här avsnittet beskriver specifika för Azure Key Vault-tjänsten. Allmän information om hur du använder Azure REST-gränssnitt, inklusive autentisering/auktorisering och hur du skaffar en åtkomst-token finns [Azure REST API-referens](https://docs.microsoft.com/rest/api/).

## <a name="request-url"></a>Fråge-URL  
 Nyckelhantering åtgärderna använder HTTP DELETE, GET, korrigering, PUT- och HTTP POST och kryptografiska åtgärder mot befintliga nycklar objekt använder HTTP POST. Klienter som inte kan stödja specifika HTTP-verb kan också använda HTTP POST med huvudet X-HTTP-begäran för att ange det avsedda verbet. begäranden som normalt inte kräver en text ska innehålla en brödtext när du använder HTTP POST till exempel när du använder POST i stället för att ta bort.  

 Om du vill arbeta med objekt i Azure Key Vault är följande exempel-URL: er:  

-   Att skapa en nyckel som heter TESTKEY i användning av Key Vault- `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

-   Så här IMPORTERAR du en nyckel som heter IMPORTEDKEY till användning av Key Vault- `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

-   Få en hemlighet som kallas MINHEMLIGHET en Key Vault-användning- `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

-   Om du vill registrera en sammanfattad kallas med en nyckel TESTKEY en Key Vault-användning- `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

 Myndigheten för en begäran till en Key Vault är alltid på följande sätt,  `https://{keyvault-name}.vault.azure.net/`  

 Nycklar lagras alltid under sökvägen /keys, hemligheter måste alltid lagras under /secrets sökvägen.  

## <a name="api-version"></a>API-version  
 Azure Key Vault-tjänsten stöder protokollet versionshantering för att tillhandahålla kompatibilitet med äldre klienter, men inte alla funktioner blir tillgängliga på de klienterna. Klienter måste använda den `api-version` frågesträngparametern om du vill ange version för det protokoll som de stöder eftersom det inte finns något standardvärde.  

 Azure Key Vault-adressprotokollversioner följa ett datum numrering schemat med {åååå}. {MM}. {Dd.}.  

## <a name="request-body"></a>Begärandetext  
 Enligt specifikationen HTTP GET-åtgärder får inte ha någon brödtext och POST och PUT-åtgärder måste ha en brödtext i begäran. Texten i DELETE-åtgärder är valfritt i HTTP.  

 Om inget annat anges i beskrivningen av åtgärden, begäran brödtext content-type måste vara application/json och måste innehålla en serialiserad JSON-objekt ska följa aktuell standard att content-type.  

 Om inget annat anges i beskrivningen av åtgärden, måste begäran Accept-huvudet innehålla application/json medietypen.  

## <a name="response-body"></a>Svarstext  
 Om inget annat anges i beskrivningen av åtgärden, i brödtexten content-type för både slutförda och misslyckade åtgärder blir application/json och innehåller detaljerad felinformation.  

## <a name="using-http-post"></a>Med hjälp av HTTP POST  
 Vissa klienter kan inte använda vissa HTTP-verb, till exempel KORRIGERINGSFIL eller ta bort. Azure Key Vault stöder HTTP POST som ett alternativ för dessa klienter, förutsatt att klienten även rubriken ”X-HTTP-METHOD” till specifika ursprungliga HTTP-verb. Stöd för HTTP POST anges för varje API som definieras i detta dokument.  

## <a name="error-responses"></a>Felsvar  
 Felhantering använder HTTP-statuskoder. Typiska resultat är:  

-   2xx – lyckades: används för normal drift. Svarstexten innehåller det förväntade resultatet  

-   3xx – Omdirigering: det 304 ”inte har ändrats” kan returneras för att uppfylla villkorlig GET. Andra 3xx koder kan användas i framtiden att ange DNS-och sökväg.  

-   4xx – klientfel: används för felaktiga begäranden, saknas nycklar, syntaxfel, ogiltiga parametrar, autentiseringsfel, osv. Svarstexten innehåller detaljerade fel förklaring.  

-   5xx – Serverfel: används för internt serverfel. Svarstexten innehåller sammanfattade felinformation.  

 Systemet är avsedd att fungera bakom en proxyserver eller brandvägg. Därför kan en klient får andra felkoder.  

 Azure Key Vault returnerar även information om fel i svarstexten när ett problem uppstår. Svarstexten är JSON-formaterade och har formatet:  

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
 Alla förfrågningar till Azure Key Vault måste autentiseras. Azure Key Vault stöder Azure Active Directory-åtkomsttoken som kan erhållas med OAuth2 [[RFC6749](http://tools.ietf.org/html/rfc6749)]. 
 
 Mer information om att registrera ditt program och autentisering för att använda Azure Key Vault finns [registrera klientprogrammet med Azure AD](https://docs.microsoft.com/rest/api/index#register-your-client-application-with-azure-ad).
 
 Åtkomsttoken måste skickas till tjänsten med hjälp av HTTP-Authorization-huvud:  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 När en åtkomst-token inte har angetts, eller när en token inte accepteras av tjänsten, returneras till klienten ett HTTP 401-fel och innehåller WWW-Authenticate-rubriken, till exempel:  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 Parametrarna för WWW-Authenticate-huvud är:  

-   auktorisering: adressen till Autentiseringstjänsten OAuth2 som kan användas för att hämta en åtkomst-token för begäran.  

-   resurs: namnet på resursen som ska användas i dess tillstånd.  

## <a name="see-also"></a>Se även  
 [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
