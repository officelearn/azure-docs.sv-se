---
title: Övervaka din lokala identitetsinfrastruktur i molnet.
description: På den här sidan förklarar vi vad Azure AD Connect Health är och hur tjänsten kan hjälpa dig.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 006914b279f2d762bf0dee6f83d6866235c062bc
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887512"
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet
Azure Active Directory (Azure AD) Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna. Tjänsten hjälper dig att upprätthålla en tillförlitlig anslutning till Office 365 och Microsoft Online Services genom att tillhandahålla övervakningsfunktioner för viktiga identitetskomponenter som Active Directory Federation Services-servrar (AD FS), Azure AD Connect-servrar (kallas även synkroniseringsmotorn), Active Directory-domänkontrollanter osv. Den gör det också enkelt att komma åt viktiga datapunkter om komponenterna så att du snabbt kan få information om användningen och andra viktiga insikter för att fatta välgrundade beslut.

Informationen visas på [Azure AD Connect Health-portalen](https://aka.ms/aadconnecthealth). Du kan visa aviseringar, prestandaövervakning, användningsanalys och annan information på Azure AD Connect Health-portalen. Med Azure AD Connect Health har du åtkomst till all hälsoinformation om dina viktiga identitetskomponenter på samma ställe.

![Vad är Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


I takt med att funktionerna i Azure AD Connect Health ökar i antal blir det allt viktigare att få en överblick i portalen. Då är det praktiskt att ha tillgång till en enda instrumentpanel med all identitetsinformation. Du får en stabilare, felfriare och mer integrerad miljö som underlättar arbetet för användarna.
## <a name="why-use-azure-ad-connect-health"></a>Varför ska jag använda Azure AD Connect Health?
Om du integrerar dina lokala kataloger med Azure AD hjälper du dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Dock medför den här integreringen utmaningar för att säkerställa en hälsosam miljö där användarna på ett tillförlitligt sätt kan komma åt resurser både lokalt och i molnet från vilken enhet som helst. Azure AD Connect Health möjliggör övervakning och insyn i den lokala identitetsinfrastrukturen som används för att få åtkomst till Office 365 eller andra Azure AD-program. Det enda du behöver göra är att installera en agent på alla lokala identitetsservrar.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health för AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health för AD FS stöder AD FS 2.0 på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det stöder även övervakning av AD FS-proxyservrar eller webbprogramproxyservrar som tillhandahåller autentiseringsstöd för åtkomst till extranät. Med en enkel och prisvärd installation av hälsoagenten tillhandahåller Azure AD Connect Health för AD FS följande uppsättning viktiga funktioner:

* Övervakning med aviseringar så att du vet när AD FS och AD FS-proxyservrarna har problem.
* E-postmeddelanden för viktiga aviseringar.
* Trender i prestandadata, vilket är användbart för kapacitetsplanering för AD FS.
* Användningsanalys för AD FS-inloggningar med pivoteringsmöjligheter (appar, användare, nätverksplats osv.), vilket är användbart för att förstå hur AD FS används.
* Rapporter för AD FS, till exempel de 50 användarna med flest misslyckade inloggningsförsök på grund av felaktigt användarnamn/lösenord och deras senaste IP-adresser.
* Rapport om riskfyllda IP-adresser för misslyckad AD FS-inloggningar
  
Läs mer här om att [använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)

Följande videoklipp ger en översikt över Azure AD Connect Health för AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
Azure AD Connect Health för synkronisering övervakar och tillhandahåller information om synkroniseringar som sker mellan din lokala Active Directory och Azure AD. Azure AD Connect Health för synkronisering tillhandahåller följande uppsättning viktiga funktioner:

* Övervakning med aviseringar så att du vet när en Azure AD Connect-server (eller synkroniseringsmotorn) har problem.
* E-postmeddelanden för viktiga aviseringar.
* Synkronisera Operational Insights inklusive diagram över svarstider för synkroniseringsåtgärder och trender inom olika åtgärder som tilläggs-, uppdaterings- och borttagningsåtgärder.
* Snabb översiktlig information om synkroniseringsegenskaper och den senaste lyckade exporten till Azure AD.
* Rapporter om synkroniseringsfel på objektnivå \(kräver inte Azure AD Premium\).

Läs mer här om att [använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)

Följande videoklipp ger en översikt över Azure AD Connect Health för synkronisering.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health för AD DS](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health för Active Directory Domain Services (AD DS) tillhandahåller övervakning för domänkontrollanter som har installerats på Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Om du installerar hälsoagenten kan du övervaka din lokala AD DS-miljö från molnet. Azure AD Connect Health för AD DS tillhandahåller följande viktiga funktioner:

* Övervakning av aviseringar, så att du kan upptäcka när domänkontrollanter är skadade, och e-postmeddelanden för viktiga aviseringar
* Instrumentpanelen Domänkontrollanter, vilken ger en snabb översikt över dina domänkontrollanters hälso- och driftstatus
* Instrumentpanelen Replikeringsstatus, som innehåller den senaste replikeringsinformationen tillsammans med länkar till felsökningsguider för identifierade fel
* Snabb åtkomst överallt till prestandadiagram från populära prestandaräknare, vilket behövs för felsökning och övervakning

Läs mer här om att [använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)

Följande videoklipp ger en översikt över Azure AD Connect Health för AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Komma igång med Azure AD Connect Health
Använd följande steg för att komma igång med Azure AD Connect Health:

1. [Hämta Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) eller [starta en utvärderingsperiod](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Ladda ned och installera Azure AD Connect Health-agenter](#download-and-install-azure-ad-connect-health-agent) på dina identitetsservrar.
3. Visa Azure AD Connect Health-instrumentpanelen på [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Kom ihåg att innan du ser några data på Azure AD Connect Health-instrumentpanelen så måste du installera Azure AD Connect Health-agenterna på dina målservrar.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Ladda ned och installera Azure AD Connect Health-agenten
* Säkerställ att du [uppfyller kraven](active-directory-aadconnect-health-agent-install.md#requirements) för Azure AD Connect Health.
* Kom igång med Azure AD Connect Health för AD FS
    * [Hämta Azure AD Connect Health Agent för AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Se installationsinstruktionerna](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Kom igång med Azure AD Connect Health för synkronisering
    * [Hämta och installera den senaste versionen av Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771). Hälsoagenten för synkronisering installeras som en del av installationen av Azure AD Connect (version 1.0.9125.0 eller senare).
* Kom igång med Azure AD Connect Health för AD DS
    * [Ladda ned Azure AD Connect Health Agent för AD DS](http://go.microsoft.com/fwlink/?LinkID=820540).
    * [Se installationsinstruktionerna](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health-portalen
På Azure AD Connect Health-portalen kan du visa varningar, övervaka prestanda och hitta användningsanalyser. URL:en https://aka.ms/aadconnecthealth tar dig till huvudbladet i Azure AD Connect Health. Tänk dig ett blad som ett fönster. Huvudbladet innehåller en **snabbstart**, tjänster i Azure AD Connect Health och ytterligare konfigurationsalternativ. Mer information finns i form av skärmbilden nedan tillsammans med kortfattade beskrivningar. När du har distribuerat agenterna identifierar hälsotjänsten automatiskt de tjänster som Azure AD Connect Health övervakar.

> [!NOTE]
> För mer information om licenser går du till [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md) eller [sidan med priser för Azure AD](https://aka.ms/aadpricing).
    
![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/portalsidebar.png)

* **Snabbstart**: När du väljer det här alternativet öppnas bladet **Snabbstart**. Du kan hämta Azure AD Connect Health-agenten genom att välja **Hämta verktyg**. Du kan också komma åt dokumentationen och ge feedback.
* **Azure Active Directory Connect (synkronisering)**: När du väljer det här alternativet visas de Azure AD Connect-servrar som Azure AD Connect Health övervakar för tillfället. Posten **Sync errors** (Synkroniseringsfel) visar grundläggande synkroniseringsfel för din första publicerade tjänst efter kategorier. När du väljer posten **Sync services** (Synkroniseringstjänster) öppnas ett blad som visar information om dina Azure AD Connect-servrar. Läs mer om funktionerna i [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md).
* **Active Directory Federation Services**: När du väljer det här alternativet visas alla AD FS-tjänster som Azure AD Connect Health övervakar för tillfället. När du väljer en instans visas information om tjänstinstansen på bladet som öppnas. Den här informationen innehåller en översikt, egenskaper, aviseringar, övervakning och användningsanalys. Läs mer om funktionerna i [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md).
* **Active Directory Domain Services**: När du väljer det här alternativet visas alla AD DS-skogar som Azure AD Connect Health övervakar för tillfället. När du väljer en skog visas information om skogen på bladet som öppnas. Denna information innehåller en översikt över viktig information, instrumentpanelerna Domänkontrollanter och Replikeringsstatus, aviseringar och övervakning. Läs mer om funktionerna i [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md).
* **Konfigurera**: Det här avsnittet innehåller alternativ för att aktivera eller inaktivera följande:

  - Posten **Settings** (Inställningar ) innehåller grundläggande konfigurationer av dina agenter. Inställningen för automatisk uppgradering aktiverar automatisk uppdatering av Azure AD Connect Health-agenten till den senaste versionen: med det här alternativet uppdateras Azure AD Connect Health-agenten automatiskt till den senaste versionen när den blir tillgänglig. Den här funktionen är aktiverad som standard. Ge Microsoft åtkomst till din Azure AD-katalogs hälsodata i felsökningssyfte: Om det här alternativet är aktiverat kan Microsoft se samma data som du ser. Den här informationen kan komma till nytta vid felsökning och om du behöver hjälp med olika problem. Alternativet är inaktiverat som standard.
* **Role based access control (IAM)** (Rollbaserad åtkomstkontroll) är avsnittet för att hantera åtkomst till Connect Health-data i rollbasen. 

## <a name="related-links"></a>Relaterade länkar
* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
