---
title: Om Säkerhetskopiering av virtuella Azure-datorer
description: Läs om säkerhetskopiering av Azure virtuella datorer och Observera några metodtips.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 1e80b2083a2fce90259ac0634d9e7f796f459fcd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880975"
---
# <a name="about-azure-vm-backup"></a>Om Säkerhetskopiering av virtuella Azure-datorer

Den här artikeln beskrivs hur [Azure Backup-tjänsten](backup-introduction-to-azure-backup.md) säkerhetskopierar virtuella Azure-datorer (VM).

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Här är hur Azure Backup är klar en säkerhetskopiering för virtuella Azure-datorer:

1. För virtuella Azure-datorer som har valts för säkerhetskopiering startar Azure Backup ett säkerhetskopieringsjobb enligt schemat för säkerhetskopiering du anger.
1. Under den första säkerhetskopieringen installeras en säkerhetskopieringstillägget på den virtuella datorn om Virtuellt datorn körs.
    - För Windows-datorer i _VMSnapshot_ -tillägget har installerats.
    - För virtuella Linux-datorer i _VMSnapshotLinux_ -tillägget har installerats.
1. För Windows-datorer som kör samordnar säkerhetskopiering med Windows Volume Shadow Copy Service (VSS) kan en programkonsekvent ögonblicksbild av den virtuella datorn.
    - Som standard tar Backup Fullständig VSS-säkerhetskopiering.
    - Om Backup inte kan dra en programkonsekvent ögonblicksbild, sedan tar det en filkonsekvent ögonblicksbild av det underliggande lagringsutrymmet (eftersom ingen programskrivning du medan Virtuellt datorn stoppas).
1. Säkerhetskopieringen tar en filkonsekvent säkerhetskopiering för virtuella Linux-datorer. Du måste manuellt anpassa före/efter-skript för appkonsekventa ögonblicksbilder.
1. När säkerhetskopieringen har tagit ögonblicksbilden överförs data till valvet. 
    - Säkerhetskopieringen optimeras genom att säkerhetskopiera varje VM-disk parallellt.
    - För varje disk som säkerhetskopieras, Azure Backup läser block på disken och identifierar och överför endast de datablock som har ändrats (delta) sedan föregående säkerhetskopia.
    - Ögonblicksbilddata kan inte kopieras direkt till valvet. Det kan ta några timmar vid Högbelastningstider. Total tid för säkerhetskopiering för en virtuell dator ska vara mindre än 24 timmar för principer för daglig säkerhetskopiering.

1. När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

![Arkitektur för säkerhetskopiering av virtuell Azure-dator](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Kryptering av Virtuella Azure-säkerhetskopieringar

När du säkerhetskopierar virtuella Azure-datorer med Azure Backup är virtuella datorer krypterade i vila med Storage Service Encryption (SSE). Azure Backup kan säkerhetskopiera virtuella Azure-datorer som är krypterade med hjälp av Azure Disk Encryption.

**Kryptering** | **Detaljer** | **Support**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption krypterar både Operativsystemet och datadiskarna för virtuella Azure-datorer.<br/><br/> Azure Disk Encryption kan integreras med BitLocker-krypteringsnycklar (BEKs), som skyddas i ett nyckelvalv som hemligheter. Azure Disk Encryption är integrerat med Azure Key Vault nyckelkryptering nycklar (KeyExchange-nycklar). | Azure Backup stöder säkerhetskopiering av hanterade och ohanterade virtuella datorer i Azure krypteras endast BEKs eller med BEKs tillsammans med KeyExchange-nycklar.<br/><br/> Både BEKs och KeyExchange-nycklar säkerhetskopieras och krypterad.<br/><br/> Eftersom KeyExchange-nycklar och BEKs som säkerhetskopieras, kan användare med behörighet att återställa nycklar och hemligheter tillbaka till nyckelvalvet om det behövs. Dessa användare även återställa den krypterade virtuella datorn.<br/><br/> Krypterade nycklar och hemligheter kan inte läsas av obehöriga användare eller av Azure.
**SSE** | Azure Storage erbjuder kryptering i vila med SSE, genom att automatiskt kryptera data innan de lagras. Azure Storage kan du också dekrypterar data innan de hämtas. | Azure Backup använder SSE för vilande kryptering av virtuella Azure-datorer.

Säkerhetskopiering stöder både virtuella datorer som har krypterats med BEKs endast eller virtuella datorer som har krypterats med BEKs tillsammans med KeyExchange-nycklar för hanterade och ohanterade virtuella Azure-datorer.

Säkerhetskopierade BEKs (hemligheter) och KeyExchange-nycklar (nycklar) krypteras. De kan läsa och används endast när de har återställts till nyckelvalvet av behöriga användare. Varken obehöriga användare eller Azure kan läsa eller använda säkerhetskopierade nycklar eller hemligheter.

BEKs säkerhetskopieras också. Så om BEKs går förlorade kan kan behöriga användare återställa BEKs till nyckelvalvet och återställa krypterade virtuella datorer. Endast användare med nödvändiga behörighetsnivån kan säkerhetskopiera och återställa krypterade virtuella datorer eller nycklar och hemligheter.

## <a name="snapshot-creation"></a>Skapa en ögonblicksbild

Azure Backup har tagit ögonblicksbilder enligt schemat för säkerhetskopiering. 

- **Windows-datorer:** För Windows-datorer samordnar Backup-tjänsten med VSS för att ta en programkonsekvent ögonblicksbild av VM-diskarna.

  - Som standard tar Azure Backup Fullständig VSS-säkerhetskopiering. [Läs mer](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Ange följande registernyckel för att ändra inställningen så att Azure Backup har tagit VSS säkerhetskopior från en kommandotolk:

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **Virtuella Linux-datorer:** Om du vill ta appkonsekventa ögonblicksbilder av virtuella Linux-datorer använder Linux förskript och efterskript ramverk för att skriva egna skript för att säkerställa konsekvens.

  - Azure Backup anropar bara de före/efter-skript som skrivits av dig.
  - Om förskript och efterskript köras, märker Azure Backup återställningspunkten som programkonsekventa. När du använder anpassade skript, är du dock slutgiltigt ansvariga för konsekvens för programmet.
  - [Läs mer](backup-azure-linux-app-consistent.md) om hur du konfigurerar skript.

### <a name="snapshot-consistency"></a>Ögonblicksbild konsekvens

I följande tabell beskrivs de olika typerna av ögonblicksbild konsekvens:

**ögonblicksbild** | **Detaljer** | **Recovery** | **Beräkningen**
--- | --- | --- | ---
**Programkonsekvent** | Programkonsekvent säkerhetskopiering avbilda minne innehåll och väntande i/o-åtgärder. Appkonsekventa ögonblicksbilder använda VSS-skrivaren (eller före/efter-skript för Linux) för att säkerställa konsekvens AppData innan en säkerhetskopiering sker. | När du återställer en virtuell dator med en programkonsekvent ögonblicksbild, den virtuella datorn startas. Det finns inga skadade data eller dataförlust. Apparna starta i ett konsekvent tillstånd. | Windows: Alla VSS-skrivare har uppdaterats<br/><br/> Linux: Skript är före/efter konfigurerad och lyckades
**Filsystemkonsekvent** | Programkonsekventa säkerhetskopior för filsystem som tillhandahåller konsekvens genom att ta en ögonblicksbild av alla filer på samma gång.<br/><br/> | När du återställer en virtuell dator med en konsekvent ögonblicksbild filsystemet, den virtuella datorn startas. Det finns inga skadade data eller dataförlust. Appar måste implementera sina egna ”åtgärds-”-mekanism för att se till att återställda data är konsekventa. | Windows: Vissa VSS-skrivarna misslyckades <br/><br/> Linux: Som standard (om före/efter skript inte konfigurerats eller misslyckade)
**Kraschkonsekvent** | Kraschkonsekventa ögonblicksbilder inträffar vanligtvis om en Azure-dator stängs av vid tidpunkten för säkerhetskopieringen. Endast de data som redan finns på disken vid tidpunkten för säkerhetskopieringen inhämtas och säkerhetskopieras.<br/><br/> En kraschkonsekvent återställningspunkt garantera inte datakonsekvens för operativsystemet eller appen. | Även om det finns inga garantier, startas vanligtvis den virtuella datorn och startar sedan en diskkontroll för att åtgärda skadade data. Alla data i minnet eller skrivåtgärder som inte har överfört till disk innan kraschen går förlorade. Appar implementerar sina egna dataverifieringen. En databas-app kan till exempel använda dess transaktionsloggen för verifiering. Om transaktionsloggen har poster som inte finns i databasen, samlar databasprogrammet transaktioner tillbaka förrän data är konsekventa. | Virtuella datorn är i avstängning

## <a name="backup-and-restore-considerations"></a>Överväganden för säkerhetskopiering och återställning 

**Beräkningen** | **Detaljer**
--- | ---
**Disk** | Säkerhetskopiering av VM-diskar är parallell. Till exempel om en virtuell dator har fyra diskar, försöker Backup-tjänsten att säkerhetskopiera alla fyra diskar parallellt. Backup är inkrementell (endast ändrade data).
**Scheduling** |  Säkerhetskopiera virtuella datorer i olika vid olika tidpunkter på dagen för att minska säkerhetskopieringstrafik och kontrollera att tiderna inte överlappar varandra. Säkerhetskopiera virtuella datorer på samma gång orsakar trafik Sylter.
**Förbereda säkerhetskopiering** | Tänk på den tid som behövs för att förbereda säkerhetskopieringen. Förberedelsetid innehåller installera eller uppdatera säkerhetskopieringstillägget och utlösa en ögonblicksbild enligt schemat för säkerhetskopiering.
**Dataöverföring** | Överväg att den tid som behövs för Azure Backup att identifiera de inkrementella ändringarna från tidigare säkerhetskopia.<br/><br/> I en inkrementell säkerhetskopia anger Azure Backup ändringarna beräknar kontrollsumma av blocket. Om ett block ändras måste markeras den för överföring till valvet. Tjänsten analyserar de identifierade blocken att ytterligare minska mängden data du överför. När du har utvärderat de ändrade blocken överför Azure Backup ändringarna till valvet.<br/><br/> Det kan finnas en fördröjning mellan att ta ögonblicksbilden och kopiera den till valvet.<br/><br/> Vid Högbelastningstider, kan det ta upp till åtta timmar för säkerhetskopior som ska bearbetas. Tid för säkerhetskopiering för en virtuell dator ska vara mindre än 24 timmar för daglig säkerhetskopiering.
**Den första säkerhetskopieringen** | Även om den totala tiden för säkerhetskopieringen för inkrementell säkerhetskopiering är mindre än 24 timmar som inte kanske är fallet för den första säkerhetskopieringen. Den tid som behövs för den första säkerhetskopieringen beror på storleken för data och när säkerhetskopieringen har bearbetats.
**Återställa kö** | Azure Backup-processer återställa jobb från flera lagringskonton på samma gång och all återställning begäranden i en kö.
**Återställ kopia** | Under återställningsprocessen kopieras data från valvet till lagringskontot.<br/><br/> Totalt antal återställningstiden beror på i/o-åtgärder per sekund (IOPS) och genomströmning på lagringskontot.<br/><br/> Välj ett lagringskonto som inte lästs in med andra programskrivningar och läsningar för att minska tid som kopia.

### <a name="backup-performance"></a>Prestanda vid säkerhetskopiering

Dessa vanliga scenarier kan påverka den totala tiden för säkerhetskopieringen:

- **Lägger till en ny disk till en skyddad virtuell Azure-dator:** Om en virtuell dator används för tillfället inkrementell säkerhetskopia och en ny disk har lagts till, ökar säkerhetskopierades. Total tid för säkerhetskopiering kan vara mer än 24 timmar på grund av den inledande replikeringen av den nya disken, tillsammans med deltareplikering av befintliga diskar.
- **Fragmenterade diskar:** Säkerhetskopiering är snabbare när diskändringar är sammanhängande. Om ändringarna är utspridd och fragmenterad över en disk, blir säkerhetskopiering långsammare. 
- **Disk omsättning:** Om skyddade diskar som är under inkrementell säkerhetskopiering har en daglig omsättning av fler än 200 GB, säkerhetskopiering kan ta lång tid (fler än åtta timmar) för att slutföra. 
- **Backup-versioner:** Den senaste versionen av säkerhetskopiering (kallas version omedelbar återställning) använder en mer optimerade process än kontrollsumma jämförelse för att identifiera ändringar. Men om du använder omedelbar återställning och har tagit bort en ögonblicksbild, säkerhetskopieringen växlar till kontrollsumma jämförelse. I det här fallet kommer säkerhetskopieringen överstiger 24 timmar (eller misslyckas).

## <a name="best-practices"></a>Bästa praxis

När du konfigurerar säkerhetskopior av virtuella datorer, föreslår vi följande dessa metoder:

- Ändra schema standardtiderna som anges i en princip. Om standardtid som i principen är 12:00 AM, öka exempelvis tidtagningen av flera minuter så att resurserna används optimalt.
- För säkerhetskopiering av virtuella datorer som använder premium-lagring, rekommenderar vi kör den senaste versionen av Azure Backup ([omedelbar återställning](backup-instant-restore-capability.md)). Om du inte kör den senaste versionen, allokerar säkerhetskopiering cirka 50 procent av det totala utrymmet. Backup-tjänsten kräver den här utrymme för att kopiera ögonblicksbilden till samma lagringskonto och för att överföra den till valvet.
- Om du återställa virtuella datorer från ett enda valv, rekommenderar vi att du använder olika [lagringskonton för generell användning v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) så att mål-lagringskontot inte begränsas. Varje virtuell dator måste till exempel ha ett annat lagringskonto. Till exempel om 10 virtuella datorer har återställts måste använda 10 olika lagringskonton.
- Återställer från en general-purpose v1 lagringsskikt (snapshot) kommer att slutföras på bara några minuter eftersom ögonblicksbilden är i samma lagringskonto. Återställningar från lagringsskikt generell användning v2 (valv) kan ta timmar. I fall där data finns tillgänglig i allmänna v1-lagring, rekommenderar vi att du använder den [omedelbar återställning](backup-instant-restore-capability.md) funktionen för snabbare återställningar. (Om data måste återställas från ett valv, sedan det tar längre tid.)
- Gränsen för antalet diskar per lagringskonto är i förhållande till hur mycket diskarna används av program som körs på en infrastruktur som en tjänst (IaaS) VM. Som en allmän regel om 5 till 10 diskar eller mer finns på ett enda lagringskonto, en belastningsutjämning genom att flytta vissa diskar för att avgränsa storage-konton.

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Azure virtuella datorer säkerhetskopieras med Azure Backup är föremål för [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Fakturering startar inte förrän den första framgångsrika säkerhetskopieringen är klar. Nu startar faktureringen för både lagring och skyddade virtuella datorer. Fakturering fortsätter så länge alla säkerhetskopierade data för den virtuella datorn lagras i ett valv. Om du slutar skydda en virtuell dator, men säkerhetskopierade data för den virtuella datorn finns i ett valv, fortsätter fakturering.

Fakturering för en angiven virtuell dator stoppar endast om skyddet har stoppats och tar bort alla säkerhetskopierade data. När skydd avslutades och det finns inga aktiva säkerhetskopieringsjobb, storleken på den senaste framgångsrika säkerhetskopieringen för virtuell dator blir den storlek på skyddad instans som används för den månatliga fakturan.

Den skyddade instansen beräkningen baseras på den *faktiska* storleken på den virtuella datorn. Den Virtuella datorns storlek är summan av alla data på den virtuella datorn, exklusive tillfällig lagring. Priserna baseras på de faktiska data som har lagrats på datadiskar, inte på maximalt storlek som stöds för varje datadisk som är kopplad till den virtuella datorn.

På samma sätt baseras säkerhetskopieringslagring-faktura på mängden data som lagras i Azure Backup, vilket är summan av de faktiska data i varje återställningspunkt.

Till exempel ta en A2-den standardstorlek VM som har två ytterligare datadiskar med en maximal storlek på 4 TB. I följande tabell visas de faktiska data som lagras på var och en av dessa diskar:

**Disk** | **Maxstorlek** | **Faktiska data finns**
--- | --- | ---
OS-disk | 4 095 GB | 17 GB
Lokal/temporär disk | 135 GB | 5 GB (ingår inte för säkerhetskopiering)
Datadisk 1 | 4 095 GB | 30 GB
Datadisk 2 | 4 095 GB | 0 GB

Den verkliga storleken hos den virtuella datorn är i det här fallet 17 GB + 30 GB + 0 GB = 47 GB. Den här storleken för skyddade instansen (47 GB) blir grund för månatliga faktura. När mängden data i den virtuella datorn växer kan används den skyddade instans storleken för fakturering ändringar så att de matchar.

## <a name="next-steps"></a>Nästa steg

Nu kan [förbereda för säkerhetskopiering av Azure virtuella datorer](backup-azure-arm-vms-prepare.md).
