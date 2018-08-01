---
title: Hantera program med Azure Active Directory | Microsoft Docs
description: Den här artikeln fördelarna med att integrera Azure Active Directory med din lokala, moln och SaaS-program.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: bf53829a2d2578132f9a3595c0bac5e8eb588916
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366779"
---
# <a name="managing-applications-with-azure-active-directory"></a>Hantera program med Azure Active Directory
Utöver det faktiska arbetsflödet eller innehåll har företag två grundläggande krav för alla program:

1. För att öka produktiviteten, ska program vara enkelt att upptäcka och komma åt
2. Om du vill aktivera säkerhet och styrning organisationen som behöver kontroll och översikt över vem som kan komma åt och vem som faktiskt åtkomst till varje program

I världen med molnprogram, detta kan bäst uppnås med hjälp av identitet för att styra ”*vem som får göra vad*”.

I databehandling terminologi:

* *Som* kallas *identitet* -hanteringen av användare och grupper
* *Vad* kallas *åtkomsthantering* – hantering av åtkomst till skyddade resurser

Båda komponenterna tillsammans kallas *identitet och åtkomst hantering (IAM)*, som definieras av den [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) som ”*security-gren som kan enskilda till rätt personer få åtkomst till rätt resurser till höger gånger på grund av rätt*”.

OK, så vad är problemet? Om IAM är *inte hanteras* på en plats med en integrerad lösning:

* Identitetsadministratörer behöver skapa eller uppdatera användarkonton i alla program separat, individuellt aktiviteten redundant och tidskrävande.
* Användare måste memorera olika autentiseringsuppgifter för att komma åt de program som de behöver för att arbeta med. Därmed tenderar användare att skriva ned sina lösenord eller använda andra lösningar för hantering av lösenord. Följande alternativ säkerhetsrisker andra data.
* Redundant, tidskrävande aktiviteter minska den tid som användaren och administratörer tillbringar på företagsaktiviteter som ökar din verksamhet slutresultat.

Så vad förhindrar Allmänt organisationer från att anta integrerade IAM-lösningar?

* De flesta tekniska lösningar baseras på programvaruplattformar som måste distribueras och anpassas av varje organisation för sina egna program.
* Molnprogram antas ofta till en högre kostnad än IT-organisationen kan integreras med befintliga IAM-lösningar.
* Säkerhet och övervakning verktyg kräver ytterligare anpassning och integrering för att uppnå omfattande E2E-scenarier.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory-integrerad med program
Azure Active Directory är Microsofts omfattande identitet som en tjänst (IDaaS)-lösning som:

* Aktiverar IAM som en tjänst i molnet 
* Ger central hantering, enkel inloggning (SSO) och rapportering 
* Stöder integrerad åtkomsthantering för [tusentals program](https://azure.microsoft.com/marketplace/active-directory/) i programgalleriet, inklusive Salesforce, Google Apps, Box, Concur med mera. 

Med Azure Active Directory, alla program som du publicerar för partner och kunder (företag eller konsumenter) får samma identitet och åtkomst till funktioner för hantering.<br> På så sätt kan du avsevärt minska dina driftskostnader.

Vad händer om du behöver implementera ett program som ännu inte visas i programgalleriet? Även om det är lite mer tidskrävande än att konfigurera enkel inloggning till program från galleriet för ger Azure AD dig en guide som hjälper dig med konfigurationen.

Värdet för Azure AD är mer omfattande än ”bara” molnprogram. Du kan också använda det med dina lokala program genom att tillhandahålla säker fjärråtkomst. Med säker fjärråtkomst, kan du behöver för VPN eller andra traditionella fjärråtkomst management-implementeringar.

Azure AD tillhandahåller lösningen huvudsakliga datasäkerhet och produktivitet problem genom att tillhandahålla hantering av central och enkel inloggning (SSO) för alla dina program.

* Användare kan komma åt flera program med en inloggning vilket ger mer tid till intäkter generering eller företag operations aktiviteter klar.
* Identitetsadministratörer kan hantera åtkomst till program i ett och samma ställe.

Fördelen för användaren och för ditt företag är uppenbara. Låt oss ta en närmare titt på fördelarna för en identity-administratör och organisationen.

## <a name="integrated-application-benefits"></a>Integrerade programmet fördelar
SSO-processen har två steg:

* Autentisering, processen med att validera användarens identitet.
* Auktorisering beslutet att aktivera eller blockera åtkomst till en resurs med en åtkomstprincip.

När du använder Azure AD och hantera program och aktivera enkel inloggning:

* Autentiseringen görs på användarens lokala (t.ex. AD) eller Azure AD-konto.
* Auktorisering körs på Azure AD tilldelning och skydd principen att säkerställa konsekvent användarupplevelse och gör att du kan lägga till tilldelning, platser och MFA villkor på alla program, oavsett dess interna funktioner.

Det är viktigt att förstå som hur auktoriseringen trätt i kraft på målprogrammet varierar beroende på hur programmet har integrerat med Azure AD.

* **Program förintegrerade efter tjänstleverantör** som Office 365 och Azure, de är program som skapats direkt på Azure AD och förlita dig på den för sina omfattande funktioner för identitets- och åtkomsthantering. Åtkomst till dessa program är aktiverat via kataloginformation och utfärdande.
* **Redan integrerade program från Microsoft och anpassade program** dessa är oberoende molnprogram som förlitar sig på en intern programkatalogen och kan köras oberoende av Azure AD. Åtkomst till programmen aktiveras genom att utfärda en programspecifik autentiseringsuppgift som mappas till ett program-konto. Beroende på programfunktioner vara autentiseringsuppgifterna en federation token eller användarnamn och lösenord för ett konto som etablerades tidigare i programmet.
* **Lokala program** program publiceras via Azure AD-programproxy som främst att aktivera åtkomst till lokala program. Dessa program är beroende av en central en lokal katalog som Windows Server Active Directory. Åtkomst till programmen aktiveras genom att utlösa proxyn för att leverera programinnehållet till användaren samtidigt som respektera krav för lokal inloggning.

Om en användare ansluter till din organisation, måste du skapa ett konto för användaren i Azure AD för de primära inloggnings-operationerna. Om den här användaren kräver åtkomst till ett hanterat program, till exempel Salesforce, måste du också skapa ett konto för den här användaren i Salesforce och länkar den till Azure-konto och kontrollera SSO fungerar. När användaren lämnar din organisation, bör du ta bort Azure AD-kontot och alla motsvarande konton i IAM lagrar programmens användaren hade åtkomst till.

## <a name="access-detection"></a>Identifiering av åtkomst
IT-avdelningar är ofta inte medvetna om alla molnprogram som används i moderna företag. Tillsammans med Cloud App Discovery, Azure AD tillhandahåller en lösning för att identifiera dessa program.

## <a name="account-management"></a>Kontohantering
Traditionellt har konton i olika program är att hantera en manuell process som utförs av IT eller supportpersonal i organisationen. Azure AD automatiserar fullständigt kontohantering i program som är integrerade med tjänstleverantörer och program som är förintegrerade av Microsoft som stöder automatisk användaretablering eller SAML just etablering.

## <a name="automated-user-provisioning"></a>Automatisk användaretablering
Vissa program ange automatiseringsgränssnitt för skapandet och borttagningen (eller inaktivering) av konton. Om en leverantör erbjuder sådant gränssnitt, utnyttjas den av Azure AD. Detta minskar dina driftskostnader eftersom administrativa uppgifter sker automatiskt och förbättrar säkerheten för din miljö, eftersom det minskar risken för obehörig åtkomst.

## <a name="access-management"></a>Åtkomsthantering
Du kan hantera åtkomst till program med hjälp av enskilda eller regel driven tilldelningar med Azure AD. Du kan också delegera åtkomsthantering till rätt personer i organisationen att säkerställa bästa tillsyn och minskar belastningen på supportavdelningen.

## <a name="on-premises-applications"></a>Lokala program
Inbyggda application proxy kan du publicera dina lokala program till dina användare, vilket resulterar i åtkomst både konsekvent upplevelse med moderna molnprogram och fördelarna från Azure AD-funktioner för övervakning, rapportering och säkerhet.

## <a name="reporting-and-monitoring"></a>Rapportering och övervakning
Azure AD tillhandahåller förintegrerade rapportering och övervakning av funktioner som gör att du enkelt se vem som har åtkomst till program och när de faktiskt används.

## <a name="related-capabilities"></a>Relaterade funktioner
Du kan skydda dina program med detaljerade åtkomstprinciper och förintegrerade MFA med Azure AD. Läs mer om Azure MFA i [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Komma igång
Kom igång med att integrera program med Azure AD genom att ta en titt på de [integrera Azure Active Directory med program komma igång-guide](plan-an-application-integration.md).

## <a name="see-also"></a>Se också
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Stegvisa distributionsplan för enkel inloggning till ett SaaS-program](http://aka.ms/ssodeploymentplan)

