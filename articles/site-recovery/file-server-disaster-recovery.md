---
title: "Skydda en filserver med hjälp av Azure Site Recovery"
description: "Den här artikeln beskriver hur du skyddar en filserver med hjälp av Azure Site Recovery"
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 779ec70a3b45a0ac3e766c956aac94932d4d126b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="protect-a-file-server-using-azure-site-recovery"></a>Skydda en filserver med hjälp av Azure Site Recovery 

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten bidrar till din affärskontinuitet och haveriberedskap (BCDR) strategi genom att hålla dina appar och köra tillgängliga under planerade och oplanerade avbrott. Site Recovery samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning av olika arbetsbelastningar hanterar.

Den här artikeln beskriver hur du skyddar en filserver med hjälp av Azure Site Recovery och andra rekommendationer som passar olika miljöer.     

- [Replikera Azure IaaS-filservrar](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikera lokala filservern med Azure Site Recovery](#replicate-an-onpremises-file-server-using-azure-site-recovery)

## <a name="file-server-architecture"></a>Filen Server-arkitektur
Syftet med en öppen distribuerade fildelning system är att tillhandahålla en miljö där en grupp geografiskt distribuerade användare kan arbeta arbete med filer och garanteras att deras integritet krav gäller. Ett typiskt lokala filserver ekosystem som stöder ett stort antal samtidiga användare och ett stort antal artiklar använder DFSR Distributed File System Replication () för replikering schemaläggning och begränsning av bandbredd. DFSR använder en komprimeringsalgoritm kända som komprimering RDC (Remote Differential), som kan användas för att effektivt uppdatera filer i ett nätverk med begränsad bandbredd. Den identifierar tillägg, borttagningar och omflyttningar av data i filer, aktivera DFSR att replikera de ändrade filblocken när filer uppdateras. Det finns också filserver miljöer där dagliga säkerhetskopior vidtas i arbetsbelastningen tidsinställningar tillgodoser krasch och det finns ingen implementering av DFSR.

Följande topologin illustrerar File Server-miljö med DFSR implementeras.
                
![dfsr-architexture](media/site-recovery-file-server/dfsr-architecture.JPG)

I ovanstående reference flera filservrar som anges som medlemmar, aktivt delta vid replikering av filer mellan en replikeringsgrupp. Innehållet i den replikerade mappen kommer att vara tillgänglig för alla klienter som skickar begäranden till antingen medlemmar, även vid en av medlemmarna försätts i offlineläge.

## <a name="disaster-recovery-recommendation-for-file-servers"></a>Disaster recovery rekommendation för filservrar:

1.  Replikera en filserver med hjälp av Azure Site Recovery: filservrar kan replikeras till Azure med Azure Site Recovery. När en eller flera av de filen lokalt inte är tillgänglig, recovery virtuella datorer kan tas i Azure, vilket kan sedan fungera förfrågningar från klienter, lokalt, förutsatt att datorns plats-till-plats VPN-anslutning och Active directory som konfigurerats i Azure. Detta kan göras vid en konfigurerad DFSR-miljö eller en enkel fil servermiljö med ingen DFSR. 

2.  Utöka DFSR till en Azure Iaas-VM: – i en klustrad filserver servermiljö med DFSR implementerats föreslås en metod är att utöka lokala DFSR till Azure. En virtuell Azure-dator aktiveras sedan att utföra filserverrollen. 

    När beroenden för plats-till-plats VPN-anslutning och Active directory ska hanteras och DFSR är på plats när en eller flera av de filen lokalt är tillgänglig, kan klienterna fortfarande att ansluta till Azure VM, som kommer att betjäna förfrågningar.

    Den här metoden föreslås i fall dina virtuella datorer har konfigurationer som inte stöds av Azure Site Recovery som till exempel: delad klusterdisk som ibland används i filen Server-miljöer.  DFSR också fungerar bra i miljöer med låg bandbredd med medelhög omsättningen. Den extra kostnaden för att ha en Azure VM och igång hela tiden måste också finnas med den här.  

3.  Använd Azure filen Sync-tjänsten för att replikera dina filer: Om du förbereder för din resa till molnet, eller redan använder en virtuell dator i Azure och vi rekommenderar användning av Azure File synkroniseringstjänsten, vilket ger synkronisering av helt hanterad filresurser i molnet som är tillgänglig v IA branschstandard [Server Message Block ](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)protokollet (SMB). Azure-filen resurser kan sedan monteras samtidigt av molnet eller lokala distributioner av Windows, Linux och macOS. 

Följande diagram ger en grafiska representation syftar övergång ut beslutet av vilken strategi för din servermiljö och fil.

![decisiontree](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-while-making-decision-of-disaster-recovery-to-azure"></a>Faktorer att tänka på när du fattar beslut för katastrofåterställning till Azure

|Miljö  |Rekommendation  |Saker att tänka på |
|---------|---------|---------|
|Filen Server-miljö med eller utan DFSR|   [Använda Azure Site Recovery för replikering](#replicate-an-onpremises-file-servers-using-azure-site-recovery)   |    Site Recovery stöder inte delade diskkluster kan NAS. Om din miljö använder någon av dessa konfigurationer, använda någon av andra metoder. <br> Azure Site Recovery stöder inte SMB 3.0, vilket innebär att endast när uppdateras ändringar som görs i filerna i den ursprungliga platsen för filen kommer den replikerade virtuella datorn ändringarna.
|Filen Server-miljö med DFSR     |  [Utöka DFSR till en virtuell Azure IaaS-dator:](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |     DFSR fungerar bra i mycket bandbredd crunched miljöer, men kräver den här metoden har en Azure VM upp och igång hela tiden. Kostnaden för den virtuella datorn måste redovisas i planeringen.         |
|Azure Iaas-VM     |     [Azure filsynkronisering](#use-azure-file-sync-service-to-replicate-your-files)   |     I katastrofåterställning om du använder Azure filsynkronisering måste under växling vid fel manuella åtgärder vidtas för att säkerställa att filresurser som är tillgänglig på ett transparent sätt till klientdatorn. AFS kräver port 445 öppnas från klientdatorn.     |


### <a name="site-recovery-support"></a>Site Recovery-stöd
Site Recovery replikering är oberoende av programmet, förväntas de rekommendationer som anges här gäller för följande scenarier:
| Källa    |Till en sekundär plats    |Till Azure
|---------|---------|---------|
|Azure| -|Ja|
|Hyper-V|   Ja |Ja
|VMware |Ja|   Ja
|Fysisk server|   Ja |Ja
 

> [!IMPORTANT]
> Innan du fortsätter med någon av de se till att följande beroenden är åtgärdat nedan tre metoder:

**Plats-till-plats-anslutning**: direkt anslutning mellan lokal plats och Azure-nätverk måste införas för att tillåta kommunikation mellan servrar.  Detta kan säkerställas genom en säker plats-till-plats VPN-anslutning till ett Microsoft Azure-nätverk som ska användas som DR-plats.  
Se: [upprätta plats-till-plats VPN-anslutning mellan lokal plats och Azure-nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

**Active Directory**: DFSR beror på Active Directory.  Det innebär att Active Directory-skog med lokala domänkontrollanter utökas till DR-plats i Azure. Även om du inte använder DFSR om de avsedda användarna måste beviljas / verifieras för åtkomst som i de flesta organisation, måste dessa steg utföras.
Se: [utöka lokala Active Directory till Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Disaster recovery rekommendation för Azure IaaS-virtuella datorer

Om du konfigurerar och hanterar katastrofåterställning av filservrar som finns på Azure IaaS-VM, kan du välja mellan två alternativ, baserat på om du vill flytta till [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

1. [Använda Azure File Sync](#use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine)
2. [Använd Azure Site Recovery](#replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery)

## <a name="use-azure-file-sync-service-to-replicate-files-hosted-on-iaas-virtual-machine"></a>Använd Azure filen Sync-tjänsten för att replikera filer som lagras på virtuell IaaS-dator

**Azure Files** kan användas för att helt ersätter eller kompletterar traditionella lokala filservrar eller NAS-enheter. Azure-filresurser kan också replikeras med Azure-filsynkronisering till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används. Följ anvisningarna nedan DR-rekommendation för Azure virtuella datorer som utför samma funktion som traditionella filservrar:
1.  Skydda datorer med hjälp av Azure Site Recovery med hjälp av anvisningarna [här](azure-to-azure-quickstart.md).
2.  Använd Azure filsynkronisering replikera filer från den virtuella datorn som fungerar som en filserver till molnet.
3.  Använda Azure Site Recovery [återställningsplanen](site-recovery-create-recovery-plans.md) funktion för att lägga till skript till [montera filresursen Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

De beskrivs kortfattat nedanstående steg för hur du använder Azure filen Sync-tjänsten:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du väljer Geo-Redundant lagring med läsbehörighet (RA-GRS) för dina lagringskonton får du tillgång till läsåtkomst till dina data från den sekundära regionen vid en katastrof. Referera till den [strategi för Azure File share katastrofåterställning](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för ytterligare information.
2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Distribuera Azure filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) på filservern Azure.
4. Skapa en grupp för synkronisering: slutpunkter i en grupp för synkronisering kommer att synkroniseras med varandra. En grupp för synkronisering måste innehålla minst en Molnslutpunkt som representerar en Azure-filresurs, och en Server-slutpunkt som representerar en sökväg på en Windows-Server.
5.  Filerna kommer nu att synkroniseras i Azure-filresurs och lokal server.
6.  Vid katastrofåterställning i din lokala miljö, utföra som redundans med en [återställningsplanen](site-recovery-create-recovery-plans.md) och Lägg till skript för att [montera filresursen Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

### <a name="replicate-an-iaas-file-server-virtual-machine-using-azure-site-recovery"></a>Replikera en file server virtuell IaaS-dator med hjälp av Azure Site Recovery

Om du har lokala klienter som ansluter till den virtuella datorn på filen utföra IaaS de två första stegen, samt annan gå vidare till steg 3.

1. Upprätta plats-till-plats VPN-anslutning mellan lokal plats och Azure-nätverk.
1. Utöka lokala Active Directory.
1. [Ställ in katastrofåterställning](azure-to-azure-tutorial-enable-replication.md) för IaaS-filen server-dator till en sekundär region.


Mer information om återställning till en sekundär region [här](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-using-azure-site-recovery"></a>Replikera en lokal filserver som använder Azure Site Recovery

Den under steg detalj replikering för en VMware-VM steg för att replikera en virtuell dator i Hyper-V, se [här](tutorial-hyper-v-to-azure.md).

1.  [Förbered Azure-resurser](tutorial-prepare-azure.md) för replikering av lokala datorer.
2.  Upprätta plats-till-plats VPN-anslutning mellan lokal plats och Azure-nätverk.  
3. Utöka lokala Active Directory.
4.  [Förbereda lokal VMware-servrar](tutorial-prepare-on-premises-vmware.md).
5.  [Ställ in katastrofåterställning](tutorial-vmware-to-azure.md) till Azure för lokala virtuella datorer.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Utöka DFSR till en virtuell Azure IaaS-dator:

1.  Upprätta plats-till-plats VPN-anslutning mellan lokal plats och Azure-nätverk. 
2.  Utöka lokala Active Directory.
3.  [Skapa och etablera en filserver VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) i Windows Azure Virtual Network.
Kontrollera att den virtuella datorn har lagts till på samma Windows Azure Virtual Network, som har mellan anslutningen med den lokala miljön. 
4.  Installera och [Konfigurera DFS Replication](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) på Windows Server.
5.  [Implementera en DFS Namespace](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6.  Med DFS Namespace som implementerats, kan redundans för delade mappar från produktionen till DR platser göras genom att uppdatera mappmål DFS Namespace.  När ändringarna DFS Namespace replikera via Active Directory, är användare anslutna till en lämplig mappmål transparent.

## <a name="use-azure-file-sync-service-to-replicate-your-on-premises-files"></a>Använd Azure filen synkroniseringstjänsten för att replikera lokala filer:
Med hjälp av tjänsten Azure filsynkronisering, kan du replikera önskade filer till molnet, så att vid en katastrof och att dina lokala filen server kan du montera de önskade sökvägar från molnet till och fortsätta att förfrågningar från klientdatorer.
Den föreslagna metoden med att integrera Azure filsynkronisering med Azure Site Recovery är
1.  Skydda filservrar med hjälp av Azure Site Recovery med hjälp av anvisningarna [här](tutorial-vmware-to-azure.md).
2.  Använd Azure filsynkronisering replikera filer från datorn som fungerar som en filserver till molnet.
3.  Använda Azure Site Recovery återställning plan för att lägga till skript om du vill montera Azure filresursen på den över FileServer VM i Azure.

Den under steg poster med hjälp av Azure filen Sync-tjänsten:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du väljer geo-redundant lagring med läsbehörighet (RA-GRS) (rekommenderas) för storage-konton måste ha du läsbehörighet till dina data från den sekundära regionen vid en katastrof. Referera till den [strategi för Azure File share katastrofåterställning](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) för ytterligare information.
2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Distribuera Azure filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) i din lokala filserver.
4. Skapa en grupp för synkronisering: slutpunkter i en grupp för synkronisering kommer att synkroniseras med varandra. En grupp för synkronisering måste innehålla minst en Molnslutpunkt som representerar en Azure-filresurs, och en Server-slutpunkt som representerar en sökväg på den lokala Windows-Server.
1. Filerna kommer nu att synkroniseras i Azure-filresurs och lokal server.
6.  Vid katastrofåterställning i din lokala miljö, utföra som redundans med en [återställningsplanen](site-recovery-create-recovery-plans.md) och lägga till skript för att montera filresursen för Azure och få åtkomst till resursen i den virtuella datorn.

> [!NOTE]
> Kontrollera att port 445 är öppen: Azure-filer som använder SMB-protokollet. SMB kommunicerar via TCP-port 445. Kontrollera om din brandvägg blockerar TCP-port 445 från klientdatorn.


## <a name="doing-a-test-failover"></a>Gör ett redundanstest

1.  Gå till Azure-portalen och välj Recovery-tjänsten-valvet.
2.  Klicka på återställningsplan som skapats för filserver-miljö.
3.  Klicka på Testa redundans.
4.  Välj återställningspunkten och Azure-nätverk för att starta processen testa redundans.
5.  Du kan utföra dina verifieringar när den sekundära miljön är upp.
6.  När validering har slutförts kan du på 'Rensa redundanstestet' återställningsplanen och redundanstestmiljön har rensats.

Mer information om hur du utför redundanstestet [här](site-recovery-test-failover-to-azure.md).

Anvisningar om hur du gör testa redundans för AD och DNS, referera till [redundanstestning för AD- och DNS-](site-recovery-active-directory.md).

## <a name="doing-a-failover"></a>Genomför en redundansväxling enligt

1.  Gå till Azure-portalen och välj Recovery Services-valvet.
2.  Klicka på återställningsplan som skapats för filserver-miljö.
3.  Klicka på 'Redundans'.
4.  Välj återställningspunkt för att starta processen för växling vid fel.

Mer information om hur du utför en växling vid fel [här](site-recovery-failover.md).
