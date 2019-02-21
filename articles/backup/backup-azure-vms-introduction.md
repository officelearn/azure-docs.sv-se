---
title: Om Säkerhetskopiering av virtuella Azure-datorer
description: Läs om säkerhetskopiering av Azure virtuella datorer och Observera några metodtips.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c38c457bbf428d7252cf57168685201a2ca227ba
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446808"
---
# <a name="about-azure-vm-backup"></a>Om Säkerhetskopiering av virtuella Azure-datorer

Den här artikeln beskrivs hur [Azure Backup-tjänsten](backup-introduction-to-azure-backup.md) säkerhetskopierar virtuella Azure-datorer.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Här är hur Azure Backup är klar en säkerhetskopiering för virtuella Azure-datorer.

1. Azure Backup-tjänsten initierar en säkerhetskopiering i enlighet med schemat för säkerhetskopiering du anger för virtuella Azure-datorer som har valts för säkerhetskopiering.
2. Under den första säkerhetskopieringen installeras en säkerhetskopieringstillägget på den virtuella datorn om den körs.
    - För Windows-datorer i _VMSnapshot_ -tillägget har installerats.
    - För virtuella Linux-datorer i _VMSnapshotLinux_ -tillägget har installerats.
3. För Windows-datorer som kör samordnar säkerhetskopiering med VSS för att ta en programkonsekvent ögonblicksbild av den virtuella datorn.
    - Som standard tar Backup Fullständig VSS-säkerhetskopiering.
    - Om säkerhetskopieringen är det går inte att ta en programkonsekvent ögonblicksbild får sedan tar det en filkonsekvent ögonblicksbild av det underliggande lagringsutrymmet (eftersom ingen programskrivning du medan Virtuellt datorn stoppas).
4. För Linux virtuella datorer Backup tar du en filkonsekvent säkerhetskopiering. Du måste manuellt anpassa före/efter-skript för appkonsekventa ögonblicksbilder.
5. När ögonblicksbilden tas data har överförts till valvet. 
    - Backup optimeras genom att säkerhetskopiera varje VM-disk parallellt.
    - För varje disk som säkerhetskopieras, Azure Backup läser block på disken, och identifierar och överför endast de datablock som har ändrats sedan föregående säkerhetskopia (delta).
    - När ögonblicksbilden tas data har överförts till valvet.
    - Ögonblicksbilddata kan inte kopieras direkt till valvet. Det kan ta några timmar vid Högbelastningstider. Total tid för säkerhetskopiering för en virtuell dator vara mindre att 24 timmar för principer för daglig säkerhetskopiering.

6. När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

![Arkitektur för säkerhetskopiering av virtuell Azure-dator](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Kryptera Virtuella Azure-säkerhetskopieringar

När du säkerhetskopierar virtuella Azure-datorer med Azure Backup är virtuella datorer krypterade i vila med Storage Service Encryption (SSE). Dessutom kan kan Azure Backup säkerhetskopiera virtuella Azure-datorer som är krypterade med hjälp av Azure Disk Encryption (ADE).


**Kryptering** | **Detaljer** | **Support**
--- | --- | ---
**ADE** | ADE krypterar både Operativsystemet och datadiskarna för virtuella Azure-datorer.<br/><br/> ADE integreras med BitLocker krypteringsnycklar (BEK) skyddas i ett nyckelvalv som hemligheter, eller med krypteringsnycklar för Azure Key Vault nyckel (KEK). | Azure Backup stöder säkerhetskopiering av hanterade och ohanterade virtuella datorer i Azure krypteras endast BEK eller med BEK och KEK.<br/><br/> Båda BEK och säkerhetskopieras och krypterad.<br/><br/> Eftersom KEK BEK säkerhetskopieras och, om det behövs användare med behörigheter kan återställa nycklar och hemligheter till nyckelvalvet och återställa krypterade virtuella datorn.<br/><br/> Krypterade nycklar och hemligheter kan inte läsas av obehöriga användare eller av Azure.
**SSE** | Med SSE, Azure storage tillhandahåller kryptering i vila genom att automatiskt kryptera data innan de lagras och dekrypterar dem före hämtning. | Azure Backup använder SSE för kryptering av vilande data med Azure virtuella datorer.

- Säkerhetskopiering av virtuella datorer som har krypterats med BitLocker-kryptering nyckel (BEK) endast och BEK tillsammans med nyckel kryptering Key(KEK) stöds för hanterade och ohanterade virtuella Azure-datorer.
- Den säkerhetskopierade BEK (hemligheter) och KEK (nycklar) krypteras. De kan läsa och används endast när återställas tillbaka till nyckelvalvet av behöriga användare. Varken obehöriga användare eller Azure kan läsa eller Använd säkerhetskopieras nycklar eller hemligheter.
- Eftersom BEK också säkerhetskopieras, om BEK går förlorade, kan behöriga användare återställa BEK till Nyckelvalvet och återställa krypterade virtuella datorn. 
- Endast användare med rätt nivå av behörigheter kan säkerhetskopiera och återställa krypterade virtuella datorer, samt nycklar och hemligheter.



## <a name="taking-snapshots"></a>Tagning av ögonblicksbilder

Azure Backup-ögonblicksbilder i enlighet med schemat för säkerhetskopiering. 

- **Virtuella Windows-datorer**: För Windows-datorer samordnar Backup-tjänsten med Volume Shadow Copy Service (VSS) att ta en programkonsekvent ögonblicksbild av VM-diskarna.
    - Som standard tar Azure Backup Fullständig VSS-säkerhetskopiering. [Läs mer](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Om du vill ändra inställningen så att Azure-säkerhetskopior tar VSS säkerhetskopior anger du följande registernyckel från en kommandotolk: **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **Virtuella Linux-datorer**: Om du vill ta appkonsekventa ögonblicksbilder av Linux VM använder Linux förskript och efterskript ramverk för att skriva egna skript för att säkerställa konsekvens.
    -  Azure Backup anropar bara före/efter-skript som har skrivits av dig.
    - Om förskript och efterskript köras, märker Azure Backup återställningspunkten som programkonsekventa. Du är slutgiltigt ansvariga för konsekvens för programmet när du använder anpassade skript.
    - [Läs mer](backup-azure-linux-app-consistent.md) om hur du konfigurerar skript.


### <a name="snapshot-consistency"></a>Ögonblicksbild konsekvens

I följande tabell beskrivs olika typer av konsekvens.

**ögonblicksbild** | **Detaljer** | **Recovery** | **Beräkningen**
--- | --- | --- | ---
**Programkonsekvent** | Programkonsekvent säkerhetskopiering avbilda minne innehåll och väntande i/o-åtgärder. Appkonsekventa ögonblicksbilder använda VSS-skrivaren (eller före/efter-skript för Linux) som ser till att AppData konsekvens innan en säkerhetskopiering sker. | När du återställer med en programkonsekvent ögonblicksbild startas den virtuella datorn. Det finns inga skadade data eller dataförlust. Apparna starta i ett konsekvent tillstånd. | Windows: Alla VSS-skrivare har uppdaterats<br/><br/> Linux: Skript är före/efter konfigurerad och lyckades
**Filsystemkonsekvent** | Programkonsekventa säkerhetskopior av filen ger programkonsekventa säkerhetskopior av diskfiler genom att ta en ögonblicksbild av alla filer på samma gång.<br/><br/> | När du återställer med en filkonsekventa ögonblicksbilder, den virtuella datorn startas. Det finns inga skadade data eller dataförlust. Appar måste implementera sina egna ”åtgärds-”-mekanism för att se till att återställda data är konsekventa. | Windows: Vissa VSS-skrivarna misslyckades <br/><br/> Linux: Som standard (om före/efter-skript inte har konfigurerats eller misslyckade)
**Kraschkonsekvent** | Kraschkonsekvens händer ofta när en Azure-dator stängs av vid tidpunkten för säkerhetskopieringen.  Endast de data som redan finns på disken vid tidpunkten för säkerhetskopieringen inhämtas och säkerhetskopieras.<br/><br/> En kraschkonsekvent återställningspunkt garantera inte datakonsekvens för operativsystemet eller appen. | Det finns inga garantier, men vanligtvis den virtuella datorn startas och sätt med en disk Kontrollera för att åtgärda skadade data. Alla data i minnet eller Skriv inte överföras till disk går förlorade. Appar implementerar sina egna dataverifieringen. Till exempel för en app för databasen samlar om transaktionsloggar innehåller poster som inte finns i databasen, databasprogrammet tills data är konsekventa. | Virtuella datorn är i avstängning


## <a name="restore-considerations"></a>Återställa överväganden 



**Beräkningen** | **Detaljer**
--- | ---
**Disk** | Säkerhetskopiering av virtuell disk är parallell. Om en virtuell dator har fyra diskar, till exempel försöker tjänsten att säkerhetskopiera alla fyra diskar parallellt. Backup är inkrementell (endast ändrade data).
**Scheduling** |  För att minska säkerhetskopieringstrafik överlappar säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen, utan. Säkerhetskopiera virtuella datorer på samma gång orsakar trafik Sylter.
**Förbereda säkerhetskopiering** | Överväg att tiden i förberedelse för säkerhetskopiering som innehåller installera eller uppdatera säkerhetskopieringstillägget och utlösa en ögonblicksbild i enlighet med schemat för säkerhetskopiering.
**Dataöverföring** | Tid som krävs för backup-tjänsten att beräkna de inkrementella ändringarna från tidigare säkerhetskopia.<br/><br/> I en inkrementell säkerhetskopiering avgör ändringarna service-datorer kontrollsumman för blocket. Om ett block ändras dess identifierade för att skicka till valvet. Tjänsten tester i identifierade block att försöka minimera ytterligare dataöverföring. Efter utvärdering av alla ändrats blockerade överförs ändringarna till valvet.<br/><br/> Det kan finnas en fördröjning mellan att ta ögonblicksbilden och kopiera den till valvet.<br/><br/> Det kan ta upp till åtta timmar för säkerhetskopieringar process vid Högbelastningstider. Tid för säkerhetskopiering för en virtuell dator ska vara mindre än 24 timmar för daglig säkerhetskopiering.
**Den första säkerhetskopieringen** | Även om den totala tiden för säkerhetskopieringen på mindre än 24 timmar är giltig för inkrementell säkerhetskopiering, kanske den inte för den första säkerhetskopieringen. Tid behövs beror på storleken på data och när säkerhetskopieringen görs.
**Återställa kö** | Azure Backup-processer återställa jobb från flera lagringskonton på samma gång och återställning begäranden placeras i en kö.
**Återställ kopia** | Under återställningsprocessen kopieras data från valvet till lagringskontot.<br/><br/> Återställ tiden beror på IOPS och dataflöden för storage-konto.<br/><br/> Välj ett lagringskonto som inte lästs in med andra programskrivningar och läsningar för att minska tid som kopia.


### <a name="backup-performance"></a>Prestanda vid säkerhetskopiering

Dessa vanliga scenarier kan påverka tidpunkt för säkerhetskopiering:

- Lägg till en ny disk i en skyddad virtuell Azure-dator: Om en virtuell dator används för tillfället inkrementell säkerhetskopia och en ny disk har lagts till, kan säkerhetskopiering vara mer än 24 timmar på grund av den inledande replikeringen av den nya disken, tillsammans med deltareplikering av befintliga diskar.
- Fragmenterade diskar: Säkerhetskopiering är snabbare när diskändringar är samordnad. Om ändringarna är utspridd och fragmenterad över en disk, blir säkerhetskopiering långsammare. 
- Disk omsättning: Om skyddade diskar som repareras inkrementell säkerhetskopiering har en daglig omsättning av fler än 200 GB, kan säkerhetskopiering ta lång tid (fler än åtta timmar) att slutföra. 
- Backup-versioner: Om du använder den senaste versionen av säkerhetskopiering (kallas version omedelbar återställning) använder en mer optimerade process än kontrollsumma jämförelse för att jämföra ändringar. Om du använder den senaste versionen och har tagit bort en ögonblicksbild, kommer säkerhetskopiering växlar du använder kontrollsumma jämförelse och säkerhetskopieringen överstiger 24 timmar (eller misslyckas).

## <a name="best-practices"></a>Bästa praxis 
Vi rekommenderar att följande dessa metoder när du konfigurerar säkerhetskopior av virtuella datorer:

- Överväg att ändra standard Schemalägg tid som anges i en princip. Om standardtiden principen är 12:00:00 kan du till exempel du ökar med minuter så att resurserna används optimalt.
- För virtuella datorer i Premium allokerar säkerhetskopiering på icke - omedelbar RP-funktionen ~ 50% av det totala utrymmet för kontot. Backup-tjänsten kräver den här utrymme för att kopiera ögonblicksbilden till samma lagringskonto och för att överföra den till valvet.
- För att återställa virtuella datorer från ett enda valv, rekommenderas att använda olika [v2-konton](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) så inte begränsas mållagringskontot. Varje virtuell dator måste till exempel ha olika lagringskonto (om 10 virtuella datorer har återställts och sedan bör du använda 10 olika lagringskonton).
- Återställningar från lagringsskikt för nivå 1 (snapshot) kommer att slutföras på bara några minuter (eftersom det är samma lagringskonto) mot den nivå 2-lagringsskikt (valv) som kan ta timmar. Vi rekommenderar att du använder [omedelbar återställning](backup-instant-restore-capability.md) funktionen för snabbare återställningar för fall där data är tillgängliga på nivå 1 (om data måste återställas från valvet och sedan det tar tid).
- Gränsen för antalet diskar per lagringskonto är i förhållande till hur hög diskarna som används av program som körs på IaaS VM. Kontrollera om flera diskar finns på ett enda lagringskonto. Som en allmän regel om 5 till 10 diskar eller mer finns på ett enda lagringskonto, en belastningsutjämning genom att flytta vissa diskar för att avgränsa storage-konton.

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Azure virtuella datorer säkerhetskopieras med Azure Backup är föremål för [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

- Fakturering startar inte förrän den första framgångsrika säkerhetskopieringen är klar. Nu startar faktureringen för både lagring och skyddade virtuella datorer.
- Fakturering fortsätter så länge det finns några säkerhetskopieringsdata som lagras i ett valv för den virtuella datorn. Om du slutar skydda en virtuell dator, men säkerhetskopierade data för den virtuella datorn finns i ett valv, fortsätter fakturering.
- Fakturering för en angiven virtuell dator stoppar endast om skyddet har stoppats och tar bort alla säkerhetskopierade data.
- När skydd avslutades och det finns inga aktiva säkerhetskopieringsjobb, storleken på den senaste framgångsrika säkerhetskopieringen för virtuell dator blir den storlek på skyddad instans som används för den månatliga fakturan.
- Skyddade instanser beräkningen baseras på den *faktiska* storleken på den virtuella datorn, vilket är summan av alla data på den virtuella datorn, exklusive tillfällig lagring.
- Priserna baseras på den faktiska data som lagras i datadisken.
- Priserna för säkerhetskopiering av virtuella datorer baseras inte på den maximala storleken som stöds för varje datadisk som är kopplade till den virtuella datorn.
- På samma sätt baseras säkerhetskopieringslagring-faktura på mängden data som lagras i Azure Backup, vilket är summan av de faktiska data i varje återställningspunkt.

Till exempel ta en Standard A2 medelstora VM som har två ytterligare datadiskar med en maximal storlek på 4 TB. Följande tabell innehåller de faktiska data som lagras på var och en av dessa diskar:

**Disk** | **Maxstorlek** | **Faktiska data finns**
--- | --- | ---
OS-disk | 4 095 GB | 17 GB 
Lokal/temporär disk | 135 GB | 5 GB (ingår inte för säkerhetskopiering) 
Datadisk 1 | 4 095 GB | 30 GB 
Datadisk 2 | 4 095 GB | 0 GB 

- Den verkliga storleken hos den virtuella datorn är i det här fallet 17 GB + 30 GB + 0 GB = 47 GB.
- Den här storlek på skyddad instans (47 GB) blir grund för månatliga faktura.
- När mängden data i den virtuella datorn växer, används storleken på skyddad instans för fakturering ändringar därefter.


## <a name="next-steps"></a>Nästa steg

Nu kan [förbereda](backup-azure-arm-vms-prepare.md) för virtuell Azure-säkerhetskopiering.
