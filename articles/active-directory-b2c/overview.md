---
title: Vad är Azure Active Directory B2C?
description: Lär dig hur du kan använda Azure Active Directory B2C för att stödja externa identiteter i dina program, inklusive social inloggning med Facebook, Google och andra identitetsleverantörer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 61b6d5ea903d00519c58556bc99da7065741a6e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78184068"
---
# <a name="what-is-azure-active-directory-b2c"></a>Vad är Azure Active Directory B2C?

Azure Active Directory B2C tillhandahåller företags-till-kund-identitet som en tjänst. Dina kunder använder sina önskade sociala, företagsrelaterade eller lokala kontoidentiteter för att få enkel inloggning till dina program och API:er.

![Infografik för Azure AD B2C-identitetsleverantörer och underordnade program](./media/overview/azureadb2c-overview.png)

Azure Active Directory B2C (Azure AD B2C) är en CIAM-lösning (Customer Identity Access Management) som kan stödja miljontals användare och miljarder autentiseringar per dag. Det tar hand om skalning och säkerhet för autentiseringsplattformen, övervakning och automatisk hantering av hot som denial-of-service, lösenordsspray eller brute force-attacker.

## <a name="custom-branded-identity-solution"></a>Anpassad identitetslösning

Azure AD B2C är en autentiseringslösning för white label. Du kan anpassa hela användarupplevelsen med ditt varumärke så att den smälter samman sömlöst med dina webb- och mobilappar.

Anpassa varje sida som visas av Azure AD B2C när användarna registrerar sig, loggar in och ändrar sin profilinformation. Anpassa HTML-, CSS- och JavaScript-enheten i dina användarresor så att Azure AD B2C-upplevelsen ser ut och känns som om den är en infödd del av ditt program.

![Anpassade registrerings- och inloggningssidor och bakgrundsbild](./media/overview/sign-in-small.png)

## <a name="single-sign-on-access-with-a-user-provided-identity"></a>Enkel inloggningsåtkomst med en identitet som tillhandahålls av användaren

Azure AD B2C använder standardbaserade autentiseringsprotokoll som OpenID Connect, OAuth 2.0 och SAML. Den integreras med de flesta moderna applikationer och kommersiella off-the-shelf programvara.

![Diagram över identiteter från tredje part som matas till Azure AD B2C](./media/overview/scenario-singlesignon.png)

Genom att fungera som central autentiseringsmyndighet för dina webbapplikationer, mobilappar och API:er kan du med Azure AD B2C skapa en enda inloggningslösning (SSO) för dem alla. Centralisera insamlingen av användarprofil och inställningsinformation och samla in detaljerade analyser om inloggningsbeteende och registreringskonvertering.

## <a name="integrate-with-external-user-stores"></a>Integrera med externa användararkiv

Azure AD B2C tillhandahåller en katalog som rymmer 100 anpassade attribut per användare. Du kan dock också integrera med externa system. Använd till exempel Azure AD B2C för autentisering, men delegera till en extern kundrelationshantering (CRM) eller kundlojalitetsdatabas som sanningskälla för kunddata.

Ett annat scenario med externt användararkiv är att Azure AD B2C ska hantera autentiseringen för ditt program, men integrera med ett externt system som lagrar användarprofil eller personliga data. Till exempel för att uppfylla krav på datahemvist som regionala eller lokala principer för datalagring.

![Ett logiskt diagram över Azure AD B2C som kommunicerar med ett externt användararkiv](./media/overview/scenario-remoteprofile.png)

Azure AD B2C kan underlätta insamling av information från användaren under registrering eller profilredigering och sedan lämna dessa data till det externa systemet. Sedan, under framtida autentiseringar, kan Azure AD B2C hämta data från det externa systemet och, om det behövs, inkludera dem som en del av autentiseringstokensvaret som skickas till ditt program.

## <a name="progressive-profiling"></a>Progressiv profilering

Ett annat alternativ för användarens färd omfattar progressiv profilering. Progressiv profilering gör det möjligt för dina kunder att snabbt slutföra sin första transaktion genom att samla in en minimal mängd information. Samla sedan in mer profildata från kunden om framtida inloggningar.

![En visuell skildring av progressiv profilering](./media/overview/scenario-progressive.png)

## <a name="third-party-identity-verification-and-proofing"></a>Identitetskontroll och korrekturkontroll från tredje part

Använd Azure AD B2C för att underlätta identitetsverifiering och korrektur genom att samla in användardata och sedan skicka den till ett tredjepartssystem för att utföra validering, förtroendebedömning och godkännande för att skapa användarkonton.

![Ett diagram som visar användarflödet för identitetskontroll från tredje part](./media/overview/scenario-idproofing.png)

Det här är bara några av de saker du kan göra med Azure AD B2C som identitetsplattform för företag. I följande avsnitt i den här översikten får du hjälp med ett demoprogram som använder Azure AD B2C. Du är också välkommen att gå vidare direkt till en mer djupgående [teknisk översikt över Azure AD B2C](technical-overview.md).

## <a name="example-woodgrove-groceries"></a>Exempel: WoodGrove Livsmedel

[WoodGrove Groceries][woodgrove] är ett live webbprogram som skapats av Microsoft för att demonstrera flera Azure AD B2C-funktioner. De följande avsnitten granskar några av de autentiseringsalternativ som Azure AD B2C tillhandahåller på WoodGrove-webbplatsen.

### <a name="business-overview"></a>Översikt över företag

WoodGrove är en online livsmedelsbutik som säljer livsmedel till både enskilda konsumenter och företagskunder. Deras företagskunder köper livsmedel på uppdrag av sitt företag, eller företag som de hanterar.

### <a name="sign-in-options"></a>Inloggningsalternativ

WoodGrove Groceries erbjuder flera inloggningsalternativ baserat på den relation deras kunder har med butiken:

* **Enskilda** kunder kan registrera sig eller logga in med enskilda konton, till exempel med en leverantör av social identitet eller en e-postadress och ett lösenord.
* **Företagskunder** kan registrera sig eller logga in med sina företagsautentiseringsuppgifter.
* **Partners** och leverantörer är privatpersoner som förser matbutiken med produkter att sälja. Partneridentitet tillhandahålls av [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).

![Individuella (B2C), företag (B2C) och partner (B2B) inloggningssidor](./media/overview/woodgrove-overview.png)

### <a name="authenticate-individual-customers"></a>Autentisera enskilda kunder

När en kund väljer **Logga in med ditt personliga konto**omdirigeras de till en anpassad inloggningssida som finns hos Azure AD B2C. Du kan se i följande bild att vi har anpassat användargränssnittet (UI) för att se ut och känna precis som WoodGrove Groceries webbplats. WoodGroves kunder bör vara omedvetna om att autentiseringsupplevelsen är värd och skyddad av Azure AD B2C.

![Anpassad WoodGrove-inloggningssida som finns värd för Azure AD B2C](./media/overview/sign-in.png)

WoodGrove låter sina kunder registrera sig och logga in med sina Google-, Facebook- eller Microsoft-konton som identitetsleverantör. Eller så kan de registrera sig med hjälp av sin e-postadress och ett lösenord för att skapa vad som kallas ett *lokalt konto*.

När en kund väljer **Registrera dig med ditt personliga konto** och sedan registrerar sig **nu**visas en anpassad registreringssida.

![Anpassad WoodGrove-registreringssida som finns på Azure AD B2C](./media/overview/sign-up.png)

När du har angett en e-postadress och valt **Skicka verifieringskod**skickar Azure AD B2C koden till dem. När de har angett sin kod väljer du **Verifiera kod**och anger sedan annan information i formuläret, de måste också godkänna användarvillkoren.

Om du klickar på knappen **Skapa** omdirigeras Azure AD B2C till WoodGrove-webbplatsen för livsmedel. När azure AD B2C omdirigeras skickar den en OpenID Connect-autentiseringstoken till WoodGrove-webbprogrammet. Användaren är nu inloggad och redo att gå, deras visningsnamn visas i det övre högra hörnet för att visa att de är inloggade.

![WoodGrove Groceries webbplatshuvud som visar användaren är inloggad](./media/overview/signed-in-individual.png)

### <a name="authenticate-business-customers"></a>Autentisera företagskunder

När en kund väljer ett av alternativen under **Företagskunder**anropar woodgrove-webbplatsen för livsmedel en annan Azure AD B2C-princip än för enskilda kunder.

Den här principen ger användaren möjlighet att använda sina företagsautentiseringsuppgifter för registrering och inloggning. I Exemplet WoodGrove uppmanas användare att logga in med alla Office 365- eller Azure AD-konton. Den här principen använder ett Azure `/common` [AD-program med flera innehavare](../active-directory/develop/howto-convert-app-to-be-multi-tenant.md) och Azure AD-slutpunkten för att federera Azure AD B2C med alla Office 365-kunder i världen.

### <a name="authenticate-partners"></a>Autentisera partner

Länken **Logga in med ditt leverantörskonto** använder Azure Active Directory B2B:s samarbetsfunktioner. Azure AD B2B är en familj av funktioner i Azure Active Directory för att hantera partneridentiteter. Dessa identiteter kan federeras från Azure Active Directory för åtkomst till Azure AD B2C-skyddade program.

Läs mer om Azure AD B2B i [Vad är gästanvändaråtkomst i Azure Active Directory B2B?](../active-directory/b2b/what-is-b2b.md).

<!-- UNCOMMENT WHEN REPO IS UPDATED WITH LATEST DEMO CODE
### Sample code

If you'd like to jump right into the code to see how the WoodGrove Groceries application is built, you can find the repository on GitHub:

[Azure-Samples/active-directory-external-identities-woodgrove-demo][woodgrove-repo] (GitHub)
-->

## <a name="next-steps"></a>Nästa steg

Nu när du har en uppfattning om vad Azure AD B2C är och några av de scenarier som det kan hjälpa till med, gräva lite djupare i dess funktioner och tekniska aspekter.

> [!div class="nextstepaction"]
> [Teknisk översikt för Azure AD B2C >](technical-overview.md)

<!-- LINKS - External -->
[woodgrove]: https://aka.ms/ciamdemo
[woodgrove-repo]: https://github.com/Azure-Samples/active-directory-external-identities-woodgrove-demo
