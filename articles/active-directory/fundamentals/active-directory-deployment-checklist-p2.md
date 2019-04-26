---
title: Checklista för distribution till Azure AD
description: Azure Active Directory checklista för distribution av funktionen
services: active-directory
ms.service: active-directory
ms.subservice: ''
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9ee0d6fab96c84eee8a520d01d97faddab49f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249716"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Distributionsguide för Azure Active Directory-funktionen

Det kan verka skrämmande att distribuera Azure Active Directory (Azure AD) för din organisation och skydda den. Den här artikeln beskrivs vanliga uppgifter att kunder vara till hjälp för att slutföra i faser, under loppet av 30, 60, 90 dagar eller mer att förbättra deras säkerhet efterlevnadsstatus. Även organisationer som redan har distribuerat Azure AD kan använda den här guiden för att se till att de får ut maximalt av sina investeringar.

Ett framgångsrikt och utförs identitetsinfrastrukturen banar vägen för säker åtkomst till din produktivitet arbetsbelastningar och data av kända användare och enheter endast.

Dessutom kunder kan kontrollera sina [identitet skyddar score](identity-secure-score.md) att se hur justerade de är bästa praxis från Microsoft. Kontrollera dina säker poäng före och efter tillämpning av de här rekommendationerna för att se hur väl du åstadkommer jämfört med andra i din bransch och andra organisationer av storleken på dina.

## <a name="prerequisites"></a>Nödvändiga komponenter

Många av rekommendationerna i den här guiden kan implementeras med Azure AD kostnadsfri, Basic eller ingen licens alls. Där licenser krävs ange vi vilken licens krävs minst att utföra uppgiften.

Mer information om licensiering finns på följande sidor:

* [Azure AD-licensiering](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Azure AD B2B-licensiering vägledning](../b2b/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Fas 1: Skapa en grund för säkerhet

I den här fasen kan aktivera administratörer grundläggande säkerhetsfunktioner att skapa en säkrare och mer lättanvända grund i Azure AD innan vi importera eller skapa vanliga användarkonton. Den här grundläggande fasen säkerställer att du är i tillståndet säkrare från början och att slutanvändarna bara behöver introduceras nya en gång.

| Aktivitet | Information | Licens som krävs |
| ---- | ------ | ---------------- |
| [Ange mer än en global administratör](../users-groups-roles/directory-emergency-access.md) | Tilldela minst två molnbaserad permanent globala administratörskonton för användning om det finns ett nödfall. Dessa konton är inte användas för varje dag och ska ha långa och komplexa lösenord. | Azure AD Kostnadsfri |
| [Använda icke-globala administrativa roller, där det är möjligt](../users-groups-roles/directory-assign-admin-roles.md) | Ge dina administratörer endast åtkomst till de områden som de behöver åtkomst till. Inte alla administratörer måste vara globala administratörer. | Azure AD Kostnadsfri |
| [Aktivera Privileged Identity Management för att spåra användning för admin-rollen](../privileged-identity-management/pim-getting-started.md) | Aktivera Privileged Identity Management att starta spårning av administrativ roll. | Azure AD Premium P2 |
| [Lansera självåterställning av lösenord](../authentication/howto-sspr-deployment.md) | Minska antalet samtal till supportavdelningen för lösenordsåterställning genom att låta personal att återställa sina lösenord med hjälp av principer du som en administratör-kontroll. | Azure AD Basic |
| [Skapa en lista med specifik anpassade förbjudna lösenord organisation](../authentication/howto-password-ban-bad-configure.md) | Förhindra att användarna skapar lösenord som innehåller vanliga ord eller fraser från din organisation eller ditt område. | Azure AD Basic |
| [Aktivera lokal integrering med Azure AD-lösenordsskydd](../authentication/concept-password-ban-bad-on-premises.md) | Utöka lista med förbjudna lösenord till din lokala katalog så lösenord Ange lokala är också kompatibla med den globala och klientspecifik förbjudna lösenordslistor. | Azure AD Premium P1 |
| [Aktivera Microsofts lösenord vägledning](https://www.microsoft.com/research/publication/password-guidance/) | Stoppa att kräva att användare kan ändra sitt lösenord på ett schema, inaktivera krav på komplexitet och dina användare är mer apt att komma ihåg lösenorden och hålla dem något som är säker. | Azure AD Kostnadsfri |
| [Inaktivera periodiska lösenordsåterställning för molnbaserade användarkonton](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Periodiska lösenordsåterställning Uppmana användarna att öka deras befintliga lösenord. Följ sedan riktlinjerna i Microsofts lösenord vägledning dokument och spegling av din lokala princip för endast molnbaserade användare. | Azure AD Kostnadsfri |
| [Anpassa Azure Active Directory smart kontoutelåsning](../authentication/howto-password-smart-lockout.md) | Stoppa utelåsningar från molnbaserade användare replikeras till den lokala Active Directory-användare | Azure AD Basic |
| [Aktivera smarta Extranätsutelåsning för AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS extranätsutelåsning skyddar mot brute force lösenord gissa attacker, samtidigt som giltig AD FS-användarna fortsätta att använda sina konton. | |
| [Distribuera Azure AD Multi-Factor Authentication med hjälp av principer för villkorlig åtkomst](../authentication/howto-mfa-getstarted.md) | Kräv att användare utför en tvåstegsverifiering vid åtkomst till känsliga program med hjälp av principer för villkorlig åtkomst. | Azure AD Premium P1 |
| [Aktivera Azure Active Directory Identity Protection](../identity-protection/enable.md) | Aktivera spårning av riskfyllda inloggningar och komprometterade autentiseringsuppgifter för användare i din organisation. | Azure AD Premium P2 |
| [Använd riskhändelser för att utlösa Multi-Factor authentication och ändring av lösenord](../authentication/tutorial-risk-based-sspr-mfa.md) | Aktivera automatisering som kan utlösa händelser som multifaktorautentisering, lösenordsåterställning och blockerar över inloggningar baserat på risk. | Azure AD Premium P2 |
| [Aktivera konvergerade registrering för lösenordsåterställning via självbetjäning och Azure AD Multi-Factor Authentication (förhandsversion)](../authentication/concept-registration-mfa-sspr-converged.md) | Tillåt användarna att registrera från en gemensam upplevelsen för både Azure Multi-Factor Authentication och lösenordsåterställning via självbetjäning. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Fas 2: Importera användare, aktivera synkronisering och hantera enheter

Nu ska vi lägga till i grunden som i fas 1 genom att importera våra användare och aktivera synkronisering, planera för gäståtkomst och förbereda för ytterligare funktioner.

| Aktivitet | Information | Licens som krävs |
| ---- | ------ | ---------------- |
| [Installera Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md) | Förbered för att synkronisera användare från din befintliga lokala katalog till molnet. | Azure AD Kostnadsfri |
| [Implementera lösenordets Hash-synkronisering](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md) | Synkronisera lösenordshash så att lösenordsändringar som ska replikeras, felaktiga lösenord identifiering och reparation och rapportering av läckta autentiseringsuppgifter. | Azure AD Premium P1 |
| [Implementera tillbakaskrivning av lösenord](../authentication/howto-sspr-writeback.md) | Tillåt ändring av lösenord i molnet för att skrivas tillbaka till en lokal Windows Server Active Directory-miljö. | Azure AD Premium P1 |
| [Implementera Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md) | Aktivera övervakning av EU: s statistik för Azure AD Connect-servrar, AD FS-servrar och domänkontrollanter. | Azure AD Premium P1 |
| [Tilldela licenser till användare av gruppmedlemskap i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Spara tid och energi genom att skapa licensiering grupper som aktiverar eller inaktiverar funktioner av gruppen i stället för inställningen per användare. | |
| [Skapa en plan för gästanvändares åtkomst](../b2b/what-is-b2b.md) | Samarbeta med gästanvändare genom att låta dem logga in på dina appar och tjänster med sina egna arbets-, Skol- eller sociala identiteter. | [Azure AD B2B-licensiering vägledning](../b2b/licensing-guidance.md) |
| [Besluta om enhetshanteringsstrategi](../devices/overview.md) | Bestäm vad din organisation tillåter inte om enheter. Registrera vs koppla, Bring Your Own Device vs företagets tillhandahålls. | |
| [Distribuera Windows Hello för företag i din organisation](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Förbereda för lösenord utan autentisering med hjälp av Windows Hello | |

## <a name="phase-3-manage-applications"></a>Fas 3: Hantera program

När vi fortsätter att bygga vidare på föregående steg, vi identifiera kandidat program för migrering och integrering med Azure AD och slutföra installationen av programmen.

| Aktivitet | Information | Licens som krävs |
| ---- | ------ | ---------------- |
| Identifiera dina program | Identifiera program som används i din organisation: lokala, SaaS-program i molnet och andra line-of-business-program. Anger om programmen kan och bör hanteras med Azure AD. | Ingen licens krävs |
| [Integrera SaaS-program som stöds i galleriet](../manage-apps/add-application-portal.md) | Azure AD har ett galleri som innehåller tusentals redan integrerade program. Några av de program som din organisation använder är förmodligen i galleriet tillgänglig direkt från Azure-portalen. | Azure AD Kostnadsfri |
| [Använda programproxy för att integrera dina lokala program](../manage-apps/application-proxy-add-on-premises-application.md) | Programproxy kan användare komma åt lokala program genom att logga in med sitt Azure AD. | Azure AD Basic |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Steg 4: Granska Privilegierade identiteter, Slutför en åtkomstgranskning och hantera livscykeln för användare

Fas 4 ser administratörer tillämpa minsta privilegium principer för administration, du har slutfört sin första åtkomstgranskningar och aktivera automatisering av vanliga aktiviteter för livscykelhantering av användare.

| Aktivitet | Information | Licens som krävs |
| ---- | ------ | ---------------- |
| [Framtvinga användningen av Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Ta bort administrativa roller från normala dagliga användarkonton. Berättiga administrativa användare att använda deras roll efter lyckas en kontroll för multifaktorautentisering, vilket ger en motivering eller begära godkännande från utnämnda godkännare. | Azure AD Premium P2 |
| [Slutför en åtkomstgranskning för Azure AD-katalogroller i PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Arbeta med dina säkerhets- och ledarskap team att skapa en princip för granskning av åtkomst om du vill granska administrativ åtkomst baserat på organisationens principer. | Azure AD Premium P2 |
| [Implementera principer för dynamiskt medlemskap](../users-groups-roles/groups-dynamic-membership.md) | Använda dynamiska grupper för att automatiskt tilldela användare i grupper baserat på deras attribut från HR (eller din källa till sanningen), till exempel avdelning, rubrik, region, och andra attribut. |  |
| [Implementera gruppbaserade programetablering](../manage-apps/what-is-access-management.md) | Använd gruppbaserad åtkomsthantering driftsättning till automatiskt etablera användare för SaaS-program. |  |
| [Automatisera användaretablering och avetablering](../manage-apps/user-provisioning.md) | Ta bort manuella steg från anställdas konto livscykeln att förhindra obehörig åtkomst. Synkronisera identiteter från din källa till sanningen (HR-systemet) till Azure AD. |  |

## <a name="next-steps"></a>Nästa steg

[Azure AD-licensiering och prisinformation](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfigurationer för identitets- och Enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Gemensamma rekommenderade principer för åtkomst av identitets- och Enhetsåtkomst](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
