---
title: Planera för distribution av Azure File Sync | Microsoft Docs
description: Lär dig vad du ska tänka på när du planerar för en Azure Files distribution.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d2dbe29c5a348363172f57da86483ccf3fd787f0
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046093"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planera för distribution av Azure File Sync
Använd Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

I den här artikeln beskrivs viktiga överväganden för en Azure File Sync distribution. Vi rekommenderar att du också läser [planering för en Azure Files distribution](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure File Sync terminologi
Innan du får information om hur du planerar för en Azure File Sync distribution är det viktigt att förstå terminologin.

### <a name="storage-sync-service"></a>Tjänsten för synkronisering av lagring
Tjänsten Storage Sync är Azure-resursen på den översta nivån för Azure File Sync. Tjänsten Storage Sync service är en peer med lagrings konto resursen och kan distribueras på samma sätt till Azure-resurs grupper. En distinkt toppnivå resurs från lagrings konto resursen krävs eftersom tjänsten för synkronisering av lagrings utrymme kan skapa synkroniseringsrelation med flera lagrings konton via flera Sync-grupper. En prenumeration kan ha flera distribuerade resurser för lagrings-synkroniseringstjänsten.

### <a name="sync-group"></a>Synkronisera grupp
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. Om du till exempel har två distinkta uppsättningar med filer som du vill hantera med Azure File Sync skapar du två synkroniserade grupper och lägger till olika slut punkter i varje synkroniseringsresurs. En tjänst för synkronisering av lagring kan vara värd för så många Sync-grupper som du behöver.  

### <a name="registered-server"></a>Registrerad Server
Det registrerade serverobjektet representerar en förtroende relation mellan servern (eller klustret) och tjänsten för synkronisering av lagring. Du kan registrera så många servrar som en tjänst instans för lagrings synkronisering som du vill. En server (eller ett kluster) kan dock bara registreras med en lagrings tjänst för synkronisering i taget.

### <a name="azure-file-sync-agent"></a>Azure File Sync agent
Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs. Azure File Sync agenten har tre huvud komponenter: 
- **FileSyncSvc. exe**: bakgrunds tjänsten som ansvarar för att övervaka ändringar på Server slut punkter och för att initiera svarssessioner till Azure.
- **StorageSync. sys**: det Azure File Sync fil system filter, som ansvarar för att skikta filer till Azure Files (när moln nivåer är aktiverat).
- **PowerShell-hanterings-cmdlet**: PowerShell-cmdletar som du använder för att interagera med Microsoft. StorageSync Azure Resource Provider. Du kan hitta dessa på följande (standard) platser:
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Server slut punkt
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. Flera Server slut punkter kan finnas på samma volym om deras namn rymder inte överlappar varandra (till exempel `F:\sync1` och `F:\sync2`). Du kan konfigurera principer för moln nivåer individuellt för varje server slut punkt. 

Du kan skapa en server slut punkt via en monterings punkt. Observera att mountpoints i Server slut punkten hoppas över.  

Du kan skapa en server slut punkt på system volymen men det finns två begränsningar om du gör det:
* Det går inte att aktivera moln skiktning.
* Snabb namn områdes återställning (där systemet snabbt tar bort hela namn området och börjar återställa innehållet) utförs inte.


> [!Note]  
> Endast icke-flyttbara volymer stöds.  Enheter som har mappats från en fjärran sluten resurs stöds inte för en server slut punkts Sök väg.  Dessutom kan en server slut punkt finnas på Windows-systemvolymen, även om det inte finns stöd för moln nivåer på system volymen.

Om du lägger till en server plats som har en befintlig uppsättning filer som en server slut punkt till en Sync-grupp, slås filerna samman med andra filer som redan finns på andra slut punkter i Sync-gruppen.

### <a name="cloud-endpoint"></a>Moln slut punkt
En moln slut punkt är en Azure-filresurs som är en del av en Sync-grupp. Hela Azure-filresursen synkroniseras och en Azure-filresurs kan endast vara medlem i en moln slut punkt. Därför kan en Azure-filresurs vara medlem i en enda Sync-grupp. Om du lägger till en Azure-filresurs som har en befintlig uppsättning filer som en moln slut punkt till en Sync-grupp, slås de befintliga filerna samman med andra filer som redan finns på andra slut punkter i Sync-gruppen.

> [!Important]  
> Azure File Sync har stöd för att göra ändringar i Azure-filresursen direkt. Alla ändringar som görs på Azure-filresursen måste dock först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras bara för en moln slut punkt var 24: e timme. Ändringar som görs i en Azure-filresurs via REST-protokollet kommer dessutom inte att uppdatera tidpunkten för senaste ändring av SMB och visas inte som en ändring genom synkronisering. Mer information finns i [Azure Files vanliga frågor och svar](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Moln nivåer är en valfri funktion i Azure File Sync där ofta använda filer cachelagras lokalt på servern medan alla andra filer är i nivå av Azure Files baserat på princip inställningar. Mer information finns i [förstå moln nivåer](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync system krav och interoperabilitet 
Det här avsnittet beskriver Azure File Sync agent system krav och samverkan med Windows Server-funktioner och-roller och lösningar från tredje part.

### <a name="evaluation-cmdlet"></a>Utvärderings-cmdlet
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av Azure File Sync Evaluation-cmdleten. Denna cmdlet söker efter eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Kontrollerna avser de flesta men inte alla funktioner som nämns nedan. Vi rekommenderar att du läser igenom resten av det här avsnittet noggrant för att se till att distributionen går smidigt. 

Du kan installera utvärderings-cmdleten genom att installera AZ PowerShell-modulen, som kan installeras genom att följa anvisningarna här: [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Användning  
Du kan starta utvärderings verktyget på ett par olika sätt: du kan utföra system kontroller, data uppsättnings kontroller eller båda. Så här utför du både system-och data uppsättnings kontroller: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Så här testar du endast din data uppsättning:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Så här testar du system kraven:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Så här visar du resultatet i CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Systemkrav
- En server som kör någon av följande operativ system versioner:

    | Version | SKU: er som stöds | Distributions alternativ som stöds |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Data Center och standard | Full och Core |
    | Windows Server 2016 | Data Center och standard | Full och Core |
    | Windows Server 2012 R2 | Data Center och standard | Full och Core |
    | Windows Server IoT 2019 för lagring| Data Center och standard | Full och Core |
    | Windows Storage Server 2016| Data Center och standard | Full och Core |
    | Windows Storage Server 2012 R2| Data Center och standard | Full och Core |

    Framtida versioner av Windows Server kommer att läggas till när de släpps.

    > [!Important]  
    > Vi rekommenderar att du behåller alla servrar som du använder med Azure File Sync uppdaterad med de senaste uppdateringarna från Windows Update. 

- En server med minst 2 GiB minne.

    > [!Important]  
    > Om servern körs på en virtuell dator med dynamiskt minne aktiverat, ska den virtuella datorn konfigureras med minst 2048 MiB för minne.
    
- En lokalt ansluten volym som är formaterad med NTFS-filsystemet.

### <a name="file-system-features"></a>Funktioner i fil systemet

| Funktion | Supportstatus | Anteckningar |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Fullt stöd | Windows ACL: er bevaras av Azure File Sync och framtvingas av Windows Server på Server slut punkter. Windows ACL: er stöds inte (ännu) av Azure Files om filer nås direkt i molnet. |
| Hårda länkar | Överhoppad | |
| Symboliska länkar | Överhoppad | |
| Monteringspunkter | Stöds delvis | Monterings punkter kan vara roten i en server slut punkt, men de hoppas över om de finns i en server slut punkts namnrymd. |
| Knut punkter | Överhoppad | Till exempel Distributed File System DfrsrPrivate-och DFSRoots-mappar. |
| Referenspunkter | Överhoppad | |
| NTFS-komprimering | Fullt stöd | |
| Sparse-filer | Fullt stöd | Synkronisering av sparse-filer (blockeras inte), men synkroniserar till molnet som en fullständig fil. Om fil innehållet ändras i molnet (eller på en annan server) är filen inte längre sparse när ändringen laddas ned. |
| Alternativa data strömmar (ADS) | Konserverat, men inte synkroniserat | Till exempel synkroniseras inte klassificerings etiketter som skapats av fil klassificerings infrastrukturen. Befintliga klassificerings etiketter på filer på alla Server slut punkter lämnas orörda. |

> [!Note]  
> Endast NTFS-volymer stöds. ReFS, FAT, FAT32 och andra fil system stöds inte.

### <a name="files-skipped"></a>Överhoppade filer

| Fil/mapp | Obs! |
|-|-|
| pagefile.sys | Filinformation till system |
| Desktop. ini | Filinformation till system |
| tummes. db | Temporär fil för miniatyrer |
| ehthumbs. db | Temporär fil för medie miniatyrer |
| ~$\*.\* | Tillfällig Office-fil |
| \*. tmp | Temporär fil |
| \*.laccdb | Lås fil för åtkomst databasen|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern Sync-fil|
| \\system volym information | Mapp som är speciell för volym |
| $RECYCLE. PLATS| Mapp |
| \\SyncShareState | Mapp för synkronisering |

### <a name="failover-clustering"></a>Redundanskluster
Windows Server-redundanskluster stöds av Azure File Sync för distributions alternativet "fil server för allmän användning". Redundanskluster stöds inte på Skalbar filserver för program data (SOFS) eller på klusterdelade volymer (CSV: er).

> [!Note]  
> Azure File Sync agenten måste installeras på varje nod i ett redundanskluster för att synkroniseringen ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
**Windows server 2016 och Windows server 2019**   
Datadeduplicering stöds på volymer med moln nivåer som är aktiverade på Windows Server 2016 och Windows Server 2019. Genom att aktivera datadeduplicering på en volym med aktive rad moln nivå kan du cachelagra fler filer lokalt utan att tillhandahålla mer lagrings utrymme. 

När datadeduplicering har Aktiver ATS på en volym med aktive rad moln nivå, kommer deduplicering av optimerade filer på serverns slut punkt att på samma sätt som en normal fil baserat på princip inställningarna för moln skiktet. När de deduplicerade filerna har flyttats, körs skräp insamlings jobbet för datadeduplicering automatiskt för att frigöra disk utrymme genom att ta bort onödiga segment som inte längre refereras till av andra filer på volymen.

Observera att volym besparingarna gäller endast för servern. dina data i Azure-filresursen kommer inte att dedupliceras.

> [!Note]  
> För att stödja datadeduplicering på volymer med moln skiktning aktiverat på Windows Server 2019 måste Windows Update [KB4520062](https://support.microsoft.com/help/4520062) installeras och Azure File Sync agent version 9.0.0.0 eller senare krävs.

**Windows Server 2012 R2**  
Azure File Sync stöder inte datadeduplicering och moln nivåer på samma volym på Windows Server 2012 R2. Om datadeduplicering har Aktiver ATS på en volym måste moln nivån vara inaktive rad. 

**Anteckningar**
- Om datadeduplicering installeras innan du installerar Azure File Sync agent krävs en omstart för att stödja datadeduplicering och moln nivåer på samma volym.
- Om datadeduplicering har Aktiver ATS på en volym efter att moln nivån har Aktiver ATS optimerar optimerings jobbet för den inledande dedupliceringen filer på volymen som inte redan är i nivå och kommer att ha följande påverkan på moln nivåer:
    - Principen för ledigt utrymme kommer att fortsätta att göra filer på nivå av filer efter det lediga utrymmet på volymen med hjälp av termisk karta.
    - Datum policyn hoppar över nivåer av filer som annars kan ha kvalificerats för lagrings nivåer på grund av att optimerings jobbet för deduplicering har åtkomst till filerna.
- För pågående optimerings jobb för deduplicering kommer moln nivåer med datum policyn att förskjutas av [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) -inställningen för datadeduplicering, om filen inte redan är i nivå. 
    - Exempel: om MinimumFileAgeDays-inställningen är sju dagar och den datum policyn för molnnivå är 30 dagar, kommer datum principen att nivånamn efter 37 dagar.
    - Obs! när en fil har en nivå av Azure File Sync hoppar optimerings jobbet för deduplicering över filen.
- Om en server som kör Windows Server 2012 R2 med Azure File Sync-agenten installerad uppgraderas till Windows Server 2016 eller Windows Server 2019, måste följande steg utföras för att stödja datadeduplicering och moln nivåer på samma volym:  
    - Avinstallera Azure File Sync-agenten för Windows Server 2012 R2 och starta om servern.
    - Hämta Azure File Sync agent för den nya versionen av serveroperativ systemet (Windows Server 2016 eller Windows Server 2019).
    - Installera Azure File Sync agent och starta om servern.  
    
    Obs! Azure File Sync konfigurations inställningar på servern bevaras när agenten avinstalleras och installeras om.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync stöder interop med DFS-namnområden (DFS-N) och DFS Replication (DFS-R).

**DFS-namnrymder (DFS-n)** : Azure File Sync stöds fullt ut på DFS-n-servrar. Du kan installera Azure File Sync agenten på en eller flera DFS-N-medlemmar för att synkronisera data mellan server slut punkterna och moln slut punkten. Mer information finns i [Översikt över DFS-namnrymder](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-r)** : eftersom DFS-r och Azure File Sync båda är lösningar för replikering rekommenderar vi i de flesta fall att du ersätter DFS-R med Azure File Sync. Det finns dock flera scenarier där du vill använda DFS-R och Azure File Sync tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure File Sync-distribution. Mer information finns i [Migrera en DFS Replication (DFS-R) distribution till Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Alla lokala servrar som behöver en kopia av dina fildata kan vara anslutna direkt till Internet.
- Filial servrar konsoliderar data till en enda hubb-server som du vill använda Azure File Sync.

För att Azure File Sync och DFS-R ska fungera sida vid sida:

1. Azure File Sync moln nivåer måste inaktive ras på volymer med replikerade DFS-R-mappar.
2. Server slut punkter ska inte konfigureras i mappar för skrivskyddad DFS-R-replikering.

Mer information finns i [DFS Replication översikt](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Att använda Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Agent installation och Server registrering bör ske när du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="windows-search"></a>Windows Search
Om moln skiktning är aktiverat på en server slut punkt hoppas filer som skiktas över och inte indexeras av Windows Search. Filer som inte är på en nivå indexeras korrekt.

### <a name="antivirus-solutions"></a>Antivirus lösningar
Eftersom antivirus programmet fungerar genom att söka igenom filer efter känd skadlig kod kan ett antivirus program orsaka återkallande av nivåbaserade filer. I version 4,0 och senare av Azure File Sync agenten har filer på nivån säker Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS angett. Vi rekommenderar att du rådfrågar din program varu leverantör för att lära dig hur du konfigurerar lösningen för att hoppa över att läsa filer med den här attributuppsättningen (många gör det automatiskt). 

Microsofts interna antivirus lösningar, Windows Defender och System Center Endpoint Protection (SCEP), hoppar båda automatiskt över att läsa filer som har det här attributet angivet. Vi har testat dem och identifierat ett mindre problem: när du lägger till en server i en befintlig Sync-grupp, anropas filer som är mindre än 800 byte (nedladdade) på den nya servern. De här filerna kommer att finnas kvar på den nya servern och kommer inte att skiktas eftersom de inte uppfyller storleks kravet för skikt (> 64 KB).

> [!Note]  
> Antivirus leverantörer kan kontrol lera kompatibiliteten mellan sina produkter och Azure File Sync med hjälp av [Testsviten Azure File Sync Antivirus kompatibilitet](https://www.microsoft.com/download/details.aspx?id=58322)som är tillgänglig för hämtning på Microsoft Download Center.

### <a name="backup-solutions"></a>Säkerhets kopierings lösningar
Liksom antivirus lösningar kan säkerhets kopierings lösningar orsaka åter kallelse av filer på nivå. Vi rekommenderar att du använder en lösning för säkerhets kopiering i molnet för att säkerhetskopiera Azure-filresursen i stället för en lokal säkerhets kopierings produkt.

Om du använder en lokal lösning för säkerhets kopiering ska säkerhets kopiorna utföras på en server i den synkroniserade grupp där moln nivå inaktive ras. När du utför en återställning använder du alternativen på volym-eller fil nivå återställning. Filer som återställs med alternativet Återställning på filnivå synkroniseras till alla slut punkter i Sync-gruppen och befintliga filer ersätts med den version som återställs från säkerhets kopian.  Återställningar på volym nivå ersätter inte nyare fil versioner i Azure-filresursen eller andra server slut punkter.

> [!Note]  
> Återställning utan operativ system (BMR) kan orsaka oväntade resultat och stöds inte för närvarande.

> [!Note]  
> Med version 9 av Azure File Sync agent, stöds nu VSS-ögonblicksbilder (inklusive tidigare versioner) på volymer som har aktiverat moln skikt. Du måste dock aktivera tidigare versions kompatibilitet via PowerShell. [Lär dig mer](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>Krypterings lösningar
Stöd för krypterings lösningar är beroende av hur de implementeras. Azure File Sync är känt att arbeta med:

- BitLocker-kryptering
- Azure Information Protection, Azure Rights Management Services (Azure RMS) och Active Directory RMS

Azure File Sync är känt att inte arbeta med:

- NTFS-krypterat fil system (EFS)

I allmänhet bör Azure File Sync stödja samverkan med krypterings lösningar som är under fil systemet, till exempel BitLocker, och med lösningar som implementeras i fil formatet, till exempel Azure Information Protection. Ingen särskild interoperabilitet har gjorts för lösningar som är ovanför fil systemet (till exempel NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra HSM-lösningar (hierarkisk lagrings hantering)
Inga andra HSM-lösningar ska användas med Azure File Sync.

## <a name="region-availability"></a>Tillgänglighet för regioner
Azure File Sync är endast tillgängligt i följande regioner:

| Region | Data Center plats |
|--------|---------------------|
| Australien, östra | New South Wales |
| Australien, sydöstra | Victoria |
| Brasilien, södra | Sao Paulo (delstat) |
| Kanada, centrala | Toronto |
| Kanada, östra | Quebec City |
| Indien, centrala | Pune |
| USA, centrala | Iowa |
| Asien, östra | Hongkong SAR |
| USA, östra | Virginia |
| USA, östra 2 | Virginia |
| Frankrike, centrala | Paris |
| Frankrike, södra * | Marseille |
| Sydkorea, centrala | Seoul |
| Sydkorea, södra | Busan |
| Japan, östra | Tokyo, Saitama |
| Japan, västra | Osaka |
| USA, norra centrala | Illinois |
| Europa, norra | Irland |
| Sydafrika, norra | Johannesburg |
| Södra Afrika, västra * | Kapstaden |
| USA, södra centrala | Texas |
| Indien, södra | Chennai |
| Sydostasien | Singapore |
| Storbritannien, södra | London |
| Storbritannien, västra | Cardiff |
| US Gov, Arizona | Arizona |
| US Gov, Texas | Texas |
| USA Gov Virginia | Virginia |
| Förenade Arabemiraten, norra | Dubai |
| Förenade Arabemiraten Central * | Abu Dhabi |
| Europa, västra | Nederländerna |
| USA, västra centrala | Wyoming |
| USA, västra | Kalifornien |
| USA, västra 2 | Washington |

Azure File Sync stöder endast synkronisering med en Azure-filresurs som är i samma region som tjänsten för synkronisering av lagring.

För regionerna som har marker ATS med asterisker måste du kontakta Azure-supporten för att begära åtkomst till Azure Storage i dessa regioner. Processen beskrivs i [det här dokumentet](https://azure.microsoft.com/global-infrastructure/geographies/).

### <a name="azure-disaster-recovery"></a>Azure haveri beredskap
Azure File Sync integreras med alternativet [Geo-redundant Storage redundans](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) för att skydda mot förlust av en Azure-region. GRS Storage fungerar med asynkron blockering av replikering mellan lagring i den primära regionen, som du normalt interagerar med och lagring i den länkade sekundära regionen. I händelse av en katastrof som gör att Azure-regionen tillfälligt eller permanent är offline, kommer Microsoft att redundansväxla redundans till den kopplade regionen. 

> [!Warning]  
> Om du använder Azure-filresursen som en moln slut punkt i ett GRS lagrings konto bör du inte initiera redundans för lagrings konto. Om du gör det slutar synkroniseringen att fungera och det kan leda till oväntade dataförluster för nyligen nivåbaserade filer. Om en Azure-region förloras utlöser Microsoft lagrings kontots redundans på ett sätt som är kompatibelt med Azure File Sync.

För att stödja redundansväxlingen mellan Geo-redundant lagring och Azure File Sync, är alla Azure File Sync regionerna kopplade till en sekundär region som matchar den sekundära region som används av lagringen. Dessa par är följande:

| Primär region      | Länkad region      |
|---------------------|--------------------|
| Australien, östra      | Australien, sydöstra|
| Australien, sydöstra | Australien, östra     |
| Brasilien, södra        | USA, södra centrala   |
| Kanada, centrala      | Kanada, östra        |
| Kanada, östra         | Kanada, centrala     |
| Indien, centrala       | Indien, södra        |
| USA, centrala          | USA, östra 2          |
| Asien, östra           | Sydostasien     |
| USA, östra             | USA, västra            |
| USA, östra 2           | USA, centrala         |
| Frankrike, centrala      | Frankrike, södra       |
| Frankrike, södra        | Frankrike, centrala     |
| Japan, östra          | Japan, västra         |
| Japan, västra          | Japan, östra         |
| Sydkorea, centrala       | Sydkorea, södra        |
| Sydkorea, södra         | Sydkorea, centrala      |
| Europa, norra        | Europa, västra        |
| USA, norra centrala    | USA, södra centrala   |
| Sydafrika, norra  | Sydafrika, västra  |
| Sydafrika, västra   | Sydafrika, norra |
| USA, södra centrala    | USA, norra centrala   |
| Indien, södra         | Indien, centrala      |
| Sydostasien      | Asien, östra          |
| Storbritannien, södra            | Storbritannien, västra            |
| Storbritannien, västra             | Storbritannien, södra           |
| US Gov, Arizona      | US Gov, Texas       |
| US Gov, Iowa         | USA Gov Virginia    |
| USA Gov Virginia      | US Gov, Texas       |
| Europa, västra         | Europa, norra       |
| USA, västra centrala     | USA, västra 2          |
| USA, västra             | USA, östra            |
| USA, västra 2           | USA, västra centrala    |

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>Rekommenderad Azure File Sync dator konfiguration

Azure File Sync dator kraven bestäms av antalet objekt i namn området och omsättningen på data uppsättningen. En enskild server kan kopplas till flera Sync-grupper och antalet objekt som anges i följande tabell konton för det fullständiga namn område som en server är ansluten till. Till exempel Server slut punkt A med 10 000 000 objekt + Server slut punkt B med 10 000 000 objekt = 20 000 000-objekt. För den här exempel distributionen rekommenderar vi 8CPU, 16GiBt minne för stabilt tillstånd och (om möjligt) 48GiB av minne för den första migreringen.
 
Namn områdes data lagras i minnet av prestanda skäl. På grund av detta kräver större namn rymder mer minne för att upprätthålla bästa prestanda och mer omsättning kräver mer processor kraft för att bearbeta. 
 
I följande tabell har vi tillhandahållit både storleken på namn området och en konvertering till kapacitet för vanliga fil resurser i generella syften, där den genomsnittliga fil storleken är 512KiB. Om fil storlekarna är mindre bör du överväga att lägga till ytterligare minne för samma mängd kapacitet. Basera minnes konfigurationen på storleken på namn området.

| Storlek på namnrymd – filer & kataloger (miljoner)  | Typisk kapacitet (TiB)  | CPU-kärnor  | Rekommenderat minne (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (ursprunglig synkronisering)/2 (typisk omsättning)      |
| 5        | 2.4     | 2        | 16 (ursprunglig synkronisering)/4 (typisk omsättning)    |
| 10       | 4.8     | 4        | 32 (ursprunglig synkronisering)/8 (typisk omsättning)   |
| 30       | 14,3    | 8        | 48 (ursprunglig synkronisering)/16 (typisk omsättning)   |
| 50       | 23,8    | 16       | 64 (ursprunglig synkronisering)/32 (typisk omsättning)  |
| 100 *     | 47,7   | 32       | 128 (ursprunglig synkronisering)/32 (typisk omsättning)  |

\*fler än 100 000 000 filer & kataloger inte har testats. Detta är en mjuk gräns.

> [!TIP]
> Inledande synkronisering av ett namn område är en intensiv åtgärd och vi rekommenderar att du allokerar mer minne tills den första synkroniseringen har slutförts. Detta är inte obligatoriskt, men det kan påskynda den inledande synkroniseringen. 
> 
> Typisk omsättning är 0,5% av namn området som ändras per dag. Överväg att lägga till mer processor för högre omsättnings nivåer. 

## <a name="next-steps"></a>Nästa steg
* [Överväg inställningar för brand vägg och proxy](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
