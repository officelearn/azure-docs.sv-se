---
title: Välja rätt autentiseringsmetod för din Azure AD-hybrididentitetslösning | Microsoft Docs
description: 'Den här guiden hjälper koncernchefer, CIO: er, Ciso, Chief identitet arkitekter, Enterprise-arkitekter och säkerhet och IT-beslutsfattare som är ansvarig för att välja en autentiseringsmetod för sina Azure AD-hybrididentitetslösning i medelstora och stora organisationer.'
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 773d4dd28da3165261d75e4f800750c1f54377d0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702295"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning 

Den här artikeln börjar en serie av artiklar som hjälper organisationer att implementera en fullständig hybrididentitetslösning för Azure Active Directory (AD Azure). Den här lösningen har beskrivs som den [Hybrid Identity Digital omvandling Framework](https://aka.ms/aadframework). Den behandlar verksamhetens resultat och mål organisationer kan fokusera på att implementera en robust och säker hybrididentitetslösning. 

Den första affärsresultat av framework anger kraven för organisationer att skydda autentiseringsprocessen när användare har åtkomst till molnappar. Första företag målet i den säkra affärsresultat för autentisering är användarnas möjlighet att logga in på molnappar med hjälp av den lokala användarnamn och lösenord. Den här processen för inloggning och autentisering går allt i molnet.

Att välja rätt autentiseringsmetod är det första problemet för organisationer som vill flytta sina appar till molnet. Inte gör det här beslutet lättvindigt, av följande skäl:

1. Är det första beslutet för organisationer som vill flytta till molnet. 

2. Autentiseringsmetoden är en viktig del i en organisation närvaro i molnet. Den kontrollerar åtkomsten till alla data i molnet och resurser.

3. Det är grunden för alla andra avancerad säkerhet och funktioner för användarupplevelse i Azure AD.

4. Autentiseringsmetoden som är svårt att ändra när den har implementerats.

Identiteten är ny kontrollplanet för IT-säkerhet. Autentisering är alltså en organisations åtkomst guard till den nya världen i molnet. Organisationer behöver en identity-kontrollplanet som förstärks deras säkerhet och behåller sina appar i molnet från obehöriga användare.

### <a name="out-of-scope"></a>Utanför
Organisationer som inte har en befintlig lokal katalog fotavtryck inte fokus i den här artikeln. Vanligtvis skapar dessa företag identiteter endast i molnet, vilket inte kräver en hybrididentitetslösning. Endast molnbaserade identiteter finns bara i molnet och är inte associerad med den motsvarande lokala identiteter.

## <a name="authentication-methods"></a>Autentiseringsmetoder
När Azure AD-hybrididentitetslösning är din nya kontrollplanet, är autentisering grunden för molnåtkomst. Det är ett viktigt första beslut att konfigurera en hybrididentitetslösning i Azure AD för att välja rätt autentiseringsmetod. Implementera den autentiseringsmetod som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod som du behöver tänka på tid, befintlig infrastruktur, komplexiteten och kostnaden för att implementera ditt val. Dessa faktorer är olika för varje organisation och kan ändras över tid. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD stöder följande autentiseringsmetoder för hybrididentitetslösningar.

### <a name="cloud-authentication"></a>Autentisering i molnet
När du väljer den här autentiseringsmetoden hanterar Azure AD användarnas inloggningsprocess. Tillsammans med sömlös enkel inloggning (SSO), logga användare in på molnappar utan att behöva ange sina autentiseringsuppgifter igen. Med autentisering i molnet, kan du välja mellan två alternativ: 

**Azure AD-lösenordshashsynkronisering**. Det enklaste sättet att aktivera autentisering för lokala katalogobjekt i Azure AD. Användare kan använda samma användarnamn och lösenord som de använder lokalt utan att behöva distribuera ytterligare infrastruktur. Vissa premiumfunktioner i Azure AD som Identity Protection och [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync), kräver synkronisering av lösenordshash, oavsett vilken autentiseringsmetod som du väljer.

> [!NOTE] 
> Lösenord lagras i klartext aldrig eller krypterats med en algoritm för omvändbar i Azure AD. Läs mer om den faktiska processen för synkronisering av lösenordshash, [implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization). 

**Azure AD-direktautentisering**. Tillhandahåller en enkel lösenordsverifieringen för tjänster som Azure AD-autentisering med hjälp av en programvaruagent som körs på en eller flera lokala servrar. Servrar verifiera användare direkt med en lokal Active Directory, vilket garanterar att valideringen av lösenordet inte inträffa i molnet. 

Företag med en säkerhetskrav att omedelbart tillämpa lokal användare konto tillstånd, principer för lösenord och logga in timmar kan använda den här autentiseringsmetoden. Läs mer om processen för faktiska direktautentisering [användare logga in med Azure AD-direktautentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta).

### <a name="federated-authentication"></a>Federerad autentisering
När du väljer den här autentiseringsmetoden av Azure AD-händer autentiseringsprocessen till ett separat betrodda autentiseringssystem, till exempel en lokal Active Directory Federation Services (AD FS) för att verifiera användarens lösenord.

Autentiseringssystem kan ge ytterligare avancerade autentiseringskrav. Exempel är smartkortbaserad autentisering eller multifaktorautentisering från tredje part. Mer information finns i [distribution av Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Följande avsnitt hjälper dig att bestämma vilken autentiseringsmetod som passar dig med hjälp av ett beslutsträd. Det hjälper dig att avgöra om du vill distribuera molnet eller federerad autentisering för din Azure AD-hybrididentitetslösning.

## <a name="decision-tree"></a>Beslutsträd

![Beslutsträd för Azure AD-autentisering](media/azure-ad/azure-ad-authn-image1.png)

Information om beslut frågor:

1. Azure AD kan hantera inloggning för användare utan att förlita dig på den lokala komponenter att bekräfta lösenord.
2. Azure AD kan lämna över användarinloggning till en betrodd autentiseringsprovider till exempel Microsofts AD FS.
3. Om du vill tillämpa säkerhetsprinciper för användarnivå Active Directory som kontot har upphört att gälla inaktiverat konto, lösenord har upphört att gälla, låst konto och logga in timmar på varje användare logga in, Azure AD kräver vissa lokala komponenter.
4. Logga in-funktioner som inte stöds av Azure AD:
   * Logga in med smartkort eller certifikat.
   * Logga in med lokala MFA-servern.
   * Logga in med 3 part authentication-lösning.
   * Flera platser på lokal autentiseringslösning.
5. Azure AD Identity Protection kräver Lösenordshashsynkronisering oavsett vilken inloggningsmetod du väljer, att tillhandahålla den *användare med läckta autentiseringsuppgifter* rapporten. Organisationer kan redundansväxla till Lösenordshashsynkronisering om deras primära inloggningsmetod misslyckas och den konfigurerades innan händelsen fel.

>[!NOTE]
> Azure AD Identity Protection kräver [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licenser.

## <a name="detailed-considerations"></a>Detaljerad information

### <a name="cloud-authentication-password-hash-synchronization"></a>Autentisering i molnet: Synkronisering av lösenordshash

* **Arbete**. Synkronisering av lösenordshash kräver minst arbetskrävande avseende distribution, underhåll och infrastruktur.  Den här nivån av arbete gäller vanligtvis för organisationer som behöver bara användarna att logga in på Office 365, SaaS-appar och andra Azure AD-baserade resurser. När aktiverat synkronisering av lösenordshash är en del av Azure AD Connect-synkroniseringen och körs varannan minut.

* **Användarupplevelsen**. För att förbättra användarnas inloggning ska distribuera sömlös SSO med synkronisering av lösenordshash. Sömlös SSO eliminerar onödiga anvisningarna när användare är inloggad.

* **Avancerade scenarier**. Om organisationer väljer att, är det möjligt att använda insikter från identiteter med Azure AD Identity Protection rapporter med Azure AD Premium P2. Ett exempel är rapporten läckta autentiseringsuppgifter. Windows Hello för företag har [specifika krav när du använder synkronisering av lösenordshash](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync) kräver synkronisering av lösenordshash för etablera användare med sina företagsuppgifter i den hanterade domänen.

    Organisationer som kräver Multi-Factor authentication med synkronisering av lösenordshash måste använda multifaktorautentisering i Azure AD. Dessa organisationer kan inte använda från tredje part eller den lokala Multi-Factor authentication-metoderna.

* **Affärskontinuitet**. Med molnautentisering synkronisering av lösenordshash har hög tillgänglighet som en molntjänst som kan skalas till alla Microsoft-datacenter. Distribuera en andra Azure AD Connect-server i mellanlagringsläge i en standby-konfiguration för att säkerställa synkronisering av lösenordshash inte går under långa perioder.

* **Överväganden**. För närvarande tillämpa inte synkronisering av lösenordshash omedelbart ändringar i lokala konto tillstånd. I så fall kan har en användare åtkomst till molnappar tills kontot användartillstånd synkroniseras till Azure AD. Organisationer kan vilja kringgå den här begränsningen genom att köra en ny synkroniseringscykel när administratörer massuppdateringar till en lokal användare konto tillstånd. Ett exempel inaktivera konton.

> [!NOTE]
> Lösenordet har upphört att gälla och kontot utelåst tillstånd för närvarande inte är synkroniserad med Azure AD med Azure AD Connect. 

Referera till [implementera synkronisering av lösenordshash](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) distributionsanvisningar.

### <a name="cloud-authentication-pass-through-authentication"></a>Autentisering i molnet: Direktautentisering  

* **Arbete**. För direktautentisering, behöver du en eller flera (Vi rekommenderar tre) lightweight agenter som installerats på befintliga servrar. Dessa agenter måste ha åtkomst till din lokala Active Directory Domain Services, inklusive din lokala AD-domänkontrollanter. Behöver de utgående åtkomst till Internet och åtkomst till dina domänkontrollanter. Därför är det inte stöd för att distribuera agenter i ett perimeternätverk. 

    Direktautentisering kräver obegränsad nätverksåtkomst till domänkontrollanter. All nätverkstrafik krypteras och begränsat till autentiseringsbegäranden. Mer information om den här processen finns i den [djupgående om säkerhet](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-security-deep-dive) på direktautentisering.

* **Användarupplevelsen**. För att förbättra användarnas inloggning ska distribuera sömlös SSO med direktautentisering. Sömlös SSO eliminerar onödiga anvisningarna när användare loggar in.

* **Avancerade scenarier**. Direktautentisering tillämpar lokala kontoprinciper vid tidpunkten för inloggningen. Exempel: åtkomst nekas när den lokala användarens tillstånd är inaktiverad låst konto, eller [lösenord har upphört att gälla](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) eller hamnar utanför timmar när användaren tillåts att logga in. 

    Organisationer som kräver Multi-Factor authentication med direktautentisering måste använda Azure Multi-Factor Authentication (MFA). Dessa organisationer kan inte använda en från tredje part eller den lokala Multi-Factor authentication-metod. Avancerade funktioner kräver att synkronisering av lösenordshash distribueras, oavsett om du väljer direktautentisering. Ett exempel är rapporten läckta autentiseringsuppgifter för Identity Protection.

* **Affärskontinuitet**. Vi rekommenderar att du distribuerar två extra direktautentisering agenter. Dessa tillägg måste vara uppfyllda utöver första agenten på Azure AD Connect-servern. Den här ytterligare distributionen säkerställer hög tillgänglighet för begäranden om autentisering. När du har tre agenter som har distribuerats, kan fortfarande en agent misslyckas om en annan agent är nere för underhåll. 

    Det finns en annan fördel att distribuera synkronisering av lösenordshash förutom direktautentisering. Den fungerar som en metod för säkerhetskopiering autentisering när den primära autentiseringsmetoden är inte längre tillgänglig.

* **Överväganden**. Du kan använda synkronisering av lösenordshash som en säkerhetskopiering autentiseringsmetod för direktautentisering, när agenterna inte kan verifiera en användares autentiseringsuppgifter på grund av en betydande lokalt fel skulle uppstå. Redundans till synkronisering av lösenordshash sker inte automatiskt och du måste använda Azure AD Connect för att växla metoden inloggning manuellt. 

    Stöd för andra överväganden vid direktautentisering, inklusive alternativ-ID finns i [vanliga frågor och svar](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq).

Referera till [implementera direktautentisering](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) distributionsanvisningar.

### <a name="federated-authentication"></a>Federerad autentisering

* **Arbete**. Ett system för federerad autentisering är beroende av ett externt system som är betrodda att autentisera användare. Vissa företag vill använda sina befintliga federerade system-investering med sina Azure AD-hybrididentitetslösning. Underhåll och hantering av externa system hamnar utanför kontrollen av Azure AD. Det är upp till organisationen med hjälp av det externa systemet så att den har distribuerats på ett säkert sätt och kan hantera belastningen för autentisering. 

* **Användarupplevelsen**. Användarupplevelsen för federerad autentisering är beroende av implementeringen av funktionerna, topologi och konfiguration av federation-servergruppen. Vissa organisationer måste den här flexibiliteten att anpassa och konfigurera åtkomst till federation-grupp som passar deras säkerhetskrav. Det är exempelvis möjligt att konfigurera internt anslutna användare och enheter för att logga in användare automatiskt, utan att de behöver ange autentiseringsuppgifter. Den här konfigurationen fungerar eftersom de redan har loggat in till sina enheter. Om det behövs kan försvåra vissa avancerade säkerhetsfunktioner användarnas inloggningsprocess.

* **Avancerade scenarier**. En lösning för federerad autentisering krävs vanligtvis när kunder har ett krav för autentisering som Azure AD inte stöder internt. Se detaljerad information som hjälper dig att [väljer rätt inloggningsalternativ](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Beakta följande vanliga krav:

  * Autentisering som kräver smartkort eller certifikat.
  * Lokala MFA-servrar eller tredje parts multifaktor-leverantörer.
  * Autentisering med hjälp av autentiseringsmetoder från tredje part lösningar. Se den [kompatibilitetslistan för Azure AD-federation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-compatibility).
  * Logga in som kräver en sAMAccountName, till exempel domän\användarnamn, i stället för en UPN User Principal Name (), till exempel user@domain.com.

* **Affärskontinuitet**. Federerad system kräver normalt en Utjämning av nätverksbelastning-matris av servrar, kallas även en servergrupp. Den här gruppen har konfigurerats i ett internt nätverk och perimeternätverket Nätverkstopologi för att säkerställa hög tillgänglighet för begäranden om autentisering.

    Distribuera synkronisering av lösenordshash tillsammans med federerad autentisering som en metod för säkerhetskopiering autentisering när den primära autentiseringsmetoden är inte längre tillgänglig. Ett exempel är när de lokala servrarna är inte tillgängliga. Vissa stora företag kräver en federationslösning som stöd för flera Internet ingående punkter som konfigurerats med geo-DNS för låg latens autentiseringsbegäranden.

* **Överväganden**. Federerad system kräver normalt mer stora investeringar i infrastruktur på plats. De flesta organisationer väljer det här alternativet om de redan har en lokal federation-investering. Och om det är en stark affärsmässiga skäl att använda en enda identitet-provider. Federation är mer komplex att driva och felsöka jämfört med molnlösningar för autentisering.

För en nonroutable-domän som inte kan verifieras i Azure AD, behöver du ytterligare konfiguration att implementera användarinloggning ID i. Det här kravet kallas alternativ inloggning ID-stöd. Se [konfigurera alternativa inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) begränsningar och krav. Om du väljer att använda en tredje parts Multi-Factor authentication-provider med federation, kontrollerar du att providern har stöd för WS-Trust för att tillåta enheter att ansluta till Azure AD.

Referera till [distribuera federationsservrar](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) distributionsanvisningar.

> [!NOTE] 
> När du distribuerar din Azure AD-hybrididentitetslösning, måste du implementera en av topologier som stöds av Azure AD Connect. Läs mer om stöds och som inte stöds sammansättningar vid [topologier för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies).

## <a name="architecture-diagrams"></a>Arkitekturdiagram

Följande diagram visas de övergripande arkitektur komponenter som krävs för varje autentiseringsmetod som du kan använda med din Azure AD-hybrididentitetslösning. De ger en översikt om hur du jämför lösningarna.

* Enkelheten med en lösning för lösenord hash-synkronisering:

    ![Azure AD-hybrididentitet med synkronisering av lösenordshash](media/azure-ad/azure-ad-authn-image2.png)

* Krav för direktautentisering, med två agenter för redundans:

    ![Azure AD-hybrididentitet med direktautentisering](media/azure-ad/azure-ad-authn-image3.png)

* Komponenter som krävs för federation i perimeternätverket och interna nätverket på din organisation:

    ![Azure AD-hybrididentitet med federerad autentisering](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Jämföra metoder

|Beräkningen|Synkronisering av lösenordshash + sömlös SSO|Direktautentisering + sömlös SSO|Federation med AD FS|
|:-----|:-----|:-----|:-----|
|Var sker autentisering?|I molnet|I molnet när du har ett säkert lösenord verifiering exchange med lokala autentiseringsagent|Lokal|
|Vad är en lokal server-krav utöver etablering systemet: Azure AD Connect?|Ingen|En server för varje ytterligare autentisering-agent|Två eller flera AD FS-servrar<br><br>Två eller flera WAP-servrar i perimeternätverket/DMZ-nätverk|
|Vilka är kraven för den lokala Internet och nätverk utöver etablering systemet?|Ingen|[Utgående Internetåtkomst](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) från servrarna som kör autentiseringsagenter|[Inkommande Internetåtkomst](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) till WAP-servrar i perimeternätverket<br><br>Inkommande åtkomst till AD FS-servrar från WAP-servrar i perimeternätverket<br><br>Utjämning av nätverksbelastning|
|Finns det ett krav för SSL-certifikat?|Nej|Nej|Ja|
|Finns det en lösning för hälsoövervakning?|Krävs inte|Agentstatus som tillhandahålls av [Azure Active Directory Administrationscenter](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|Användarna får enkel inloggning till molnresurser från domänanslutna enheter i företagets nätverk?|Ja med [sömlös SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Ja med [sömlös SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Ja|
|Vilka typer av inloggning stöds?|UserPrincipalName + lösenord<br><br>Windows-integrerad autentisering med hjälp av [sömlös SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternativa inloggnings-ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)|UserPrincipalName + lösenord<br><br>Windows-integrerad autentisering med hjälp av [sömlös SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternativa inloggnings-ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq)|UserPrincipalName + lösenord<br><br>sAMAccountName + lösenord<br><br>Windows-integrerad autentisering<br><br>[Certifikat och smartkort-autentisering](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativa inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Är Windows Hello för företag som stöds?|[Förtroendemodell med nyckel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Förtroendemodell med nyckel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Kräver Windows Server 2016 domänens funktionsnivå*|[Förtroendemodell med nyckel](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Förtroendemodell med certifikat](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Vad är Multi-Factor authentication-alternativ?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Anpassade kontroller med villkorlig åtkomst *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Anpassade kontroller med villkorlig åtkomst *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA-servern](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[Tredje parts MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Anpassade kontroller med villkorlig åtkomst *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)|
|Vilka användare konto tillstånd stöds?|Inaktiverade konton<br>(upp till 30-minuters fördröjning)|Inaktiverade konton<br><br>Låst konto<br><br>Kontot har gått ut<br><br>Lösenordet har gått ut<br><br>Logga in timmar|Inaktiverade konton<br><br>Låst konto<br><br>Kontot har gått ut<br><br>Lösenordet har gått ut<br><br>Logga in timmar|
|Vad är alternativ för villkorlig åtkomst?|[Azure AD villkorlig åtkomst, med Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD villkorlig åtkomst, med Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD villkorlig åtkomst, med Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[ADFS-anspråksregler](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blockerar äldre protokoll som stöds?|[Ja](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)|[Ja](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)|[Ja](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Kan du anpassa logotyp, bild och beskrivning på sidan logga in?|[Ja, med Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Ja, med Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Ja](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Vilka avancerade scenarier stöds?|[Smart lösenordsutelåsning](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Läcka ut autentiseringsuppgifter rapporter med Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Smart lösenordsutelåsning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Flera webbplatser med låg latens autentiseringssystem<br><br>[AD FS extranätsutelåsning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrering med tredje parts identitetssystem](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

> [!NOTE] 
> Anpassade kontroller i Azure AD villkorsstyrd åtkomst stöder för närvarande inte registrera enheten.

## <a name="recommendations"></a>Rekommendationer
Identitetssystemet säkerställer användarnas åtkomst till molnappar och line-of-business-appar som du migrerar och göra tillgängliga i molnet. Om du vill behålla behöriga användare är produktiva och illvilliga aktörer utanför din organisations känsliga data, autentisering kontrollerar åtkomsten till appar.

Använd eller aktivera lösenordshashsynkronisering oavsett vilken autentiseringsmetod som du väljer, av följande skäl:

1. **Hög tillgänglighet och katastrofåterställning recovery**. Direktautentisering och federation är beroende av den lokala infrastrukturen. För direktautentisering, lokala avtryck innehåller för servermaskinvara och nätverk på direktautentisering agenter kräver. För federation är den lokala avtryck ännu större. Det krävs servrar i ditt perimeternätverk på autentiseringsbegäranden för proxy och interna federationsservrar. 

    Distribuera redundanta servrar för att undvika felkritiska fel. Sedan kommer autentiseringsförfrågningar alltid betjänas om någon av komponenterna inte. Både direktautentisering och federation också beroende av domänkontrollanter för att svara på begäranden om autentisering, som också kan misslyckas. Många av dessa komponenter måste Underhåll vara felfritt. Avbrott är mer troligt när Underhåll inte planeras och implementeras på rätt sätt. Undvika avbrott genom att använda synkronisering av lösenordshash eftersom Microsoft Azure AD-autentisering Molntjänsten kan skalas globalt och alltid är tillgänglig.

2. **Lokala avbrott överlevnad**.  Konsekvenserna av en lokal strömavbrott på grund av ett cyberattackkedjan eller en katastrof kan vara betydande sträcker sig från reputational varumärke skada en paralyzed organisation som behöver bry dig om attacken. Många organisationer har nyligen offer för angrepp av skadlig kod, inklusive riktad utpressningstrojaner som orsakade sina lokala servrar att gå nedåt. När Microsoft hjälper kunder att hantera dessa typer av attacker, visas två typer av organisationer:

   * Organisationer som tidigare slagits på synkronisering av lösenordshash ändra autentiseringsmetod för att använda synkronisering av lösenordshash. De var online igen på bara några timmar. Med åtkomst till e-post via Office 365 kan arbetat de för att lösa problem och få åtkomst till andra molnbaserade arbetsbelastningar.

   * Organisationer som inte tidigare aktivera lösenordshashsynkronisering var tvungen att använda ej betrodda externa e-system för kommunikation att lösa problem. I sådana fall kan tog det dem veckor att återställa den lokala identitetsinfrastrukturen, innan användarna kunde logga in på molnbaserade appar igen.

3. **Identitetsskydd**. En av de bästa sätten att skydda användare i molnet är Azure AD Identity Protection med Azure AD Premium P2. Microsoft söker igenom kontinuerligt Internet för användare och lösenord visar att skadliga aktörer sälja och tillgängliggöra för mörka Internet. Azure AD kan använda den här informationen för att kontrollera om någon av de användarnamn och lösenord i din organisation har komprometterats. Det är därför viktigt att aktivera synkronisering av lösenordshash oavsett vilken autentiseringsmetod som du använder, om som är federerat eller direktautentisering. Läckta autentiseringsuppgifter kan visas som en rapport. Använd den här informationen för att blockera eller tvinga användare att ändra sina lösenord när de försöker logga in med läckta lösenord.

Till sist enligt till [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft har den mest kompletta uppsättningen identitets- och hanteringsfunktioner. Microsoft hanterar [450 miljarder autentiseringsbegäranden](https://www.microsoft.com/en-us/security/intelligence-report) varje månad för att ge åtkomst till tusentals SaaS-program som Office 365 från vilken enhet som helst. 

## <a name="conclusion"></a>Sammanfattning

Den här artikeln beskrivs olika autentiseringsalternativ som organisationer kan konfigurera och distribuera för att stödja åtkomst till molnappar. Organisationer kan välja mellan hashsynkronisering för lösenord, direktautentisering och federation för att uppfylla olika företag, säkerhet och tekniska krav. 

Överväg att varje autentiseringsmetod. Gör det enklare att distribuera lösningen och användarens upplevelse av att logga in, åtgärda dina affärsbehov? Utvärdera om din organisation behöver avancerade scenarier och funktioner för affärskontinuitet för varje autentiseringsmetod. Slutligen kan utvärdera överväganden för varje autentiseringsmetod. Något av dem hindrar dig från att implementera valfri?

## <a name="next-steps"></a>Nästa steg

I dagens värld hot är tillgänglig dygnet runt och kommer från var som helst. Implementera rätt autentiseringsmetod och det minska din säkerhetsrisker och skydda dina identiteter.

[Kom igång](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) med Azure AD och distribuera rätt autentiseringslösning för din organisation.

Om du tänker om hur du migrerar från federerad autentisering i molnet, Lär dig mer om [ändrar inloggningsmetoden](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). För att planera och implementera migreringen kan använda [dessa distribution projektplaner](https://aka.ms/deploymentplans).
