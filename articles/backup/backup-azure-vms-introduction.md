---
title: Om Säkerhetskopiering av virtuella Azure-datorer
description: I den här artikeln kan du läsa om hur Azure Backup-tjänsten säkerhetskopierar Virtuella Azure-datorer och hur du följer metodtips.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415972"
---
# <a name="an-overview-of-azure-vm-backup"></a>En översikt över Azure VM-säkerhetskopiering

I den här artikeln beskrivs hur [Azure Backup-tjänsten](backup-introduction-to-azure-backup.md) säkerhetskopierar virtuella Azure-datorer (VMs).

Azure Backup tillhandahåller oberoende och isolerade säkerhetskopior för att skydda mot oavsiktlig förstörelse av data på dina virtuella datorer. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalning är enkla, säkerhetskopior är optimerade och du kan enkelt återställa efter behov.

Som en del av säkerhetskopieringsprocessen tas en [ögonblicksbild](#snapshot-creation)och data överförs till Recovery Services-valvet utan påverkan på produktionsarbetsbelastningar. Ögonblicksbilden ger olika nivåer av konsekvens, som beskrivs [här](#snapshot-consistency).

Azure Backup har också specialiserade erbjudanden för databasarbetsbelastningar som [SQL Server](backup-azure-sql-database.md) och [SAP HANA](sap-hana-db-about.md) som är arbetsbelastningsmedvetna, erbjuder 15 minuters RPO (återställningspunktsmål) och tillåter säkerhetskopiering och återställning av enskilda databaser.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Så här slutför Azure Backup en säkerhetskopia för virtuella Azure-datorer:

1. För virtuella Azure-datorer som har valts för säkerhetskopiering startar Azure Backup ett säkerhetskopieringsjobb enligt det säkerhetskopieringsschema du anger.
1. Under den första säkerhetskopieringen installeras ett säkerhetskopieringstillägg på den virtuella datorn om den virtuella datorn körs.
    - För virtuella Windows-datorer installeras [VMSnapshot-tillägget.](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows)
    - För virtuella Linux-datorer installeras [VMSnapshotLinux-tillägget.](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux)
1. För virtuella Windows-datorer som körs koordinerar säkerhetskopiering med VSS (Windows Volume Shadow Copy Service) för att ta en appkonsekvent ögonblicksbild av den virtuella datorn.
    - Som standard tar Säkerhetskopieringen fullständiga VSS-säkerhetskopior.
    - Om säkerhetskopiering inte kan ta en appkonsekvent ögonblicksbild, då det tar en fil-konsekvent ögonblicksbild av den underliggande lagringen (eftersom inga programskrivningar inträffar medan den virtuella datorn stoppas).
1. För virtuella Linux-datorer kräver säkerhetskopiering en filkonsekvent säkerhetskopiering. För appkonsekventa ögonblicksbilder måste du manuellt anpassa skript före/efter.
1. När säkerhetskopieringen har hämtat ögonblicksbilden överförs data till valvet.
    - Säkerhetskopian optimeras genom att säkerhetskopiera varje VM-disk parallellt.
    - För varje disk som säkerhetskopieras läser Azure Backup blocken på disken och identifierar och överför endast de datablock som har ändrats (delta) sedan den tidigare säkerhetskopian.
    - Ögonblicksbilddata kanske inte kopieras omedelbart till valvet. Det kan ta några timmar vid rusningstid. Den totala säkerhetskopieringstiden för en virtuell dator är mindre än 24 timmar för principer för daglig säkerhetskopiering.
1. Ändringar som görs i en Windows VM efter att Azure Backup har aktiverats på den är:
    - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 installeras i den virtuella datorn
    - Starttypen för VSS (Volume Shadow Copy Service) har ändrats till automatisk från manuell
    - IaaSVmProvider Windows-tjänsten läggs till

1. När dataöverföringen är klar tas ögonblicksbilden bort och en återställningspunkt skapas.

![Azure arkitektur för säkerhetskopiering av virtuella datorer](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Kryptering av Azure VM-säkerhetskopior

När du säkerhetskopierar virtuella Azure-datorer med Azure Backup krypteras virtuella datorer i vila med SSE (Storage Service Encryption). Azure Backup kan också säkerhetskopiera virtuella Azure-datorer som krypteras med hjälp av Azure Disk Encryption.

**Kryptering** | **Detaljer** | **Support**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption krypterar både operativsystem och datadiskar för virtuella Azure-datorer.<br/><br/> Azure Disk Encryption integreras med BitLocker-krypteringsnycklar (BEKs), som skyddas i ett nyckelvalv som hemligheter. Azure Disk Encryption integreras också med Azure Key Vault-nyckelkrypteringsnycklar (KEKs). | Azure Backup stöder säkerhetskopiering av hanterade och ohanterade virtuella Azure-datorer som endast är krypterade med BEKs eller med BEKs tillsammans med KEKs.<br/><br/> Både BEKs och KEKs säkerhetskopieras och krypteras.<br/><br/> Eftersom KEKs och BEKs säkerhetskopieras kan användare med nödvändiga behörigheter återställa nycklar och hemligheter tillbaka till nyckelvalvet om det behövs. Dessa användare kan också återställa den krypterade virtuella datorn.<br/><br/> Krypterade nycklar och hemligheter kan inte läsas av obehöriga användare eller av Azure.
**Sse** | Med SSE tillhandahåller Azure Storage kryptering i vila genom att automatiskt kryptera data innan du lagrar den. Azure Storage dekrypterar också data innan du hämtar dem. | Azure Backup använder SSE för kryptering i vila av virtuella Azure-datorer.

För hanterade och ohanterade virtuella Azure-datorer stöder säkerhetskopiering både virtuella datorer som är krypterade med endast BEK:er eller virtuella datorer krypterade med BEKs tillsammans med KEKs.

De säkerhetskopierade BEKs (hemligheterna) och KEK:erna (nycklar) är krypterade. De kan bara läsas och användas när de återställs tillbaka till nyckelvalvet av behöriga användare. Varken obehöriga användare eller Azure kan läsa eller använda säkerhetskopierade nycklar eller hemligheter.

BEKs säkerhetskopieras också. Så, om BEKs går förlorade, behöriga användare kan återställa BEKs till nyckeln valv och återställa krypterade virtuella datorer. Endast användare med den nödvändiga behörighetsnivån kan säkerhetskopiera och återställa krypterade virtuella datorer eller nycklar och hemligheter.

## <a name="snapshot-creation"></a>Skapa ögonblicksbilder

Azure Backup tar ögonblicksbilder enligt säkerhetskopieringsschemat.

- **Virtuella datorer i Windows:** För virtuella Windows-datorer koordinerar säkerhetskopieringstjänsten med VSS för att ta en appkonsekvent ögonblicksbild av VM-diskarna.  Som standard tar Azure Backup en fullständig VSS-säkerhetskopiering (det trunkerar loggarna för program som SQL Server vid tidpunkten för säkerhetskopiering för att få konsekvent säkerhetskopiering på programnivå).  Om du använder en SQL Server-databas för azure VM-säkerhetskopiering kan du ändra inställningen för att ta en VSS-kopia-säkerhetskopia (för att bevara loggar). Mer information finns i [den här artikeln](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **Virtuella Linux-datorer:** Om du vill ta appkonsekventa ögonblicksbilder av virtuella Linux-datorer använder du Linux-ramverket för förskript och efterskript för att skriva egna anpassade skript för att säkerställa konsekvens.

  - Azure Backup anropar endast pre/post-skript som du har skrivit.
  - Om förskripten och postskripten körs korrekt markerar Azure Backup återställningspunkten som programkonsekvent. Men när du använder anpassade skript är du ytterst ansvarig för programmets konsekvens.
  - [Läs mer](backup-azure-linux-app-consistent.md) om hur du konfigurerar skript.

## <a name="snapshot-consistency"></a>Konsekvens för ögonblicksbilder

I följande tabell beskrivs de olika typerna av ögonblicksbildkonsekvens:

**Ögonblicksbild** | **Detaljer** | **Återställning** | **Övervägande**
--- | --- | --- | ---
**Programkonsekvent** | Appkonsekventa säkerhetskopior samlar in minnesinnehåll och väntande I/O-åtgärder. Appkonsekventa ögonblicksbilder använder en VSS-skrivare (eller pre/post-skript för Linux) för att säkerställa konsekvensen i appdata innan en säkerhetskopia sker. | När du återställer en virtuell dator med en appkonsekvent ögonblicksbild startar den virtuella datorn. Det finns inga data korruption eller förlust. Apparna startar i ett konsekvent tillstånd. | Windows: Alla VSS-författare lyckades<br/><br/> Linux: För/post-skript är konfigurerade och lyckades
**Filsystemet konsekvent** | Konsekventa säkerhetskopior av filsystemet ger konsekvens genom att ta en ögonblicksbild av alla filer samtidigt.<br/><br/> | När du återställer en virtuell dator med en konsekvent ögonblicksbild av filsystemet startar den virtuella datorn. Det finns inga data korruption eller förlust. Appar måste implementera sin egen "fix-up"-mekanism för att se till att återställda data är konsekventa. | Windows: Vissa VSS-författare misslyckades <br/><br/> Linux: Standard (om skript före/efter inte är konfigurerade eller misslyckade)
**Kraschkonsekvent** | Kraschkonsekventa ögonblicksbilder inträffar vanligtvis om en Azure-dator stängs av vid säkerhetskopieringen. Endast de data som redan finns på disken vid säkerhetskopieringen fångas in och säkerhetskopieras. | Startar med vm-startprocessen följt av en diskkontroll för att åtgärda fel vid skador. Alla minnesdata eller skrivåtgärder som inte har överförts till disken innan kraschen förlorades. Appar implementerar sin egen dataverifiering. En databasapp kan till exempel använda transaktionsloggen för verifiering. Om transaktionsloggen har poster som inte finns i databasen, återställer databasprogramvaran transaktioner tillbaka tills data är konsekventa. | VM är i avstängning (stoppad/ deallocated) tillstånd.

## <a name="backup-and-restore-considerations"></a>Säkerhetskopiera och återställa överväganden

**Övervägande** | **Detaljer**
--- | ---
**Disk** | Säkerhetskopiering av VM-diskar är parallell. Om en virtuell dator till exempel har fyra diskar försöker säkerhetskopieringstjänsten säkerhetskopiera alla fyra diskarna parallellt. Säkerhetskopieringen är inkrementell (endast ändrade data).
**Schemaläggning** |  För att minska säkerhetskopieringstrafiken säkerhetskopierar du olika virtuella datorer vid olika tidpunkter på dagen och ser till att tiderna inte överlappar varandra. Säkerhetskopiering av virtuella datorer samtidigt orsakar trafikstockningar.
**Förbereda säkerhetskopior** | Tänk på hur mycket tid det behövs för att förbereda säkerhetskopian. Förberedelsetiden inkluderar installation eller uppdatering av säkerhetskopieringstillägget och utlösa en ögonblicksbild enligt säkerhetskopieringsschemat.
**Överföring av data** | Tänk på den tid som krävs för Azure Backup för att identifiera de inkrementella ändringarna från den tidigare säkerhetskopian.<br/><br/> I en inkrementell säkerhetskopia bestämmer Azure Backup ändringarna genom att beräkna kontrollsumman för blocket. Om ett block ändras markeras det för överföring till valvet. Tjänsten analyserar de identifierade blocken för att försöka minimera mängden data som ska överföras ytterligare. När du har utvärderat alla ändrade block överför Azure Backup ändringarna till valvet.<br/><br/> Det kan finnas en fördröjning mellan att ta ögonblicksbilden och kopiera den till valvet.<br/><br/> Vid rusningstid kan det ta upp till åtta timmar innan säkerhetskopior bearbetas. Säkerhetskopieringstiden för en virtuell dator kommer att vara mindre än 24 timmar för den dagliga säkerhetskopieringen.
**Den första säkerhetskopieringen** | Även om den totala säkerhetskopieringstiden för inkrementella säkerhetskopior är mindre än 24 timmar, kanske det inte är fallet för den första säkerhetskopieringen. Den tid som behövs för den första säkerhetskopieringen beror på storleken på data och när säkerhetskopian bearbetas.
**Återställ kö** | Azure Backup processer återställa jobb från flera lagringskonton samtidigt, och det sätter återställningsbegäranden i en kö.
**Återställa kopia** | Under återställningen kopieras data från valvet till lagringskontot.<br/><br/> Den totala återställningstiden beror på I/O-åtgärder per sekund (IOPS) och dataflödet för lagringskontot.<br/><br/> Om du vill minska kopieringstiden väljer du ett lagringskonto som inte är inläst med andra programskrivningar och läsningar.

### <a name="backup-performance"></a>Prestanda för säkerhetskopiering

Dessa vanliga scenarier kan påverka den totala säkerhetskopieringstiden:

- **Lägga till en ny disk i en skyddad Virtuell Azure:Adding a new disk to a protected Azure VM:** Om en virtuell dator genomgår inkrementell säkerhetskopiering och en ny disk läggs till, kommer säkerhetskopieringstiden att öka. Den totala säkerhetskopieringstiden kan vara mer än 24 timmar på grund av den första replikeringen av den nya disken, tillsammans med deltareplikering av befintliga diskar.
- **Fragmenterade diskar:** Säkerhetskopieringsåtgärder är snabbare när diskändringarna är sammanhängande. Om ändringarna sprids ut och fragmenteras över en disk blir säkerhetskopieringen långsammare.
- **Disk omsättning:** Om skyddade diskar som genomgår inkrementell säkerhetskopiering har en daglig omsättning på mer än 200 GB kan säkerhetskopieringen ta lång tid (mer än åtta timmar) att slutföra.
- **Backup versioner:** Den senaste versionen av Säkerhetskopiering (kallas Instant Restore-versionen) använder en mer optimerad process än jämförelse av kontrollsumma för att identifiera ändringar. Men om du använder Omedelbar återställning och har tagit bort en ögonblicksbild av säkerhetskopian växlar säkerhetskopian till jämförelse av kontrollsumman. I det här fallet kommer säkerhetskopieringen att överstiga 24 timmar (eller misslyckas).

## <a name="best-practices"></a>Bästa praxis

När du konfigurerar vm-säkerhetskopior föreslår vi följande metoder:

- Ändra standardschematiderna som anges i en princip. Om till exempel standardtiden i principen är 12:00 ökar du tidsinställningen med flera minuter så att resurserna används optimalt.
- Om du återställer virtuella datorer från ett enda valv rekommenderar vi starkt att du använder olika [virtuella v2-lagringskonton](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) för att säkerställa att mållagringskontot inte begränsas. Varje virtuell dator måste till exempel ha ett annat lagringskonto. Om till exempel 10 virtuella datorer återställs använder du 10 olika lagringskonton.
- För säkerhetskopiering av virtuella datorer som använder premiumlagring, med Omedelbar återställning, rekommenderar vi att du allokerar *50 %* ledigt utrymme för det totala tilldelade lagringsutrymmet, vilket **endast** krävs för den första säkerhetskopian. Det 50 % lediga utrymmet är inte ett krav för säkerhetskopior när den första säkerhetskopieringen är klar
- Gränsen för antalet diskar per lagringskonto är relativ till hur mycket diskarna används av program som körs på en infrastruktur som en tjänst (IaaS) VM. Om det finns 5 till 10 diskar eller fler på ett enda lagringskonto, balanserar du belastningen genom att flytta vissa diskar till separata lagringskonton.

## <a name="backup-costs"></a>Kostnader för säkerhetskopiering

Virtuella Azure-datorer som säkerhetskopieras med Azure Backup omfattas av [prissättningen för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Faktureringen startar inte förrän den första lyckade säkerhetskopian är klar. Nu börjar faktureringen för både lagring och skyddade virtuella datorer. Faktureringen fortsätter så länge som säkerhetskopieringsdata för den virtuella datorn lagras i ett valv. Om du stoppar skyddet för en virtuell dator, men säkerhetskopieringsdata för den virtuella datorn finns i ett valv, fortsätter faktureringen.

Fakturering för en angiven virtuell dator stoppas endast om skyddet stoppas och alla säkerhetskopierade data tas bort. När skyddet stoppas och det inte finns några aktiva säkerhetskopieringsjobb blir storleken på den senaste lyckadesa vm-säkerhetskopieringen den skyddade instansstorleken som används för den månatliga fakturan.

Beräkningen av storleken för skyddad instans baseras på den virtuella datorns *faktiska* storlek. Den virtuella datorns storlek är summan av alla data i den virtuella datorn, exklusive den tillfälliga lagringen. Prissättningen baseras på de faktiska data som lagras på datadiskarna, inte på den maximala storleken som stöds för varje datadisk som är kopplad till den virtuella datorn.

På samma sätt baseras lagringsräkningen för säkerhetskopiering på mängden data som lagras i Azure Backup, vilket är summan av de faktiska data i varje återställningspunkt.

Ta till exempel en virtuell dator med A2-standardstorlek som har två ytterligare datadiskar med en maximal storlek på 32 TB vardera. I följande tabell visas de faktiska data som lagras på var och en av dessa diskar:

**Disk** | **Max storlek** | **Faktiska data finns**
--- | --- | ---
OS-disk | 32 TB | 17 GB
Lokal/tillfällig disk | 135 GB | 5 GB (ingår ej för säkerhetskopiering)
Datadisk 1 | 32 TB| 30 GB
Datadisk 2 | 32 TB | 0 GB

Den faktiska storleken på den virtuella datorn i det här fallet är 17 GB + 30 GB + 0 GB = 47 GB. Den här storleken på skyddade instanser (47 GB) blir grunden för månadsräkningen. När mängden data i den virtuella datorn växer, ändras den skyddade instansstorleken som används för fakturering för att matcha.

## <a name="next-steps"></a>Nästa steg

Förbered [nu för Azure VM-säkerhetskopiering](backup-azure-arm-vms-prepare.md).
