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
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181795"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Hybrididentitet och Microsofts identitetslösningar
Företag och affärer blir idag allt mer en blandning av lokala och molnbaserade program.  Att ha program och användare som kräver åtkomst till de programmen, lokalt och i molnet, har blivit en utmanande uppgift.

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner, skapar en enda användaridentitet för autentisering och auktorisering till alla resurser, oavsett plats. Vi kallar det för hybrididentitet.

## <a name="what-is-azure-ad-connect"></a>Vad är Azure AD Connect?

Azure AD Connect är Microsoft-verktyget som har utformats för att uppfylla och uppnå dina hybrididentitetsmål.  På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD.  Den tillhandahåller följande funktioner:
    
- [Synkronisering](how-to-connect-sync-whatis.md) – Den här komponenten är ansvarig för att skapa användare, grupper och andra objekt. Den är också ansvarig för att se till att identitetsinformationen för dina lokala användare och grupper matchar molnet.  Den är ansvarig för att synkronisera lösenord-hashar med Azure AD.
-   [AD FS och federationsintegrering](how-to-connect-fed-whatis.md) – Federation är en valfri del av Azure AD Connect och kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Det har även AD FS-hanteringsfunktioner som certifikatsförnyelse och ytterligare AD FS-serverdistributioner.
-   [Direktautentisering](how-to-connect-pta.md) – Ytterligare en valfri komponent som gör att användare kan använda samma lösenord lokalt och i molnet men inte kräver den ytterligare infrastrukturen som en federerad miljö
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









