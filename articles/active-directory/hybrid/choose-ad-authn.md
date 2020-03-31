---
title: Autentisering för Azure AD-hybrididentitetslösningar
titleSuffix: Active Directory
description: Den här guiden hjälper vd:ar, CIOs, CISOs, Chief Identity Architects, Enterprise Architects och Security and IT beslutsfattare som ansvarar för att välja en autentiseringsmetod för sin Azure AD hybrid identitetslösning i medelstora till stora organisationer.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e263ecde532a8aaed420932bf355910da201723e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365839"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Välj rätt autentiseringsmetod för azure Active Directory-hybrididentitetslösningen

Att välja rätt autentiseringsmetod är det första problemet för organisationer som vill flytta sina appar till molnet. Ta inte lätt på detta beslut av följande skäl:

1. Det är det första beslutet för en organisation som vill flytta till molnet.

2. Autentiseringsmetoden är en viktig komponent i en organisations närvaro i molnet. Den styr åtkomsten till alla molndata och resurser.

3. Det är grunden för alla andra avancerade säkerhets- och användarupplevelsefunktioner i Azure AD.

Identitet är det nya kontrollplanet för IT-säkerhet, så autentisering är en organisations åtkomstbevakning till den nya molnvärlden. Organisationer behöver ett identitetskontrollplan som stärker deras säkerhet och håller sina molnappar säkra från inkräktare.

> [!NOTE]
> För att ändra autentiseringsmetoden krävs planering, testning och eventuellt driftstopp. [Stegvis distribution](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) är ett bra sätt att testa och gradvis migrera från federation till molnautentisering.

### <a name="out-of-scope"></a>Utanför räckvidden
Organisationer som inte har ett befintligt lokalt katalogavtryck är inte i fokus för den här artikeln. Vanligtvis skapar dessa företag identiteter endast i molnet, vilket inte kräver en hybrididentitetslösning. Molnidentiteter finns enbart i molnet och är inte associerade med motsvarande lokala identiteter.

## <a name="authentication-methods"></a>Autentiseringsmetoder
När Azure AD hybrid identitetslösning är ditt nya kontrollplan, autentisering är grunden för molnåtkomst. Att välja rätt autentiseringsmetod är ett avgörande första beslut när du konfigurerar en Azure AD-hybrididentitetslösning. Implementera autentiseringsmetoden som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod måste du tänka på tid, befintlig infrastruktur, komplexitet och kostnad för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan förändras med tiden.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD stöder följande autentiseringsmetoder för hybrididentitetslösningar.

### <a name="cloud-authentication"></a>Molnautentisering
När du väljer den här autentiseringsmetoden hanterar Azure AD användarnas inloggningsprocess. Tillsammans med sömlös enkel inloggning (SSO) kan användare logga in på molnappar utan att behöva ange sina autentiseringsuppgifter igen. Med molnautentisering kan du välja mellan två alternativ:

**Azure AD-lösenord hash-synkronisering**. Det enklaste sättet att aktivera autentisering för lokala katalogobjekt i Azure AD. Användare kan använda samma användarnamn och lösenord som de använder lokalt utan att behöva distribuera någon ytterligare infrastruktur. Vissa premiumfunktioner i Azure AD, till exempel Identity Protection och [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md), kräver synkronisering av lösenord hash-funktioner, oavsett vilken autentiseringsmetod du väljer.

> [!NOTE]
> Lösenord lagras aldrig i klartext eller krypteras med en reversibel algoritm i Azure AD. Mer information om själva processen för synkronisering av lösenord hash finns i Implementera synkronisering av [lösenord hash med Azure AD Connect-synkronisering](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Azure AD-direktautentisering**. Tillhandahåller en enkel lösenordsvalidering för Azure AD-autentiseringstjänster med hjälp av en programvaruagent som körs på en eller flera lokala servrar. Servrarna validerar användarna direkt med din lokala Active Directory, vilket säkerställer att lösenordsverifieringen inte sker i molnet.

Företag med säkerhetskrav för att omedelbart framtvinga lokala användarkontotillstånd, lösenordsprinciper och inloggningstider kan använda den här autentiseringsmetoden. Mer information om den faktiska direktautentiseringsprocessen finns [i Användarloggning med Azure AD-direktautentisering](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Federerad autentisering
När du väljer den här autentiseringsmetoden lämnar Azure AD autentiseringsprocessen till ett separat tillförlitligt autentiseringssystem, till exempel lokala AD FS (Active Directory Federation Services), för att validera användarens lösenord.

Autentiseringssystemet kan tillhandahålla ytterligare avancerade autentiseringskrav. Exempel är smartcard-baserad autentisering eller multifaktorautentisering från tredje part. Mer information finns i [Distribuera Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

I följande avsnitt kan du bestämma vilken autentiseringsmetod som är rätt för dig med hjälp av ett beslutsträd. Det hjälper dig att avgöra om du vill distribuera moln- eller federerad autentisering för din Azure AD-hybrididentitetslösning.

## <a name="decision-tree"></a>Beslutsträd

![Beslutsträd för Azure AD-autentisering](./media/choose-ad-authn/azure-ad-authn-image1.png)

Information om beslutsfrågor:

1. Azure AD kan hantera inloggning för användare utan att förlita sig på lokala komponenter för att verifiera lösenord.
2. Azure AD kan lämna över användarloggning till en betrodd autentiseringsleverantör, till exempel Microsofts AD FS.
3. Om du behöver tillämpa active directory-säkerhetsprinciper på användarnivå, till exempel kontot har upphört att gälla, inaktiverat konto, lösenord som har upphört att gälla, att kontot är utelåst och inloggningstimmar för varje användares inloggning, kräver Azure AD vissa lokala komponenter.
4. Inloggningsfunktioner som inte stöds internt av Azure AD:
   * Logga in med smartkort eller certifikat.
   * Logga in med lokal MFA-server.
   * Logga in med autentiseringslösning från tredje part.
   * Lokal autentiseringslösning på flera platser.
5. Azure AD Identity Protection kräver Password Hash Sync oavsett vilken inloggningsmetod du väljer, för att ge rapporten *Användare med läckta autentiseringsuppgifter.* Organisationer kan växla över till Password Hash Sync om deras primära inloggningsmetod misslyckas och den konfigurerades före felhändelsen.

> [!NOTE]
> Azure AD Identity Protection kräver [Azure AD Premium P2-licenser.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Detaljerade överväganden

### <a name="cloud-authentication-password-hash-synchronization"></a>Molnautentisering: Synkronisering av lösenordsh hash

* **Ansträngning**. Synkronisering av lösenord hash kräver minsta möjliga ansträngning när det gäller distribution, underhåll och infrastruktur.  Den här nivån av arbete gäller vanligtvis för organisationer som bara behöver sina användare att logga in på Office 365, SaaS-appar och andra Azure AD-baserade resurser. När den är aktiverat är synkronisering av lösenord hash en del av Azure AD Connect-synkroniseringsprocessen och körs varannan minut.

* **Användarupplevelse**. För att förbättra användarnas inloggningsupplevelse distribuera sömlös SSO med synkronisering av lösenord hash. Sömlös SSO eliminerar onödiga uppmaningar när användare är inloggade.

* **Avancerade scenarier**. Om organisationer väljer att använda det möjligt att använda insikter från identiteter med Azure AD Identity Protection-rapporter med Azure AD Premium P2. Ett exempel är rapporten läckta autentiseringsuppgifter. Windows Hello för företag har [särskilda krav när du använder synkronisering av lösenordshÃ¤nning](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) kräver lösenordsh hash-synkronisering för att etablera användare med deras företagsautentiseringsuppgifter i den hanterade domänen.

    Organisationer som kräver multifaktorautentisering med lösenordsh hash-synkronisering måste använda azure AD-multifaktorautentisering eller [anpassade kontroller för villkorlig åtkomst](../../active-directory/conditional-access/controls.md#custom-controls-preview). Dessa organisationer kan inte använda metoder för autentisering från tredje part eller lokala multifaktorer som är beroende av federationen.

> [!NOTE]
> Azure AD Villkorlig åtkomst kräver [Azure AD Premium P1-licenser.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Kontinuitet i verksamheten**. Att använda synkronisering av lösenord hash-synkronisering med molnautentisering är mycket tillgängligt som en molntjänst som skalas till alla Microsoft-datacenter. Om du vill vara säker på att synkroniseringen av lösenordsh hash-värdet inte går under längre perioder distribuerar du en andra Azure AD Connect-server i mellanlagringsläge i en standby-konfiguration.

* **Överväganden**. För närvarande tillämpar synkronisering av lösenord hash inte omedelbart ändringar i lokala kontotillstånd. I det här fallet har en användare åtkomst till molnappar tills användarkontotillståndet synkroniseras med Azure AD. Organisationer kanske vill övervinna den här begränsningen genom att köra en ny synkroniseringscykel när administratörer har gjort massuppdateringar till lokala användarkontotillstånd. Ett exempel är att inaktivera konton.

> [!NOTE]
> Lösenordet har upphört att gälla och att låsta kontotillstånd för närvarande inte synkroniseras med Azure AD med Azure AD Connect. När du ändrar en användares lösenord och anger *att användaren måste ändra lösenord vid nästa inloggningsflagga* synkroniseras inte lösenordshã¤nen till Azure AD med Azure AD Connect förrän användaren ändrar sitt lösenord.

Se implementera [synkronisering av lösenordshã¥1er](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) för distributionssteg.

### <a name="cloud-authentication-pass-through-authentication"></a>Molnautentisering: Direktautentisering  

* **Ansträngning**. För direktautentisering behöver du en eller flera (vi rekommenderar tre) lätta agenter installerade på befintliga servrar. Dessa agenter måste ha åtkomst till dina lokala Active Directory Domain Services, inklusive dina lokala AD-domänkontrollanter. De behöver utgående åtkomst till Internet och tillgång till domänkontrollanterna. Därför stöds det inte att distribuera agenter i ett perimeternätverk.

    Direktautentisering kräver obegränsad nätverksåtkomst till domänkontrollanter. All nätverkstrafik är krypterad och begränsad till autentiseringsbegäranden. Mer information om den här processen finns i [djupdykningen](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) för säkerhet vid genomströmning.

* **Användarupplevelse**. För att förbättra användarnas inloggningsupplevelse kan du distribuera sömlös SSO med direktautentisering. Sömlös SSO eliminerar onödiga uppmaningar när användare har loggat in.

* **Avancerade scenarier**. Direktautentisering tillämpar den lokala kontoprincipen vid inloggningen. Åtkomst nekas till exempel när en lokal användares kontotillstånd är inaktiverat, utelåst eller om lösenordet upphör att gälla eller [inloggningsförsöket](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) faller utanför de timmar då användaren tillåts logga in.

    Organisationer som kräver multifaktorautentisering med direktautentisering måste använda anpassade Azure Multi-Factor Authentication (MFA) eller [anpassade kontroller för villkorlig åtkomst](../../active-directory/conditional-access/controls.md#custom-controls-preview). Dessa organisationer kan inte använda en autentiseringsmetod för flera faktorer från tredje part eller lokal funktion som är beroende av federationen. Avancerade funktioner kräver att synkronisering av lösenord hash-funktioner distribueras oavsett om du väljer direktautentisering eller inte. Ett exempel är rapporten för läckta autentiseringsuppgifter för Identitetsskydd.

* **Kontinuitet i verksamheten**. Vi rekommenderar att du distribuerar två extra direktautentiseringsagenter. Dessa extrafunktioner är utöver den första agenten på Azure AD Connect-servern. Den här ytterligare distributionen säkerställer hög tillgänglighet för autentiseringsbegäranden. När du har distribuerat tre agenter kan en agent fortfarande misslyckas när en annan agent är nere för underhåll.

    Det finns en annan fördel med att distribuera synkronisering av lösenord hash-synkronisering förutom direktautentisering. Den fungerar som en autentiseringsmetod för säkerhetskopiering när den primära autentiseringsmetoden inte längre är tillgänglig.

* **Överväganden**. Du kan använda synkronisering av lösenordshã¥1er som en autentiseringsmetod för säkerhetskopiering för direktautentisering, när agenterna inte kan validera en användares autentiseringsuppgifter på grund av ett betydande lokalt fel. Växla över till synkronisering av lösenord hash-synkronisering sker inte automatiskt och du måste använda Azure AD Connect för att växla inloggningsmetoden manuellt.

    För andra överväganden om direktautentisering, inklusive stöd för alternativt ID, finns [vanliga frågor och svar](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Se implementera [direktautentisering](../../active-directory/hybrid/how-to-connect-pta.md) för distributionssteg.

### <a name="federated-authentication"></a>Federerad autentisering

* **Ansträngning**. Ett federerat autentiseringssystem förlitar sig på ett externt betrott system för att autentisera användare. Vissa företag vill återanvända sina befintliga federerade systeminvesteringar med sin Azure AD hybrid-identitetslösning. Underhåll och hantering av det federerade systemet faller utanför kontrollen av Azure AD. Det är upp till organisationen genom att använda det federerade systemet för att se till att det distribueras på ett säkert sätt och kan hantera autentiseringsbelastningen.

* **Användarupplevelse**. Användarupplevelsen av federerad autentisering beror på implementeringen av funktioner, topologi och konfiguration av federationsgruppen. Vissa organisationer behöver den här flexibiliteten för att anpassa och konfigurera åtkomsten till federationsgruppen så att den passar deras säkerhetskrav. Det är till exempel möjligt att konfigurera internt anslutna användare och enheter för att logga in användare automatiskt, utan att du erar dem för autentiseringsuppgifter. Den här konfigurationen fungerar eftersom de redan har loggat in på sina enheter. Om det behövs gör vissa avancerade säkerhetsfunktioner användarnas inloggningsprocess svårare.

* **Avancerade scenarier**. En federerad autentiseringslösning krävs vanligtvis när kunder har ett autentiseringskrav som Azure AD inte stöder internt. Se detaljerad information som hjälper dig att [välja rätt inloggningsalternativ](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Tänk på följande gemensamma krav:

  * Autentisering som kräver smartkort eller certifikat.
  * Lokala MFA-servrar eller tredjepartsleverantörer av flera faktorer som kräver en federerad identitetsleverantör.
  * Autentisering med hjälp av autentiseringslösningar från tredje part. Se [kompatibilitetslistan för Azure AD-federation](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Logga in som kräver ett sAMAccountName, till exempel DOMÄN\användarnamn, i stället user@domain.comför ett UPN (User Principal Name), till exempel .

* **Kontinuitet i verksamheten**. Federerade system kräver vanligtvis en belastningsbalanserad matris med servrar, så kallade en servergrupp. Den här servergruppen är konfigurerad i en intern nätverks- och perimeternätverkstopologi för att säkerställa hög tillgänglighet för autentiseringsbegäranden.

    Distribuera synkronisering av lösenord hash-synkronisering tillsammans med federerad autentisering som en autentiseringsmetod för säkerhetskopiering när den primära autentiseringsmetoden inte längre är tillgänglig. Ett exempel är när de lokala servrarna inte är tillgängliga. Vissa stora företagsorganisationer kräver en federationslösning för att stödja flera Internet-ingress-punkter som konfigurerats med geo-DNS för autentiseringsbegäranden med låg latens.

* **Överväganden**. Federerade system kräver vanligtvis en mer betydande investering i lokal infrastruktur. De flesta organisationer väljer det här alternativet om de redan har en lokal federationsinvestering. Och om det är ett starkt affärskrav att använda en enda identitetsleverantör. Federationen är mer komplex att använda och felsöka jämfört med molnautentiseringslösningar.

För en icke-dirigerbar domän som inte kan verifieras i Azure AD behöver du extra konfiguration för att implementera inloggning av användar-ID. Det här kravet kallas stöd för alternativt inloggnings-ID. Se [Konfigurera alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) för begränsningar och krav. Om du väljer att använda en multifaktorautentiseringsprovider från tredje part med federation, se till att providern stöder WS-Trust så att enheter kan gå med i Azure AD.

Se Distribuera [federationsservrar](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) för distributionssteg.

> [!NOTE]
> När du distribuerar din Azure AD-hybrididentitetslösning måste du implementera en av de topologier som stöds i Azure AD Connect. Läs mer om konfigurationer som stöds och inte stöds på [Topologies för Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Arkitekturdiagram

I följande diagram beskrivs de arkitekturkomponenter på hög nivå som krävs för varje autentiseringsmetod som du kan använda med din Azure AD-hybrididentitetslösning. De ger en översikt som hjälper dig att jämföra skillnaderna mellan lösningarna.

* Enkelhet i en lösning för synkronisering av lösenord hash:er:

    ![Azure AD hybrid identitet med lösenord hash synkronisering](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Agentkrav för genomströmningsautentisering med hjälp av två agenter för redundans:

    ![Azure AD-hybrididentitet med direktautentisering](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Komponenter som krävs för federation i din omkrets och interna nätverk i din organisation:

    ![Azure AD-hybrididentitet med federerad autentisering](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Jämföra metoder

|Att tänka på|Synkronisering av lösenord hash + sömlös SSO|Direktautentisering + Sömlös SSO|Federation med AD FS|
|:-----|:-----|:-----|:-----|
|Var sker autentisering?|I molnet|I molnet efter ett säkert utbyte av lösenordsverifiering med den lokala autentiseringsagenten|Lokal|
|Vilka är de lokala serverkraven utöver etableringssystemet: Azure AD Connect?|Inget|En server för varje ytterligare autentiseringsagent|Två eller flera AD FS-servrar<br><br>Två eller flera WAP-servrar i perimeter/DMZ-nätverket|
|Vilka är kraven för lokalt Internet och nätverk utanför etableringssystemet?|Inget|[Utgående Internet-åtkomst](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) från servrar som kör autentiseringsagenter|[Inkommande internetåtkomst](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) till WAP-servrar i omkretsen<br><br>Inkommande nätverksåtkomst till AD FS-servrar från WAP-servrar i omkretsen<br><br>Utjämning av nätverksbelastning|
|Finns det ett TLS/SSL-certifikatkrav?|Inga|Inga|Ja|
|Finns det en lösning för hälsoövervakning?|Krävs inte|Agentstatus som tillhandahålls av [Azure Active Directory-administrationscenter](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Får användarna enkel inloggning till molnresurser från domänanslutna enheter i företagsnätverket?|Ja med [sömlös SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja med [sömlös SSO](../../active-directory/hybrid/how-to-connect-sso.md)|Ja|
|Vilka inloggningstyper stöds?|UserPrincipalName + lösenord<br><br>Windows-integrerad autentisering med sömlös [SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativt inloggnings-ID](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + lösenord<br><br>Windows-integrerad autentisering med sömlös [SSO](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativt inloggnings-ID](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + lösenord<br><br>sAMAccountName + lösenord<br><br>Windows-integrerad autentisering<br><br>[Certifikat- och smartkortsautentisering](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativt inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Stöds Windows Hello för företag?|[Nyckelförtroendemodell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Nyckelförtroendemodell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Kräver funktionsnivå för Windows Server 2016-domän*|[Nyckelförtroendemodell](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modell för certifikatförtroende](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Vilka är alternativen för multifaktorautentisering?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Anpassade kontroller med villkorlig åtkomst*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Anpassade kontroller med villkorlig åtkomst*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA-server](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA från tredje part](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Anpassade kontroller med villkorlig åtkomst*](../../active-directory/conditional-access/controls.md)|
|Vilka användarkontotillstånd stöds?|Inaktiverade konton<br>(upp till 30 minuters fördröjning)|Inaktiverade konton<br><br>Kontot är utelåst<br><br>Kontot har upphört att gälla<br><br>Lösenordet har upphört att gälla<br><br>Inloggningstider|Inaktiverade konton<br><br>Kontot är utelåst<br><br>Kontot har upphört att gälla<br><br>Lösenordet har upphört att gälla<br><br>Inloggningstider|
|Vilka är alternativen för villkorlig åtkomst?|[Azure AD-villkorlig åtkomst med Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Azure AD-villkorlig åtkomst med Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Azure AD-villkorlig åtkomst med Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[REGLER för AD FS-anspråk](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Stöds blockering av äldre protokoll?|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](../../active-directory/conditional-access/overview.md)|[Ja](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Kan du anpassa logotypen, bilden och beskrivningen på inloggningssidorna?|[Ja, med Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja, med Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ja](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Vilka avancerade scenarier stöds?|[Smart lösenordsutelåsning](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Läckta autentiseringsuppgifter rapporter, med Azure AD Premium P2](../../active-directory/reports-monitoring/concept-risk-events.md)|[Smart lösenordsutelåsning](../../active-directory/authentication/howto-password-smart-lockout.md)|Autentiseringssystem för autentisering på flera plats<br><br>[AD FS extranätsutelåsning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integration med identitetssystem från tredje part](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Anpassade kontroller i Azure AD Villkorlig åtkomst stöder för närvarande inte enhetsregistrering.

## <a name="recommendations"></a>Rekommendationer
Ditt identitetssystem säkerställer användarnas åtkomst till molnappar och de affärsappar som du migrerar och gör tillgängliga i molnet. Om du vill att behöriga användare ska vara produktiva och dåliga aktörer borta från organisationens känsliga data styr autentiseringen åtkomsten till appar.

Använd eller aktivera synkronisering av lösenordshã¥1er för vilken autentiseringsmetod du väljer, av följande skäl:

1. **Hög tillgänglighet och haveriberedskap**. Direktautentisering och federation är beroende av lokal infrastruktur. För direktautentisering omfattar det lokala fotavtrycket den servermaskinvara och nätverk som direktautentiseringsagenterna kräver. För federationen är det lokala fotavtrycket ännu större. Det kräver servrar i perimeternätverket för att proxyautentiseringsbegäranden och de interna federationsservrarna.

    Om du vill undvika enstaka felpunkter distribuerar du redundanta servrar. Då kommer autentiseringsbegäranden alltid att servas om någon komponent misslyckas. Både direktautentisering och federation förlitar sig också på domänkontrollanter för att svara på autentiseringsbegäranden, vilket också kan misslyckas. Många av dessa komponenter behöver underhåll för att hålla sig friska. Avbrott är mer sannolika när underhåll inte planeras och implementeras korrekt. Undvik avbrott med hjälp av synkronisering av lösenordsh hash eftersom Microsoft Azure AD-molnautentiseringstjänsten skalas globalt och alltid är tillgänglig.

2. **Lokala avbrott överlevnad**.  Konsekvenserna av ett lokalt avbrott på grund av en cyberattack eller katastrof kan vara betydande, allt från rykte varumärkesskador till en förlamad organisation som inte kan hantera attacken. Nyligen var många organisationer offer för malware attacker, inklusive riktade ransomware, vilket orsakade deras lokala servrar att gå ner. När Microsoft hjälper kunder att hantera den här typen av attacker ser det två kategorier av organisationer:

   * Organisationer som tidigare aktiverat synkronisering av lösenord hash-synkronisering ändrade sin autentiseringsmetod för att använda synkronisering av lösenord hash. De var online igen om några timmar. Genom att använda åtkomst till e-post via Office 365 arbetade de med att lösa problem och komma åt andra molnbaserade arbetsbelastningar.

   * Organisationer som inte tidigare har aktiverat synkronisering av lösenord hash-synkronisering var tvungna att tillgripa icke betrodda externa e-postsystem för konsumenter för kommunikation för att lösa problem. I dessa fall tog det dem veckor att återställa sin lokala identitetsinfrastruktur, innan användarna kunde logga in på molnbaserade appar igen.

3. **Identitetsskydd**. Ett av de bästa sätten att skydda användare i molnet är Azure AD Identity Protection med Azure AD Premium P2. Microsoft söker kontinuerligt igenom Internet efter användar- och lösenordslistor som dåliga aktörer säljer och gör tillgängliga på den mörka webben. Azure AD kan använda den här informationen för att verifiera om något av användarnamnen och lösenorden i organisationen har komprometterats. Därför är det viktigt att aktivera synkronisering av lösenord hash oavsett vilken autentiseringsmetod du använder, oavsett om den är federerad eller direktautentisering. Läckta autentiseringsuppgifter visas som en rapport. Använd den här informationen om du vill blockera eller tvinga användare att ändra sina lösenord när de försöker logga in med läckta lösenord.

## <a name="conclusion"></a>Slutsats

I den här artikeln beskrivs olika autentiseringsalternativ som organisationer kan konfigurera och distribuera för att stödja åtkomst till molnappar. För att uppfylla olika affärs-, säkerhets- och tekniska krav kan organisationer välja mellan synkronisering av lösenord hash, direktautentisering och federation.

Tänk på varje autentiseringsmetod. Tar arbetet med att distribuera lösningen och användarens upplevelse av inloggningsprocessen upp dina affärsbehov? Utvärdera om din organisation behöver avancerade scenarier och funktioner för affärskontinuitet för varje autentiseringsmetod. Slutligen utvärdera övervägandena för varje autentiseringsmetod. Hindrar någon av dem dig från att genomföra ditt val?

## <a name="next-steps"></a>Nästa steg

I dagens värld är hoten närvarande 24 timmar om dygnet och kommer från överallt. Implementera rätt autentiseringsmetod, och det kommer att minska dina säkerhetsrisker och skydda dina identiteter.

[Kom igång](../../active-directory/fundamentals/get-started-azure-ad.md) med Azure AD och distribuera rätt autentiseringslösning för din organisation.

Om du funderar på att migrera från federerad till molnautentisering kan du läsa mer om [hur du ändrar inloggningsmetoden](../../active-directory/hybrid/plan-connect-user-signin.md). Om du vill hjälpa dig att planera och implementera migreringen använder du [dessa projektdistributionsplaner](https://aka.ms/deploymentplans) eller överväger att använda den nya [rollout-funktionen för stegvis](../../active-directory/hybrid/how-to-connect-staged-rollout.md) distribution för att migrera federerade användare till att använda molnautentisering i en stegvis metod.
