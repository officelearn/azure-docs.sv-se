---
title: Ansökningstyper i v1.0 | Azure
description: Beskriver de typer av appar och scenarier som stöds av Slutpunkten för Azure Active Directory v2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c290cbf36fd53d5afb5fd805cda896fb6879bb4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154958"
---
# <a name="application-types-in-v10"></a>Programtyper i v1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) stöder autentisering för en mängd moderna apparkitekturer, alla baserade på branschstandardprotokoll OAuth 2.0 eller OpenID Connect.

Följande diagram illustrerar scenarier och programtyper och hur olika komponenter kan läggas till:

![Programtyper och scenarier](./media/authentication-scenarios/application-types-scenarios.png)

Det här är de fem primära programscenarierna som stöds av Azure AD:

- **[Spa (Single page Application)](single-page-application.md)**: En användare måste logga in på ett ensidigt program som skyddas av Azure AD.
- **[Webbläsare till webbprogram](web-app.md)**: En användare måste logga in på ett webbprogram som skyddas av Azure AD.
- **[Native program till webb-API:](native-app.md)** Ett inbyggt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
- **[Webbprogram till webb-API:](web-api.md)** Ett webbprogram måste hämta resurser från ett webb-API som skyddas av Azure AD.
- **[Daemon eller serverprogram till webb-API:](service-to-service.md)** Ett daemonprogram eller ett serverprogram utan webbanvändargränssnitt måste hämta resurser från ett webb-API som skyddas av Azure AD.

Följ länkarna om du vill veta mer om varje typ av app och förstå scenarierna på hög nivå innan du börjar arbeta med koden. Du kan också lära dig om de skillnader du behöver veta när du skriver en viss app som fungerar med v1.0-slutpunkten eller v2.0-slutpunkten.

> [!NOTE]
> V2.0-slutpunkten stöder inte alla Azure AD-scenarier och -funktioner. För att avgöra om du ska använda v2.0-slutpunkten läser du om [v2.0-begränsningar](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Du kan utveckla någon av de appar och scenarier som beskrivs här med hjälp av olika språk och plattformar. De är alla backas upp av kompletta kodexempel som finns i koden prover guide: [v1.0 kodexempel efter scenario](sample-v1-code.md) och [v2.0 kodexempel efter scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). Du kan också hämta kodexemplen direkt från motsvarande [GitHub-exempeldatabaser](https://github.com/Azure-Samples?q=active-directory).

Om ditt program behöver en viss del eller ett visst segment i ett end-to-end-scenario kan dessutom funktioner läggas till oberoende av dem. Om du till exempel har ett inbyggt program som anropar ett webb-API kan du enkelt lägga till ett webbprogram som också anropar webb-API:et.

## <a name="app-registration"></a>Appregistrering

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrera en app som använder slutpunkten Azure AD v1.0

Alla program som lägger ut autentisering till Azure AD måste registreras i en katalog. Det här steget innebär att berätta för Azure AD om ditt program, inklusive webbadressen där det finns, url:en för att skicka svar efter autentisering, URI för att identifiera ditt program med mera. Denna information krävs av några viktiga skäl:

* Azure AD måste kommunicera med programmet när du hanterar inloggning eller utbyte av token. Informationen som skickas mellan Azure AD och programmet innehåller följande:
  
  * **Program-ID URI** - Identifieraren för ett program. Det här värdet skickas till Azure AD under autentisering för att ange vilket program som anroparen vill ha en token för. Dessutom inkluderas det här värdet i token så att programmet vet att det var det avsedda målet.
  * **Svara på URL** och **Omdirigera URI** - För ett webb-API eller webbprogram är svars-URL:en den plats där Azure AD skickar autentiseringssvaret, inklusive en token om autentiseringen lyckades. För ett inbyggt program är Redirect URI en unik identifierare som Azure AD omdirigerar användaragenten till i en OAuth 2.0-begäran.
  * **Program-ID** - ID för ett program som genereras av Azure AD när programmet registreras. När du begär en auktoriseringskod eller token skickas program-ID och nyckel till Azure AD under autentiseringen.
  * **Nyckel** - Nyckeln som skickas tillsammans med ett program-ID när du autentiserar till Azure AD för att anropa ett webb-API.
* Azure AD måste se till att programmet har de behörigheter som krävs för att komma åt dina katalogdata, andra program i organisationen och så vidare.

Mer information finns i hur du [registrerar en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="single-tenant-and-multi-tenant-apps"></a>Appar med en innehavare och flera innehavare

Etableringen blir tydligare när du förstår att det finns två kategorier av program som kan utvecklas och integreras med Azure AD:

* **En klientprogram** - Ett enda klientprogram är avsett att användas i en organisation. Dessa är vanligtvis line-of-business (LoB) program skrivna av en företagsutvecklare. Ett enda klientprogram behöver bara nås av användare i en katalog, och därför behöver det bara etableras i en katalog. Dessa program registreras vanligtvis av en utvecklare i organisationen.
* **Program för flera innehavare** - Ett program med flera innehavare är avsett att användas i många organisationer, inte bara en organisation. Det kallas vanligen för SaaS-webbprogram (Software as a Service) och skrivs av en oberoende programvaruleverantör (ISV). Program med flera innehavare måste etableras i varje katalog där de ska användas, vilket kräver användarens eller administratörens medgivande för att registrera dem. Medgivandeprocessen startar när ett program har registrerats i katalogen och ges åtkomst till Graph API eller kanske någon annan webb-API. När en användare eller administratör från en annan organisation registrerar sig för att använda programmet visas de med en dialogruta som visar de behörigheter som programmet kräver. Användaren eller administratören kan sedan samtycka till programmet, vilket ger programmet tillgång till angivna data, och slutligen registrerar programmet i sin katalog. Mer information finns [i Översikt över ramverket för medgivande](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Ytterligare överväganden vid utveckling av appar för en eller flera innehavare

Vissa ytterligare överväganden uppstår när du utvecklar ett program för flera innehavare i stället för ett enda klientprogram. Om du till exempel gör ditt program tillgängligt för användare i flera kataloger behöver du en mekanism för att avgöra vilken klientorganisation de befinner sig i. Ett enda klientprogram behöver bara leta i sin egen katalog för en användare, medan ett program med flera innehavare måste identifiera en viss användare från alla kataloger i Azure AD. För att utföra den här uppgiften tillhandahåller Azure AD en gemensam autentiseringsslutpunkt där alla program för flera innehavare kan styra inloggningsbegäranden i stället för en klientspecifik slutpunkt. Den här `https://login.microsoftonline.com/common` slutpunkten är för alla kataloger i Azure AD, medan en klientspecifik slutpunkt kan vara `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Den gemensamma slutpunkten är särskilt viktig att tänka på när du utvecklar ditt program eftersom du behöver den nödvändiga logiken för att hantera flera klienter under inloggning, signering och tokenverifiering.

Om du för närvarande utvecklar ett enda klientprogram men vill göra det tillgängligt för många organisationer kan du enkelt göra ändringar i programmet och dess konfiguration i Azure AD för att göra det kompatibelt med flera innehavare. Dessutom använder Azure AD samma signeringsnyckel för alla token i alla kataloger, oavsett om du tillhandahåller autentisering i ett enda klient- eller program för flera innehavare.

Varje scenario som anges i det här dokumentet innehåller ett underavsnitt som beskriver dess etableringskrav. Mer detaljerad information om hur du etablerar ett program i Azure AD och skillnaderna mellan program med enstaka och flera innehavare finns i [Integrera program med Azure Active Directory](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) för mer information. Fortsätt läsa för att förstå de vanliga programscenarierna i Azure AD.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra grunderna i Azure [AD-autentisering](v1-authentication-scenarios.md)
