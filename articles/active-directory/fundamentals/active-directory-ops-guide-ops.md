---
title: Referens för Azure Active Directory allmänt verksamhetsguide
description: I referensguiden för åtgärder beskrivs de kontroller och åtgärder du bör vidta för att säkra allmänna
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422950"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Referens för Azure Active Directory allmänt verksamhetsguide

I det här avsnittet i [referensguiden för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskrivs de kontroller och åtgärder du bör vidta för att optimera de allmänna åtgärderna i Azure Active Directory (Azure AD).

> [!NOTE]
> Dessa rekommendationer är aktuella från och med publiceringsdatumet men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sin operativa praxis när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till nyckeluppgifter

För att hantera Azure Active Directory krävs kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett distributionsprojekt. Det är fortfarande viktigt att du ställer in dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Aktivitet | Ägare |
| :- | :- |
| Öka enhetsförbättringar på identitetssäker poäng | InfoSec operationsteam |
| Underhåll Azure AD Connect-servrar | IAM Operations Team |
| Utför regelbundet och triage IdFix-rapporter | IAM Operations Team |
| Triage Azure AD Connect hälsovarningar för synkronisering och AD FS | IAM Operations Team |
| Om inte använda Azure AD Connect Health, då kunden har motsvarande process och verktyg för att övervaka anpassad infrastruktur | IAM Operations Team |
| Om inte använda AD FS, då kunden har motsvarande process och verktyg för att övervaka anpassad infrastruktur | IAM Operations Team |
| Övervaka hybridloggar: Azure AD App Proxy-kopplingar | IAM Operations Team |
| Övervaka hybridloggar: Passthrough-autentiseringsagenter | IAM Operations Team |
| Övervakarhybridloggar: Tjänsten för tillbakaskrivning av lösenord | IAM Operations Team |
| Övervaka hybridloggar: Lokal gateway för lösenordsskydd | IAM Operations Team |
| Övervaka hybridloggar: Azure MFA NPS-tillägg (om tillämpligt) | IAM Operations Team |

När du granskar listan kan du behöva tilldela en ägare för uppgifter som saknar en ägare eller justera ägarskapet för uppgifter med ägare som inte är anpassade till rekommendationerna ovan.

#### <a name="owners-recommended-reading"></a>Ägarna rekommenderade läsning

- [Tilldela administratörsroller i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Styrning i Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Hybridhantering

### <a name="recent-versions-of-on-premises-components"></a>Senaste versioner av lokala komponenter

Med de mest uppdaterade versionerna av lokala komponenter ger kunden alla de senaste säkerhetsuppdateringarna, prestandaförbättringar samt funktioner som kan bidra till att ytterligare förenkla miljön. De flesta komponenter har en automatisk uppgraderingsinställning, vilket automatiserar uppgraderingsprocessen.

Dessa komponenter inkluderar:

- Azure AD Connect
- Proxykopplingar för Azure AD-program
- Azure AD-direktautentiseringsagenter
- Azure AD Connect hälsoagenter

Om inte en har upprättats bör du definiera en process för att uppgradera dessa komponenter och förlita dig på den automatiska uppgraderingsfunktionen när det är möjligt. Om du hittar komponenter som ligger sex eller flera månader efter bör du uppgradera så snart som möjligt.

#### <a name="hybrid-management-recommended-reading"></a>Hybridhantering rekommenderad läsning

- [Azure AD Connect: Automatisk uppgradering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Förstå Azure AD-programproxyanslutningar | Automatiska uppdateringar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect hälsoaviseringsbaslinje

Organisationer bör distribuera [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) för övervakning och rapportering av Azure AD Connect och AD FS. Azure AD Connect och AD FS är kritiska komponenter som kan bryta livscykelhantering och autentisering och därför leda till avbrott. Azure AD Connect Health hjälper till att övervaka och få insikter i din lokala identitetsinfrastruktur och på så sätt säkerställa tillförlitligheten i din miljö.

![Azure AD Connect Heath-arkitektur](./media/active-directory-ops-guide/active-directory-ops-img16.png)

När du övervakar hälsotillståndet för din miljö måste du omedelbart ta itu med eventuella aviseringar om hög allvarlighetsgrad, följt av varningar om lägre allvarlighetsgrad.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Hälsa rekommenderas läsning

- [Installation av Azure AD Connect Health Agent](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Lokala agenter loggar

Vissa identitets- och åtkomsthanteringstjänster kräver lokala agenter för att aktivera hybridscenarier. Exempel på detta är återställning av lösenord, direktautentisering (PTA), Azure AD Application Proxy och Azure MFA NPS-tillägg. Det är viktigt att driftteamets baslinje och övervakar dessa komponenters hälsa genom att arkivera och analysera komponentagentloggarna med hjälp av lösningar som System Center Operations Manager eller SIEM. Det är lika viktigt att ditt Infosec Operations-team eller supportavdelning förstår hur du felsöker felmönster med fel.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Lokala agenter loggar rekommenderas läsning

- [Felsöka programproxyn](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Felsökning av återställning av lösenord med självbetjäning - Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Förstå Azure AD-programproxy-kopplingar](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: Felsöka direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Felsöka felkoder för Azure MFA NPS-tillägget](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Lokal agenthantering

Genom att anta bästa praxis kan det vara optimalt att fungera lokalt. Tänk på följande metodtips:

- Flera Azure AD-proxyanslutningar per kopplingsgrupp rekommenderas för att tillhandahålla sömlös belastningsutjämning och hög tillgänglighet genom att undvika enskilda felpunkter när proxyprogrammen används. Om du för närvarande bara har en koppling i en kopplingsgrupp som hanterar program i produktion bör du distribuera minst två kopplingar för redundans.
- Det kan vara användbart att skapa och använda en appproxyanslutningsgrupp för felsökningsändamål för felsökning av scenarier och när du använder nya lokala program. Vi rekommenderar också att du installerar nätverksverktyg som Message Analyzer och Fiddler i anslutningsdatorerna.
- Flera direktautentiseringsagenter rekommenderas för att tillhandahålla sömlös belastningsutjämning och hög tillgänglighet genom att undvika enstaka felpunkt under autentiseringsflödet. Var noga med att distribuera minst två direktautentiseringsagenter för redundans.

#### <a name="on-premises-agents-management-recommended-reading"></a>Lokal agenthantering rekommenderade läsning

- [Förstå Azure AD-programproxy-kopplingar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD-direktautentisering – snabbstart](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Hantering i stor skala

### <a name="identity-secure-score"></a>Identitetssäker poäng

[Identitetssäker poäng](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) ger ett kvantifierbart mått på säkerhetspositionen för din organisation. Det är nyckeln till att ständigt granska och ta itu med rapporterade resultat och strävar efter att ha högsta möjliga poäng. Resultatet hjälper dig att:

- Objektivt mäta din identitetssäkerhetsstatus
- Planera förbättringar i identitetssäkerheten
- Granska framgången för dina förbättringar

![Säkerhetspoäng](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Om din organisation för närvarande inte har något program för att övervaka ändringar i Identity Secure Score, rekommenderar vi att du implementerar en plan och tilldelar ägare att övervaka och driva förbättringsåtgärder. Organisationer bör åtgärda förbättringsåtgärder med en poängeffekt som är högre än 30 så snart som möjligt.

### <a name="notifications"></a>Meddelanden

Microsoft skickar e-postkommunikation till administratörer för att meddela olika ändringar i tjänsten, konfigurationsuppdateringar som behövs och fel som kräver administratörsintervention. Det är viktigt att kunderna anger e-postadresserna för meddelandet så att meddelanden skickas till rätt gruppmedlemmar som kan bekräfta och agera på alla meddelanden. Vi rekommenderar att du lägger till flera mottagare i [Office 365 Message Center](https://docs.microsoft.com/office365/admin/manage/message-center) och begär att meddelanden (inklusive Azure AD Connect Health-meddelanden) ska skickas till en distributionslista eller delad postlåda. Om du bara har ett globalt administratörskonto med en e-postadress måste du konfigurera minst två e-postkompatibla konton.

Det finns två "Från"-adresser <o365mc@email2.microsoft.com>som används av Azure AD: , som skickar Office 365 Message Center-meddelanden; och <azure-noreply@microsoft.com>, som skickar meddelanden om:

- [Azure AD Access-granskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Certifikatmeddelanden för företagsapp som upphör att gälla](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Meddelanden om etablering av företagsapp

I följande tabell finns en information om vilken typ av meddelanden som skickas och var du kan söka efter dem:

| Meddelandekälla | Vad skickas | Var ska man kontrollera |
|:-|:-|:-|
| Teknisk kontakt | Synkroniseringsfel | Azure portal - egenskaper blad |
| Meddelandecenter för Office 365 | Meddelanden om tillbud och försämringar av identitetstjänster och O365 backend-tjänster | Office-portal |
| Sammanfattning av identitetsskydd | Sammanfattning av identitetsskydd | Azure AD-identitetsskyddsblad |
| Azure AD Connect Health | Aviseringar | Azure portal - Azure AD Connect Hälsoblad |
| Meddelanden om företagsprogram | Meddelanden när certifikaten håller på att upphöra att gälla och etableringsfel | Azure portal - Enterprise Application blade (varje app har sin egen e-postadressinställning) |

#### <a name="notifications-recommended-reading"></a>Meddelanden rekommenderade läsning

- [Ändra organisationens adress, tekniska kontakt med mera – Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Operativ yta

### <a name="ad-fs-lockdown"></a>AD FS-låsning

Organisationer som konfigurerar program för att autentisera direkt till Azure AD drar nytta av [smart utelåsning](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)i Azure AD . Om du använder AD FS i Windows Server 2012 R2 implementerar du AD FS [extranätsutelåsningsskydd](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Om du använder AD FS på Windows Server 2016 eller senare implementerar du [smart utelåsning av extranät](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Vi rekommenderar åtminstone att du aktiverar extranätsutelåsning för att begränsa risken för brute force-attacker mot lokala Active Directory. Men om du har AD FS i Windows 2016 eller högre, bör du också aktivera extranät smart lockout som hjälper till att minska [lösenord sprayattacker.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Om AD FS endast används för Azure AD-federation finns det några slutpunkter som kan inaktiveras för att minimera angreppsytan. Om TILL exempel AD FS bara används för Azure AD bör du inaktivera andra WS-Trust-slutpunkter än de slutpunkter som är aktiverade för **användarnamn** och **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Tillgång till maskiner med lokala identitetskomponenter

Organisationer bör låsa åtkomsten till datorerna med lokala hybridkomponenter på samma sätt som din lokala domän. En operatör för säkerhetskopiering eller Hyper-V-administratör bör till exempel inte kunna logga in på Azure AD Connect Server för att ändra regler.

Active Directory-modellen för administrativ nivå har utformats för att skydda identitetssystem med hjälp av en uppsättning buffertzoner mellan fullständig kontroll över miljön (nivå 0) och de arbetsstationstillgångar med hög risk som angripare ofta kompromissar med. ![Diagram över de tre nivåerna i nivåmodellen](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Nivåmodellen](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) består av tre nivåer och innehåller endast administrativa konton, inte standardanvändarkonton.

- **Nivå 0** - Direkt kontroll av företagsidentiteter i miljön. Nivå 0 innehåller konton, grupper och andra resurser som har direkt eller indirekt administrativ kontroll över Active Directory-skogen, domäner eller domänkontrollanter och alla tillgångar i den. Säkerhetskänsligheten för alla tillgångar i nivå 0 är motsvarande eftersom de alla har effektiv kontroll av varandra.
- **Nivå 1** - Kontroll av företagets servrar och program. Nivå 1-material inkluderar serveroperativsystem, molntjänster och företagsprogram. Nivå 1-administratörskonton har administrativ kontroll över en betydande mängd affärsvärden som dessa tillgångar är värdar för. En vanlig exempelroll är serveradministratörer som hanterar de här operativsystemen med möjlighet att påverka alla företagstjänster.
- **Nivå 2** - Kontroll av användararbetsstationer och enheter. Nivå 2-administratörskonton har administrativ kontroll över en betydande mängd affärsvärden som dessa arbetsstationer och enheter är värdar för. Exempel innefattar administratörer för supportavdelningen och datorsupport eftersom de kan påverka integriteten för nästan alla användardata.

Lås åtkomsten till lokala identitetskomponenter som Azure AD Connect, AD FS och SQL-tjänster på samma sätt som för domänkontrollanter.

## <a name="summary"></a>Sammanfattning

Det finns sju aspekter på en säker identitetsinfrastruktur. Den här listan hjälper dig att hitta de åtgärder du bör vidta för att optimera åtgärderna för Azure Active Directory (Azure AD).

- Tilldela ägare till nyckeluppgifter.
- Automatisera uppgraderingsprocessen för lokala hybridkomponenter.
- Distribuera Azure AD Connect-hälsotillstånd för övervakning och rapportering av Azure AD Connect och AD FS.
- Övervaka hälsotillståndet för lokala hybridkomponenter genom att arkivera och analysera komponentagentloggarna med System Center Operations Manager eller en SIEM-lösning.
- Implementera säkerhetsförbättringar genom att mäta din säkerhetsposition med Identity Secure Score.
- Lås AD FS.
- Lås tillträdet till maskiner med lokala identitetskomponenter.

## <a name="next-steps"></a>Nästa steg

Se [Azure AD-distributionsplanerna](active-directory-deployment-plans.md) för implementeringsinformation om alla funktioner som du inte har distribuerat.
