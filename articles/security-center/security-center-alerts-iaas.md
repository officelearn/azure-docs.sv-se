---
title: Hot identifiering för virtuella datorer & servrar i Azure Security Center | Microsoft Docs
description: I det här avsnittet presenteras de virtuella dator-och Server aviseringarna som finns i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: f23865fc0a1943a5157e4ff8eb8de10a71ef0883
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295794"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Hot identifiering för virtuella datorer & servrar i Azure Security Center

Det här avsnittet innehåller olika typer av identifierings metoder och aviseringar som är tillgängliga för virtuella datorer och servrar med följande operativ system. En lista över versioner som stöds finns i [plattformar och funktioner som stöds av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Aktivitets<a name="windows-machines"></a>

Security Center integreras med Azure-tjänster för att övervaka och skydda dina Windows-baserade datorer.  Security Center visar aviseringar och reparations förslag från alla dessa tjänster i ett lättanvänt format.

### Microsoft Server Defender ATP<a nanme="windows-atp"></a>

Azure Security Center utvidgar dess moln skydds plattform genom att integrera med Windows Defender Avancerat skydd (ATP). Detta ger omfattande EDR-funktioner (slut punkts identifiering och svar).

> [!NOTE]
> Windows Server Defender ATP-sensorn aktive ras automatiskt på Windows-servrar som har publicerats på Azure Security Center.

När Windows Server Defender ATP identifierar ett hot utlöses en avisering. Aviseringen visas på instrument panelen för Security Center. Från instrument panelen kan du pivotera till Windows Defender ATP-konsolen för att utföra en detaljerad undersökning för att få fram omfattningen av angreppet. Mer information om Windows Server Defender ATP finns i [onboard servers to Windows Defender ATP-tjänsten](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analys av krasch dumpning<a nanme="windows-dump"></a>

När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen.

En krasch kan ha orsakats av skadlig kod eller innehålla skadlig kod. För att undvika att de upptäcks av säkerhets produkter använder olika former av skadlig kod en fil lös attack, vilket förhindrar skrivning till disk eller kryptering av program varu komponenter som skrivs till disk. Den här typen av attack är svår att identifiera med hjälp av traditionella diskbaserade metoder.

Den här typen av attack kan dock identifieras med hjälp av minnes analys. Genom att analysera minnet i krasch dumpningen kan Security Center identifiera de tekniker som angreppet använder för att utnyttja sårbarheter i program varan, komma åt konfidentiella data och ligger gömda kvar på en komprometterad dator. Detta görs av Security Center Server del med minsta möjliga prestanda påverkan på värdar.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Kod inmatning identifierad**|Kodinmatning är inmatningen av körbara moduler i processer eller trådar som körs. Den här tekniken används av skadlig kod för att komma åt data, medan de har dolts för att förhindra att de hittas och tas bort. <br/>Den här varningen anger att en inmatad modul finns i kraschdumpen. Security Center kontrollerar om den inmatade modulen överensstämmer med en profil för misstänkt beteende för att skilja mellan skadliga och icke-skadliga inmatade moduler.|
|**Misstänkt kod segment identifierat**|Anger att ett kod segment har tilldelats med metoder som inte är standard, till exempel reflekterande injektion och process ihålig. Aviseringen ger ytterligare egenskaper för det kod segment som har bearbetats för att ge kontext för funktioner och beteenden för det rapporterade kod segmentet.|
|**Shellcode identifierad**|Shellcode är nyttolasten som körs när skadlig kod har utnyttjat en sårbarhet i ett program.<br/>Den här varningen anger att krasch dumpnings analys har identifierat körbar kod som innehåller beteenden som ofta utförs av skadliga nytto laster. Även om icke-skadliga program kan utföra det här beteendet, är det inte typiskt för vanliga program utvecklings metoder.|

### Avkänning av filbaserad attack<a nanme="windows-fileless"></a>

I Azure ser vi regelbundet Filspecifika attacker riktade mot våra kunders slut punkter.

För att undvika identifiering kan fil lösa angrepp injicera skadliga nytto laster i minnet. Angripares nytto laster finns kvar i minnet för komprometterade processer och utför en mängd olika skadliga aktiviteter.

Med fil lösa angrepp kan automatiserade minnes kriminal tekniska-tekniker identifiera fillösa verktyg för attacker, tekniker och beteenden. Den här lösningen genomsöker regelbundet datorn vid körning och extraherar insikter direkt från minnet för säkerhets kritiska processer.

Det hittar bevis för utnyttjande, kod inmatning och körning av skadliga nytto laster. Vid fil lös angrepp genereras detaljerade säkerhets aviseringar för att påskynda aviseringen prioritering, korrelation och underordnade svars tider. Den här metoden kompletterar event-baserade EDR-lösningar som ger större identifierings täckning.

> [!NOTE]
> Du kan simulera Windows-aviseringar genom att ladda ned [Azure Security Center Spelbok](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Säkerhets aviseringar och följer de rikt linjer som anges

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Filbaserad attack teknik upptäcktes**|Minnet för den angivna processen nedan innehåller en fil med en filbaserad attack-Toolkit: Meterpreter. Filbaserade angrepps verktyg har vanligt vis ingen närvaro på fil systemet, vilket gör det svårt att identifiera vanliga antivirus program.|

### <a name="further-reading"></a>Ytterligare läsning

Exempel och mer information om Security Center identifiering:

* [Hur Azure Security Center automatiserar identifieringen av cyberhot-attack](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Hur Azure Security Center identifierar sårbarheter med hjälp av administrations verktyg](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux<a name="linux-machines"></a>

Security Center samlar in gransknings poster från Linux-datorer med hjälp av **granskning**, ett av de vanligaste Linux-gransknings ramverken. granskad har fördelen att du har varit i en lång tid och lever i Mainline-kärnan. 

### Linux-granskade varningar och MMA-integrering (Microsoft Monitoring Agent)<a name="linux-auditd"></a>

Det granskade systemet består av ett under system i kernel-nivå, som ansvarar för att övervaka system anrop, filtrera dem med en angiven regel uppsättning och skriva meddelanden till en socket. Security Center integrerar funktioner från det granskade paketet i Microsoft Monitoring Agent (MMA). Den här integrationen aktiverar insamling av granskade händelser i alla Linux-distributioner som stöds utan krav.  

granskade poster samlas in, berikas och sammanställs i händelser med hjälp av Linux MMA-agenten. Security Center arbetar ständigt med att lägga till nya analyser, som utnyttjar Linux-signaler för att identifiera skadliga beteenden på moln-och lokala Linux-datorer. På liknande sätt som med Windows-funktioner, är dessa analys omfång över misstänkta processer, misstänkta inloggnings försök, inläsning av kernel-modul och andra aktiviteter, vilket indikerar att en dator är utsatt för en attack eller har brutits.  

Nedan visas flera exempel på analyser som visar hur vi sträcker sig över olika faser av attackens livs cykel.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Process som ser åtkomst till filen SSH-auktoriserade nycklar på ett ovanligt sätt**|En SSH-auktoriserad nyckel fil har öppnats i en metod som liknar kända kampanjer från skadlig kod. Den här åtkomsten kan tyda på att en angripare försöker få beständig åtkomst till en dator|
|**Identifierat beständigt försök**|Värd data analys har identifierat att ett start skript för enanvändarläge har installerats. <br/>Eftersom det är sällsynt att alla legitima processer måste köras i detta läge, kan detta tyda på att en angripare har lagt till en skadlig process på varje körnings nivå för att garantera beständighet.|
|**Manipulering av schemalagda aktiviteter har identifierats**|Värd data analys har upptäckt möjlig manipulering av schemalagda aktiviteter. Angripare lägger ofta till schemalagda aktiviteter på datorer som de har komprometterat för att få beständighet.|
|**Ändring av misstänkt fil tidsstämpel**|Värd data analysen identifierade en misstänkt tids stämplings ändring. Angripare kopierar ofta tidsstämplar från befintliga legitima filer till nya verktyg för att undvika att dessa nyligen tappade filer identifieras|
|**En ny användare har lagts till i sudoers-gruppen**|Värd data analys upptäckte att en användare lades till i gruppen sudoers, vilket gör att dess medlemmar kan köra kommandon med hög behörighet.|
|**Sannolik sårbarhet för DynoRoot-säkerhetsproblem i DHCP-klienten**|Värd data analys upptäckte körningen av ett ovanligt kommando med den överordnade processen för dhclient-skript.|
|**Misstänkt kernel-modul identifierad**|Värd data analys identifierade en delad objekt fil som läses in som en kernel-modul. Detta kan vara en legitim aktivitet eller en indikation på att någon av datorerna har komprometterats.|
|**Process som är associerad med den digitala valuta utvinning identifierad**|Värd data analys upptäckte körningen av en process som normalt är kopplad till digital valuta utvinning|
|**Potentiell port vidarebefordran till extern IP-adress**|Värd data analys identifierade initieringen av port vidarebefordran till en extern IP-adress.|

> [!NOTE]
> Du kan simulera Windows-aviseringar genom [att hämta Azure Security Center Spelbok: Säkerhets aviseringar](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) och följer de rikt linjer som anges.


Mer information finns i dessa artiklar:  

* [Utnyttja Azure Security Center för att upptäcka när komprometterade Linux-datorer angrips](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center kan identifiera nya sårbarheter i Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 