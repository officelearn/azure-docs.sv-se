---
title: Villkorlig åtkomst införande kit - Azure Active Directory
description: Börja använda Azure AD villkorlig åtkomst för åtkomst till resurser
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
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387578"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Börja använda Azure AD villkorlig åtkomst

Med en mobil- och molnorienterade värld användare åtkomst till organisationens resurser från valfri plats med olika typer av enheter och appar. Därför kan är bara fokusera på vem som kan komma åt en resurs inte längre tillräckligt. Du kan styra vem som har åtkomst till och identifiera där användaren är och vilken enhet som används och mycket mer.

Ange den här kontrollen **villkorlig åtkomst i Azure Active Directory (AD)** kan du ange de villkor som alla användare måste uppfylla för åtkomst till ett program, till exempel Multi-Factor Authentication (MFA). Med hjälp av principer för villkorlig åtkomst styr hur behöriga användare (användare som har beviljats åtkomst till en molnapp) åtkomst till molnappar vissa villkor. Referera till [vad är villkorlig åtkomst i Azure Active Directory](overview.md#conditional-access-policies) för mer information.

## <a name="key-benefits"></a>Viktiga fördelar

De främsta fördelarna med hjälp av Azure AD villkorlig åtkomst är:

* **Öka produktiviteten:** Principer för villkorlig åtkomst (CA) kan du rikta den punkt då uppmanas användarna att MFA och har åtkomst som är blockerad eller krävs för att använda en betrodd enhet. Du kan till exempel ange principer, till exempel endast att kräva att användarna till MFA i ett program när av företagets nätverk. Minska MFA begäranden ger användare mer effektiva än om de behöver MFA varje gång de loggar in. Dessutom kan du ange principer per användare-basis Azure AD villkorlig åtkomst och skapar även en appspecifik principer.
* **Hantera risker:** Aktivera principer för villkorlig åtkomst ger dig molnskala identitetsskydd, riskbaserad åtkomstkontroll funktioner och support för interna multifaktorautentisering. Koppling villkorlig åtkomst med identity protection kan du definiera när åtkomst till ett program blockeras eller begränsad.
* **-Kompatibilitet och styrning:** Granskning förfrågningar och godkännanden för programmet och förstå övergripande programanvändning är enklare med Azure AD eftersom den stöder interna granskningsloggarna för varje begäran för åtkomst av programmet som utförs. Granskning innehåller beställaren identitet, begärda datum, motivering, godkännandestatus och godkännare identitet. Informationen är också tillgänglig från ett API, vilket gör att import av dessa data till en säkerhetsincident och händelsen övervakning (SIEM) system val.
* **Hantera kostnader:** Flyttar principer för åtkomst till Azure AD minskar beroende av anpassade eller lokala lösningar, till exempel Active Directory Federation Services (ADFS) för villkorlig åtkomst, vilket minskar kostnaden för att köra denna infrastruktur.

## <a name="customer-case-studies"></a>Kundfallstudier

Upptäck hur de flesta organisationer använda Azure AD villkorlig åtkomst för att definiera och implementera automatiserade besluten om åtkomstkontroll till molnappar baserat på villkor. Följande berättelserna visar hur dessa kundernas behov uppfylls.

* [**Wipro** enheter mobil produktivitet med Microsoft cloud security-verktyg för att förbättra arbetet med kunder.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Principer för villkorlig åtkomst i Azure AD har aktiverat företaget att dela dokument, resurser och program med betrodda utanför enheter---som kan använda sina egna autentiseringsuppgifter---samtidigt som de behåller kontrollen över sin egen företagets data.
* [**Accenture** skyddar flytten till molnet med Microsoft Cloud App security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture utvärderar den [Appkontroll för villkorsstyrd åtkomst](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) funktion i Cloud App Security, som använder Azure Active Directory villkorlig åtkomst till gate programåtkomst baserat på vissa villkor. LePenske säger att den här funktionen kan vara användbart för, exempelvis att aktivera skrivskyddad åtkomst när förbud mot nedladdningar.
* [**Aramex** leverans begränsad - globala logistik och transport företaget skapar molnanslutna office med identitets- och åtkomsthanteringslösning](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Säkerställa säker åtkomst var särskilt svårt med Aramexs fjärranslutna anställda. Företaget nu tillämpa villkorlig åtkomst så att dessa fjärranslutna anställda åtkomst till sina SaaS-program från utanför nätverket. Regel för villkorlig åtkomst ska bestämma om du vill använda Multifaktorautentisering, vilket ger endast till personer rätt behörighet.

Läs mer om kunder och partner upplevelser på Azure AD villkorlig åtkomst på - [se vad andra uppnår gör med Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Meddelanden

Azure AD får förbättringar med jämna mellanrum. Om du vill hålla dig uppdaterad med den senaste utvecklingen, se [vad är nytt i Azure Active Directory?](../fundamentals/whats-new.md)

Senaste bloggar efter Tech-Community och Microsoft Identity Division:

* 24 september 2018 [villkorlig åtkomst för Azure Active Directory i Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* Den 21 september 2018 [Azure AD villkorlig åtkomst som anpassade kontroller finns i offentlig förhandsversion](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* Den 21 september 2018 [stöd för Azure AD villkorlig åtkomst för begränsad åtkomst med Microsoft Cloud App Security är nu tillgänglig](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* Den 21 september 2018 [Azure AD villkorlig åtkomst: Managed browser har stöd för iOS/Android-plattformar nu i förhandsversion](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* Den 21 september 2018 [Azure AD villkorlig åtkomst för landskoder finns i offentlig förhandsversion](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* Den 21 september 2018 [Azure AD--användningsvillkor nu tillgänglig](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Utbildningsresurser

Följ länkarna nedan för att få en översikt över hur Azure AD villkorlig åtkomst-funktioner.

* Läs ”[vad är villkorlig åtkomst i Azure Active Directory?](overview.md)”
* Vet ”[vad är villkor i Azure Active Directory villkorlig åtkomst?](conditions.md)”
* Vet ”[vad är platsvillkoret i Azure Active Directory villkorlig åtkomst?](location-condition.md)”
* Vet ”[vad är access styr i Azure Active Directory villkorlig åtkomst?](controls.md)”
* Hitta ”[vad är vad om-verktyget i Azure Active Directory villkorlig åtkomst”?](what-if-tool.md)
* Följ [bästa praxis för villkorlig åtkomst i Azure Active Directory](best-practices.md)

Se även följande länkar för vägledning för att skydda åtkomst till alla tjänster som är integrerade med Azure Active Directory.

* [Vad är skydd vid baslinjen (förhandsversion)?](baseline-protection.md) Skydd vid baslinjen säkerställer att du behöver minst baslinje-säkerhetsnivå aktiverad i din Azure Active Directory-miljö.
* [Identitets- och åtkomstkonfigurationer](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Beskriver hur du konfigurerar säker åtkomst till molntjänster via Enterprise Mobility + Security-produkter genom att implementera en rekommenderad miljön och konfigurationen, inklusive en obligatorisk uppsättning principer för villkorlig åtkomst och relaterade funktioner.
* [Referens för Azure Active Directory villkorsstyrd åtkomst](technical-reference.md). Lär dig:
   * Vilka appar använder villkorlig åtkomst?
   * Vilka tjänster aktiveras med villkorlig åtkomst?
* [Aktivera villkorlig åtkomst i Azure Active Directory för säker åtkomst](https://www.youtube.com/watch?v=eLAYBwjCGoA). Den här videon om du vill veta hur villkorlig åtkomst spelar en roll i andra företags- och Mobility Suite arbetsbelastningar.

### <a name="training-videos"></a>Utbildningsvideor

**Villkorlig åtkomst i Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Enhetsbaserad villkorlig åtkomst**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Aktivera Azure Active Directory för villkorlig åtkomst för säker åtkomst**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Online-kurser

Referera till följande kurser för villkorlig åtkomst och mycket mer på [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Design Identitetshantering i Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * ”Den här kursen vägleder dig genom viktiga element som du behöver veta för att utforma din lösning för Identitetshantering med Azure AD”. Azure AD villkorsstyrd åtkomst beskrivs i ”Using roller och åtkomstkontroll med Azure AD” modulen.

* Pluralsight.com: [Design-autentisering för Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * ”Den här kursen förklarar hur du använder Azure AD för att lösa alla dina cloud autentiseringskrav”. Azure AD villkorsstyrd åtkomst beskrivs i ”krav för olika Autentiseringsscenarier”-modulen.

* Pluralsight.com: [Design-auktorisering för Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * ”Den här kursen lär auktorisering som finns tillgängliga med Azure och Azure AD”. Azure AD villkorsstyrd åtkomst beskrivs i ”auktorisering med Azure Resource Manager och Azure AD”-modulen.

### <a name="books"></a>Böcker

* O'Reilly - [och implementera Azure-lösningar – andra utgåvan.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * ”Kom igång med Azure-tjänster och lär dig hur du implementerar dem i din organisation. Azure AD villkorsstyrd åtkomst beskrivs i kapitlet [distribution och synkronisering av Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)”.

* Wiley- [Mastering Microsoft Azure Infrastructure Services](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * ”Här är allt du behöver för att förstå, utvärdera, distribuera och underhålla miljöer som använder Microsoft Azure”.

## <a name="white-papers"></a>White paper-faktablad

* Publicerad 18 December 2018 [skapa en flexibel hanteringsstrategi för åtkomstkontroll med Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Det här dokumentet innehåller vägledning om strategier för en organisation kan använda för att ge ökad flexibilitet för att minska risken för kontoutelåsning under oförutsedda avbrott.

* Publicerad 18 September 2018 [resurser för att migrera program till Azure Active Directory](../manage-apps/migration-resources.md)
   * Detta White Paper innehåller en lista med resurser som hjälper dig att migrera programåtkomst och autentisering till Azure Active Directory (AD Azure).

* Publicerade 12 juli 2018 [Azure säkerhet och efterlevnad skiss: PaaS-webbprogram som är värd för Storbritannien officiella arbetsbelastningar](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure skisser bestå av vägledning dokument och automation-mallar som distribuerar molnbaserade arkitekturer för att erbjuda lösningar på scenarier där ackreditering eller efterlevnadskrav.

## <a name="guidance-for-it-administrators"></a>Vägledning för IT-administratörer

Logga in på den [Azure-portalen](https://portal.azure.com/) som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst. Referera till [behörigheter för administratör i Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Som IT-administratör kan använda [Azure AD villkorlig åtkomst](overview.md) så att användare måste autentisera med Azure Multi-Factor Authentication, logga in från ett betrott nätverk eller en betrodd enhet.

Här är användbara länkar som hjälper dig att komma igång:

* [Metodtips för villkorlig åtkomst i Azure Active Directory](best-practices.md)
* [Använd Azure AD-åtkomstgranskningar att hantera användare som har undantagits från principer för villkorlig åtkomst](../governance/conditional-access-exclusion.md)
* [Anvisningar: Planera distributionen av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md)
* [Snabbstart: Kräva MFA för specifika appar med Azure Active Directory villkorsstyrd åtkomst](app-based-mfa.md)
* [Snabbstart: Kräv användningsvillkor godkännas före åtkomst till molnappar](require-tou.md)
* [Snabbstart: Blockera åtkomst när en session risk identifieras med Azure Active Directory villkorsstyrd åtkomst](app-sign-in-risk.md)
* [Azure AD villkorlig åtkomst till vanliga frågor och svar](faqs.md)
   * Fler frågor du kan också visa den [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Om du inte hittar svaret på ett problem, är vårt supportteam alltid tillgängliga för att hjälpa dig ytterligare. Använd [kontakta Microsofts support](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Självstudier

* [**Snabbstart: Kräva MFA för specifika appar med Azure Active Directory villkorsstyrd åtkomst**](app-based-mfa.md)
   * Den här snabbstarten visar hur du konfigurerar en Azure AD villkorlig åtkomstprincip som kräver multifaktorautentisering för ett valt moln-app i din miljö.

* [**Snabbstart: Kräv användningsvillkor godkännas före åtkomst till molnappar**](require-tou.md)
   * Den här snabbstarten visar hur du konfigurerar en Azure AD villkorlig åtkomstprincip som kräver en användningsvillkor godkännas för ett valt moln-app i din miljö.

* [**Snabbstart: Blockera åtkomst när en session risk identifieras med Azure Active Directory villkorsstyrd åtkomst**](app-sign-in-risk.md)
   * Den här snabbstarten visar hur du konfigurerar principer för villkorlig åtkomst som blockerar en inloggning när en konfigurerade inloggningsrisk nivå har identifierats.

* [Självstudie: **Migrera en klassisk princip som kräver Multi-Factor authentication i Azure portal**](policy-migration-mfa.md)
   * Den här självstudiekursen visar hur du migrerar en klassiska princip som kräver multifaktorautentisering (MFA) för en molnapp.

## <a name="end-user-readiness-and-communication"></a>Slutanvändarens beredskap och kommunikation

Villkorlig åtkomst använder andra Azure AD-funktioner som kan påverka slutanvändarens upplevelse. Du kan till exempel använda Azure Multi-Factor authentication för att aktivera stark autentisering för användare. I så fall använder du mallar för slutanvändare av Azure MFA.

## <a name="next-steps"></a>Nästa steg

* Starta din distribution med den [villkorlig åtkomst distributionen planera dokumentation](plan-conditional-access.md).
