---
title: Programtyper i v1.0 | Azure
description: Beskriver vilka typer av appar och scenarier som stöds av Azure Active Directory v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 5a8e43934393c2e6a97b81809284781a5c207e41
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816352"
---
# <a name="application-types-in-v10"></a>Programtyper i v1.0

Azure Active Directory (Azure AD) stöder autentisering för en mängd olika moderna apparkitekturer alla baserat på branschstandardprotokollen OAuth 2.0 eller OpenID Connect.

Följande diagram illustrerar scenarier och programtyper, och hur du kan lägga till olika komponenter:

![Programtyper och scenarier](./media/authentication-scenarios/application_types_and_scenarios.png)

Det här är de fem primära programmet scenarier som stöds av Azure AD:

- **[Ensidesapplikation (SPA)](single-page-application.md)**: en användare måste logga in på ett enkelsidigt program som skyddas av Azure AD.
- **[Webbläsarens webbprogram](web-app.md)**: en användare måste logga in till ett webbprogram som skyddas av Azure AD.
- **[Internt program till webb-API](native-app.md)**: ett internt program som körs på en telefon, surfplatta eller dator måste autentisera en användare för att hämta resurser från ett webb-API som skyddas av Azure AD.
- **[Webbprogram till webb-API](web-api.md)**: ett program behöver få resurser från ett webb-API som skyddas av Azure AD.
- **[Daemon eller ett serverprogram till webb-API](service-to-service.md)**: en daemon-program eller ett serverprogram utan användargränssnitt för web måste hämta resurser från ett webb-API som skyddas av Azure AD.

Följ länkarna för att lära dig mer om varje typ av app och förstå de övergripande scenarierna innan du börjar arbeta med kod. Du kan också läsa om skillnader som du behöver veta när du skriver en viss app som fungerar med v1.0 slutpunkten eller v2.0-slutpunkten.

> [!NOTE]
> V2.0-slutpunkten stöder inte alla Azure AD-scenarier och funktioner. Läs mer om för att avgöra om du ska använda v2.0-slutpunkten, [v2.0 begränsningar](active-directory-v2-limitations.md).

Du kan utveckla appar och scenarier som beskrivs här på olika språk och plattformar. De är kompletterade med fullständig kodexempel i guiden kod-exempel: [v1.0 kodexempel efter scenario](sample-v1-code.md) och [v2.0-kodexempel efter scenario](sample-v2-code.md). Du kan också hämta kodexemplen direkt från motsvarande [GitHub-lagringsplatser för exemplet](https://github.com/Azure-Samples?q=active-directory).

Dessutom, om ditt program kräver en viss typ eller segment i ett scenario för slutpunkt till slutpunkt, kan i de flesta fall att funktionerna läggas oberoende av varandra. Till exempel om du har ett internt program som anropar ett webb-API kan du kan enkelt lägga till ett webbprogram som också anropar webb-API.

## <a name="app-registration"></a>Appregistrering

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrera en app som använder Azure AD v1.0-slutpunkten

Alla program som outsources autentisering till Azure AD måste registreras i en katalog. Det här steget ska du tala om Azure AD om ditt program, inklusive URL: en där den finns, URL: en att skicka svar efter autentisering, URI: N att identifiera ditt program och mycket mer. Den här informationen krävs för några viktiga skäl:

* Azure AD behöver kommunicera med programmet vid hantering av inloggnings- eller utbyte token. Informationen som skickas mellan Azure AD och programmet innehåller följande:
  
  * **Program-ID-URI** -ID för ett program. Det här värdet skickas till Azure AD under autentiseringen för att indikera vilket program anroparen vill ha en token för. Dessutom inkluderas det här värdet i token så att programmet vet att det var det avsedda målet.
  * **Svars-URL** och **omdirigerings-URI** – för en webb-API eller ett webbprogram, svars-URL är den plats där Azure AD skickar autentiseringssvaret, inklusive en token om autentiseringen lyckades. För ett internt program är omdirigerings-URI en unik identifierare till vilken Azure AD omdirigerar användaragenten i ett OAuth 2.0-begäran.
  * **Program-ID** -ID för ett program som genereras av Azure AD när programmet har registrerats. När du begär en auktoriseringskod eller token, skickas det program-ID och nyckel till Azure AD under autentiseringen.
  * **Nyckeln** -nyckeln som ska skickas tillsammans med ett program-ID när du autentiserar till Azure AD att anropa ett webb-API.
* Azure AD behöver att säkerställa att programmet har behörigheterna som krävs för att komma åt dina katalogdata, andra program i din organisation, och så vidare.

Mer information lär du dig hur du [registrera en app med Azure AD v1.0 slutpunkten](quickstart-v1-add-azure-ad-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Appar för enstaka klientorganisationer och flera innehavare

Etablering blir tydligare när du förstår att det finns två typer av program som kan utvecklas och integrerad med Azure AD:

* **Program för klienter med enkel** – en enda klient-programmet är avsett att användas i en organisation. Det här är vanligtvis line-of-business (LoB) program som skrivits av företagsutvecklare. En enda klient behöver bara användas av användare i en katalog och därför kan den bara behöver etableras i en katalog. Programmen registreras vanligtvis av en utvecklare i organisationen.
* **Program med flera innehavare** -program med flera innehavare är avsedd att användas i många organisationer är inte bara en organisation. Det här är vanligtvis software-as-a-service (SaaS)-program som skrivits av en oberoende programvaruleverantör (ISV). Program för flera innehavare måste tillhandahållas i varje katalog där de ska användas, vilket kräver att användaren eller administratören tillstånd att registrera dem. Förfarandet medgivande startar när ett program som har registrerats i katalogen och ges åtkomst till Graph API eller kanske en annan webb-API. När en användare eller administratör från en annan organisation registrerar sig att använda programmet, visas en dialogruta som visar de behörigheter som programmet kräver. Användaren eller administratören kan sedan godkänna att programmet, vilket ger programmet åtkomst till den angivna data och slutligen registrerar programmet i sin katalog. Mer information finns i [översikt över ramverket för medgivande](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Ytterligare överväganden när du utvecklar en eller flera klient apps

Vissa ytterligare överväganden uppstå när du utvecklar ett program för flera klienter i stället för en enskild klient-program. Till exempel om du gör ditt program tillgängligt för användare i flera kataloger, måste en mekanism för att avgöra vilken klientorganisation som de finns i. En enskild klient behöver bara titta i sin egen katalog för en användare, även om ett program för flera innehavare behöver identifiera en viss användare från alla kataloger i Azure AD. Om du vill utföra den här uppgiften, tillhandahåller Azure AD en gemensam autentiseringsslutpunkt där alla program med flera innehavare kan dirigera inloggningsförfrågningar, i stället för en klientspecifik slutpunkt. Den här slutpunkten är https://login.microsoftonline.com/common för alla kataloger i Azure AD, medan en klientspecifik slutpunkt kan vara https://login.microsoftonline.com/contoso.onmicrosoft.com. Vanliga slutpunkten är särskilt viktigt att tänka på när du utvecklar ditt program eftersom du behöver den nödvändiga logiken för att hantera flera klienter under inloggning, utloggning, och tokenvalidering.

Om du utvecklar för närvarande en enda klient-program, men vill göra den tillgänglig för många organisationer, kan du enkelt ändra programmet och dess konfiguration i Azure AD för att göra det flera innehavare kan. Dessutom använder Azure AD samma signeringsnyckeln för alla token i alla kataloger om du tillhandahåller autentisering i en enda klient eller program med flera innehavare.

Varje scenario som beskrivs i det här dokumentet innehåller ett avsnitt som beskriver dess etablering krav. Mer detaljerad information om att etablera ett program i Azure AD, samt skillnaderna mellan enstaka och flera innehavare program finns i [integrera program med Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) för mer information. Läs vidare för att förstå de vanliga Programscenarier i Azure AD.

## <a name="next-steps"></a>Nästa steg

- Läs mer om andra Azure AD [grunder](authentication-scenarios.md)
