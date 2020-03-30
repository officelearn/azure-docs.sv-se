---
title: Ensidiga program i Azure Active Directory
description: Beskriver vad ensidiga program (SPA) är och grunderna för protokollflöde, registrering och tokenförfallodatum för den här apptypen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154754"
---
# <a name="single-page-applications"></a>Ensidiga program

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Ensidiga program (SPA) är vanligtvis strukturerade som ett JavaScript-presentationslager (frontend) som körs i webbläsaren och en webb-API-server som körs på en server och implementerar programmets affärslogik. Mer information om det implicita auktoriseringsbidraget och hjälper dig att avgöra om det är rätt för ditt programscenario finns [i Förstå det implicita bidragsflödet för OAuth2 i Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

I det här fallet när användaren loggar in använder JavaScript-klientdelen [Active Directory Authentication Library for JavaScript (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) och det implicita auktoriseringsbidraget för att hämta en ID-token (id_token) från Azure AD. Token cachelagras och klienten bifogar den till begäran som innehavartoken när anrop görs till webb-API-serverdelen, som är skyddad med hjälp av OWIN-mellanprogram.

## <a name="diagram"></a>Diagram

![Programdiagram på en sida](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Protokollflöde

1. Användaren navigerar till webbprogrammet.
1. Programmet returnerar JavaScript-klientdelen (presentationslagret) till webbläsaren.
1. Användaren initierar inloggning, till exempel genom att klicka på en inloggningslänk. Webbläsaren skickar en GET till slutpunkten för Azure AD-auktorisering för att begära en ID-token. Den här begäran innehåller program-ID och svars-URL i frågeparametrarna.
1. Azure AD validerar svars-URL:en mot den registrerade svars-URL:en som konfigurerades i Azure-portalen.
1. Användaren loggar in på inloggningssidan.
1. Om autentiseringen lyckas skapar Azure AD en ID-token och returnerar den som ett URL-fragment (#) till programmets svars-URL. För ett produktionsprogram ska den här svars-URL:en vara HTTPS. Den returnerade token innehåller anspråk om användaren och Azure AD som krävs av programmet för att validera token.
1. JavaScript-klientkoden som körs i webbläsaren extraherar token från svaret som ska användas för att skydda anrop till programmets webb-API-serverdel.
1. Webbläsaren anropar programmets webb-API-serverdel med ID-token i auktoriseringshuvudet. Azure AD-autentiseringstjänsten utfärdar en ID-token som kan användas som en bäraretoken om resursen är samma som klient-ID (i det här fallet gäller detta eftersom webb-API:et är appens egen serverdel).

## <a name="code-samples"></a>Kodexempel

Se [kodexemplen för ensidiga programscenarier](sample-v1-code.md#single-page-applications). Var noga med att komma tillbaka ofta som nya prover läggs till ofta.

## <a name="app-registration"></a>Appregistrering

* En enda klient - Om du skapar ett program bara för din organisation måste det registreras i företagets katalog med hjälp av Azure-portalen.
* Flera innehavare - Om du skapar ett program som kan användas av användare utanför organisationen måste det vara registrerat i företagets katalog, men måste också registreras i varje organisations katalog som ska använda programmet. Om du vill göra ditt program tillgängligt i deras katalog kan du inkludera en registreringsprocess för dina kunder som gör det möjligt för dem att godkänna ditt program. När de registrerar sig för ditt program visas en dialogruta som visar de behörigheter som programmet kräver och sedan alternativet att godkänna. Beroende på vilka behörigheter som krävs kan en administratör i den andra organisationen behöva ge sitt samtycke. När användaren eller administratören samtycker registreras programmet i deras katalog.

När du har registrerat programmet måste det vara konfigurerat för att använda OAuth 2.0 implicit beviljandeprotokoll. Som standard är det här protokollet inaktiverat för program. Om du vill aktivera OAuth2 implicit beviljande protokoll för ditt program, redigera dess programmanifest från Azure-portalen och ange "oauth2AllowImplicitFlow" värde till sant. Mer information finns i [Programmanifest](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Token förfallodatum

Använda ADAL.js hjälper till med:

* Uppdatera en token som har upphört att gälla
* Begära en åtkomsttoken för att anropa en webb-API-resurs

Efter en lyckad autentisering skriver Azure AD en cookie i användarens webbläsare för att upprätta en session. Observera att sessionen finns mellan användaren och Azure AD (inte mellan användaren och webbprogrammet). När en token upphör att gälla använder ADAL.js den här sessionen för att tyst hämta en annan token. ADAL.js använder en dold iFrame för att skicka och ta emot begäran med OAuth implicit beviljande protokoll. ADAL.js kan också använda samma mekanism för att tyst hämta åtkomsttoken för andra webb-API-resurser som programmet anropar så länge dessa resurser stöder resursdelning mellan ursprung (CORS), registreras i användarens katalog och eventuellt samtycke krävdes som ges av användaren under inloggningen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [programtyper och scenarier](app-types.md)
* Lär dig mer om grunderna i Azure [AD-autentisering](v1-authentication-scenarios.md)
