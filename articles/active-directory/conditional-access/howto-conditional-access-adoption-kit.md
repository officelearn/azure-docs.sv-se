---
title: Antagande paket för villkorlig åtkomst – Azure Active Directory
description: Anta villkorlig åtkomst för Azure AD för åtkomst till resurser
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c34f59c3e9f679adf8ae410f648cb7de6dba6447
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430053"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Anta villkorlig åtkomst för Azure AD

I en mobil-och första värld, kan användare få åtkomst till organisationens resurser från var som helst med olika typer av enheter och appar. Därför räcker det inte längre att fokusera på vem som har åtkomst till en resurs. Du kan kontrol lera vem som har åtkomst och identifiera var användaren är och vilken enhet som används och mycket mer.

För att tillhandahålla den här kontrollen kan du använda **villkorlig åtkomst för Azure Active Directory (AD)** för att ange de villkor som användaren måste uppfylla för åtkomst till ett program, till exempel Multi-Factor Authentication (MFA). Genom att använda principer för villkorlig åtkomst styr vi hur auktoriserade användare (användare som har beviljats åtkomst till en molnbaserad app) får åtkomst till molnappar under vissa förhållanden. Mer information finns [i vad är villkorlig åtkomst i Azure Active Directory](overview.md) .

## <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med att använda villkorlig åtkomst i Azure AD är:

* **Öka produktiviteten:** Med principer för villkorlig åtkomst (CA) kan du rikta in dig på den punkt där användarna uppmanas att använda MFA, ha åtkomst blockerad eller som krävs för att använda en betrodd enhet. Du kan till exempel ange principer, till exempel bara kräva att användare använder MFA i ett program när de inte är i företags nätverket. Genom att minska MFA-begärandena blir användarna mer produktiva än om de måste MFA varje gången de loggar in. Dessutom kan du med villkorlig åtkomst i Azure AD ange principer per användare och även skapa appar specifika principer.
* **Hantera risk:** Genom att aktivera principer för villkorlig åtkomst får du till gång till identitets skydd i moln skala, riskfyllda funktioner för åtkomst kontroll och inbyggd Multi-Factor Authentication-stöd. Genom koppling villkorlig åtkomst med identitets skydd kan du definiera när åtkomst till ett program blockeras eller översätts.
* **Hantera efterlevnad och styrning:** Granskning av åtkomst begär Anden och godkännanden för programmet, samt förståelse av övergripande program användning är enklare med Azure AD eftersom det stöder interna gransknings loggar för varje program åtkomst förfrågan som utförs. Granskning inkluderar beställarens identitet, begärt datum, affärs justering, godkännande status och god kännare identitet. Dessa data är också tillgängliga från ett API, vilket gör det möjligt att importera dessa data till ett SIEM-system (säkerhets tillbud och event Monitoring).
* **Hantera kostnad:** Att flytta åtkomst principer till Azure AD minskar beroendet av anpassade eller lokala lösningar som Active Directory Federation Services (AD FS) (ADFS) för villkorlig åtkomst, vilket minskar kostnaden för att köra infrastrukturen.

## <a name="customer-case-studies"></a>Kundfallstudier

Upptäck hur de flesta organisationer använder villkorlig åtkomst i Azure AD för att definiera och implementera automatiska åtkomst kontroll beslut för att komma åt molnappar baserat på villkor. Följande artiklar visar hur dessa kunder behöver uppfyllas.

* [**Wipro** driver mobil produktivitet med Microsofts moln säkerhets verktyg för att förbättra kund engagemang.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Principer för villkorlig åtkomst i Azure AD har aktiverat företaget för att dela dokument, resurser och program med betrodda utanför entiteter---som kan använda sina egna autentiseringsuppgifter---samtidigt som de behåller kontrollen över sina egna företags data.
* [**Accenture** skyddar sin flytt till molnet med Microsoft Cloud App Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture utvärderar funktionen [appkontroll för villkorsstyrd åtkomst](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) i Cloud App Security, som använder Azure Active Directory villkorlig åtkomst till grind program åtkomst baserat på vissa villkor. LePenske anger att den här funktionen kan vara användbar för, t. ex. Aktivera skrivskyddad fil åtkomst medan hämtningar förhindras.
* [ **Aramex** Delivery Limited-global logistik och transport företag skapar ett moln anslutet kontor med identitets-och åtkomst hanterings lösning](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Att säkerställa säker åtkomst var särskilt svårt med Aramex fjär anställda. Företaget använder nu villkorlig åtkomst för att ge dessa anställda till gång till sina SaaS-program utanför nätverket. Regeln för villkorlig åtkomst avgör om du vill genomdriva Multi-Factor Authentication, vilket ger endast rätt personer rätt åtkomst.

Mer information om kund-och partner upplevelser för villkorlig åtkomst i Azure AD finns i [de fantastiska saker som människor gör med Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Meddelanden

Azure AD tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen, se [Vad är nytt i Azure Active Directory?](../fundamentals/whats-new.md)

De senaste bloggarna från teknisk community och Microsoft Identity Division:

* 24 september 2018 [Azure Active Directory villkorlig åtkomst i Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 september 2018, [anpassade kontroller för villkorlig åtkomst i Azure AD finns i offentlig för hands version](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 september 2018 [är Azure AD-stöd för villkorlig åtkomst för begränsad åtkomst med Microsoft Cloud App Security nu tillgängligt](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 september 2018, [Azure AD villkorlig åtkomst: Managed Browser-stöd för iOS/Android-plattformar finns nu i för hands version](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 september 2018 [är Azure AD villkorlig åtkomst för lands koder i offentlig för hands version](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 september 2018 är användnings [villkoren för Azure AD nu tillgängliga](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Utbildnings resurser

Följ länkarna nedan för att få en översikt över hur funktioner för villkorlig åtkomst i Azure AD fungerar.

* Lär dig "[Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)"
* Vet du[Vad är villkor i Azure Active Directory villkorlig åtkomst?](conditions.md)"
* Vet[du "Vad är plats villkoret i Azure Active Directory villkorlig åtkomst?](location-condition.md)"
* Vet du[Vad är åtkomst kontroller i Azure Active Directory villkorlig åtkomst?](controls.md)
* [Vad händer om-verktyget finns i Azure Active Directory villkorlig åtkomst?](what-if-tool.md)
* Följ [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md)

Se även följande länkar för vägledning för att skydda åtkomsten till alla tjänster som är integrerade med Azure Active Directory.

* [Konfigurationer för identitets-och enhets åtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Beskriver hur du konfigurerar säker åtkomst till moln tjänster genom Enterprise Mobility + Security produkter genom att implementera en rekommenderad miljö och konfiguration, inklusive en före skriven uppsättning principer för villkorlig åtkomst och relaterade funktioner.
* [Referens för Azure Active Directory villkorlig åtkomst inställningar](technical-reference.md). Mer
   * Vilka appar använder villkorlig åtkomst?
   * Vilka tjänster är aktiverade med villkorlig åtkomst?
* [Aktivera Azure Active Directory villkorlig åtkomst för säker användar åtkomst](https://www.youtube.com/watch?v=eLAYBwjCGoA). Titta på den här videon och lär dig hur villkorlig åtkomst spelar en roll i andra företags-och Mobility Suite-arbetsbelastningar.

### <a name="training-videos"></a>Utbildningsvideor

**Villkorlig åtkomst i Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Enhets-baserad villkorlig åtkomst**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Aktivera Azure Active Directory för villkorlig åtkomst för säker användar åtkomst**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Onlinekurser

Se följande kurser för villkorlig åtkomst med mera på [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [utforma identitets hantering i Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Den här kursen vägleder dig genom de viktigaste objekten du behöver känna till för att utforma din lösning för identitets hantering med Azure AD." Villkorlig åtkomst för Azure AD beskrivs i modulen "using roles and Access Control with Azure AD".

* Pluralsight.com: [utforma autentisering för Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Den här kursen förklarar hur du använder Azure AD för att lösa alla dina krav på moln autentisering." Villkorlig åtkomst för Azure AD beskrivs i modulen "autentiseringskrav för olika scenarier".

* Pluralsight.com: [design-auktorisering för Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "I den här kursen lär du dig tillgängliga alternativ för Azure och Azure AD." Villkorlig åtkomst för Azure AD beskrivs i modulen auktorisering med Azure Resource Manager och Azure AD.

### <a name="books"></a>Böcker

* O ' Reilly – [implementera Azure-lösningar – andra utgåvan.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Kom igång med Azure-tjänster och lär dig hur du implementerar dem i din organisation. Villkorlig åtkomst för Azure AD beskrivs i kapitlet [distribuera och synkronisera Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [mastering Microsoft Azure infrastruktur tjänster](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Här är allt du behöver för att förstå, utvärdera, distribuera och underhålla miljöer som använder Microsoft Azure."

## <a name="white-papers"></a>Whitepapers

* Publicerad den 18 december 2018, [skapa en elastisk åtkomst kontroll hanterings strategi med Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Det här dokumentet ger vägledning om strategier som en organisation kan anta för att ge återhämtning för att minska risken för utelåsning under oförutsedda avbrott.

* Publicerad 18 september 2018, [resurser för att migrera program till Azure Active Directory](../manage-apps/migration-resources.md)
   * Detta whitepaper innehåller en lista över resurser som hjälper dig att migrera program åtkomst och autentisering till Azure Active Directory (Azure AD).

* Publicerad 12 juli 2018 [handlingsplan för säkerhet och efterlevnad i Azure: PaaS webb program värd för offentliga Storbritannien-arbetsbelastningar](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure-ritningar består av väglednings dokument och automatiserings mallar som distribuerar molnbaserade arkitekturer för att erbjuda lösningar till scenarier som har ackrediterings-eller efterlevnads krav.

## <a name="guidance-for-it-administrators"></a>Vägledning för IT-administratörer

Logga in på [Azure Portal](https://portal.azure.com/) som global administratör, säkerhets administratör eller villkorlig åtkomst administratör. Se [Administratörs roll behörigheter i Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Som IT-administratör ska du använda [villkorlig åtkomst för Azure AD](overview.md) för att kräva att användarna autentiserar med Azure Multi-Factor Authentication, loggar in från ett betrott nätverk eller en betrodd enhet.

Här är användbara länkar som hjälper dig att komma igång:

* [Metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md)
* [Använd åtkomst granskningar i Azure AD för att hantera användare som har uteslutits från principer för villkorlig åtkomst](../governance/conditional-access-exclusion.md)
* [Gör så här: planera din distribution av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md)
* [Snabb start: Kräv MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md)
* [Snabb start: Kräv användnings villkor för att godkännas innan du får åtkomst till molnappar](require-tou.md)
* [Snabb start: blockera åtkomst när en sessionsgräns identifieras med Azure Active Directory villkorlig åtkomst](app-sign-in-risk.md)
* [Vanliga frågor och svar om villkorlig åtkomst i Azure AD](faqs.md)
   * Du kan också visa [MSDN-forumet](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)för ytterligare frågor.
   * Om du inte hittar svaret på ett problem är våra support team alltid tillgängliga för att hjälpa dig. Använd [kontakta Microsoft-supporten](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Självstudiekurser

* [**Snabb start: Kräv MFA för vissa appar med Azure Active Directory villkorlig åtkomst**](app-based-mfa.md)
   * Den här snabb starten visar hur du konfigurerar en princip för villkorlig åtkomst i Azure AD som kräver Multi-Factor Authentication för en vald molnbaserad app i din miljö.

* [**Snabb start: Kräv användnings villkor för att godkännas innan du får åtkomst till molnappar**](require-tou.md)
   * Den här snabb starten visar hur du konfigurerar en princip för villkorlig åtkomst i Azure AD som kräver att en ToU godkänns för en vald molnbaserad app i din miljö.

* [**Snabb start: blockera åtkomst när en sessionsgräns identifieras med Azure Active Directory villkorlig åtkomst**](app-sign-in-risk.md)
   * Den här snabb starten visar hur du konfigurerar en princip för villkorlig åtkomst som blockerar en inloggning när en konfigurerad risk nivå för inloggning har upptäckts.

* [Självstudie: **Migrera en klassisk princip som kräver Multi-Factor Authentication i Azure Portal**](policy-migration-mfa.md)
   * Den här självstudien visar hur du migrerar en klassisk princip som kräver Multi-Factor Authentication (MFA) för en molnbaserad app.

## <a name="end-user-readiness-and-communication"></a>Slut användar beredskap och kommunikation

Villkorlig åtkomst använder andra Azure AD-funktioner som kan påverka slut användar upplevelsen. Du kan till exempel använda Azure Multi-Factor Authentication för att aktivera stark autentisering för användare. I så fall kommer du att använda mallarna för slutanvändare i Azure MFA.

## <a name="next-steps"></a>Nästa steg

* Starta distributionen med dokumentationen för [distributions planering för villkorlig åtkomst](plan-conditional-access.md).
