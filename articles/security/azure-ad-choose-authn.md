---
title: Välja rätt autentiseringsmetod för din Azure AD-hybrididentitetslösning | Microsoft Docs
description: Den här guiden är avsedd för att hjälpa koncernchefer, IT-chefer, CISOs, chefen identitet arkitekter, Enterprise-arkitekter och säkerhet och IT-beslutsfattare ansvarar för att välja en autentiseringsmetod för sina Azure AD-hybrididentitetslösning i medelstora och stora organisationer.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 373efcd821946ab5814ab58284ef7778b779260a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Att välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning 

Den här artikeln är först i en serie med artiklar som hjälper företag att implementera ett komplett hybrididentitetslösning för Azure AD. Ett komplett hybrididentitetslösning för Azure AD har beskrivs som Hybrid Identity digitala Transformation Framework och omfattar affärsresultatet och mål organisationer bör fokusera på att se till att de har implementerat en robusta och säker hybrididentitet lösning. Första business resultatet av framework anger kraven för organisationer att skydda autentiseringsprocessen när användare har åtkomst till molnappar. Första verksamhetsmål i autentisering skyddad business resultatet är möjligheten för användare att logga in på molnappar med hjälp av lokalt användarnamn och lösenord. Den här processen logga in och hur användare autentiseras möjliggör allt i molnet.

Att välja rätt autentiseringsmetod är första prioritet för organisationer som vill flytta sina appar till molnet. Det här beslutet bör noga av följande skäl:

1. Det är det första beslutet för en organisation som vill flytta till molnet. 

2. Autentiseringsmetoden är en kritisk komponent i en organisation finns i molnet, hur du styr åtkomst till alla molndata och resurser.

3. Det är grunden för alla andra avancerad säkerhet och användarens upplevelse funktioner i Azure AD.

4. Svårt att ändra den autentiseringsmetod som genomförs en gång.

Med identiteten som IT-säkerhet nya kontrollplan är autentisering en organisation åtkomst skydd för alla nya molntjänster. Organisationer bör kontrollera identitet kontrollplan gör bättre säkerhet och hålla sina molnappar från obehöriga användare.

### <a name="out-of-scope"></a>Utanför omfånget

Organisationer som inte har en befintlig lokal katalog storleken är inte fokus i den här artikeln. Normalt skapar dessa företag identiteter i molnet, som inte kräver en hybrididentitetslösning. Endast molnbaserad identiteter finns enbart i molnet och är associerade inte med den motsvarande lokala identiteter.  

## <a name="choosing-the-right-authentication-method"></a>Att välja rätt autentiseringsmetod

Med Azure AD hybrididentitetslösning som det nya kontrollplanet är autentisering grunden för molnåtkomst. Att välja rätt autentiseringsmetod är ett viktigt första beslut du ställer in en hybrididentitetslösning i Azure AD. Implementera autentiseringsmetoden som har konfigurerats med Azure AD Connect som också etablera användare i molnet. 

Om du vill välja en autentiseringsmetod som du behöver tänka tid, befintlig infrastruktur, komplexiteten och kostnaden för att genomföra valet. Dessa faktorer är olika för varje organisation och kan ändras med tiden. 

Azure AD stöder följande autentiseringsmetoder för hybrididentitetslösningar:

### <a name="cloud-authentication"></a>Autentisering i molnet
När du väljer den här autentiseringsmetoden hanterar processen inloggning för användare i Azure AD. Tillsammans med sömlös enkel inloggning (SSO), kan användarna logga in på molnappar utan att behöva ange sina autentiseringsuppgifter. Med autentisering i molnet kan du välja mellan två alternativ: 

**Lösenord hash-synkronisering (PHS)** – det enklaste sättet att aktivera autentisering för lokala katalogobjekt i Azure AD. Lösenordets Hash-synkronisering kan du använda samma användarnamn och lösenord som de använder lokalt utan att behöva distribuera någon ytterligare infrastruktur. Vissa premiumfunktioner i Azure AD, till exempel Identity Protection kräver Lösenordshashsynkronisering oavsett vilken autentiseringsmetod har valts.

> [!NOTE] 
> Lösenordet lagras i klartext aldrig eller krypterats med en algoritm för omvändbar i Azure AD. Mer information om själva Lösenordshashsynkronisering finns [implementera Lösenordssynkronisering med Azure AD Connect-synkronisering med hash-](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Direkt-autentisering (Tereftalsyra)** – ger en enkel lösenordsverifieringen för Azure AD authentication services med hjälp av en agent för program som körs på en eller flera lokala servrar för att verifiera användare direkt med din lokala Active Directory säkerställt valideringen av lösenordet inträffa inte i molnet. Företag med ett säkerhetsvillkor att omedelbart tillämpa lokalt användarkonto anger lösenordsprinciper och inloggningstiden använder den här autentiseringsmetoden. Mer information om de faktiska direktautentisering finns [användare logga in med Azure Active Directory direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Federerad autentisering
När du väljer den här autentiseringsmetoden Azure AD händer av autentiseringsprocessen till ett separat betrodda autentisering system, till exempel en lokal Active Directory Federation Services (AD FS) att verifiera användarens lösenord. Autentisering-system kan ge ytterligare autentiseringskrav, t.ex smartkort-baserad autentisering eller multifaktorautentisering från tredje part. Mer information finns i [distribuera Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Följande avsnitt hjälper dig att avgöra vilken autentiseringsmetod som passar dig med hjälp av en beslutsträdet. Det hjälper dig att avgöra om du vill distribuera moln eller federerad autentisering för din Azure AD-hybrididentitetslösning.

## <a name="azure-ad-authentication-decision-tree"></a>Azure AD authentication beslutsträdet

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Detaljerad information om autentiseringsmetoder

### <a name="cloud-authentication-password-hash-sync"></a>Autentisering i molnet: lösenordets Hash-synkronisering 

* **Prestanda:** Lösenordshashsynkronisering kräver minst arbetskrävande om distribution, underhåll och infrastruktur för organisationer som behöver bara aktivera sina användare att logga in på Office 365, SaaS-appar och andra Azure AD-baserade resurser. När aktiverad Lösenordshashsynkronisering är en del av Azure AD Connect-synkroniseringen och körs varannan minut. 

* **Användarupplevelsen:** rekommenderas att organisationerna distribuerar sömlös enkel inloggning (SSO) med Lösenordshashsynkronisering att förbättra användarens inloggningsupplevelse genom att undvika onödiga frågor när de loggat in.

* **Avancerade scenarier:** organisationer välja att det är möjligt att använda insikter från identiteter med Azure AD Identity Protection rapporter, till exempel rapporten läckta autentiseringsuppgifter. Windows Hello för företag är ett annat alternativ som har [specifika krav när du använder Lösenordshashsynkronisering](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). Organisationer som kräver Multi-Factor authentication med Hash-synkronisering måste använda multifaktorautentisering i Azure AD och kan inte använda en tredje part eller lokalt multifaktorautentisering metoder.

* **Kontinuitet för företag:** Lösenordshashsynkronisering är natur hög tillgänglighet som en molntjänst som kan skalas till alla Microsoft-datacenter. Det rekommenderas att en andra Azure AD Connect-server distribueras i mellanlagringsläge i en standby-konfiguration för disaster recovery ändamål.

* **Att tänka på:** Lösenordshashsynkronisering omedelbart tillämpar inte ändringar i ett lokalt konto tillstånd för närvarande. I så fall kan har en användare åtkomst till molnappar tills användaren kontotillstånd är synkroniserad med Azure AD. Organisationer kan kringgå den här begränsningen rekommenderas en ny synkroniseringscykel för har aktiverats när administratörer massuppdateringar till lokala användare konto tillstånd, till exempel inaktivera konton. Kontot utelåst är ett annat konto användartillstånd som synkroniseras i nästa cykel. 

> [!NOTE] 
> Lösenordet har upphört att gälla tillstånd för närvarande inte har synkroniserats till Azure AD med Azure AD Connect. 

Referera till [implementera Lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) distributionssteg.

### <a name="cloud-authentication-pass-through-authentication"></a>Autentisering i molnet: direkt-autentisering  

* **Prestanda:** för direkt-autentisering, behöver du en eller flera (tre rekommenderas) lightweight agenter som installerats på befintliga servrar med åtkomst till din lokala Active Directory Domain Services, inklusive åtkomst till din lokala AD-domänen domänkontrollanter. Dessa agenter behöver utgående åtkomst till Internet och har åtkomst till dina domänkontrollanter. Därför kan stöds den inte för att distribuera agenter i ett perimeternätverk, eftersom den kräver obegränsad nätverksåtkomst till domänkontrollanter. All nätverkstrafik krypteras och begränsat till autentiseringsbegäranden. Mer information om den här processen finns på [säkerhet ingående](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) på direktautentisering.

* **Användarupplevelsen:** rekommenderas att organisationerna distribuerar sömlös enkel inloggning med direktautentisering att förbättra användarens inloggningsupplevelse genom att undvika onödiga frågor när de loggat in.

* **Avancerade scenarier:** direkt-autentisering tvingar omedelbart kontoprincip lokalt. Till exempel nekas åtkomst när en lokal användare kontotillstånd är inaktiverat, låst, lösenord upphört att gälla eller faller utanför användarens tillåtna inloggningstider.  Organisationer som kräver Multi-Factor authentication med direktautentisering måste använda multifaktorautentisering i Azure AD och kan inte använda en tredje part eller lokalt multifaktorautentisering-metod. Avancerade funktioner som rapporten läckta autentiseringsuppgifter för identitetsskydd kräver att Lösenordshashsynkronisering distribueras oavsett om du väljer direktautentisering.

* **Kontinuitet för företag:** vi rekommenderar att du distribuerar två extra direktautentisering agenter, förutom första agenten på Azure AD Connect-servern att säkerställa hög tillgänglighet av autentiseringsbegäranden. När du har tre agenter distribueras, kan en agent fortfarande misslyckas om en annan agent är nere för underhåll. En annan fördel med att distribuera Lösenordshashsynkronisering förutom direkt-autentisering är den kan fungera som säkerhetskopiering autentiseringsmetod när den primära autentiseringsmetoden är inte längre tillgängliga, till exempel om de lokala servrarna inte är tillgängliga.

* **Att tänka på:** om du använder Lösenordshashsynkronisering som en säkerhetskopiering autentiseringsmetod för direktautentisering och agenterna kan inte verifiera användarens autentiseringsuppgifter och sedan redundans till lösenordets Hash-synkronisering sker inte automatiskt. Du skulle behöva byta metod för inloggning manuellt med hjälp av Azure AD Connect. Direkt-autentisering har endast stöd för molnappar som använder modern autentisering och vissa Exchange Online-protokoll som ActiveSync-, POP3- och IMAP4. Till exempel [Microsoft Office 2013 och senare stöder modern autentisering, men inte tidigare versioner](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) på mer information om stöd för Office-app. Se [vanliga frågor och](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) och andra överväganden på direktautentisering, inklusive Alternativt ID stöd.

Referera till [implementera direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) distributionssteg.

### <a name="federated-authentication"></a>Federerad autentisering

* **Prestanda:** med hjälp av en federerad autentisering är beroende av ett externt system för att autentisera användare. Vissa företag vill återanvända befintliga investeringar med sina Azure AD-hybrididentitetslösning externa system. Underhåll och hantering av externa system hamnar utanför kontrollen av Azure AD. Det är organisationen med hjälp av externa system och kontrollera att den har distribuerats på ett säkert sätt och kan hantera belastningen för autentisering. 

* **Användarupplevelsen:** användarupplevelsen för federerad autentisering är beroende av implementeringen av funktioner, topologi och konfiguration av federation-servergruppen. Vissa organisationer kräver den här flexibiliteten att anpassa och konfigurera åtkomst till federation-grupp som passar deras säkerhetskrav. Det är exempelvis möjligt att konfigurera internt anslutna användare och enheter för att logga in användare automatiskt utan att fråga efter autentiseringsuppgifter, eftersom de har redan loggat in på sina enheter. Å andra sidan, om det behövs kan vissa funktioner för avancerad säkerhet försvåra användarens inloggningsprocessen.

* **Avancerade scenarier:** federerat autentiseringslösning är vanligtvis krävs när kunder har en autentiseringskravet stöds inte internt av Azure AD är detaljerad information [som listas här](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/), men vanliga kraven omfattar:

    * Autentisering kräver smartkort eller certifikat
    * Med hjälp av lokalt MFA-Server eller tredje parts Multi-Factor provider.
    * Autentisering med hjälp av lösning från tredje part. Finns det [kompatibilitetslista för Azure AD-federation](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Användarna måste logga in med sina sAMAccountName, till exempel DOMÄN\användarnamn istället för att använda en Principal Name (User Principal Name), t.ex. user@domain.com

* **Kontinuitet för företag:** externa system kräver vanligtvis en belastningen belastningsutjämnade matriskonfiguration av servrar, även kallat en grupp i ett internt nätverk och perimeter nätverkstopologi att säkerställa hög tillgänglighet för autentiseringsbegäranden. Lösenordets Hash-synkronisering kan distribueras tillsammans med federerat autentisering som autentiseringsmetod för säkerhetskopiering när den primära autentiseringsmetoden är inte längre tillgängliga, till exempel om de lokala servrarna inte är tillgängliga. Vissa stora företag behöver en federation-lösning för att stödja flera Internet ingång punkter som konfigurerats med geo-DNS för låg latens autentiseringsbegäranden.

* **Att tänka på:** externa system kräver vanligtvis en mer betydande investering i lokal infrastruktur. De flesta organisationer välja det här alternativet om de redan har en lokal federation investering och det är en starkt affärsmässiga skäl att använda en enda identitetsleverantör. Federation är mer komplex att driva och felsöka jämfört med autentisering av molnlösningar. Med användar-ID med en icke-dirigerbara domän som inte kan verifieras i Azure AD för att logga behöva extra konfiguration för att implementera. Det här kravet kallas alternativt inloggnings ID support. Se [konfigurera alternativa inloggnings-ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) begränsningar och krav. Om du väljer att använda en tredje parts flerfunktionsautentiseringsleverantör med federation, kontrollera att providern har stöd för WS-Trust om du vill att enheter ska kunna ansluta till Azure AD.

Referera till [implementera federationstjänster](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) distributionssteg.

> [!NOTE] 
> När du distribuerar din Azure AD-hybrididentitetslösning, måste du kontrollera att du kan genomföra någon topologier som stöds av Azure AD Connect. Lär dig mer om stöds och som inte stöds konfigurationer på [topologier för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Nätverksdiagram

I följande diagram ger en översikt över övergripande arkitektur-komponenter som krävs för varje autentiseringsmetod som du kan använda med din Azure AD-hybrididentitetslösning. Det ger dig en översikt över att jämföra skillnaderna mellan lösningarna.

Följande diagram ger en översikt över enkelheten i en lösning för lösenordets Hash-synkronisering:

![PHS](media/azure-ad/azure-ad-authn-image2.png)

Följande diagram ger en översikt över direktautentisering agentkrav:

![TEREFTALSYRA](media/azure-ad/azure-ad-authn-image3.png)

I följande bild beskrivs de komponenter som krävs för federering i ditt perimeternätverk och det interna nätverket på din organisation:

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>Jämförelse av metoder för autentisering

|Beräkningen|Lösenordshashsynkronisering + sömlös SSO|Direkt-autentisering + sömlös SSO|Federation med AD FS|
|:-----|:-----|:-----|:-----|
|Där sker autentisering?|I molnet|I molnet, efter ett säkert lösenord verifiering exchange med lokal autentiseringsagent|Lokal|
|Vilka är kraven på lokal server utöver etablering systemet: Azure AD Connect?|Ingen|1-server för varje ytterligare autentisering-agent|2 eller flera AD FS-servrar<br>2 eller högre WAP-servrar i perimeternätverket/DMZ-nätverk|
|Vad är lokal Internet- och nätverkskrav utöver etablering systemet?|Ingen|[Utgående Internetåtkomst](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) från servrar som kör autentisering agenter|[Inkommande Internetåtkomst](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) till WAP-servrar i perimeternätverket<br>Inkommande nätverksåtkomst till AD FS-servrar från WAP-servrar i perimeternätverket<br>Utjämning av nätverksbelastning|
|Finns det ett krav för SSL-certifikat?|Nej|Nej|Ja|
|Finns det en lösning för hälsoövervakning?|Krävs inte|Agentstatus som tillhandahålls av [Azure Active Directory Administrationscenter](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Användare får enkel inloggning till molnresurser från domänanslutna enheter i företagets nätverk?|Ja med [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ja med [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ja|
|Vilka typer av inloggning stöds?|UserPrincipalName + lösenord<br>Windows-integrerad autentisering med hjälp av [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Alternativt inloggnings-ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + lösenord<br>Windows-integrerad autentisering med hjälp av [sömlös SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Alternativt inloggnings-ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + lösenord<br>sAMAccountName + lösenord<br>Windows-integrerad autentisering<br>[Certifikat och smartkort-autentisering](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[Alternativt inloggnings-ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Är Windows Hello för företag som stöds?|[Förtroendemodell med nyckel](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Certifikatet förtroendemodell med Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Förtroendemodell med nyckel](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Certifikatet förtroendemodell med Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Förtroendemodell med nyckel](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Förtroendemodell med certifikat](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Vad är alternativ för multifaktorautentisering?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Azure MFA-Server](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[Tredjeparts-MFA](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Vilka användare konto tillstånd stöds?|Inaktiva konton<br>Låst konto<br>(upp till 30 minuter fördröjning)|Inaktiva konton<br>Låst konto<br>Lösenordet har gått ut<br>Inloggningstiden|Inaktiva konton<br>Låst konto<br>Lösenordet har gått ut<br>Inloggningstiden|
|Vad är alternativ för villkorlig åtkomst?|[Azure AD-villkorlig åtkomst ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD-villkorlig åtkomst ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD-villkorlig åtkomst](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[Anspråksregler i AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blockerar äldre protokoll som stöds?|Nej|Nej|[Ja](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Kan du anpassa logotyp, bild och beskrivning på sidan logga in?|[Ja, med Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ja, med Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ja](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Vilka avancerade scenarier stöds?|[Smart lösenord kontoutelåsning](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[Läcka ut autentiseringsuppgifter rapporter](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Smart lösenord kontoutelåsning](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Låg latens för flera platser autentisering system<br>[AD FS extranät-låsning](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[Integrering med tredjepartssystem identitet](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Rekommendationer och överväganden från Azure AD

Systemet identitet garanterar att dina användare har åtkomst till molnet appar och av branschspecifika appar som du migrerar och göra den tillgänglig i molnet. Autentisering styr åtkomsten till appar att behöriga användare produktiva och obehöriga utanför din organisation känsliga data. Därför bör du beakta följande rekommendationer att välja rätt autentiseringsmetod för din organisation. 

Använd eller aktivera Lösenordshashsynkronisering oavsett vilken autentiseringsmetod du väljer, av följande skäl:

1. **Hög tillgänglighet och katastrofåterställning återställning:** direkt-autentisering och federation förlitar sig på lokal infrastruktur. För direktautentisering är storleken på lokala servermaskinvara och nätverk direktautentisering agenter kräver. Lokala storleken är ännu större för federation, eftersom den kräver servrar i perimeternätverket att proxy autentiseringsbegäranden och interna federationsservrar. Om du vill undvika enskilda felpunkter på fel bör organisationen distribuera redundant servrar för att säkerställa autentiseringsbegäranden behandlas alltid om någon komponent misslyckas. Både direktautentisering och federation är också beroende av domänkontrollanter för att besvara autentiseringsbegäranden, som också kan misslyckas. Många av dessa komponenter måste Underhåll ska vara felfria och avbrott sannolikt när Underhåll inte är planerad och implementerats korrekt. Avbrott kan undvikas genom att använda Lösenordshashsynkronisering eftersom Microsoft Azure AD cloud autentiseringstjänst skalas globalt och alltid är tillgängligt.

2. **Lokala avbrott överlevnad:** konsekvenserna av att en lokal strömavbrott på grund av en attack cyber eller katastrof kan vara betydande mellan reputational varumärken skada en paralyzed organisation som det går inte att åtgärda angreppet. Många organisationer har offer för angrepp från skadlig kod, inklusive riktade är en utpressningstrojan som, som orsakade sina lokala servrar som inte är tillgänglig under det senaste året. I hjälper kunder som handlar om dessa typer av attacker, Microsoft har upptäckt två typer av organisationer:

   a. Organisationer som tidigare har aktiverat Lösenordshashsynkronisering var online igen i några timmar genom att ändra deras autentiseringsmetod om du vill använda lösenordets Hash-synkronisering. Med åtkomst till e-post via Office 365, kan de fungerar för att lösa problem och har åtkomst till andra molnbaserade arbetsbelastningar.

   b. Organisationer som inte tidigare aktivera Lösenordshashsynkronisering tvungna att använda ej betrodda externa e-system för kommunikation och lösa problem. I sådana fall kan tog det dem veckor eller mer att vara igång igen.

3. **Identitetsskydd:** på bästa sätt att skydda användare i molnet är Azure AD Identity Protection. Microsoft genomsöker kontinuerligt Internet för användare och lösenordslistor, obehöriga sälja och göra den tillgänglig på webben mörkt. Azure AD kan använda den här informationen för att kontrollera om någon av de användarnamn och lösenord i din organisation till. Det är därför viktigt att aktivera Lösenordshashsynkronisering oavsett vilken autentiseringsmetod som du använder, oavsett om det är federerat eller direkt-autentisering. Läckta autentiseringsuppgifter visas som en rapport och kan användas för att blockera eller tvinga användaren att ändra sina lösenord när de försöker att logga in med ett läckta lösenord.

Slutligen enligt [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft har den mest komplett uppsättningen funktioner för identitets- och åtkomsthantering. Microsoft fends av sju biljoner cyber händelser per dag och ger inloggning till tusentals SaaS-program som Office 365 behöriga användare från valfri enhet. 

## <a name="conclusion"></a>Sammanfattning

I den här artikeln beskrivs olika autentiseringsalternativ organisationer kan konfigurera och distribuera för att stödja åtkomst till molnappar. Organisationer kan välja mellan Lösenordshashsynkronisering, direktautentisering och federation för att uppfylla olika företag, säkerhet och tekniska krav. Med varje autentiseringsmetod välja organisationen om dina affärsbehov hanteras av arbete för att distribuera lösningen och användarupplevelsen processens inloggning. Du måste också att utvärdera om din organisation behöver avancerade scenarier och funktionerna för verksamhetskontinuitet för varje autentiseringsmetod. Slutligen har du utvärdera överväganden för varje autentiseringsmetod för att se om någon förhindrar du implementera ditt val.

## <a name="next-steps"></a>Nästa steg

I dagens värld hot är tillgänglig dygnet runt och komma från överallt. Implementera rätt autentiseringsmetod hjälper risker säkerhet och skydda dina identiteter. 

[Kom igång](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) med Azure AD och distribuera rätt autentiseringslösning för din organisation.

Lär dig mer om du funderar på att migrera från federerad till autentisering i molnet [om hur du ändrar inloggning metoden](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Du kan använda för att hjälpa dig att planera och implementera migreringen [dessa projektet planer för att hjälpa dig](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/tree/master/Authentication).
