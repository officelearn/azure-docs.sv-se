---
title: Omdirigerings-URI/svars-URL restriktioner och begränsningar – Microsoft identity-plattformen
description: 'Begränsningar för svars-URL: er/omdirigerings-URL: er och begränsningar'
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486238"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Restriktioner och begränsningar för omdirigerings-URI/svars-URL

En omdirigerings-URI eller svars-URL är platsen att auktoriseringsservern skickas användaren till en gång appen har godkänts och beviljas en auktoriseringskod eller åtkomst-token. Kod eller token finns i omdirigerings-URI eller svara token så det är viktigt att du registrerar platsen som en del av registreringsprocessen.

## <a name="maximum-number-of-redirect-uris"></a>Maximalt antal omdirigerade URI: er

I följande tabell visar det maximala antalet omdirigerings-URI: er som du kan lägga till när du registrerar din app. 

| Konton som loggat in | Maximalt antal omdirigerade URI: er | Beskrivning |
|--------------------------|---------------------------------|-------------|
| Arbets- eller skolkonton i alla organisationens Azure Active Directory (Azure AD)-klient för Microsoft | 256 | `signInAudience` fältet i applikationsmanifestet är inställd på antingen *AzureADMyOrg* eller *AzureADMultipleOrgs* |
| Personliga Microsoft-konton och arbeta och skolkonton | 100 | `signInAudience` i applikationsmanifestet anges till *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maxlängden för URI: N

Du kan använda högst 256 tecken för varje omdirigerings-URI som du lägger till en appregistrering.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Begränsningar med jokertecken i URI: er

Wildcard URIs, till exempel `https://*.contoso.com`, är praktiska men bör undvikas. Jokertecken i omdirigeringen har URI: N säkerhetsriskerna. Enligt OAuth 2.0-specifikationen ([avsnittet 3.1.2 av RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), en slutpunktens omdirigerings-URI måste vara en absolut URI. 

Programmodell Azure AD stöder inte jokertecken URI: er för appar som är konfigurerade för att logga in personliga Microsoft-konton och arbeta eller skolkonton. Jokertecken URI: er tillåts dock för appar som är konfigurerade för att logga in arbets- eller skolkonton i organisationens Azure AD-klient idag. 
 
> [!NOTE]
> Den nya [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) upplevelse tillåter inte att utvecklare kan lägga till jokertecken URI: er i Användargränssnittet. Att lägga till wilcard URI för appar som loggar du in arbets- eller skolkonton stöds endast via app-redigeringsprogrammet för applikationsmanifestet. Framöver kommer kommer inte nya appar att kunna använda jokertecken i omdirigerings-URI. Dock äldre appar som innehåller jokertecken i omdirigering URI: er fortsätter att fungera.

Om din situation kräver mer omdirigerings-URI: er än maxgränsen som tillåts i stället för att lägga till en jokertecken omdirigerings-URI, pröva någon av följande metoder.

### <a name="use-a-state-parameter"></a>Använd en tillstånd-parameter

Om du har ett antal underordnade domäner, och om din situation kräver att omdirigera användare efter lyckad autentisering till samma sida där de började, kan det vara bra att parametern tillstånd. 

Den här metoden:

1. Skapa en ”delade” omdirigerings-URI per program att bearbeta de säkerhetstoken som du får från slutpunkten för auktorisering.
1. Ditt program kan skicka programspecifika parametrar (t.ex underdomän URL där användaren har sitt ursprung eller något som varumärkesinformation) i parametern state. När du använder en parameter för tillstånd, skydda mot CSRF skydd som anges i [avsnittet 10.12 av RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Parametrarna programspecifika omfattar all information som behövs för programmet att återge rätt upplevelse för användaren, det vill säga, skapa lämpliga programmets tillstånd. Azure AD-auktorisering endpoint remsor HTML från parametern state så se till att du skickar inte HTML innehåll i den här parametern.
1. När Azure AD skickar ett svar till ”delade” omdirigerings-URI, skickas parametern state tillbaka till programmet.
1. Programmet kan sedan använda värdet i parametern state för att avgöra vilka URL: en till ytterligare skicka användaren till. Kontrollera att du verifierar för CSRF-skydd.

> [!NOTE]
> På så sätt kan en komprometterad klient för att ändra ytterligare parametrar som skickas i parametern state därmed omdirigera användaren till en annan URL, vilket är den [öppna omdirigeraren threat](https://tools.ietf.org/html/rfc6819#section-4.2.4) som beskrivs i RFC 6819. Klienten måste därför skydda dessa parametrar genom att kryptera tillståndet eller verifierar den på annat sätt, till exempel verifiera domännamnet i omdirigerings-URI mot token.

### <a name="add-redirect-uris-to-service-principals"></a>Lägg till omdirigerings-URI: er till tjänstens huvudnamn

En annan metod är att lägga till omdirigerings-URI: er till de [tjänsthuvudnamn](app-objects-and-service-principals.md#application-and-service-principal-relationship) som representerar din appregistrering i alla Azure AD-klient. Du kan använda den här metoden när du inte använda en tillstånd-parameter eller din situation kräver att du kan lägga till nya omdirigerade URI: er i din appregistrering för varje ny klient som du stöder. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om den [programmanifest](reference-app-manifest.md)
