---
title: Planera för distribution av en Azure-filsynkronisering (förhandsversion) | Microsoft Docs
description: Lär dig vad du bör tänka på när du planerar för distribution av en Azure-filer.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: ebfa7da32859f8d2d0ff3778af3b5cca99bdf1f4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planera för distribution av en Azure-filsynkronisering (förhandsgranskning)
Använda Azure filsynkronisering (förhandsgranskning) för att centralisera din organisations filresurser i Azure-filer, samtidigt som flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure filsynkronisering omvandlar Windows Server till en snabb cache med Azure-filresursen. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt data lokalt, inklusive SMB och NFS FTPS. Du kan ha valfritt antal cacheminnen som du behöver över hela världen.

Den här artikeln beskrivs viktiga överväganden för distribution av en Azure filsynkronisering. Vi rekommenderar att du också läser [planera för distribution av en Azure-filer](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologi för Azure filsynkronisering
Innan du hämtar detaljer om att planera för distribution av en Azure filsynkronisering är det viktigt att du förstår termer.

### <a name="storage-sync-service"></a>Tjänst för synkronisering av lagring
Synkroniseringstjänsten lagring är översta Azure resurs för Azure filsynkronisering. Storage-synkroniseringstjänsten resursen är en peer-till-konto lagringsresurs och på samma sätt kan distribueras till Azure-resursgrupper. En distinkta översta resurs från resursen för storage-konto krävs eftersom synkroniseringstjänsten för lagring kan skapa synkroniseringsrelationer med flera lagringskonton via flera synkroniseringsgrupper. En prenumeration kan ha flera lagring synkroniseringstjänsten resurser har distribuerats.

### <a name="sync-group"></a>Synkroniseringsgrupp
En synkronisering grupp definierar sync-topologin för en uppsättning filer. Slutpunkter inom en synkronisering grupp hålls synkroniserade med varandra. Om du till exempel har två distinkta grupper av filer som du vill hantera med Azure filsynkronisering, skulle du skapa två synkroniseringsgrupper och Lägg till olika slutpunkter varje sync-grupp. Storage-synkroniseringstjänsten kan vara värd för så många synkroniseringsgrupper.  

### <a name="registered-server"></a>Registrerad server
Registrerad server-objektet representerar en förtroenderelation mellan din server (eller ett kluster) och lagring-synkroniseringstjänsten. Du kan registrera så många servrar till en instans av synkroniseringstjänsten för lagring. Dock kan en server (eller kluster) registreras med endast en synkroniseringstjänsten för lagring i taget.

### <a name="azure-file-sync-agent"></a>Azure filsynkronisering agent
Azure filsynkronisering agenten är hämtningsbara paket som gör det möjligt för Windows Server till att synkroniseras med en Azure-filresursen. Azure filsynkronisering agenten har tre delar: 
- **FileSyncSvc.exe**: bakgrunden Windows-tjänst som ansvarar för att övervaka ändringar på servern slutpunkter och initierar synkroniseringssessioner till Azure.
- **StorageSync.sys**: det Azure-filsynkronisering Filsystemfilter, som är ansvarig för lagringsnivåer filer till Azure Files (när molnet skiktning är aktiverad).
- **PowerShell cmdlet: ar**: PowerShell-cmdlets som används för att interagera med resursprovidern Microsoft.StorageSync Azure. Du hittar dem på följande platser (standard):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Serverslutpunkt
En serverslutpunkt representerar en specifik plats på en registrerad server, till exempel en mapp på en server-volym. Flera server-slutpunkter kan finnas på samma volym om deras namnområden inte överlappar (till exempel `F:\sync1` och `F:\sync2`). Du kan konfigurera molnet lagringsnivåer principer individuellt för varje serverslutpunkt. 

Du kan skapa en serverslutpunkt för via en monteringspunkt. Observera att monteringspunkter i serverns slutpunktens hoppas över.  

Du kan skapa en serverslutpunkt för på volymen, men det finns två begränsningar om du gör det:
* Molnet skiktning kan inte aktiveras.
* Namnområdet för snabb återställning (där systemet snabbt stänger ned hela namnområdet och startar sedan att återställa innehållet) utförs inte.


> [!Note]  
> Endast icke flyttbara volymer stöds.  Enheter som mappas från en fjärransluten delad resurs stöds inte för slutpunktsökväg till en server.  Dessutom kan en serverslutpunkt för kan finnas på systemvolymen om molnet Windows skiktning stöds inte på systemvolymen.

Om du lägger till en plats på servern som har en befintlig uppsättning filer som en serverslutpunkt i en grupp för synkronisering av slås filerna samman med andra filer som redan finns på andra slutpunkter i gruppen synkronisering.

### <a name="cloud-endpoint"></a>Molnslutpunkt
En molnslutpunkt är en Azure-filresurs som ingår i en grupp för synkronisering. Hela Azure file share synkroniseringar och en Azure-filresursen kan vara medlem i endast en molnslutpunkt. En Azure-filresurs kan därför vara medlem i gruppen med endast en synkronisering. Om du lägger till en Azure-filresurs som har en befintlig uppsättning filer som en molnslutpunkt till en grupp för synkronisering av slås de befintliga filerna samman med andra filer som redan finns på andra slutpunkter i gruppen synkronisering.

> [!Important]  
> Azure filsynkronisering har stöd för att göra ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av en Azure-filen ändras identifiering synkroniseringsjobb. Ett jobb för identifiering av ändring initieras för en molnslutpunkt bara en gång per dygn. Dessutom kommer inte att uppdatera SMB senast ändrad ändringar som gjorts i en Azure-filresursen via REST-protokollet och ska inte ses som en ändring av synkronisering. Mer information finns i [Azure Files vanliga frågor och](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Molnet skiktning är en valfri funktion för Azure filsynkronisering som sällan används eller filer som är större än 64 KiB i storlek kan nivåindelas till Azure Files. När en fil är nivåer ersätter Azure filsynkronisering Filsystemfilter (StorageSync.sys) filen lokalt med en pekare eller en referenspunkt. Referenspunkten representerar en URL till filen i Azure-filer. En skiktad har ”offline” attributet i NTFS så att program från tredje part kan identifiera nivåindelade filer. När en användare öppnar en skiktad fil, återkallar Azure filsynkronisering sömlöst fildata från Azure-filer utan att användaren behöver veta att filen inte lagras lokalt på datorn. Den här funktionen kallas även hierarkiska Storage Management (HSM).

> [!Important]  
> Molnet skiktning stöds inte för server-slutpunkter på volymer för Windows-system.

## <a name="azure-file-sync-interoperability"></a>Azure filsynkronisering samverkan 
Det här avsnittet beskriver Azure filsynkronisering samverkan med Windows Server-funktioner och roller och lösningar från tredje part.

### <a name="supported-versions-of-windows-server"></a>Vilka versioner av Windows Server
Vilka versioner av Windows Server med Azure filsynkronisering finns för närvarande

| Version | Stöds SKU: er | Alternativ för distribution som stöds |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |
| Windows Server 2012 R2 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |

Framtida versioner av Windows Server läggs när de blir tillgängliga. Tidigare versioner av Windows kan läggas till utifrån feedback från användare.

> [!Important]  
> Vi rekommenderar att alla servrar som du använder med Azure filsynkronisering uppdaterad med de senaste uppdateringarna från Windows Update. 

### <a name="file-system-features"></a>Filsystem: funktioner
| Funktion | Stöd för status | Anteckningar |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Fullt stöd | Windows ACL: er bevaras av Azure filen synkronisering och tillämpas av Windows Server på server-slutpunkter. Windows ACL: er (ännu inte) stöds av Azure-filer om filer kan nås direkt i molnet. |
| Hårda länkar | Hoppades över | |
| Symboliska länkar | Hoppades över | |
| Monteringspunkter | Delvis | Monteringspunkter kan vara roten för en serverslutpunkt för, men de hoppas över som ingår i en serverslutpunkt-namnområdet. |
| Vägkorsningar | Hoppades över | Till exempel Distributed File System DfrsrPrivate och DFSRoots mappar. |
| Referenspunkter | Hoppades över | |
| NTFS-komprimering | Fullt stöd | |
| Sparse-filer | Fullt stöd | Synkronisering av sparse-filer (blockeras inte), men de synkroniseras till molnet som en fullständig fil. Ändrar filens innehåll i molnet (eller på en annan server), är filen inte längre sparse när ändringen har hämtats. |
| Alternativa dataströmmar (ADS) | Bevaras, men inte synkroniserats | Till exempel synkroniserats klassificering taggar som skapats av Filklassificeringsinfrastrukturen har inte. Den befintliga klassificering taggar på filer på varje server-slutpunkter är orörda. |

> [!Note]  
> NTFS-volymer stöds. ReFS, FAT, FAT32 och andra filsystem stöds inte.

### <a name="files-skipped"></a>Filer som hoppades över
| Filen/mappen | Obs! |
|-|-|
| Desktop.ini | Filen som är specifika för system |
| ethumbs.DB$ | Temporär fil för miniatyrer |
| ~$\*.\* | Temporär fil för Office |
| \*tmp | Temporär fil |
| \*.laccdb | Låsning Access DB-fil|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern Sync-fil|
| \\System Volume Information | Mappen som är specifika för volym |
| $RECYCLE. BIN| Mapp |
| \\SyncShareState | Mapp för synkronisering |

### <a name="failover-clustering"></a>Failover-kluster
Windows Server Failover Clustering stöds av Azure filsynkronisering för alternativet ”filserver för allmänt bruk” distribution. Redundanskluster stöds inte på ”skalbar filserver för programdata” (SOFS) eller på klusterdelade volymer (CSV).

> [!Note]  
> Azure filsynkronisering agenten måste installeras på varje nod i ett redundanskluster för synkronisering ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
För volymer som inte har molnet skiktning aktiverade stöder Azure filsynkronisering Windows Server-Datadeduplicering är aktiverat på volymen. Vi stöder för närvarande inte samverkan mellan Azure filsynkronisering med moln skiktning aktiverad och Datadeduplicering.

### <a name="distributed-file-system-dfs"></a>Distribuerat filsystem (DFS)
Azure filsynkronisering stöder interop med DFS-namnområden (DFS-N) och DFS Replication (DFS-R) från och med [Azure filsynkronisering agent 1.2](https://go.microsoft.com/fwlink/?linkid=864522).

**DFS-namnområden (DFS-N)**: Azure filsynkronisering stöds fullt ut på DFS-N-servrar. Du kan installera filsynkronisering för Azure-agenten på en eller flera medlemmar av DFS-N att synkronisera data mellan server-slutpunkter och moln-slutpunkten. Mer information finns i [översikt över DFS-namnområden](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-R)**: eftersom DFS-R eller Azure filsynkronisering båda lösningarna för replikering, i de flesta fall, rekommenderar vi att ersätta DFS-R med Azure filsynkronisering. Det finns dock flera scenarier där du vill använda DFS-R och Azure filsynkronisering tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure-filen Sync-distribution. Mer information finns i [migrera en distribution av DFS Replication (DFS-R) till Azure filsynkronisering](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Alla lokala servrar som behöver en kopia av filen data kan anslutas direkt till internet.
- Grenen servrar konsolidera data på en enda navserver som du vill använda Azure File Sync.

För Azure filsynkronisering och DFS-R ska fungera sida-vid-sida:

1. Azure filsynkronisering molnet lagringsnivåer måste inaktiveras på volymer med DFS-R replikerade mappar.
2. Server-slutpunkter ska inte konfigureras på mappar i DFS-R skrivskyddad-replikering.

Mer information finns i [översikt över DFS Replication](https://technet.microsoft.com/library/jj127250).

### <a name="antivirus-solutions"></a>Antivirusprogram lösningar
Eftersom antivirus fungerar genom att skanna filer för känd skadlig kod, kan ett antivirusprogram orsaka återkallar nivåindelade filer. Eftersom nivåindelade filer har ”offline” attributet, rekommenderar vi att du samråd med programleverantören att lära dig hur du konfigurerar sitt lösning för att hoppa över läsa offlinefiler. 

Följande lösningar är kända för att stödja hoppar över offline-filer:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee slutpunktssäkerhet](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (se ”Genomsök bara vad du behöver” på sidan 90 i PDF-filen)
- [Kaspersky antivirusprogram](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Säkerhetskopieringslösningar
Säkerhetskopieringslösningar kan orsaka återkallar nivåindelade filer som antivirus lösningar. Vi rekommenderar att du säkerhetskopierar Azure-filresursen i stället för en lokal säkerhetskopiering produkt med en lösning för säkerhetskopiering av molnet.

### <a name="encryption-solutions"></a>Krypteringslösningar
Stöd för krypteringslösningar beror på hur de är implementerade. Azure filsynkronisering är känt att arbeta med:

- BitLocker-kryptering
- Azure Information Protection, Azure Rights Management Services (Azure RMS) och Active Directory RMS

Azure filsynkronisering känns inte arbeta med:

- NTFS krypterat filsystem (EFS)

I allmänhet bör Azure filsynkronisering stöd för samverkan med krypteringslösningar som visas nedan filsystemet, exempelvis BitLocker, och lösningar som implementeras i filformat, till exempel BitLocker. Inga särskilda samverkan har gjorts för lösningar som är placerade ovanför filsystem (till exempel NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra lösningar för hantering av hierarkisk lagring (HSM)
Ingen annan HSM-lösning bör inte användas med Azure filsynkronisering.

## <a name="region-availability"></a>Regional tillgänglighet
Azure filsynkronisering är endast tillgänglig i följande regioner i förhandsgranskningen:

| Region | Datacenter-plats |
|--------|---------------------|
| Östra Australien | New South Wales |
| Centrala Kanada | Toronto |
| Östra Kanada | Quebec City |
| Centrala USA | Iowa |
| Östasien | Hongkong SAR |
| Östra USA | Virginia |
| Östra US2 | Virginia |
| Norra Europa | Irland |
| Sydostasien | Singapore |
| Storbritannien, södra | London |
| Västra Europa | Nederländerna |
| Västra USA | Kalifornien |

I preview stöder synkroniserar endast med en Azure-filresurs som finns i samma region som synkroniseringstjänsten för lagring.

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Överväg att brandväggar och proxyservrar inställningar](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure-filer](storage-files-deployment-guide.md)
* [Distribuera Azure filsynkronisering](storage-sync-files-deployment-guide.md)
