---
title: Azure Active Directory Connect vanliga frågor och svar - | Microsoft Docs
description: Den här artikeln får du svar på vanliga frågor om Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 963f0ebdddab9fe7519b34153b7fd787da0fe7dd
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43112093"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – vanliga frågor och svar

## <a name="general-installation"></a>Allmän installation
**F: installationen fungerar om den globala administratören för Azure Active Directory (Azure AD) har tvåfaktorautentisering (2FA) aktiverad?**  
Det här scenariot stöds från och med februari 2016-versioner.

**F: finns det ett sätt att installera Azure AD Connect obevakad?**  
Azure AD Connect-installationen stöds bara när du använder installationsguiden. En obevakad, tyst installation stöds inte.

**F: Jag har en skog där en domän inte kan kontaktas. Hur gör jag för att installera Azure AD Connect?**  
Det här scenariot stöds från och med februari 2016-versioner.

**F: kan Azure Active Directory Domain Services (Azure AD DS) health-agenten fungerar på server core?**  
Ja. När du har installerat agenten kan du genomföra registreringen genom att använda följande PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**F: kan Azure AD Connect synkroniserar stöd från två domäner till i Azure AD?**  
Ja, det här scenariot stöds. Referera till [flera domäner](active-directory-aadconnect-multiple-domains.md).
 
**F: kan du ha flera anslutningar för samma Active Directory-domän i Azure AD Connect?**  
Nej, flera anslutningar för samma AD-domän stöds inte. 

**F: kan jag flytta Azure AD Connect-databasen från den lokala databasen till en fjärransluten SQL Server-instans?**   
Ja, ange följande allmänna riktlinjer om hur du gör detta. Vi arbetar på ett mer detaljerad dokument.
1. Säkerhetskopiera LocalDB ADSync-databas.
Det enklaste sättet att göra detta är att använda SQL Server Management Studio installerat på samma dator som Azure AD Connect. Ansluta till *(localdb)\.\ADSync*, och sedan säkerhetskopiera ADSync-databas.

2. Återställa ADSync-databas till din fjärranslutna SQL Server-instans.

3. Installera Azure AD Connect mot de befintliga [fjärranslutna SQL-databas](active-directory-aadconnect-existing-database.md).
   Artikeln visar hur du migrerar till med hjälp av en lokal SQL-databas. Om du migrerar till att använda en fjärransluten SQL-databas i steg 5 i processen måste du också ange ett befintligt tjänstkonto som använder Windows-synkroniseringstjänsten. Den här synkroniseringstjänstkontot för motor som beskrivs här:
   
      **Använd ett befintligt tjänstkonto**: som standard använder Azure AD Connect ett virtuellt tjänstkonto av synkroniseringstjänsterna för att använda. Om du använder en fjärransluten SQL Server-instans eller använder en proxyserver som kräver autentisering, Använd ett hanterat tjänstkonto eller ett tjänstkonto i domänen och känna till lösenordet. I detta fall anger du det konto som ska användas. Se till att användare som kör installationen är administratörer i SQL så att inloggningsuppgifterna för tjänstkontot kan skapas. Mer information finns i [Azure AD Connect: konton och behörigheter](active-directory-aadconnect-accounts-permissions.md#adsync-service-account). 
   
      Med den senaste versionen kan SQL-administratören nu distribuera databasen ”out of band” och därefter kan den installeras av Azure AD Connect-administratören med databasägarrättigheter. Mer information finns i [installera Azure AD Connect med hjälp av SQL-delegerade administratörsbehörigheter](active-directory-aadconnect-sql-delegation.md).

För att göra det enkelt, rekommenderar vi att användare som installerar Azure AD Connect är systemadministratörer i SQL. Men med de senaste byggena kan du nu använda ett delegerat SQL-administratörer, enligt beskrivningen i [installera Azure AD Connect med SQL-delegerade administratörsbehörigheter](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Nätverk
**F: Jag har en brandvägg, nätverksenhet eller något annat som begränsar den tid som anslutningen kan hålls öppen i nätverket. Vad mitt klientsidan timeout-tröskelvärde ska när jag använder Azure AD Connect?**  
Alla nätverksprogramvara, fysiska enheter eller något annat som begränsar den maximala tid som anslutningar ska vara aktiva bör använda ett tröskelvärde för minst fem minuter (300 sekunder) för anslutning mellan den server där Azure AD Connect-klienten är installerad och Azure Active Directory. Den här rekommendationen gäller även för alla utgivna Microsoft Identity-synkroniseringsverktyg.

**F: är enkla domännamn utan toppdomän (SLDs) stöds?**  
Medan vi rekommenderar inte att den här konfigurationen ([finns i artikeln](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)), med hjälp av Azure AD Connect-synkronisering med en enkel domän utan toppdomän stöds, så länge nätverkskonfigurationen för den enda nivå domänen fungerar korrekt.

**F: är skogar med åtskilt AD-domäner som stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar som innehåller åtskilda namnområden.

**F: är ”prickade” NetBIOS-namn stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar eller domäner där NetBIOS-namnet innehåller en punkt (.).

**F: är endast IPv6-miljö som stöds?**  
Nej, Azure AD Connect har inte stöd för en ren IPv6-miljö.

## <a name="federation"></a>Federation
**F: Vad gör jag om jag får ett e-postmeddelande som ber mig att förnya min Office 365-certifikat?**  
Anvisningar om hur du förnyar certifikatet finns i [förnya certifikat](active-directory-aadconnect-o365-certs.md).

**F: Jag har ”Uppdatera automatiskt förlitande part” för Office 365 förlitande part. Måste jag göra något när min certifikatet för tokensignering automatiskt rullar över?**  
Använd de anvisningar som beskrivs i artikeln [förnya certifikat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Miljö
**F: finns det stöd för att byta namn på servern när du har installerat Azure AD Connect?**  
Nej. Ändra servernamnet återger Synkroniseringsmotorn inte ansluta till SQL-databasinstans och kan inte starta tjänsten.

## <a name="identity-data"></a>Identitetsdata
**F: Varför matchar inte den lokala UPN i attributet userPrincipalName (UPN) i Azure AD?**  
Information finns i följande artiklar:

* [Användarnamn i Office 365, Azure eller Intune matchar inte den lokala UPN eller alternativa inloggnings-ID](https://support.microsoft.com/kb/2523192)
* [Ändringarna synkroniserats inte av verktyget Azure Active Directory-synkronisering när du har ändrat UPN-namnet för ett användarkonto om du vill använda en annan federerad domän](https://support.microsoft.com/kb/2669550)

Du kan också konfigurera Azure AD för att tillåta Synkroniseringsmotorn att uppdatera UPN-namnet, enligt beskrivningen i [funktioner i Azure AD Connect sync service](active-directory-aadconnectsyncservice-features.md).

**F: finns det stöd för ungefärlig matchning en lokal Azure AD-grupp eller kontaktobjekt med en befintlig Azure AD-grupp eller kontakta objekt?**  
Ja, den här mjuk matchning baseras på proxyAddress. Mjuk matchar stöds inte för grupper som inte är aktiverade för e-post.

**F: finns det stöd för att manuellt ange ImmutableId-attributet på en befintlig Azure AD-grupp eller kontakta objekt om du vill hård-matcha den till en lokal Azure AD-grupp eller kontakta objekt?**  
Nej, ange attributet ImmutableId manuellt på en befintlig Azure AD-grupp eller kontaktobjekt till hårda-matcha det för närvarande stöds inte.

## <a name="custom-configuration"></a>Anpassad konfiguration
**F: var dokumenteras PowerShell-cmdlets för Azure AD Connect?**  
Med undantag för de cmdletar som dokumenteras på den här platsen stöds inte andra PowerShell-cmdletar finns i Azure AD Connect för användning av kunden.

**F: kan jag använda alternativet ”Server exportservern/importera” som finns i hanteraren för synkroniseringstjänsten att flytta konfigurationen mellan servrar?**  
Nej. Det här alternativet hämtar inte alla konfigurationsinställningar och den bör inte användas. I stället använda guiden för att skapa den grundläggande konfigurationen på den andra servern och Använd Regelredigeraren sync för att generera PowerShell-skript för att flytta alla anpassade regler mellan servrar. Mer information finns i [Swingmigrering](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**F: kan cachelagras lösenord för Azure-inloggningssidan och kan den här cachelagring förhindras eftersom den innehåller ett inkommande lösenord-element med den *automatisk komplettering = ”false”* attributet?**  
För närvarande kan ändra HTML-attribut för den **lösenord** fältet, inklusive automatisk komplettering-taggen stöds inte. Vi arbetar med en funktion som gör för anpassade JavaScript, vilket låter dig lägga till valfritt attribut så den **lösenord** fält.

**F: Mina Azure-inloggningssida visar användarnamnen för användare som har tidigare loggat in. Det här beteendet stängas av?**  
För närvarande kan ändra HTML-attribut för den **lösenord** indatafältet, inklusive automatisk komplettering-taggen stöds inte. Vi arbetar med en funktion som gör för anpassade JavaScript, vilket låter dig lägga till valfritt attribut så den **lösenord** fält.

**F: finns det ett sätt att förhindra att samtidiga sessioner?**  
Nej.

## <a name="auto-upgrade"></a>Automatisk uppgradering

**F: Vad är fördelarna och konsekvenserna av att använda automatisk uppgradering?**  
Vi uppmanar alla kunder att aktivera automatisk uppgradering för sina Azure AD Connect-installationen. Fördelen är att du alltid får de senaste korrigeringarna, inklusive säkerhetsuppdateringar säkerhetsproblem som har hittats i Azure AD Connect. Uppgraderingsprocessen är enkel och problemfri och sker automatiskt när en ny version är tillgänglig. Tusentals kunder för Azure AD Connect kan du använda automatisk uppgradering med varje ny version.

Processen för automatisk Klientuppgradering upprättar alltid först om en installation är kvalificerad för automatisk uppgradering. Om den är berättigade, utförs uppgraderingen och testas. Processen omfattar även söker efter anpassade ändringar i regler och specifika miljöfaktorer. Om testerna visar att en uppgradering är misslyckas, återställs automatiskt den tidigare versionen.

Beroende på storleken på miljön, kan ta ett par timmar. Medan uppgraderingen pågår, sker ingen synkronisering mellan Windows Server Active Directory och Azure AD.

**F: Jag har fått ett e-postmeddelande som uppmanar mig som min automatisk uppgradering fungerar inte längre och jag behöver installera en ny version. Varför behöver jag göra detta?**  
Förra året släppte vi en version av Azure AD Connect som under vissa omständigheter kan ha inaktiverat funktionen Automatiska uppgraderingar på servern. Vi har åtgärdats i Azure AD Connect version 1.1.750.0. Om du har drabbats av problemet, kan du lösa det genom att köra ett PowerShell-skript för att reparera den eller genom att uppgradera manuellt till den senaste versionen av Azure AD Connect. 

Att köra PowerShell-skriptet [ladda ned skriptet](https://aka.ms/repairaadconnect) och kör den på din Azure AD Connect-server i en administrativ PowerShell-fönster. Lär dig hur du kör skriptet [visa den här korta videon](https://aka.ms/repairaadcau).

Om du vill uppgradera manuellt, måste du hämta och kör den senaste versionen av filen AADConnect.msi.
 
-  Om din nuvarande version är äldre än 1.1.750.0, [ladda ned och uppgradera till den senaste versionen](https://www.microsoft.com/download/details.aspx?id=47594).
- Om din Azure AD Connect-version är 1.1.750.0 eller senare, ingen ytterligare åtgärd krävs. Du använder redan den version som innehåller automatisk Klientuppgradering korrigeringen. 

**F: Jag har fått ett e-postmeddelande som talar om att uppgradera till den senaste versionen för att aktivera automatisk uppgradering. Jag använder version 1.1.654.0. Behöver jag uppgradera?**  
Ja, du måste uppgradera till version 1.1.750.0 eller senare för att aktivera automatisk uppgradering. [Ladda ned och uppgradera till den senaste versionen](https://www.microsoft.com/download/details.aspx?id=47594).

**F: Jag har fått ett e-postmeddelande som talar om att uppgradera till den senaste versionen för att aktivera automatisk uppgradering. Om jag har använt PowerShell för att aktivera automatisk uppgradering, fortfarande behöver jag installera den senaste versionen?**  
Ja, du behöver uppgradera till version 1.1.750.0 eller senare. Automatisk Klientuppgradering problemet finns i versioner innan 1.1.750.0 minimera inte när du aktiverar tjänsten Automatiska uppgraderingar med PowerShell.

**F: Jag vill uppgradera till en nyare version, men jag är inte säker som har installerat Azure AD Connect och vi har inte det användarnamn och lösenord. Måste jag det?**
Du behöver inte känna till användarnamn och lösenord som användes ursprungligen för att uppgradera Azure AD Connect. Använda Azure AD-konto som har rollen Global administratör.

**F: hur kan jag ta reda på vilken version av Azure AD Connect jag använder?**  
Om du vill kontrollera vilken version av Azure AD Connect är installerat på din server, gå till Kontrollpanelen och leta upp den installerade versionen av Microsoft Azure AD Connect genom att välja **program** > **program och funktioner** enligt nedan:

![Azure AD Connect-version på Kontrollpanelen](media/active-directory-aadconnect-faq/faq1.png)

**F: hur uppgraderar jag till den senaste versionen av Azure AD Connect?**  
Läs hur du uppgraderar till den senaste versionen i [Azure AD Connect: uppgradera från en tidigare version till senast](active-directory-aadconnect-upgrade-previous-version.md). 

**F: vi redan uppgraderats till den senaste versionen av Azure AD Connect förra året. Måste jag uppgradera igen?**  
Azure AD Connect-teamet gör frekventa uppdateringar till tjänsten. Om du vill dra nytta av felkorrigeringar och säkerhetsuppdateringar samt nya funktioner, är det viktigt att servern är uppdaterad med den senaste versionen. Om du aktiverar automatisk uppgradering uppdateras din programvaruversion automatiskt. Versionshistorik för Azure AD Connect finns i [Azure AD Connect: versionshistorik](active-directory-aadconnect-version-history.md).

**F: hur lång tid tar det för att utföra uppgraderingen och vilken inverkan på Mina användare?**  
Den tid som behövs för att uppgradera beror på storleken på din klient. För stora organisationer kan det vara bäst att utföra uppgraderingen på kvällar eller helger. Inga synkroniseringsåtgärden äger rum under uppgraderingen.

**F: jag tror att jag uppgraderat till Azure AD Connect, men Office-portalen nämner fortfarande DirSync. Varför är detta?**  
Office-teamet arbetar för att hämta Office-uppdateringar för portalen att visa produktnamnet på aktuella. Den visar inte vilket sync-verktyg som du använder.

**F: mitt automatiska uppgraderingar status är Stopped, ”pausad”. Varför är det pausat? Bör jag aktivera den?**  
En bugg introducerades i en tidigare version som under vissa omständigheter, lämnar du automatisk Klientuppgradering statusen inställd på ”pausad”. Att manuellt aktivera det är tekniskt möjligt, men kräver flera komplexa steg. Det bästa du kan göra är att installera den senaste versionen av Azure AD Connect.

**F: mitt företag har strikta ändringshantering krav och jag vill styra när den har push-installeras. Kan jag styra när automatisk uppgradering startas?**  
Nej, det finns ingen sådan funktion idag. Funktionen utvärderas i en kommande version.

**F: Jag får ett e-postmeddelande om automatisk uppgradering misslyckades? Hur vet jag att det lyckades?**  
Du meddelas inte om resultatet vid uppgraderingen. Funktionen utvärderas i en kommande version.

**F: du publicerar en tidslinje för när du planerar att skicka ut automatiska uppgraderingar?**  
Automatisk uppgradering är det första steget i processen för versionen av en nyare version. När det finns en ny version, uppgraderingar push-installeras automatiskt. Nyare versioner av Azure AD Connect har tillkännagivits i förväg i den [översikt över Azure AD](../fundamentals/whats-new.md).

**F: kan automatisk uppgradering också uppgradera Azure AD Connect Health?**  
Ja, automatisk uppgradering uppgraderar också Azure AD Connect Health.

**F: du också automatiska uppgraderingar Azure AD Connect-servrar i mellanlagringsläge?**  
Ja, du kan automatisk Klientuppgradering en Azure AD Connect-server som är i mellanlagringsläge.

**F: om automatisk uppgraderingen misslyckas och min Azure AD Connect-servern startar inte, vad bör jag?**  
I sällsynta fall startar inte tjänsten Azure AD Connect när du utför uppgraderingen. I dessa fall åtgärdar att servern startas om vanligtvis problemet. Öppna ett supportärende om Azure AD Connect-tjänsten fortfarande inte startar. Mer information finns i [skapar en tjänstbegäran att kontakta supporten för Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**F: Jag vet inte vilka risker är när jag uppgraderar till en nyare version av Azure AD Connect. Kan du anropa mig för att hjälpa mig med uppgraderingen?**  
Om du behöver för att uppgradera till en nyare version av Azure AD Connect, öppna ett supportärende på [skapar en tjänstbegäran att kontakta supporten för Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Felsökning
**F: hur kan jag få hjälp med Azure AD Connect?**

[Söka i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Sök KB för tekniska lösningar på vanliga problem för reparation om support för Azure AD Connect.

[Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Sök efter tekniska frågor och svar eller ställa egna frågor genom att gå till [Azure AD-community](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Få support för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
