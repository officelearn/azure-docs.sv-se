---
title: Planera för distribution av Azure File Sync | Microsoft Docs
description: Lär dig att tänka på när du planerar för distribution av Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: d5b01566f672309837f738e185820a0f13eda1c1
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382262"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planera för distribution av Azure File Sync
Använd Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln beskrivs viktiga överväganden för distribution av Azure File Sync. Vi rekommenderar att du också läser [planera för distribution av Azure Files](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Azure File Sync-terminologi
Det är viktigt att du förstår termer som innan du hämtar in information om att planera för distribution av Azure File Sync.

### <a name="storage-sync-service"></a>Tjänst för synkronisering av lagring
Storage Sync-tjänsten är översta Azure-resursen för Azure File Sync. Tjänst för Lagringssynkronisering resursen är en peer för resursen för lagringskonton och på samma sätt kan bara distribueras till Azure-resursgrupper. En resurs med distinkta på toppnivå från resursen för lagringskonton är nödvändigt eftersom Storage Sync-tjänsten kan skapa synkroniseringsrelationer med flera storage-konton via flera synkroniseringsgrupper. En prenumeration kan ha flera resurser som Storage Sync-tjänsten har distribuerats.

### <a name="sync-group"></a>Synkroniseringsgrupp
En synkroniseringsgrupp definierar sync-topologi för en uppsättning filer. Slutpunkter inom en synkroniseringsgrupp hålls synkroniserade med varandra. Om du har till exempel två distinkta grupper av filer som du vill hantera med Azure File Sync kan du skapa två synkroniseringsgrupper och lägga till olika slutpunkter för varje synkroniseringsgrupp. En tjänst för Lagringssynkronisering kan vara värd för så många synkroniseringsgrupper.  

### <a name="registered-server"></a>Registrerad server
Registrerad server-objektet representerar en förtroenderelation mellan din server (eller kluster) och Storage Sync-tjänsten. Du kan registrera så många servrar till en tjänst för Lagringssynkronisering-instans. Men kan en server (eller kluster) registreras med endast en Storage Sync-tjänsten i taget.

### <a name="azure-file-sync-agent"></a>Azure File Sync-agenten
Azure File Sync-agenten är hämtningsbara paket som gör det möjligt för Windows Server till att synkroniseras med en Azure-filresurs. Azure File Sync-agenten har tre huvudkomponenter: 
- **FileSyncSvc.exe**: bakgrunden Windows-tjänst som ansvarar för att övervaka ändringar på server-slutpunkter och för att initiera synkroniseringssessioner till Azure.
- **StorageSync.sys**: The Azure File Sync filsystemsfilter, som ansvarar för lagringsnivåer filer till Azure Files (när molnet lagringsnivåer är aktiverad).
- **PowerShell-cmdletar för hantering av**: PowerShell-cmdletar som används för att interagera med Microsoft.StorageSync Azure-resursprovidern. Du hittar dem på följande platser (standard):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Server-slutpunkt
En serverslutpunkt representerar en specifik plats på en registrerad server, till exempel en mapp på en server-volym. Flera serverslutpunkter kan finnas på samma volym om deras namnområden inte överlappar (till exempel `F:\sync1` och `F:\sync2`). Du kan konfigurera molnet lagringsnivåer principer separat för varje serverslutpunkt. 

Du kan skapa en serverslutpunkt via en monteringspunkt. Observera att monteringspunkter inom Serverslutpunkten hoppas över.  

Du kan skapa en serverslutpunkt på systemvolymen, men det finns två begränsningar om du gör det:
* Molnet lagringsnivåer kan inte aktiveras.
* Snabb namnområde återställning (där systemet snabbt stänger ned hela namnområdet och börjar sedan att återkalla innehåll) utförs inte.


> [!Note]  
> Endast icke flyttbara volymer stöds.  Enheter som är mappad från en fjärresurs stöds inte för slutpunktsökväg till en server.  Dessutom kan en serverslutpunkt kan finnas i Windows i molnet men systemvolymen lagringsnivåer stöds inte på systemvolymen.

Om du lägger till en plats på servern som har en befintlig uppsättning filer som en serverslutpunkt till en synkroniseringsgrupp slås filerna samman med andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

### <a name="cloud-endpoint"></a>Molnslutpunkt
En slutpunkt i molnet är en Azure-filresurs som ingår i en synkroniseringsgrupp. Hela Azure filen resurs synkroniseringar och en Azure-filresurs kan vara medlem i endast en molnslutpunkt. En Azure-filresurs kan därför vara medlem i endast en synkroniseringsgrupp. Om du lägger till en Azure-filresurs som har en befintlig uppsättning filer som en slutpunkt i molnet till en synkroniseringsgrupp slås de befintliga filerna samman med andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

> [!Important]  
> Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt bara en gång var 24: e timme. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering. Mer information finns i [Azure Files vanliga frågor och svar](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Lagringsnivåer för moln är en valfri funktion i Azure File Sync som sällan används eller komma åt filer som är större än 64 KiB i storlek kan vara nivåindelad till Azure Files. När en fil är nivåindelad ersätter Azure File Sync filsystemsfilter (StorageSync.sys) filen lokalt med en pekare eller en referenspunkt. Referenspunkten representerar en URL till filen i Azure Files. En nivåindelad fil har den ”offline” replikuppsättningen i NTFS, så att program från tredje part kan identifiera nivåindelade filer. När en användare öppnar en nivåindelad fil, återkallar Azure File Sync sömlöst fildata från Azure-filer utan att användaren behöver du veta att filen inte lagras lokalt på systemet. Den här funktionen kallas även hierarkisk Storage Management (HSM).

> [!Important]  
> Molnet lagringsnivåer stöds inte för serverslutpunkter på volymer för Windows-system.

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync-systemkrav och samverkan 
Det här avsnittet beskrivs systemkraven för Azure File Sync-agenten och samverkan med Windows Server-funktioner och roller och lösningar från tredje part.

### <a name="evaluation-tool"></a>Utvärderingsverktyg för
Innan du distribuerar Azure File Sync, bör du utvärdera om den är kompatibel med ditt system med hjälp av verktyget Azure File Sync-utvärdering. Det här verktyget är ett AzureRM PowerShell-cmdlet som söker efter potentiella problem med ditt filsystem och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Observera att dess kontroller omfatta de flesta, men inte alla funktioner nedan; Vi rekommenderar att du läser igenom resten av det här avsnittet noga för att se till att distributionen går smidigt. 

#### <a name="download-instructions"></a>Hämta anvisningar
1. Se till att du har den senaste versionen av PackageManagement och PowerShellGet installerat (Detta kan du installera förhandsversionsmoduler)
    
    ```PowerShell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. Starta om PowerShell
3. Installera modulerna
    
    ```PowerShell
        Install-Module -Name AzureRM.StorageSync -AllowPrerelease
    ```

#### <a name="usage"></a>Användning  
Du kan anropa verktyget utvärdering på ett par olika sätt: du kan utföra Systemkontroller, datauppsättning kontroller eller båda. Om du vill utföra både system- och datauppsättningen kontrollerar du: 

```PowerShell
    Invoke-AzureRmStorageSyncCompatibilityCheck -Path <path>
```

Så här testar endast din datauppsättning:
```PowerShell
    Invoke-AzureRmStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Så här testar endast systemkrav:
```PowerShell
    Invoke-AzureRmStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Så här visar resultatet i CSV:
```PowerShell
    $errors = Invoke-AzureRmStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Systemkrav
- En server som kör Windows Server 2012 R2 eller Windows Server 2016:

    | Version | Godkända SKU: er | Distributionsalternativen som stöds |
    |---------|----------------|------------------------------|
    | Windows Server 2016 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |
    | Windows Server 2012 R2 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |

    Framtida versioner av Windows Server läggs till när de blir tillgängliga. Tidigare versioner av Windows kan läggas till baserat på feedback från användare.

    > [!Important]  
    > Vi rekommenderar att alla servrar som du använder med Azure File Sync uppdaterad med de senaste uppdateringarna från Windows Update. 

- En server med minst 2 GiB minne.

    > [!Important]  
    > Om servern körs på en virtuell dator med dynamiskt minne aktiverat, kommer den virtuella datorn ska konfigureras med en minsta 2048 MiB minne.
    
- En lokalt ansluten volym som formaterats med NTFS-filsystemet.

### <a name="file-system-features"></a>Filsystem: funktioner
| Funktion | Supportstatus | Anteckningar |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Stöds fullt ut | Windows-ACL: er bevaras av Azure File Sync och tillämpas av Windows Server på server-slutpunkter. Windows-ACL: er (ännu inte) stöds av Azure Files om filer används direkt i molnet. |
| Hårda länkar | Hoppades över | |
| Symboliska länkar | Hoppades över | |
| Monteringspunkter | Stöds delvis | Monteringspunkter kan vara roten av en serverslutpunkt, men de hoppas över om de ingår i en serverslutpunkt namnområde. |
| Vägkorsningar | Hoppades över | Till exempel Distributed File System DfrsrPrivate och DFSRoots mappar. |
| Referenspunkter | Hoppades över | |
| NTFS-komprimering | Stöds fullt ut | |
| Sparse-filer | Stöds fullt ut | Synkronisering av sparse-filer (blockeras inte), men de synkroniseras till molnet som en fullständig fil. Ändrar filinnehållet i molnet (eller på en annan server), är filen inte längre sparse när ändringen har hämtats. |
| Alternativa dataströmmar (ADS) | Bevaras, men inte synkroniserad | Till exempel synkroniseras inte taggar för dataklassificering som skapats av Filklassificeringsinfrastrukturen. Befintliga klassificeringstaggar på varje server-slutpunkter är orörda. |

> [!Note]  
> NTFS-volymer stöds. ReFS, FAT, FAT32 och andra filsystem stöds inte.

### <a name="files-skipped"></a>Filer som hoppades över
| Fil/mapp | Obs! |
|-|-|
| Desktop.ini | Filen som är specifika för system |
| ethumbs.DB$ | Temporär fil för miniatyrbilder |
| ~$\*.\* | Tillfällig Office-fil |
| \*.tmp | Temporär fil |
| \*.laccdb | Låsning Access DB-fil|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern Sync-fil|
| \\System Volume Information | Mappen som är specifika för volym |
| $RECYCLE. BIN| Mapp |
| \\SyncShareState | Mapp för synkronisering |

### <a name="failover-clustering"></a>Redundanskluster
Windows Server Failover Clustering stöds av Azure File Sync för ”filserver för allmänt bruk” distributionsalternativ. Redundansklustring stöds inte på ”Scale-Out File Server for application data” (SOFS) eller på klusterdelade volymer (CSV).

> [!Note]  
> Azure File Sync-agenten måste installeras på varje nod i ett redundanskluster för synkronisering ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
För volymer som inte har molnlagringsnivåer aktiverad, stöder Azure File Sync Windows Server-Datadeduplicering håller på att aktiveras på volymen. Vi stöder för närvarande inte samverkan mellan Azure File Sync med molnlagringsnivåer aktiverad och Datadeduplicering.

### <a name="distributed-file-system-dfs"></a>Distribuerat filsystem (DFS)
Azure File Sync har stöd för interop med DFS-namnområden (DFS-N) och DFS Replication (DFS-R) från och med [Azure File Sync-agenten 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**DFS-namnområden (DFS-N)**: Azure File Sync stöds fullt ut på DFS-N-servrar. Du kan installera Azure File Sync-agenten på en eller flera DFS-N-medlemmar att synkronisera data mellan slutpunkterna för server och molnslutpunkten. Mer information finns i [översikt över DFS-namnområden](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-R)**: eftersom DFS-R- och Azure File Sync är båda replikeringslösningar, i de flesta fall rekommenderar vi ersätta DFS-R med Azure File Sync. Det finns dock flera scenarier där du vill använda DFS-R- och Azure File Sync tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure File Sync-distribution. Mer information finns i [migrera en distribution av DFS Replication (DFS-R) till Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Alla lokala servrar som behöver en kopia av filinformationen kan anslutas direkt till internet.
- Grenen servrar konsolidera data på en enda navserver som du vill använda Azure File Sync.

För Azure File Sync och DFS-R att fungera sida-vid-sida:

1. Azure File Sync cloud lagringsnivåer måste inaktiveras på volymer med DFS-R replikerade mappar.
2. Serverslutpunkter ska inte konfigureras för DFS-R skrivskyddad replikering mappar.

Mer information finns i [översikt över DFS Replication](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Med hjälp av sysprep på en server som har Azure File Sync-agenten installerad stöds inte och kan leda till oväntade resultat. Agentregistreringen för installation och server ska inträffa efter distribution av server-avbildning och slutföra sysprep mini-installationen.

### <a name="windows-search"></a>Windows Search
Om molnet lagringsnivåer är aktiverat på en serverslutpunkt, filer som är nivåindelade överhoppade och indexeras inte av Windows Search. Icke-nivåindelade filer indexeras korrekt.

### <a name="antivirus-solutions"></a>Antiviruslösningar
Eftersom antivirus fungerar genom att skanna filer för känd skadlig kod, kan ett antivirusprogram orsaka återkallande av nivåindelade filer. Eftersom nivåindelade filer har attributet ”offline” ange, rekommenderar vi att du samråd med programvaruleverantören att lära dig hur du konfigurerar sin lösning om du vill hoppa över läsning av offline-filer. 

Följande lösningar är kända för att stödja hoppar över offline-filer:

- [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - Windows Defender hoppar automatiskt över läsning av filer offline-attributet ange. Vi har testat Defender och identifierat en mindre problem: när du lägger till en server i en befintlig synkroniseringsgrupp filer mindre än 800 byte hämtas (hämtas) på den nya servern. Dessa filer finns kvar på den nya servern och kommer inte att vara nivåindelad eftersom de inte uppfyller kravet på lagringsnivåer storlek (> 64kb).
- [System Center Endpoint Protection (SCEP)](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - SCEP fungerar på samma sätt som Defender; se ovan
- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (se ”Genomsök vad du behöver bara” på sidan 90 i PDF-filen)
- [Kaspersky antivirus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Lösningar för säkerhetskopiering
Som antivirus orsaka säkerhetskopieringslösningar återkallande av nivåindelade filer. Vi rekommenderar att du använder en lösning för säkerhetskopiering av molnet för att säkerhetskopiera Azure-filresursen i stället för en lokal säkerhetskopiering produkt.

Om du använder en lösning för säkerhetskopiering av en lokal ska säkerhetskopiering utföras på en server i synkroniseringsgruppen som har molnlagringsnivåer inaktiverad. När du återställer filer i slutpunkten serverplatsen, använder du alternativet för återställning på fil. Filer som har återställts kommer att synkroniseras till alla slutpunkter i synkroniseringsgruppen och befintliga filer kommer att ersättas med den version som återställts från en säkerhetskopia.

> [!Note]  
> Programmedveten, återställningsalternativ för volymnivå och utan operativsystem (BMR) kan orsaka oväntade resultat och stöds inte för närvarande. Dessa återställning alternativ kommer att stödjas i framtida versioner.

### <a name="encryption-solutions"></a>Krypteringslösningar
Stöd för krypteringslösningar beror på hur de implementeras. Azure File Sync är känt att arbeta med:

- BitLocker-kryptering
- Azure Information Protection, Azure Rights Management Services (Azure RMS), och Active Directory RMS

Azure File Sync känns inte fungerar med:

- NTFS krypterade filsystem (EFS)

I allmänhet Azure File Sync ska ha stöd för samverkan med krypteringslösningar som finns nedanför filsystem, till exempel BitLocker, och med lösningar som är implementerade i filformat, till exempel Azure Information Protection. Inga särskilda interoperabilitet har gjorts för lösningar som finns ovanför filsystemet (till exempel NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra lösningar för hantering av hierarkisk lagring (HSM)
Ingen annan HSM-lösning bör användas tillsammans med Azure File Sync.

## <a name="region-availability"></a>Regional tillgänglighet
Azure File Sync är tillgänglig i följande regioner:

| Region | Plats för datacenter |
|--------|---------------------|
| Östra Australien | New South Wales |
| Sydöstra Australien | Victoria |
| Centrala Kanada | Toronto |
| Östra Kanada | Quebec City |
| Indien, centrala | Pune |
| Centrala USA | Iowa |
| Östasien | Hongkong SAR |
| Östra USA | Virginia |
| USA, östra 2 | Virginia |
| Norra Europa | Irland |
| Södra Indien | Chennai |
| Sydostasien | Singapore |
| Storbritannien, södra | London |
| Storbritannien, västra | Cardiff |
| Västra Europa | Nederländerna |
| Västra USA | Kalifornien |

Azure File Sync stöder synkronisering endast med en Azure-filresurs som finns i samma region som Storage Sync-tjänsten.

### <a name="azure-disaster-recovery"></a>Azure-haveriberedskap
För att skydda mot förlust av en Azure-region, Azure File Sync kan integreras med den [geo-redundant lagringsredundans](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS)-alternativet. GRS-lagringen fungerar med hjälp av asynkrona Blockreplikering mellan lagring i den primära regionen, som du normalt interagerar, och lagring i den parade sekundära regionen. Vid katastrofåterställning vilket gör att en Azure-region till offlineläge tillfälligt eller permanent misslyckas Microsoft över lagring till den parade regionen. 

Stöd för redundans-integrering mellan geo-redundant lagring och Azure File Sync kan är alla regioner för Azure File Sync kopplad till en sekundär region som matchar den sekundära regionen som används av storage. Dessa par är följande:

| Primär region      | Länkad region      |
|---------------------|--------------------|
| Östra Australien      | Sydöstra Australien |
| Sydöstra Australien | Östra Australien     |
| Centrala Kanada      | Östra Kanada        |
| Östra Kanada         | Centrala Kanada     |
| Indien, centrala       | Södra Indien        |
| Centrala USA          | USA, östra 2          |
| Östasien           | Sydostasien     |
| Östra USA             | Västra USA            |
| USA, östra 2           | Centrala USA         |
| Norra Europa        | Västra Europa        |
| Södra Indien         | Indien, centrala      |
| Sydostasien      | Östasien          |
| Storbritannien, södra            | Storbritannien, västra            |
| Storbritannien, västra             | Storbritannien, södra           |
| Västra Europa         | Norra Europa       |
| Västra USA             | Östra USA            |

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Överväg att brandvägg och proxyinställningar](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
