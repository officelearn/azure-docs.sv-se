---
title: Supportmatris för säkerhetskopiering av virtuella Azure-datorer
description: Ger en sammanfattning av supportinställningar och begränsningar när du säkerhetskopierar virtuella Azure-datorer med Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: d86ce94c62ec9f25b364e9fdc963e3043b274722
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389298"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Supportmatris för säkerhetskopiering av virtuella Azure-datorer

Du kan använda [Azure Backup-tjänsten](backup-overview.md) för att säkerhetskopiera lokala datorer och arbetsbelastningar och virtuella Azure-datorer (VIRTUELLA datorer). Den här artikeln sammanfattar supportinställningar och begränsningar när du säkerhetskopierar virtuella Azure-datorer med Azure Backup.

Andra supportmatriser:

- [Allmän supportmatris](backup-support-matrix.md) för Azure Backup
- [Stödmatris](backup-support-matrix-mabs-dpm.md) för säkerhetskopiering av Azure Backup server/System Center Data Protection Manager (DPM)
- [Stödmatris](backup-support-matrix-mars-agent.md) för säkerhetskopiering med MARS-agenten (Microsoft Azure Recovery Services)

## <a name="supported-scenarios"></a>Scenarier som stöds

Så här säkerhetskopierar och återställer du virtuella Azure-datorer med Azure Backup-tjänsten.

**Scenario** | **Säkerhetskopiering** | **Agent** |**Återställa**
--- | --- | --- | ---
Direkt säkerhetskopiering av virtuella Azure-datorer  | Säkerhetskopiera hela den virtuella datorn.  | Ingen ytterligare agent behövs på Den virtuella Azure-datorn. Azure Backup installerar och använder ett tillägg till [Azure VM-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) som körs på den virtuella datorn. | Återställ enligt följande:<br/><br/> - **Skapa en grundläggande virtuell dator**. Detta är användbart om den virtuella datorn inte har någon särskild konfiguration, till exempel flera IP-adresser.<br/><br/> - **Återställ vm-disken**. Återställ disken. Anslut den sedan till en befintlig virtuell dator eller skapa en ny virtuell dator från disken med hjälp av PowerShell.<br/><br/> - **Byt ut VM-disk**. Om det finns en virtuell dator och den använder hanterade diskar (okrypterade) kan du återställa en disk och använda den för att ersätta en befintlig disk på den virtuella datorn.<br/><br/> - **Återställ specifika filer/mappar**. Du kan återställa filer/mappar från en virtuell dator i stället för från hela den virtuella datorn.
Direkt säkerhetskopiering av virtuella Azure-datorer (endast Windows)  | Säkerhetskopiera specifika filer/mappar/volym. | Installera [Azure Recovery Services-agenten](backup-azure-file-folder-backup-faq.md).<br/><br/> Du kan köra MARS-agenten tillsammans med säkerhetskopian för Azure VM-agenten för att säkerhetskopiera den virtuella datorn på fil-/mappnivå. | Återställ specifika mappar/filer.
Säkerhetskopiera Azure VM till säkerhetskopieringsserver  | Säkerhetskopiera filer/mappar/volymer. Systemtillstånd/bare metal-filer. programdata till System Center DPM eller Till Microsoft Azure Backup Server (MABS).<br/><br/> DPM/MABS backar sedan upp till säkerhetskopieringsvalvet. | Installera DPM/MABS-skyddsagenten på den virtuella datorn. MARS-agenten är installerad på DPM/MABS.| Återställa filer/mappar/volymer. Systemtillstånd/bare metal-filer. appdata.

Läs mer om säkerhetskopiering [med hjälp av en säkerhetskopieringsserver](backup-architecture.md#architecture-back-up-to-dpmmabs) och om [supportkrav](backup-support-matrix-mabs-dpm.md).

>[!NOTE]
> **Azure Backup stöder nu selektiv säkerhetskopiering och återställning av disk med hjälp av azure virtual machine-säkerhetskopieringslösningen.**
>
>Idag stöder Azure Backup säkerhetskopiering av alla diskar (operativsystem och data) i en virtuell dator tillsammans med hjälp av lösningen för säkerhetskopiering av virtuella datorer. Med exkluderingsdiskfunktioner får du möjlighet att säkerhetskopiera en eller några från de många datadiskarna i en virtuell dator. Detta ger en effektiv och kostnadseffektiv lösning för dina säkerhetskopierings- och återställningsbehov. Varje återställningspunkt innehåller data för diskarna som ingår i säkerhetskopieringen, vilket gör att du ytterligare kan få en delmängd diskar återställda från den angivna återställningspunkten under återställningen. Detta gäller för att återställa både från ögonblicksbilden och valvet.
>
>**För att registrera dig för förhandsgranskningen, skriv till oss påAskAzureBackupTeam@microsoft.com**

## <a name="supported-backup-actions"></a>Säkerhetskopieringsåtgärder som stöds

**Åtgärd** | **Support**
--- | ---
Säkerhetskopiera en virtuell dator som är avstängd/offline-VM | Stöds.<br/><br/> Ögonblicksbild är endast kraschkonsekvent, inte appkonsekvent.
Säkerhetskopiera diskar efter migrering till hanterade diskar | Stöds.<br/><br/> Säkerhetskopieringen fortsätter att fungera. Ingen åtgärd krävs.
Säkerhetskopiera hanterade diskar efter att resursgruppslåset har aktiverats | Stöds inte.<br/><br/> Azure Backup kan inte ta bort de äldre återställningspunkterna och säkerhetskopior kommer att börja misslyckas när den maximala gränsen för återställningspunkter har uppnåtts.
Ändra principer för säkerhetskopiering för en virtuell dator | Stöds.<br/><br/> Den virtuella datorn kommer att backas upp med hjälp av schema- och kvarhållningsinställningarna i den nya principen. Om kvarhållningsinställningarna utökas markeras befintliga återställningspunkter och behålls. Om de minskas kommer befintliga återställningspunkter att beskäras i nästa rensningsjobb och så småningom tas bort.
Avbryta ett säkerhetskopieringsjobb| Stöds under ögonblicksbildprocessen.<br/><br/> Stöds inte när ögonblicksbilden överförs till valvet.
Säkerhetskopiera den virtuella datorn till en annan region eller prenumeration |Stöds inte.
Säkerhetskopior per dag (via Azure VM-tillägget) | En schemalagd säkerhetskopiering per dag.<br/><br/>Azure Backup-tjänsten stöder upp till nio säkerhetskopieringar på begäran per dag, men Microsoft rekommenderar inte mer än fyra dagliga säkerhetskopieringar på begäran för att säkerställa bästa prestanda.
Säkerhetskopior per dag (via MARS-agenten) | Tre schemalagda säkerhetskopior per dag.
Säkerhetskopior per dag (via DPM/MABS) | Två schemalagda säkerhetskopior per dag.
Månatlig/årlig säkerhetskopiering| Stöds inte när du säkerhetskopierar med Azure VM-tillägg. Endast dagligen och veckovis stöds.<br/><br/> Du kan ställa in principen för att behålla dagliga/veckovisa säkerhetskopior för månatlig/års kvarhållningsperiod.
Automatisk klockjustering | Stöds inte.<br/><br/> Azure Backup justeras inte automatiskt för sommartidsändringar när du säkerhetskopierar en virtuell dator.<br/><br/>  Ändra principen manuellt efter behov.
[Säkerhetsfunktioner för hybridsäkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |Det går inte att inaktivera säkerhetsfunktioner.
Säkerhetskopiera den virtuella datorn vars maskintid ändras | Stöds inte.<br/><br/> Om datortiden ändras till en framtida datumtid efter att säkerhetskopiering för den virtuella datorn har aktiverats. Men även om tidsändringen återställs garanteras inte en lyckad säkerhetskopiering.  

## <a name="operating-system-support-windows"></a>Stöd för operativsystem (Windows)

I följande tabell sammanfattas de operativsystem som stöds när du säkerhetskopierar virtuella datorer med Windows Azure.

**Scenario** | **Stöd för operativsystemet**
--- | ---
Säkerhetskopiera med Azure VM-agenttillägg | - Windows 10 Client (endast 64 bitar) <br/><br/>- Windows Server 2019 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2016 (Datacenter/Datacenter Core/Standard) <br/><br/> - Windows Server 2012 R2 (Datacenter/Standard) <br/><br/> - Windows Server 2008 R2 (RTM och SP1 Standard)  <br/><br/> - Windows Server 2008 (endast 64 bitar)
Säkerhetskopiera med MARS-agent | [Operativsystem som stöds.](backup-support-matrix-mars-agent.md#supported-operating-systems)
Säkerhetskopiera med DPM/MABS | Operativsystem som stöds för säkerhetskopiering med [MABS](backup-mabs-protection-matrix.md) och [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

Azure Backup stöder inte 32-bitars operativsystem.

## <a name="support-for-linux-backup"></a>Stöd för Säkerhetskopiering av Linux

Det här är vad som stöds om du vill säkerhetskopiera Linux-datorer.

**Åtgärd** | **Support**
--- | ---
Säkerhetskopiera virtuella Linux Azure-datorer med Linux Azure VM-agenten | Fil konsekvent säkerhetskopiering.<br/><br/> Appkonsekvent säkerhetskopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Under återställningen kan du skapa en ny virtuell dator, återställa en disk och använda den för att skapa en virtuell dator eller återställa en disk och använda den för att ersätta en disk på en befintlig virtuell dator. Du kan också återställa enskilda filer och mappar.
Säkerhetskopiera virtuella linux Azure-datorer med MARS-agent | Stöds inte.<br/><br/> MARS-agenten kan endast installeras på Windows-datorer.
Säkerhetskopiera virtuella Linux Azure-datorer med DPM/MABS | Stöds inte.

## <a name="operating-system-support-linux"></a>Stöd för operativsystem (Linux)

För Azure VM Linux-säkerhetskopior stöder Azure Backup listan över [Linux-distributioner som stöds av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Observera följande:

- Azure Backup stöder inte Core OS Linux.
- Azure Backup stöder inte 32-bitars operativsystem.
- Andra bring-your-own Linux-distributioner kan fungera så länge [Azure VM-agenten för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) är tillgänglig på den virtuella datorn och så länge Python stöds.
- Azure Backup stöder inte en proxykonfigurerad Linux-virtuell dator om den inte har Python version 2.7 installerad.

## <a name="backup-frequency-and-retention"></a>Frekvensen och kvarhållningen för säkerhetskopiering

**Inställning** | **Gränser**
--- | ---
Maximala återställningspunkter per skyddad instans (maskin/arbetsbelastning) | 9999.
Maximal utfallstid för en återställningspunkt | Ingen gräns.
Maximal säkerhetskopieringsfrekvens till valvet (Azure VM-tillägg) | En gång om dagen.
Maximal säkerhetskopieringsfrekvens till valv (MARS-agent) | Tre säkerhetskopior per dag.
Högsta säkerhetskopieringsfrekvens till DPM/MABS | Var 15:e minut för SQL Server.<br/><br/> En gång i timmen för andra arbetsbelastningar.
Kvarhållning av återställningspunkt | Dagligen, veckovis, månadsvis och årligen.
Högsta kvarhållningsperiod | Beror på säkerhetskopieringsfrekvensen.
Återställningspunkter på DPM-/MABS-disk | 64 för filservrar och 448 för appservrar.<br/><br/> Bandåterställningspunkter är obegränsade för lokal DPM.

## <a name="supported-restore-methods"></a>Återställningsmetoder som stöds

**Alternativet Återställ** | **Detaljer**
--- | ---
**Skapa en ny virtuell dator** | Skapar och får snabbt igång en grundläggande virtuell dator från en återställningspunkt.<br/><br/> Du kan ange ett namn för den virtuella datorn, välja resursgruppen och det virtuella nätverket (VNet) där den ska placeras och ange ett lagringskonto för den återställda virtuella datorn. Den nya virtuella datorn måste skapas i samma region som källdatorn.
**Återställ disk** | Återställer en VM-disk som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup innehåller en mall som hjälper dig att anpassa och skapa en virtuell dator. <br/><br> Återställningsjobbet genererar en mall som du kan hämta och använda för att ange anpassade vm-inställningar och skapa en virtuell dator.<br/><br/> Diskarna kopieras till den resursgrupp som du anger.<br/><br/> Du kan också koppla disken till en befintlig virtuell dator eller skapa en ny virtuell dator med PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägga till konfigurationsinställningar som inte fanns där vid säkerhetskopieringen eller lägga till inställningar som måste konfigureras med mallen eller PowerShell.
**Ersätt befintlig** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Om det har tagits bort kan det här alternativet inte användas.<br/><br/> Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn innan disken ersätts och lagrar den på den mellanlagringsplats du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts med den valda återställningspunkten.<br/><br/> Ögonblicksbilden kopieras till valvet och behålls i enlighet med bevarandeprincipen. <br/><br/> Efter åtgärden ersätt disk behålls den ursprungliga disken i resursgruppen. Du kan välja att manuellt ta bort de ursprungliga diskarna om de inte behövs. <br/><br/>Ersätt befintliga stöds för okrypterade hanterade virtuella datorer. Det stöds inte för ohanterade diskar, [generaliserade virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)eller för virtuella datorer [som skapats med anpassade avbildningar](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Om återställningspunkten har mer eller mindre diskar än den aktuella virtuella datorn, kommer antalet diskar i återställningspunkten bara att återspegla vm-konfigurationen.<br><br> Ersätta befintliga stöds inte för virtuella datorer med länkade resurser (till [exempel användartilldelade hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) eller Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)eftersom klientappen för säkerhetskopiering inte har behörighet för dessa resurser när återställningen utförs.
**Korsregion (sekundär region)** | Återställning mellan regioner kan användas för att återställa virtuella Azure-datorer i den sekundära regionen, som är en [Azure-kopplad region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> Du kan återställa alla virtuella Azure-datorer för den valda återställningspunkten om säkerhetskopieringen görs i den sekundära regionen.<br><br> Den här funktionen är tillgänglig för alternativen nedan:<br> * [Skapa en virtuell dator](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Återställa diskar](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> Vi stöder för närvarande inte alternativet [Ersätt befintliga diskar.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Behörigheter<br> Återställningsåtgärden på sekundär region kan utföras av administratörer för säkerhetskopiering och appadministratörer.

## <a name="support-for-file-level-restore"></a>Stöd för återställning på filnivå

**Återställa** | **Stöds**
--- | ---
Återställa filer över operativsystem | Du kan återställa filer på alla datorer som har samma (eller kompatibla) operativsystem som den säkerhetskopierade virtuella datorn. Se [tabellen Kompatibelt operativsystem](backup-azure-restore-files-from-vm.md#system-requirements).
Återställa filer från krypterade virtuella datorer | Stöds inte.
Återställa filer från nätverksbegränsade lagringskonton | Stöds inte.
Återställa filer på virtuella datorer med Windows Lagringsutrymmen | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställ i stället filerna på en kompatibel virtuell dator.
Återställa filer på Linux VM med LVM/raid-matriser | Återställning stöds inte på samma virtuella dator.<br/><br/> Återställ på en kompatibel virtuell dator.
Återställa filer med särskilda nätverksinställningar | Återställning stöds inte på samma virtuella dator. <br/><br/> Återställ på en kompatibel virtuell dator.

## <a name="support-for-vm-management"></a>Stöd för hantering av virtuella datorer

I följande tabell sammanfattas stöd för säkerhetskopiering under vm-hanteringsuppgifter, till exempel lägga till eller ersätta VM-diskar.

**Återställa** | **Stöds**
--- | ---
Återställ över prenumeration/region/zon. | Stöds inte.
Återställa till en befintlig virtuell dator | Använd alternativet Ersätt disk.
Återställa disk med lagringskonto aktiverat för Azure Storage Service Encryption (SSE) | Stöds inte.<br/><br/> Återställ till ett konto som inte har SSE aktiverat.
Återställa till konton för blandad lagring |Stöds inte.<br/><br/> Baserat på lagringskontotypen kommer alla återställda diskar att vara antingen premium eller standard och inte blandade.
Återställa virtuell dator direkt till en tillgänglighetsuppsättning | För hanterade diskar kan du återställa disken och använda alternativet tillgänglighetsuppsättning i mallen.<br/><br/> Stöds inte för ohanterat diskar. För ohanterade diskar återställer du disken och skapar sedan en virtuell dator i tillgänglighetsuppsättningen.
Återställa säkerhetskopiering av ohanterade virtuella datorer efter uppgradering till hanterad virtuell dator| Stöds.<br/><br/> Du kan återställa diskar och sedan skapa en hanterad virtuell dator.
Återställa den virtuella datorn till återställningspunkten innan den virtuella datorn migrerades till hanterade diskar | Stöds.<br/><br/> Du återställer till ohanterade diskar (standard), konverterar de återställda diskarna till hanterad disk och skapar en virtuell dator med hanterade diskar.
Återställa en virtuell dator som har tagits bort. | Stöds.<br/><br/> Du kan återställa den virtuella datorn från en återställningspunkt.
Återställa en domänkontrollant (DC) vm som ingår i en konfiguration med flera DOMÄNER via portalen | Stöds om du återställer disken och skapar en virtuell dator med PowerShell.
Återställa virtuell dator i olika virtuella nätverk |Stöds.<br/><br/> Det virtuella nätverket måste finnas i samma prenumeration och region.

## <a name="vm-compute-support"></a>Stöd för vm-beräkning

**Compute** | **Support**
--- | ---
Storlek på virtuell dator |Alla Azure VM-storlekar med minst 2 CPU-kärnor och 1 GB RAM.<br/><br/> [Läs mer.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Säkerhetskopiera virtuella datorer i [tillgänglighetsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) | Stöds.<br/><br/> Du kan inte återställa en virtuell dator i en tillgänglig uppsättning med alternativet att snabbt skapa en virtuell dator. När du återställer den virtuella datorn återställer du i stället disken och använder den för att distribuera en virtuell dator, eller återställer en disk och använder den för att ersätta en befintlig disk.
Säkerhetskopiera virtuella datorer som distribueras med [hybridanvändningsförmån (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Stöds.
Säkerhetskopiera virtuella datorer som distribueras i en [skalningsuppsättning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |Stöds inte.
Säkerhetskopiera virtuella datorer som distribueras från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Publicerad av Microsoft, tredje part) |Stöds.<br/><br/> Den virtuella datorn måste köra ett operativsystem som stöds.<br/><br/> När du återställer filer på den virtuella datorn kan du bara återställa till ett kompatibelt operativsystem (inte ett tidigare eller senare operativsystem). Vi återställer inte virtuella Azure Marketplace-datorer som backas upp som virtuella datorer, eftersom dessa behöver inköpsinformation utan endast som diskar.
Säkerhetskopiera virtuella datorer som distribueras från en anpassad avbildning (tredje part) |Stöds.<br/><br/> Den virtuella datorn måste köra ett operativsystem som stöds.<br/><br/> När du återställer filer på den virtuella datorn kan du bara återställa till ett kompatibelt operativsystem (inte ett tidigare eller senare operativsystem).
Säkerhetskopiera virtuella datorer som migreras till Azure| Stöds.<br/><br/> För att säkerhetskopiera den virtuella datorn måste VM-agenten installeras på den migrerade datorn.
Säkerhetskopiera konsekvens för flera virtuella datorer | Azure Backup ger inte data och programkonsekvens mellan flera virtuella datorer.
Säkerhetskopiering med [diagnostikinställningar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)  | Unsupported. <br/><br/> Om återställningen av den virtuella Azure-datorn med diagnostikinställningar utlöses med alternativet [Skapa ny,](backup-azure-arm-restore-vms.md#create-a-vm) misslyckas återställningen.
Återställa zone-fästa virtuella datorer | Stöds (för virtuell dator som säkerhetskopieras efter januari 2019 och där [tillgänglighetszon](https://azure.microsoft.com/global-infrastructure/availability-zones/) är tillgängliga).<br/><br/>Vi stöder för närvarande återställning till samma zon som är fäst i virtuella datorer. Men om zonen inte är tillgänglig misslyckas återställningen.
Gen2 virtuella datorer | Stöds <br> Azure Backup stöder säkerhetskopiering och återställning av [Gen2 virtuella datorer](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/). När dessa virtuella datorer återställs från återställningspunkten återställs de som [Gen2-virtuella datorer](https://azure.microsoft.com/updates/generation-2-virtual-machines-in-azure-public-preview/).

## <a name="vm-storage-support"></a>Stöd för VM-lagring

**Komponent** | **Support**
--- | ---
Datadiskar för virtuella Azure-datorer | Säkerhetskopiera en virtuell dator med 16 eller färre datadiskar.<BR> För att registrera dig för den privata förhandsversionen av virtuella datorer med 16+ diskar (upp till 32 diskar) skriver du till oss på AskAzureBackupTeam@microsoft.com
Storlek på datadisk | Individuell diskstorlek kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar i en virtuell dator.
Lagringstyp | Standard HDD, Standard SSD, Premium SSD.
Hanterade diskar | Stöds.
Krypterade diskar | Stöds.<br/><br/> Virtuella Azure-datorer som är aktiverade med Azure Disk Encryption kan säkerhetskopieras (med eller utan Azure AD-appen).<br/><br/> Krypterade virtuella datorer kan inte återställas på fil-/mappnivå. Du måste återställa hela den virtuella datorn.<br/><br/> Du kan aktivera kryptering på virtuella datorer som redan är skyddade av Azure Backup.
Diskar med skrivaccelerator aktiverade | Stöds inte.<br/><br/> Azure-säkerhetskopiering utesluter automatiskt diskarna med Skriv accelerator aktiverat under säkerhetskopiering. Eftersom de inte säkerhetskopieras kan du inte återställa dessa diskar från återställningspunkter för den virtuella datorn.
Säkerhetskopiera & återställ deduplicerade virtuella datorer/diskar | Azure Backup stöder inte deduplicering. Mer information finns i den här [artikeln](https://docs.microsoft.com/azure/backup/backup-support-matrix#disk-deduplication-support) <br/> <br/>  - Azure Backup deduplicerar inte över virtuella datorer i Recovery Services-valvet <br/> <br/>  - Om det finns virtuella datorer i dedupliceringstillstånd under återställningen kan filerna inte återställas eftersom valvet inte förstår formatet. Du kan dock utföra den fullständiga vm-återställningen.
Lägga till disk i skyddad virtuell dator | Stöds.
Ändra storlek på disk på skyddad virtuell dator | Stöds.
Delad lagring| Det går inte att säkerhetskopiera virtuella datorer med klusterdelad volym (CSV) eller utskalningsfilserver. CSV-skribenter kommer sannolikt att misslyckas under säkerhetskopieringen. Vid återställning kan det hända att diskar som innehåller CSV-volymer inte kommer upp.
[Delade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable) | Stöds inte.

## <a name="vm-network-support"></a>Stöd för VM-nätverk

**Komponent** | **Support**
--- | ---
Antal nätverksgränssnitt | Upp till maximalt antal nätverkskort som stöds för en viss Azure VM-storlek.<br/><br/> Nätverkskort skapas när den virtuella datorn skapas under återställningsprocessen.<br/><br/> Antalet nätverkskort på den återställda virtuella datorn speglar antalet nätverkskort på den virtuella datorn när du har aktiverat skydd. Om du tar bort nätverkskort när du har aktiverat skydd påverkas inte antalet.
Extern/intern belastningsutjämnare |Stöds. <br/><br/> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om hur du återställer virtuella datorer med särskilda nätverksinställningar.
Flera reserverade IP-adresser |Stöds. <br/><br/> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om hur du återställer virtuella datorer med särskilda nätverksinställningar.
Virtuella datorer med flera nätverkskort| Stöds. <br/><br/> [Läs mer](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) om hur du återställer virtuella datorer med särskilda nätverksinställningar.
Virtuella datorer med offentliga IP-adresser| Stöds.<br/><br/> Associera en befintlig offentlig IP-adress med nätverkskortet, eller skapa en adress och associera den med nätverkskortet när återställningen är klar.
Nätverkssäkerhetsgrupp (NSG) på nätverkskort/undernät. |Stöds.
Statisk IP-adress | Stöds inte.<br/><br/> En ny virtuell dator som har skapats från en återställningspunkt tilldelas en dynamisk IP-adress.<br/><br/> För klassiska virtuella datorer kan du inte säkerhetskopiera en virtuell dator med en reserverad IP-adress och ingen definierad slutpunkt.
Dynamisk IP-adress |Stöds.<br/><br/> Om nätverkskortet på källdatorn använder dynamisk IP-adressering använder nätverkskortet på den återställda virtuella datorn det också.
Azure Traffic Manager| Stöds.<br/><br/>Om den säkerhetskopierade virtuella datorn finns i Traffic Manager lägger du manuellt till den återställda virtuella datorn i samma Traffic Manager-instans.
Azure DNS |Stöds.
Anpassad DNS |Stöds.
Utgående anslutning via HTTP-proxy | Stöds.<br/><br/> En autentrad proxy stöds inte.
Tjänstslutpunkter för virtuellt nätverk| Stöds.<br/><br/> Inställningar för brandvägg och virtuellt nätverkslagringskonto bör tillåta åtkomst från alla nätverk.

## <a name="vm-security-and-encryption-support"></a>Stöd för VM-säkerhet och kryptering

Azure Backup stöder kryptering för data under överföring och i vila:

Nätverkstrafik till Azure:

- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.
- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- Endast du har tillgång till den lösenfras som krävs för att låsa upp dessa data. Microsoft kan aldrig dekryptera säkerhetskopierade data.

  > [!WARNING]
  > När du har konfigurerat valvet är det bara du som har åtkomst till krypteringsnyckeln. Microsoft sparar aldrig någon kopia och har inte åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa dina säkerhetskopierade data.

Datasäkerhet:

- När du säkerhetskopierar virtuella Azure-datorer måste du konfigurera kryptering *inom* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och oss **dm-crypt** på virtuella Linux-datorer.
- På serverdelen använder Azure Backup [Azure Storage Service-kryptering](../storage/common/storage-service-encryption.md), som skyddar vilande data.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | ![Ja][green] | ![Ja][green]
Virtuella Azure-datorer | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]

## <a name="vm-compression-support"></a>Stöd för VM-komprimering

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, som sammanfattas i följande tabell. Observera följande:

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure-lagringskontot över lagringsnätverket. Det är inte nödvändigt att komprimera denna trafik.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhetskopieras till DPM/MABS.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
Lokala Windows-datorer utan DPM/MABS | Ej tillämpligt | ![Ja][green]
Virtuella Azure-datorer | Ej tillämpligt | Ej tillämpligt
Lokala/virtuella Azure-datorer med DPM | ![Ja][green] | ![Ja][green]
Lokala/virtuella Azure-datorer med MABS | ![Ja][green] | ![Ja][green]

## <a name="next-steps"></a>Nästa steg

- [Säkerhetskopiera virtuella Azure-datorer](backup-azure-arm-vms-prepare.md).
- [Säkerhetskopiera Windows-datorer direkt](tutorial-backup-windows-server-to-azure.md) utan en reservserver.
- [Konfigurera MABS](backup-azure-microsoft-azure-backup.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till MABS.
- [Konfigurera DPM](backup-azure-dpm-introduction.md) för säkerhetskopiering till Azure och säkerhetskopiera sedan arbetsbelastningar till DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
