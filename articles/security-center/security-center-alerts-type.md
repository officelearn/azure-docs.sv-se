---
title: Säkerhetsaviseringar per typ i Azure Security Center | Microsoft Docs
description: I den här artikeln beskrivs de olika typerna av säkerhetsaviseringar som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 24c6487ee7ec7d8398f933e29ca51cc9e390f47f
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633273"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Förstå säkerhetsaviseringar i Azure Security Center
Den här artikeln visar de olika typerna av säkerhetsaviseringar och meddelanden som är tillgängliga i Azure Security Center. Mer information om att hantera aviseringar och händelser finns i [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md).

För avancerad identifiering rekommenderar vi att du uppgraderar till Azure Security Center Standard. En kostnadsfri 60-dagars utvärderingsversion är tillgänglig. Om du vill uppgradera väljer du **Prisnivå** i avsnittet om [säkerhetsprinciper](security-center-azure-policy.md). Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Security Center har för den begränsade förhandsversionen publicerat en ny uppsättning identifieringar som utnyttjar auditd-poster, ett gemensamt granskningsramverk för att upptäcka skadligt beteende på Linux-datorer. Skicka ett e-postmeddelande med dina prenumerations-id:n till [oss](mailto:ASC_linuxdetections@microsoft.com) för att ta del av förhandsversionen.

## <a name="what-type-of-alerts-are-available"></a>Vilka typer av aviseringar finns?
Azure Security Center använder olika [identifieringsfunktioner](security-center-detection-capabilities.md) för att uppmärksamma kunder om eventuella hot mot deras miljöer. Dessa aviseringar innehåller värdefull information om vad som utlöste aviseringen, vilka resurser som berörs och attackens källa. Informationen som ingår i en avisering varierar beroende på vilken typ av analys som används för att identifiera hot. Händelser kan också innehålla ytterligare sammanhangsinformation som kan vara användbart när du utreder ett hot.  Den här artikeln innehåller information om följande aviseringstyper:

* Virtual Machine Behavioral Analysis (VMBA)
* Nätverksanalys
* SQL Database- och SQL Data Warehouse-analys
* Sammanhangsbaserad information

## <a name="virtual-machine-behavioral-analysis"></a>Beteendeanalys av virtuell dator
Azure Security Center kan använda beteendeanalyser för att identifiera resurser som har komprometterats, baserat på analysen av Event Logs i virtuella datorer. Till exempel processgenereringshändelser och inloggningshändelser. Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker.

> [!NOTE]
> Mer information om hur identifieringsfunktionerna i Security Center fungerar finns i [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Händelseanalys
Security Center använder analyser för att identifiera resurser som har komprometterats, baserat på analysen av händelseloggar i virtuella datorer. Till exempel processgenereringshändelser och inloggningshändelser. Dessutom korreleras data med andra tecken för att hitta bevis på utbredda attacker.

* **Körning av misstänkt process identifierad**: Angripare försöker oftast att köra skadlig kod utan identifiering genom att låtsas vara ofarliga processer. De här aviseringarna indikerar att körningen av en processen matchar ett av följande mönster:
    * En process som är känd för att användas för skadliga syften kördes. Medan individuella kommandon kan verka ofarliga, beräknas varningen baserat på en sammanställning av dessa kommandon.
    * En process utfördes från en ovanligt plats.
    * En process utfördes från en plats som är vanlig för kända misstänkta filer.
    * En process utfördes från en misstänkt sökväg.
    * En process utfördes i en onormal kontext.
    * En process utfördes på ett ovanligt konto
    * En process med ett misstänkt filnamnstillägg kördes.
    * En process med ett misstänkt dubbelt filnamnstillägg kördes.
    * En process med ett misstänkt höger-till-vänster (RLO)-tecken i filnamnet kördes.
    * En process vars namn är processen liknar men skiljer sig från en ofta körs utfördes
    * En process vars namn motsvarar ett känt angriparverktyg utfördes.
    * En process med ett slumpmässigt namn kördes.
    * En process med ett misstänkt filnamnstillägg kördes.
    * En dold fil kördes.
    * En process kördes som en underordnad till en annan orelaterad process.
    * En ovanlig process har skapats av en systemrutin.
    * En avvikande process startades av tjänsten Windows Update.
    * En process utfördes med en ovanlig kommandorad. Detta har associerats med legitima processer som uppsnappas för körning av skadlig kod.
    * Ett försök att starta alla körbara filer (*.exe) i en katalog har utförts från kommandoraden.
    * En process utfördes av PsExec-verktyg som kan användas för att köra processer via fjärranslutning.
    * Överordnad körbar fil Apache Tomcat® (Tomcat#.exe) användes för att starta misstänkta underordnade processer som kan vara värdar för eller starta skadliga kommandon.
    * Microsoft Windows ”assistenten för programkompatibilitet” (pcalua.exe) användes för att starta körbar kod som kan vara skadlig.
    * Ett misstänkt processavslut upptäcktes.
    * Systemprocessen SVCHOST utfördes i en onormal kontext.
    * Systemprocessen SVCHOST utfördes i en ovanlig servicegrupp.
    * En misstänkt kommandorad kördes.
    * Ett PowerShell-skript har gemensamma drag med kända misstänkta skript.
    * En känd skadlig PowerShell Powersploit-cmdlet har körts.
    * En inbyggd SQL-användare utförde en process som den normalt inte utför
    * En Base64-kodad körbar fil upptäcktes, som kan indikera att en angripare försöker undvika identifiering genom att skapa en körbar fil direkt via en sekvens av kommandon.

* **Misstänkt aktivitet för RDP-resurs**: Angripare siktar ofta in öppna hanteringsportar som RDP med råstyrkeattack. Dessa aviseringar tyder på misstänkt fjärrskrivbordsinloggning som anger:
    * Fjärrskrivbordsinloggningar har försökts.
    * Fjärrskrivbordsinloggningar försöktes med ogiltiga konton.
    * Fjärrskrivbordsinloggningar försöktes, av vilka vissa har kunna logga in på datorn.
* **Misstänkt aktivitet för SSH-resurs**: Angripare siktar ofta in öppna hanteringsportar som SSH med råstyrkeattack. Dessa aviseringar tyder på misstänkt SSH-inloggning som anger:
    * Misslyckade SSH-inloggningar har försökts.
    * SSH-inloggningar försöktes, av vilka vissa har genomförts.
* **Misstänkt WindowPosition-registervärde**: Den här varningen anger att en ändring av WindowPosition-registerkonfiguration gjordes som kan tyda på dolda programfönster i icke-synliga avsnitt på skrivbordet.
* **Eventuella försök att kringgå AppLocker**: AppLocker kan användas för att begränsa de processer som kan köras på Windows, vilket begränsar exponeringen för skadliga program. Den här varningen anger ett möjligt försök att kringgå AppLocker-begränsningar med hjälp av betrodda körbara filer (tillåtna av AppLocker-principer) för att köra icke betrodd kod.
* **Misstänkta namngivna pipe-kommunikationer**: Den här varningen anger att data har skrivits till en lokal namngiven pipe från ett kommando för Windows-konsolen. Namngivna pipes är kända för att användas av angripare för att aktivera och kommunicera med en skadlig installation.
* **Avkodning av en körbar fil med hjälp av det inbyggda certutil.exe-verktyget**: Den här varningen indikerar att ett inbyggt administratörsverktyg, certutil.exe, användes för att avkoda en körbar fil. Angripare är kända för att missbruka funktioner hos legitima administratörsverktyg för att utföra skadliga åtgärder, till exempel med ett verktyg som certutil.exe, för att avkoda en skadlig körbar fil som sedan ska köras.
* **En händelselogg rensades**: Den här varningen anger en misstänkt rensning av händelselogg, som ofta används av angripare för att försöka sudda ut sina spår.
* **Inaktivera och ta bort IIS-loggfiler**: Den här varningen anger att IIS-loggen har inaktiverats och tagits bort, vilket ofta används av angripare för att försök sudda ut sina spår.
* **Misstänkt filborttagning**: Den här varningen anger misstänkt borttagning av filer som kan användas av en angripare för att ta bort bevis på skadliga binärfiler.
* **Skuggkopior av alla filer har tagits bort**: Den här varningen anger att skuggkopior har tagits bort.
* **Misstänkta kommandon för att rensa filer**: Den här varningen anger en kombination av systeminformationskommandon som används för att utföra självrensning efter att potentiella hot har utförts.  Medan *systeminfo.exe* är ett legitimt Windows-verktyg, är det ovanligt att det körs två gånger i följd, följt av ett borttagningskommando på så sätt som har inträffat här.
* **Skapande av misstänkt konto**: Den här varningen anger att ett konto har skapats som är snarlikt ett befintlig inbyggt konto med administratörsprivilegier. Den här tekniken kan användas av angripare för att skapa ett falskt konto utan att bli identifierade.
* **Misstänkt aktivitet för skuggkopia av volym**: Den här varningen anger borttagningsaktivitet av skuggkopia på resursen. Skuggkopia av volym (VSC) är en viktig artefakt som lagrar ögonblicksbilder av data. Den här aktiviteten är kopplad till Ransowmare, men det kan också vara legitim.
* **Persistence-metod för Windows-registret**: Den här varningen anger ett försök att spara en körbar fil i Windows-registret. Skadlig kod använder ofta en sådan metod för att överleva en omstart.
* **Misstänkt ny brandväggsregel**: Den här varningen anger att en ny brandväggsregel har lagts till via *netsh.exe* för att tillåta trafik från en körbar fil på en misstänkt plats.
* **Misstänkta XCOPY-körningar**: den här varningen anger ett antal XCOPY-körningar som skulle kunna signalera att en av dina datorer har komprometterats och användes för att sprida skadlig kod.
* **Undertryckning av juridisk information som visas för användare vid inloggning**: den här varningen anger en ändring i registernyckeln som styr om en juridiska information visas för användarna när de loggar in. Det här är en vanlig aktivitet som utförs av angripare efter att de har komprometterat en värd.
* **Avvikande blandning av versaler och gemener i kommandoraden identifierad**: Den här varningen anger användning av en blandning av versaler och gemener i kommandoraden, vilket är en teknik som används av angripare för att komma undan skiftlägeskänsliga eller hash-baserade regler för datorn.
* **Dold kommandorad**: Den här varningen anger att misstänkta indikatorer för döljande har upptäckts på kommandoraden.
* **Flera domänkonton efterfrågas**: Angripare efterfrågar ofta AD-domänkonton samtidigt som de rekognoserar användare, domänadministratörskonton, domänkontrollanter och förtroenderelationer mellan domäner. Den här varningen anger att ett ovanligt antal distinkta domänkonton efterfrågades inom en kort tidsperiod.
* **Möjlig lokala rekognoseringsaktivitet**: den här varningen anger att en kombination av systeminformationskommandon som är associerade med rekognoseringsaktivitet har körts.  Även om *systeminfo.exe* är ett giltigt Windows-verktyg är det ovanligt att det körs två gånger i följd.
* **Möjlig körningen av körbar keygen**: Den här varningen anger att en process vars namn är en indikation på att ett keygen-verktyg har körts. Dessa verktyg används vanligtvis för att motverka licensieringsmekanismer för programvaror men de laddas ofta ner tillsammans med andra skadliga program.
* **Misstänkt körning via rundll32.exe**: Den här varningen anger att rundll32.exe användes för att köra en process med ett ovanligt namn vilket överensstämmer med namngivningsschemat för processen som används av angripare för att installera en förstastegsinstallation på en komprometterad värd.
* **Misstänkt kombination av HTA och PowerShell**: Den här varningen anger att ett Microsoft Application-värd (HTA) håller på att starta PowerShell-kommandon. Det här är en teknik som används av angripare för att starta skadliga PowerShell-skript.
* **Ändring till en registernyckel som kan missbrukas för att kringgå UAC**: Den här varningen anger att en registernyckel som kan missbrukas för att kringgå UAC (User Account Control) har ändrats, vilket ofta används av angripare för att flytta från åtkomst utan privilegier (standardanvändare) till privilegierad åtkomst (till exempel administratör) på en komprometterad värd.
* **Användning av misstänkta domännamn i kommandoraden**: Den här varningen anger att ett misstänkt domännamn har använts, vilket kan vara tecken på att en angripare är värd för skadliga verktyg som slutpunkter för kommando och kontroll och exfiltrering av data.
* **Ett konto har skapats på flera värdar inom en 24-timmarsperiod**: Den här varningen anger att ett försök gjordes att skapa samma användarkonto på flera värdar vilket kan vara tecken på att en angripare flyttar sidled över nätverket efter att en eller flera nätverksentiteter har komprometterats.
* **Misstänkt användning av CACLS till lägre säkerhetsläget för systemet**: Den här varningen anger att CACLS-listan (change access control list) har ändrats. Den här metoden används ofta av angripare för att ge fullständig åtkomst till systemfiler som ftp.exe, net.exe, wscript.exe o.s.v.
* **Parametrar för misstänkt Kerberos Golden Ticket-attack**: Den här varningen anger att kommandoradsparametrar som är konsekventa med en Kerberos Golden Ticket-attack utfördes. En komprometterad krbtgt-nyckel kan användas av en angripare för att utge sig för att vara vilken användare de vill.
* **Aktivering av registernyckeln WDigest UseLogonCredential**: den här varningen anger att nyckeln har ändrats för att tillåta logga i autentiseringsuppgifter lagras i klartext i LSA-minnet, vilka sedan inhämtats från minnet.
* **Potentiellt misstänkt användning av Telegram-verktyget**: Den här varningen anger installation av Telegram, en kostnadsfri molnbaserad snabbmeddelandetjänst som används av angripare för att överföra skadliga binärfiler till en annan dator, telefon eller surfplatta.
* **Ny ASEP har skapats**: Den här varningen anger att en ny ASEP (Auto Start Extensibility Point) har skapats, vilket gör att processnamnet som identifieras i kommandoraden startas automatiskt och kan användas av en angripare för att uppnå beständighet.
* **Ändringar i misstänkt Set-ExecutionPolicy och WinRM**: Den här varningen anger konfigurationsändringar vilka associeras med användning av den skadliga ChinaChopper webshell.
* **Inaktivering av kritiska tjänster**: Den här varningen anger att kommandot ”net.exe stop” användes för att stoppa kritiska tjänster som SharedAccess eller Windows Security Center.
* **Misstänkt användning av -s-växel för FTP**: Den här varningen anger användning av ”-s”-växel för FTP, som kan användas av skadlig kod för att ansluta till en fjärransluten server och hämta ytterligare skadliga binärfiler.
* **Misstänkt körning av kommandot VBScript.Encode**: Den här varningen anger att kommandot *VBScript.Encode* utfördes, vilket kodar skript till oläsbar text, vilket gör det svårare för användare att undersöka koden.
* **Allokering av VBScript http-objekt**: Den här varningen anger att en VBScript-fil skapats med hjälp av kommandotolken, som kan användas för att hämta skadliga filer.
* **Attack mot tröga tangenter**: Den här varningen anger att en angripare kanske saboterar en binär åtkomst (till exempel tröga tangenter, skärmtangentbord, skärmläsaren) för att ge bakdörrsåtkomst.
* **Indikationer på Petya-utpressningstrojan**: Den här varningen anger att de tekniker som är associerade med Petya-utpressningstrojan har observerats.
* **Försök att inaktivera AMSI**: Den här varningen anger ett försök att inaktivera skanningsgränssnitt för program mot skadlig kod (AMSI), vilket skulle inaktivera identifiering av program mot skadlig kod.
* **Indikatorer på utpressningstrojan**: Den här varningen anger misstänkt aktivitet som traditionellt är associerade med en utpressningstrojan för låsskärm och kryptering.
* **Misstänkt insamling av spår för utdatafil**: Den här varningen anger att ett spår (till exempel av nätverksaktivitet) har samlas in och matats ut till en ovanlig filtyp.
* **Högrisksprogramvara**: Den här varningen anger användning av programvara som har associerats med installation av skadlig kod. Angripare paketerar ofta skadlig kod med annars ofarliga verktyg såsom visas i den här varningen och installerar tyst den skadliga koden i bakgrunden.
* **Misstänkt fil har skapas**: Den här varningen anger skapande eller körning av en process som används av angripare för att hämta ytterligare skadlig kod till en komprometterad värd när en bifogad fil i ett nätfiskedokument har öppnats.
* **Misstänkta autentiseringsuppgifter i kommandoraden**: Den här varningen anger ett misstänkt lösenord som används för att köra en fil. Den här tekniken har använts av angripare för att köra den skadliga koden Pirpi.
* **Möjliga körning av spridare av skadlig kod**: Den här varningen anger ett filnamn som har använts av angripare för att installera skadlig kod.
* **Misstänkt körning via rundll32.exe**: Den här varningen anger rundll32.exe som används för att köra en notepad.exe eller reg.exe, konsekvent med den processinmatningsteknik som används av angripare.
* **Misstänkta kommandoradsargument**: Den här varningen anger misstänkta kommandoradsargument som har använts tillsammans med ett omvänt gränssnitt som används av aktivitetsgruppen HYDROGEN.
* **Misstänkta autentiseringsuppgifter för dokument**: Den här varningen anger att ett misstänkt, vanligt förberäknat lösenords-hash som används av skadlig kod har använts för att köra en fil.
* **Konstruktion av dynamisk PS-skript**: Den här varningen anger att ett PowerShell-skript har skapats dynamiskt. Angripare använder den här metoden för att progressivt skapa ett skript för att kunna komma undan ID-system.
* **Metasploit indikatorer**: den här varningen anger aktivitet som associeras med Metasploit-ramverket, vilket ger en mängd angriparfunktioner och verktyg.
* **Misstänkt kontoaktivitet**: Den här varningen anger ett försök att ansluta till en dator med ett konto som nyligen har komprometterats.
* **Skapande av konto**: Den här varningen anger skapandet av ett nytt konto på datorn.


### <a name="crash-analysis"></a>Kraschanalys


Analysen där kraschen dumpar minnet är en metod som används för att identifiera avancerad skadlig kod som kan undvika traditionella säkerhetslösningar. Olika former av skadlig kod försöker undvika att identifieras av antivirusprodukter genom att aldrig skriva till disk, eller genom att kryptera programvarukomponenter som skrivs till disk. Den här metoden gör det svårt att identifiera den skadliga koden med traditionella antivirusprogram. Sådan skadlig kod kan dock identifieras med minnesanalyser eftersom den skadliga koden måste lämna spår i minnet för att kunna fungera.

När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen. Kraschen kan ha orsakats av skadlig kod, allmänna program- eller systemproblem. Genom att analysera minnet i kraschdumpen kan Azure Security Center identifiera tekniker som utnyttjar sårbarheter i programvara, som kommer åt känsliga data och som ligger gömda på en komprometterad dator. Detta åstadkoms med minsta möjliga prestandapåverkan på de värdar där analysen utförs av Security Centers serverdel.

* **Kodinmatning upptäckt**: Kodinmatning är inmatningen av körbara moduler i processer eller trådar som körs. Den här tekniken används av skadlig kod för åtkomst till data, dölja eller förhindra borttagningen (till exempel persistence). Den här varningen anger att en inmatad modul finns i kraschdumpen. Legitima programutvecklare kan ibland utföra den här typen av kodinmatning för icke-skadliga ändamål, t.ex för att ändra eller utöka ett befintligt program eller en komponent i operativsystemet. För att skilja mellan skadlig och icke-skadlig inmatning av moduler kontrollerar Security Center om den inmatade modulen överensstämmer med en profil för misstänkt beteende. Resultatet av den här kontrollen anges i ”SIGNATURE”-fältet i varningen och avspeglas i varningens allvarlighetsgrad, varningsbeskrivningen och i stegen för att åtgärda varningen.
* **Misstänkt kodsegment**: Det misstänkta kodsegmentet varnar om att ett kodsegment har tilldelats med metoder som inte är standard, t.ex. sådana som används av reflekterande inmatning och processurholkning. Ytterligare egenskaper i kodsegmentet bearbetas för att ge ett sammanhang vad gäller funktioner och beteenden för det rapporterade kodsegmentet.
* **Shellcode upptäckt**: Shellcode är nyttolasten som körs när skadlig kod har utnyttjat en sårbarhet i ett program. Den här aviseringen anger att en analys av kraschdumpfiler har identifierat körbar kod som uppvisar beteenden som vanligen utförs av skadliga nyttolaster. Icke-skadlig programvara kan uppvisa detta beteende, men det är inte typiskt i normala programutvecklingsrutiner.
* **Modulkapning upptäckt**: Windows använder DLL:er (Dynamic Link Library) för att program ska kunna använda vanliga systemfunktioner i Windows. DLL-kapning inträffar när skadlig kod ändrar inläsningsordningen för DLL-filer för att läsa in skadliga nyttolaster i minnet, där godtycklig kod kan köras. Den här aviseringen anger att en analys av kraschdumpfiler har upptäckt en modul med ett liknande namn som har lästs in från två olika sökvägar. En av de inlästa sökvägarna kommer från en vanlig plats för binära Windows-systemfiler. Legitima programutvecklare ändrar ibland inläsningsordningen för DLL-filer av icke-skadliga skäl, till exempel för instrumentering eller för att utöka Windows OS- eller Windows-program. För att skilja mellan skadliga och potentiellt oskadliga ändringar av DLL-filernas inläsningsordning kontrollerar Security Center om en inläst modul överensstämmer med en misstänkt profil eller inte.
* **Maskering av Windows-modul upptäckt**: Skadlig kod kan använda vanliga namn på binära Windows-systemfiler (t.ex. SVCHOST.EXE) eller moduler (t.ex. NTDLL.DLL) för att smälta in och dölja den skadliga programvarans egentliga natur för administratören. Den här aviseringen anger att en analys av kraschdumpfiler innehåller moduler som använder namn på Windows-systemmoduler, men inte uppfyller andra kriterier som är typiska för Windows-moduler. En analys av en kopia av den maskerande modulen på disk kan ge mer information för att avgöra om modulen är skadlig eller inte.
* **Modifierad systembinärfil upptäckt**: Skadlig kod kan ändra grundläggande systembinärfiler för att komma åt data eller ligga kvar i ett komprometterat system utan att bli upptäckt. Den här aviseringen anger att en analys av kraschdumpfiler har upptäckt att grundläggande Windows OS-binärfiler har ändrats i minnet eller på disk. Legitima programutvecklare kan ibland ändra systemmoduler i minnet för icke-skadliga ändamål, till exempel Detours eller för programkompatibilitet. För att skilja mellan skadliga och potentiellt oskadliga moduler kontrollerar Security Center om modulen överensstämmer med en misstänkt profil eller inte.

## <a name="network-analysis"></a>Nätverksanalys
Hotidentifieringen i nätverk av Security Center sker genom automatisk insamling av säkerhetsinformation från din Azure IPFIX-trafik (Internet Protocol Flow Information Export). Tjänsten analyserar den här informationen, och korrelerar ofta information från flera källor för att identifiera hot.

* **Möjliga inkommande SQL-råstyrkeattacker**: Analysen av nätverkstrafik upptäckte misstänkt inkommande SQL-kommunikation. Den här aktiviteten är konsekvent med råstyrkeattacker mot SQL-servrar.
* **Misstänkt inkommande RDP-nätverksaktivitet från flera källor**: Analysen av nätverkstrafik identifierade avvikande inkommande Remote Desktop Protocol (RDP)-kommunikation från flera källor. Mer specifikt visar provade nätverksdata unika IP-adresser som ansluter till din dator, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din RDP-slutpunkt från flera värdar (Botnät).
* **Misstänkt inkommande RDP-nätverksaktivitet**: Analysen av nätverkstrafik identifierade avvikande inkommande Remote Desktop Protocol (RDP)-kommunikation. Mer specifikt visar provade nätverksdata ett högt antal inkommande anslutningar till din dator, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din RDP-slutpunkt.
* **Misstänkt utgående RDP-nätverksaktivitet till flera destinationer**: Analysen av nätverkstrafik identifierade avvikande utgående Remote Desktop Protocol (RDP)-kommunikation till flera destinationer. Den här aktiviteten kan indikera att din dator har drabbats och nu används nu för råstyrkeattacker mot externa RDP-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.
* **Misstänkt utgående RDP-nätverksaktivitet**: Analysen av nätverkstrafik identifierade avvikande utgående Remote Desktop Protocol (RDP)-kommunikation. Mer specifikt visar provade nätverksdata ett högt antal utgående anslutningar från din dator, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera att din dator har drabbats och nu används nu för råstyrkeattacker mot externa RDP-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.
* **Misstänkt inkommande SSH-nätverksaktivitet**: Analysen av nätverkstrafik identifierade avvikande inkommande SSH-kommunikation. Mer specifikt visar provade nätverksdata ett högt antal inkommande anslutningar till din dator, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din SSH-slutpunkt.
* **Misstänkt inkommande SSH-nätverksaktivitet från flera källor**: Analysen av nätverkstrafik identifierade avvikande inkommande SSH-kommunikation. Mer specifikt visar provade nätverksdata unika IP-adresser som ansluter till din dator, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera ett försök till råstyrkeattacker mot din SSH-slutpunkt från flera värdar (Botnät).
* **Misstänkt utgående SSH-nätverksaktivitet**: Analysen av nätverkstrafik identifierade avvikande utgående SSH-kommunikation. Mer specifikt visar provade nätverksdata ett högt antal utgående anslutningar från din dator, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera att din dator har drabbats och nu används nu för råstyrkeattacker mot externa SSH-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.
* **Misstänkt utgående SSH-nätverksaktivitet till flera destinationer**: Analysen av nätverkstrafik identifierade avvikande utgående SSH-kommunikation till flera destinationer. Mer specifikt visar provade nätverksdata att din dator ansluter till unika IP-adresser, vilket anses onormalt för den här miljön. Den här aktiviteten kan indikera att din dator har drabbats och nu används nu för råstyrkeattacker mot externa SSH-slutpunkter. Observera att den här typen av aktivitet skulle kunna göra att din IP-adress flaggas som skadlig av externa enheter.
* **Nätverkskommunikation med en obehörig dator upptäckte**: Analysen av nätverkstrafik anger att datorn har kommunicerat med vad som eventuellt är ett kommando- och kontrollcenter.
* **Möjlig komprometterad dator upptäcktes**: Analysen av nätverkstrafik upptäckte utgående aktivitet, vilket kan betyda att den fungerar som en del av ett botnät. Analysen baseras på IP-adresser som används av din resurs tillsammans med offentliga DNS-poster.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>SQL Database- och SQL Data Warehouse-analys

Resursanalysen i Security Center fokuserar på PaaS-tjänster (platform as a service), till exempel integrering med [Threat Detection for Azure SQL Database](../sql-database/sql-database-threat-detection.md) och Azure SQL Data Warehouse. SQL Threat Detection identifierar avvikande aktiviteter som visar onormala och potentiellt skadliga försök att komma åt eller utnyttja databaser, och utlöser följande aviseringar:

* **Sårbarhet mot SQL-inmatning**: Den här aviseringen utlöses när ett program genererar en felaktig SQL-instruktion i databasen. Det här kan tyda på en eventuell sårbarhet för SQL-inmatningsattacker. Det finns två möjliga orsaker till att en felaktig instruktion genereras:
    * Ett fel i programkoden konstruktioner den felaktiga SQL-instruktionen
    * Programkod eller lagrade procedurer rensar inte indata från användare när den felaktiga SQL-instruktionen skapas, och det här kan utnyttjas för SQL-inmatning
* **Potential SQL injection** (Potentiell SQL-inmatning): Den här aviseringen utlöses när en aktiv sårbarhet utnyttjas mot ett program som är sårbart för SQL-inmatning. Det innebär att angriparen försöker mata in skadliga SQL-instruktioner med hjälp av den sårbara programkoden eller lagrade procedurer.
* **Access from unusual location** (Åtkomst från ovanlig plats): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
* **Access from unusual Azure data center** (Åtkomst från ovanligt Azure-datacenter): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern från ett ovanligt Azure-datacenter som nyligen setts på den här servern. I vissa fall identifierar aviseringen en giltig åtgärd (som ditt nya program i Azure, Power BI eller Azure SQL Query Editor). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
* **Access from unfamiliar principal** (Åtkomst från ovanligt huvudkonto): Den här aviseringen utlöses när åtkomstmönstret till SQL-servern ändras, där någon har loggat in på SQL-servern med ett ovanligt huvudkonto (SQL-användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
* **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
* **Brute force SQL credentials** (Nyckelsökningsattack mot SQL-autentiseringsuppgifter): Den här aviseringen utlöses när det sker ett onormalt stort antal misslyckade inloggningar med olika autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="contextual-information"></a>Sammanhangsbaserad information
Vid en undersökning måste analytiker tillhandahålla information om sammanhanget så att en bedömning av risken och lämpliga åtgärder kan vidtas.  Till exempel, om en nätverksavvikelse upptäcks är det svårt att bestämma vilka åtgärder som ska vidtas utan att förstå vad som sker på nätverket som helhet eller vilken resurs som är målet för attacken. För att hjälpa med som omfatta en säkerhetsincident artefakter, relaterade händelser och information som kan hjälpa utredaren. Tillgängligheten för ytterligare information kan variera beroende på vilken typ av hot som har upptäckts och konfigurationen av din miljö. För vissa säkerhetsincidenter kan sådan information vara otillgänglig.

Om ytterligare information är tillgänglig visas den i säkerhetsincidenten under listan över aviseringar. Detta kan innehålla information som:

- Logga raderingshändelser
- PNP-enhet ansluten från okänd källa
- Aviseringar som inte är användbara
- Skapa nytt konto
- Filen avkodas med certutil-verktyget

![Varning om onormal åtkomst](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Nästa steg
I den här artikeln lär du dig om de olika typerna av säkerhetsaviseringar i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
