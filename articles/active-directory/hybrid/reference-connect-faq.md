---
title: Vanliga frågor och svar om Azure Active Directory Connect – | Microsoft-dokument
description: Den här artikeln besvarar vanliga frågor om Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5aa30bc819531ee8cc9cd337648a6cbc661bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149821"
---
# <a name="azure-active-directory-connect-faq"></a>Vanliga frågor och svar om Azure Active Directory Connect

## <a name="general-installation"></a>Allmän installation

**F: Hur kan jag härda min Azure AD Connect-server för att minska säkerhetsattackytan?**

Microsoft rekommenderar att du härdar din Azure AD Connect-server för att minska säkerhetsattackytan för den här kritiska komponenten i IT-miljön.  Om du följer rekommendationerna nedan minskar säkerhetsriskerna för din organisation.

* Distribuera Azure AD Connect på en domänansluten server och begränsa administrativ åtkomst till domänadministratörer eller andra hårt kontrollerade säkerhetsgrupper

Du kan läsa mer här: 

* [Skydda administratörsgrupper](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Skydda inbyggda administratörskonton](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Säkerhetsförbättring och underhåll genom att minska angreppsytor](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Minska Active Directory-angreppsytan](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

**F: Fungerar installationen om Global Admin för Azure Active Directory (Azure AD) har tvåfaktorsautentisering (2FA) aktiverat?**  
Från och med byggandena i februari 2016 stöds det här scenariot.

**F: Finns det något sätt att installera Azure AD Connect utan uppsikt?**  
Azure AD Connect-installation stöds bara när du använder installationsguiden. En obevakad, tyst installation stöds inte.

**F: Jag har en skog där en domän inte kan kontaktas. Hur installerar jag Azure AD Connect?**  
Från och med byggandena i februari 2016 stöds det här scenariot.

**F: Fungerar hälsoagenten För Azure Active Directory Domain Services (Azure AD DS) på serverkärnan?**  
Ja. När du har installerat agenten kan du slutföra registreringsprocessen med hjälp av följande PowerShell-cmdlet: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**F: Stöder Azure AD Connect synkronisering från två domäner till en Azure AD?**  
Ja, det här scenariot stöds. Se [flera domäner](how-to-connect-install-multiple-domains.md).
 
**F: Kan du ha flera anslutningsappar för samma Active Directory-domän i Azure AD Connect?**  
Nej, flera kopplingar för samma AD-domän stöds inte. 

**F: Kan jag flytta Azure AD Connect-databasen från den lokala databasen till en fjärr-SQL Server-instans?**   
Ja, följande steg ger allmän vägledning om hur du gör detta. Vi arbetar för närvarande med ett mer detaljerat dokument.
1. Säkerhetskopiera LocalDB ADSync-databasen.
Det enklaste sättet att göra detta är att använda SQL Server Management Studio installerat på samma dator som Azure AD Connect. Anslut till *(LocalDb).\ADSync*och säkerhetskopiera sedan ADSync-databasen.

2. Återställ ADSync-databasen till fjärr-SQL Server-instansen.

3. Installera Azure AD Connect mot den befintliga [fjärr-SQL-databasen](how-to-connect-install-existing-database.md).
   Artikeln visar hur du migrerar till att använda en lokal SQL-databas. Om du migrerar till att använda en fjärr-SQL-databas måste du i steg 5 i processen också ange ett befintligt tjänstkonto som Windows Sync-tjänsten ska köras som. Det här tjänstkontot för synkroniseringsmotor beskrivs här:
   
      **Använd ett befintligt tjänstkonto**: Som standard använder Azure AD Connect ett virtuellt tjänstkonto för de synkroniseringstjänster som ska användas. Om du använder en sql server-instans på fjärrområdet eller använder en proxy som kräver autentisering använder du ett hanterat tjänstkonto eller ett tjänstkonto i domänen och känner till lösenordet. I detta fall anger du det konto som ska användas. Kontrollera att användare som kör installationen är systemadministratörer i SQL så att inloggningsuppgifter för tjänstkontot kan skapas. Mer information finns i [Azure AD Connect-konton och behörigheter](reference-connect-accounts-permissions.md#adsync-service-account). 
   
      Med den senaste versionen kan SQL-administratören nu distribuera databasen ”out of band” och därefter kan den installeras av Azure AD Connect-administratören med databasägarrättigheter. Mer information finns i [Installera Azure AD Connect med hjälp av SQL-delegerade administratörsbehörigheter](how-to-connect-install-sql-delegation.md).

För att hålla det enkelt rekommenderar vi att användare som installerar Azure AD Connect är systemadministratörer i SQL. Men med de senaste versionerna kan du nu använda delegerade SQL-administratörer, enligt beskrivningen i [Installera Azure AD Connect med SQL-delegerade administratörsbehörigheter](how-to-connect-install-sql-delegation.md).

**F: Vilka är några av de bästa metoderna från fältet?**  

Följande är ett informationsdokument som presenterar några av de bästa metoder som teknik, support och våra konsulter har utvecklat under åren.  Detta visas i en punktlista som snabbt kan refereras.  Även om den här listan försöker vara omfattande kan det finnas ytterligare metodtips som kanske inte har gjorts på listan ännu.

- Om du använder Full SQL bör den förbli lokal kontra fjärr
    - Färre hopp
    - Enklare att felsöka
    - Mindre komplexitet
    - Måste ange resurser till SQL och tillåta omkostnader för Azure AD Connect och OS
- Bypass Proxy om det alls är möjligt, om du inte kan kringgå proxy måste du se till att timeout-värdet är större än 5 minuter.
- Om proxy krävs måste du lägga till proxyn i filen machine.config
- Var medveten om lokala SQL-jobb och -underhåll och hur de kommer att påverka Azure AD Connect – särskilt omindexering
- Se till att än DNS kan lösa externt
- Kontrollera att [serverspecifikationerna](how-to-connect-install-prerequisites.md#hardware-requirements-for-azure-ad-connect) är per rekommendation oavsett om du använder fysiska eller virtuella servrar
- Se till att om du använder en virtuell server som resurser som krävs är dedikerade
- Se till att disk- och diskkonfigurationen uppfyller de bästa metoderna för SQL Server
- Installera och konfigurera Azure AD Connect Health för övervakning
- Använd tröskelvärdet för borttagning som är inbyggt i Azure AD Connect.
- Granska noggrant utgivningsuppdateringar som ska förberedas för alla ändringar och nya attribut som kan läggas till
- Säkerhetskopiera allt
    - Tangenter för säkerhetskopiering
    - Regler för säkerhetskopiering av synkronisering
    - Konfiguration av säkerhetskopieringsserver
    - Säkerhetskopiera SQL-databas
- Se till att det inte finns några säkerhetskopieringsagenter från tredje part som säkerhetskopierar SQL utan SQL VSS Writer (vanliga i virtuella servrar med ögonblicksbilder från tredje part)
- Begränsa mängden anpassade synkroniseringsregler som används när de lägger till komplexitet
- Behandla Azure AD Connect-servrar som nivå 0-servrar
- Var slug att ändra molnsynkroniseringsregler utan större förståelse för effekten och rätt affärsdrivare
- Kontrollera att rätt URL och brandväggsportar är öppna för support för Azure AD Connect och Azure AD Connect Health
- Utnyttja det molnfiltrerade attributet för att felsöka och förhindra fiktiva objekt
- Med mellanlagringsservern se till att du använder Azure AD Connect Configuration Documenter för konsekvens mellan servrar
- Mellanlagringsservrar bör finnas i separata datacenter (Fysiska platser
- Mellanlagringsservrar är inte avsedda att vara en lösning med hög tillgänglighet, men du kan ha flera mellanlagringsservrar
- Att införa en "Lag" Mellanstationer kan minska vissa potentiella driftstopp i händelse av fel
- Testa och validera alla uppgraderingar på mellanlagringsservern först
- Verifiera alltid export innan du växlar över till mellanlagringsservern.  Utnyttja mellanlagringsservern för fullständig import och fullständiga synkroniseringar för att minska affärspåverkan
- Behåll versionskonsekvensen mellan Azure AD Connect-servrar så mycket som möjligt 

**F: Kan jag tillåta att Azure AD Connect skapar Azure AD Connector-kontot på arbetsgruppsdatorn?**
Nej.  För att Azure AD Connect ska kunna skapa Azure AD Connector-kontot automatiskt måste datorn vara domänansluten.  

## <a name="network"></a>Nätverk
**F: Jag har en brandvägg, en nätverksenhet eller något annat som begränsar den tid som anslutningarna kan vara öppna i nätverket. Vad ska min tidsgräns för klientsidan vara när jag använder Azure AD Connect?**  
All nätverksprogramvara, fysiska enheter eller något annat som begränsar den maximala tid som anslutningar kan förbli öppna bör använda ett tröskelvärde på minst fem minuter (300 sekunder) för anslutning mellan servern där Azure AD Connect-klienten är installerad och Azure Active Directory. Den här rekommendationen gäller även för alla tidigare utgivna Microsoft Identity-synkroniseringsverktyg.

**F: Stöds SPEKTringar (SINGLE Label Domains)?**  
Även om vi starkt rekommenderar mot den här nätverkskonfigurationen ([se artikel),](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)med azure AD Connect-synkronisering med en enda etikettdomän stöds, så länge nätverkskonfigurationen för domänen på en nivå fungerar korrekt.

**F: Stöds skogar med osammanhängande AD-domäner?**  
Nej, Azure AD Connect stöder inte lokala skogar som innehåller osammanhängande namnområden.

**F: Stöds "prickade" NetBIOS-namn?**  
Nej, Azure AD Connect stöder inte lokala skogar eller domäner där NetBIOS-namnet innehåller en punkt (.).

**F: Stöds ren IPv6-miljö?**  
Nej, Azure AD Connect stöder inte en ren IPv6-miljö.

**F:I har en miljö med flera skogar och nätverket mellan de två skogarna använder NAT (Network Address Translation). Stöds Azure AD Connect mellan dessa två skogar?**</br>
Nej, det går inte att använda Azure AD Connect via NAT. 

## <a name="federation"></a>Federation
**F: Vad gör jag om jag får ett e-postmeddelande där jag uppmanas att förnya mitt Office 365-certifikat?**  
Mer information om hur du förnyar certifikatet finns i [förnya certifikat](how-to-connect-fed-o365-certs.md).

**F: Jag har angett "Uppdatera förlitande part automatiskt" för den förlitande parten för Office 365. Måste jag vidta några åtgärder när mitt tokensigneringscertifikat automatiskt överstökar?**  
Använd vägledningen som beskrivs i artikeln [förnya certifikat](how-to-connect-fed-o365-certs.md).

## <a name="environment"></a>Miljö
**F: Stöds det att byta namn på servern efter att Azure AD Connect har installerats?**  
Nej. Om du ändrar servernamnet kan synkroniseringsmotorn inte ansluta till SQL-databasinstansen och tjänsten kan inte starta.

**F: Stöds nästa generations synkroniseringsregler (Cryptographic) på en FIPS-aktiverad dator?**  
Nej.  De stöds inte.

**F. Om jag har inaktiverat en synkroniserad enhet (till exempel HAADJ) i Azure-portalen, varför aktiveras den igen?**<br>
Synkroniserade enheter kan skapas eller bemästras lokalt. Om en synkroniserad enhet är aktiverad lokalt kan den återaktiveras i Azure-portalen även om den tidigare inaktiverats av en administratör. Om du vill inaktivera en synkroniserad enhet använder du den lokala Active Directory för att inaktivera datorkontot.

**F. Om jag blockerar användarinloggning på Office 365- eller Azure AD-portalen för synkroniserade användare, varför blockeras den när den loggas in igen?**<br>
Synkroniserade användare kan skapas eller bemästras lokalt. Om kontot är aktiverat lokalt kan det häva blockeringen av inloggningsblocket som placerats av administratören.

## <a name="identity-data"></a>Identitetsuppgifter
**F: Varför matchar inte attributet userPrincipalName (UPN) i Azure AD det lokala UPN?**  
Mer information finns i följande artiklar:

* [Användarnamn i Office 365, Azure eller Intune matchar inte det lokala UPN- eller alternativa inloggnings-ID:t](https://support.microsoft.com/kb/2523192)
* [Ändringarna synkroniseras inte av Synkroniseringsverktyget för Azure Active Directory när du har ändrat UPN för ett användarkonto så att det använder en annan federerad domän](https://support.microsoft.com/kb/2669550)

Du kan också konfigurera Azure AD så att synkroniseringsmotorn kan uppdatera UPN, enligt beskrivningen i [Azure AD Connect-synkroniseringstjänstfunktioner](how-to-connect-syncservice-features.md).

**F: Stöds det för att mjuka matcha en lokal Azure AD-grupp eller kontaktobjekt med en befintlig Azure AD-grupp eller kontaktobjekt?**  
Ja, den här mjuka matchningen baseras på proxyAddress. Mjuk matchning stöds inte för grupper som inte är e-postaktiverade.

**F: Stöds det att manuellt ställa in Attributet ImmutableId på en befintlig Azure AD-grupp eller kontaktobjekt så att det är svårt att matcha det med en lokal Azure AD-grupp eller kontaktobjekt?**  
Nej, manuellt ange Attributet ImmutableId på en befintlig Azure AD-grupp eller kontaktobjekt för hårdmatchning stöds det för närvarande inte.

## <a name="custom-configuration"></a>Anpassad konfiguration
**F: Var dokumenteras PowerShell-cmdlets för Azure AD Connect?**  
Med undantag för de cmdlets som dokumenteras på den här webbplatsen stöds inte andra PowerShell-cmdletar som finns i Azure AD Connect för kundanvändning.

**F: Kan jag använda alternativet "Serverexport/serverimport" som finns i Synkroniseringstjänsthanteraren för att flytta konfigurationen mellan servrar?**  
Nej. Det här alternativet hämtar inte alla konfigurationsinställningar och bör inte användas. Använd i stället guiden för att skapa baskonfigurationen på den andra servern och använda synkroniseringsregelredigeraren för att generera PowerShell-skript för att flytta en anpassad regel mellan servrar. Mer information finns i [Swing-migrering](how-to-upgrade-previous-version.md#swing-migration).

**F: Kan lösenord cachelagras för Inloggningssidan i Azure och kan den här cachelagringen förhindras eftersom den innehåller ett inmatningselement för lösenord med *attributet komplettera automatiskt = attributet "false"?***  
Det går för närvarande inte att ändra HTML-attributen för fältet **Lösenord,** inklusive taggen komplettera automatiskt. Vi arbetar för närvarande på en funktion som möjliggör anpassade JavaScript, som låter dig lägga till alla attribut till fältet **Lösenord.**

**F: Inloggningssidan för Azure visar användarnamnen för användare som tidigare har loggat in. Kan det här beteendet stängas av?**  
Det går för närvarande inte att ändra HTML-attributen för indatafältet **Lösenord,** inklusive taggen komplettera automatiskt. Vi arbetar för närvarande på en funktion som möjliggör anpassade JavaScript, som låter dig lägga till alla attribut till fältet **Lösenord.**

**F: Finns det något sätt att förhindra samtidiga sessioner?**  
Nej.

## <a name="auto-upgrade"></a>Automatisk uppgradering

**F: Vilka är fördelarna och konsekvenserna av att använda automatisk uppgradering?**  
Vi råder alla kunder att aktivera automatisk uppgradering för deras Azure AD Connect-installation. Fördelen är att du alltid får de senaste korrigeringsfilerna, inklusive säkerhetsuppdateringar för säkerhetsproblem som har hittats i Azure AD Connect. Uppgraderingsprocessen är smärtfri och sker automatiskt så snart en ny version är tillgänglig. Tusentals Azure AD Connect-kunder använder automatisk uppgradering med varje ny version.

Processen för automatisk uppgradering fastställer alltid först om en installation är berättigad till automatisk uppgradering. Om den är berättigad utförs och testas uppgraderingen. I processen ingår även att söka anpassade ändringar av regler och specifika miljöfaktorer. Om testerna visar att en uppgradering misslyckas återställs den tidigare versionen automatiskt.

Beroende på miljöns storlek kan processen ta ett par timmar. Medan uppgraderingen pågår sker ingen synkronisering mellan Windows Server Active Directory och Azure AD.

**F: Jag fick ett e-postmeddelande som berättade för mig att min automatiska uppgradering inte längre fungerar och jag måste installera en ny version. Varför måste jag göra det här?**  
Förra året släppte vi en version av Azure AD Connect som under vissa omständigheter kan ha inaktiverat funktionen för automatisk uppgradering på din server. Vi har åtgärdat problemet i Azure AD Connect version 1.1.750.0. Om du har påverkats av problemet kan du minska det genom att köra ett PowerShell-skript för att reparera det eller genom att manuellt uppgradera till den senaste versionen av Azure AD Connect. 

Om du vill köra PowerShell-skriptet [laddar du ned skriptet](https://aka.ms/repairaadconnect) och kör det på din Azure AD Connect-server i ett administrativt PowerShell-fönster. Om du vill veta hur du kör skriptet kan du visa den [här korta videon](https://aka.ms/repairaadcau).

Om du vill uppgradera manuellt måste du hämta och köra den senaste versionen av filen AADConnect.msi.
 
-  Om din nuvarande version är äldre än 1.1.750.0 [laddar du ned och uppgraderar till den senaste versionen](https://www.microsoft.com/download/details.aspx?id=47594).
- Om din Azure AD Connect-version är 1.1.750.0 eller senare krävs ingen ytterligare åtgärd. Du använder redan den version som innehåller korrigeringen för automatisk uppgradering. 

**F: Jag fick ett e-postmeddelande som sa åt mig att uppgradera till den senaste versionen för att återaktivera automatisk uppgradering. Jag använder version 1.1.654.0. Behöver jag uppgradera?**  
Ja, du måste uppgradera till version 1.1.750.0 eller senare för att återaktivera automatisk uppgradering. [Ladda ner och uppgradera till den senaste versionen](https://www.microsoft.com/download/details.aspx?id=47594).

**F: Jag fick ett e-postmeddelande som sa åt mig att uppgradera till den senaste versionen för att återaktivera automatisk uppgradering. Om jag har använt PowerShell för att aktivera automatisk uppgradering, måste jag fortfarande installera den senaste versionen?**  
Ja, du måste fortfarande uppgradera till version 1.1.750.0 eller senare. Om du aktiverar tjänsten för automatisk uppgradering med PowerShell minskar inte problemet med automatisk uppgradering som hittades i versioner före 1.1.750.0.

**F: Jag vill uppgradera till en nyare version men jag är inte säker på vem som installerade Azure AD Connect, och vi har inte användarnamn och lösenord. Behöver vi det här?**
Du behöver inte känna till användarnamnet och lösenordet som ursprungligen användes för att uppgradera Azure AD Connect. Använd alla Azure AD-konton som har rollen Global administratör.

**F: Hur hittar jag vilken version av Azure AD Connect jag använder?**  
Om du vill kontrollera vilken version av Azure AD Connect som är installerad på servern går du till Kontrollpanelen och slår upp den installerade versionen av Microsoft Azure AD Connect genom att välja **Programprogram** > **och -funktioner**, som du ser här:

![Azure AD Connect-version på Kontrollpanelen](./media/reference-connect-faq/faq1.png)

**F: Hur uppgraderar jag till den senaste versionen av Azure AD Connect?**  
Mer information om hur du uppgraderar till den senaste versionen finns i [Azure AD Connect: Uppgradera från en tidigare version till den senaste](how-to-upgrade-previous-version.md). 

**F: Vi har redan uppgraderat till den senaste versionen av Azure AD Connect förra året. Behöver vi uppgradera igen?**  
Azure AD Connect-teamet gör frekventa uppdateringar av tjänsten. För att dra nytta av buggfixar och säkerhetsuppdateringar samt nya funktioner är det viktigt att hålla servern uppdaterad med den senaste versionen. Om du aktiverar automatisk uppgradering uppdateras programvaruversionen automatiskt. Information om hur du hittar versionsversionen av Azure AD Connect finns i [Azure AD Connect: Versionsversionshistorik](reference-connect-version-history.md).

**F: Hur lång tid tar det att utföra uppgraderingen och hur påverkar mina användare?**  
Hur mycket tid som behövs för att uppgradera beror på din klientstorlek. För större organisationer kan det vara bäst att utföra uppgraderingen på kvällen eller helgen. Under uppgraderingen sker ingen synkroniseringsaktivitet.

**F: Jag tror att jag har uppgraderat till Azure AD Connect, men Office-portalen nämner fortfarande DirSync. Varför är det så här?**  
Office-teamet arbetar med att få uppdateringar av Office-portalen så att de återspeglar det aktuella produktnamnet. Det återspeglar inte vilket synkroniseringsverktyg du använder.

**F: Min status för automatisk uppgradering säger "Suspenderad". Varför är det tillfälligt? Ska jag aktivera det?**  
Ett fel infördes i en tidigare version som under vissa omständigheter lämnar statusen för automatisk uppgradering inställd på "Suspenderad". Manuellt aktivera det är tekniskt möjligt men skulle kräva flera komplexa steg. Det bästa du kan göra är att installera den senaste versionen av Azure AD Connect.

**F: Mitt företag har strikta krav på ändringshantering och jag vill kontrollera när det trycks ut. Kan jag styra när automatisk uppgradering startas?**  
Nej, det finns ingen sådan funktion idag. Funktionen utvärderas för en framtida version.

**F: Får jag ett e-postmeddelande om den automatiska uppgraderingen misslyckades? Hur vet jag att det lyckades?**  
Du kommer inte att meddelas om resultatet av uppgraderingen. Funktionen utvärderas för en framtida version.

**F: Publicerar du en tidslinje för när du planerar att skjuta ut automatiska uppgraderingar?**  
Automatisk uppgradering är det första steget i utgivningsprocessen för en nyare version. När det finns en ny version, uppgraderingar skjuts automatiskt. Nyare versioner av Azure AD Connect meddelas i azure [AD-översikten](../fundamentals/whats-new.md).

**F: Uppgraderar automatisk uppgradering också Azure AD Connect Health?**  
Ja, automatisk uppgradering uppgraderar också Azure AD Connect Health.

**F: Uppgraderar du även Azure AD Connect-servrar automatiskt i mellanlagringsläge?**  
Ja, du kan uppgradera en Azure AD Connect-server automatiskt som är i mellanlagringsläge.

**F: Om automatisk uppgradering misslyckas och min Azure AD Connect-server inte startar, vad ska jag göra?**  
I sällsynta fall startar inte Azure AD Connect-tjänsten när du har utföra uppgraderingen. I dessa fall löser omstart av servern vanligtvis problemet. Om Azure AD Connect-tjänsten fortfarande inte startar öppnar du en supportbiljett. Mer information finns i [Skapa en tjänstbegäran om att kontakta Office 365-supporten](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/). 

**F: Jag är inte säker på vilka riskerna är när jag uppgraderar till en nyare version av Azure AD Connect. Kan du ringa mig för att hjälpa mig med uppgraderingen?**  
Om du behöver hjälp med att uppgradera till en nyare version av Azure AD Connect öppnar du en supportbiljett på [Skapa en tjänstbegäran för att kontakta Office 365-supporten](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/).

## <a name="troubleshooting"></a>Felsökning
**F: Hur kan jag få hjälp med Azure AD Connect?**

[Sök i Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/search/result.aspx?q=azure+active+directory+connect)

* Sök i KB efter tekniska lösningar för vanliga problem med break-fix om support för Azure AD Connect.

[Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* Sök efter tekniska frågor och svar eller ställ dina egna frågor genom att gå till [Azure AD-communityn](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Få support för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)

**F: Varför ser jag händelser 6311 och 6401 inträffar efter synkroniseringsstegfel?**

Händelserna 6311 - **Servern påträffade ett oväntat fel när en motringning utfördes** och 6401 - **Styrenheten för hanteringsagenten påträffade ett oväntat fel** - loggas alltid efter ett synkroniseringsstegfel. För att lösa dessa fel måste du rensa synkroniseringsstegfelen.  Mer information finns i [Felsöka fel under synkronisering](tshoot-connect-sync-errors.md) och [felsöka objektsynkronisering med Azure AD Connect-synkronisering](tshoot-connect-objectsync.md)
