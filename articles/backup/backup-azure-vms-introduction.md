---
title: Planera din infrastruktur för VM-säkerhetskopiering i Azure | Microsoft Docs
description: Viktiga överväganden när du planerar att säkerhetskopiera virtuella datorer i Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Säkerhetskopiera virtuella datorer, säkerhetskopiera virtuella datorer
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/23/2018
ms.author: markgal;trinadhk;sogup
ms.openlocfilehash: 299794b100ed438de2995d70419025dd686d2278
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Planera din infrastruktur för VM-säkerhetskopiering i Azure
Den här artikeln innehåller prestanda och resursen förslag på hur du planerar din infrastruktur för säkerhetskopiering av VM. Den definierar även viktiga aspekter av säkerhetskopieringstjänsten; dessa aspekter kan vara avgörande för att fastställa din arkitektur kapacitetsplanering och schemaläggning. Om du har [förbereda din miljö](backup-azure-arm-vms-prepare.md), planering är nästa steg innan du börjar [att säkerhetskopiera virtuella datorer](backup-azure-arm-vms.md). Om du behöver mer information om virtuella Azure-datorer finns i [virtuella datorer dokumentationen](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>Hur fungerar Azure säkerhetskopiera virtuella datorer?
När tjänsten Azure Backup initierar en säkerhetskopiering på den schemalagda tiden tjänsten triggers reservanknytning att ta en ögonblicksbild i tidpunkt. Azure Backup-tjänsten används den _VMSnapshot_ tillägget i Windows, och _VMSnapshotLinux_ tillägget Linux. Tillägget installeras under den första säkerhetskopieringen. Om du vill installera tillägget måste du köra den virtuella datorn. Om den virtuella datorn inte körs tar Backup-tjänsten en ögonblicksbild av snapshot det underliggande lagringsutrymmet (eftersom ingen programskrivning medan den virtuella datorn stoppas).

När en ögonblicksbild av virtuella Windows-datorer, samordnar Backup-tjänsten med Volume Shadow Copy Service (VSS) att hämta en programkonsekvent ögonblicksbild av den virtuella datorns diskar. Om du säkerhetskopierar virtuella Linux-datorer, kan du skriva egna skript för att säkerställa konsekvens när en VM-ögonblicksbild. Information om att aktivera dessa skript finns senare i den här artikeln.

När Azure Backup-tjänsten har tagit ögonblicksbilden överförs data till valvet. För att maximera effektiviteten identifierar och överför tjänsten endast de datablock som har ändrats sedan föregående säkerhetskopia.

![Arkitektur för virtuell dator i Azure-säkerhetskopiering](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

> [!NOTE]
> 1. Under säkerhetskopieringen innehåller Azure Backup inte den tillfälliga disk som är ansluten till den virtuella datorn. Mer information finns i bloggen på [tillfällig lagring](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Azure Backup tar en lagringsnivå ögonblicksbilden och överför denna ögonblicksbild till valvet, ändras inte lagringskontonycklarna tills jobbet har slutförts.
> 3. För virtuella datorer premium säkerhetskopior Azure ögonblicksbilden till lagringskontot. Detta är att se till att tjänsten Backup använder tillräckligt IOPS för överföring av data till valvet. Den här ytterligare en kopia av lagring debiteras enligt den virtuella datorn allokerade storleken. 
>

### <a name="data-consistency"></a>Datakonsekvens
Säkerhetskopiera och återställa företag kritiska data komplicerade av att affärskritiska data måste säkerhetskopieras när de program som ger data körs. För att lösa det Azure Backup har stöd för programkonsekvent säkerhetskopiering för både Windows- och Linux virtuella datorer
#### <a name="windows-vm"></a>Virtuell Windows-dator
Azure Backup tar Fullständig VSS-säkerhetskopiering på virtuella Windows-datorer (Läs mer om [Fullständig VSS-säkerhetskopiering](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Följande registernyckel måste anges för den virtuella datorn om du vill aktivera VSS-säkerhetskopior.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Virtuella Linux-datorer
Azure Backup är ett ramverk för skript. För att garantera programmets konsekvens när du säkerhetskopierar virtuella Linux-datorer, skapa skript före och efter skript som styr säkerhetskopiering arbetsflödet och miljö. Azure-säkerhetskopiering anropar skriptet före innan VM-ögonblicksbild och anropar efter skriptet när jobbet för VM-ögonblicksbild har slutförts. Mer information finns i [programmet konsekvent VM säkerhetskopior med hjälp av skript före och efter skriptet](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).
> [!NOTE]
> Azure-säkerhetskopiering endast anropar den customer skrivna före och efter skript. Om skript före och efter skript köras, markerar Azure Backup återställningspunkten som programmet konsekvent. Kunden är slutgiltigt ansvariga för konsekvens för programmet när du använder anpassade skript.
>


Den här tabellen beskrivs typerna av konsekvens och de villkor som de sker under Azure VM säkerhetskopiera och återställa.

| Konsekvens | VSS-baserade | Förklaring och information |
| --- | --- | --- |
| Programkonsekvens |Ja för Windows|Programkonsekvens är idealisk för arbetsbelastningar som det säkerställer att:<ol><li> Den virtuella datorn *startar*. <li>Det finns *inte är skadad*. <li>Det finns *ingen dataförlust*.<li> Data är konsekventa till program som använder data, genom att programmet vid tidpunkten för säkerhetskopiering – med hjälp av VSS eller före/post skript.</ol> <li>*Virtuella Windows-datorer*-mest Microsoft-arbetsbelastningar har VSS-skrivare som gör belastningsspecifikt åtgärder som rör datakonsekvens. Till exempel har Microsoft SQL Server en VSS-skrivare som garanterar att skrivningar till transaktionsloggfilen och databasen utförs korrekt. För Virtuella för Windows Azure-säkerhetskopior om du vill skapa en programkonsekvent återställningspunkt måste säkerhetskopiering tillägget anropa VSS-arbetsflöde och slutför den innan VM-ögonblicksbild. För en Azure VM-ögonblicksbild vara korrekt VSS-skrivare för alla Virtuella Azure-program måste du slutföra samt. (Läs den [grunderna i VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) och fördjupa dig djupare i information om [hur det fungerar](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Virtuella Linux-datorer*-kunder kan köra [anpassat skript för före och efter skript för att garantera programmets konsekvens](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Filsystemskonsekvens |Ja – för Windows-baserade datorer |Det finns två scenarier där återställningspunkt kan vara *konsekvent filsystemet*:<ul><li>Säkerhetskopior av virtuella Linux-datorer i Azure, utan Förproduktion-script/efter-script eller om Förproduktion-script/efter-script misslyckades. <li>VSS-fel vid säkerhetskopiering för virtuella Windows-datorer i Azure.</li></ul> I båda dessa fall är det bäst att göra så att: <ol><li> Den virtuella datorn *startar*. <li>Det finns *inte är skadad*.<li>Det finns *ingen dataförlust*.</ol> Program måste implementera sina egna ”korrigering av” mekanism för återställda data. |
| Kraschkonsekvens |Nej |Den här situationen är detsamma som en virtuell dator som har en ”krasch” (via antingen en mjuk eller hård reset). Kraschkonsekvens inträffar oftast när den virtuella Azure-datorn är avstängd vid tidpunkten för säkerhetskopiering. En kraschkonsekvent återställningspunkt ger inga garantier runt konsekvenskontroll av data på lagringsmedium--ur operativsystemet eller programmet. Endast de data som finns på disken redan vid tidpunkten för säkerhetskopieringen fångas och säkerhetskopieras. <br/> <br/> Det finns inga garantier, vanligtvis, operativsystemet startas, följt av disk-kontrollerar proceduren som chkdsk, åtgärda eventuella skador fel. Alla data i minnet eller skrivningar som inte har överförts till disken går förlorade. Programmet följer vanligen med sin egen verifiering om återställning av data som behöver göras. <br><br>Exempelvis om transaktionsloggen har poster finns inte i databasen, samlar databasens programvara tillbaka förrän data är konsekventa. När data sprids över flera virtuella diskar (till exempel volymer), ger inga garantier för data i en kraschkonsekvent återställningspunkt. |

## <a name="performance-and-resource-utilization"></a>Prestanda och Resursanvändning
Som säkerhetskopieringsprogram som är distribuerade på plats bör du planera för kapacitet och resursanvändningen behov när du säkerhetskopierar virtuella datorer i Azure. Den [Azure Storage begränsar](../azure-subscription-service-limits.md#storage-limits) definiera hur du struktur distribueringar av Virtuella datorer för att få högsta prestanda med minimal påverkan på arbetsbelastningar som körs.

När du planerar prestanda vid säkerhetskopiering kan du vara uppmärksam på följande begränsningar i Azure Storage:

* Maximalt antal utgående per lagringskonto
* Totalt antal förfrågningar per lagringskonto

### <a name="storage-account-limits"></a>Lagringskontogränser
Säkerhetskopierade data kopieras från ett lagringskonto, lägger till i/o-åtgärder per sekund (IOPS) och utgående trafik (eller genomströmning) mätvärden för lagringskontot. På samma gång förbrukar virtuella datorer också IOPS och genomflöde. Målet är att säkerställa säkerhetskopierings- och trafik för virtuella datorer som inte överskrider din lagringskontogränser.

### <a name="number-of-disks"></a>Antal diskar
Säkerhetskopieringen försöker slutföra ett säkerhetskopieringsjobb så snabbt som möjligt. På så sätt kan förbrukar den så många resurser som möjligt. Men alla i/o-åtgärder begränsas av den *mål genomströmning för enda Blob*, som har högst 60 MB per sekund. Säkerhetskopieringen försöker säkerhetskopiera var och en av de Virtuella diskar i ett försök att maximera hastigheten *parallellt*. Om en virtuell dator har fyra diskar, försöker tjänsten att säkerhetskopiera alla fyra diskar parallellt. Den **antal diskar** säkerhetskopieras, är den viktigaste faktorn för att fastställa säkerhetskopiering lagringstrafik för kontot.

### <a name="backup-schedule"></a>Schemat för säkerhetskopiering
Ytterligare en faktor som påverkar prestanda är den **Säkerhetskopieringsschemat**. Om du konfigurerar principerna så att alla virtuella datorer säkerhetskopieras samtidigt, har du schemalagt en trafik har fastnat. Säkerhetskopieringen försöker att säkerhetskopiera alla diskar parallellt. För att minska säkerhetskopiering trafiken från ett lagringskonto kan du säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen, utan överlappande.

## <a name="capacity-planning"></a>Kapacitetsplanering
Kombinera faktorerna som tidigare måste du planera för lagringsbehov för användning av kontot. Hämta den [VM säkerhetskopiering kapacitetsplanering Excel-kalkylblad](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) att se effekten av disk- och alternativ för schemat för säkerhetskopiering.

### <a name="backup-throughput"></a>Säkerhetskopiering genomflöde
För varje disk som ska säkerhetskopieras, Azure Backup läser block på disken och lagrar bara de ändrade data (inkrementell säkerhetskopiering). I följande tabell visas genomsnittsvärden för säkerhetskopiering service genomflöde. Med följande data kan beräkna du hur lång tid som behövs för att säkerhetskopiera en disk för en given storlek.

| Säkerhetskopieringen | Bästa genomflöde |
| --- | --- |
| Den första säkerhetskopieringen |160 Mbit/s |
| Inkrementell säkerhetskopiering (DR) |640 Mbit/s <br><br> Genomströmning sjunker om de ändrade data (som behöver säkerhetskopieras) är utspridda över disken.|

## <a name="total-vm-backup-time"></a>Total tid för säkerhetskopiering av VM
När säkerhetskopieringen oftast används till att läsa och kopiera data, andra åtgärder att bidra till den totala tiden som krävs för att säkerhetskopiera en virtuell dator:

* Tid som krävs för att [installera eller uppdatera säkerhetskopiering tillägget](backup-azure-arm-vms.md).
* Snapshot-tid, vilket är den tid det tar att utlösa en ögonblicksbild. Ögonblicksbilder utlöses nära schemalagd tid för säkerhetskopiering.
* Kötid. Eftersom Backup-tjänsten bearbetar säkerhetskopior från flera kunder, kanske kopiera säkerhetskopierade data från ögonblicksbilden till säkerhetskopiering eller Recovery Services-valvet inte startar omedelbart. Läsa i tidpunkter för belastning, Väntetid kan sträcka ut på grund av hur många säkerhetskopior som bearbetas av upp till åtta timmar. Total tid för VM-säkerhetskopiering är dock mindre än 24 timmar för principer för daglig säkerhetskopiering. <br>
**Det innehåller giltig endast för inkrementella säkerhetskopieringar och inte för den första säkerhetskopieringen. Tid för första säkerhetskopiering är proportionell och kan vara större än 24 timmar, beroende på storleken på data och gång säkerhetskopieringen utförs.**
* Dataöverföringstid, tid som krävs för säkerhetskopieringstjänsten för att beräkna inkrementella ändringar från tidigare säkerhetskopia och överför ändringarna till valvet lagring.

### <a name="why-am-i-observing-longer12-hours-backup-time"></a>Varför jag sett longer(>12 hours) Säkerhetskopiera tid?
Säkerhetskopiering består av två faser: ta ögonblicksbilder och överföra ögonblicksbilder till valvet. Backup-tjänsten optimerar för lagring. När du överför ögonblicksbilddata till ett valv, överför tjänsten endast inkrementella ändringar från tidigare ögonblicksbild.  För att fastställa inkrementella ändringar beräknar tjänsten kontrollsumma för blocken. Om ett block ändras identifieras blocket som ett block ska skickas till valvet. Sedan ytterligare service flyttar till var och en av de identifiera blocken vill minimera att överföra data. Efter utvärdering av alla ändrade block tjänsten slår samman ändringar och skickar dem till valvet. I vissa äldre program är liten, fragmenterade skrivningar inte optimalt för lagring. Om ögonblicksbilden innehåller många små, fragmenterade skrivningar, använder tjänsten extra tid för bearbetning av data som skrivits av programmen. Den rekommenderade skrivning från Azure, för program som körs på den virtuella datorn är minst 8 KB. Om programmet använder ett block med mindre än 8 KB, påverkas Säkerhetskopieringens prestanda. Hjälp med att aktivera tillämpningsprogrammet för att förbättra prestanda vid säkerhetskopiering, se [justera program för optimala prestanda med Azure storage](../virtual-machines/windows/premium-storage-performance.md). Men artikel om säkerhetskopieringsprestanda används Premium storage exempel, gäller riktlinjer som för standardlagring diskar.

## <a name="total-restore-time"></a>Totalt antal återställningstid
En återställningsåtgärd består av två huvudsakliga sub aktiviteter: kopiera data från valvet till det valda kunden storage-kontot och skapa den virtuella datorn. Kopiera data från valvet beror på där säkerhetskopiorna lagras internt i Azure och där kunden storage-konto lagras. Åtgången tid för att kopiera data beror på:
* Kötid - eftersom tjänsten bearbetar återställningsjobb från flera kunder samtidigt, restore-begäranden är placeras i en kö.
* Kopiera data tid - Data kopieras från valvet till kunden storage-konto. Återställa tiden beror på IOPS och genomströmning Azure Backup-tjänsten hämtar för det valda kunden storage-kontot. Välj ett lagringskonto som inte lästs in med andra program skrivningar och läsningar för att minska den kopiera tiden under återställningsprocessen.

## <a name="best-practices"></a>Bästa praxis
Vi rekommenderar följande dessa metoder när du konfigurerar säkerhetskopieringar för virtuella datorer:

* Inte schemalägga fler än 10 klassiska virtuella datorer från samma Molntjänsten att säkerhetskopiera på samma gång. Om du vill säkerhetskopiera flera virtuella datorer från samma molntjänst är en fristående säkerhetskopiering av en timme.
* Schemalägg inte fler än 40 VMs att säkerhetskopiera på samma gång.
* Schemalägga VM säkerhetskopior vid låg belastning. Det här sättet Backup-tjänsten använder IOPS för att överföra data från customer storage-konto till valvet.
* Se till att en principen tillämpas på virtuella datorer som är fördelade på olika lagringskonton. Vi rekommenderar högst 20 totala diskar från en enda lagringskonto skyddas av samma schemat för säkerhetskopiering. Om du har större än 20 diskar i ett lagringskonto kan sprida dessa virtuella datorer över flera principer för att hämta nödvändiga IOPS under fasen överföring av säkerhetskopieringen.
* Återställ inte en virtuell dator som körs på Premium-lagring till samma lagringskonto. Om åtgärden återställningsprocessen sammanfaller med säkerhetskopieringen, minskar antalet IOPS som är tillgängliga för säkerhetskopiering.
* Se till att lagringskontot att värdar premiumdiskar har minst 50% ledigt utrymme för mellanlagring ögonblicksbild för en lyckad säkerhetskopia för Premium VM-säkerhetskopiering. 
* Kontrollera att python-version på virtuella Linux-datorer aktiverad för säkerhetskopiering är 2.7

## <a name="data-encryption"></a>Datakryptering
Azure-säkerhetskopiering Kryptera inte data som en del av säkerhetskopieringen. Men du kan kryptera data i den virtuella datorn och säkerhetskopiera skyddade data sömlöst (Läs mer om [säkerhetskopiering av krypterade data](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Beräkna kostnaden för skyddade instanser
Virtuella Azure-datorer som säkerhetskopieras via Azure Backup är föremål för [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Skyddade instanser beräkningen baseras på den *faktiska* storlek för den virtuella datorn, vilket är summan av alla data i den virtuella datorn och exklusive tillfällig lagring.

Priser för att säkerhetskopiera virtuella datorer baseras inte på den maximala storleken som stöds för varje datadisk som är ansluten till den virtuella datorn. Priser baseras på faktiska data som lagras i datadisk. Växeln lagring för säkerhetskopiering är på samma sätt, baserat på mängden data som lagras i Azure Backup, vilket är summan av den faktiska data i varje återställningspunkt.

Till exempel ta en Standard A2-storlek virtuell dator med två ytterligare hårddiskar med en maximal storlek på 1 TB. Följande tabell innehåller de faktiska data som lagras på var och en av dessa diskar:

| Disktyp | Maxstorlek | Faktiska data finns |
| --------- | -------- | ----------- |
| Operativsystemdisk |1 023 GB |17 GB |
| Lokal disk / tillfälliga disk |135 GB |5 GB (ingår inte för säkerhetskopiering) |
| Datadisk 1 |1 023 GB |30 GB |
| Datadisk 2 |1 023 GB |0 GB |

Den verkliga storleken på den virtuella datorn är i det här fallet 17 GB + 30 GB + 0 GB = 47 GB. Den här skyddade instansstorleken (47 GB) blir till grund för den månatliga fakturan. När mängden data i den virtuella datorn växer används skyddade instansstorleken för fakturering ändringar därefter.

Faktureringen startar inte förrän den första lycka säkerhetskopieringen har slutförts. Nu inleds faktureringen för både lagring och skyddade instanser. Fakturering fortsätter så länge det finns säkerhetskopierade data lagras i ett valv för den virtuella datorn. Om du stoppar skydd på den virtuella datorn, men säkerhetskopierade data för virtuella datorer finns i ett valv, fortsätter fakturering.

Fakturering för en angiven virtuell dator stoppar om skyddet har stoppats och alla säkerhetskopierade data tas bort. När skydd stanna och det finns inga aktiva jobb för säkerhetskopiering, storleken på den senaste lyckade säkerhetskopieringen blir skyddade-instansstorleken används för varje månad.

## <a name="questions"></a>Har du några frågor?
Om du har frågor eller om du saknar en funktion är du välkommen att [lämna feedback](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Nästa steg
* [Säkerhetskopiera virtuella datorer](backup-azure-arm-vms.md)
* [Hantera säkerhetskopiering av virtuella datorer](backup-azure-manage-vms.md)
* [Återställa virtuella datorer](backup-azure-arm-restore-vms.md)
* [Felsökning av problem med backup VM](backup-azure-vms-troubleshoot.md)
