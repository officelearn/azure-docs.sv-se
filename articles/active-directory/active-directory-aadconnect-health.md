<properties
    pageTitle="Övervaka din lokala identitetsinfrastruktur i molnet."
    description="På den här sidan förklarar vi vad Azure AD Connect Health är och hur tjänsten kan hjälpa dig."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="stevenpo"
    editor="karavar"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="vakarand"/>

# Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet

Azure AD Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna.  Tjänsten hjälper dig att upprätthålla en tillförlitlig anslutning till Office 365 och Microsoft Online Services genom att tillhandahålla övervakningsfunktioner för viktiga identitetskomponenter som AD FS-servrar, Azure AD Connect-servrar (kallas även synkroniseringsmotorn), Active Directory-domänkontrollanter osv. Den gör det också enkelt att komma åt viktiga datapunkter om komponenterna så att du snabbt kan få information om användningen och andra viktiga insikter.

Informationen visas för dig på [Azure AD Connect Health-portalen](https://aka.ms/aadconnecthealth). Du kan visa aviseringar, prestandaövervakning, användningsanalys och mycket mer på Azure AD Connect Health-portalen. Med Azure AD Connect Health har du åtkomst till all hälsoinformation om dina viktiga identitetskomponenter på samma ställe.

![Vad är Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

Framtida uppdateringar av Azure AD Connect Health kommer att erbjuda utökad övervakning och ännu bättre insyn i ytterligare identitetskomponenter. Du får tillgång till en enda instrumentpanel för identitetsinfrastrukturen och en ännu mer robust, felfri och integrerad miljö som dina användare kan dra nytta av för att bli mer effektiva.

<!-- <center>![What is Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)</center> -->

## Varför ska jag använda Azure AD Connect Health?

Om du integrerar dina lokala kataloger med Azure AD hjälper du dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Dock medför den här integreringen utmaningar för att säkerställa en hälsosam miljö där användarna på ett tillförlitligt sätt kan komma åt resurser både lokalt och i molnet från vilken enhet som helst. Azure AD Connect Health tillhandahåller en enkel molnbaserad lösning för övervakning och insyn i den lokala identitetsinfrastrukturen som används för att få åtkomst till Office 365 eller andra Azure AD-program. Det enda du behöver göra är att installera en agent på alla lokala identitetsservrar.

## [Azure AD Connect Health för AD FS](active-directory-aadconnect-health-adfs.md)

Azure AD Connect Health för AD FS stöder AD FS 2.0 i Windows Server 2008 R2, AD FS i Windows Server 2012 och Windows Server 2012 R2. Detta omfattar även AD FS-proxyservrar eller webbprogramproxyservrar som tillhandahåller autentiseringsstöd för åtkomst till extranät. Med en mycket enkel och billig installation av hälsoagenten tillhandahåller Azure AD Connect Health för AD FS följande uppsättning viktiga funktioner:

- Övervakning med aviseringar så att du vet när AD FS och AD FS-proxyservrarna har problem.
- E-postmeddelanden för viktiga aviseringar.
- Visa trender i prestandadata, vilket är användbart för kapacitetsplanering för AD FS.
- Användningsanalys för AD FS-inloggningar med pivoteringsmöjligheter (appar, användare, nätverksplats osv.), vilket är användbart för att förstå hur AD FS används.
- Rapporter för AD FS, till exempel de 50 användarna med flest misslyckade inloggningsförsök på grund av felaktigt användarnamn/lösenord.

Följande videoklipp ger en översikt över Azure AD Connect Health för AD FS

>[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]

## [Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)

Azure AD Connect Health för synkronisering övervakar och tillhandahåller information om synkroniseringar som sker mellan din lokala Active Directory och Azure Active Directory. Azure AD Connect Health för synkronisering tillhandahåller följande uppsättning viktiga funktioner:

- Övervakning med aviseringar så att du vet när Azure AD Connect-servrarna (eller synkroniseringsmotorn) har problem.
- E-postmeddelanden för viktiga aviseringar.
- Synkronisera åtgärdsinformation inklusive diagram över svarstider för synkroniseringsåtgärder och trender i synkroniseringsåtgärder som tilläggs-, uppdaterings- och borttagningsåtgärder.
- Snabb översiktlig information om synkroniseringsegenskaper och den senaste lyckade exporten till Azure AD.

Följande videoklipp ger en översikt över Azure AD Connect Health för synkronisering

>[AZURE.VIDEO azure-active-directory-connect-health-monitoring-the-sync-engine]

## [Azure AD Connect Health för AD DS (förhandsgranskning)](active-directory-aadconnect-health-adds.md)

Azure AD Connect Health för AD DS tillhandahåller övervakning för domänkontrollanter som har installerats på Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2. Med denna enkla och prisvärda hälsoagentsinstallationkan du övervaka din lokala AD DS-miljö direkt från molnet. Azure AD Connect Health för AD DS tillhandahåller följande viktiga funktioner:

- Övervakning av aviseringar, så att du kan upptäcka när domänkontrollanter är skadade, och e-postmeddelanden för viktiga aviseringar.
- Instrumentpanelen Domänkontrollanter, vilken ger en snabb översikt över dina domänkontrollanters hälso- och driftstatus.
- Instrumentpanelen Replikeringsstatus, som innehåller den senaste replikeringsinformationen tillsammans med länkar till felsökningsguider för identifierade fel.
- Snabb åtkomst överallt till prestandadiagram från populära prestandaräknare, vilket behövs för felsökning och övervakning.

Följande videoklipp ger en översikt över Azure AD Connect Health för AD DS

>[AZURE.VIDEO azure-ad-connect-health-monitors-on-premises-ad-domain-services]

## Komma igång med Azure AD Connect Health
Det är mycket enkelt att komma igång med Azure AD Connect Health. Följ stegen nedan:

1. [Hämta Azure AD Premium](active-directory-get-started-premium.md) eller [starta en utvärderingsversion](https://azure.microsoft.com/trial/get-started-active-directory/)

2. [Hämta och installera Azure AD Connect Health-agenter](#download-and-install-azure-ad-connect-health-agent) på dina identitetsservrar.

3. Öppna Azure AD Connect Health-instrumentpanelen på [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth)

>[AZURE.NOTE]Kom ihåg att innan du ser några data på Azure AD Connect Health-instrumentpanelen så måste du installera Azure AD Connect Health-agenterna på dina målservrar.

## Hämta och installera Azure AD Connect Health-agenten

- Läs [kraven](active-directory-aadconnect-health-agent-install.md#Requirements) för Azure AD Connect Health

- Du kommer igång med Azure AD Connect Health för AD FS genom att ladda ned den senaste versionen av agenten här:  [Ladda ned Azure AD Connect Health Agent för AD FS.](http://go.microsoft.com/fwlink/?LinkID=518973)
[](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)

- Kom igång med Azure AD Connect Health för synkronisering genom att ladda ned och installera [den senaste versionen av Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771).  Hälsoagenten installeras som en del av installationen av Azure AD Connect (version 1.0.9125.0 eller senare).  Azure AD Connect har stöd för en uppgradering på plats från tidigare versioner.

- Kom igång direkt med Azure AD Connect Health för AD DS genom att ladda ned den senaste versionen av agenten här: [Ladda ned Azure AD Connect Health Agent för AD DS.](http://go.microsoft.com/fwlink/?LinkID=820540)
[](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)

## Azure AD Connect Health-portalen
På Azure AD Connect Health-portalen kan du visa varningar, övervaka prestanda och användningsanalys. https://aka.ms/aadconnecthealth tar dig till huvudbladet i Azure AD Connect Health.  Tänk dig ett blad som ett fönster. Huvudbladet innehåller en snabbstart, tjänster i Azure AD Connect Health och ytterligare konfigurationsalternativ. Dessa förklaras kortfattat under skärmbilden.  När du har distribuerat agenterna identifierar hälsotjänsten automatiskt de tjänster som Azure AD Connect Health övervakar.

![Azure AD Connect Health-portalen](./media/active-directory-aadconnect-health/portal4.png)

- **Snabbstart** – Om du väljer det här alternativet öppnas bladet Snabbstart. Här kan du ladda ned Azure AD Connect Health-agenten genom att välja Hämta verktyg, öppna dokumentationen och lämna feedback.

- **Active Directory Federation Services** – Representerar alla AD FS-tjänster som Azure AD Connect Health övervakar för tillfället. När du väljer någon av instanserna öppnas ett blad med information om den tjänstinstansen.  Den här informationen innehåller en översikt, egenskaper, aviseringar, övervakning och användningsanalys. Läs mer om funktionerna [här.](active-directory-aadconnect-health-adfs.md)

- **Azure Active Directory Connect (synkronisering)** – Representerar dina Azure AD Connect-servrar som Azure AD Connect Health övervakar för närvarande. När du väljer posten öppnas ett blad med information om dina Azure AD Connect-servrar. Läs mer om funktionerna [här.](active-directory-aadconnect-health-sync.md)
 
- **Active Directory Domain Services** – Representerar alla AD DS-skogar som Azure AD Connect Health övervakar för tillfället. När du väljer någon av skogarna öppnas ett blad med information om den skogen.  Denna information innehåller en översikt över viktig information, instrumentpanelerna Domänkontrollanter och Replikeringsstatus, aviseringar och övervakning. Läs mer om funktionerna [här.](active-directory-aadconnect-health-adds.md)

- **Konfigurera** – Med det här alternativet kan du aktivera eller inaktivera följande:

    1. Uppdatera automatiskt Azure AD Connect Health-agenten till den senaste versionen – Med det här alternativet uppdateras Azure AD Connect Health-agenten automatiskt till den senaste versionen när den blir tillgänglig. Den här funktionen är aktiverad som standard.

    2. Ge Microsoft åtkomst till din Azure AD-katalogs hälsodata i felsökningssyfte – Om det här alternativet är aktiverat kan Microsoft se samma data som du ser. Det här alternativet kan hjälpa dig med felsökning och om du behöver hjälp med problem. Alternativet är inaktiverat som standard.


## Relaterade länkar

* [Installation av Azure AD Connect Health Agent](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health-åtgärder](active-directory-aadconnect-health-operations.md)
* [Använda Azure AD Connect Health med AD FS](active-directory-aadconnect-health-adfs.md)
* [Använda Azure AD Connect Health för synkronisering](active-directory-aadconnect-health-sync.md)
* [Använda Azure AD Connect Health med AD DS](active-directory-aadconnect-health-adds.md)
* [Vanliga frågor och svar om Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Versionshistorik för Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)



<!--HONumber=sep16_HO1-->


