---
title: Hantera program med Azure Active Directory | Microsoft Docs
description: "Den här artikeln fördelarna med att integrera Azure Active Directory med din lokala, moln och SaaS-program."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 95b96f10-2d5c-4b78-8af8-d3657a24140f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: f4035e4c1ef2bc78256ed842bc4b1ac65ecf8b53
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="managing-applications-with-azure-active-directory"></a>Hantera program med Azure Active Directory
Utöver det faktiska arbetsflödet eller innehåll har företag två grundläggande krav för alla program:

1. Om du vill öka produktiviteten ska program vara enkelt att identifiera och komma åt
2. Om du vill aktivera säkerhet och styrning behöver organisationen kontroll och tillsyn på vem som kan och faktiskt har åtkomst till varje program

Inom molnprogram detta uppnås bäst identiteten för att styra ”*som är tillåten att göra vad*”.

Inom datorsammanhang terminologi:

* *Som* kallas *identitet* -hantering av användare och grupper
* *Vad* kallas *åtkomsthantering* – hantering av åtkomst till skyddade resurser

Båda komponenterna tillsammans kallas *identitet och åtkomst Management (IAM)*, som definieras av den [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) som ”*undantas säkerhet som kan rätt personer åtkomst till rätt resurser i högra gånger på grund av rätt*”.

Okej vad är problemet? Om IAM *inte hanteras* på ett ställe med en integrerad lösning:

* Identity-administratörer har individuellt skapa och uppdatera användarkonton i alla program separat, en redundant och tidskrävande aktivitet.
* Användarna behöver komma ihåg flera autentiseringsuppgifter för att komma åt de program som de behöver för att arbeta med. Därför tenderar användare att skriva ned sina lösenord eller använda andra lösningar för hantering av lösenord som innehåller andra data säkerhetsrisker.
* Redundant tidskrävande aktiviteter minska mängden tid användare och administratörer som arbetar på affärsverksamhet som ökar din verksamhet slutresultatet.

Så vad förhindrar normalt organisationer från att anta integrerad IAM-lösningar?

* De flesta tekniska lösningar baseras på programvaruplattformar som måste distribueras och av varje organisation för sina egna program.
* Molnprogram antas ofta till en högre nivå än IT-organisation kan integrera med befintliga IAM.
* Säkerhet och övervaka verktygsuppsättning kräver ytterligare anpassning och integrering för att uppnå omfattande E2E-scenarier.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory-integrerad med program
Azure Active Directory är Microsofts omfattande identitet som en tjänst (IDaaS) som:

* Aktiverar IAM som en tjänst i molnet 
* Tillhandahåller centrala hantering, enkel inloggning (SSO) och rapportering 
* Stöder integrerad åtkomsthantering för [tusentals program](https://azure.microsoft.com/marketplace/active-directory/) i programgalleriet inklusive Salesforce, Google Apps, rutan, Concur och mycket mer. 

Med Azure Active Directory, alla program som du publicerar för partner och kunder (business eller konsumenten) har samma identitet och åtkomst hanteringsfunktioner.<br> Detta gör att du kan avsevärt minska din driftskostnader.

Vad händer om du behöver implementera ett program som ännu inte finns i programgalleriet? Här är lite mer tid än att konfigurera enkel inloggning för program från galleriet programmet ger Azure AD dig en guide som hjälper dig med konfigurationen.

Värdet för Azure AD är mer omfattande än ”bara” molnprogram. Du kan också använda den med lokala program genom att tillhandahålla säker fjärråtkomst. Med säker fjärråtkomst, kan du bli kvitt på behovet av VPN-anslutningar eller andra traditionella fjärråtkomst management-implementeringar.

Azure AD innehåller lösningen på problem med de viktigaste data säkerhet och produktivitet genom att tillhandahålla central hantering och enkel inloggning (SSO) för alla program.

* Användare kan komma åt flera program med en inloggning ger mer tid till intäkter genererar eller affärsenhet operations aktiviteter.
* Identity-administratörer kan hantera åtkomst till program på en plats.

Fördelen för användaren och för ditt företag är uppenbara. Låt oss ta en närmare titt på fördelarna för identitetsadministratör och organisationen.

## <a name="integrated-application-benefits"></a>Fördelar med integrerat program
SSO-processen har två steg:

* Autentisering, hur du verifierar användarens identitet.
* Auktorisering, beslutet att aktivera eller blockera åtkomst till en resurs med en åtkomstprincip.

När du använder Azure AD för att hantera program och aktivera enkel inloggning:

* Autentisering görs på användarens lokala (t.ex. AD) eller Azure AD-kontot.
* Auktorisering körs på Azure AD tilldelning och skydd principen för att säkerställa konsekvent slutanvändarupplevelse och gör att du kan lägga till tilldelning, platser och MFA villkor på alla program, oavsett dess interna funktioner.

Det är viktigt att förstå som hur tillståndet trätt i kraft på målprogrammet varierar beroende på hur programmet har integrerat med Azure AD.

* **Redan integrerade program av tjänstleverantören** som Office 365 och Azure, de är program som skapats direkt på Azure AD och förlita dig på den för sina omfattande identitets- och hanteringsfunktioner. Åtkomst till dessa program aktiveras via kataloginformation och utfärdande.
* **Redan integrerade program från Microsoft och anpassade program** dessa är oberoende molnprogram som förlitar sig på ett internt programkatalogen och kan användas oberoende av Azure AD. Åtkomst till dessa program har aktiverats genom att utfärda ett program autentiseringsuppgifter som mappas till ett konto för programmet. Beroende på programfunktioner kanske autentiseringsuppgifter en federation-token eller användarnamnet och lösenordet för ett konto som tidigare har etablerats i programmet.
* **Lokala program** program som publicerats via Azure AD application proxy främst att aktivera åtkomst till lokala program. Dessa program är beroende av en central lokal katalog som Windows Server Active Directory. Åtkomst till dessa program har aktiverats genom att utlösa proxyn att leverera programinnehållet till användaren vid respektera lokal inloggning krav.

Om en användare ansluter till din organisation, måste du skapa ett konto för användaren i Azure AD för de primära inloggning-operationerna. Om användaren behöver åtkomst till ett hanterat program, till exempel Salesforce, måste du också skapa ett konto för den här användaren i Salesforce och länka det till Azure-konto så att SSO fungerar. När användaren lämnar företaget bör du ta bort Azure AD-kontot och alla motsvarighet konton i IAM lagrar program som användaren har åtkomst till.

## <a name="access-detection"></a>Åtkomst-identifiering
IT-avdelningar är ofta inte medvetna om alla molnprogram som används i moderna företag. Tillsammans med Cloud App Discovery ger Azure AD dig en lösning för att identifiera dessa program.

## <a name="account-management"></a>Kontohantering
Hantera konton i olika program traditionellt är en manuell process som utförs av IT eller supportpersonalen i organisationen. Azure AD automatiserar fullständigt kontohantering över alla service provider integrerade program och de program som redan integrerade Microsoft stöder automatisk användaretablering eller SAML kompilerats etablering.

## <a name="automated-user-provisioning"></a>Automatisk användaretablering
Vissa program ange automation-gränssnitt för att skapa och ta bort (eller avaktivering) av konton. Om en provider erbjuder sådant gränssnitt kan utnyttjas den av Azure AD. Detta din driftskostnaderna minskar eftersom administrativa uppgifter sker automatiskt och förbättrar säkerheten för din miljö, eftersom det minskar risken för obehörig åtkomst.

## <a name="access-management"></a>Åtkomsthantering
Du kan hantera åtkomst till program med hjälp av enskilda eller regel drivs tilldelningar med Azure AD. Du kan också delegera åtkomsthantering till rätt personer i organisationen säkerställa bästa tillsyn och minska belastningen på supportavdelningen.

## <a name="on-premises-applications"></a>Lokala program
Den inbyggda i application proxy kan du publicera dina lokala program till användarna som resulterar i åtkomst både konsekvent upplevelse med moderna molnapp och fördelarna från funktionerna i Azure AD övervakning, rapportering och säkerhet.

## <a name="reporting-and-monitoring"></a>Övervakning och rapportering
Azure AD innehåller förintegrerade rapportering och övervakning av funktioner som gör att du vet vem som har tillgång till program och de faktiskt används.

## <a name="related-capabilities"></a>Relaterade funktioner
Du kan skydda dina program med detaljerade åtkomstprinciper och förintegrerade MFA med Azure AD. Läs mer om Azure MFA finns [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Komma igång
Om du vill komma igång med att integrera program med Azure AD kan ta en titt på den [integrera Azure Active Directory med program komma igång](active-directory-integrating-applications-getting-started.md).

## <a name="see-also"></a>Se också
[Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)

