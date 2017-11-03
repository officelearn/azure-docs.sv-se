---
title: "Använd Azure AD-autentisering att få åtkomst till Azure Media Services-API med övriga | Microsoft Docs"
description: "Lär dig hur du kommer åt Azure Media Services API med Azure Active Directory-autentisering med hjälp av REST."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 1c62857699fb29b3583363e1c6f2dc7874635f40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Använd Azure AD-autentisering för åtkomst till Azure Media Services-API med övriga

Azure Media Services-teamet har publicerat Azure Active Directory (AD Azure) autentiseringsstöd för åtkomst till Azure Media Services. Det har också meddelat planer för att inaktualisera Azure Access Control service autentisering för åtkomst till Media Services. Eftersom varje Azure-prenumeration och alla Media Services-konto är kopplat till en Azure AD-klient, ger stöd för Azure AD-autentisering många fördelar för säkerheten. Mer information om den här ändringen och migrering (om du använder Media Services .NET SDK för din app) finns i följande blogginlägg och artiklar:

- [Azure Media Services annonserar stöd för Azure AD och utfasningen av Access Control-autentisering](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation)
- [Åtkomst till Azure Media Services API med hjälp av Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md)
- [Använda Azure AD-autentisering för att komma åt Azure Media Services API med hjälp av Microsoft .NET](media-services-dotnet-get-started-with-aad.md)
- [Komma igång med Azure AD authentication med hjälp av Azure portal](media-services-portal-get-started-with-aad.md)

Vissa kunder behöver utveckla sina lösningar för Media Services under följande begränsningar:

*   De använder ett programmeringsspråk som inte är Microsoft .NET- eller C# eller körningsmiljön är inte Windows.
*   Azure AD-bibliotek, till exempel Active Directory-Autentiseringsbibliotek är inte tillgängliga för programmeringsspråket eller kan inte användas för sina körningsmiljön.

Vissa kunder har utvecklat program med hjälp av REST API för både autentisering för åtkomstkontroll och Azure Media Services. Du behöver ett sätt att använda REST API för Azure AD-autentisering och efterföljande Azure Media Services-åtkomst för dessa kunder. Du behöver inte förlita dig på någon av Azure AD-bibliotek eller på Media Services .NET SDK. I den här artikeln vi beskriver en lösning och ger exempelkod för det här scenariot. Eftersom koden är alla REST API-anrop med inga beroende på Azure AD eller Azure Media Services-biblioteket koden kan enkelt översättas till alla andra programmeringsspråk.

> [!IMPORTANT]
> Media Services stöder för närvarande Azure Access Control services autentisering-modell. Access Control-autentisering kommer dock föråldrad den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentisering så snart som möjligt.


## <a name="design"></a>Designa

I den här artikeln använder vi följande designen för autentisering och auktorisering:

*  Authorization protocol: OAuth 2.0. OAuth 2.0 är en standard för web säkerhet som omfattar både autentisering och auktorisering. Det stöds av Google, Microsoft, Facebook och PayPal. Det har ratificerat oktober 2012. Microsoft stöder ordentligt OAuth 2.0 och OpenID Connect. Båda dessa normer stöds i flera tjänster och klientbibliotek, inklusive Azure Active Directory, öppna Webbgränssnitt för .NET (OWIN) Katana och Azure AD-bibliotek.
*  Bevilja: bevilja klientens autentiseringsuppgifter. Klientens autentiseringsuppgifter är en av de fyra grant-typerna för OAuth 2.0. Det används ofta för Azure AD Microsoft Graph API-åtkomst.
*  Autentiseringsläge: tjänstens huvudnamn. Andra autentiseringsläget är användare eller interaktiv autentisering.

Summan av fyra program eller tjänster som är inblandade i Azure AD-autentisering och auktorisering flödet för med Media Services. Program och tjänster och flödet, beskrivs i följande tabell:

|Programtyp |Program |Flöde|
|---|---|---|
|Client | Kunden app eller lösning | Den här appen (faktiskt, dess proxy) har registrerats i Azure AD-klient som Azure-prenumerationen och media service-konto finns. Tjänstens huvudnamn för den registrerade appen beviljas sedan med rollen ägare eller deltagare i den åtkomst kontrollen (IAM) för media-tjänstkontot. Tjänstens huvudnamn representeras av app-ID och klienten klienthemligheten. |
|Identitetsprovider (IDP) | Azure AD som IDP | Den registrerade app tjänsten huvudnamn (klient-ID och klienthemlighet) autentiseras av Azure AD som IDP. Autentiseringen utförs internt och implicit. Klienten är autentiserad istället för användaren som klientautentiseringsuppgifter. |
|Skydda säkerhetstokentjänst (STS) / OAuth-server | Azure AD som STS | Efter autentisering av IDP (eller OAuth-Server i OAuth 2.0-villkor), en åtkomst-token eller en JSON-Webbtoken (JWT) är utfärdat av Azure AD som STS/OAuth-Server för åtkomst till resursen mellannivå: i det här fallet Media Services REST API-slutpunkt. |
|Resurs | Media Services REST API | Varje Media Services REST API-anrop är auktoriserad genom en åtkomst-token som utfärdas av Azure AD som STS eller OAuth-servern. |

Om du kör exempelkoden och avbilda en JWT eller en åtkomst-token har JWT följande attribut:

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

Här följer mappningar mellan attribut i JWT och fyra program eller tjänster i tabellen ovan:

|Programtyp |Program |JWT-attribut |
|---|---|---|
|Client |Kunden app eller lösning |AppID: ”02ed1e8e-af8b-477e-af3d-7e7219a99ac6”. Klient-ID för ett program som du ska registrera till Azure AD i nästa avsnitt. |
|Identitetsprovider (IDP) | Azure AD som IDP |IDP: ”https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/”.  GUID är ID för Microsoft-klient (microsoft.onmicrosoft.com). Varje innehavare har sin egen, unikt ID. |
|Skydda säkerhetstokentjänst (STS) / OAuth-server |Azure AD som STS | ISS: ”https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/”. GUID är ID för Microsoft-klient (microsoft.onmicrosoft.com). |
|Resurs | Media Services REST API |eller: ”https://rest.media.azure.net”. Mottagaren eller målgruppen för åtkomst-token. |

## <a name="steps-for-setup"></a>Steg för installation

Att registrera och konfigurera Azure AD-program för Azure AD-autentisering och få en åtkomsttoken för Azure Media Services REST API-slutpunkt anropas, gör du följande:

1.  I den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), registrera en Azure AD-program (till exempel wzmediaservice) till Azure AD-klient (till exempel microsoft.onmicrosoft.com). Det spelar ingen roll om du har registrerat som webbapp eller inbyggda appen. Du kan också välja alla inloggnings-URL och reply-URL (till exempel http://wzmediaservice.com för både).
2. I den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=213885), gå till den **konfigurera** fliken i ditt program. Observera den **klient-ID**. Sedan, under **nycklar**, generera en **klientnyckel** (klienthemlighet). 

    > [!NOTE] 
    > Anteckna klienthemligheten. Det kommer inte att visas igen.
    
3.  I den [Azure-portalen](http://ms.portal.azure.com), gå till Media Services-kontot. Välj den **åtkomstkontroll** (IAM)-fönstret. Lägg till en ny medlem som har ägaren eller deltagarrollen. Huvudnamn, söka efter namnet på programmet som du har registrerat i steg 1 (i det här exemplet wzmediaservice).

## <a name="info-to-collect"></a>Information för att samla in

Samla in följande datapunkter ska ingå i koden för att förbereda REST kodning:

*   Azure AD som en STS-slutpunkt: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. En JWT-åtkomsttoken har begärts från den här slutpunkten. Förutom fungerar som en IDP fungerar även Azure AD som en Säkerhetstokentjänst. Azure AD utfärdar en JWT för åtkomst till företagsresurser (en åtkomst-token). En JWT-token har olika krav.
*   Azure Media Services REST API som resurs eller en publik: https://rest.media.azure.net.
*   Klient-ID: Se steg 2 i [steg för att installationen](#steps-for-setup).
*   Klienthemligheten: se steg 2 i [steg för att installationen](#steps-for-setup).
*   Media Services konto REST API-slutpunkt i följande format:

    https://[media_service_account_name].restv2. [data_center].media.azure.net/API 

    Detta är den slutpunkt mot vilken alla Media Services REST API-anrop i ditt program görs. Till exempel https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

Du kan sedan lägga parametrarna fem i filen web.config eller app.config, i din kod.

## <a name="sample-code"></a>Exempelkod

Du kan hitta exempelkoden i [Azure AD-autentisering för Azure Media Services-åtkomst: båda via REST API](https://github.com/willzhan/WAMSRESTSoln).

Exempelkoden består av två delar:

*   En DLL-biblioteksprojekt som har alla REST API-koden för Azure AD-autentisering och auktorisering. Det har också en metod för att göra REST API-anrop till Media Services REST API-slutpunkten med den åtkomst-token.
*   En konsol test-klient som initierar Azure AD-autentisering och annan Media Services REST API-anrop.

Exempelprojektet har tre funktioner:

*   Azure AD-autentiseringar via klientens autentiseringsuppgifter ger med hjälp av REST API.
*   Azure Media Services-åtkomst med hjälp av REST API.
*   Azure Storage-åtkomst med hjälp av endast REST API (som används för att skapa ett Media Services-konto med hjälp av REST-API).


## <a name="where-is-the-refresh-token"></a>Där är uppdateringstoken?

Vissa webbläsare kan be: där är uppdateringstoken? Varför inte använda en uppdateringstoken här?

Syftet med en uppdateringstoken är inte att uppdatera en åtkomst-token. I stället är den utformad att kringgå autentisering eller användaren någon åtgärd från slutanvändaren och fortfarande få en giltig åtkomsttoken när ett tidigare token upphör att gälla. Ett bättre namn för en uppdateringstoken kan vara ungefär ”kringgå återexport-sign in användartoken”.

Om du använder OAuth 2.0 auktorisering bevilja flöde (användarnamn och lösenord, fungerar för en användares räkning), kan du hämta ett förnyat åtkomsttoken utan åtgärder från användaren begär en uppdateringstoken. För OAuth 2.0 klientens autentiseringsuppgifter bevilja flödet som beskrivs i den här artikeln, fungerar dock klienten för sin egen räkning. Du behöver inte användaren behöver göra något alls och auktorisering servern behöver inte (och inte) ger dig en uppdateringstoken. Om du felsöka den **GetUrlEncodedJWT** metod du märker att svaret från token slutpunkten har en åtkomst-token, men ingen uppdateringstoken.

## <a name="next-steps"></a>Nästa steg

Kom igång med [överföringen av filer till ditt konto](media-services-dotnet-upload-files.md).
