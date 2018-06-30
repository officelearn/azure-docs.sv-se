---
title: Azure Active Directory Connect vanliga frågor och svar - | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor och svar om Azure AD Connect.
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
ms.openlocfilehash: 7edabc99da5e1466e848336c647a33213c9edd8b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132995"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect – vanliga frågor och svar

## <a name="general-installation"></a>Allmän installation
**F: installationen fungerar om den globala administratören i Azure Active Directory (AD Azure) har tvåfaktorsautentisering (2FA) aktiverad?**  
Det här scenariot stöds från och med februari 2016-versioner.

**F: finns det ett sätt att installera Azure AD Connect obevakad?**  
Azure AD Connect-installationen stöds bara när du använder installationsguiden. En obevakad, tyst installation stöds inte.

**F: Jag har en skog där en domän inte kan kontaktas. Hur installerar Azure AD Connect?**  
Det här scenariot stöds från och med februari 2016-versioner.

**F: kan Azure Active Directory Domain Services (Azure AD DS) health-agenten fungerar på server core?**  
Ja. När du har installerat agenten kan du slutföra registreringen med följande PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**F: kan Azure AD Connect synkroniserar stöd från två domäner till på Azure AD?**  
Ja, det här scenariot stöds. Referera till [flera domäner](active-directory-aadconnect-multiple-domains.md).
 
**F: kan du ha flera kopplingar för samma Active Directory-domän i Azure AD Connect?**  
Ingen, flera kopplingar för samma AD-domänen stöds inte. 

**F: kan jag flytta Azure AD Connect-databasen från den lokala databasen till en fjärrinstans av SQL Server?**   
Ja, ange följande allmänna riktlinjer om hur du gör detta. Vi arbetar på ett mer detaljerad dokument.
1. Säkerhetskopiera LocalDB ADSync-databasen.
Det enklaste sättet att göra detta är att använda SQL Server Management Studio installerat på samma dator som Azure AD Connect. Ansluta till *(localdb)\.\ADSync*, och sedan säkerhetskopiera databasen ADSync.

2. Återställa databasen ADSync till fjärransluten SQL Server-instans.

3. Installera Azure AD Connect mot den befintliga [fjärranslutna SQL-databas](active-directory-aadconnect-existing-database.md).
   Artikeln visar hur du migrerar till med hjälp av en lokal SQL-databas. Om du migrerar till att använda en fjärransluten SQL-databas i steg 5 i processen måste du också ange ett befintligt tjänstkonto som använder Windows-synkroniseringstjänsten. Den här motorn synkroniseringstjänstkontot som beskrivs här:
   
      **Använd ett befintligt tjänstkonto**: som standard använder Azure AD Connect ett virtuella tjänstkonto för Sync services för att använda. Om du använder en fjärrinstans av SQL Server eller använder en proxyserver som kräver autentisering, använder ett hanterat tjänstkonto eller ett tjänstkonto i domänen och känna till lösenordet. I detta fall anger du det konto som ska användas. Se till att användare som kör installationen är systemadministratörer i SQL så att inloggningsuppgifterna för tjänstkontot kan skapas. Mer information finns i [Azure AD Connect: konton och behörigheter](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). 
   
      Med den senaste versionen kan SQL-administratören nu distribuera databasen ”out of band” och därefter kan den installeras av Azure AD Connect-administratören med databasägarrättigheter. Mer information finns i [installera Azure AD Connect med administratörsbehörighet för SQL delegerad](active-directory-aadconnect-sql-delegation.md).

Om du vill behålla saker enkla rekommenderar vi att användare som installerar Azure AD Connect är systemadministratörer i SQL. Men med de senaste versionerna nu kan du använda delegerade administratörer för SQL, enligt beskrivningen i [installera Azure AD Connect med administratörsbehörighet för SQL delegerad](active-directory-aadconnect-sql-delegation.md).

## <a name="network"></a>Nätverk
**F: Jag har en brandvägg, nätverksenhet eller något annat som begränsar den tid som anslutningen kan hålls öppen i nätverket. Vad bör min klientsidan timeout tröskelvärdet vara när jag använder Azure AD Connect?**  
Alla nätverksprogramvara fysiska enheter eller något annat som begränsar den maximala tid som anslutningar kan förbli öppen bör använda ett tröskelvärde på minst 5 minuter (300 sekunder) för anslutningen mellan den server där Azure AD Connect-klienten är installerad och Azure Active Directory. Den här rekommendationen gäller även alla tidigare Microsoft Identity synkroniseringsverktyg.

**F: är enkla domännamn utan toppdomän (SLDs) stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar och domäner som använder SLDs.

**F: är skogar med åtskilda AD-domäner som stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar som innehåller åtskilda namnområden.

**F: är ”prickad” NetBIOS-namn som stöds?**  
Nej, Azure AD Connect har inte stöd för lokala skogar och domäner där NetBIOS-namnet innehåller en punkt (.).

**F: är rent IPv6-miljö som stöds?**  
Nej, Azure AD Connect inte stöder en ren IPv6-miljö.

## <a name="federation"></a>Federation
**F: Vad gör jag om jag får ett e-postmeddelande som frågar mig att förnya certifikatet min Office 365?**  
Anvisningar om hur du förnyar certifikatet finns [förnya certifikat](active-directory-aadconnect-o365-certs.md).

**F: Jag har ”Uppdatera automatiskt förlitande part” anges för Office 365 förlitande part. Måste jag göra något när min certifikatet för tokensignering automatiskt rullar över?**  
Använd de anvisningar som beskrivs i artikel [förnya certifikat](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Miljö
**F: finns det stöd för att byta namn på servern när du har installerat Azure AD Connect?**  
Nej. Ändra servernamnet återger Synkroniseringsmotorn inte ansluta till SQL-databasinstans och det går inte att starta tjänsten.

## <a name="identity-data"></a>Identitetsdata
**F: Varför matchar inte lokal UPN i attributet userPrincipalName (UPN) i Azure AD?**  
Information finns i följande artiklar:

* [Användarnamn i Office 365, Azure eller Intune matchar inte lokal UPN eller alternativ inloggnings-ID](https://support.microsoft.com/en-us/kb/2523192)
* [Ändringarna synkroniserats inte med verktyget Azure Active Directory-synkronisering när du ändrar UPN-namnet för ett användarkonto om du vill använda en annan federerad domän](https://support.microsoft.com/en-us/kb/2669550)

Du kan också konfigurera Azure AD för att tillåta Synkroniseringsmotorn att uppdatera UPN-namnet, enligt beskrivningen i [Azure AD Connect sync tjänstens funktioner](active-directory-aadconnectsyncservice-features.md).

**F: finns det stöd för att soft-matcha ett lokalt Azure AD-grupp eller kontaktobjekt med en befintlig Azure AD-grupp eller kontakta objektet?**  
Ja, mjuk matchningen baseras på proxyAddress. Mjuka matchar stöds inte för grupper som inte är aktiverade för e-post.

**F: finns det stöd för att manuellt ange attributet ImmutableId för en befintlig Azure AD-grupp eller kontakta objekt om du vill hårda-matcha den till en lokal Azure AD-grupp eller kontakta objektet?**  
Nej, ange attributet ImmutableId manuellt på en befintlig Azure AD-grupp- eller kontaktobjekt till hårddisken-matcha den för närvarande stöds inte.

## <a name="custom-configuration"></a>Anpassad konfiguration
**F: var dokumenteras PowerShell-cmdlets för Azure AD Connect?**  
Andra PowerShell-cmdletar finns i Azure AD Connect stöds inte för kundens användning med undantag för de cmdlets som finns dokumenterade på den här platsen.

**F: kan jag använda alternativet ”Server exportservern/importera” som hittas i Synchronization Service Manager för att flytta konfigurationen mellan servrar?**  
Nej. Det här alternativet Hämta inte alla konfigurationsinställningar och den bör inte användas. I stället använda guiden för att skapa den grundläggande konfigurationen på den andra servern och använda redigeraren för synkronisering av regeln för att generera PowerShell-skript för att flytta en anpassad regel mellan servrar. Mer information finns i [svänga migrering](active-directory-aadconnect-upgrade-previous-version.md#swing-migration).

**F: kan cachelagras lösenord för Azure inloggningssidan och kan den här cachelagring förhindras eftersom den innehåller ett lösenord indataelementet med den *autocomplete = ”false”* attributet?**  
För närvarande ändrar HTML-attribut i den **lösenord** fältet, inklusive autocomplete-taggen stöds inte. Vi arbetar på en funktion som gör det möjligt för anpassade JavaScript, vilket låter dig lägga till attribut som den **lösenord** fältet.

**F: på Azure-inloggningssida visas användarnamn för användare som har tidigare har loggat in. Det här beteendet stängas av?**  
För närvarande ändrar HTML-attribut i den **lösenord** inmatningsfält, inklusive autocomplete-taggen stöds inte. Vi arbetar på en funktion som gör det möjligt för anpassade JavaScript, vilket låter dig lägga till attribut som den **lösenord** fältet.

**F: finns det ett sätt att förhindra samtidiga sessioner?**  
Nej.

## <a name="auto-upgrade"></a>Uppgradera automatiskt

**F: Vad är fördelarna och konsekvenserna av att använda automatisk uppgradering?**  
Vi uppmanar alla kunder att aktivera automatisk uppgradering för sina Azure AD Connect-installationen. Fördelen är att du alltid får de senaste korrigeringarna, inklusive säkerhetsuppdateringar säkerhetsproblem som har hittats i Azure AD Connect. Uppgraderingsprocessen är problemfritt och sker automatiskt när en ny version är tillgänglig. Azure AD Connect kunder tusentals använda uppgradera automatiskt med varje ny utgåva.

Uppgradera automatiskt processen upprättar alltid först om en installation lämpar sig för automatisk uppgradering. Om den är berättigad, utförs uppgraderingen och testas. Processen omfattar också söker efter anpassade ändringar i regler och specifika miljöfaktorer. Om testerna visar att en uppgradering lyckas, återställs automatiskt den tidigare versionen.

Processen kan ta några timmar beroende på storleken på miljön. Medan uppgraderingen pågår, sker ingen synkronisering mellan Windows Server Active Directory och Azure AD.

**F: Jag har fått ett e-postmeddelande om att uppgradera min automatiskt fungerar inte längre som jag behöver installera en ny version. Varför måste jag göra detta?**  
Senaste året släppte vi en version av Azure AD Connect som i vissa fall kan ha inaktiverat funktionen Automatiska uppgradering på servern. Vi har löst problemet i Azure AD Connect version 1.1.750.0. Om du har drabbats av problemet, kan du minska den genom att köra ett PowerShell.skript för att reparera den eller genom att manuellt uppgradera till den senaste versionen av Azure AD Connect. 

Att köra PowerShell-skriptet [hämta skriptet](https://aka.ms/repairaadconnect) och kör den på Azure AD Connect-servern i ett administrativa PowerShell-fönster. Information om hur du kör skriptet [visa den här korta videon](https://aka.ms/repairaadcau).

Om du vill uppgradera manuellt, måste du hämta och kör den senaste versionen av filen AADConnect.msi.
 
-  Om din nuvarande version är äldre än 1.1.750.0, [ladda ned och uppgradera till den senaste versionen](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
- Om din Azure AD Connect-version är 1.1.750.0 eller senare, ingen ytterligare åtgärd krävs. Du använder redan den version som innehåller korrigeringsfilen uppgradera automatiskt. 

**F: Jag har fått ett e-postmeddelande om att uppgradera till den senaste versionen för att aktivera automatisk uppgradering på nytt. Jag använder version 1.1.654.0. Behöver jag uppgradera?**  
Ja, du måste uppgradera till version 1.1.750.0 eller senare för att aktivera automatisk uppgradering igen. [Ladda ned och uppgradera till den senaste versionen](https://www.microsoft.com/en-us/download/details.aspx?id=47594).

**F: Jag har fått ett e-postmeddelande om att uppgradera till den senaste versionen för att aktivera automatisk uppgradering på nytt. Om jag använder PowerShell för att aktivera automatisk uppgradering fortfarande behöver jag installera den senaste versionen?**  
Ja, du fortfarande behöver uppgradera till version 1.1.750.0 eller senare. Uppgradera automatiskt problemet finns i versionerna innan 1.1.750.0 minska inte när du aktiverar tjänsten Automatiska uppgradering med PowerShell.

**F: Jag vill uppgradera till en nyare version, men jag är inte säker som har installerat Azure AD Connect och vi har inte användarnamn och lösenord. Behövs detta?**
Du behöver inte känna till användarnamn och lösenord som ursprungligen användes för att uppgradera Azure AD Connect. Använda Azure AD-kontot som har rollen Global administratör.

**F: hur kan jag hitta vilken version av Azure AD Connect jag använder?**  
Om du vill kontrollera vilken version av Azure AD Connect är installerat på servern, gå till Kontrollpanelen och leta upp den installerade versionen av Microsoft Azure AD Connect genom att välja **program** > **program och funktioner** , som visas här:

![Azure AD Connect-versionen på Kontrollpanelen](media/active-directory-aadconnect-faq/faq1.png)

**F: hur uppgradera till den senaste versionen av Azure AD Connect?**  
Information om hur du uppgraderar till den senaste versionen finns [Azure AD Connect: uppgradera från en tidigare version till senast](active-directory-aadconnect-upgrade-previous-version.md). 

**F: vi redan har uppgraderats till den senaste versionen av Azure AD Connect senaste året. Vi behöver uppgradera igen?**  
Azure AD Connect-teamet gör uppdateras ofta till tjänsten. Om du vill utnyttja felkorrigeringar och säkerhetsuppdateringar samt nya funktioner, är det viktigt att servern är uppdaterad med den senaste versionen. Om du aktiverar automatisk uppgradering uppdateras din programvaruversionen automatiskt. Versionshistorik för Azure AD Connect finns [Azure AD Connect: versionshistorik](active-directory-aadconnect-version-history.md).

**F: hur lång tid tar det för att utföra uppgraderingen och vad är effekten på Mina användare?**  
Den tid som behövs för att uppgradera beror på storleken på din klient. För större organisationer kan det vara bäst att utföra uppgraderingen i kväll eller helg. Inga synkroniseringsåtgärden äger rum under uppgraderingen.

**F: jag tror att jag har uppgraderat till Azure AD Connect, men Office-portalen nämns fortfarande DirSync. Varför är detta?**  
Office-teamet arbetar för att hämta Office-portalen uppdateringar så att den återger produktnamnet på aktuella. Den visar inte vilka synkroniseringsverktyget som du använder.

**F: min uppgradera automatiskt status står ”pausad”. Varför är det avbruten? Bör jag aktivera det?**  
Ett programfel introducerades i en tidigare version som under vissa omständigheter lämnar du uppgradera automatiskt statusen inställd på ”pausad”. Att manuellt aktivera det är tekniskt möjligt, men kräver flera komplicerade steg. Det bästa som du kan göra är att installera den senaste versionen av Azure AD Connect.

**F: mitt företag har strikt ändringshantering krav och jag vill styra när den har gjort. Kan du styra när automatisk uppgradering startas?**  
Nej, det finns ingen sådan funktion idag. Funktionen utvärderas för framtida versioner.

**F: Jag får ett e-postmeddelande om det gick inte att uppgradera automatiskt? Hur vet jag att lyckades?**  
Du meddelas inte om resultatet av uppgraderingen. Funktionen utvärderas för framtida versioner.

**F: du publicerar en tidslinje för när du planerar att skicka ut automatiska uppgraderingar?**  
Automatisk uppgradering är det första steget i processen för versionen av en nyare version. När det finns en ny version, pushas uppgraderingar automatiskt. Nyare versioner av Azure AD Connect är förhand angivna i den [översikt över Azure AD](../fundamentals/whats-new.md).

**F: kan uppgradera automatiskt också uppgradera Azure AD Connect Health?**  
Ja, uppgradera automatiskt uppgraderas även Azure AD Connect Health.

**F: du också automatiska uppgradering Azure AD Connect-servrar i mellanlagringsläge?**  
Ja, du kan automatiska uppgradering en Azure AD Connect-server som är i mellanlagringsläge.

**F: Vad ska jag göra om automatisk uppgraderingen misslyckas och min Azure AD Connect-servern inte startar?**  
I sällsynta fall startar inte Azure AD Connect-tjänsten när du utför uppgraderingen. I dessa fall att servern startas om vanligtvis för att rätta till problemet. Öppna ett supportärende om Azure AD Connect-tjänsten fortfarande inte startar. Mer information finns i [skapar en tjänstbegäran att kontakta supporten för Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**F: Jag vet inte vilka risker är när jag uppgraderar till en nyare version av Azure AD Connect. Kan du anropa mig om du vill hjälpa mig med uppgraderingen?**  
Om du behöver hjälp uppgradering till en nyare version av Azure AD Connect, öppna ett supportärende på [skapar en tjänstbegäran att kontakta supporten för Office 365](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Felsökning
**F: hur kan jag få hjälp med Azure AD Connect?**

[Sök i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* Sök KB tekniska lösningar på vanliga problem med reparations om stöd för Azure AD Connect.

[Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Sök efter tekniska frågor och svar eller egna frågor genom att gå till [Azure AD-gemenskapen](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Få support för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
