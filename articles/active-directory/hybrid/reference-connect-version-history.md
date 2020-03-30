---
title: 'Azure AD Connect: Versionsversionshistorik | Microsoft-dokument'
description: I den här artikeln visas alla versioner av Azure AD Connect och Azure AD Sync
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 10/7/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb295ca561bfa69805362182dc60ce908e1f206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331144"
---
# <a name="azure-ad-connect-version-release-history"></a>Versionshistorik för Azure AD Connect
Azure Active Directory (Azure AD) -teamet uppdaterar regelbundet Azure AD Connect med nya funktioner. Alla tillägg gäller inte för alla målgrupper.


Den här artikeln är utformad för att hjälpa dig att hålla reda på de versioner som har släppts och för att förstå vilka ändringarna som finns i den senaste versionen.

Den här tabellen är en lista över relaterade ämnen:

Hjälpavsnitt |  Information
--------- | --------- |
Steg för att uppgradera från Azure AD Connect | Olika metoder för att [uppgradera från en tidigare version till den senaste](how-to-upgrade-previous-version.md) Azure AD Connect-versionen.
Nödvändiga behörigheter | Behörigheter som krävs för att installera en uppdatering finns i [konton och behörigheter](reference-connect-accounts-permissions.md#upgrade).
Ladda ned| [Ladda ned Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>Att släppa en ny version av Azure AD Connect är en process som kräver flera steg för kvalitetskontroll för att säkerställa tjänstens driftsfunktioner, och medan vi går igenom den här processen uppdateras versionsnumret för en ny version samt versionsstatusen återspeglar det senaste tillståndet.
Medan vi går igenom den här processen kommer versionsnumret för versionen av versionen att visas med ett "X" i den mindre versionsnumret position, som i "1.3.X.0" - detta indikerar att viktig information i detta dokument är giltiga för alla versioner som börjar med "1.3.". Så snart vi har slutfört releaseprocessen kommer versionsnumret att uppdateras till den senast släppta versionen och utgivningsstatusen kommer att uppdateras till "Släppt för nedladdning och automatisk uppgradering".
Alla versioner av Azure AD Connect kommer inte att göras tillgängliga för automatisk uppgradering. Utgivningsstatusen anger om en version görs tillgänglig för automatisk uppgradering eller endast för hämtning. Om automatisk uppgradering har aktiverats på din Azure AD Connect-server uppgraderas den servern automatiskt till den senaste versionen av Azure AD Connect som släpps för automatisk uppgradering. Observera att inte alla Azure AD Connect-konfigurationer är berättigade till automatisk uppgradering. Följ denna länk för att läsa mer om [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md)

>[!IMPORTANT]
> Från och med den 1 november 2020 börjar vi implementera en utfasningsprocess där versioner av Azure AD Connect som släpptes för mer än 18 månader sedan kommer att vara inaktuella. Då börjar vi den här processen genom att ta bort alla versioner av Azure AD Connect med version 1.3.20.0 (som släpptes den 4/24/2019) och äldre, och vi fortsätter att utvärdera utfasningen av äldre versioner av Azure AD Connect varje gång en ny version släpps.
>
> Du måste se till att du kör en ny version av Azure AD Connect för att få en optimal supportupplevelse. 
>
>Om du kör en föråldrad version av Azure AD Connect kanske du inte har de senaste säkerhetskorrigeringarna, prestandaförbättringar, felsöknings- och diagnostikverktyg och serviceförbättringar, och om du behöver support kanske vi inte kan ge dig nivån på din organisation behöver.
>
>Om du har aktiverat Azure AD Connect för synkronisering kommer du snart automatiskt att börja ta emot hälsomeddelanden som varnar dig om kommande utfasningar när du kör en av de äldre versionerna.
>
>Se [den här artikeln](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version) om du vill veta mer om hur du uppgraderar Azure AD Connect till den senaste versionen.

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>Versionsstatus
2019-01-09: Släpp för nedladdning. Inte tillgängligt via automatisk uppgradering.
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
- Vi har uppdaterat Password Hash Sync för Azure AD Domain Services för att korrekt ta hänsyn till utfyllnad i Kerberos-hashar.  Detta ger en prestandaförbättring under lösenordssynkronisering från AAD till Azure AD Domain Services.
- Vi har lagt till stöd för tillförlitliga sessioner mellan autentiseringsagenten och servicebussen.
- Den här versionen tillämpar TLS 1.2 för kommunikation mellan autentiseringsagent och molntjänster.
- Vi har lagt till en DNS-cache för websocket-anslutningar mellan autentiseringsagent och molntjänster.
- Vi har lagt till möjligheten att rikta in dig på specifik agent från molnet för att testa agentanslutningen.

### <a name="fixed-issues"></a>Åtgärdade problem
- Release 1.4.18.0 hade en bugg där PowerShell-cmdlet för DSSO använde inloggningsfönsterautentiseringsuppgifterna i stället för de administratörsuppgifter som angavs när ps kördes. Som ett resultat av vilket det inte var möjligt att aktivera DSSO i flera skogar via AADConnect-användargränssnittet. 
- En korrigering gjordes för att aktivera DSSO samtidigt i hela skogen via AADConnect-användargränssnittet

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>Versionsstatus
2019-08-11: Släppt för nedladdning. Inte tillgängligt via automatisk uppgradering.

>[!IMPORTANT]
>På grund av en intern schemaändring i den här versionen av Azure AD Connect måste du uppdatera MSOnline PowerShell-modulen till version 1.1.183.57 eller senare om du hanterar konfigurationsinställningar för ADFS-förtroenderelation med MSOnline PowerShell.
### <a name="fixed-issues"></a>Åtgärdade problem

Den här versionen åtgärdar ett problem med befintliga Hybrid Azure AD-anslutna enheter. Den här versionen innehåller en ny enhetssynkroniseringsregel som korrigerar problemet.
Observera att den här regeländringen kan orsaka borttagning av föråldrade enheter från Azure AD. Detta är inte en anledning till oro, eftersom dessa enhetsobjekt inte används av Azure AD under godkännande för villkorlig åtkomst. För vissa kunder kan antalet enheter som ska tas bort via den här regeln överskrida borttagningströskeln. Om du ser borttagningen av enhetsobjekt i Azure AD som överskrider tröskelvärdet för exportborttagning rekommenderas att borttagningarna går igenom. [Så här tillåter du att borttagningar flödar när de överskrider borttagningströskeln](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>Versionsstatus
2019-09-28: Släppt för automatisk uppgradering för att välja klienter. Inte tillgänglig för nedladdning.

Den här versionen åtgärdar en bugg där vissa servrar som uppgraderades automatiskt från en tidigare version till 1.4.18.0 och upplevde problem med självbetjäningslösenordsåterställning (SSPR) och lösenordsåterställning.

### <a name="fixed-issues"></a>Åtgärdade problem

Under vissa omständigheter har servrar som uppgraderades automatiskt till version 1.4.18.0 inte återaktivera återställning av lösenordsåterbeställning av självbetjäning och återställning av lösenord efter att uppgraderingen slutförts. Den här versionen för automatisk uppgradering åtgärdar problemet och aktiverar återställning av självbetjäningslösenord och återställning av lösenord.

Vi fixade en bugg i komprimeringsverktyget för synkroniseringsfel som inte hanterade surrogattecken korrekt.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>Vi undersöker en incident där vissa kunder har problem med befintliga Hybrid Azure AD-anslutna enheter efter uppgradering till den här versionen av Azure AD Connect. Vi råder kunder som har distribuerat Hybrid Azure AD-koppling att skjuta upp uppgraderingen till den här versionen tills grundorsaken till dessa problem är helt klarlagda och mildrade. Mer information kommer att ges så snart som möjligt.

>[!IMPORTANT]
>Med den här versionen av Azure AD Connect kan vissa kunder se att vissa eller alla sina Windows-enheter försvinner från Azure AD. Detta är inte en anledning till oro, eftersom dessa enhetsidentiteter inte används av Azure AD under godkännande för villkorlig åtkomst. Mer information finns i [Förstå Azure AD Connect 1.4.xx.x-enhets försvann](reference-connect-device-disappearance.md)


### <a name="release-status"></a>Versionsstatus
2019-09-25: Släppt endast för automatisk uppgradering.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
- Nya felsökningsverktyg hjälper till att felsöka scenarier för synkronisering av "användare som inte synkroniseras", "grupp inte synkroniserar" eller "gruppmedlem som inte synkroniserar".
- Lägga till stöd för nationella moln i AAD Connect felsökningsskript 
- Kunderna bör informeras om att de inaktuella WMI-slutpunkterna för MIIS_Service nu har tagits bort. Alla WMI-åtgärder bör nu göras via PS-cmdlets.
- Säkerhetsförbättring genom att återställa begränsad delegering på AZUREADSSOACC-objekt
- När du lägger till/redigerar en synkroniseringsregel, om det finns några attribut som används i regeln som finns i anslutningsschemat men inte läggs till i kopplingen, läggs attributen automatiskt till i kopplingen. Detsamma gäller för den objekttyp som regeln påverkar. Om något läggs till i kopplingen markeras kopplingen för fullständig import på nästa synkroniseringscykel.
- Att använda en företags- eller domänadministratör som anslutningskonto stöds inte längre i nya AAD Connect-distributioner. Aktuella AAD Connect-distributioner med en Enterprise- eller Domain-administratör eftersom anslutningskontot inte påverkas av den här versionen.
- I Synkroniseringshanteraren körs en fullständig synkronisering på regelskapande/redigering/borttagning. En popup visas på alla regeländringar som meddelar användaren om fullständig import eller fullständig synkronisering kommer att köras.
- Lade till begränsningssteg för lösenordsfel i sidan kopplingar > egenskaper > anslutningssidan
- Lade till en varning om utfasning för synkroniseringstjänsthanteraren på sidan anslutningsegenskaper. Den här varningen meddelar användaren att ändringar ska göras via AADC-guiden.
- Lade till nya fel för problem med en användares lösenordsprincip.
- Förhindra felkonfiguration av gruppfiltrering efter domän- och organisationsenhetsfilter. Gruppfiltrering visar ett fel när domänen/organisationsenheten för den angivna gruppen redan har filtrerats bort och användaren inte går framåt tills problemet är löst.
- Användare kan inte längre skapa en anslutningsapp för Active Directory Domain Services eller Windows Azure Active Directory i användargränssnittet för Synkroniseringstjänsthanteraren.
- Fast hjälpmedel för anpassade gränssnittskontroller i Synkroniseringstjänsthanteraren.
- Aktiverade sex federationshanteringsuppgifter för alla inloggningsmetoder i Azure AD Connect.  (Tidigare var endast aktiviteten "Uppdatera AD FS TLS/SSL-certifikat" tillgänglig för alla inloggningar.)
- Lade till en varning när inloggningsmetoden ändras från federation till PHS eller PTA om att alla Azure AD-domäner och användare konverteras till hanterad autentisering.
- Tog bort tokensigneringscertifikat från uppgiften "Återställ Azure AD och AD FS trust" och lade till en separat underuppgift för att uppdatera dessa certifikat.
- Lade till en ny federationshanteringsuppgift som kallas "Hantera certifikat" som har underuppgifter för att uppdatera TLS- eller tokensigneringscertifikaten för AD FS-servergruppen.
- Lade till en ny underuppgift för federationshantering med namnet "Ange primär server" som gör det möjligt för administratörer att ange en ny primär server för AD FS-servergruppen.
- Lade till en ny federationshanteringsuppgift med namnet "Hantera servrar" som har underuppgifter för att distribuera en AD FS-server, distribuera en proxyserver för webbprogram och ange primär server.
- Lade till en ny federationshanteringsuppgift med namnet "Visa federationskonfiguration" som visar de aktuella AD FS-inställningarna.  (På grund av det här tillägget har AD FS-inställningarna tagits bort från sidan "Granska din lösning".)

### <a name="fixed-issues"></a>Åtgärdade problem
- Löst synkroniseringsfelproblem för scenariot där ett användarobjekt som tar över motsvarande kontaktobjekt har en självreferens (t.ex. är användaren sin egen chef).
- Hjälp popups visas nu på tangentbord fokus.
- Om en app i konflikt körs från 6 timmar för automatisk uppgradering, om en app i konflikt körs från 6 timmar, dödar du den och fortsätter med uppgraderingen.
- Begränsa antalet attribut som en kund kan välja till 100 per objekt när du väljer katalogtillägg. Detta förhindrar att felet uppstår under export eftersom Azure har högst 100 tilläggsattribut per objekt.
- Fixade en bugg för att göra AD-anslutningsskriptet mer robust
- Fixade ett fel för att få AADConnect att installera på en dator med en befintlig WCF-tjänst med namngivna rör mer robust.
- Förbättrad diagnostik och felsökning runt grupprinciper som inte tillåter adsync-tjänsten att starta när den installeras från början.
- Fixade ett fel där visningsnamnet för en Windows-dator skrevs felaktigt.
- Åtgärda ett fel där OS-typ för en Windows-dator skrevs felaktigt.
- Fixade ett fel där datorer som inte var Windows 10 synkroniserade oväntat. Observera att effekten av den här ändringen är att datorer som inte har synkroniserats med Windows-10 nu tas bort. Detta påverkar inte några funktioner eftersom synkroniseringen av Windows-datorer endast används för Hybrid Azure AD-domänanslutning, som bara fungerar för Windows-10-enheter.
- Lade till flera nya (interna) cmdlets i ADSync PowerShell-modulen.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>Det finns ett känt problem med att uppgradera Azure AD Connect från en tidigare version till 1.3.21.0 där O365-portalen inte återspeglar den uppdaterade versionen även om Azure AD Connect har uppgraderats.
>
> För att lösa detta måste du importera **AdSync-modulen** och sedan köra`Set-ADSyncDirSyncConfiguration` powershell-cmdlet på Azure AD Connect-servern.  Du kan använda följande steg:
>
>1. Öppna Powershell i administrationsläge
>2. Kör `Import-Module "ADSync"`
>3. Kör `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`
 
### <a name="release-status"></a>Versionsstatus 

2019-05-14: Släppt för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem 

- Fixade en behörighetshöjning som finns i Microsoft Azure Active Directory Connect build 1.3.20.0.  Det här säkerhetsproblemet kan under vissa förhållanden göra det möjligt för en angripare att köra två powershell-cmdlets i samband med ett privilegierat konto och utföra privilegierade åtgärder.  Den här säkerhetsuppdateringen åtgärdar problemet genom att inaktivera dessa cmdlets. Mer information finns i [säkerhetsuppdatering](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000).

## <a name="13200"></a>1.3.20.0 

### <a name="release-status"></a>Versionsstatus 

2019-04-24: Släppt för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 

- Lägg till stöd för Domänuppdatering 
- Funktionen Exchange Mail Public Folders går GA 
- Förbättra guidefelhanteringen för tjänstfel 
- Lade till varningslänk i användargränssnittet för Synkroniseringstjänsthanteraren på sidan anslutningsegenskaper. 
- Unified Groups tillbakaskrivning funktionen är nu GA 
- Förbättrat SSPR-felmeddelande när domänkontrollanten saknar en LDAP-kontroll 
- Lade till diagnostik för DCOM-registerfel under installationen  
- Förbättrad spårning av PHS RPC-fel 
- Tillåt EA-creds från en underordnad domän 
- Tillåt att databasnamn anges under installationen (standardnamn ADSync)
- Uppgradera till ADAL 3.19.8 för att hämta en WS-Trust-korrigering för Ping och lägga till stöd för nya Azure-instanser 
- Ändra gruppsynkroniseringsregler för att flöda samAccountName, DomainNetbios och DomainFQDN till molnet – behövs för anspråk 
- Ändrad standardsynkroniseringsregelhantering – läs mer [här](how-to-connect-fix-default-rules.md).
- Lade till en ny agent som körs som en Windows-tjänst. Den här agenten, som heter "Admin Agent", möjliggör djupare fjärrdiagnostik för Azure AD Connect-servern för att hjälpa Microsoft-tekniker att felsöka när du öppnar ett supportärende. Den här agenten är inte installerad och aktiverad som standard.  Mer information om hur du installerar och aktiverar agenten finns [i Vad är Azure AD Connect-administratörsagenten?](whatis-aadc-admin-agent.md). 
- Uppdaterade licensavtalet för slutanvändare (EULA) 
- Lade till stöd för automatisk uppgradering för distributioner som använder AD FS som sin inloggningstyp.  Detta tog också bort kravet på att uppdatera AD FS Azure AD Relying Party Trust som en del av uppgraderingsprocessen. 
- Lade till en Azure AD-förtroendehanteringsuppgift som innehåller två alternativ: analysera/uppdatera förtroende och återställa förtroende. 
- Ändrade ad FS Azure AD Relying Party-förtroendebeteendet så att den alltid använder växeln -SupportMultipleDomain (inkluderar förtroende- och Azure AD-domänuppdateringar). 
- Ändrade installationsbeteendet för AD FS-servergrupp så att det kräver ett PFX-certifikat genom att ta bort alternativet att använda ett förinstallerat certifikat.
- Uppdaterade arbetsflödet för installation av nya AD FS-servergrupp så att det bara tillåter distribution av 1 AD FS- och 1 WAP-server.  Alla ytterligare servrar kommer att göras efter den första installationen. 

### <a name="fixed-issues"></a>Åtgärdade problem 

- Åtgärda SQL-återanslutningslogiken för ADSync-tjänsten 
- Åtgärdat för att tillåta ren installation med en tom SQL AOA DB 
- Åtgärda PS-behörighetsskript för att förfina GWB-behörigheter 
- Åtgärda VSS-fel med LocalDB  
- Åtgärda vilseledande felmeddelande när objekttypen inte är i omfattning 
- Korrigerade ett problem där installation av Azure AD PowerShell på en server potentiellt kan orsaka en sammansättningskonflikt med Azure AD Connect. 
- Fast PHS-fel på mellanlagringsservern när anslutningsautentiseringsuppgifter uppdateras i användargränssnittet för synkroniseringstjänsthanteraren. 
- Fixade några minnesläckor 
- Diverse korrigeringar för automatisk uppgradering 
- Diverse korrigeringar av export- och obekräftade importbearbetning 
- Fixade ett fel med hantering av ett omvänt snedstreck i domän- och organisationsenhetsfiltrering 
- Åtgärdade ett problem där ADSync-tjänsten tar mer än 2 minuter att stoppa och orsakar ett problem vid uppgraderingstillfället. 




## <a name="12700"></a>1.2.70.0

### <a name="release-status"></a>Versionsstatus

2018-12-18: Släppt för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

Den här versionen uppdaterar icke-standardkopplingar (till exempel Generic LDAP Connector och Generic SQL Connector) som levereras med Azure AD Connect. Mer information om tillämpliga kopplingar finns i version 1.1.911.0 i [Connector Version Release History](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history).


## <a name="12690"></a>1.2.69.0

### <a name="release-status"></a>Versionsstatus
2018-12-11: Släppt för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem
Med den här snabbkorrigeringsversionen kan användaren välja en måldomän, inom den angivna skogen, för behållaren RegisteredDevices när enhetens tillbakaskrivning aktiveras.  I de tidigare versionerna som innehåller den nya enhetsalternativfunktionen (1.1.819.0 – 1.2.68.0) begränsades behållarens plats för RegisteredDevices till skogsroten och tillät inte underordnade domäner.  Den här begränsningen visade sig bara på nya distributioner – uppgraderingar på plats påverkades inte.  

Om någon version som innehåller den uppdaterade enhetsalternativfunktionen har distribuerats till en ny server och enhetsreskrivning har aktiverats måste du manuellt ange platsen för behållaren om du inte vill ha den i skogsroten.  För att göra detta måste du inaktivera enhetens tillbakaskrivning och återaktivera det, vilket gör att du kan ange behållarplatsen på sidan "Skrivbackskog".



## <a name="12680"></a>1.2.68.0

### <a name="release-status"></a>Versionsstatus 

2018-11-30: Släppt för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

Den här snabbkorrigeringsversionen åtgärdar en konflikt där ett autentiseringsfel kan uppstå på grund av den oberoende närvaron av MODULEN MSOnline PowerShell Gallery på synkroniseringsservern.



## <a name="12670"></a>1.2.67.0

### <a name="release-status"></a>Versionsstatus 

2018-11-19: Släppt för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

Den här snabbkorrigeringsversionen korrigerar en regression i den tidigare versionen där återställning av lösenord misslyckas när du använder en ADD-domänkontrollant på Windows Server 2008/R2.

## <a name="12650"></a>1.2.65.0 

### <a name="release-status"></a>Versionsstatus 

2018-10-25: släppt för nedladdning

 
### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 


- Ändrade funktionen för attributre write-back för att säkerställa värdbaserad röst-post fungerar som förväntat.  Under vissa scenarier skrev Azure AD över attributet msExchUcVoicemailSettings under tillbakaskrivning med ett null-värde.  Azure AD rensar nu inte längre det lokala värdet för det här attributet om molnvärdet inte har angetts.
- Lade till diagnostik i Azure AD Connect-guiden för att undersöka och identifiera anslutningsproblem till Azure AD. Samma diagnostik kan också köras direkt via Powershell med test-AdSyncAzureServiceConnectivity Cmdlet. 
- Lade till diagnostik i Azure AD Connect-guiden för att undersöka och identifiera anslutningsproblem till AD. Samma diagnostik kan också köras direkt via Powershell med hjälp av startanslutningsvalidationsfunktionen i ADConnectivityTools Powershell-modulen.  Mer information finns i [Vad är ADConnectivityTool PowerShell-modulen?](how-to-connect-adconnectivitytools.md)
- Lade till en AD-schemaversion för kontroll för Hybrid Azure Active Directory Join och enhetsskrivning 
- Ändrade attributsökningen för katalogtillägg för att vara icke-skiftlägeskänslig.
-   Lade till fullt stöd för TLS 1.2. Den här versionen stöder alla andra protokoll som inaktiveras och endast TLS 1.2 aktiveras på datorn där Azure AD Connect är installerat.  Mer information finns i [TLS 1.2 enforcement för Azure AD Connect](reference-connect-tls-enforcement.md)

 

### <a name="fixed-issues"></a>Åtgärdade problem   

- Fixade ett fel där Azure AD Connect-uppgraderingen skulle misslyckas om SQL Always On användes. 
- Fixade ett fel för att tolka OU-namn som innehåller ett snedstreck. 
- Åtgärdade ett problem där direktautentisering skulle inaktiveras för en ren installation i mellanlagringsläge. 
- Fixade ett fel som hindrade PowerShell-modulen från att läsas in när felsökningsverktygen kördes 
- Fixade ett fel som skulle hindra kunder från att använda numeriska värden i det första tecknet i ett värdnamn. 
- Fixade ett fel där Azure AD Connect tillåter ogiltiga partitioner och behållarval 
- Fixade felmeddelandet "Ogiltigt lösenord" när Desktop SSO är aktiverat. 
- Olika buggfixar för AD FS Trust Management  
- När du konfigurerar enhetsåterskrivning – fixade schemakontrollen för att leta efter objektklassen msDs-DeviceContainer (introducerades på WS2012 R2)

 
## <a name="118820"></a>1.1.882.0  

2018-09-07: släppt för nedladdning, kommer inte att släppas för automatisk uppgradering 

### <a name="fixed-issues"></a>Åtgärdade problem  

Azure AD Connect-uppgradering misslyckas om SQL Always On Availability har konfigurerats för ADSync DB. Den här snabbkorrigeringen åtgärdar problemet och gör att Uppgraderingen kan lyckas. 

## <a name="118800"></a>1.1.880.0

### <a name="release-status"></a>Versionsstatus

2018-08-21: Släppt för nedladdning och automatisk uppgradering. 

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Ping Federate-integreringen i Azure AD Connect är nu tillgänglig för allmän tillgänglighet. [Läs mer om hur du federerar Azure AD med Ping Federate](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#federation-with-pingfederate)
- Azure AD Connect skapar nu säkerhetskopian av Azure AD-förtroendet i AD FS varje gång en uppdatering görs och lagrar den i en separat fil för enkel återställning om det behövs. [Läs mer om de nya funktionerna och Azure AD-förtroendehanteringen i Azure AD Connect](https://aka.ms/fedtrustinaadconnect).
- Nytt felsökningsverktyg hjälper till att felsöka ändring av primär e-postadress och dölja konto från den globala adresslistan
- Azure AD Connect har uppdaterats för att inkludera den senaste inbyggda SQL Server 2012-klienten
- När du växlar användarloggning till synkronisering av lösenord hash eller direktautentisering i uppgiften "Ändra användarloggning" aktiveras kryssrutan Sömlös enkel inloggning som standard.
- Lagt till stöd för Windows Server Essentials 2019
- Azure AD Connect Health-agenten uppdaterades till den senaste versionen 3.1.7.0
- Om installationsprogrammet upptäcker ändringar i standardsynkroniseringsreglerna under en uppgradering uppmanas administratören att göra en varning innan den ändrar reglerna. Detta gör det möjligt för användaren att vidta korrigerande åtgärder och återuppta senare. Gammalt beteende: Om det fanns någon ändrad out-of-box regel då manuell uppgradering var att skriva över dessa regler utan att ge någon varning till användaren och synkronisera schemaläggaren inaktiverades utan att informera användaren. Nytt beteende: Användaren uppmanas med varning innan du skriver över de ändrade synkroniseringsreglerna utanför. Användaren kommer att ha möjlighet att stoppa uppgraderingsprocessen och återuppta senare efter att ha gjort korrigerande åtgärder.
- Tillhandahålla en bättre hantering av ett FIPS-efterlevnadsproblem, vilket ger ett felmeddelande för MD5-hash-generering i en FIPS-kompatibel miljö och en länk till dokumentation som ger en lösning på problemet.
- UI uppdatering för att förbättra federationsuppgifter i guiden, som nu är under en separat undergrupp för federation. 
- Alla ytterligare federationsuppgifter grupperas nu under en enda undermeny för enkel användning.
- En ny omarbetad ADSyncConfig Posh Module (AdSyncConfig.psm1) med nya AD-behörigheter funktioner flyttas från den gamla ADSyncPrep.psm1 (som kan vara inaktuella inom kort)

### <a name="fixed-issues"></a>Åtgärdade problem 

- Fixade ett fel där AAD Connect-servern skulle visa hög CPU-användning efter uppgradering till .NET 4.7.2
- Fixade ett fel som periodvis skulle skapa ett felmeddelande för ett automatiskt löst SQL-dödlägeproblem
- Åtgärdade flera tillgänglighetsproblem för Redigeraren för synkroniseringsregler och Synkroniseringstjänsthanteraren  
- Fixade ett fel där Azure AD Connect inte kan hämta registerinställningsinformation
- Åtgärdade ett fel som skapade problem när användaren går framåt/tillbaka i guiden
- Fixade ett fel för att förhindra att ett fel uppstod på grund av felaktig flertrådshandning i guiden
- När sidan för gruppsynkroniseringsfiltrering stöter på ett LDAP-fel när säkerhetsgrupper matchas returnerar Azure AD Connect nu undantaget med fullständig återgivning.  Grundorsaken till hänvisningsundantaget är fortfarande okänd och kommer att åtgärdas av ett annat fel.
-  Fixade ett fel där behörigheter för STK- och NGC-nycklar (ms-DS-KeyCredentialLink-attribut på användar-/enhetsobjekt för WHfB) inte angavs korrekt.     
- Fixade ett fel där "Set-ADSyncRestrictedPermissions" inte kallades korrekt
-  Lägga till stöd för behörighetsbeviljande för gruppåterskrivning i AADConnects installationsguide
- När inloggningsmetoden ändrades från Password Hash Sync till AD FS inaktiverades inte synkronisering av lösenord hash.
- Lade till verifiering för IPv6-adresser i AD FS-konfiguration
- Uppdaterade meddelandemeddelandet för att informera om att det finns en befintlig konfiguration.
- Enhetsreskrivningen kan inte identifiera behållare i ej betrodd skog. Detta har uppdaterats för att ge ett bättre felmeddelande och en länk till lämplig dokumentation
- Om du avmarkerar en organisationsenhet och sedan synkronisering/tillbakaskrivning som motsvarar den organisationsenhet ger ett allmänt synkroniseringsfel. Detta har ändrats för att skapa ett mer begripligt felmeddelande.

## <a name="118190"></a>1.1.819.0

### <a name="release-status"></a>Versionsstatus

2018-05-14: Släppt för automatisk uppgradering och nedladdning.

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

Nya funktioner och förbättringar

- Den här versionen innehåller den offentliga förhandsversionen av integreringen av PingFederate i Azure AD Connect. Med den här versionen kan kunder enkelt och tillförlitligt konfigurera sin Azure Active Directory-miljö för att utnyttja PingFederate som sin federationsleverantör. Om du vill veta mer om hur du använder den här nya funktionen kan du läsa vår [onlinedokumentation](plan-connect-user-signin.md#federation-with-pingfederate). 
- Uppdaterade Felsökningsverktyget för Azure AD Connect, där det nu analyserar fler felscenario, till exempel länkade postlådor och AD-dynamiska grupper. Läs mer om felsökningsverktyget [här](tshoot-connect-objectsync.md).
- Konfigurationen av enhetsåterskrivning hanteras nu endast i Azure AD Connect-guiden.
- En ny PowerShell-modul som heter ADSyncTools.psm1 läggs till som kan användas för att felsöka SQL-anslutningsproblem och olika andra felsökningsverktyg. Läs mer om ADSyncTools-modulen [här](tshoot-connect-tshoot-sql-connectivity.md). 
- En ny ytterligare uppgift "Konfigurera enhetsalternativ" har lagts till. Du kan använda uppgiften för att konfigurera följande två åtgärder: 
  - **Hybrid Azure AD-koppling:** Om din miljö har ett lokalt AD-fotavtryck och du också vill ha nytta av de funktioner som tillhandahålls av Azure Active Directory kan du implementera hybrid-Azure AD-anslutna enheter. Det är enheter som är anslutna både till din lokala Active Directory och din Azure Active Directory.
  - **Enhetsreskript tillbaka:** Enhetsreskript tillbaka används för att aktivera villkorlig åtkomst baserat på enheter till AD FS (2012 R2 eller högre) skyddade enheter

    >[!NOTE] 
    > - Alternativet för att aktivera tillbakaskrivning av enheter från Anpassa synkroniseringsalternativ kommer att vara nedtonat. 
    > -  PowerShell-modulen för ADPrep är inaktuell med den här versionen.



### <a name="fixed-issues"></a>Åtgärdade problem 

- Den här versionen uppdaterar SQL Server Express-installationen till SQL Server 2012 SP4, som bland annat innehåller korrigeringar för flera säkerhetsproblem.  Se [här](https://support.microsoft.com/help/4018073/sql-server-2012-service-pack-4-release-information) för mer information om SQL Server 2012 SP4.
- Synkroniseringsregelbearbetning: utgående synkroniseringsregler för koppling utan anslutningsvillkor ska avaktiveras om den överordnade synkroniseringsregeln inte längre är tillämplig
- Flera hjälpmedelskorrigeringar har tillämpats på användargränssnittet för Synkroniseringstjänsthanteraren och Redigeraren för synkroniseringsregler
- Azure AD Connect-guiden: Fel uppstod när AD Connect skapas när Azure AD Connect finns i en arbetsgrupp
- Azure AD Connect-guiden: På inloggningssidan för Azure AD visas kryssrutan verifiering när det finns någon obalans i AD-domäner och Azure AD-verifierade domäner
- Uppgradera PowerShell-korrigering automatiskt för att ställa in automatiskt uppgraderingstillstånd korrekt i vissa fall efter ett automatiskt uppgraderingsförsök.
- Azure AD Connect-guiden: Uppdaterad telemetri för att samla in tidigare information som saknas
- Azure AD Connect-guiden: Följande ändringar har gjorts när du använder aktiviteten **Ändra användarloggning** för att växla från AD FS till Direktautentisering:
    - Direktautentiseringsagenten installeras på Azure AD Connect-servern och direktautentiseringsfunktionen är aktiverad innan vi konverterar domäner från federerade till hanterade.
    - Användare konverteras inte längre från federerade till hanterade. Endast domäner konverteras.
- Azure AD Connect-guiden: AD FS Multi Domain Regex är inte korrekt när användaren UPN har " specialtecken Regex uppdatering för att stödja specialtecken
- Azure AD Connect-guiden: Ta bort falska meddelande om att konfigurera källankaretattribut när ingen ändring ändras 
- Azure AD Connect-guiden: AD FS-stöd för scenariot med dubbla federationer
- Azure AD Connect-guiden: AD FS-anspråk uppdateras inte för tillagd domän när en hanterad domän konverteras till federerade
- Azure AD Connect-guiden: Under identifiering av installerade paket hittar vi inaktuella Dirsync/Azure AD Sync/Azure AD Connect-relaterade produkter. Vi kommer nu att försöka avinstallera de inaktuella produkterna.
- Azure AD Connect-guiden: Korrigera felmeddelandemappning när installationen av autentiseringsagenten för passthrough misslyckas
- Azure AD Connect-guiden: Borttagen behållare för konfiguration från sidan För domän-organisationsenhetsfiltrering
- Installera synkroniseringsmotor: ta bort onödig äldre logik som ibland misslyckades från Sync Engine installera msi
- Azure AD Connect-guiden: Åtgärda popup-hjälptext på sidan Valfria funktioner för synkronisering av lösenord hash
- Synkroniseringsmotorkörning: Åtgärda scenariot där ett CS-objekt har ett importerat borttagnings- och synkroniseringsregler försöker etablera om objektet.
- Synkroniseringsmotorns körning: Lägg till hjälplänk för felsökningsguide för onlineanslutning till händelseloggen för ett importfel
- Synkroniseringsmotorns körningstid: Minskad minnesanvändning av Sync Scheduler när anslutningar räknas upp
- Azure AD Connect-guiden: Åtgärda ett problem med att lösa ett anpassat synkroniseringstjänstkonto som inte har några AD-läsbehörighet
- Guiden Azure AD Connect: Förbättra loggningen av domän- och organisationsenhetsfiltreringsval
- Azure AD Connect-guiden: AD FS Lägg till standardanspråk i federationsförtroende som skapats för MFA-scenario
- Guiden Azure AD Connect: AD FS Deploy WAP: Det går inte att använda nya certifikat för att lägga till server
- Azure AD Connect-guiden: DSSO-undantag när onPremCredentials inte initieras för en domän 
- Flöda prioritera attributet AD distinguishedName från active user-objektet.
- Fast en kosmetisk bugg var företräde för den första OOB Sync regeln var inställd på 99 istället för 100



## <a name="117510"></a>1.1.751.0
Status 2018-04-12: Släppt endast för nedladdning

>[!NOTE]
>Den här versionen är en snabbkorrigering för Azure AD Connect

### <a name="azure-ad-connect-sync"></a>Azure AD Connect-synkronisering
#### <a name="fixed-issues"></a>Åtgärdade problem
Korrigerade ett problem var automatisk Azure-instansidentifiering för Kina-klienter misslyckades ibland.  

### <a name="ad-fs-management"></a>AD FS-hantering
#### <a name="fixed-issues"></a>Åtgärdade problem

Det uppstod ett problem i logiken för återförsök i konfigurationen som skulle resultera i ett ArgumentException som anger "ett objekt med samma nyckel har redan lagts till".  Detta skulle orsaka att alla återförsöksåtgärder misslyckas.

## <a name="117500"></a>1.1.750.0
Status 2018-03-22: Släppt för automatisk uppgradering och nedladdning.
>[!NOTE]
>När uppgraderingen till den här nya versionen är klar utlöser den automatiskt en fullständig synkronisering och fullständig import för Azure AD-anslutningen och en fullständig synkronisering för AD-kopplingen. Eftersom detta kan ta lite tid, beroende på storleken på din Azure AD Connect-miljö, se till att du har vidtagit nödvändiga åtgärder för att stödja detta eller vänta med att uppgradera tills du har hittat ett bekvämt tillfälle att göra det.

>[!NOTE]
>"Funktionen AutoUpgrade inaktiverades felaktigt för vissa klienter som distribuerade versioner senare än 1.1.524.0. Om du vill vara säkra på att din Azure AD Connect-instans fortfarande är kvalificerad för Automatisk registrering kör du följande PowerShell-cmdlet: "Set-ADSyncAutoUpgrade -AutoupGradeState Enabled"


### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem

* Set-ADSyncAutoUpgrade cmdlet skulle tidigare blockera Autoupgrade om automatisk uppgradering tillstånd är inställt på Pausad. Den här funktionen har nu ändrats så att den inte blockerar Automatisk registrering av framtida versioner.
* Ändrade alternativet **Användarinloggning** "Lösenordssynkronisering" till "Synkronisering av lösenord hash".  Azure AD Connect synkroniserar lösenordshämningar, inte lösenord, så detta stämmer överens med vad som faktiskt händer.  Mer information finns i [Implementera synkronisering av lösenordsh hash-synkronisering med Azure AD Connect-synkronisering](how-to-connect-password-hash-synchronization.md)

## <a name="117490"></a>1.1.749.0
Status: Släppt för att välja kunder

>[!NOTE]
>När uppgraderingen till den här nya versionen är klar utlöser den automatiskt en fullständig synkronisering och fullständig import för Azure AD-anslutningen och en fullständig synkronisering för AD-kopplingen. Eftersom detta kan ta lite tid, beroende på storleken på din Azure AD Connect-miljö, se till att du har vidtagit nödvändiga åtgärder för att stödja detta eller vänta med att uppgradera tills du har hittat ett bekvämt tillfälle att göra det.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärda tidsfönstret för bakgrundsuppgifter för partitionsfiltreringssidan när du växlar till nästa sida.

* Fixade ett fel som orsakade åtkomstfel under den anpassade åtgärden ConfigDB.

* Fixade ett fel för att återställa från TIMEOUT för SQL-anslutning.

* Fixade ett fel där certifikat med SAN-jokertecken misslyckades med en nödvändig kontroll.

* Fixade ett fel som gör att miiserver.exe kraschar under en Azure AD-anslutningsexport.

* Fixade ett fel som felaktiga lösenordsförsök loggade på DOMÄNKONTROLLANTEN när du kör Azure AD Connect-guiden för att ändra konfigurationen.


#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Lägga till sekretessinställningar för den allmänna dataskyddsförordningen (GDPR).  För mer information se artikeln [här](reference-connect-user-privacy.md).

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]  

* programtelemetri - admin kan växla denna klass av data på / av efter behag

* Azure AD Health-data – administratören måste besöka hälsoportalen för att kontrollera sina hälsoinställningar.
   När servicepolicyn har ändrats kommer agenterna att läsa och genomdriva den.

* Lade till konfigurationsåtgärder för tillbakaskrivning av enheter och en förloppsindikator för sidinitualisering

* Förbättrad allmän diagnostik med HTML-rapport och fullständig datainsamling i en ZIP-text / HTML-rapport

* Förbättrad tillförlitlighet en automatisk uppgradering och lagt till ytterligare telemetri för att säkerställa att hälsan för servern kan fastställas

* Begränsa behörigheter som är tillgängliga för privilegierade konton på AD Connector-konto

  * För nya installationer begränsar guiden de behörigheter som privilegierade konton har på MSOL-kontot när MSOL-kontot har skapats.

Ändringarna tar hand om följande:
1. Express installationer
2. Anpassade installationer med konto för automatisk skapande
3. Ändrade installationsprogrammet så att det inte kräver SA-behörighet vid ren installation av Azure AD Connect

* Lade till ett nytt verktyg för att felsöka synkroniseringsproblem för ett visst objekt. Den är tillgänglig under alternativet Felsöka objektsynkronisering i Azure AD Connect-guiden Felsöka ytterligare uppgift. För närvarande söker verktyget efter följande:

  * UserPrincipalName matchar inte mellan synkroniserat användarobjekt och användarkontot i Azure AD-klienten.
  * Om objektet filtreras från synkronisering på grund av domänfiltrering
  * Om objektet filtreras från synkronisering på grund av organisationsenhetsfiltrering

* Lade till ett nytt verktyg för att synkronisera den aktuella lösenordshhen som lagras i den lokala Active Directory för ett visst användarkonto.

Verktyget kräver ingen lösenordsändring. Den är tillgänglig under alternativet Felsöka lösenordshÃ¶ring av Azure AD Connect-guiden Felsöka ytterligare uppgift.






## <a name="116540"></a>1.1.654.0
Status: 12 december 2017

>[!NOTE]
>Den här versionen är en säkerhetsrelaterad snabbkorrigering för Azure AD Connect

### <a name="azure-ad-connect"></a>Azure AD Connect
En förbättring har lagts till Azure AD Connect version 1.1.654.0 (och efter) för att säkerställa att de rekommenderade behörighetsändringarna som beskrivs under avsnittet [Lås åtkomst till AD DS-kontot](#lock) automatiskt tillämpas när Azure AD Connect skapar AD DS-kontot. 

- När du konfigurerar Azure AD Connect kan installationsadministratören antingen tillhandahålla ett befintligt AD DS-konto eller låta Azure AD Connect automatiskt skapa kontot. Behörighetsändringarna tillämpas automatiskt på AD DS-kontot som skapas av Azure AD Connect under installationen. De tillämpas inte på befintliga AD DS-konto som tillhandahålls av installationsadministratören.
- För kunder som har uppgraderat från en äldre version av Azure AD Connect till 1.1.654.0 (eller efter), tillämpas inte behörighetsändringarna retroaktivt på befintliga AD DS-konton som skapats före uppgraderingen. De tillämpas endast på nya AD DS-konton som skapats efter uppgraderingen. Detta inträffar när du lägger till nya AD-skogar som ska synkroniseras till Azure AD.

>[!NOTE]
>Den här versionen tar bara bort säkerhetsproblemet för nya installationer av Azure AD Connect där tjänstkontot skapas av installationsprocessen. För befintliga installationer, eller i de fall där du själv tillhandahåller kontot, bör du se till att det här säkerhetsproblemet inte finns.

#### <a name="lock-down-access-to-the-ad-ds-account"></a><a name="lock"></a>Lås åtkomsten till AD DS-kontot
Lock down access to the AD DS account by implementing the following permission changes in the on-premises AD:  

*   Inaktivera arv på det angivna objektet
*   Ta bort alla åtkomstkontrollistor på det specifika objektet, förutom AKU:er som är specifika för SELF. Vi vill behålla standardbehörigheterna intakta när det gäller SELF.
*   Tilldela dessa specifika behörigheter:

Typ     | Namn                          | Åtkomst               | Gäller för
---------|-------------------------------|----------------------|--------------|
Tillåt    | SYSTEM                        | Fullständig kontroll         | Detta objekt  |
Tillåt    | Företagsadministratörer             | Fullständig kontroll         | Detta objekt  |
Tillåt    | Domänadministratörer                 | Fullständig kontroll         | Detta objekt  |
Tillåt    | Administratörer                | Fullständig kontroll         | Detta objekt  |
Tillåt    | Domänkontrollanter för företag | Lista innehåll        | Detta objekt  |
Tillåt    | Domänkontrollanter för företag | Läs alla egenskaper  | Detta objekt  |
Tillåt    | Domänkontrollanter för företag | Läs behörigheter     | Detta objekt  |
Tillåt    | Autentiserade användare           | Lista innehåll        | Detta objekt  |
Tillåt    | Autentiserade användare           | Läs alla egenskaper  | Detta objekt  |
Tillåt    | Autentiserade användare           | Läs behörigheter     | Detta objekt  |

Om du vill dra åt inställningarna för AD DS-kontot kan du köra [det här PowerShell-skriptet](https://gallery.technet.microsoft.com/Prepare-Active-Directory-ef20d978). PowerShell-skriptet tilldelar de behörigheter som nämns ovan till AD DS-kontot.

#### <a name="powershell-script-to-tighten-a-pre-existing-service-account"></a>PowerShell-skript för att dra åt ett befintligt tjänstkonto

Om du vill använda PowerShell-skriptet, för att tillämpa dessa inställningar, på ett befintligt AD DS-konto (eter som tillhandahålls av din organisation eller skapats av en tidigare installation av Azure AD Connect, hämta skriptet från den angivna länken ovan.

##### <a name="usage"></a>Användning:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN <$ObjectDN> -Credential <$Credential>
```

Var 

**$ObjectDN** = Active Directory-kontot vars behörigheter måste skärpas.

**$Credential** = Administratörsautentiseringsuppgifter som har de behörigheter som krävs för att begränsa behörigheterna för $ObjectDN kontot. Dessa privilegier innehas vanligtvis av företags- eller domänadministratören. Använd det fullständigt kvalificerade domännamnet för administratörskontot för att undvika kontosökningsfel. Exempel: contoso.com\admin.

>[!NOTE] 
>$credential. Användarnamn ska vara i FQDN\användarnamnsformat. Exempel: contoso.com\admin 

##### <a name="example"></a>Exempel:

```powershell
Set-ADSyncRestrictedPermissions -ObjectDN "CN=TestAccount1,CN=Users,DC=bvtadwbackdc,DC=com" -Credential $credential 
```
### <a name="was-this-vulnerability-used-to-gain-unauthorized-access"></a>Användes det här säkerhetsproblemet för att få obehörig åtkomst?

För att se om det här säkerhetsproblemet användes för att kompromettera din Azure AD Connect-konfiguration bör du verifiera det senaste datum för återställning av lösenord för tjänstkontot.  Om tidsstämpeln i oväntade, ytterligare undersökning, via händelseloggen, för att lösenordsåterställning händelse, bör genomföras.

Mer information finns i [Microsoft Security Advisory 4056318](https://technet.microsoft.com/library/security/4056318)

## <a name="116490"></a>1.1.649.0
Status: 27 oktober 2017

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder via azure AD Connect Auto Upgrade-funktionen.

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issue"></a>Åtgärdat problem 
* Åtgärdade ett versionskompatibilitetsproblem mellan Azure AD Connect och Azure AD Connect Health Agent (för synkronisering). Det här problemet påverkar kunder som utför Azure AD Connect på plats uppgradering till version 1.1.647.0, men har för närvarande Health Agent version 3.0.127.0. Efter uppgraderingen kan hälsoagenten inte längre skicka hälsodata om Azure AD Connect-synkroniseringstjänst till Azure AD Health Service. Med den här korrigeringen installeras Health Agent version 3.0.129.0 under Azure AD Connect-uppgradering på plats. Health Agent version 3.0.129.0 har inget kompatibilitetsproblem med Azure AD Connect version 1.1.649.0.


## <a name="116470"></a>1.1.647.0
Status: 19 oktober 2017

> [!IMPORTANT]
> Det finns ett känt kompatibilitetsproblem mellan Azure AD Connect version 1.1.647.0 och Azure AD Connect Health Agent (för synkronisering) version 3.0.127.0. Det här problemet förhindrar att hälsoagenten skickar hälsodata om Azure AD Connect-synkroniseringstjänsten (inklusive objektsynkroniseringsfel och körningsdata) till Azure AD Health Service. Innan du uppgraderar din Azure AD Connect-distribution manuellt till version 1.1.647.0 kontrollerar du den aktuella versionen av Azure AD Connect Health Agent som är installerad på din Azure AD Connect-server. Du kan göra det genom att gå till *Kontrollpanelen → Lägg till ta bort program* och leta efter program Microsoft Azure AD Connect Health Agent for *Sync*. Om dess version är 3.0.127.0 rekommenderar vi att du väntar på att nästa Azure AD Connect-version ska vara tillgänglig före uppgraderingen. Om Health Agent-versionen inte är 3.0.127.0, är det bra att fortsätta med den manuella uppgraderingen på plats. Observera att det här problemet inte påverkar swing uppgradering eller kunder som utför ny installation av Azure AD Connect.
>
>

### <a name="azure-ad-connect"></a>Azure AD Connect
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdade ett problem med *inloggningsuppgiften Ändra användare i* Azure AD Connect-guiden:

  * Problemet uppstår när du har aktiverat en befintlig **enabled**Azure AD *Connect-distribution*med lösenordssynkronisering och du försöker ange inloggningsmetoden för användare som direktautentisering . Innan ändringen tillämpas visas prompten "*Inaktivera lösenordssynkronisering*" i guiden felaktigt. Lösenordssynkronisering är dock aktiverat när ändringen har tillämpats. Med den här korrigeringen visar guiden inte längre uppmaningen.

  * Avsiktligt inaktiveras inte lösenordssynkronisering i guiden när du uppdaterar inloggningsmetoden för användare med hjälp av aktiviteten *Ändra användarloggning.* Detta för att undvika störningar för kunder som vill behålla lösenordssynkronisering, även om de aktiverar direktautentisering eller federation som sin primära inloggningsmetod för användare.
  
  * Om du vill inaktivera lösenordssynkronisering efter uppdatering av användarens inloggningsmetod måste du köra aktiviteten *Anpassa synkroniseringskonfiguration* i guiden. När du navigerar till sidan *Valfria funktioner* avmarkerar du alternativet *Lösenordssynkronisering.*
  
  * Observera att samma problem också uppstår om du försöker aktivera/inaktivera sömlös enkel inloggning. Specifikt har du en befintlig Azure AD *Connect-distribution*med lösenordssynkronisering aktiverad och inloggningsmetoden för användare har redan konfigurerats som direktautentisering . Med hjälp av aktiviteten *Ändra användarloggning* försöker du kontrollera/avmarkera alternativet *Aktivera sömlös enkel inloggning* medan användarens inloggningsmetod fortfarande är konfigurerad som "Direktautentisering". Innan ändringen tillämpas visas prompten "*Inaktivera lösenordssynkronisering*" i guiden felaktigt. Lösenordssynkronisering är dock aktiverat när ändringen har tillämpats. Med den här korrigeringen visar guiden inte längre uppmaningen.

* Åtgärdade ett problem med *inloggningsuppgiften Ändra användare i* Azure AD Connect-guiden:

  * Problemet uppstår när du har en befintlig Azure AD *Connect-distribution*med lösenordssynkronisering **inaktiverad**och du försöker ange inloggningsmetoden för användare som direktautentisering . När ändringen tillämpas aktiverar guiden både direktautentisering och lösenordssynkronisering. Med den här korrigeringsfilen aktiveras inte längre lösenordssynkronisering i guiden.

  * Tidigare var lösenordssynkronisering en förutsättning för att aktivera direktautentisering. När du anger användarens inloggningsmetod som *direktautentisering*aktiverar guiden både direktautentisering och lösenordssynkronisering. Nyligen togs lösenordssynkronisering bort som en förutsättning. Som en del av Azure AD Connect version 1.1.557.0 gjordes en ändring i Azure AD Connect för att inte aktivera lösenordssynkronisering när du anger användarens inloggningsmetod som *direktautentisering*. Ändringen tillämpades dock bara på Azure AD Connect-installationen. Med den här korrigeringsfilen tillämpas samma ändring också på aktiviteten *Ändra användares inloggning.*
  
  * Observera att samma problem också uppstår om du försöker aktivera/inaktivera sömlös enkel inloggning. Specifikt har du en befintlig Azure AD *Connect-distribution*med lösenordssynkronisering inaktiverad och inloggningsmetoden för användare har redan konfigurerats som direktautentisering . Med hjälp av aktiviteten *Ändra användarloggning* försöker du kontrollera/avmarkera alternativet *Aktivera sömlös enkel inloggning* medan användarens inloggningsmetod fortfarande är konfigurerad som "Direktautentisering". När ändringen tillämpas aktiveras lösenordssynkronisering i guiden. Med den här korrigeringsfilen aktiveras inte längre lösenordssynkronisering i guiden. 

* Åtgärdade ett problem som orsakade att Azure AD Connect-uppgraderingen misslyckades med felet "*Det gick inte att uppgradera synkroniseringstjänsten*". Dessutom kan synkroniseringstjänsten inte längre starta med händelsefel "*Tjänsten kunde inte starta eftersom databasens version är nyare än den version av binärfilerna som installerats*". Problemet uppstår när administratören som utför uppgraderingen inte har sysadmin-behörighet till SQL-servern som används av Azure AD Connect. Med den här korrigeringsfilen kräver Azure AD Connect bara att administratören har db_owner behörighet till ADSync-databasen under uppgraderingen.

* Fixade ett Azure AD Connect-uppgraderingsproblem som påverkade kunder som har aktiverat [Sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). När Azure AD Connect har uppgraderats visas sömlös enkel inloggning felaktigt som inaktiverad i Azure AD Connect-guiden, även om funktionen förblir aktiverad och fullt fungerande. Med den här korrigeringen visas funktionen nu korrekt som aktiverad i guiden.

* Åtgärdade ett problem som orsakade azure AD Connect-guiden för att alltid visa uppmaningen "*Konfigurera källankare*" på sidan *Klar att konfigurera,* även om inga ändringar relaterade till Source Anchor gjordes.

* När du utför manuell uppgradering på plats av Azure AD Connect måste kunden ange autentiseringsuppgifterna för den globala administratören för motsvarande Azure AD-klientorganisation. Tidigare kunde uppgraderingen fortsätta även om den globala administratörens autentiseringsuppgifter tillhörde en annan Azure AD-klientorganisation. Uppgraderingen verkar slutföras, men vissa konfigurationer sparas inte korrekt med uppgraderingen. Med den här ändringen förhindrar guiden att uppgraderingen fortsätter om autentiseringsuppgifterna inte matchar Azure AD-klienten.

* Tog bort redundant logik som i onödan startade om Azure AD Connect Health-tjänsten i början av en manuell uppgradering.


#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Lade till logik för att förenkla de steg som krävs för att konfigurera Azure AD Connect med Microsoft Germany Cloud. Tidigare måste du uppdatera specifika registernycklar på Azure AD Connect-servern för att den ska fungera korrekt med Microsoft Germany Cloud, enligt beskrivningen i den här artikeln. Nu kan Azure AD Connect automatiskt identifiera om din klient är i Microsoft Germany Cloud baserat på de globala administratörsautentiseringsuppgifter som anges under installationen.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
> [!NOTE]
> Synkroniseringstjänsten har ett WMI-gränssnitt som gör att du kan utveckla din egen anpassade schemaläggare. Det här gränssnittet är nu inaktuellt och tas bort från framtida versioner av Azure AD Connect som levereras efter den 30 juni 2018. Kunder som vill anpassa synkroniseringsschemat bör använda den [inbyggda schemaläggaren](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-scheduler).

#### <a name="fixed-issues"></a>Åtgärdade problem
* När Azure AD Connect-guiden skapar AD Connector-kontot som krävs för att synkronisera ändringar från lokala Active Directory, tilldelar den inte kontot den behörighet som krävs för att läsa PublicFolder-objekt. Det här problemet påverkar både Express-installation och Anpassad installation. Den här ändringen åtgärdar problemet.

* Åtgärdade ett problem som gjorde att felsökningssidan för Azure AD Connect-guiden inte renderade korrekt för administratörer som kör från Windows Server 2016.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Vid felsökning av lösenordssynkronisering med hjälp av felsökningssidan i Azure AD Connect returnerar felsökningssidan nu domänspecifik status.

* Tidigare, om du försökte aktivera synkronisering av lösenord hash, azure AD Connect kontrollerar inte om AD Connector-kontot har krävs behörigheter för att synkronisera lösenordshÃ¤ringar från lokala AD. Nu verifierar och varnar Azure AD Connect-guiden dig om AD Connector-kontot inte har tillräcklig behörighet.

### <a name="ad-fs-management"></a>AD FS-hantering
#### <a name="fixed-issue"></a>Åtgärdat problem 
* Åtgärdade ett problem som gällde användningen av [ms-DS-ConsistencyGuid som source](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) anchor-funktion. Det här problemet påverkar kunder som har konfigurerat *Federation med AD FS* som inloggningsmetod för användare. När du kör *Konfigurera källankare-uppgift* i guiden växlar Azure AD Connect till att använda *ms-DS-ConsistencyGuid som källattribut för immutableId. Som en del av den här ändringen försöker Azure AD Connect uppdatera anspråksreglerna för ImmutableId i AD FS. Det här steget misslyckades dock eftersom Azure AD Connect inte hade de administratörsbehörighet som krävs för att konfigurera AD FS. Med den här korrigeringsfilen uppmanar Azure AD Connect dig nu att ange administratörsautentiseringsuppgifterna för AD FS när du kör aktiviteten *Konfigurera källankare.*



## <a name="116140"></a>1.1.614.0
Status: 5 september 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="known-issues"></a>Kända problem
* Det finns ett känt problem som gör att Azure AD Connect-uppgraderingen misslyckas med felet "*Det går inte att uppgradera synkroniseringstjänsten*". Dessutom kan synkroniseringstjänsten inte längre starta med händelsefel "*Tjänsten kunde inte starta eftersom databasens version är nyare än den version av binärfilerna som installerats*". Problemet uppstår när administratören som utför uppgraderingen inte har sysadmin-behörighet till SQL-servern som används av Azure AD Connect. Dbo-behörigheter är inte tillräckliga.

* Det finns ett känt problem med Azure AD Connect-uppgradering som påverkar kunder som har aktiverat [Sömlös enkel inloggning](how-to-connect-sso.md). När Azure AD Connect har uppgraderats visas funktionen som inaktiverad i guiden, även om funktionen fortfarande är aktiverad. En korrigering för det här problemet kommer att tillhandahållas i framtida version. Kunder som är oroliga för det här visningsproblemet kan åtgärda det manuellt genom att aktivera Sömlös enkel inloggning i guiden.

#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdade ett problem som hindrade Azure AD Connect från att uppdatera anspråksreglerna i lokala AD FS samtidigt [som ms-DS-ConsistencyGuid aktiveras som källankare.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) Problemet uppstår om du försöker aktivera funktionen för en befintlig Azure AD Connect-distribution som har AD FS konfigurerat som inloggningsmetod. Problemet beror på att guiden inte frågar efter ADFS-autentiseringsuppgifter innan du försöker uppdatera anspråksreglerna i AD FS.
* Åtgärdade ett problem som orsakade att Azure AD Connect misslyckades med installationen om den lokala AD-skogen har NTLM inaktiverat. Problemet beror på azure AD Connect-guiden som inte tillhandahåller fullständiga kvalificerade autentiseringsuppgifter när de säkerhetskontexter som krävs för Kerberos-autentisering skapas. Detta medför att Kerberos-autentisering misslyckas och Azure AD Connect-guiden kan återgå till att använda NTLM.

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdade ett problem där det inte går att skapa en ny synkroniseringsregel om taggattributet inte är ifyllt.
* Åtgärdade ett problem som orsakade att Azure AD Connect kunde ansluta till lokal AD för lösenordssynkronisering med NTLM, även om Kerberos är tillgängligt. Det här problemet uppstår om den lokala AD-topologin har en eller flera domänkontrollanter som har återställts från en säkerhetskopia.
* Åtgärdade ett problem som orsakade att fullständiga synkroniseringssteg inträffade i onödan efter uppgraderingen. I allmänhet krävs fullständiga synkroniseringssteg efter uppgradering om det finns ändringar i synkroniseringsregler utanför kassan. Problemet berodde på ett fel i ändringsidentifieringslogiken som felaktigt upptäckte en ändring när synkroniseringsregeluttryck med nya radtecken uppstod. Nyradade tecken infogas i synkroniseringsregeluttryck för att förbättra läsbarheten.
* Åtgärdade ett problem som kan orsaka att Azure AD Connect-servern inte fungerar korrekt efter automatisk uppgradering. Det här problemet påverkar Azure AD Connect-servrar med version 1.1.443.0 (eller tidigare). Mer information om problemet finns i artikeln [Azure AD Connect fungerar inte korrekt efter en automatisk uppgradering](https://support.microsoft.com/help/4038479/azure-ad-connect-is-not-working-correctly-after-an-automatic-upgrade).
* Åtgärdade ett problem som kan orsaka automatisk uppgradering som ska göras om var femte minut när fel påträffas. Med korrigeringen försöker automatisk uppgradering med exponentiell back-off när fel påträffas.
* Åtgärdade ett problem där lösenordssynkroniseringshändelse 611 felaktigt visas i Windows Application Event loggar som **informativ i** stället för **fel**. Händelse 611 genereras när lösenordssynkronisering uppstår ett problem. 
* Åtgärdade ett problem i Azure AD Connect-guiden som gör att funktionen för gruppåterskrivning kan aktiveras utan att välja en organisationsenhet som krävs för gruppåterskrivning.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Lade till en felsökningsuppgift i Azure AD Connect-guiden under Ytterligare uppgifter. Kunder kan använda den här uppgiften för att felsöka problem som rör lösenordssynkronisering och samla in allmän diagnostik. I framtiden utökas felsökningsuppgiften så att den omfattar andra katalogsynkroniseringsrelaterade problem.
* Azure AD Connect stöder nu ett nytt installationsläge som kallas **Använd befintlig databas**. Med det här installationsläget kan kunder installera Azure AD Connect som anger en befintlig ADSync-databas. Mer information om den här funktionen finns i artikeln [Använd en befintlig databas](how-to-connect-install-existing-database.md).
* För förbättrad säkerhet, Azure AD Connect nu standard använder TLS1.2 för att ansluta till Azure AD för katalog synkronisering. Tidigare var standardvärdet TLS1.0.
* När Azure AD Connect Password Synchronization Agent startar försöker den ansluta till Azure AD välkänd slutpunkt för lösenordssynkronisering. När anslutningen har slutförts omdirigeras den till en regionspecifik slutpunkt. Tidigare cachelagrar lösenordssynkroniseringsagenten den regionspecifika slutpunkten tills den startas om. Nu rensar agenten cacheminnet och försöker igen med den välkända slutpunkten om den stöter på anslutningsproblem med den regionspecifika slutpunkten. Den här ändringen säkerställer att lösenordssynkronisering kan redundans till en annan regionspecifik slutpunkt när den cachelagrade regionspecifika slutpunkten inte längre är tillgänglig.
* Om du vill synkronisera ändringar från en lokal AD-skog krävs ett AD DS-konto. Du kan antingen (i) skapa AD DS-kontot själv och tillhandahålla dess autentiseringsuppgifter till Azure AD Connect, eller (ii) tillhandahålla autentiseringsuppgifter för företagsadministratören och låta Azure AD Connect skapa AD DS-kontot åt dig. Tidigare (i) är standardalternativet i Azure AD Connect-guiden. Nu är (ii) standardalternativet.

### <a name="azure-ad-connect-health"></a>Azure AD Connect Health

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Lade till stöd för Microsoft Azure Government Cloud och Microsoft Cloud Germany.

### <a name="ad-fs-management"></a>AD FS-hantering
#### <a name="fixed-issues"></a>Åtgärdade problem
* Cmdleten Initialize-ADSyncNGCKeysWriteBack i AD prep powershell-modulen tillämpade felaktigt ACL:er på enhetsregistreringsbehållaren och skulle därför bara ärva befintliga behörigheter.  Detta uppdaterades så att synkroniseringstjänstkontot har rätt behörighet.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* AAD Connect Verifiera ADFS-inloggningsuppgiften uppdaterades så att den verifierar inloggningar mot Microsoft Online och inte bara tokenhämtning från ADFS.
* När du konfigurerar en ny ADFS-servergrupp med AAD Connect flyttades sidan som ber om ADFS-autentiseringsuppgifter så att den nu inträffar innan användaren uppmanas att tillhandahålla ADFS- och WAP-servrar.  På så sätt kan AAD Connect kontrollera att det angivna kontot har rätt behörighet.
* Under AAD Connect-uppgraderingen misslyckas vi inte längre med en uppgradering om ADFS AAD Trust inte uppdateras.  Om det händer visas ett lämpligt varningsmeddelande och fortsätta att återställa förtroendet via den ytterligare AAD Connect-uppgiften.

### <a name="seamless-single-sign-on"></a>Sömlös enkel inloggning
#### <a name="fixed-issues"></a>Åtgärdade problem
* Åtgärdade ett problem som orsakade att Azure AD [Connect-guiden](how-to-connect-sso.md)returnerade ett fel om du försöker aktivera sömlös enkel inloggning . Felmeddelandet är *"Konfigurationen av Microsoft Azure AD Connect Authentication Agent misslyckades.",* Det här problemet påverkar befintliga kunder som manuellt har uppgraderat förhandsversionen av autentiseringsagenterna för [direktautentisering](how-to-connect-sso.md) baserat på stegen som beskrivs i den här [artikeln](how-to-connect-pta-upgrade-preview-authentication-agents.md).


## <a name="115610"></a>1.1.561.0
Status: 23 juli 2017

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Åtgärdat problem 

* Åtgärdade ett problem som gjorde att den färdiga synkroniseringsregeln "Out to AD - User ImmutableId" togs bort:

  * Problemet uppstår när Azure AD Connect uppgraderas eller när uppgiftsalternativet *Uppdatera synkroniseringskonfiguration* i Azure AD Connect-guiden används för att uppdatera Azure AD Connect-synkroniseringskonfigurationen.
  
  * Den här synkroniseringsregeln gäller för kunder som har aktiverat [ms-DS-ConsistencyGuid som källankare.](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) Den här funktionen introducerades i version 1.1.524.0 och efter. När synkroniseringsregeln tas bort kan Azure AD Connect inte längre fylla i lokalt AD ms-DS-ConsistencyGuid-attribut med attributet ObjectGuid. Det hindrar inte nya användare från att etableras i Azure AD.
  
  * Korrigeringen säkerställer att synkroniseringsregeln inte längre tas bort under uppgraderingen, eller under konfigurationsändringen, så länge funktionen är aktiverad. För befintliga kunder som har påverkats av det här problemet säkerställer korrigeringen också att synkroniseringsregeln läggs tillbaka efter uppgradering till den här versionen av Azure AD Connect.

* Åtgärdade ett problem som gör att direktsynkroniseringsregler har prioritetsvärde som är mindre än 100:

  * I allmänhet är prioritetsvärdena 0 - 99 reserverade för anpassade synkroniseringsregler. Under uppgraderingen uppdateras prioritetsvärdena för out-of-box-synkroniseringsregler för att hantera synkroniseringsregeländringar. På grund av det här problemet kan out-of-box synkroniseringsregler tilldelas prioritetsvärde som är mindre än 100.
  
  * Korrigeringen förhindrar att problemet uppstår under uppgraderingen. Det återställer dock inte prioritetsvärdena för befintliga kunder som har påverkats av problemet. En separat korrigering kommer att tillhandahållas i framtiden för att hjälpa till med restaureringen.

* Åtgärdade ett problem där [skärmen Domän- och organisationsenhetsfiltrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i Azure AD Connect-guiden visar *alternativet Synkronisera alla domäner och organisationsenheter* som det är valt, även om OU-baserad filtrering är aktiverad.

*   Åtgärdade ett problem som gjorde att [skärmen Konfigurera katalogpartitioner](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) i Synkroniseringstjänsthanteraren returnerade ett fel om du klickar på knappen *Uppdatera.* Felmeddelandet är *"Ett fel uppstod när domäner uppdaterades: Det gick inte att casta objekt av typen System.Collections.ArrayList" för att skriva 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheet.MaPropertyPages.PartitionObject".* Felet uppstår när ny AD-domän har lagts till i en befintlig AD-skog och du försöker uppdatera Azure AD Connect med knappen Uppdatera.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* [Funktionen Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) har utökats för att stödja kunder med följande konfigurationer:
  * Du har aktiverat funktionen för tillbakaskrivning av enheten.
  * Du har aktiverat funktionen för gruppåterskrivning.
  * Installationen är inte en Express-inställningar eller en DirSync-uppgradering.
  * Du har mer än 100 000 objekt i metaversumet.
  * Du ansluter till mer än en skog. Expressinställningen ansluter bara till en skog.
  * AD Connector-kontot är inte längre standardkontot MSOL_.
  * Servern är inställd på att vara i mellanlagringsläge.
  * Du har aktiverat funktionen för tillbakaskrivning av användare.
  
  >[!NOTE]
  >Omfattningen expansion av funktionen Automatisk uppgradering påverkar kunder med Azure AD Connect build 1.1.105.0 och efter. Om du inte vill att din Azure AD Connect-server ska uppgraderas automatiskt måste du `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`köra följande cmdlet på din Azure AD Connect-server: . Mer information om hur du aktiverar/inaktiverar automatisk uppgradering finns i artikeln [Azure AD Connect: Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md).

## <a name="115580"></a>1.1.558.0
Status: Kommer inte att släppas. Ändringar i den här versionen ingår i version 1.1.561.0.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Åtgärdat problem 

* Åtgärdade ett problem som gjorde att den färdiga synkroniseringsregeln "Out to AD - User ImmutableId" togs bort när organisationsenhetens filterkonfiguration uppdateras. Den här synkroniseringsregeln krävs för [ms-DS-ConsistencyGuid som source anchor-funktion](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

* Åtgärdade ett problem där [skärmen Domän- och organisationsenhetsfiltrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i Azure AD Connect-guiden visar *alternativet Synkronisera alla domäner och organisationsenheter* som det är valt, även om OU-baserad filtrering är aktiverad.

*   Åtgärdade ett problem som gjorde att [skärmen Konfigurera katalogpartitioner](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) i Synkroniseringstjänsthanteraren returnerade ett fel om du klickar på knappen *Uppdatera.* Felmeddelandet är *"Ett fel uppstod när domäner uppdaterades: Det gick inte att casta objekt av typen System.Collections.ArrayList" för att skriva 'Microsoft.DirectoryServices.MetadirectoryServices.UI.PropertySheet.MaPropertyPages.PartitionObject".* Felet uppstår när ny AD-domän har lagts till i en befintlig AD-skog och du försöker uppdatera Azure AD Connect med knappen Uppdatera.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* [Funktionen Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) har utökats för att stödja kunder med följande konfigurationer:
  * Du har aktiverat funktionen för tillbakaskrivning av enheten.
  * Du har aktiverat funktionen för gruppåterskrivning.
  * Installationen är inte en Express-inställningar eller en DirSync-uppgradering.
  * Du har mer än 100 000 objekt i metaversumet.
  * Du ansluter till mer än en skog. Expressinställningen ansluter bara till en skog.
  * AD Connector-kontot är inte längre standardkontot MSOL_.
  * Servern är inställd på att vara i mellanlagringsläge.
  * Du har aktiverat funktionen för tillbakaskrivning av användare.
  
  >[!NOTE]
  >Omfattningen expansion av funktionen Automatisk uppgradering påverkar kunder med Azure AD Connect build 1.1.105.0 och efter. Om du inte vill att din Azure AD Connect-server ska uppgraderas automatiskt måste du `Set-ADSyncAutoUpgrade -AutoUpgradeState disabled`köra följande cmdlet på din Azure AD Connect-server: . Mer information om hur du aktiverar/inaktiverar automatisk uppgradering finns i artikeln [Azure AD Connect: Automatisk uppgradering](how-to-connect-install-automatic-upgrade.md).

## <a name="115570"></a>1.1.557.0
Status: juli 2017

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder via azure AD Connect Auto Upgrade-funktionen.

### <a name="azure-ad-connect"></a>Azure AD Connect

#### <a name="fixed-issue"></a>Åtgärdat problem 
* Åtgärdade ett problem med cmdleten Initialize-ADSyncDomainJoinedComputerSync som orsakade att den verifierade domänen som konfigurerats på det befintliga tjänstanslutningspunktsobjektet ändrades även om den fortfarande är en giltig domän. Det här problemet uppstår när din Azure AD-klient har mer än en verifierad domän som kan användas för att konfigurera tjänstanslutningspunkten.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Återställning av lösenord är nu tillgänglig för förhandsversion med Microsoft Azure Government cloud och Microsoft Cloud Germany. Mer information om Azure AD Connect-stöd för de olika tjänstinstanserna finns i artikeln [Azure AD Connect: Särskilda överväganden för instanser](reference-connect-instances.md).

* Cmdlet initialize-ADSyncDomainJoinedComputerSync har nu en ny valfri parameter med namnet AzureADDomain. Med den här parametern kan du ange vilken verifierad domän som ska användas för att konfigurera tjänstanslutningspunkten.

### <a name="pass-through-authentication"></a>Direktautentisering

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Namnet på agenten som krävs för direktautentisering har ändrats från *Microsoft Azure AD Application Proxy Connector* till Microsoft Azure AD Connect Authentication *Agent*.

* Om du aktiverar direktautentisering aktiveras inte längre synkronisering av lösenordsh hash som standard.


## <a name="115530"></a>1.1.553.0
Status: juni 2017

> [!IMPORTANT]
> Det finns schema- och synkroniseringsregeländringar som introduceras i den här versionen. Synkroniseringstjänsten för Azure AD Connect utlöser fullständiga import- och fullständiga synkroniseringssteg efter uppgraderingen. Information om ändringarna beskrivs nedan. Om du tillfälligt vill skjuta upp stegen för fullständig import och fullständig synkronisering efter uppgraderingen läser du artikeln [Så här skjuter du upp fullständig synkronisering efter uppgradering](how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).
>
>

### <a name="azure-ad-connect-sync"></a>Azure AD Connect Sync

#### <a name="known-issue"></a>Kända problem
* Det finns ett problem som påverkar kunder som använder [OU-baserad filtrering](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) med Azure AD Connect-synkronisering. När du navigerar till [sidan Domän- och organisationsenhetsfiltrering](how-to-connect-install-custom.md#domain-and-ou-filtering) i Azure AD Connect-guiden förväntas följande beteende:
  * Om organisationsenhetsbaserad filtrering är aktiverad markeras alternativet **Synkronisera valda domäner och organisationsenheter.**
  * Annars är alternativet **Synkronisera alla domäner och ru:er** markerat.

Problemet som uppstår är att **alternativet Synkronisera alla domäner och us alltid** är markerat när du kör guiden.  Detta inträffar även om OU-baserad filtrering tidigare har konfigurerats. Innan du sparar några AAD Connect-konfigurationsändringar kontrollerar du att **alternativet Synkronisera valda domäner och us är markerat** och bekräfta att alla ru:er som behöver synkroniseras är aktiverade igen. Annars inaktiveras OU-baserad filtrering.

#### <a name="fixed-issues"></a>Åtgärdade problem

* Åtgärdade ett problem med tillbakaskrivning av lösenord som gör att en Azure AD-administratör kan återställa lösenordet för ett lokalt AD-privilegierat användarkonto. Problemet uppstår när Azure AD Connect beviljas behörigheten Återställ lösenord över det privilegierade kontot. Problemet åtgärdas i den här versionen av Azure AD Connect genom att inte tillåta en Azure AD-administratör att återställa lösenordet för ett godtyckligt lokalt AD-privilegierat användarkonto om inte administratören är ägare till det kontot. Mer information finns i [Security Advisory 4033453](https://technet.microsoft.com/library/security/4033453).

* Åtgärdade ett problem som var relaterat till [ms-DS-ConsistencyGuid som källankare-funktion](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) där Azure AD Connect inte skrivs tillbaka till det lokala AD ms-DS-ConsistencyGuid-attributet. Problemet uppstår när det finns flera lokala AD-skogar tillagda i Azure AD Connect och *användaridentiteterna finns över flera kataloger alternativet* är markerat. När en sådan konfiguration används fyller de resulterande synkroniseringsreglerna inte i attributet sourceAnchorBinary i Metaverse. Attributet sourceAnchorBinary används som källattribut för attributet ms-DS-ConsistencyGuid. Därför sker inte tillbakaskrivning till attributet ms-DSConsistencyGuid. För att åtgärda problemet har följande synkroniseringsregler uppdaterats för att säkerställa att attributet sourceAnchorBinary i Metaverse alltid fylls i:
  * In från AD - InetOrgPerson AccountEnabled.xml
  * In från AD - InetOrgPerson Common.xml
  * In från AD - AnvändarkontoEnabled.xml
  * In från AD - User Common.xml
  * In från AD - Användare Gå SOAInAAD.xml

* Tidigare, även om [ms-DS-ConsistencyGuid som Source Anchor-funktionen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) inte är aktiverad, läggs synkroniseringsregeln "Ut till AD – User ImmutableId" fortfarande till Azure AD Connect. Effekten är godartad och orsakar inte tillbakaskrivning av attributet ms-DS-ConsistencyGuid. För att undvika förvirring har logik lagts till för att säkerställa att synkroniseringsregeln endast läggs till när funktionen är aktiverad.

* Åtgärdade ett problem som orsakade att synkroniseringen av lösenordsh hash-synkronisering misslyckades med felhändelse 611. Det här problemet uppstår när en eller flera domänkontrollanter har tagits bort från lokala AD. I slutet av varje lösenordssynkroniseringscykel innehåller synkroniseringscookien som utfärdas av lokala AD-ID:n för de borttagna domänkontrollanterna med USN-värdet (Update Sequence Number) på 0. Lösenordssynkroniseringshanteraren kan inte bevara synkroniseringscookien som innehåller USN-värdet 0 och misslyckas med felhändelse 611. Under nästa synkroniseringscykel återanvänder Lösenordssynkroniseringshanteraren den senast sparade synkroniseringscookien som inte innehåller USN-värdet 0. Detta medför att samma lösenordsändringar ska synkroniseras om. Med den här korrigeringsfilen beständigs synkroniseringshanteraren synkroniseringscookien korrekt.

* Tidigare, även om automatisk uppgradering har inaktiverats med hjälp av cmdlet set-ADSyncAutoUpgrade, fortsätter den automatiska uppgraderingsprocessen att söka efter uppgradering med jämna mellanrum och förlitar sig på den hämtade avbetalningsinstallationen för att uppfylla inaktiveringen. Med den här korrigeringsfilen söker inte längre den automatiska uppgraderingsprocessen efter uppgradering med jämna mellanrum. Korrigeringen tillämpas automatiskt när uppgraderingsinstallatör för den här Azure AD Connect-versionen körs en gång.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

* Tidigare var [ms-DS-ConsistencyGuid som source anchor-funktionen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor) endast tillgänglig för nya distributioner. Nu är den tillgänglig för befintliga distributioner. Mer specifikt:
  * Om du vill komma åt funktionen startar du Azure AD Connect-guiden och väljer alternativet *Uppdatera källankare.*
  * Det här alternativet är bara synligt för befintliga distributioner som använder objectGuid som sourceAnchor-attribut.
  * När du konfigurerar alternativet validerar guiden tillståndet för attributet ms-DS-ConsistencyGuid i den lokala Active Directory. Om attributet inte är konfigurerat för något användarobjekt i katalogen används ms-DS-ConsistencyGuid som sourceAnchor-attribut. Om attributet är konfigurerat på ett eller flera användarobjekt i katalogen, drar guiden slutsatsen att attributet används av andra program och är inte lämpligt som sourceAnchor-attribut och tillåter inte att source anchor-ändringen fortsätter. Om du är säker på att attributet inte används av befintliga program måste du kontakta supporten för information om hur du undertrycker felet.

* Azure AD Connect är specifikt för **attributet UserCertificate** på enhetsobjekt och söker nu efter certifikatvärden som krävs för [att ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelse](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) och filtrerar bort resten innan du synkroniserar med Azure AD. För att aktivera detta beteende har den färdiga synkroniseringsregeln "Out to AAD - Device Join SOAInAD" uppdaterats.

* Azure AD Connect stöder nu tillbakaskrivning av Exchange Online **cloudPublicDelegas-attribut** till lokala AD **publicDelegas-attribut.** Detta gör det möjligt att scenariot där en Exchange Online-postlåda kan beviljas SendOnBehalfTo-rättigheter till användare med lokal Exchange-postlåda. För att stödja den här funktionen har en ny out-of-box-synkroniseringsregel "Out to AD – User Exchange Hybrid PublicDelegates writeback" lagts till. Den här synkroniseringsregeln läggs bara till i Azure AD Connect när Exchange Hybrid-funktionen är aktiverad.

* Azure AD Connect stöder nu synkronisering av **altRecipient-attributet** från Azure AD. För att stödja den här ändringen har följande out-of-box synkroniseringsregler uppdaterats för att inkludera det attributflöde som krävs:
  * In från AD – Användarutbyte
  * Ut till AAD – Användare ExchangeOnline
  
* Attributet **cloudSOAExchMailbox** i Metaverse anger om en viss användare har Exchange Online-postlådan eller inte. Dess definition har uppdaterats för att inkludera ytterligare Exchange Online RecipientDisplayTypes som utrustning och konferensrumspostlådor. För att aktivera den här ändringen har definitionen av attributet cloudSOAExchMailbox, som hittas under den färdiga synkroniseringsregeln "In från AAD – User Exchange Hybrid", uppdaterats från:

```
CBool(IIF(IsNullOrEmpty([cloudMSExchRecipientDisplayType]),NULL,BitAnd([cloudMSExchRecipientDisplayType],&amp;HFF) = 0))
```

... till följande:

```
CBool(
  IIF(IsPresent([cloudMSExchRecipientDisplayType]),(
    IIF([cloudMSExchRecipientDisplayType]=0,True,(
      IIF([cloudMSExchRecipientDisplayType]=2,True,(
        IIF([cloudMSExchRecipientDisplayType]=7,True,(
          IIF([cloudMSExchRecipientDisplayType]=8,True,(
            IIF([cloudMSExchRecipientDisplayType]=10,True,(
              IIF([cloudMSExchRecipientDisplayType]=16,True,(
                IIF([cloudMSExchRecipientDisplayType]=17,True,(
                  IIF([cloudMSExchRecipientDisplayType]=18,True,(
                    IIF([cloudMSExchRecipientDisplayType]=1073741824,True,(
                       IF([cloudMSExchRecipientDisplayType]=1073741840,True,False)))))))))))))))))))),False))

```

* Lade till följande uppsättning X509Certificate2-kompatibla funktioner för att skapa synkroniseringsregeluttryck för att hantera certifikatvärden i attributet userCertificate:

    ||||
    | --- | --- | --- |
    |CertSubject|CertIssuer|CertKeyAlgorithm|
    |CertSubjectNameDN|CertIssuerOid|CertNameInfo|
    |CertSubjectNameOid|CertIssuerDN|IsCert (IsCert)|
    |CertFriendlyName|CertThumbprint|CertExtensionOids|
    |CertFormat (CertFormat)|CertNotAfter|CertPublicKeyOid|
    |CertSerialNumber|CertNotBefore|CertPublicKeyParametersOid|
    |CertVersion (CertVersion)|CertSignatureAlgorithmOid|Välj|
    |CertKeyAlgorithmParams|CertHashString (CertHashString)|Var|
    |||With|

* Följande schemaändringar har införts så att kunder kan skapa anpassade synkroniseringsregler för att flöda sAMAccountName, domainNetBios och domainFQDN för gruppobjekt samt distinguishedName för användarobjekt:

  * Följande attribut har lagts till i MV-schemat:
    * Grupp: Kontonamn
    * Grupp: domänNetBios
    * Grupp: domainFQDN
    * Person: distinguishedName

  * Följande attribut har lagts till i Azure AD Connector-schemat:
    * Grupp: OnPremisesSamAccountName
    * Grupp: NetBiosName
    * Grupp: DnsDomainName
    * Användare: OnPremisesDistinguishedName

* ADSyncDomainJoinedComputerSync cmdlet-skriptet har nu en ny valfri parameter med namnet AzureEnvironment. Parametern används för att ange vilken region motsvarande Azure Active Directory-klient är värd. Giltiga värden inkluderar:
  * AzureCloud (standard)
  * AzureChinaCloud
  * AzureGermanyCloud
  * USA:s regering
 
* Uppdaterad Redigerare för synkroniseringsregel för att använda Anslut (i stället för etablering) som standardvärde för länktyp under skapandet av synkroniseringsregeln.

### <a name="ad-fs-management"></a>AD FS-hantering

#### <a name="issues-fixed"></a>Problem åtgärdade

* Följande URL:er är nya WS-Federation-slutpunkter som introduceras av Azure AD för att förbättra motståndskraften mot autentiseringsbrott och läggs till i lokal AD FS-svarspartsförtroendekonfiguration:
  * https://ests.login.microsoftonline.com/login.srf
  * https://stamp2.login.microsoftonline.com/login.srf
  * https://ccs.login.microsoftonline.com/login.srf
  * https://ccs-sdf.login.microsoftonline.com/login.srf
  
* Åtgärdade ett problem som orsakade ad fs att generera felaktigt anspråksvärde för UtfärdareID. Problemet uppstår om det finns flera verifierade domäner i Azure AD-klienten och domänsuffixet för attributet userPrincipalName som används johndoe@us.contoso.comför att generera IssuerID-anspråket är minst 3-nivåer djup (till exempel ). Problemet löses genom att uppdatera den regex som används av anspråksreglerna.

#### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
* Tidigare kan ADFS-certifikathanteringsfunktionen som tillhandahålls av Azure AD Connect endast användas med ADFS-grupper som hanteras via Azure AD Connect. Nu kan du använda funktionen med ADFS-grupper som inte hanteras med Azure AD Connect.

## <a name="115240"></a>1.1.524.0
Släppt: maj 2017

> [!IMPORTANT]
> Det finns schema- och synkroniseringsregeländringar som introduceras i den här versionen. Azure AD Connect Synchronization Service utlöser fullständiga import- och fullständiga synkroniseringssteg efter uppgraderingen. Information om ändringarna beskrivs nedan.
>
>

**Fasta problem:**

Azure AD Connect-synkronisering

* Åtgärdade ett problem som gör att automatisk uppgradering sker på Azure AD Connect-servern även om kunden har inaktiverat funktionen med cmdlet set-ADSyncAutoUpgrade. Med den här korrigeringsfilen söker den automatiska uppgraderingsprocessen på servern fortfarande efter uppgradering med jämna mellanrum, men den hämtade installationsprogrammet uppfyller konfigurationen för automatisk uppgradering.
* Under DirSync-uppgradering på plats skapar Azure AD Connect ett Azure AD-tjänstkonto som ska användas av Azure AD-anslutningen för synkronisering med Azure AD. När kontot har skapats autentiserar Azure AD Connect med Azure AD med kontot. Ibland misslyckas autentiseringen på grund av tillfälliga problem, vilket i sin tur gör att DirSync-uppgradering på plats misslyckas med felet *"Ett fel har inträffat när konfigurera AAD-synkroniseringsuppgift: AADSTS50034: För att logga in på det här programmet måste kontot läggas till i xxx.onmicrosoft.com-katalogen."* För att förbättra återhämtningen för DirSync-uppgraderingen försöker Azure AD Connect nu om autentiseringssteget.
* Det uppstod ett problem med build 443 som gör att DirSync-uppgradering på plats lyckas men att köra profiler som krävs för katalogsynkronisering skapas inte. Helande logik ingår i den här versionen av Azure AD Connect. När kunden uppgraderar till den här versionen identifierar Azure AD Connect saknade körningsprofiler och skapar dem.
* Åtgärdade ett problem som gör att lösenordssynkroniseringsprocessen inte kan startas med händelse-ID 6900 och felet *"Ett objekt med samma nyckel har redan lagts till".* Det hÃ¤r problemet uppstÃ¥r om du uppdaterar OU-filtreringskonfigurationen fÃ¶r att inkludera AD-konfigurationspartitionen. För att åtgärda problemet synkroniserar lösenordssynkroniseringsprocessen nu endast lösenordsändringar från AD-domänpartitioner. Icke-domänpartitioner som konfigurationspartition hoppas över.
* Under Express-installationen skapar Azure AD Connect ett lokalt AD DS-konto som ska användas av AD-kopplingen för att kommunicera med lokala AD. Tidigare har kontot skapats med flaggan PASSWD_NOTREQD som angetts på attributet user-Account-Control och ett slumpmässigt lösenord anges på kontot. Nu tar Azure AD Connect uttryckligen bort flaggan PASSWD_NOTREQD när lösenordet har angetts för kontot.
* Åtgärdade ett problem som gör att DirSync-uppgraderingen misslyckas med felet *"ett dödläge uppstod i SQL-servern som försöker hämta ett programlås"* när attributet mailNickname finns i det lokala AD-schemat, men inte är bundet till AD-användarobjektklassen.
* Åtgärdade ett problem som gör att enhetens tillbakaskrivningsfunktion inaktiveras automatiskt när en administratör uppdaterar Azure AD Connect-synkroniseringskonfiguration med Azure AD Connect-guiden. Det här problemet orsakas av att guiden utför en förutsättningskontroll för den befintliga enhetsåterställningskonfigurationen i lokal AD och kontrollen misslyckas. Korrigeringen är att hoppa över kontrollen om enhetens tillbakaskrivning redan är aktiverat tidigare.
* Om du vill konfigurera OU-filtrering kan du antingen använda Azure AD Connect-guiden eller Synkroniseringstjänsthanteraren. Tidigare, om du använder Azure AD Connect-guiden för att konfigurera organisationsenhetsfiltrering, inkluderas nya organisationsenheter som skapats efteråt för katalogsynkronisering. Om du inte vill att nya organisationsenheter ska inkluderas måste du konfigurera organisationsenhetens filtrering med synkroniseringstjänsthanteraren. Nu kan du uppnå samma beteende med azure AD Connect-guiden.
* Åtgärdade ett problem som gör att lagrade procedurer som krävs av Azure AD Connect skapas under installationsadministratörens schema i stället för under dbo-schemat.
* Åtgärdade ett problem som gör att Attributet TrackingId som returneras av Azure AD utelämnades i händelseloggarna för AAD Connect-servern. Problemet uppstår om Azure AD Connect tar emot ett omdirigeringsmeddelande från Azure AD och Azure AD Connect inte kan ansluta till slutpunkten som tillhandahålls. TrackingId används av supporttekniker för att korrelera med servicesidans loggar under felsökning.
* När Azure AD Connect tar emot LargeObject-fel från Azure AD genererar Azure AD Connect en händelse med EventID 6941 och meddelandet *"Det etablerade objektet är för stort. Trimma antalet attributvärden för det här objektet."* Samtidigt genererar Azure AD Connect också en vilseledande händelse med EventID 6900 och meddelandet *"Microsoft.Online.Coexistence.ProvisionRetryException: Lyckades inte kommunicera med Windows Azure Active Directory-tjänsten."* För att minimera förvirring genererar Azure AD Connect inte längre den senare händelsen när LargeObject-fel tas emot.
* Åtgärdade ett problem som gör att Synkroniseringstjänsthanteraren inte svarar när du försöker uppdatera konfigurationen för allmän LDAP-anslutning.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Synkroniseringsregeländringar – Följande synkroniseringsregeländringar har implementerats:
  * Uppdaterad standardsynkroniseringsregel inställd på att inte exportera attribut **userCertificate** och **userSMIMECertificate** om attributen har fler än 15 värden.
  * AD-attribut **employeeID** och **msExchBypassModerationLink** ingår nu i standardsynkroniseringsregeluppsättningen.
  * **AD-attributfoto** har tagits bort från standardsynkroniseringsregeluppsättningen.
  * Lade till **preferredDataLocation** till Metaverse-schemat och AAD Connector-schemat. Kunder som vill uppdatera båda attributen i Azure AD kan implementera anpassade synkroniseringsregler för att göra det. 
  * Lade till **userType** i metaversumschemat och AAD Connector-schemat. Kunder som vill uppdatera båda attributen i Azure AD kan implementera anpassade synkroniseringsregler för att göra det.

* Azure AD Connect gör det nu automatiskt möjligt att använda Attributet ConsistencyGuid som source anchor-attribut för lokala AD-objekt. Dessutom fyller Azure AD Connect attributet ConsistencyGuid med attributet objectGuid om det är tomt. Den här funktionen gäller endast för ny distribution. Mer information om den här funktionen finns i artikelavsnittet [Azure AD Connect: Design concepts - Använda ms-DS-ConsistencyGuid som sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).
* Nya felsökning cmdlet Invoke-ADSyncDiagnostics har lagts till för att diagnostisera lösenord hash synkronisering relaterade problem. Information om hur du använder cmdlet finns i artikeln [Felsöka synkronisering av lösenordshã¤nde med Azure AD Connect-synkronisering](tshoot-connect-password-hash-synchronization.md).
* Azure AD Connect stöder nu synkronisering av E-postaktiverade gemensamma mappobjekt från lokalt AD till Azure AD. Du kan aktivera funktionen med hjälp av Azure AD Connect-guiden under Valfria funktioner. Mer information om den här funktionen finns i artikeln [stöd för office 365-katalogbaserad kantblockering för lokala e-postaktiverade gemensamma mappar](https://blogs.technet.microsoft.com/exchange/2017/05/19/office-365-directory-based-edge-blocking-support-for-on-premises-mail-enabled-public-folders).
* Azure AD Connect kräver ett AD DS-konto för att synkronisera från lokala AD. Tidigare, om du installerade Azure AD Connect med expressläget, kan du ange autentiseringsuppgifterna för ett Enterprise Admin-konto och Azure AD Connect skulle skapa det AD DS-konto som krävs. För en anpassad installation och lägga till skogar i en befintlig distribution måste du dock ange AD DS-kontot i stället. Nu har du också möjlighet att ange autentiseringsuppgifter för ett Enterprise Admin-konto under en anpassad installation och låta Azure AD Connect skapa det AD DS-konto som krävs.
* Azure AD Connect stöder nu SQL AOA. Du måste aktivera SQL AOA innan du installerar Azure AD Connect. Under installationen identifierar Azure AD Connect om den angivna SQL-instansen är aktiverad för SQL AOA eller inte. Om SQL AOA är aktiverat räknar Azure AD Connect vidare ut om SQL AOA är konfigurerat för att använda synkron replikering eller asynkron replikering. När du ställer in tillgänglighetsgrupplyssnaren rekommenderar vi att du anger egenskapen RegisterAllProvidersIP till 0. Den här rekommendationen beror på att Azure AD Connect för närvarande använder SQL Native Client för att ansluta till SQL och SQL Native Client inte stöder användning av MultiSubNetFailover-egenskapen.
* Om du använder LocalDB som databas för din Azure AD Connect-server och har nått sin storleksgräns på 10 GB startar inte längre synkroniseringstjänsten. Tidigare måste du utföra ShrinkDatabase-åtgärden på LocalDB för att frigöra tillräckligt med DB-utrymme för att synkroniseringstjänsten ska kunna starta. Därefter kan du använda Synkroniseringstjänsthanteraren för att ta bort körningshistorik för att frigöra mer DB-utrymme. Nu kan du använda Start-ADSyncPurgeRunHistory cmdlet för att rensa körhistorikdata från LocalDB för att återta DB-utrymme. Dessutom stöder den här cmdleten ett offlineläge (genom att ange parametern -offline) som kan användas när synkroniseringstjänsten inte körs. Offlineläget kan bara användas om synkroniseringstjänsten inte körs och databasen som används är LocalDB.
* För att minska mängden lagringsutrymme som krävs komprimerar Azure AD Connect nu synkroniseringsfelinformation innan de lagras i LocalDB/SQL-databaser. När du uppgraderar från en äldre version av Azure AD Connect till den här versionen utför Azure AD Connect en engångskomprimering på befintlig synkroniseringsfelinformation.
* Tidigare, efter uppdatering av organisationsenhetsfiltreringskonfiguration, måste du köra Fullständig import manuellt för att säkerställa att befintliga objekt inkluderas/utesluts från katalogsynkronisering. Nu utlöser Azure AD Connect automatiskt Fullständig import under nästa synkroniseringscykel. Dessutom tillämpas fullständig import endast på AD-kopplingar som påverkas av uppdateringen. Den här förbättringen gäller endast ou-filtreringsuppdateringar som görs med azure AD Connect-guiden. Det är inte tillämpligt på OU-filtreringsuppdatering som görs med synkroniseringstjänsthanteraren.
* Tidigare har gruppbaserad filtrering endast stöd för användare, grupper och kontaktobjekt. Gruppbaserad filtrering stöder nu även datorobjekt.
* Tidigare kan du ta bort Connector Space-data utan att inaktivera Azure AD Connect-synkroniseringsschemaläggare. Nu blockerar Synkroniseringstjänsthanteraren borttagningen av Anslutningsutrymmesdata om den upptäcker att schemaläggaren är aktiverad. Vidare returneras en varning för att informera kunder om potentiell dataförlust om anslutningsutrymmedata tas bort.
* Tidigare måste du inaktivera PowerShell-transkription för Azure AD Connect-guiden för att köras korrekt. Det här problemet är delvis löst. Du kan aktivera PowerShell-transkription om du använder Azure AD Connect-guiden för att hantera synkroniseringskonfiguration. Du måste inaktivera PowerShell-transkription om du använder Azure AD Connect-guiden för att hantera ADFS-konfiguration.



## <a name="114860"></a>1.1.486.0
Släppt: april 2017

**Fasta problem:**
* Åtgärdade problemet där Azure AD Connect inte kommer att installeras på lokaliserad version av Windows Server.

## <a name="114840"></a>1.1.484.0
Släppt: april 2017

**Kända problem:**

* Den här versionen av Azure AD Connect installeras inte om följande villkor är sanna:
   1. Du utför antingen DirSync på plats uppgradering eller ny installation av Azure AD Connect.
   2. Du använder en lokaliserad version av Windows Server där namnet på den inbyggda administratörsgruppen på servern inte är "Administratörer".
   3. Du använder standard-SQL Server 2012 Express LocalDB som installeras med Azure AD Connect i stället för att tillhandahålla din egen fullständiga SQL.

**Fasta problem:**

Azure AD Connect-synkronisering
* Åtgärdade ett problem där synkroniseringsschemat hoppar över hela synkroniseringssteget om en eller flera kopplingar saknas körprofil för det synkroniseringssteget. Du har till exempel lagt till en anslutningsapp med synkroniseringstjänsthanteraren manuellt utan att skapa en deltaimportkörningsprofil för den. Den här korrigeringsfilen säkerställer att synkroniseringsschemat fortsätter att köra Delta Import för andra kopplingar.
* Åtgärdade ett problem där synkroniseringstjänsten omedelbart slutar bearbeta en körningsprofil när det uppstår ett problem med ett av körningsstegen. Den här korrigeringsfilen säkerställer att synkroniseringstjänsten hoppar över det körningssteget och fortsätter att bearbeta resten. Du har till exempel en deltaimportkörningsprofil för AD-kopplingen med flera körningssteg (en för varje lokal AD-domän). Synkroniseringstjänsten kör Delta Import med de andra AD-domänerna även om en av dem har problem med nätverksanslutningen.
* Åtgärdade ett problem som gör att Azure AD Connector-uppdateringen hoppas över under automatisk uppgradering.
* Åtgärdade ett problem som gör att Azure AD Connect felaktigt avgör om servern är en domänkontrollant under installationen, vilket i sin tur gör att DirSync-uppgraderingen misslyckas.
* Åtgärdade ett problem som gör att DirSync-uppgradering på plats inte skapar någon körningsprofil för Azure AD Connector.
* Åtgärdade ett problem där användargränssnittet i Synkroniseringstjänsthanteraren inte svarar när du försöker konfigurera Generic LDAP Connector.

AD FS-hantering
* Åtgärdade ett problem där Azure AD Connect-guiden misslyckas om AD FS-den primära noden har flyttats till en annan server.

SSO för stationära datorer
* Åtgärdade ett problem i Azure AD Connect-guiden där inloggningsskärmen inte låter dig aktivera Desktop SSO-funktionen om du väljer Lösenordssynkronisering som inloggningsalternativ under nyinstallation.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Azure AD Connect Sync stöder nu användningen av konto för virtuell tjänst, hanterat tjänstkonto och grupphanterad tjänstkonto som tjänstkonto. Detta gäller endast nyinstallation av Azure AD Connect. När du installerar Azure AD Connect:
    * Som standard skapar Azure AD Connect-guiden ett virtuellt tjänstkonto och använder det som tjänstkonto.
    * Om du installerar på en domänkontrollant återgår Azure AD Connect till tidigare beteende där det skapar ett domänanvändarkonto och använder det som tjänstkonto i stället.
    * Du kan åsidosätta standardbeteendet genom att ange något av följande:
      * Ett grupphanterad tjänstkonto
      * Ett hanterat tjänstkonto
      * Ett domänanvändarkonto
      * Ett lokalt användarkonto
* Tidigare, om du uppgraderar till en ny version av Azure AD Connect som innehåller anslutningar uppdatera eller synkronisera regeländringar, Azure AD Connect kommer att utlösa en fullständig synkroniseringscykel. Nu utlöser Azure AD Connect selektivt fullständigt importsteg endast för kopplingar med uppdatering och fullständigt synkroniseringssteg endast för kopplingar med synkroniseringsregeländringar.
* Tidigare gäller tröskelvärdet för exportborttagning endast för export som utlöses via synkroniseringsschemat. Nu utökas funktionen så att den omfattar export som utlöses manuellt av kunden med hjälp av Synkroniseringstjänsthanteraren.
* På din Azure AD-klient finns det en tjänstkonfiguration som anger om funktionen lösenordssynkronisering är aktiverad för din klient eller inte. Tidigare är det enkelt att tjänstkonfigurationen konfigureras felaktigt av Azure AD Connect när du har en aktiv och en mellanlagringsserver. Nu försöker Azure AD Connect att hålla tjänstkonfigurationen konsekvent med din aktiva Azure AD Connect-server.
* Azure AD Connect-guiden identifierar och returnerar nu en varning om lokal AD inte har AD-papperskorgen aktiverad.
* Tidigare har Export till Azure AD time out och misslyckas om den sammanlagda storleken på objekten i batchen överskrider vissa tröskelvärden. Nu kommer synkroniseringstjänsten att försöka skicka om objekten i separata, mindre batchar om problemet påträffas.
* Synkroniseringstjänstnyckelhanteringsprogrammet har tagits bort från Start-menyn i Windows. Hantering av krypteringsnyckeln kommer att fortsätta att stödjas via kommandoradsgränssnittet med miiskmu.exe. Information om hur du hanterar krypteringsnyckel finns i artikeln [Abandoning the Azure AD Connect Sync encryption key](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-adsync-service-account-encryption-key).
* Tidigare, om du ändrar azure AD Connect-synkroniseringstjänstkontots lösenord, kommer synkroniseringstjänsten inte att kunna starta korrekt förrän du har övergett krypteringsnyckeln och initierat lösenordet för Azure AD Connect-synkroniseringstjänstkontot. Nu behövs inte längre den här processen.

SSO för stationära datorer

* Azure AD Connect-guiden kräver inte längre att port 9090 öppnas i nätverket när du konfigurerar direktautentisering och SSO för stationära datorer. Endast port 443 krävs. 

## <a name="114430"></a>1.1.443.0
Släppt: mars 2017

**Fasta problem:**

Azure AD Connect-synkronisering
* Åtgärdade ett problem som gör att Azure AD Connect-guiden misslyckas om visningsnamnet för Azure AD Connector inte innehåller den ursprungliga onmicrosoft.com domän som tilldelats Azure AD-klienten.
* Åtgärdade ett problem som gör att Azure AD Connect-guiden misslyckas när anslutningen till SQL-databasen när lösenordet för synkroniseringstjänstkontot innehåller specialtecken som apostrof, kolon och utrymme.
* Åtgärdade ett problem som gör att felet "Avbildningen har ett ankare som skiljer sig från avbildningen" som ska inträffa på en Azure AD Connect-server i mellanlagringsläge, efter att du tillfälligt har uteslutit ett lokalt AD-objekt från synkronisering och sedan inkluderat det igen för synkronisering.
* Åtgärdade ett problem som gör att felet "Objektet som finns av DN är en fantom" ska inträffa på en Azure AD Connect-server i mellanlagringsläge, efter att du tillfälligt har uteslutit ett lokalt AD-objekt från synkronisering och sedan inkluderat det igen för synkronisering.

AD FS-hantering
* Åtgärdade ett problem där Azure AD Connect-guiden inte uppdaterar AD FS-konfigurationen och anger rätt anspråk på den förlitande partens förtroende efter att alternativt inloggnings-ID har konfigurerats.
* Åtgärdade ett problem där Azure AD Connect-guiden inte kan hantera AD FS-servrar vars tjänstkonton är konfigurerade med userPrincipalName-format i stället för sAMAccountName-format.

Direktautentisering
* Åtgärdade ett problem som gör att Azure AD Connect-guiden misslyckas om Pass Through Authentication har valts men registreringen av dess anslutningsapp misslyckas.
* Åtgärdade ett problem som gör att Azure AD Connect-guiden kringgår valideringskontroller av inloggningsmetod som valts när desktop SSO-funktionen är aktiverad.

Lösenordsåterställning
* Åtgärdade ett problem som kan orsaka att Azure AAD Connect-servern inte försöker ansluta igen om anslutningen har dödats av en brandvägg eller proxy.

**Nya funktioner/förbättringar:**

Azure AD Connect-synkronisering
* Get-ADSyncScheduler cmdlet returnerar nu en ny boolesk egenskap med namnet SyncCycleInProgress. Om det returnerade värdet är sant betyder det att det pågår en schemalagd synkroniseringscykel.
* Målmappen för lagring av Azure AD Connect-installations- och installationsloggar har flyttats från %localappdata%\AADConnect till %programdata%\AADConnect för att förbättra tillgängligheten till loggfilerna.

AD FS-hantering
* Lade till stöd för uppdatering av AD FS-servergrupp TLS/SSL-certifikat.
* Lade till stöd för hantering av AD FS 2016.
* Du kan nu ange befintliga gMSA (Group Managed Service Account) under AD FS-installationen.
* Du kan nu konfigurera SHA-256 som signaturhh-algoritm för Azure AD-förlitande part förtroende.

Lösenordsåterställning
* Introducerade förbättringar så att produkten kan fungera i miljöer med strängare brandväggsregler.
* Förbättrad anslutningstillförlitlighet till Azure Service Bus.

## <a name="113800"></a>1.1.380.0
Släppt: december 2016

**Fast problem:**

* Åtgärdade problemet där problemregeln för Active Directory Federation Services (AD FS) saknas i den här versionen.

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder via azure AD Connect Auto Upgrade-funktionen.

## <a name="113710"></a>1.1.371.0
Släppt: december 2016

**Känt problem:**

* Regeln issuerid-anspråk för AD FS saknas i den här versionen. Regeln problemrid-anspråk krävs om du matar flera domäner med Azure Active Directory (Azure AD). Om du använder Azure AD Connect för att hantera din lokala AD FS-distribution, tar uppgradering till den här versionen bort den befintliga problemrid-anspråksregeln från din AD FS-konfiguration. Du kan komma runt problemet genom att lägga till anspråksregeln för issuerid efter installationen/uppgraderingen. Mer information om hur du lägger till anspråksregeln för issuerid finns i den här artikeln om [stöd för flera domäner för federerning med Azure AD](how-to-connect-install-multiple-domains.md).

**Fast problem:**

* Om port 9090 inte öppnas för den utgående anslutningen misslyckas installationen eller uppgraderingen av Azure AD Connect.

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder via azure AD Connect Auto Upgrade-funktionen.

## <a name="113700"></a>1.1.370.0
Släppt: december 2016

**Kända problem:**

* Regeln issuerid-anspråk för AD FS saknas i den här versionen. Regeln för problemrederianspråk krävs om du matar flera domäner med Azure AD. Om du använder Azure AD Connect för att hantera din lokala AD FS-distribution, tar uppgradering till den här versionen bort den befintliga problemrid-anspråksregeln från din AD FS-konfiguration. Du kan komma runt problemet genom att lägga till anspråksregeln för problemrÃ¤nning efter installation/uppgradering. Mer information om hur du lägger till problemrÃ¤ndefÃ¤ndesregeln finns i den hÃ¤r artikeln [fÃ¤r fÃ¤ã¤ger fÃ¤r tilltÃ¤ndes med Azure AD](how-to-connect-install-multiple-domains.md).
* Port 9090 måste vara öppen utgående för att slutföra installationen.

**Nya funktioner:**

* Direktautentisering (förhandsversion).

>[!NOTE]
>Den här versionen är inte tillgänglig för kunder via azure AD Connect Auto Upgrade-funktionen.

## <a name="113430"></a>1.1.343.0
Släppt: November 2016

**Känt problem:**

* Regeln issuerid-anspråk för AD FS saknas i den här versionen. Regeln för problemrederianspråk krävs om du matar flera domäner med Azure AD. Om du använder Azure AD Connect för att hantera din lokala AD FS-distribution, tar uppgradering till den här versionen bort den befintliga problemrid-anspråksregeln från din AD FS-konfiguration. Du kan komma runt problemet genom att lägga till anspråksregeln för problemrÃ¤nning efter installation/uppgradering. Mer information om hur du lägger till problemrÃ¤ndefÃ¤ndesregeln finns i den hÃ¤r artikeln [fÃ¤r fÃ¤ã¤ger fÃ¤r tilltÃ¤ndes med Azure AD](how-to-connect-install-multiple-domains.md).

**Fasta problem:**

* Ibland misslyckas installationen av Azure AD Connect eftersom det inte går att skapa ett lokalt tjänstkonto vars lösenord uppfyller den komplexitetsnivå som anges av organisationens lösenordsprincip.
* Åtgärdade ett problem där kopplingsregler inte omvärderas när ett objekt i kopplingsutrymmet samtidigt blir utanför omfånget för en kopplingsregel och blir in-scope för en annan. Detta kan inträffa om du har två eller flera join regler vars koppling villkor är ömsesidigt uteslutande.
* Åtgärdade ett problem där inkommande synkroniseringsregler (från Azure AD), som inte innehåller kopplingsregler, inte bearbetas om de har lägre prioritetsvärden än de som innehåller kopplingsregler.

**Förbättringar:**

* Lade till stöd för installation av Azure AD Connect på Windows Server 2016 Standard eller senare.
* Lade till stöd för att använda SQL Server 2016 som fjärrdatabas för Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Släppt: augusti 2016

**Fasta problem:**

* Ändringar av synkroniseringsintervallet sker inte förrän nästa synkroniseringscykel har slutförts.
* Azure AD Connect-guiden accepterar inte ett Azure AD-konto vars användarnamn börjar med ett understreck (\_).
* Azure AD Connect-guiden kan inte autentisera Azure AD-kontot om kontolösenordet innehåller för många specialtecken. Felmeddelande "Det gick inte att validera autentiseringsuppgifter. Ett oväntat fel har inträffat." returneras.
* Avinstallation av mellanlagringsserver inaktiverar lösenordssynkronisering i Azure AD-klienten och gör att lösenordssynkronisering misslyckas med aktiv server.
* Lösenordssynkronisering misslyckas i mindre vanliga fall när det inte finns något lösenordsh hash lagrat på användaren.
* När Azure AD Connect-servern är aktiverad för mellanlagringsläge inaktiveras inte återställning av lösenord tillfälligt.
* Azure AD Connect-guiden visar inte den faktiska lösenordssynkroniserings- och lösenordsåterskrivningskonfigurationen när servern är i mellanlagringsläge. Det visar dem alltid som funktionshindrade.
* Konfigurationsändringar av lösenordssynkronisering och tillbakaskrivning av lösenord sparas inte av Azure AD Connect-guiden när servern är i mellanlagringsläge.

**Förbättringar:**

* Uppdaterade cmdleten Start-ADSyncSyncCycle för att ange om den kan starta en ny synkroniseringscykel eller inte.
* Lade till cmdleten Stop-ADSyncSyncCycle för att avsluta synkroniseringscykeln och åtgärden, som för närvarande pågår.
* Uppdaterade cmdleten Stop-ADSyncScheduler för att avsluta synkroniseringscykeln och åtgärden, som för närvarande pågår.
* När du konfigurerar [katalogtillägg](how-to-connect-sync-feature-directory-extensions.md) i Azure AD Connect-guiden kan Azure AD-attributet av typen "Teletex string" nu väljas.

## <a name="111890"></a>1.1.189.0
Släppt: juni 2016

**Fasta problem och förbättringar:**

* Azure AD Connect kan nu installeras på en FIPS-kompatibel server.
  * För lösenordssynkronisering finns i [Synkronisering av lösenord hash och FIPS](how-to-connect-password-hash-synchronization.md#password-hash-synchronization-and-fips).
* Åtgärdade ett problem där ett NetBIOS-namn inte kunde matchas till FQDN i Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Släppt: maj 2016

**Nya funktioner:**

* Varnar och hjälper dig att verifiera domäner om du inte gjorde det innan du körde Azure AD Connect.
* Lagt till stöd för [Microsoft Cloud Germany](reference-connect-instances.md#microsoft-cloud-germany).
* Lade till stöd för den senaste molninfrastrukturen för [Microsoft Azure Government](reference-connect-instances.md#microsoft-azure-government) med nya URL-krav.

**Fasta problem och förbättringar:**

* Lade till filtrering i Redigeraren för synkroniseringsregler för att göra det enkelt att hitta synkroniseringsregler.
* Förbättrad prestanda när du tar bort ett anslutningsutrymme.
* Åtgärdade ett problem när samma objekt både togs bort och lades till i samma körning (kallas ta bort/lägg till).
* En inaktiverad synkroniseringsregel aktiverar inte längre inkluderade objekt och attribut vid uppdatering av uppgraderings- eller katalogschema.

## <a name="111300"></a>1.1.130.0
Släppt: april 2016

**Nya funktioner:**

* Lagt till stöd för attribut med flera värden [i katalogtillägg](how-to-connect-sync-feature-directory-extensions.md).
* Lade till stöd för fler konfigurationsvariationer för [automatisk uppgradering](how-to-connect-install-automatic-upgrade.md) som ska anses vara berättigade till uppgradering.
* Lade till några cmdlets för [anpassad schemaläggare](how-to-connect-sync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Släppt: mars 2016

**Fasta problem:**

* Kontrollera att Express-installationen inte kan användas på Windows Server 2008 (pre-R2) eftersom lösenordssynkronisering inte stöds på det här operativsystemet.
* Uppgraderingen från DirSync med en anpassad filterkonfiguration fungerade inte som förväntat.
* Vid uppgradering till en nyare version och det inte finns några ändringar i konfigurationen bör en fullständig import/synkronisering inte schemaläggas.

## <a name="111100"></a>1.1.110.0
Släppt: februari 2016

**Fasta problem:**

* Uppgradering från tidigare versioner fungerar inte om installationen inte finns i standardmappen C:\Program Files.
* Om du installerar och **avmarkerar Starta synkroniseringsprocessen** i slutet av installationsguiden aktiveras inte schemaläggaren om du kör installationsguiden en andra gång.
* Schemaläggaren fungerar inte som förväntat på servrar där usa-en-datum-/tidsformatet inte används. Det kommer `Get-ADSyncScheduler` också att blockera för att returnera rätt tider.
* Om du har installerat en tidigare version av Azure AD Connect med AD FS som inloggningsalternativ och uppgradering kan du inte köra installationsguiden igen.

## <a name="111050"></a>1.1.105.0
Släppt: februari 2016

**Nya funktioner:**

* [Automatisk uppgraderingsfunktion](how-to-connect-install-automatic-upgrade.md) för Express-inställningar kunder.
* Stöd för den globala administratören med hjälp av Azure Multi-Factor Authentication och Privileged Identity Management i installationsguiden.
  * Du måste tillåta att proxyn https://secure.aadcdn.microsoftonline-p.com även tillåter trafik till om du använder multifaktorautentisering.
  * Du måste https://secure.aadcdn.microsoftonline-p.com lägga till listan över betrodda platser för multifaktorautentisering för att fungera korrekt.
* Tillåt att ändra användarens inloggningsmetod efter den första installationen.
* Tillåt [filtrering av domän- och organisationsenhetsans](how-to-connect-install-custom.md#domain-and-ou-filtering) organisation i installationsguiden. Detta gör det också möjligt att ansluta till skogar där inte alla domäner är tillgängliga.
* [Scheduler](how-to-connect-sync-feature-scheduler.md) är inbyggd i synkroniseringsmotorn.

**Funktioner som marknadsförs från förhandsgranskning till GA:**

* [Tillbakaskrivning av enheten](how-to-connect-device-writeback.md).
* [Katalogtillägg](how-to-connect-sync-feature-directory-extensions.md).

**Nya förhandsgranskningsfunktioner:**

* Det nya standardintervallet för synkroniseringscykel är 30 minuter. Brukade vara tre timmar för alla tidigare utgåvor. Lägger till stöd för att ändra [schemaschemats](how-to-connect-sync-feature-scheduler.md) beteende.

**Fasta problem:**

* Sidan Verifiera DNS-domäner kände inte alltid igen domänerna.
* Frågar efter autentiseringsuppgifter för domänadministratörer när AD FS konfigureras.
* De lokala AD-kontona känns inte igen av installationsguiden om de finns i en domän med ett annat DNS-träd än rotdomänen.

## <a name="1091310"></a>1.0.9131.0
Släppt: december 2015

**Fasta problem:**

* Lösenordssynkronisering kanske inte fungerar när du ändrar lösenord i Active Directory Domain Services (AD DS), men fungerar när du anger ett lösenord.
* När du har en proxyserver kan autentiseringen till Azure AD misslyckas under installationen eller om en uppgradering avbryts på konfigurationssidan.
* Det går inte att uppdatera från en tidigare version av Azure AD Connect med en fullständig SQL Server-instans om du inte är EN SA (SQL Server systemadministratör).
* När du uppdaterar från en tidigare version av Azure AD Connect med en fjärr-SQL Server visas felet "Det går inte att komma åt ADSync SQL-databasen".

## <a name="1091250"></a>1.0.9125.0
Släppt: November 2015

**Nya funktioner:**

* Kan konfigurera om AD FS till Azure AD-förtroende.
* Kan uppdatera Active Directory-schemat och återskapa synkroniseringsregler.
* Kan inaktivera en synkroniseringsregel.
* Kan definiera "AuthoritativeNull" som en ny litteral i en synkroniseringsregel.

**Nya förhandsgranskningsfunktioner:**

* [Azure AD Connect-hälsotillstånd för synkronisering](how-to-connect-health-sync.md).
* Stöd för lösenordsynkronisering av [Azure AD Domain Services.](../user-help/active-directory-passwords-update-your-own-password.md)

**Nytt scenario som stöds:**

* Stöder flera lokala Exchange-organisationer. Mer information finns i [Hybriddistributioner med flera Active Directory-skogar](https://docs.microsoft.com/previous-versions/exchange-server/exchange-150/jj873754(v=exchg.150)).

**Fasta problem:**

* Problem med lösenordssynkronisering:
  * Ett objekt som flyttas från scope till in-scope kommer inte att ha sitt lösenord synkroniserat. Detta inkluderar både organisations- och attributfiltrering.
  * Att välja en ny organisationsenhet som ska inkluderas synkroniserad kräver inte en fullständig lösenordssynkronisering.
  * När en inaktiverad användare är aktiverad synkroniseras inte lösenordet.
  * Kö för återförsök är oändlig och den tidigare gränsen på 5 000 objekt som ska dras tillbaka har tagits bort.
* Det går inte att ansluta till Active Directory med Windows Server 2016 skogsfunktionell nivå.
* Det går inte att ändra gruppen som används för gruppfiltrering efter den första installationen.
* Inte längre skapar en ny användarprofil på Azure AD Connect-servern för varje användare som gör en lösenordsändring med tillbakaskrivning av lösenord aktiverat.
* Det går inte att använda långa heltalsvärden i synkroniseringsregler.
* Kryssrutan "Enhetsreskrivning" förblir inaktiverad om det inte går att nå domänkontrollanter.

## <a name="1086670"></a>1.0.8667.0
Släppt: augusti 2015

**Nya funktioner:**

* Installationsguiden för Azure AD Connect är nu lokaliserad till alla Windows Server-språk.
* Lade till stöd för kontoupplåsning när du använder Azure AD-lösenordshantering.

**Fasta problem:**

* Installationsguiden för Azure AD Connect kraschar om en annan användare fortsätter installationen i stället för den person som först startade installationen.
* Om en tidigare avinstallation av Azure AD Connect inte avinstallerar Azure AD Connect-synkroniseringen på ett rent sätt, är det inte möjligt att installera om.
* Det går inte att installera Azure AD Connect med Express-installationen om användaren inte finns i skogens rotdomän eller om en icke-engelsk version av Active Directory används.
* Om FQDN för Active Directory-användarkontot inte kan lösas visas ett vilseledande felmeddelande "Det gick inte att genomföra schemat".
* Om kontot som används i Active Directory Connector ändras utanför guiden misslyckas guiden vid efterföljande körningar.
* Azure AD Connect misslyckas ibland med att installera på en domänkontrollant.
* Det går inte att aktivera och inaktivera "Mellanlagringsläge" om tilläggsattribut har lagts till.
* Återställning av lösenord misslyckas i vissa konfigurationer på grund av ett felaktigt lösenord i Active Directory Connector.
* DirSync kan inte uppgraderas om ett unikt namn (DN) används i attributfiltrering.
* Överdriven CPU-användning när du använder återställning av lösenord.

**Funktioner för borttagna förhandsgranskningar:**

* [Förhandsgranskningsfunktionen Användaråterskrivning](how-to-connect-preview.md#user-writeback) togs tillfälligt bort baserat på feedback från våra förhandsversionskunder. Det kommer att läggas till igen senare efter att vi har tagit upp den lämnade feedback.

## <a name="1086410"></a>1.0.8641.0
Släppt: juni 2015

**Första versionen av Azure AD Connect.**

Ändrat namn från Azure AD Sync till Azure AD Connect.

**Nya funktioner:**

* [Installation av expressinställningar](how-to-connect-install-express.md)
* Kan [konfigurera AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)
* Kan [uppgradera från DirSync](how-to-dirsync-upgrade-get-started.md)
* [Förhindra oavsiktliga borttagningar](how-to-connect-sync-feature-prevent-accidental-deletes.md)
* Infört [mellanlagringsläge](how-to-connect-sync-staging-server.md)

**Nya förhandsgranskningsfunktioner:**

* [Tillbakaskrivning av användare](how-to-connect-preview.md#user-writeback)
* [Tillbakaskrivning av grupp](how-to-connect-preview.md#group-writeback)
* [Tillbakaskrivning av enheter](how-to-connect-device-writeback.md)
* [Katalogtillägg](how-to-connect-preview.md)

## <a name="104940501"></a>1.0.494.0501
Släppt: maj 2015

**Nytt krav:**

* Azure AD Sync kräver nu att .NET Framework version 4.5.1 installeras.

**Fasta problem:**

* Återställning av lösenord från Azure AD misslyckas med ett anslutningsfel för Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Släppt: april 2015

**Fasta problem och förbättringar:**

* Active Directory Connector bearbetas inte bort korrekt om papperskorgen är aktiverad och det finns flera domäner i skogen.
* Prestanda för importåtgärder har förbättrats för Azure Active Directory Connector.
* När en grupp har överskridit medlemsgränsen (som standard anges gränsen till 50 000 objekt) togs gruppen bort i Azure Active Directory. Med det nya beteendet tas inte gruppen bort, ett fel genereras och nya medlemskapsändringar exporteras inte.
* Det går inte att etablera ett nytt objekt om en stegvis borttagning med samma DN redan finns i kopplingsutrymmet.
* Vissa objekt är markerade för synkronisering under en deltasynkronisering även om det inte finns någon ändring mellanlagd på objektet.
* Om du tvingar fram en lösenordssynkronisering tas också den önskade DC-listan bort.
* CSExportAnalyzer har problem med vissa objekttillstånd.

**Nya funktioner:**

* En koppling kan nu ansluta till "ANY"-objekttyp i MV.

## <a name="104850222"></a>1.0.485.0222
Släppt: februari 2015

**Förbättringar:**

* Förbättrad importprestanda.

**Fasta problem:**

* Password Sync hedrar det cloudFiltered-attribut som används av attributfiltrering. Filtrerade objekt omfattas inte längre av lösenordssynkronisering.
* I sällsynta situationer där topologin hade många domänkontrollanter fungerar inte lösenordssynkronisering.
* "Stoppad server" när du importerar från Azure AD Connector efter att enhetshantering har aktiverats i Azure AD/Intune.
* Om du ansluter till FSP (Foreign Security Principals) från flera domäner i samma skog uppstår ett tvetydigt kopplingsfel.

## <a name="104751202"></a>1.0.475.1202
Släppt: december 2014

**Nya funktioner:**

* Lösenordssynkronisering med attributbaserad filtrering stöds nu. Mer information finns i [Lösenordssynkronisering med filtrering](how-to-connect-sync-configure-filtering.md).
* Attributet ms-DS-ExternalDirectoryObjectID skrivs tillbaka till Active Directory. Den här funktionen lägger till stöd för Office 365-program. Den använder OAuth2 för att komma åt online och lokala postlådor i en Hybrid Exchange-distribution.

**Problem med fasta uppgraderingar:**

* En nyare version av inloggningsassistenten finns på servern.
* En anpassad installationssökväg användes för att installera Azure AD Sync.
* Ett ogiltigt anpassat kopplingsvillkor blockerar uppgraderingen.

**Andra korrigeringar:**

* Fixade mallarna för Office Pro Plus.
* Fasta installationsproblem som orsakas av användarnamn som börjar med ett streck.
* Fast förlora sourceAnchor inställningen när du kör installationsguiden en andra gång.
* Fast ETW-spårning för lösenordssynkronisering.

## <a name="104701023"></a>1.0.470.1023
Släppt: oktober 2014

**Nya funktioner:**

* Lösenordssynkronisering från flera lokala Active Directory till Azure AD.
* Lokaliserat installationsgränssnitt till alla Windows Server-språk.

**Uppgradering från AADSync 1,0 GA**

Om du redan har Azure AD Sync installerat finns det ytterligare ett steg du måste ta om du har ändrat någon av de färdiga synkroniseringsreglerna. När du har uppgraderat till 1.0.470.1023-versionen dupliceras synkroniseringsreglerna som du har ändrat. Gör följande för varje ändrad synkroniseringsregel:

1. Leta reda på synkroniseringsregeln som du har ändrat och ta del av ändringarna.
1. Ta bort synkroniseringsregeln.
1. Leta reda på den nya synkroniseringsregeln som skapas av Azure AD Sync och sedan tillämpa ändringarna igen.

**Behörigheter för Active Directory-kontot**

Active Directory-kontot måste beviljas ytterligare behörigheter för att kunna läsa lösenordshã¤ringar från Active Directory. Behörigheterna att bevilja heter "Replikera katalogändringar" och "Replikera katalogändringar alla". Båda behörigheterna krävs för att kunna läsa lösenordsharen.

## <a name="104190911"></a>1.0.419.0911
Släppt: September 2014

**Första versionen av Azure AD Sync.**

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
