---
title: Anslut Active Directory med Azure Active Directory. | Microsoft Docs
description: Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Office 365-, Azure- och SaaS-program som är integrerade med Azure AD.
keywords: introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c8ab6b6e6bab7451de7d975dde644386fd4cb84e
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52311542"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>Hybrididentitet och Microsofts identitetslösningar
[Microsoft Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)-hybrididentitetslösningar (Azure AD) gör det möjligt att synkronisera lokala katalogobjekt med Azure AD medan du fortfarande hanterar användarna lokalt. Det första beslutet att fatta när du planerar att synkronisera din lokala Windows Server Active Directory med Azure AD är huruvida du vill använda hanterade eller federerade identiteter. 

- **Hanterade identiteter** – Användarkonton och grupper synkroniseras från en lokal Active Directory, och användarautentiseringen hanteras av Azure.   
- **Federerade identiteter** medger mer kontroll över användare genom att separera användarautentisering från Azure och delegera autentisering till en betrodd, lokal identitetsprovider. 

Det finns flera alternativ för att konfigurera hybrididentitet. När du överväger vilken identitetsmodell som bäst passar organisationens behov bör du även ta med tid, befintlig infrastruktur, komplexitet och kostnad i beräkningen. Dessa faktorer är olika för varje organisation och kan ändras över tid. Men om dina krav ändras har du även flexibiliteten att växla till en annan identitetsmodell.

## <a name="managed-identity"></a>Hanterad identitet 

Hanterad identitet är det enklaste sättet att synkronisera lokala katalogobjekt (användare och grupper) med Azure AD. 

![Synkroniserad hybrididentitet](./media/whatis-hybrid-identity/managed.png)

Hanterad identitet är den enklaste och snabbaste metoden, men dina användare behöver ändå ha ett separat lösenord för molnbaserade resurser. För att undvika detta kan du även (valfritt) [synkronisera en hash av användarlösenord](how-to-connect-password-hash-synchronization.md) till din Azure AD-katalog. Synkronisering av lösenordshasher gör att användare kan logga in på molnbaserade organisationsresurser med samma användarnamn och lösenord som de använder lokalt. Azure AD Connect kontrollerar regelbundet din lokala katalog efter ändringar och håller Azure AD-katalogen synkroniserad. När ett användarattribut eller ett lösenord ändras i lokal Active Directory uppdateras det automatiskt i Azure AD. 

För de flesta organisationer som bara behöver ge användarna möjlighet att logga in på Office 365, SaaS-program och andra Azure AD-baserade resurser rekommenderas standardalternativet för synkronisering av hashlösenord. Om det inte fungerar för dig behöver du välja mellan direktautentisering och AD FS.

> [!TIP]
> Användarlösenord lagras i lokal Windows Server Active Directory i form av ett hashvärde som representerar den faktiska användarens lösenord. Ett hashvärde är ett resultat av en enkelriktad matematisk funktion (hashalgoritmen). Det finns ingen metod för att återställa resultatet av en envägsfunktion till versionen av ett lösenord med oformaterad text. Du kan inte använda en lösenordshash för att logga in på ditt lokala nätverk. När du väljer att synkronisera lösenords extraherar Azure AD Connect lösenordshasher från lokal Active Directory och tillämpar extra säkerhetsbearbetning på lösenordshashen innan den synkroniseras till Azure AD. Synkronisering av hashlösenord kan även användas tillsammans med tillbakaskrivning av lösenord för att aktivera självåterställning av lösenord i Azure AD. Dessutom kan du aktivera enkel inloggning (SSO) för användare på domänanslutna datorer som är anslutna till företagsnätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användarnamn för att få säker åtkomst till molnresurser. 
>

## <a name="pass-through-authentication"></a>Direktautentisering

[Azure AD-direktautentisering](how-to-connect-pta.md) är en enkel lösning för lösenordsverifiering för Azure AD-baserade tjänster som använder din lokala Active Directory. Om principer för säkerhet och efterlevnad för din organisation inte tillåter att användares lösenord skickas, även i hashad form, och du endast behöver stödja enkel skrivbordsinloggning för domänanslutna enheter rekommenderas det att du överväger att använda direktautentisering. Direktautentisering kräver inte någon distribution i DMZ, vilket förenklar distributionsinfrastrukturen jämfört med AD FS. När användare loggar in med Azure AD verifierar den här autentiseringsmetoden användarnas lösenord direkt mot din lokala Active Directory.

![Direktautentisering](./media/whatis-hybrid-identity/pass-through-authentication.png)

Med direktautentisering behövs ingen komplex nätverksinfrastruktur, och du behöver inte lagra lokala lösenord i molnet. I kombination med enkel inloggning ger direktautentisering en helt integrerad upplevelse vid inloggning till Azure AD eller andra molntjänster.

Direktautentisering konfigureras med Azure AD Connect, som använder en enkel lokal agent som lyssnar efter begäranden om lösenordsverifiering. Agenten kan enkelt distribueras till flera datorer för att tillhandahålla hög tillgänglighet och belastningsutjämning. Eftersom all kommunikation endast är utgående finns inga krav om att anslutningsprogrammet ska vara installerat i en DMZ. Kraven för serverdatorer för anslutningsprogrammet är följande:

- Windows Server 2012 R2 eller senare
- Anslutet till en domän i skogen varigenom användare verifieras

## <a name="federated-identity-ad-fs"></a>Federerad identitet (AD FS)

För att få mer kontroll över hur användare kommer åt Office 365 och andra molntjänster kan du konfigurera katalogsynkronisering med enkel inloggning (SSO) med hjälp av [Active Directory Federation Services (AD FS)](how-to-connect-fed-whatis.md). Federation av dina användares inloggningar med AD FS delegerar autentisering till en lokal server som verifierar användarnas autentiseringsuppgifter. I den här modellen skickas autentiseringsuppgifter för lokal Active Directory aldrig till Azure AD.

![Federerad identitet](./media/whatis-hybrid-identity/federated-identity.png)

Den här inloggningsmetoden kallas även identitetsfederation och säkerställer att all användarautentisering kontrolleras lokalt och gör att administratörer kan implementera rigorösare nivåer av åtkomstkontroll. Identitetsfederation med AD FS är det mest komplicerade alternativet och kräver distribution av ytterligare servrar i din lokala miljö. Identitetsfederation gör även att du måste tillhandahålla support dygnet runt för din Active Directory- och AD FS-infrastruktur. Denna höga nivå av support är nödvändig eftersom om din lokala anslutning till Internet, domänkontrollanten eller AD FS-servrarna förloras kan användarna inte logga in på molntjänster.

> [!TIP]
> Om du bestämmer dig för att använda federation med Active Directory Federation Services (AD FS) kan du även konfigurera synkronisering av hashlösenord som en reservåtgärd för de fall då det inte går att nå AD FS-infrastrukturen.
>

## <a name="common-scenarios-and-recommendations"></a>Vanliga scenarier och rekommendationer

Här följer några vanliga scenarier med hybrididentitet och åtkomsthantering med rekommendationer om vilket (eller vilka) hybrididentitetsalternativ som passar för respektive scenario.

|Jag behöver:|PHS och SSO<sup>1</sup>| PTA och SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Synkronisera nya användare, kontakter och gruppkonton som skapas i min lokala Active Directory till molnet automatiskt.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Konfigurera min klientorganisation för Office 365-hybridscenarier|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Göra det möjligt för mina användare att logga in och komma åt molntjänster med sina lokala lösenord|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Implementera enkel inloggning med hjälp av företagsautentiseringsuppgifter|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Se till att inga lösenordshashvärden lagras i molnet| |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Stödja molnlösningar för multifaktorautentisering| |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Stödja lokala lösningar för multifaktorautentisering| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Stödja autentisering med smartkort för mina användare<sup>4</sup>| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|
|Visa meddelanden om lösenord som upphör att gälla i Office-portalen och i skrivbordsversionen av Windows 10| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> Lösenordshash-synkronisering med enkel inloggning.
>
> <sup>2</sup> Direktautentisering och enkel inloggning. 
>
> <sup>3</sup> federerad enkel inloggning med AD FS.
>
> <sup>4</sup> AD FS kan integreras med din företags-PKI för att tillåta inloggning med certifikat. Dessa certifikat kan vara mjuka certifikat som distribueras via betrodda etableringskanaler, till exempel MDM, GPO eller smartkortscertifikat (inklusive PIV/CAC-kort) eller Hello för företag (cert-förtroende). Mer information om stöd för smartkortsautentisering finns på [den här bloggen](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?

Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål.  På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD.  Den tillhandahåller följande funktioner:
    
- [Synkronisering](how-to-connect-sync-whatis.md) – Den här komponenten är ansvarig för att skapa användare, grupper och andra objekt. Den är också ansvarig för att se till att identitetsinformationen för dina lokala användare och grupper matchar molnet.  Den är ansvarig för att synkronisera lösenord-hashar med Azure AD.
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md) – en valfri komponent som tillåter användare att använda de samma lösenord lokalt och i molnet genom att synkronisera en hash av användarnas lösenord med Azure AD.
-   [AD FS och federationsintegrering](how-to-connect-fed-whatis.md) – Federation är en valfri del av Azure AD Connect och kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Det har även AD FS-hanteringsfunktioner som certifikatsförnyelse och ytterligare AD FS-serverdistributioner.
-   [Direktautentisering](how-to-connect-pta.md) – Ytterligare en valfri komponent som gör att användare kan använda samma lösenord lokalt och i molnet men inte kräver den ytterligare infrastrukturen som en federerad miljö
-   [PingFederate- och federationsintegrering](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) – ett annat federationsalternativ som gör att du kan använda PingFederate som identitetsprovider.
-   [Hälsoövervakning](whatis-hybrid-identity-health.md) – Azure AD Connect Health kan tillhandahålla robust övervakning och en central plats i Azure-portalen för att övervaka den här aktiviteten. 


![Vad är Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Vad är Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna. Tjänsten hjälper dig att upprätthålla en tillförlitlig anslutning till Office 365 och Microsoft Online Services genom att tillhandahålla övervakningsfunktioner för viktiga identitetskomponenter som Active Directory Federation Services-servrar (AD FS), Azure AD Connect-servrar (kallas även synkroniseringsmotorn), Active Directory-domänkontrollanter osv. Den gör det också enkelt att komma åt viktiga datapunkter om komponenterna så att du snabbt kan få information om användningen och andra viktiga insikter för att fatta välgrundade beslut.

Informationen visas på [Azure AD Connect Health-portalen](https://aka.ms/aadconnecthealth). Du kan visa aviseringar, prestandaövervakning, användningsanalys och annan information på Azure AD Connect Health-portalen. Med Azure AD Connect Health har du åtkomst till all hälsoinformation om dina viktiga identitetskomponenter på samma ställe.

![Vad är Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


I takt med att funktionerna i Azure AD Connect Health ökar i antal blir det allt viktigare att få en överblick i portalen. Då är det praktiskt att ha tillgång till en enda instrumentpanel med all identitetsinformation. Du får en stabilare, felfriare och mer integrerad miljö som underlättar arbetet för användarna.


## <a name="why-use-azure-ad-connect"></a>Varför ska jag använda Azure AD Connect?
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Användare och företag kan dra nytta av följande:

* Användarna kan använda samma identitet för att komma åt lokala program och molntjänster som Office 365.
* Ett enda verktyg är allt som krävs för att tillhandahålla en enkel distributionsupplevelse för synkronisering och inloggning.
* Innehåller de senaste funktionerna för dina scenarier. Azure AD Connect ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync. Mer information finns i [Jämförelse av katalogintegreringsverktyg för hybrididentitet](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Varför ska jag använda Azure AD Connect Health?
Om du integrerar dina lokala kataloger med Azure AD hjälper du dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Dock medför den här integreringen utmaningar för att säkerställa en hälsosam miljö där användarna på ett tillförlitligt sätt kan komma åt resurser både lokalt och i molnet från vilken enhet som helst. Azure AD Connect Health möjliggör övervakning och insyn i den lokala identitetsinfrastrukturen som används för att få åtkomst till Office 365 eller andra Azure AD-program. Det enda du behöver göra är att installera en agent på alla lokala identitetsservrar.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health för AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health för AD FS stöder AD FS 2.0 på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det stöder även övervakning av AD FS-proxyservrar eller webbprogramproxyservrar som tillhandahåller autentiseringsstöd för åtkomst till extranät. Med en enkel och snabb installation av hälsoagenten tillhandahåller Azure AD Connect Health för AD FS dig en uppsättning viktiga funktioner.

#### <a name="key-benefits-and-best-practices"></a>Viktiga fördelar och metodtips

- *Förbättrad säkerhet*
  - [Trender för extranätsutelåsning](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Rapport över misslyckade inloggningar](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Uppfyller sekretesskrav](reference-connect-health-user-privacy.md)    
- *Få meddelanden om alla [kritiska ADFS-systemproblem](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Serverkonfiguration och tillgänglighet 
    - [Prestanda och anslutning](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Regelbundet underhåll    
- *Lätt att distribuera och hantera*
  - Snabb [agentinstallation](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - Automatisk agentuppgradering till senaste version 
  - Data tillgängliga i portalen inom några minuter    
- *Omfattande [användningsstatistik](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - Främsta programanvändning
  - Nätverksplatser och TCP-anslutning
  - Tokenbegäranden per server    
- *En utmärkt användarupplevelse* 
  - Instrumentpanel som Azure-portalen
  - [Aviseringar via e-postmeddelanden](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Funktioner

*   Övervakning med aviseringar så att du vet när AD FS och AD FS-proxyservrarna har problem.
*   E-postmeddelanden för viktiga aviseringar.
*   Trender i prestandadata, vilket är användbart för kapacitetsplanering för AD FS.
*   Användningsanalyser för AD FS-inloggningar med pivoter (appar, användare, nätverksplats osv.)
*   Rapporter för AD FS, till exempel de 50 användarna med flest misslyckade inloggningsförsök på grund av felaktigt användarnamn/lösenord och deras senaste IP-adresser.
*   Rapport om riskfyllda IP-adresser för misslyckad AD FS-inloggningar

Läs mer här om att [använda Azure AD Connect Health med AD FS](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)
Azure AD Connect Health för synkronisering övervakar och tillhandahåller information om synkroniseringar som sker mellan din lokala Active Directory och Azure AD. Azure AD Connect Health för synkronisering tillhandahåller följande uppsättning viktiga funktioner:

* Övervakning med aviseringar så att du vet när en Azure AD Connect-server (eller synkroniseringsmotorn) har problem.
* E-postmeddelanden för viktiga aviseringar.
* Synkronisera Operational Insights inklusive diagram över svarstider för synkroniseringsåtgärder och trender inom olika åtgärder som tilläggs-, uppdaterings- och borttagningsåtgärder.
* Snabb översiktlig information om synkroniseringsegenskaper och den senaste lyckade exporten till Azure AD.
* Rapporter om synkroniseringsfel på objektnivå \(kräver inte Azure AD Premium\).

Läs mer här om att [använda Azure AD Connect Health för synkronisering](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health för AD DS](how-to-connect-health-adds.md)
Azure AD Connect Health för Active Directory Domain Services (AD DS) tillhandahåller övervakning för domänkontrollanter som har installerats på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Om du installerar hälsoagenten kan du övervaka din lokala AD DS-miljö från molnet. Azure AD Connect Health för AD DS tillhandahåller följande viktiga funktioner:

* Övervakning av aviseringar, så att du kan upptäcka när domänkontrollanter är skadade, och e-postmeddelanden för viktiga aviseringar
* Instrumentpanelen Domänkontrollanter, vilken ger en snabb översikt över dina domänkontrollanters hälso- och driftstatus
* Instrumentpanelen Replikeringsstatus, som innehåller den senaste replikeringsinformationen tillsammans med länkar till felsökningsguider för identifierade fel
* Snabb åtkomst överallt till prestandadiagram från populära prestandaräknare, vilket behövs för felsökning och övervakning

Läs mer här om att [använda Azure AD Connect Health med AD DS](how-to-connect-health-adds.md)

## <a name="next-steps"></a>Nästa steg


- [Maskinvara och krav](how-to-connect-install-prerequisites.md) 
- [Standardinställningar](how-to-connect-install-express.md)
- [Anpassade inställningar](how-to-connect-install-custom.md)
- [Synkronisering av lösenordshash](how-to-connect-password-hash-synchronization.md)|
- [Direktautentisering](how-to-connect-pta.md)
- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
- [Installera Azure AD Connect Health-agenter](how-to-connect-health-agent-install.md) 
- [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md)
- [Versionshistorik](reference-connect-version-history.md)
- [Jämförelse av katalogintegreringsverktyg](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Vanliga frågor och svar om Azure AD Connect](reference-connect-faq.md)









