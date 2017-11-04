---
title: "Planera för distribution av en Azure-filsynkronisering (förhandsversion) | Microsoft Docs"
description: "Lär dig vad du bör tänka på när du planerar för distribution av en Azure-filer."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d626f71aa21cea562ef6c9554c05e6de027e7f4d
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planera för distribution av en Azure-filsynkronisering (förhandsgranskning)
Med Azure File Sync (förhandsversionen) kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Detta möjliggörs genom att Windows-servern omvandlas till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Den här guiden beskriver vad du bör tänka på när du distribuerar Azure filsynkronisering. Vi rekommenderar att du läser [planera för distribution av en Azure-filer](storage-files-planning.md) guide för testning. 

## <a name="understanding-azure-file-sync-terminology"></a>Förstå Azure filsynkronisering terminologi
Innan du fortsätter till Azure filsynkronisering information är det viktigt att du förstår termer.

### <a name="storage-sync-service"></a>Storage-synkroniseringstjänsten
Synkroniseringstjänsten lagring är den översta Azure-resurs som representerar Azure filsynkronisering. Storage-synkroniseringstjänsten resursen är en peer för resursen Storage-konto och kan similarily distribueras till grupper för Azure-resurs. En distinkta översta resurs från resursen för Storage-konto krävs eftersom synkroniseringstjänsten för lagring kan skapa synkroniseringsrelationer med flera lagringskonton via flera synkroniseringsgrupper. En prenumeration kan ha flera lagring synkroniseringstjänsten resurser har distribuerats.

### <a name="sync-group"></a>Synkronisera grupp
En synkronisering grupp definierar sync-topologin för en uppsättning filer. Slutpunkter inom en grupp för synkronisering kommer att synkroniseras med varandra. Om du till exempel har två distinkta grupper av filer som du vill hantera med AFS, skulle du skapa två synkroniseringsgrupper och lägga till olika slutpunkter i varje. Storage-synkroniseringstjänsten kan vara värd för så många synkroniseringsgrupper.  

### <a name="registered-server"></a>Registrerad Server
Registrerad Server-objektet representerar en förtroenderelation mellan din server (eller ett kluster) och lagring-synkroniseringstjänsten. Du kan registrera så många servrar till Storage Sync Service-instans som du vill. Dock kan en server (eller kluster) endast registreras med en Storage-synkroniseringstjänsten vid en given tidpunkt.

### <a name="azure-file-sync-agent"></a>Azure filsynkronisering agent
Azure filsynkronisering agenten är hämtningsbara paket delar som aktiverar en Windows-Server som ska synkroniseras med en Azure-fil. Azure filsynkronisering agenten består av tre huvudkomponenter: 
- **FileSyncSvc.exe**: bakgrunden Windows-tjänst som ansvarar för att övervaka ändringar på servern slutpunkter och initierar synkroniseringssessioner till Azure.
- **StorageSync.sys**: Azure filsynkronisering filsystemet filtrera ansvarar lagringsnivåer filer till Azure Files (när molnet skiktning är aktiverad).
- **PowerShell cmdlet: ar**: PowerShell-cmdlets för att interagera med Resursprovidern Microsoft.StorageSync Azure. Dessa kan hittas på följande platser (som standard):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Serverslutpunkt
En Serverslutpunkt representerar en specifik plats på en registrerad Server, till exempel en mapp på en server-volym eller i roten på volymen. Flera Server-slutpunkter kan finnas på samma volym om deras namnområden inte överlappar (t.ex. F:\sync1 och F:\sync2). Du kan konfigurera molnet lagringsnivåer principer separat för varje Server-slutpunkt. Om du lägger till en plats med en befintlig uppsättning filer på servern som en Server-slutpunkt i en grupp för synkronisering av slås filerna samman med andra filer redan på andra slutpunkter i gruppen synkronisering.

### <a name="cloud-endpoint"></a>Molnslutpunkt
En Molnslutpunkt är en Azure-filresurs som ingår i en grupp för synkronisering. Hela Azure File share synkroniseringar och en filresurs på Azure endast kan en medlem i en Molnslutpunkt och därför en Sync-grupp. Om du lägger till en filresurs på Azure med en befintlig uppsättning filer som en Molnslutpunkt till en grupp för synkronisering av slås filerna samman med andra filer redan på andra slutpunkter i gruppen synkronisering.

> [!Important]  
> Azure filsynkronisering stöder dela ändringarna till Azure-filen direkt, men Observera att alla ändringar på Azure-filresursen först måste identifieras av en Azure-filen ändra identifiering synkroniseringsjobb, vilket är endast initatiated för en Molnslutpunkt var 24: e timme. Finns det [Azure filer FAQ](storage-files-faq.md#afs-change-detection) för mer information.

### <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Molnet skiktning är en valfri funktion för Azure filsynkronisering, vilket möjliggör sällan används eller få åtkomst till filer till nivåer till Azure Files. När en fil är nivåer Azure filsynkronisering filsystemet filtrera (StorageSync.sys) ersätter filen lokalt med en pekare eller referenspunkt som representerar en URL till filen i Azure-filer. En skiktad har attributet ”offline” ange i NTFS, så att program från tredje part kan identifiera nivåindelade filer. När en användare öppnar en skiktad fil, återkallar filsynkronisering Azure sömlöst fildata från Azure-filer utan att användaren kan filen inte lagras lokalt på datorn. Den här funktionen kallas även hierarkiska Storage Management (HSM).

## <a name="azure-file-sync-interoperability"></a>Azure File Sync samverkan 
Det här avsnittet beskriver Azure filsynkronisering samverkan med Windows Server-funktioner och roller och lösningar från tredje part.

### <a name="supported-versions-of-windows-server"></a>Vilka versioner av Windows Server
Vilka versioner av Windows Server med Azure filsynkronisering är för närvarande:

| Version | Stöds SKU: er | Alternativ för distribution som stöds |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter och Standard | Fullständig (Server med ett gränssnitt) |
| Windows Server 2012 R2 | Datacenter och Standard | Fullständig (Server med ett gränssnitt) |

När de är tillgängliga och äldre versioner av Windows kan läggas till baserat på Användarfeedback läggs framtida versioner av Windows Server.

> [!Important]  
> Vi rekommenderar att alla Windows-servrar som används med Azure File synkronisering av uppdaterad med de senaste uppdateringarna från Windows Update. 

### <a name="file-system-features"></a>Filsystem: funktioner
| Funktion | Stöd för Status | Anteckningar |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Fullt stöd | Windows ACL: er bevaras av Azure filen synkronisering och tillämpas av Windows Server på Server-slutpunkter. Windows ACL: er (ännu inte) stöds av Azure-filer om filer kan nås direkt i molnet. |
| Hårda länkar | Hoppades över | |
| Symboliska länkar | Hoppades över | |
| Monteringspunkter | Delvis | Monteringspunkter kan vara roten för en slutpunkt för Server, men kommer att hoppas över om finns i Server Endpoint namnområde. |
| Vägkorsningar | Hoppades över | |
| Referenspunkter | Hoppades över | |
| NTFS-komprimering | Fullt stöd | |
| Sparse-filer | Fullt stöd | Sparse-filer synkroniseras (inte blockeras), men kommer att synkroniseras till molnet som en fullständig fil. Ändrar filens innehåll i molnet (eller på en annan server), vara filen inte längre sparse, när ändringen har hämtats |
| Alternativa dataströmmar (ADS) | Bevaras, men inte synkroniserats | |

> [!Note]  
> NTFS-volymer stöds.

### <a name="failover-clustering"></a>Failover-kluster
Windows Server Failover Clustering stöds av Azure filsynkronisering för alternativet ”filserver för allmänt bruk” distribution. Redundanskluster stöds inte på ”skalbar filserver för programdata” (SOFS) eller på klustrade klusterdelade volymer (CSV).

> [!Note]  
> Azure filsynkronisering agenten måste installeras på varje nod i ett redundanskluster för synkronisering ska fungera korrekt.

### <a name="windows-server-data-deduplication"></a>Datadeduplicering i Windows Server
För volymer utan molnet skiktning aktiverade stöder Azure filsynkronisering Datadeduplicering är aktiverat på volymen. Vi stöder inte interop mellan Azure filsynkronisering med moln skiktning aktiverad och Datadeduplicering just nu.

### <a name="anti-virus-solutions"></a>Ett virusskyddsprogram lösningar
Eftersom ett virusskyddsprogram fungerar genom att skanna filer för känd skadlig kod, kan antivirusprogram orsaka återkallar nivåindelade filer. Eftersom nivåindelade filer har ”offline” attributet, rekommenderar vi samråd med din programvaruleverantör om hur du konfigurerar sitt lösning för att hoppa över läsa offlinefiler. 

Följande lösningar är kända för att stödja hoppar över offline-filer:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee slutpunktssäkerhet](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (se ”Genomsök bara vad du behöver” avsnitt på sidan 90)
- [Kaspersky antivirusprogram](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Säkerhetskopieringslösningar
Som ett virusskyddsprogram lösningar kanske säkerhetskopieringslösningar återkallar nivåindelade filer. Vi rekommenderar med en säkerhetskopieringslösning som molnet för säkerhetskopiering till Azure-filresursen i stället en lokal säkerhetskopiering produkt.

### <a name="encryption-solutions"></a>Krypteringslösningar
Stöd för krypteringslösningar beror på hur de är implementerade. Azure filsynkronisering är känt att arbeta med:

- BitLocker-kryptering
- Azure RMS (och äldre Active Directory RMS)

Azure filsynkronisering känns inte arbeta med:

- NTFS krypterat filsystem (EFS)

I allmänhet Azure filsynkronisering ska kunna interop med krypteringslösningar som visas nedan filsystem, till exempel BitLocker och lösningar som implementerats i filformat, till exempel BitLocker, men inga särskilda interop har gjorts att interop med lösningar som ovan filsystem (till exempel krypteras filsystemet).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra lösningar för hantering av hierarkisk lagring (HSM)
Ingen hierarkisk lagringshantering-lösning bör inte användas med Azure filsynkronisering.

## <a name="region-availability"></a>Regional tillgänglighet
Azure filsynkronisering är bara tillgängligt i följande områden i förhandsgranskningen:

| Region | Datacenter-plats |
|--------|---------------------|
| Västra USA | California, USA |
| Västra Europa | Nederländerna |
| Sydostasien | Singapore |
| Östra Australien | Nya Syd Wales, Australien |

I förhandsgranskningen kommer stöder vi bara synkronisera med en Azure-filresurs i samma region som synkroniseringstjänsten för lagring.

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av en Azure-filer](storage-files-planning.md)
* [Distribuera Azure-filer](storage-files-deployment-guide.md)
* [Distribuera Azure filsynkronisering](storage-sync-files-deployment-guide.md)
