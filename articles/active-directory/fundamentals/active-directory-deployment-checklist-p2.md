---
title: Checklista för Azure AD-distribution
description: Checklista för distribution av Azure Active Directory-funktioner
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: f84226a631014b51338d47887fe3bafc969dc571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063653"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Distributionsguide för Azure Active Directory-funktioner

Det kan verka skrämmande att distribuera Azure Active Directory (Azure AD) för din organisation och skydda den. Den här artikeln identifierar vanliga uppgifter som kunderna tycker är användbara för att slutföra i faser, under loppet av 30, 60, 90 dagar eller mer, för att förbättra sin säkerhetsposition. Även organisationer som redan har distribuerat Azure AD kan använda den här guiden för att säkerställa att de får ut det mesta av sin investering.

En välplanerad och utförd identitetsinfrastruktur banar väg för säker åtkomst till dina produktivitetsarbetsbelastningar och data endast för kända användare och enheter.

Dessutom kan kunderna kontrollera sin [identitetssäkra poäng](identity-secure-score.md) för att se hur anpassade de är till Microsofts metodtips. Kontrollera din säkra poäng före och efter genomförandet av dessa rekommendationer för att se hur bra du gör jämfört med andra i din bransch och till andra organisationer av din storlek.

## <a name="prerequisites"></a>Krav

Många av rekommendationerna i den här guiden kan implementeras med Azure AD Free eller ingen licens alls. Där licenser krävs anger vi vilken licens som krävs som minimum för att utföra uppgiften.

Ytterligare information om licensiering finns på följande sidor:

* [Azure AD-licensiering](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Företag](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Licensvägledning för Azure AD B2B](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fas 1: Skapa en grund för säkerhet

I den här fasen aktiverar administratörer grundläggande säkerhetsfunktioner för att skapa en säkrare och enklare att använda foundation i Azure AD innan vi importerar eller skapar normala användarkonton. Den här grundläggande fasen säkerställer att du är i ett säkrare tillstånd från början och att dina slutanvändare bara behöver introduceras till nya koncept en gång.

| Aktivitet | Information | Obligatorisk licens |
| ---- | ------ | ---------------- |
| [Utse mer än en global administratör](../users-groups-roles/directory-emergency-access.md) | Tilldela minst två permanenta globala administratörskonton endast för molnet för användning om det finns en nödsituation. Dessa konton används inte dagligen och bör ha långa och komplexa lösenord. | Azure AD Kostnadsfri |
| [Använd icke-globala administrativa roller där det är möjligt](../users-groups-roles/directory-assign-admin-roles.md) | Ge administratörerna bara den åtkomst de behöver till endast de områden de behöver åtkomst till. Alla administratörer behöver inte vara globala administratörer. | Azure AD Kostnadsfri |
| [Aktivera privilegierad identitetshantering för att spåra administratörsrollanvändning](../privileged-identity-management/pim-getting-started.md) | Aktivera Privilegierad identitetshantering för att börja spåra användning av administrativ roll. | Azure AD Premium P2 |
| [Lansera självåterställning av lösenord](../authentication/howto-sspr-deployment.md) | Minska helpdesk-anrop på lösenordsåterställning genom att låta personalen återställa sina egna lösenord med hjälp av principer som du som administratörskontroll. | |
| [Skapa en organisationsspecifik lista över anpassade förbjudna lösenord](../authentication/howto-password-ban-bad-configure.md) | Hindra användare från att skapa lösenord som innehåller vanliga ord eller fraser från din organisation eller ditt område. | |
| [Aktivera lokal integrering med Azure AD-lösenordsskydd](../authentication/concept-password-ban-bad-on-premises.md) | Utöka listan över förbjudna lösenord till din lokala katalog för att säkerställa att lösenord som anges lokalt också överensstämmer med de globala och klientspecifika förbjudna lösenordslistorna. | Azure AD Premium P1 |
| [Aktivera Microsofts lösenordsvägledning](https://www.microsoft.com/research/publication/password-guidance/) | Sluta kräva att användarna ändrar sitt lösenord enligt ett fast schema, inaktiverar komplexitetskrav och användarna är mer benägna att komma ihåg sina lösenord och behålla något som är säkert. | Azure AD Kostnadsfri |
| [Inaktivera periodiska återställningar av lösenord för molnbaserade användarkonton](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Periodiska återställningar av lösenord uppmuntrar användarna att öka sina befintliga lösenord. Använd riktlinjerna i Microsofts dokument för lösenordsvägledning och spegla din lokala princip för användare som endast är molnet. | Azure AD Kostnadsfri |
| [Anpassa smart utelåsning i Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Stoppa utelåsningar från molnbaserade användare från att replikeras till lokala Active Directory-användare | |
| [Aktivera Smart utelåsning med extranät för AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranet lockout skyddar mot brute force lösenord gissa attacker, samtidigt som giltiga AD FS-användare fortsätter att använda sina konton. | |
| [Distribuera Azure AD MultiFaktor-autentisering med principer för villkorlig åtkomst](../authentication/howto-mfa-getstarted.md) | Kräv att användare utför tvåstegsverifiering när de kommer åt känsliga program med principer för villkorlig åtkomst. | Azure AD Premium P1 |
| [Aktivera Azure Active Directory-identitetsskydd](../identity-protection/overview-identity-protection.md) | Aktivera spårning av riskfyllda inloggningar och komprometterade autentiseringsuppgifter för användare i organisationen. | Azure AD Premium P2 |
| [Använd riskidentifieringar för att utlösa multifaktorautentisering och lösenordsändringar](../authentication/tutorial-risk-based-sspr-mfa.md) | Aktivera automatisering som kan utlösa händelser som multifaktorautentisering, återställning av lösenord och blockering av inloggningar baserat på risk. | Azure AD Premium P2 |
| [Aktivera konvergerad registrering för återställning av lösenord för självbetjäning och Azure AD Multi-Factor Authentication (förhandsversion)](../authentication/concept-registration-mfa-sspr-converged.md) | Tillåt användarna att registrera sig från en gemensam upplevelse för både Azure Multi-Factor Authentication och self-service password reset. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fas 2: Importera användare, aktivera synkronisering och hantera enheter

Därefter lägger vi till grunden som läggs i fas 1 genom att importera våra användare och aktivera synkronisering, planera för gäståtkomst och förbereda för att stödja ytterligare funktioner.

| Aktivitet | Information | Obligatorisk licens |
| ---- | ------ | ---------------- |
| [Installera Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Förbered att synkronisera användare från din befintliga lokala katalog till molnet. | Azure AD Kostnadsfri |
| [Implementera synkronisering av lösenord hash](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Synkronisera lösenordsh hashar för att tillåta att lösenordsändringar replikeras, felaktig identifiering av lösenord och reparation och läcktautentiseringsrapportering. | Azure AD Kostnadsfri |
| [Implementera tillbakaskrivning av lösenord](../authentication/howto-sspr-writeback.md) | Tillåt att lösenordsändringar i molnet skrivs tillbaka till en lokal Active Directory-miljö i Windows Server. | Azure AD Premium P1 |
| [Implementera Azure AD Connect-hälsotillstånd](../connect-health/active-directory-aadconnect-health.md) | Aktivera övervakning av viktig hälsostatistik för dina Azure AD Connect-servrar, AD FS-servrar och domänkontrollanter. | Azure AD Premium P1 |
| [Tilldela licenser till användare efter gruppmedlemskap i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Spara tid och arbete genom att skapa licensieringsgrupper som aktiverar eller inaktiverar funktioner efter grupp i stället för att ange per användare. | |
| [Skapa en plan för gästanvändaråtkomst](../b2b/what-is-b2b.md) | Samarbeta med gästanvändare genom att låta dem logga in på dina appar och tjänster med sina egna arbets-, skol- eller sociala identiteter. | [Licensvägledning för Azure AD B2B](../b2b/licensing-guidance.md) |
| [Bestäm enhetshanteringsstrategi](../devices/overview.md) | Bestäm vad din organisation tillåter när det gäller enheter. Registrera vs gå, Ta med din egen enhet vs företag som tillhandahålls. | |
| [Distribuera Windows Hello för företag i din organisation](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Förbered för lösenordslös autentisering med Windows Hello | |
| [Distribuera lösenordslösa autentiseringsmetoder för användarna](../authentication/concept-authentication-passwordless.md) | Förse användarna med praktiska lösenordslösa autentiseringsmetoder | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>Fas 3: Hantera program

När vi fortsätter att bygga vidare på tidigare faser identifierar vi kandidatprogram för migrering och integration med Azure AD och slutför installationen av dessa program.

| Aktivitet | Information | Obligatorisk licens |
| ---- | ------ | ---------------- |
| Identifiera dina program | Identifiera program som används i din organisation: lokala, SaaS-program i molnet och andra affärsprogram. Ta reda på om dessa program kan och bör hanteras med Azure AD. | Ingen licens krävs |
| [Integrera SaaS-program som stöds i galleriet](../manage-apps/add-application-portal.md) | Azure AD har ett galleri som innehåller tusentals förintegrerade program. Vissa av de program som din organisation använder finns förmodligen i galleriet som är tillgängliga direkt från Azure-portalen. | Azure AD Kostnadsfri |
| [Använda Programproxy för att integrera lokala program](../manage-apps/application-proxy-add-on-premises-application.md) | Programproxy gör det möjligt för användare att komma åt lokala program genom att logga in med sitt Azure AD-konto. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Fas 4: Granska privilegierade identiteter, slutför en åtkomstgranskning och hantera användarnas livscykel

I fas 4 kan administratörer tillämpa lägsta behörighetsprinciper för administration, slutföra sina första åtkomstgranskningar och möjliggöra automatisering av vanliga användarlivscykeluppgifter.

| Aktivitet | Information | Obligatorisk licens |
| ---- | ------ | ---------------- |
| [Framtvinga användningen av privilegierad identitetshantering](../privileged-identity-management/pim-security-wizard.md) | Ta bort administrativa roller från vanliga dagliga användarkonton. Gör administrativa användare berättigade att använda sin roll efter att ha lyckats med en multifaktorautentiseringskontroll, tillhandahålla en affärsmotivering eller begära godkännande från utsedda godkännare. | Azure AD Premium P2 |
| [Slutför en åtkomstgranskning för Azure AD-katalogroller i PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Arbeta med dina säkerhets- och ledningsgrupper för att skapa en åtkomstgranskningsprincip för att granska administrativ åtkomst baserat på organisationens principer. | Azure AD Premium P2 |
| [Implementera principer för dynamisk gruppmedlemskap](../users-groups-roles/groups-dynamic-membership.md) | Använd dynamiska grupper för att automatiskt tilldela användare till grupper baserat på deras attribut från HR (eller din sanningskälla), till exempel avdelning, titel, region och andra attribut. |  |
| [Implementera gruppbaserad programetablering](../manage-apps/what-is-access-management.md) | Använd gruppbaserad åtkomsthanteringsetablering för att automatiskt etablera användare för SaaS-program. |  |
| [Automatisera etablering och avetablering av användare](../app-provisioning/user-provisioning.md) | Ta bort manuella steg från ditt anställdas kontolivscykel för att förhindra obehörig åtkomst. Synkronisera identiteter från din sanningskälla (HR-system) till Azure AD. |  |

## <a name="next-steps"></a>Nästa steg

[Azure AD-licensierings- och prisinformation](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurationer för identitets- och enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Vanliga principer för rekommenderad identitet och enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
