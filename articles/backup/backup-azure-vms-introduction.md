---
title: Om säkerhetskopiering av Azure virtuella datorer
description: Läs om säkerhetskopiering av Azure virtuella datorer och Observera några metodtips.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: ebb8d5d141dab39e73297342907d4439b30a9fbf
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042486"
---
# <a name="about-azure-vm-backup"></a>Om säkerhetskopiering av Azure virtuella datorer

Den här artikeln beskrivs hur [Azure Backup-tjänsten](backup-introduction-to-azure-backup.md) säkerhetskopierar virtuella Azure-datorer.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Här är hur Azure Backup är klar en säkerhetskopiering för virtuella Azure-datorer.

1. Azure Backup-tjänsten initierar en säkerhetskopiering i enlighet med schemat för säkerhetskopiering du anger för virtuella Azure-datorer som har valts för säkerhetskopiering.
2. Tjänsten utlöser säkerhetskopieringstillägget.
    - Windows-datorer används den _VMSnapshot_ tillägget.
    - Virtuella Linux-datorer används den _VMSnapshotLinux_ tillägget.
    - Tillägget installeras under den första säkerhetskopieringen av virtuella datorer.
    - Om du vill installera tillägget, måste du köra den virtuella datorn.
    - Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).
4. Säkerhetskopieringstillägget tar en storage-nivå, programkonsekvent ögonblicksbild.
5. När ögonblicksbilden tas data har överförts till valvet. För att maximera effektiviteten, tjänsten identifierar och överför endast datablock som har ändrats sedan föregående säkerhetskopia (delta).
5. När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

![Arkitektur för säkerhetskopiering av virtuell Azure-dator](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Datakryptering

Azure Backup krypterar inte data som en del av säkerhetskopieringen. Azure Backup stöder säkerhetskopiering av virtuella Azure-datorer som är krypterade med Azure Disk Encryption.

- Säkerhetskopiering av virtuella datorer krypteras med Bitlocker-kryptering Key(BEK) endast och BEK tillsammans med nyckel kryptering Key(KEK) stöds för hanterade och ohanterade virtuella Azure-datorer.
- BEK(secrets) och KEK(keys) säkerhetskopieras krypteras så att de kan läsa och används endast när återställas tillbaka till nyckelvalvet genom behöriga användare.
- Eftersom BEK också säkerhetskopieras, i scenarier där BEK går förlorad, kan behöriga användare återställa BEK till Nyckelvalvet och återställa krypterade virtuella datorn. Nycklar och hemligheter för krypterade virtuella datorer säkerhetskopieras i krypterad form, så varken obehöriga användare eller Azure kan läsa eller Använd säkerhetskopieras nycklar och hemligheter. Endast användare med rätt nivå av behörigheter kan säkerhetskopiera och återställa krypterade virtuella datorer, samt nycklar och hemligheter.

## <a name="snapshot-consistency"></a>Ögonblicksbild konsekvens

Om du vill ta ögonblicksbilder när appar körs, tar Azure Backup appkonsekventa ögonblicksbilder.

- **Windows-datorer**: För Windows-datorer samordnar Backup-tjänsten med Volume Shadow Copy Service (VSS) till en konsekvent ögonblicksbild av VM-diskarna.
    - Som standard tar Azure Backup Fullständig VSS-säkerhetskopiering. [Läs mer](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Om du vill ändra inställningen så att Azure-säkerhetskopior tar VSS säkerhetskopior anger du följande registernyckel:
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
- **Virtuella Linux-datorer**: För att säkerställa att din virtuella Linux-datorer är appkonsekvent när Azure Backup tar en ögonblicksbild, kan du använda Linux-ramverk för förskript och efterskript. Du kan skriva egna skript för att säkerställa konsekvens när du tar en ögonblicksbild för virtuell dator.
    -  Azure Backup anropar bara före och efter skript som har skrivits av dig.
    - Om förskript och efterskript köras, märker Azure Backup återställningspunkten som programkonsekventa. Du är slutgiltigt ansvariga för konsekvens för programmet när du använder anpassade skript.
    - [Läs mer](backup-azure-linux-app-consistent.md) om hur du konfigurerar skript.


#### <a name="consistency-types"></a>Konsekvens typer

I följande tabell beskrivs olika typer av konsekvens.

**ögonblicksbild** | **VSS-baserade** | **Detaljer** | **Återställning**
--- | --- | --- | ---
**Programkonsekvent** | Ja (endast Windows) | Programkonsekvent säkerhetskopiering avbilda minne innehåll och väntande i/o-åtgärder. Appkonsekventa ögonblicksbilder använda VSS-skrivaren (eller före/efter-skript för Linux) som ser till att AppData konsekvens innan en säkerhetskopiering sker. | När du återställer med en programkonsekvent ögonblicksbild startas den virtuella datorn. Det finns inga skadade data eller dataförlust. Apparna starta i ett konsekvent tillstånd.
**Filsystemkonsekvent** | Ja (endast Windows) |  Programkonsekventa säkerhetskopior av filen ger programkonsekventa säkerhetskopior av diskfiler genom att ta en ögonblicksbild av alla filer på samma gång.<br/><br/> Azure Backup-återställningspunkter är konsekvent för filen:<br/><br/> -Linux virtuella datorer säkerhetskopior som inte har före/efter-skript eller som har skript som misslyckades.<br/><br/> -Windows VM-säkerhetskopieringar där VSS misslyckades. | När du återställer med en filkonsekventa ögonblicksbilder, den virtuella datorn startas. Det finns inga skadade data eller dataförlust. Appar måste implementera sina egna ”åtgärds-”-mekanism för att se till att återställda data är konsekventa.
**Kraschkonsekvent** | Nej | Kraschkonsekvens händer ofta när en Azure-dator stängs av vid tidpunkten för säkerhetskopieringen.  Endast de data som redan finns på disken vid tidpunkten för säkerhetskopieringen inhämtas och säkerhetskopieras.<br/><br/> En kraschkonsekvent återställningspunkt garantera inte datakonsekvens för operativsystemet eller appen. | Det finns inga garantier, men vanligtvis den virtuella datorn startas och sätt med en disk Kontrollera för att åtgärda skadade data. Alla data i minnet eller Skriv inte överföras till disk går förlorade. Appar implementerar sina egna dataverifieringen. Till exempel för en app för databasen samlar om transaktionsloggar innehåller poster som inte finns i databasen, databasprogrammet tills data är konsekventa.


## <a name="service-and-subscription-limits"></a>Begränsningar för tjänsten och prenumeration

Azure Backup har ett antal gränser runt prenumerationer och valv.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Prestanda vid säkerhetskopiering

### <a name="disk-considerations"></a>Överväganden för diskar

Säkerhetskopieringen optimerar genom att säkerhetskopiera var och en av den Virtuella datorns disk parallellt. Om en virtuell dator har fyra diskar, till exempel försöker tjänsten att säkerhetskopiera alla fyra diskar parallellt. För varje disk som säkerhetskopieras, Azure Backup läser block på disken och lagrar bara de ändrade data (inkrementell säkerhetskopiering).


### <a name="scheduling-considerations"></a>Överväganden för schemaläggning

Schemaläggning av säkerhetskopiering påverkar prestanda.

- Om du konfigurerar principer så att alla virtuella datorer säkerhetskopieras samtidigt, kan du har schemalagt trafik har fastnat, som säkerhetskopieringen försöker att säkerhetskopiera alla diskar parallellt.
- Att minska säkerhetskopieringstrafik, säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen, utan överlappande.


### <a name="time-considerations"></a>Tid överväganden

Medan mest säkerhetskopieringstiden läggs på läsning och kopiering av data, Lägg till andra åtgärder och den totala tiden som behövs för att säkerhetskopiera en virtuell dator:

- **Installera säkerhetskopieringstillägget**: Tid som krävs att installera eller uppdatera säkerhetskopieringstillägget.
- **Utlösaren ögonblicksbild**: Åtgången tid för att utlösa en ögonblicksbild. Ögonblicksbilder utlöses nära den schemalagda tiden för säkerhetskopiering.
- **Väntetid för kön**: Eftersom Backup service processer jobb från flera lagringskonton för kunder på samma gång, kanske ögonblicksbilddata inte omedelbart kopieras till Recovery Services-valvet. Vid Högbelastningstider belastning, kan det ta upp till åtta timmar innan säkerhetskopiorna som ska bearbetas. Total tid för VM-säkerhetskopiering är dock mindre än 24 timmar för principer för daglig säkerhetskopiering.
- **Den första säkerhetskopieringen**: Även om den totala tiden för säkerhetskopieringen på mindre än 24 timmar är giltig för inkrementell säkerhetskopiering, kanske den inte för den första säkerhetskopieringen. Tid behövs beror på storleken på data och när säkerhetskopieringen görs.
- **Tid för överföring av data**: Tid som behövs för backup-tjänsten att beräkna de inkrementella ändringarna från tidigare säkerhetskopia och överför ändringarna till valvet lagring.

### <a name="factors-affecting-backup-time"></a>Faktorer som påverkar säkerhetskopieringstiden

Säkerhetskopieringen består av två faser, att ta ögonblicksbilder och överföra ögonblicksbilderna till valvet. Backup-tjänsten optimerar för lagring.

- När du överför ögonblicksbilddata till ett valv, överför tjänsten endast inkrementella ändringar från tidigare ögonblicksbild.
- Tjänsten beräknar kontrollsumma för block för att fastställa de inkrementella ändringarna.
    - Om ett block ändras identifieras blocket som ett block som ska skickas till valvet.
    - Tester för tjänsten ytterligare till var och en av de identifierade blocken, söker efter möjligheter att minimera dataöverföring.
    - När du har utvärderat alla ändrade block tjänsten slår samman ändringarna och skickar dem till valvet.

Situationer som kan påverka tidpunkt för säkerhetskopiering inkluderar följande:

- **Inledande säkerhetskopiering för en nyligen tillagd disk till en redan skyddad virtuell dator**: Om en virtuell dator används för tillfället inkrementell säkerhetskopiering och läggs en ny disk till den här virtuella datorn går säkerhetskopiering varaktigheten längre än 24 timmar eftersom den nytillagda disken måste genomgå en inledande replikering och deltareplikering av befintliga diskar.
- **Fragmentering**: Säkerhetskopiering produkten söker efter inkrementella ändringar mellan två säkerhetskopieringar åtgärder. Säkerhetskopiering är snabbare när ändringar på disken är samordnad jämfört med ändringar är spridning på disken. 
- **Omsättningen**: Dagliga dataomsättningen (för inkrementell replikering) per disk som är större än 200 GB kan ta mer än ~ 8 timmar att slutföra åtgärden. Om virtuella datorn har mer än en disk och en av dessa diskar tar längre tid för säkerhetskopiering, sedan det kan påverka den totala säkerhetskopieringen (eller kan resultera i fel). 
- **Kontrollsumman jämförelse (kopia) läge**: CC-läge är förhållandevis långsammare än optimerad läge som används av omedelbar RP. Om du redan använder omedelbar RP och har tagit bort nivå 1-ögonblicksbilder, växlar säkerhetskopiering till CC läge vilket gjorde att säkerhetskopiering åtgärden överstiger 24 timmar (eller misslyckas).

## <a name="restore-considerations"></a>Återställa överväganden

En återställningsåtgärd består av två huvudsakliga uppgifter: kopiera data från valvet till det valda lagringskontot och skapa den virtuella datorn. Den tid som behövs för att kopiera data från valvet beror på var säkerhetskopiorna som lagras i Azure och platsen för lagringskontot. Åtgången tid för att kopiera data beror på:

- **Väntetid för kön**: Eftersom tjänsten bearbetar återställa jobb från flera lagringskonton på samma gång, placeras återställning begäranden i en kö.
- **Tid att kopiera data**: Data kopieras från valvet till lagringskontot. Återställ tiden beror på IOPS och dataflöden för det valda lagringskontot, som använder Azure Backup-tjänsten. Om du vill minska tid som kopiering under återställningsprocessen, Välj ett lagringskonto som inte lästs in med andra programskrivningar och läsningar.

## <a name="best-practices"></a>Bästa praxis

Vi rekommenderar att följande dessa metoder när du konfigurerar säkerhetskopior av virtuella datorer:

- Uppgradera valv till omedelbar RP. Dessa [fördelar](backup-upgrade-to-vm-backup-stack-v2.md), [överväganden](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade), och fortsätt sedan med att uppgradera genom att följa de här [instruktioner](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).  
- Du kan ändra standardnamnet princip tid (för t.ex. Om din princip standardtid är 12:00 AM Överväg att öka med minuter) när ögonblicksbilder av data kommer att se till att resurser används optimalt.
- För virtuella datorer i Premium allokerar säkerhetskopiering på icke - omedelbar RP-funktionen ~ 50% av det totala utrymmet för kontot. Backup-tjänsten kräver den här utrymme för att kopiera ögonblicksbilden till samma lagringskonto och för att överföra den till valvet.
- För att återställa virtuella datorer från ett enda valv, rekommenderas att använda olika [gpv2-lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) så inte begränsas mållagringskontot. Varje virtuell dator måste till exempel ha olika lagringskonto (om 10 virtuella datorer har återställts och sedan bör du använda 10 olika lagringskonton).
- Återställningar från lagringsskikt för nivå 1 (snapshot) kommer att slutföras på bara några minuter (eftersom det är samma lagringskonto) mot den nivå 2-lagringsskikt (valv) som kan ta timmar. Vi rekommenderar att du använder [omedelbar RP](backup-upgrade-to-vm-backup-stack-v2.md) funktionen för snabbare återställningar för fall där data är tillgängliga på nivå 1 (om data måste återställas från valvet och sedan det tar tid).
- Gränsen för antalet diskar per lagringskonto är i förhållande till hur hög diskarna som används av program som körs på IaaS VM. Kontrollera om flera diskar finns på ett enda lagringskonto. Som en allmän regel om 5 till 10 diskar eller mer finns på ett enda lagringskonto, en belastningsutjämning genom att flytta vissa diskar för att avgränsa storage-konton.

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Azure virtuella datorer säkerhetskopieras med Azure Backup är föremål för [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

- Faktureringen startar inte förrän den första framgångsrika säkerhetskopieringen är klar. Nu startar faktureringen för både lagring och skyddade instanser.
- Fakturering fortsätter så länge det finns några säkerhetskopieringsdata som lagras i ett valv för den virtuella datorn. Om du stoppar skyddet på den virtuella datorn, men data för säkerhetskopiering av virtuella datorer finns i ett valv, fortsätter fakturering.
- Fakturering för en angiven virtuell dator stoppar endast om skyddet har stoppats och tar bort alla säkerhetskopierade data.
- När skydd avslutades och det finns inga aktiva säkerhetskopieringsjobb, storleken på den senaste framgångsrika säkerhetskopieringen för virtuell dator blir skyddad instans storleken som används för den månatliga fakturan.
- Skyddade instanser beräkningen baseras på den *faktiska* storleken på den virtuella datorn, vilket är summan av alla data på den virtuella datorn, exklusive tillfällig lagring.
- Priserna baseras på den faktiska data som lagras i datadisken.
- Priserna för säkerhetskopiering av virtuella datorer baseras inte på den maximala storleken som stöds för varje datadisk som är kopplade till den virtuella datorn.
- På samma sätt baseras säkerhetskopieringslagring-faktura på mängden data som lagras i Azure Backup, vilket är summan av de faktiska data i varje återställningspunkt.

Till exempel ta en Standard A2 medelstora virtuell dator med två ytterligare datadiskar med en maximal storlek på 4 TB. Följande tabell innehåller de faktiska data som lagras på var och en av dessa diskar:

| Disktyp | Maxstorlek | Faktiska data finns |
| --------- | -------- | ----------- |
| Operativsystemdisk |4 095 GB |17 GB |
| Lokal disk / temporär disk |135 GB |5 GB (ingår inte för säkerhetskopiering) |
| Datadisk 1 |4 095 GB |30 GB |
| Datadisk 2 |4 095 GB |0 GB |

- Den verkliga storleken hos den virtuella datorn för virtuell dator är i det här fallet 17 GB + 30 GB + 0 GB = 47 GB.
- Den här storleken för skyddade instanser (47 GB) blir grund för månatliga faktura.
- När mängden data i den virtuella datorn växer kan används den skyddade instansen storleken för fakturering ändringar därefter.


## <a name="next-steps"></a>Nästa steg

När du har granskat säkerhetskopieringsprocessen och prestandaöverväganden, gör du följande:

- [Lär dig mer om](../virtual-machines/windows/premium-storage-performance.md) justering appar för optimala prestanda med Azure storage. Artikeln fokuserar på premiumlagring, gäller men även för standard storage-diskar.
- [Kom igång](backup-azure-arm-vms-prepare.md) med backup genom att granska support för virtuella datorer och begränsningar för hur du skapar ett valv och förbereder virtuella datorer för säkerhetskopiering.
