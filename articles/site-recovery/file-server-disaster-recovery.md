---
title: "Skydda en filserver med hjälp av Azure Site Recovery"
description: "Den här artikeln beskriver hur du kan skydda en filserver med hjälp av Azure Site Recovery"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: cc585d6add9b9c5ce7f3379aeaf5ec79f5c61d51
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2017
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Skydda en filserver med hjälp av Azure Site Recovery 

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi genom att hålla dina företagsappar som är tillgängliga under planerade och oplanerade avbrott. Site Recovery samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning av olika arbetsbelastningar hanterar.

Den här artikeln beskriver hur du kan skydda en filserver med hjälp av Azure Site Recovery och andra rekommendationer som passar olika miljöer.

## <a name="file-server-architecture"></a>Filen server-arkitektur
En öppen, distribuerade fildelning system ger en miljö där geografiskt distribuerade användare kan samarbeta på filer utan att kompromissa med integritet krav. 

En typisk lokala filen server-ekosystemet som stöder ett stort antal samtidiga användare och innehållsobjekt använder DFSR Distributed File System Replication () för replikering schemaläggning och begränsning av bandbredd. DFSR använder komprimeringsalgoritm komprimering RDC (Remote Differential) som kan användas för att effektivt uppdatera filer i ett nätverk med begränsad bandbredd. RDC identifierar tillägg, borttagningar och omflyttningar av data i filer. Den gör DFSR att replikera de ändrade filblocken när filer uppdateras. 

I vissa miljöer för server tas dagliga säkerhetskopior vid låg belastning tidpunkter för katastrofåterställning. Dessa miljöer använder inte DFSR.

Följande topologin visar en filen server-miljö med DFSR införts:
                
![DFSR-arkitektur](media/site-recovery-file-server/dfsr-architecture.JPG)

I figur visar delta flera filservrar (kallas medlemmar) aktivt i replikering av filer mellan en replikeringsgrupp. Innehållet i den replikerade mappen är tillgängliga för alla klienter som skickar begäranden till antingen medlemmar, även om en av medlemmarna försätts offline.

## <a name="disaster-recovery-strategies-for-file-servers"></a>Strategi för katastrofåterställning för filservrar

- **Replikera en server till Azure med hjälp av Azure Site Recovery**: när en eller flera av de lokala filservrarna är tillgänglig, recovery virtuella datorer kan tas i Azure. Återställa virtuella datorer kan sedan hantera begäranden från klienter, lokalt, om det finns en plats-till-plats VPN-anslutning och Active Directory har konfigurerats i Azure. Du kan göra detta i en miljö som konfigurerats av DFSR eller i en enkel fil servermiljö med ingen DFSR. 

- **Utöka DFSR till en Azure IaaS-VM**: I en klustrad filserver servermiljö med DFSR implementerats föreslås en metod är att utöka lokala DFSR till Azure. En virtuell Azure-dator kan utföra filserverrollen. 

    När beroenden för plats-till-plats VPN-anslutning och Active Directory ska hanteras och DFSR är på plats när en eller flera av de lokala filservrarna som inte är tillgängligt, kan klienterna fortfarande att ansluta till Azure-VM. Den virtuella Azure-datorn fungerar begäranden.

    Vi rekommenderar den här metoden om dina virtuella datorer har konfigurationer som inte har stöd för Azure Site Recovery. Ett exempel på en sådan konfiguration är en delad klusterdisk som vanligtvis används i filen server-miljöer. DFSR också fungerar bra i miljöer med låg bandbredd med medelhög omsättningen. Med den här metoden måste du utföra ytterligare kostnaden för att ha en Azure VM och igång hela tiden.  

- **Använd Azure filsynkronisering för att replikera dina filer**: Om du förbereder för din resa till molnet eller redan använder en Azure VM, vi rekommenderar användning av Azure filsynkronisering. Den här tjänsten får synkroniseringen av helt hanterad filresurser i molnet som är tillgängliga via standardmässiga [Server Message Block ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)protokollet (SMB). Du kan sedan montera Azure-filresurser samtidigt genom molnet eller lokala distributioner av Windows, Linux och macOS. 

I följande diagram kan hjälpa dig att avgöra vilka strategin för din servermiljö för filen:

![beslutsträdet](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-for-making-a-disaster-recovery-decision"></a>Faktorer för att fatta ett beslut för disaster recovery

|Miljö  |Rekommendation  |Saker att tänka på |
|---------|---------|---------|
|Filen server-miljö med eller utan DFSR|   [Använda Azure Site Recovery för replikering](#replicate-an-on-premises-file-server-by-using-azure-site-recovery)   |    Site Recovery stöder inte delade diskkluster eller NAS. Om din miljö har något av de här konfigurationerna kan använda någon av andra metoder. <br> Azure Site Recovery stöder inte SMB 3.0. Den replikerade virtuella datorn kommer att innehålla ändringar i filerna endast när uppdateras ändringar i den ursprungliga platsen för filen. 
|Filen server-miljö med DFSR     |  [Utöka DFSR till en virtuell Azure IaaS-dator](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR fungerar bra i bandbredd crunched miljöer. Den här metoden kräver dock att du har en Azure VM igång hela tiden. Planeringen måste kontot för kostnaden för den virtuella datorn.         |
|Azure IaaS-VM     |     [Använda Azure File Sync](#use-azure-file-sync-to-replicate-your-on-premises-files)   |     Om du använder Azure filsynkronisering under växling vid fel, i en katastrofåterställning måste du utföra manuella åtgärder för att kontrollera att filresurserna är tillgängliga på ett transparent sätt till klientdatorn. Azure filsynkronisering kräver port 445 öppnas från klientdatorn.     |


### <a name="site-recovery-support"></a>Site Recovery-stöd
Eftersom Site Recovery replikering är oberoende av programmet, förväntas de rekommendationer som anges här gäller för följande scenarier:
| Källa    |Till en sekundär plats    |Till Azure
|---------|---------|---------|
|Azure| -|Ja|
|Hyper-V|   Ja |Ja
|VMware |Ja|   Ja
|Fysisk server|   Ja |Ja
 

> [!IMPORTANT]
> Se till att adressen beroenden innan du fortsätter med någon av följande metoder.

**Plats-till-plats-anslutning**: en direkt anslutning mellan den lokala platsen och Azure-nätverk måste upprättas för att tillåta kommunikation mellan servrar. Du kan använda en säker plats-till-plats VPN-anslutning till Azure-nätverk som ska användas som disaster recovery-plats. Mer information finns i [skapa en plats-till-plats-anslutning i Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**: DFSR beror på Active Directory. Det innebär att Active Directory-skog med lokala domänkontrollanter är utökat till webbplatsen disaster recovery i Azure. Även om du inte använder DFSR, om de avsedda användarna behöver vara beviljas/verifieras för åtkomst, måste du utföra dessa steg.
Mer information finns i [utöka lokala Active Directory till Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendations-for-azure-iaas-virtual-machines"></a>Disaster recovery rekommendationer för Azure IaaS-virtuella datorer

Om du ska konfigurera och hantera katastrofåterställning av filservrar som finns på Azure IaaS-VM, kan du välja mellan två alternativ: Azure filsynkronisering och Azure Site Recovery. Beslutet beror på om du vill flytta till [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

### <a name="use-azure-file-sync-to-replicate-files-hosted-on-iaas-virtual-machines"></a>Använd Azure filsynkronisering replikera filer som finns på IaaS-virtuella datorer

Du kan använda Azure-filer för att helt ersätter eller kompletterar traditionella lokala filservrar eller NAS-enheter. Azure-filresurser kan också replikeras med Azure filsynkronisering till Windows-servrar, antingen lokalt eller i molnet för performant och distribuerad caching data där den används. 

Följ anvisningarna nedan disaster recovery rekommendation för Azure virtuella datorer som utför samma funktion som traditionella filservrar:
1. Skydda datorer med hjälp av Azure Site Recovery igenom de steg som beskrivs här.

2. Använd Azure filsynkronisering replikera filer från den virtuella datorn som fungerar som en filserver till molnet.

3. Använd den [återställningsplanen](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-create-recovery-plans) funktion i Azure Site Recovery för att lägga till skript till [montera filresursen Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

Följande steg beskriver hur du använder tjänsten Azure filsynkronisering kort:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du väljer geo-redundant lagring med läsbehörighet för storage-konton får du tillgång till läsåtkomst till dina data från den sekundära regionen vid en katastrof. Mer information finns i [strategi för Azure File share katastrofåterställning](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

3. [Distribuera Azure filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) på filservern Azure.

4. Skapa en grupp för synkronisering. Slutpunkter inom en synkronisering grupp förblir synkroniserad med varandra. En grupp för synkronisering måste innehålla minst en molnslutpunkt som representerar en Azure-filresurs, och en server-slutpunkt som representerar en sökväg på en Windows-server.  
    Filerna kommer nu är synkroniserade i Azure-filresursen och lokal server.

5. Om det finns en katastrofåterställning i din lokala miljö, utför en redundansväxling med hjälp av en återställningsplan. Lägg till skript till [montera filresursen Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-azure-site-recovery"></a>Replikera en virtuell IaaS filen server-dator med hjälp av Azure Site Recovery

Om du har lokala klienter som har åtkomst till virtuella IaaS file server kan utföra följande steg. I annat fall utför bara steg 3.

1. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverk.  

2. Utöka lokala Active Directory.

3. [Ställ in katastrofåterställning](azure-to-azure-tutorial-enable-replication.md) för IaaS-filen server-dator till en sekundär region.

Mer information om återställning till en sekundär region finns [Azure till Azure replikeringsarkitektur](concepts-azure-to-azure-architecture.md).

## <a name="disaster-recovery-recommendations-for-on-premises-virtual-machines"></a>Disaster recovery rekommendationer för lokala virtuella datorer 

### <a name="replicate-an-on-premises-file-server-by-using-azure-site-recovery"></a>Replikera en filserver för lokalt med hjälp av Azure Site Recovery
Följ anvisningarna nedan replikering för en VMware-VM. Steg för att replikera en virtuell Hyper-V-dator, finns [ställa in haveriberedskap för lokala Hyper-V virtuella datorer till Azure](https://docs.microsoft.com/en-us/azure/site-recovery/tutorial-hyper-v-to-azure).

1. [Förbered Azure-resurser](tutorial-prepare-azure.md) för replikering av lokala datorer.

2. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverk.  

3. Utöka lokala Active Directory.

4. [Förbereda lokal VMware-servrar](tutorial-prepare-on-premises-vmware.md).

5. [Ställ in katastrofåterställning](tutorial-vmware-to-azure.md) till Azure för lokala virtuella datorer.

### <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Utöka DFSR till en virtuell Azure IaaS-dator

1. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverk. 

2. Utöka lokala Active Directory.

3. [Skapa och etablera en filserver VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) på virtuella Azure-nätverket.  
    Se till att den virtuella datorn har lagts till samma virtuella Azure-nätverket, som har cross-anslutning med den lokala miljön. 

4. Installera och [Konfigurera DFS Replication](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) på Windows Server.

5. [Implementera ett DFS-namnområde](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).

6. Med DFS-namnområde som implementerats, kan du växla över delade mappar från produktionen till katastrofåterställningsplatser genom att uppdatera mappmål för DFS-namnområdet. När dessa DFS namnområdet ändringar replikeras via Active Directory användare är anslutna till en lämplig mappmål transparent.

### <a name="use-azure-file-sync-to-replicate-your-on-premises-files"></a>Använd Azure filsynkronisering för att replikera lokala filer
Du kan replikera önskade filer till molnet med hjälp av Azure filen Sync-tjänsten. Du kan sedan montera önskade sökvägar från molnet och fortsätta att hantera begäranden från klientdatorer vid en katastrof och att dina lokala filen server.

Den föreslagna metoden med att integrera Azure filsynkronisering med Azure Site Recovery är:
1. Skydda filen server-datorer med hjälp av Azure Site Recovery. Följ stegen i [konfigurera katastrofåterställning till Azure för VMwares virtuella datorer för lokala](tutorial-vmware-to-azure.md).

2. Använd Azure filsynkronisering replikera filer från datorn som fungerar som en filserver till molnet.

3. Använda funktionen återställning plan i Azure Site Recovery för att lägga till skript för att ansluta till Azure-filresursen på misslyckades över filservern VM i Azure.

Följande steg detalj med hjälp av tjänsten Azure filsynkronisering:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du väljer läsbehörighet geo-redundant lagring (rekommenderas) för dina lagringskonton, kommer du har läsbehörighet till dina data från den sekundära regionen vid en katastrof. Mer information finns i [strategi för Azure File share katastrofåterställning](https://docs.microsoft.com/en-us/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

3. [Distribuera Azure filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) i din lokala filserver.

4. Skapa en grupp för synkronisering. Slutpunkter inom en synkronisering grupp förblir synkroniserad med varandra. En grupp för synkronisering måste innehålla minst en molnslutpunkt som representerar en Azure-filresurs, och slutpunkten för en server, som representerar en sökväg på den lokala Windows-servern.  
    Filerna kommer nu är synkroniserade i Azure-filresursen och lokal server.

5. Om det finns en katastrofåterställning i din lokala miljö, utför en redundansväxling med hjälp av en återställningsplan. Lägg till skript för att montera filresursen Azure och få åtkomst till resursen i den virtuella datorn.

> [!NOTE]
> Kontrollera att port 445 är öppen. Azure Files använder SMB-protokollet. SMB kommunicerar via TCP-port 445. Kontrollera om din brandvägg blockerar port 445 från klientdatorn.


## <a name="perform-a-test-failover"></a>Utför ett redundanstest

1. Gå till Azure-portalen och välj Recovery Services-valvet.

2. Välj återställningsplanen som skapats för filen server-miljö.

3. Välj **Redundanstestningen**.

4. Välj återställningspunkten och Azure-nätverk för att starta processen testa redundans.

5. När den sekundära miljön är in, utföra din verifieringar.

6. När validering har slutförts kan du välja **Rensa redundanstestet** på återställningsplanen och testa redundans miljön har rensats.

Läs mer om hur du utför ett redundanstest [Redundanstestningen till Azure Site Recovery](site-recovery-test-failover-to-azure.md).

Anvisningar om hur du utför testa redundans för Active Directory och DNS finns [Redundanstestning för Active Directory och DNS-](site-recovery-active-directory.md).

## <a name="perform-a-failover"></a>Utför en växling vid fel

1. Gå till Azure-portalen och välj Recovery Services-valvet.

2. Välj återställningsplanen som skapats för filen server-miljö.

3. Välj **redundans**.

4. Välj återställningspunkt för att starta processen för växling vid fel.

Mer information om hur du utför en växling vid fel finns [redundans i Site Recovery](site-recovery-failover.md).
