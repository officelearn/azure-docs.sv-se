---
title: Välja rätt autentiseringsmetod för din Azure AD-hybrididentitetslösning | Microsoft Docs
description: Den här guiden hjälper koncernchefer, IT-chefer, CISOs, chefen identitet arkitekter, Enterprise-arkitekter och säkerhet och IT-beslutsfattare ansvarar för att välja en autentiseringsmetod för sina Azure AD-hybrididentitetslösning i medelstora och stora organisationer.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 7e2eb9bad8c95e61d5b750cacd0724fc2536f2c5
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294117"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning 

Den här artikeln börjar en serie artiklar som hjälper företag att implementera en komplett hybrididentitetslösning för Azure Active Directory (AD Azure). Den här lösningen har beskrivs som den [Hybrid Identity digitala Transformation Framework](https://aka.ms/aadframework). Det täcker affärsresultatet och mål organisationer kan fokusera på att implementera en robust och säker hybrididentitetslösning. 

Första business resultatet av framework anger kraven för organisationer att skydda autentiseringsprocessen när användare har åtkomst till molnappar. Första verksamhetsmål i autentisering skyddad business resultatet är användarnas möjlighet att logga in på molnappar med hjälp av lokalt användarnamn och lösenord. Den här processen att och hur användare autentiseras gör allt i molnet möjligt.

Att välja rätt autentiseringsmetod är första prioritet för organisationer som vill flytta sina appar till molnet. Inte ta beslutet lätt, av följande skäl:

1. Det är det första beslutet för en organisation som vill flytta till molnet. 

2. Autentiseringsmetoden är en kritisk komponent i en organisation finns i molnet. Den styr åtkomsten till alla molndata och resurser.

3. Det är grunden för alla andra avancerad säkerhet och användarens upplevelse funktioner i Azure AD.

4. Autentiseringsmetoden är svårt att ändra när den har implementerats.

Identiteten är det nya kontrollplanet för IT-säkerhet. Autentisering är därför en organisations åtkomst skydd för alla nya molntjänster. Organisationer behöver en identitet kontrollplan som stärker säkerhet och behåller sin molnappar från obehöriga användare.

### <a name="out-of-scope"></a>Utanför omfånget
Organisationer som inte har en befintlig lokal katalog storleken är inte fokus i den här artikeln. Normalt skapar dessa företag identiteter i molnet, som inte kräver en hybrididentitetslösning. Endast molnbaserad identiteter finns enbart i molnet och är inte kopplade till motsvarande lokala identiteter.

## <a name="authentication-methods"></a>Autentiseringsmetoder
När Azure AD-hybrididentitetslösning är din nya plan för åtkomstkontroll, är autentisering grunden för molnåtkomst. Att välja rätt autentiseringsmetod är ett viktigt första beslut du ställer in en hybrididentitetslösning i Azure AD. Implementera den autentiseringsmetod som konfigureras med hjälp av Azure AD Connect, som också etablerar användare i molnet.

Om du vill välja en autentiseringsmetod som du behöver tänka tid, befintlig infrastruktur, komplexiteten och kostnaden för att genomföra valet. Dessa faktorer är olika för varje organisation och kan ändras med tiden. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD stöder följande autentiseringsmetoder för hybrididentitetslösningar.

### <a name="cloud-authentication"></a>Autentisering i molnet
När du väljer den här autentiseringsmetoden hanterar användarnas inloggningsprocessen i Azure AD. Tillsammans med sömlös enkel inloggning (SSO), kan användarna logga in på molnappar utan att behöva ange sina autentiseringsuppgifter. Med autentisering i molnet, kan du välja mellan två alternativ: 

**Azure AD-lösenord hash-synkronisering**. Det enklaste sättet att aktivera autentisering för lokala katalogobjekt i Azure AD. Användarna kan använda samma användarnamn och lösenord som de använder lokalt utan att behöva distribuera någon ytterligare infrastruktur. Vissa premiumfunktioner Azure AD som Identity Protection kräver synkronisering av lösenords-hash för oavsett vilken autentiseringsmetod du väljer.

> [!NOTE] 
> Lösenordet lagras i klartext aldrig eller krypterats med en algoritm för omvändbar i Azure AD. Mer information om själva för synkronisering av lösenords-hash finns [implementera Lösenordssynkronisering med Azure AD Connect-synkronisering med hash-](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Azure AD-direkt autentisering**. Tillhandahåller en enkel lösenordsverifieringen för Azure AD authentication services med hjälp av en agent för program som körs på en eller flera lokala servrar. Servrarna Validera användare direkt med lokala Active Directory, vilket garanterar att valideringen av lösenordet inte sker i molnet. 

Företag med ett säkerhetsvillkor att omedelbart tillämpa lokala användare kontot tillstånd, principer för lösenord, och logga in timmar kan använda den här autentiseringsmetoden. Mer information om de faktiska direktautentisering finns [användare logga in med Azure AD-direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Federerad autentisering
När du väljer den här autentiseringsmetoden av Azure AD händer autentiseringsprocessen till ett system med separata betrodda autentisering, till exempel lokala Active Directory Federation Services (AD FS) för att verifiera användarens lösenord.

Autentisering-system kan ge ytterligare avancerade autentiseringskrav. Exempel är smartkortbaserad autentisering eller multifaktorautentisering från tredje part. Mer information finns i [distribuera Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Följande avsnitt hjälper dig att bestämma vilken autentiseringsmetod som passar dig med hjälp av en beslutsträdet. Det hjälper dig att avgöra om du vill distribuera moln eller federerad autentisering för din Azure AD-hybrididentitetslösning.

## <a name="decision-tree"></a>Beslutsträd

![Azure AD authentication beslutsträdet](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Detaljerad information

### <a name="cloud-authentication-password-hash-synchronization"></a>Autentisering i molnet: synkronisering av lösenords-hash

* **Arbete**. Synkronisering av lösenords-hash kräver minst arbetskrävande om distribution, underhåll och infrastruktur.  Den här nivån av arbete gäller vanligtvis för organisationer som behöver bara användarna att logga in på Office 365, SaaS-appar och andra Azure AD-baserade resurser. När aktiverat synkronisering av lösenords-hash är en del av Azure AD Connect-synkroniseringen och körs varannan minut.

* **Användarupplevelsen**. Distribuera sömlös SSO med synkronisering av lösenords-hash för att förbättra användarnas inloggning. Sömlös SSO eliminerar onödiga frågor när användare loggar in.

* **Avancerade scenarier**. Om organisationer välja, är det möjligt att använda insikter från identiteter med Azure AD Identity Protection rapporter. Ett exempel är som rapporten läckta autentiseringsuppgifter. Windows Hello för företag är ett annat alternativ som har [specifika krav när du använder synkronisering av lösenords-hash-](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    Organisationer som kräver flerfunktionsautentisering med synkronisering av lösenords-hash måste använda multifaktorautentisering i Azure AD. Dessa organisationer kan inte använda från tredje part eller lokalt flerfunktionsautentisering metoder.

* **Kontinuitet för företag**. Synkronisering av lösenords-hash med autentisering i molnet har hög tillgänglighet som en molntjänst som kan skalas till alla Microsoft-datacenter. Distribuera en andra server som Azure AD Connect i mellanlagringsläge i standby-konfiguration för att kontrollera hash-synkronisering av lösenord inte går under långa perioder.

* **Överväganden**. För närvarande tillämpa inte synkronisering av lösenords-hash omedelbart ändringar i ett lokalt konto tillstånd. I så fall kan har en användare åtkomst till molnappar tills användartillstånd för kontot har synkroniserats till Azure AD. Organisationer kan vilja kringgå den här begränsningen genom att köra en ny synkroniseringscykel när administratörer massuppdateringar till lokala användare konto tillstånd. Ett exempel inaktivera konton.

> [!NOTE]
> Lösenordet har upphört att gälla och kontot utelåst tillstånd för närvarande synkroniseras inte till Azure AD med Azure AD Connect. 

Referera till [implementera Lösenordssynkronisering hash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) distributionssteg.

### <a name="cloud-authentication-pass-through-authentication"></a>Autentisering i molnet: direkt-autentisering  

* **Arbete**. För direktautentisering, behöver du en eller flera (Vi rekommenderar tre) lightweight agenter som installerats på befintliga servrar. Dessa agenter måste ha åtkomst till din lokala Active Directory Domain Services, inklusive dina lokala AD-domänkontrollanter. Behöver de utgående åtkomst till Internet och åtkomst till dina domänkontrollanter. Därför är det inte stöd för att distribuera agenter i ett perimeternätverk. 

    Direkt-autentisering kräver obegränsad nätverksåtkomst till domänkontrollanter. All nätverkstrafik krypteras och begränsat till autentiseringsbegäranden. Mer information om den här processen finns på [säkerhet ingående](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) på direktautentisering.

* **Användarupplevelsen**. Distribuera sömlös SSO med direkt autentisering för att förbättra användarnas inloggning. Sömlös SSO eliminerar onödiga frågor när användare loggar in.

* **Avancerade scenarier**. Direkt-autentisering tvingar kontoprincip lokala vid tidpunkten för inloggning. T.ex, åtkomst nekas när ett lokalt användarkonto tillstånd är inaktiverad utelåst eller [lösenordet gått](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) eller hamnar utanför timmar när användaren tillåts att logga in. 

    Organisationer som kräver flerfunktionsautentisering med direktautentisering måste använda Azure Multi-Factor Authentication (MFA). Dessa organisationer kan inte använda en tredjeparts- eller lokala flerfunktionsautentisering-metod. Avancerade funktioner kräver att synkronisering av lösenords-hash distribueras oavsett om du väljer direktautentisering. Ett exempel är läckta autentiseringsuppgifter rapport över Identity Protection.

* **Kontinuitet för företag**. Vi rekommenderar att du distribuerar två extra direktautentisering agenter. Dessa tillägg är förutom första agenten på Azure AD Connect-servern. Ytterligare distributionen säkerställer hög tillgänglighet av autentiseringsbegäranden. När du har tre agenter distribueras, kan en agent fortfarande misslyckas om en annan agent är nere för underhåll. 

    Det finns en annan fördel att distribuera hash Lösenordssynkronisering förutom direktautentisering. Det fungerar som en metod för säkerhetskopiering autentisering när den primära autentiseringsmetoden är inte längre tillgänglig.

* **Överväganden**. Du kan använda synkronisering av lösenords-hash som en säkerhetskopiering autentiseringsmetod för direktautentisering och agenterna kan inte verifiera en användares autentiseringsuppgifter. Sedan sker redundans till hash-synkronisering av lösenord inte inte automatiskt. Växla metoden inloggning manuellt med hjälp av Azure AD Connect. 

    Direkt-autentisering har endast stöd för molnappar som använder modern autentisering och vissa Exchange Online-protokoll. Vissa protokoll är ActiveSync-, POP3- och IMAP4. Till exempel Microsoft Office 2013 och senare stöder modern autentisering, men tidigare versioner inte. Mer information om stöd för Office-app finns [uppdateras Office 365 modern autentisering](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Stöd för andra överväganden direkt-autentisering, inklusive Alternativt ID finns [vanliga frågor och](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Referera till [implementera direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) distributionssteg.

### <a name="federated-authentication"></a>Federerad autentisering

* **Arbete**. Ett system för federerad autentisering är beroende av ett externt system som är betrodda att autentisera användare. Vissa företag vill återanvända befintliga investeringar med sina Azure AD-hybrididentitetslösning externa system. Underhåll och hantering av externa system hamnar utanför kontrollen av Azure AD. Det är upp till organisationen med hjälp av externa system och kontrollera att den har distribuerats på ett säkert sätt och kan hantera belastningen för autentisering. 

* **Användarupplevelsen**. Användarupplevelsen för federerad autentisering beror på implementering av funktioner, topologi och konfiguration av federation-servergruppen. Vissa organisationer måste den här flexibiliteten att anpassa och konfigurera åtkomst till federation-grupp som passar deras säkerhetskrav. Det är exempelvis möjligt att konfigurera internt anslutna användare och enheter för att logga in användare automatiskt utan att de behöver ange autentiseringsuppgifter. Den här konfigurationen fungerar eftersom de redan har loggat in till sina enheter. Om det behövs försvåra några funktioner för avancerad säkerhet användarnas inloggningsprocessen.

* **Avancerade scenarier**. En lösning för federerad autentisering krävs vanligtvis när kunder har en autentiseringskravet som Azure AD har inte stöd för internt. Visa detaljerad information som hjälper dig att [välja rätt inloggningsalternativ](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Överväg följande vanliga krav:

    * Autentiseringsmetod som kräver smartkort eller certifikat.
    * Lokala MFA servrar eller från tredje part multifaktor-leverantörer.
    * Autentisering med hjälp av lösningar från tredje part. Finns det [kompatibilitetslista för Azure AD-federation](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Logga in som kräver en sAMAccountName, t.ex, i stället för en Principal Name (User Principal Name), till exempel user@domain.com.

* **Kontinuitet för företag**. Externa system kräver vanligtvis en belastningsutjämnad matris av servrar, kallas även en grupp. Den här gruppen har konfigurerats i ett internt nätverk och topologi för perimeternätverk att säkerställa hög tillgänglighet för autentiseringsbegäranden.

    Distribuera hash Lösenordssynkronisering tillsammans med federerad autentisering som en metod för säkerhetskopiering autentisering när den primära autentiseringsmetoden är inte längre tillgänglig. Ett exempel är när de lokala servrarna är inte tillgängliga. Vissa stora företag behöver en federation-lösning för att stödja flera Internet ingång punkter som konfigurerats med geo-DNS för låg fördröjning autentiseringsbegäranden.

* **Överväganden**. Externa system kräver vanligtvis en mer betydande investering i lokal infrastruktur. De flesta organisationer välja det här alternativet om de redan har en lokal federation investering. Och om det är en starkt affärsmässiga skäl att använda en enda identitetsleverantör. Federation är mer komplex att driva och felsöka jämfört med autentisering av molnlösningar.

För en nonroutable-domän som inte kan verifieras i Azure AD, behöver du extra konfiguration för att implementera användar-ID logga in. Det här kravet kallas alternativt inloggnings ID support. Se [konfigurera alternativa inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) begränsningar och krav. Om du väljer att använda en tredje parts flerfunktionsautentiseringsleverantör med federation, se till att providern har stöd för WS-Trust om du vill att enheter ska kunna ansluta till Azure AD.

Referera till [distribuera federationsservrar](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) distributionssteg.

> [!NOTE] 
> När du distribuerar din Azure AD-hybrididentitetslösning måste du implementera ett av topologier som stöds av Azure AD Connect. Lär dig mer om stöds och som inte stöds konfigurationer på [topologier för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Nätverksdiagram

Följande diagram visar en översikt på hög nivå arkitektur-komponenter som krävs för varje autentiseringsmetod som du kan använda med din Azure AD-hybrididentitetslösning. De ger en översikt för att hjälpa dig att jämföra skillnaderna mellan lösningarna.

* Enkelhet av en lösning för lösenord hash-synkronisering:

    ![Azure AD-hybrididentitet med synkronisering av lösenords-hash](media/azure-ad/azure-ad-authn-image2.png)

* Krav för direktautentisering:

    ![Azure AD-hybrididentitet med direkt-autentisering](media/azure-ad/azure-ad-authn-image3.png)

* Komponenter som krävs för federering i ditt perimeternätverk och det interna nätverket på din organisation:

    ![Azure AD-hybrididentitet med federerad autentisering](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Jämförelse av metoder

|Beräkningen|Synkronisering av lösenords-hash + sömlös SSO|Direkt-autentisering + sömlös SSO|Federation med AD FS|
|:-----|:-----|:-----|:-----|
|Där sker autentisering?|I molnet|I molnet efter ett säkert lösenord verifiering exchange med lokal autentiseringsagent|Lokal|
|Vilka är kraven på lokal server utöver etablering systemet: Azure AD Connect?|Ingen|En server för varje ytterligare autentisering-agent|Två eller flera AD FS-servrar<br><br>Två eller flera WAP-servrar i perimeternätverket/DMZ-nätverk|
|Vilka är kraven för lokal Internet och nätverk utöver etablering systemet?|Ingen|[Utgående Internetåtkomst](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) från servrar som kör autentisering agenter|[Inkommande Internetåtkomst](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) till WAP-servrar i perimeternätverket<br><br>Inkommande nätverksåtkomst till AD FS-servrar från WAP-servrar i perimeternätverket<br><br>Utjämning av nätverksbelastning|
|Finns det ett krav för SSL-certifikat?|Nej|Nej|Ja|
|Finns det en lösning för hälsoövervakning?|Krävs inte|Agentstatus som tillhandahålls av [Azure Active Directory Administrationscenter](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Användare får enkel inloggning till molnresurser från domänanslutna enheter i företagets nätverk?|Ja med [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ja med [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ja|
|Vilka typer av inloggning stöds?|UserPrincipalName + lösenord<br><br>Windows-integrerad autentisering med hjälp av [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Alternativt inloggnings-ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + lösenord<br><br>Windows-integrerad autentisering med hjälp av [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Alternativt inloggnings-ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + lösenord<br><br>sAMAccountName + lösenord<br><br>Windows-integrerad autentisering<br><br>[Certifikat och smartkort-autentisering](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativt inloggnings-ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Är Windows Hello för företag som stöds?|[Förtroendemodell med nyckel](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Certifikatet förtroendemodell med Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Förtroendemodell med nyckel](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Certifikatet förtroendemodell med Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Förtroendemodell med nyckel](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Förtroendemodell med certifikat](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Vad är alternativen för flerfunktionsautentisering?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br><br>[Azure MFA-server](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[Tredjeparts-MFA](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Vilka användare konto tillstånd stöds?|Inaktiva konton<br>(upp till 30 minuter fördröjning)|Inaktiva konton<br><br>Låst konto<br><br>Lösenordet har gått ut<br><br>Logga in timmar|Inaktiva konton<br><br>Låst konto<br><br>Lösenordet har gått ut<br><br>Logga in timmar|
|Vad är alternativ för villkorlig åtkomst?|[Azure AD villkorlig åtkomst](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD villkorlig åtkomst](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD villkorlig åtkomst](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Anspråksregler i AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blockerar äldre protokoll som stöds?|[Ja](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Ja](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Ja](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Kan du anpassa logotyp, bild och beskrivning på sidan logga in?|[Ja, med Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ja, med Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ja](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Vilka avancerade scenarier stöds?|[Smart lösenord kontoutelåsning](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br><br>[Läcka ut autentiseringsuppgifter rapporter](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Smart lösenord kontoutelåsning](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Multisite autentiseringssystemet låg latens<br><br>[AD FS extranät-låsning](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integrering med tredje parts identitetssystem](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Rekommendationer
Din identitet säkerställer användarnas åtkomst till molnappar och av branschspecifika appar som du migrerar och göra den tillgänglig i molnet. Om du vill behålla auktoriserade användare produktiva och obehöriga utanför din organisation känsliga data, autentisering styr åtkomsten till appar.

Använd eller aktivera Lösenordssynkronisering hash för oavsett vilken autentiseringsmetod som du väljer, av följande skäl:

1. **Hög tillgänglighet och disaster recovery**. Direkt-autentisering och federation förlitar sig på lokal infrastruktur. Storleken på lokala innehåller servermaskinvaran för direktautentisering, och nätverk direkt-autentisering kräver agenter. För federation är lokala storleken ännu större. Det krävs servrar i perimeternätverket att proxy autentiseringsbegäranden och interna federationsservrar. 

    Distribuera redundant servrar för att undvika enskilda felpunkter på fel. Sedan kommer autentiseringsförfrågningar alltid betjänas om någon komponent misslyckas. Både direktautentisering och federation också beroende av domänkontrollanter för att besvara autentiseringsbegäranden, som också kan misslyckas. Många av dessa komponenter måste Underhåll ska vara felfritt. Avbrott sannolikt när Underhåll inte är planerad och implementerats korrekt. Undvika avbrott genom att använda Lösenordssynkronisering för hash-eftersom tjänsten Microsoft Azure AD cloud autentisering skalas globalt och alltid är tillgängligt.

2. **Lokala avbrott överlevnad**.  Konsekvenserna av ett lokalt avbrott på grund av en attack cyber eller katastrof kan vara betydande mellan reputational varumärken skada en paralyzed organisation som det går inte att åtgärda angreppet. Många organisationer har nyligen offer för angrepp från skadlig kod, inklusive riktade är en utpressningstrojan som, som orsakade sina lokala servrar gå nedåt. När Microsoft hjälper kunder att hantera dessa typer av attacker, ser två typer av organisationer:

   * Organisationer som tidigare har aktiverat Lösenordssynkronisering för hash-ändra sina autentiseringsmetod för att använda synkronisering av lösenords-hash. De var tillbaka online i några timmar. Med hjälp av åtkomst till e-post via Office 365, arbetat de för att lösa problem och få åtkomst till andra molnbaserade arbetsbelastningar.

   * Organisationer som inte tidigare Aktivera synkronisering av lösenords-hash tvungna att använda ej betrodda externa e-system för kommunikation och lösa problem. I sådana fall kan tog det dem veckor eller mer att vara igång igen.

3. **Identitetsskydd**. Ett bra sätt att skydda användare i molnet är Azure AD Identity Protection. Microsoft genomsöker kontinuerligt Internet för användare och lösenord visar att obehöriga sälja och göra den tillgänglig på webben mörkt. Azure AD kan använda den här informationen för att kontrollera om någon av de användarnamn och lösenord i din organisation till. Det är därför viktigt att aktivera synkronisering av lösenords-hash oavsett vilken autentiseringsmetod som du använder, om som är federerat eller direktautentisering. Läckta autentiseringsuppgifter kan visas som en rapport. Använd informationen för att blockera eller tvinga användare att ändra sina lösenord när de försöker att logga in med läckta lösenord.

Slutligen enligt [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft har mest komplett uppsättning identitets- och hanteringsfunktioner. Microsoft hanterar [450 miljarder autentiseringsbegäranden](https://www.microsoft.com/en-us/security/intelligence-report) varje månad för att ge åtkomst till tusentals SaaS-program som Office 365 från valfri enhet. 

## <a name="conclusion"></a>Sammanfattning

Den här artikeln beskrivs olika autentiseringsalternativ som organisationer kan konfigurera och distribuera för att stödja åtkomst till molnappar. Organisationer kan välja mellan synkronisering av lösenords-hash, direkt-autentisering och federation för att uppfylla olika företag, säkerhet och tekniska krav. 

Överväg att varje autentiseringsmetod. Stöder arbete för att distribuera lösningen och användarupplevelse processens inloggning, hantera dina affärsbehov? Utvärdera om din organisation behöver avancerade scenarier och funktionerna för verksamhetskontinuitet för varje autentiseringsmetod. Slutligen utvärdera överväganden för varje autentiseringsmetod. Något av dem. att du implementera ditt val?

## <a name="next-steps"></a>Nästa steg

I dagens värld hot är tillgänglig dygnet runt och komma från överallt. Implementera rätt autentiseringsmetod och den risker säkerhet och skydda dina identiteter.

[Kom igång](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) med Azure AD och distribuera rätt autentiseringslösning för din organisation.

Om du tänker om hur du migrerar från federerad till molnet autentisering, Lär dig mer om [ändra inloggning metoden](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Använd för att planera och implementera migreringen [dessa projektet distribution planer](http://aka.ms/deploymentplans).
