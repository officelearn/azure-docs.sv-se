---
title: Planera din infrastruktur för VM-säkerhetskopiering i Azure
description: Att tänka på när du planerar att säkerhetskopiera virtuella datorer i Azure
services: backup
author: markgalioto
manager: carmonm
keywords: Säkerhetskopiera virtuella datorer, virtuella datorer för säkerhetskopiering
ms.service: backup
ms.topic: conceptual
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: ae02a1bcbf00a022cfd884b02141ce084f1fffa8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232468"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planera din infrastruktur för VM-säkerhetskopiering i Azure
Den här artikeln ger prestanda och resurs förslag på hur du planerar din infrastruktur för säkerhetskopiering av virtuell dator. Den definierar även viktiga aspekter av Backup-tjänsten; följande aspekter kan vara avgörande för att fastställa din arkitektur kapacitetsplanering och schemaläggning. Om du har [förberett din miljö](backup-azure-arm-vms-prepare.md), planering är nästa steg innan du börjar [att säkerhetskopiera virtuella datorer](backup-azure-arm-vms.md). Om du behöver mer information om virtuella Azure-datorer finns i den [dokumentation om Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). 

> [!NOTE]
> Den här artikeln är avsedd för hanterade och ohanterade diskar. Om du använder ohanterade diskar finns rekommendationer för lagringskonton. Om du använder [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), du behöver bekymra dig om problem med prestanda eller resurs. Azure optimerar lagringsanvändningen åt dig.
>

## <a name="how-does-azure-back-up-virtual-machines"></a>Hur fungerar Azure säkerhetskopiera virtuella datorer?
När Azure Backup-tjänsten initierar en säkerhetskopiering på den schemalagda tiden service-utlösare säkerhetskopieringstillägget att ta en ögonblicksbild för point-in-time. Azure Backup-tjänsten använder den _VMSnapshot_ tillägget i Windows, och _VMSnapshotLinux_ tillägget i Linux. Tillägget installeras under den första säkerhetskopieringen av virtuella datorer. Om du vill installera tillägget, måste du köra den virtuella datorn. Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).

När Backup-tjänsten tar en ögonblicksbild av virtuella Windows-datorer kontaktar den VSS-tjänsten (Volume Shadow Copy) för att få en konsekvent ögonblicksbild av den virtuella datorns diskar. Om du säkerhetskopierar virtuella Linux-datorer, kan du skriva egna skript för att säkerställa konsekvens när du tar en ögonblicksbild för virtuell dator. Information om att aktivera dessa skript finns senare i den här artikeln.

När Azure Backup-tjänsten har tagit ögonblicksbilden överförs data till valvet. För att maximera effektiviteten identifierar och överför tjänsten endast de datablock som har ändrats sedan föregående säkerhetskopia.

![Arkitektur för säkerhetskopiering av virtuell Azure-dator](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

> [!NOTE]
> 1. Under säkerhetskopieringen innehåller Azure Backup inte den temporära disken kopplas till den virtuella datorn. Mer information finns i bloggen på [tillfällig lagring](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Azure Backup en ögonblicksbild för lagring på servernivå och överför denna ögonblicksbild till valvet. Ändra inte lagringskontonycklarna tills säkerhetskopieringen är klar.
>

### <a name="data-consistency"></a>Datakonsekvens
Säkerhetskopiera och återställa företag kritiska data är komplicerat genom att affärskritiska data måste säkerhetskopieras samtidigt som de program som producerar data körs. För att lösa det, stöder Azure Backup programkonsekventa säkerhetskopior för både Windows och Linux-datorer
#### <a name="windows-vm"></a>Virtuell Windows-dator
Azure Backup har tagit fullständiga säkerhetskopieringar för VSS på virtuella Windows-datorer (Läs mer om [VSS fullständig säkerhetskopiering](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Aktivera VSS-säkerhetskopior genom måste följande registernyckel anges för den virtuella datorn.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Virtuella Linux-datorer

Azure Backup är ett ramverk för skript för att styra arbetsflöde för säkerhetskopiering och miljö. För att säkerställa programkonsekvent Linux VM-säkerhetskopieringar, använder du scripting framework för att skapa anpassade förskript och efterskript. Anropa förskript innan du tar VM-ögonblicksbilden och sedan anropa efter skriptet när ögonblicksbildjobb VM har slutförts. Mer information finns i artikeln [programkonsekventa Linux VM säkerhetskopior](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

> [!NOTE]
> Azure Backup anropar bara den kund som skrivits av före och efter skript. Om förskript och efterskript köras, markerar Azure Backup återställningspunkten som programmet konsekvent. Kunden är dock slutgiltigt ansvariga för konsekvens för programmet när du använder anpassade skript.
>

I följande tabell beskrivs vilka typer av konsekvens och villkoren när de uppstår.

| Konsekvens | VSS-baserade | Förklaring och information |
| --- | --- | --- |
| Programkonsekvens |Ja för Windows|Programkonsekvens är perfekt för arbetsbelastningar som den ser till att:<ol><li> Den virtuella datorn *startar*. <li>Det finns *inte är skadad*. <li>Det finns *inga data går förlorade*.<li> Data är konsekventa till det program som använder dessa data genom att programmet vid tidpunkten för säkerhetskopieringen – med hjälp av VSS eller före/efter-skript.</ol> <li>*Windows-datorer*– de flesta Microsoft-arbetsbelastningar har VSS-skrivare som kör arbetsbelastningen-specifika åtgärder som rör datakonsekvens. Till exempel SQL Server VSS-skrivaren säkerställer skrivningar transaktionsloggfilen och till databasen, utförs korrekt. För IaaS Windows VM-säkerhetskopior om du vill skapa en programkonsekvent återställningspunkt måste säkerhetskopieringstillägget anropa VSS-arbetsflöde och slutför den innan du tar ögonblicksbilden för virtuell dator. Azure VM-ögonblicksbilden vara korrekt måste VSS-skrivare för alla Virtuella Azure-program Slutför samt. (Läs den [grunderna för VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) och gör en djupdykning i information om [hur det fungerar](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Virtuella Linux-datorer*-kunder kan köra [anpassade förskript och efterskript så programkonsekvens](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Filsystemskonsekvens |Ja – för Windows-baserade datorer |Det finns två scenarier där återställningspunkten kan vara *filsystemkonsekvent*:<ul><li>Säkerhetskopior av virtuella Linux-datorer i Azure, utan förproduktions-script/efter-script eller om förproduktions-script/efter-script misslyckades. <li>VSS-fel under säkerhetskopiering för Windows-datorer i Azure.</li></ul> I båda dessa fall är är det bästa att göra att se till att: <ol><li> Den virtuella datorn *startar*. <li>Det finns *inte är skadad*.<li>Det finns *inga data går förlorade*.</ol> Program måste implementera sina egna ”åtgärds-” mekanism för återställda data. |
| Kraschkonsekvens |Nej |Detta motsvarar en virtuell dator med en ”krasch” (via antingen en mjuka eller svårt reset). Kraschkonsekvens brukar hända när den virtuella Azure-datorn är avstängd vid tidpunkten för säkerhetskopieringen. En kraschkonsekvent återställningspunkt ger inga garantier kring konsekvenskontroll av data på lagringsmedium – antingen ur operativsystemet eller programmet. Endast de data som redan finns på disken vid tidpunkten för säkerhetskopieringen inhämtas och säkerhetskopieras. <br/> <br/> Det finns inga garantier, vanligtvis, operativsystemet startas, följt av disk-kontrollerar proceduren som chkdsk, åtgärda eventuella skadade-fel. Alla data i minnet eller skrivningar som inte har överförts till disken går förlorade. Programmet följer vanligtvis med en egen mekanism för verifiering om återställning av data som behöver göras. <br><br>Till exempel om transaktionsloggen har poster finns inte i databasen, samlar databasprogrammet tillbaka förrän data är konsekventa. När data fördelas mellan flera virtuella diskar (till exempel volymer), ger en kraschkonsekvent återställningspunkt inga garantier för data. |

## <a name="performance-and-resource-utilization"></a>Prestanda och Resursanvändning
Programvara för säkerhetskopiering som är distribuerade på plats, t.ex. bör du planera för kapacitet och resursanvändningen behov när du säkerhetskopierar virtuella datorer i Azure. Den [Azure Storage begränsar](../azure-subscription-service-limits.md#storage-limits) definiera hur du ska strukturera distribueringar av virtuella datorer för att få maximala prestanda med minimal inverkan på arbetsbelastningar som körs.

### <a name="number-of-disks"></a>Antal diskar
Säkerhetskopieringen försöker slutföra ett säkerhetskopieringsjobb så snabbt som möjligt. På så sätt kan förbrukar den så många resurser som möjligt. Men alla i/o-åtgärder begränsas av de *Måldataflöde för enskild Blob*, som har en gräns på 60 MB per sekund. Säkerhetskopieringen försöker säkerhetskopiera var och en av de Virtuella datorernas diskar i ett försök att maximera hastigheten *parallellt*. Om en virtuell dator har fyra diskar, försöker tjänsten att säkerhetskopiera alla fyra diskar parallellt. Den **antalet diskar** som säkerhetskopieras, är den viktigaste faktorn fastställa säkerhetskopieringstrafik för storage-konto.

### <a name="backup-schedule"></a>Schema för säkerhetskopiering
Ytterligare en faktor som påverkar prestanda är den **Säkerhetskopieringsschemat**. Om du konfigurerar principerna så att alla virtuella datorer säkerhetskopieras samtidigt, har du schemalagt en knipa trafik. Säkerhetskopieringen försöker att säkerhetskopiera alla diskar parallellt. Att minska säkerhetskopieringstrafik, säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen, utan överlappande.

## <a name="capacity-planning"></a>Kapacitetsplanering
Ladda ned den [VM backup kapacitetsplanering Excel-kalkylblad](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) se effekten av disk- och schema för säkerhetskopiering val.

### <a name="backup-throughput"></a>Säkerhetskopiering dataflöde
För varje disk som säkerhetskopieras, Azure Backup läser block på disken och lagrar bara de ändrade data (inkrementell säkerhetskopiering). I följande tabell visas de genomsnittliga värdena för Backup-tjänsten dataflöde. Du kan beräkna mängden tid som behövs för att säkerhetskopiera en hårddisk på en given storlek med hjälp av följande data.

| Säkerhetskopieringen | Bästa möjliga dataflöde |
| --- | --- |
| Den första säkerhetskopieringen |160 Mbit/s |
| Inkrementell säkerhetskopiering (DR) |640 Mbit/s <br><br> Dataflöde försämras betydligt om ändrade data (som måste säkerhetskopieras) är utspridda över disken.|

## <a name="total-vm-backup-time"></a>Total tid för säkerhetskopiering av virtuell dator
Även om de flesta av säkerhetskopieringstiden läggs på läsning och kopiering av data, andra åtgärder att bidra till den totala tiden som behövs för att säkerhetskopiera en virtuell dator:

* Tid som krävs för att [installera eller uppdatera säkerhetskopieringstillägget](backup-azure-arm-vms.md).
* Ögonblicksbild tiden, vilket är den tid det tar att utlösa en ögonblicksbild. Ögonblicksbilder utlöses nära den schemalagda tiden för säkerhetskopiering.
* Kötid. Eftersom Backup service processer jobb från flera kunder på samma gång, kanske ögonblicksbilddata inte omedelbart kopieras till Recovery Services-valvet. Vid Högbelastningstider belastning, kan det ta upp till åtta timmar innan säkerhetskopiorna som ska bearbetas. Total tid för VM-säkerhetskopiering är dock mindre än 24 timmar för principer för daglig säkerhetskopiering.
Total tid för säkerhetskopiering på mindre än 24 timmar är giltig för inkrementell säkerhetskopiering, men är inte avsett för den första säkerhetskopieringen. Första gången för säkerhetskopiering är proportionell och kan vara mer än 24 timmar beroende på storleken på data och när säkerhetskopieringen görs.
* Dataöverföringstid, tid som krävs för backup-tjänsten att beräkna de inkrementella ändringarna från tidigare säkerhetskopia och överför ändringarna till valvet lagring.

### <a name="why-are-backup-times-longer-than-12-hours"></a>Varför är säkerhetskopieringstider som är längre än 12 timmar?

Säkerhetskopieringen består av två faser: ta ögonblicksbilder och överföra ögonblicksbilderna till valvet. Backup-tjänsten optimerar för lagring. När du överför ögonblicksbilddata till ett valv, överför tjänsten endast inkrementella ändringar från tidigare ögonblicksbild.  Tjänsten beräknar kontrollsumma för block för att fastställa de inkrementella ändringarna. Om ett block ändras identifieras blocket som ett block som ska skickas till valvet. Sedan ytterligare tester för tjänsten i var och en av de identifierade blocken, söker efter möjligheter att minimera dataöverföring. När du har utvärderat alla ändrade block tjänsten slår samman ändringarna och skickar dem till valvet. I vissa äldre program är små, fragmenterade skrivningar inte optimala för lagring. Om ögonblicksbilden innehåller många små, fragmenterade skrivningar, är tjänsten extra tid för bearbetning av data som skrivits av programmen. För program som körs på den virtuella datorn, är det minsta rekommenderade programskrivningar blocket 8 KB. Om programmet använder ett block med mindre än 8 KB, sker prestanda vid säkerhetskopiering. Hjälp med justering ditt program för att förbättra prestanda vid säkerhetskopiering finns i [justering program för optimala prestanda med Azure storage](../virtual-machines/windows/premium-storage-performance.md). Även om artikeln på Säkerhetskopieringens prestanda använder Premium storage-exempel, gäller vägledningen för Standard storage-diskar.

## <a name="total-restore-time"></a>Totalt antal återställningstid

En återställningsåtgärd består av två huvudsakliga uppgifter: kopiera data från valvet till valda kundens lagringskonto och skapa den virtuella datorn. Den tid som behövs för att kopiera data från valvet beror på var säkerhetskopiorna som lagras i Azure och platsen för kundens lagringskonto. Åtgången tid för att kopiera data beror på:
* Kötid - eftersom tjänsten bearbetar återställa jobb från flera kunder på samma gång, återställning begärandena placeras i en kö.
* Kopiera data tid - Data kopieras från valvet till kundens lagringskonto. Återställ tiden beror på IOPS och dataflöde Azure Backup-tjänsten hämtar på valda kundens lagringskonto. Om du vill minska tid som kopiering under återställningsprocessen, Välj ett lagringskonto som inte lästs in med andra programskrivningar och läsningar.

## <a name="best-practices"></a>Bästa praxis

Vi rekommenderar att följande dessa metoder när du konfigurerar säkerhetskopieringar för alla virtuella datorer:

* Inte schemalägga säkerhetskopieringar för fler än 10 klassiska virtuella datorer från samma molntjänst på samma gång. Om du vill säkerhetskopiera flera virtuella datorer från samma molntjänst är en fristående säkerhetskopiering av en timme.
* Inte schemalägga säkerhetskopieringar för fler än 100 virtuella datorer från ett valv, på samma gång.
* Schemalägga säkerhetskopior av virtuella datorer vid låg belastning. Det här sättet Backup-tjänsten används IOPS för att överföra data från kundens lagringskonto till valvet.
* Se till att virtuella Linux-datorer aktiverad för säkerhetskopiering, har Python version 2.7 eller senare.

### <a name="best-practices-for-vms-with-unmanaged-disks"></a>Metodtips för virtuella datorer med ohanterade diskar

Följande rekommendationer gäller endast för virtuella datorer med ohanterade diskar. Om de virtuella datorerna använder hanterade diskar, hanterar Backup-tjänsten alla hanteringsaktiviteter för lagring.

* Se till att använda en princip för säkerhetskopiering för virtuella datorer som är fördelade på flera lagringskonton. Fler än 20 Totalt antal diskar från ett enda lagringskonto bör skyddas av samma schemat för säkerhetskopiering. Om du har större än 20 diskar i ett lagringskonto kan sprida dessa virtuella datorer över flera principer för att hämta nödvändiga IOPS under fasen för överföring av säkerhetskopieringen.
* Återställ inte en virtuell dator som körs på Premium-lagring till samma lagringskonto. Om åtgärden återställningsprocessen är i linje med säkerhetskopieringen, minskar tillgängliga IOPS för säkerhetskopiering.
* För virtuella datorer i Premium-säkerhetskopiering på VM-säkerhetskopieringsstack V1, bör du allokera högst 50% av det totala utrymmet för kontot så Backup-tjänsten kan kopiera ögonblicksbilden till storage-konto och överföra data från storage-kontot till valvet.


## <a name="data-encryption"></a>Datakryptering
Azure Backup krypterar inte data som en del av säkerhetskopieringen. Men du kan kryptera data i den virtuella datorn och säkerhetskopiera skyddade data smidigt (Läs mer om [säkerhetskopiering av krypterade data](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Beräkna kostnaden för skyddade instanser
Azure-datorer som säkerhetskopieras till Azure Backup är föremål för [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Skyddade instanser beräkningen baseras på den *faktiska* storleken på den virtuella datorn, vilket är summan av alla data på den virtuella datorn och exklusive tillfällig lagring.

Priserna för säkerhetskopiering av virtuella datorer baseras inte på den maximala storleken som stöds för varje datadisk som är kopplade till den virtuella datorn. Priserna baseras på den faktiska data som lagras i datadisken. På samma sätt baseras säkerhetskopieringslagring-faktura på mängden data som lagras i Azure Backup, vilket är summan av de faktiska data i varje återställningspunkt.

Till exempel ta en Standard A2 medelstora virtuell dator med två ytterligare datadiskar med en maximal storlek på 4 TB. Följande tabell innehåller de faktiska data som lagras på var och en av dessa diskar:

| Disktyp | Maxstorlek | Faktiska data finns |
| --------- | -------- | ----------- |
| Operativsystemdisk |4 095 GB |17 GB |
| Lokal disk / temporär disk |135 GB |5 GB (ingår inte för säkerhetskopiering) |
| Datadisk 1 |4 095 GB |30 GB |
| Datadisk 2 |4 095 GB |0 GB |

Den verkliga storleken hos den virtuella datorn är i det här fallet 17 GB + 30 GB + 0 GB = 47 GB. Den här storleken för skyddade instanser (47 GB) blir grund för månatliga faktura. När mängden data i den virtuella datorn växer kan används den skyddade instansen storleken för fakturering ändringar därefter.

Faktureringen startar inte förrän den första framgångsrika säkerhetskopieringen är klar. Nu startar faktureringen för både lagring och skyddade instanser. Fakturering fortsätter så länge det finns några säkerhetskopieringsdata som lagras i ett valv för den virtuella datorn. Om du stoppar skyddet på den virtuella datorn, men data för säkerhetskopiering av virtuella datorer finns i ett valv, fortsätter fakturering.

Fakturering för en angiven virtuell dator stoppar endast om skyddet har stoppats och tar bort alla säkerhetskopierade data. När skydd avslutades och det finns inga aktiva säkerhetskopieringsjobb, storleken på den senaste framgångsrika säkerhetskopieringen för virtuell dator blir skyddad instans storleken som används för den månatliga fakturan.

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
* [Säkerhetskopiera virtuella datorer](backup-azure-arm-vms.md)
* [Hantera säkerhetskopiering av virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-arm-restore-vms.md)
* [Felsöka problem med säkerhetskopiering-VM](backup-azure-vms-troubleshoot.md)
