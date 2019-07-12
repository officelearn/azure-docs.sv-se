---
title: Insamling av identifiering för virtuella datorer och servrar i Azure Security Center | Microsoft Docs
description: Det här avsnittet innehåller de virtuella datorn och server aviseringarna tillgängliga i Azure Security Center.
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
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571587"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Hotidentifiering för virtuella datorer och servrar i Azure Security Center

Det här avsnittet innehåller de olika typerna av identifieringsmetoder och aviseringar som är tillgängliga för virtuella datorer och servrar med följande operativsystem. En lista över versioner som stöds finns i [plattformar och funktioner som stöds av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Security Center integrerar med Azure-tjänster att övervaka och skydda dina Windows-baserade datorer.  Security Center visar aviseringar och åtgärdsförslag från alla dessa tjänster i ett format för enkel att använda.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure Security Center utökar sin Molnplattformar arbetsbelastning skydd genom att integrera med Windows Defender Advanced Threat Protection (ATP). Detta ger omfattande funktioner för slutpunktsidentifiering och svar (EDR).

> [!NOTE]
> Windows Server Defender ATP-sensorn aktiveras automatiskt på Windows-servrar som publiceras till Azure Security Center.

När Windows Defender ATP för Server identifierar ett hot, utlöser en avisering. Aviseringen visas på instrumentpanelen i Security Center. Från instrumentpanelen kan du växla över till Windows Defender ATP-konsolen för att utföra en detaljerade undersökningar för att upptäcka omfattningen av angrepp. Mer information om Windows Defender ATP för Server finns i [publicera servrar till tjänsten Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analys av kraschdumpfiler <a nanme="windows-dump"></a>

När programvara kraschar fångar en kraschdumpfil en del av minnet vid tidpunkten för kraschen.

En krasch kan ha orsakats av skadlig kod eller innehåller skadlig kod. För att undvika att identifieras av säkerhetsprodukter kan olika former av skadlig kod för att använda en fileless-attack, vilket innebär skrivning till disk eller kryptering programvarukomponenter som skrivs till disk. Den här typen av angrepp är svårt att identifiera med traditionella diskbaserade metoder.

Den här typen av angrepp kan dock identifieras med minnesanalyser. Genom att analysera minnet i kraschdumpen kan identifiera Security Center tekniker som angreppet använder du utnyttjar sårbarheter i programvara, kommer åt känsliga data och gömda på en komprometterad dator. Detta görs genom Security Centers serverdel med minsta möjliga prestandapåverkan på värdar.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Identifierad kodinmatning**|Kodinmatning är inmatningen av körbara moduler i processer eller trådar som körs. Den här tekniken används av skadlig kod för åtkomst till data, medan har döljer sig själv för att förhindra som hittats och tagits bort. <br/>Den här varningen anger att en inmatad modul finns i kraschdumpen. För att skilja mellan skadlig och icke-skadlig inmatning av moduler kontrollerar Security Center om den inmatade modulen överensstämmer med en profil för misstänkt beteende.|
|**Misstänkt kodsegment som identifierats**|Anger att ett kodsegment har tilldelats med metoder som inte är standard, till exempel reflekterande inmatning och processurholkning. Aviseringen ger ytterligare egenskaper i kodsegmentet som har bearbetats för att ge ett sammanhang för funktioner och beteenden för det rapporterade kodsegmentet.|
|**Shellcode-identifiering**|Shellcode är nyttolasten som körs när skadlig kod har utnyttjat en sårbarhet i ett program.<br/>Den här varningen anger att en analys av kraschdumpfiler har identifierat körbar kod som uppvisar beteenden som vanligen utförs av skadliga nyttolaster. Även om icke-skadlig programvara kan också utföra det här beteendet, är det inte typiskt i normala programutvecklingsrutiner.|

### Identifiering av fileless Attack <a nanme="windows-fileless"></a>

I Azure ser vi regelbundet fileless hot mot våra kunders slutpunkter.

Mata in skadliga nyttolaster i minnet för att undvika identifiering fileless attacker. Angripare nyttolaster kvarstår i minnet för komprometterade processer och utföra en mängd skadlig aktivitet.

Med identifiering av fileless attack identifiera automatiserade minne kriminaltekniska tekniker fileless attack verktyg, teknik och beteenden. Den här lösningen med jämna mellanrum söker igenom din dator vid körning och extraherar insikter direkt från minnet för kritiska processer.

Den hittar bevis på utnyttjande kodinmatning och körning av skadliga nyttolaster. Identifiering av fileless Attack genererar detaljerad säkerhetsaviseringar för att påskynda avisering prioritering, Korrelations- och underordnade svarstid. Den här metoden kompletterar händelsebaserad EDR lösningar ger större identifieringsomfattningen.

> [!NOTE]
> Du kan simulera Windows aviseringar genom att ladda ner [Azure Security Center Spelbok](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Säkerhetsaviseringar och följ de tillhandahållna riktlinjerna

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Fileless attack tekniken har identifierats**|Minnet för processen som definieras nedan innehåller ett fileless attack-verktyg: Meterpreter. Fileless attack verktyg vanligtvis har inte en närvaro i filsystemet, vilket gör identifiering av traditionella antivirusprogram svårt.|

### <a name="further-reading"></a>Ytterligare läsning

Exempel och mer information om identifiering av Security Center:

* [Hur Azure Security Center automatiserar identifieringen av cyberattack](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Hur Azure Security Center identifierar säkerhetsproblem med administrativa verktyg](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Security Center samlar in granskningsposter från Linux-datorer med hjälp av **auditd**, en av de vanligaste Linux granskning ramverk. auditd har fördelen med att ha varit tillgängliga för lång tid och att leva i likriktade kerneln. 

### Linux auditd aviseringar och integrering av Microsoft Monitoring Agent (MMA) <a name="linux-auditd"></a>

Auditd-systemet består av ett undersystem för kernel-nivå som ansvarar för att övervaka systemanrop, filtrera dem efter en viss regeluppsättning och att meddelanden skrivs de till en socket. Security Center integrerar funktioner från auditd-paketet i Microsoft Monitoring Agent (MMA). Den här integrationen kan auditd händelser samling i alla distributioner som stöds Linux utan att alla nödvändiga komponenter.  

auditd-poster som samlas in, utökad och aggregeras till händelser med hjälp av Linux MMA-agenten. Security Center arbetar ständigt med att lägga till nya analytics, som utnyttjar Linux signalerar att upptäcka skadligt beteende i molnet och lokala Linux-datorer. Liknar Windows-funktioner, analysresultaten omfatta flera misstänkta processer, misstänkta inloggningsförsök, kernel-modul inläsning och andra aktiviteter, vilket tyda på en dator är utsatt för en attack eller har skett.  

Nedan visas flera exempel på analys, som visar hur vi omfatta flera olika faser av attack livscykel.

> [!div class="mx-tableFixed"]

|Varning|Beskrivning|
|---|---|
|**Processen visas åtkomst till den auktoriserade nyckelfilen för SSH på ett annorlunda sätt**|En auktoriserad nyckelfil i SSH har använts under en metod som liknar känd skadlig kod kampanjer. Den här åtkomsten kan tyda på att en angripare försöker få permanent åtkomst till en dator|
|**Identifierade Persistence försök**|Värd-dataanalys har identifierat att ett startskript för enanvändarläge har installerats. <br/>Eftersom det är ovanligt att en giltig process krävs för att köra i detta läge, kan detta tyda på att en angripare har lagt till en skadlig process i alla kör nivåer att garantera persistence.|
|**Bearbetning av schemalagda aktiviteter som har identifierats**|Värd-dataanalys har identifierat möjliga manipulation av schemalagda aktiviteter. Angripare ofta lägger till schemalagda aktiviteter i datorer som de har komprometterats för att få persistence.|
|**Misstänkt fil tidsstämpel ändring**|Värd-dataanalys upptäckte en misstänkt tidsstämpel ändring. Angripare kopiera ofta tidsstämplar från befintliga legitima filer till nya verktyg för att undvika identifiering av filerna nyligen släppta|
|**En ny användare har lagts till i gruppen sudoers**|Värd-dataanalys upptäckte att en användare har lagts till gruppen sudoers, vilket gör att medlemmarna kan köra kommandon med höga privilegier.|
|**Förmodligen utnyttja för DynoRoot i dhcp-klient**|Värd-dataanalys identifierat körning av en ovanlig kommando med överordnade processen av dhclient skript.|
|**Identifierad misstänkt kernel-modul**|Värd-dataanalys upptäckte en objektfil för delade som läses in som en kernel-modul. Detta kan vara legitima aktivitet eller en indikation på att en av dina datorer har komprometterats.|
|**Processen som är associerade med digitala valuta utvinning har identifierats**|Värd-dataanalys upptäckte körningen av en process som normalt förknippas med digitala valuta datautvinning|
|**Potentiella portvidarebefordran till extern IP-adress**|Värd-dataanalys identifierat inleds port vidarebefordran till en extern IP-adress.|

> [!NOTE]
> Du kan simulera Windows aviseringar genom att ladda ned [Azure Security Center-Strategibok: Säkerhetsaviseringar](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) och följa angivna riktlinjerna.


Mer information finns i dessa artiklar:  

* [Utnyttja Azure Security Center att upptäcka när komprometterade Linux datorer attack](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center kan identifiera nya säkerhetsrisker i Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 