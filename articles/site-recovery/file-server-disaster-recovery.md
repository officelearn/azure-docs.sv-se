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
ms.openlocfilehash: f53a8641a50a6c968a6ba7b841e0e8f938b5d9f6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Skydda en filserver med hjälp av Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM) hanterar. Katastrofåterställning innehåller replikering, redundans och återställning av olika arbetsbelastningar.

Den här artikeln beskriver hur du skyddar en filserver med Site Recovery och gör andra rekommendationer som passar olika miljöer. 

- [Replikera Azure IaaS-filservrar](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikera en lokal filserver genom att använda Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Filen server-arkitektur
Syftet med ett öppet distribuerade fildelning system är att tillhandahålla en miljö där en grupp geografiskt distribuerade användare kan arbeta kan arbeta effektivt på filer och garanteras att deras integritet krav gäller. En typisk lokala filen server-ekosystemet som stöder ett stort antal samtidiga användare och ett stort antal artiklar använder DFSR Distributed File System Replication () för replikering schemaläggning och begränsning av bandbredd. 

DFSR använder en komprimeringsalgoritm kända som komprimering RDC (Remote Differential) som kan användas för att effektivt uppdatera filer i ett nätverk med begränsad bandbredd. Den identifierar tillägg, borttagningar och omflyttningar av data i filer. DFSR är aktiverat för att replikera de ändrade filblocken när filer uppdateras. Det finns även filen server-miljöer där dagliga säkerhetskopior tas i arbetsbelastningen tidsinställningar tillgodoser krasch. DFSR är inte implementerad.

Följande diagram illustrerar filen server-miljö med DFSR implementeras.
                
![DFSR-arkitektur](media/site-recovery-file-server/dfsr-architecture.JPG)

I föregående diagram delta flera filservrar kallas medlemmar aktivt vid replikering av filer mellan en replikeringsgrupp. Innehållet i den replikerade mappen är tillgängliga för alla klienter som skickar begäranden till antingen medlemmar, även om en medlem försätts offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Disaster recovery rekommendationer för filservrar

* **Replikera en filserver med Site Recovery**: filservrar kan replikeras till Azure med hjälp av Site Recovery. När en eller flera lokala filservrar inte är tillgängliga kan för återställning av virtuella datorer tas i Azure. De virtuella datorerna kan sedan hantera begäranden från klienter, lokalt, som det är plats-till-plats VPN-anslutning och Active Directory har konfigurerats i Azure. Du kan använda den här metoden när det gäller en DFSR-konfigurerade miljö eller en enkel fil servermiljö med ingen DFSR. 

* **Utöka DFSR till en Azure IaaS-VM**: I en klustrad filserver servermiljö med DFSR implementerats du kan utöka lokala DFSR till Azure. En Azure VM aktiveras sedan att utföra filserverrollen. 

    * När beroenden för plats-till-plats VPN-anslutning och Active Directory ska hanteras och DFSR är på plats när en eller flera lokala filservrar är tillgänglig kan kan klienter ansluta till Azure VM, som hanterar begäranden.

    * Du kan använda den här metoden om dina virtuella datorer har konfigurationer som inte stöds av Site Recovery. Ett exempel är en delad klusterdisk, som ibland används i filen server-miljöer. DFSR också fungerar bra i miljöer med låg bandbredd med medelhög omsättningen. Du behöver överväga ytterligare kostnaden för att ha en Azure VM och igång hela tiden. 

* **Använd Azure filsynkronisering för att replikera dina filer**: Om du planerar att använda molnet eller redan använder en Azure VM, kan du använda filsynkronisering. Filsynkronisering erbjuder synkroniseringen av helt hanterad filresurser i molnet som är tillgängliga via standardmässiga [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokollet (SMB). Azure-filresurser kan sedan monteras samtidigt med molnet eller lokala distributioner av Windows, Linux och macOS. 

Följande diagram hjälper dig att avgöra vilka strategin för din servermiljö och fil.

![beslutsträdet](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Faktorer att tänka på i ditt beslut om katastrofåterställning till Azure

|Miljö  |Rekommendation  |Saker att tänka på |
|---------|---------|---------|
|Filen server-miljö med eller utan DFSR|   [Använda Site Recovery för replikering](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery stöder inte delade diskkluster eller nätverksansluten lagring (NAS). Om din miljö använder dessa konfigurationer, använda någon av de andra metoderna. <br> Site Recovery stöder inte SMB 3.0. Den replikerade virtuella datorn innehåller ändringar endast när uppdateras ändringar i filerna i den ursprungliga platsen för filerna.
|Filen server-miljö med DFSR     |  [Utöka DFSR till en virtuell Azure IaaS-dator](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR fungerar bra i mycket bandbredd crunched miljöer. Den här metoden kräver att en virtuell Azure-dator som är igång och körs hela tiden. Du måste kontot för kostnaden för den virtuella datorn i planeringen.         |
|Azure IaaS-VM     |     [Filsynkronisering ](#use-azure-file-sync-service-to-replicate-your-files)   |     Om du använder filsynkronisering i en katastrofåterställning, måste du utföra manuella åtgärder för att kontrollera att filresurserna är tillgängliga på klientdatorn på ett transparent sätt under växling vid fel. Filsynkronisering kräver port 445 öppnas från klientdatorn.     |


### <a name="site-recovery-support"></a>Site Recovery-stöd
Eftersom Site Recovery replikering är oberoende av programmet, förväntas de här rekommendationerna gäller för följande scenarier.
| Källa    |Till en sekundär plats    |Till Azure
|---------|---------|---------|
|Azure| -|Ja|
|Hyper-V|   Ja |Ja
|VMware |Ja|   Ja
|Fysisk server|   Ja |Ja
 

> [!IMPORTANT]
> Innan du fortsätter med någon av följande tre metoder, se till att dessa beroenden är åtgärdat.

**Plats-till-plats-anslutning**: en direkt anslutning mellan den lokala platsen och Azure-nätverk måste upprättas för att tillåta kommunikation mellan servrar. Använda en säker plats-till-plats VPN-anslutning till Azure-nätverk som används som disaster recovery-platsen. Mer information finns i [upprätta en plats-till-plats VPN-anslutning mellan en lokal plats och Azure-nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**: DFSR beror på Active Directory. Det innebär att Active Directory-skog med lokala domänkontrollanter är utökat till webbplatsen disaster recovery i Azure. Även om du inte använder DFSR, om de avsedda användarna måste beviljas åtkomst eller verifiera åtkomst, måste du utföra dessa steg. Mer information finns i [utöka lokala Active Directory till Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Disaster recovery rekommendation för Azure IaaS-virtuella datorer

Om du konfigurerar och hanterar katastrofåterställning av filservrar som finns på Azure IaaS-VM, kan du välja mellan två alternativ, baserat på om du vill flytta till [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Använda filen Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Använda Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Använda filen Sync replikera filer som finns på en virtuell IaaS-dator

Azure Files kan användas för att fullständigt ersätta eller komplettera traditionella lokala filservrar eller NAS-enheter. Azure-filresurser kan också replikeras med synkronisering av filen till Windows-servrar, antingen lokalt eller i molnet för prestanda och distribuerad caching data där den används. Följande steg beskriver disaster recovery rekommendation för Azure virtuella datorer som utför samma funktion som traditionella filservrar:
* Skydda datorer med hjälp av Site Recovery. Följ stegen i [replikera en virtuell Azure-dator till en annan Azure-region](azure-to-azure-quickstart.md).
* Använd filsynkronisering replikera filer från den virtuella datorn som fungerar som en filserver till molnet.
* Använda Site Recovery [återställningsplanen](site-recovery-create-recovery-plans.md) funktion för att lägga till skript till [montera filresursen Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

Följande steg beskriver hur du använder filsynkronisering kort:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du väljer geo-redundant lagring med läsbehörighet för storage-konton får du tillgång till läsåtkomst till dina data från den sekundära regionen vid en katastrof. Mer information finns i [strategi för Azure file share katastrofåterställning](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Starta filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) på filservern Azure.
4. Skapa en grupp för synkronisering. Slutpunkter inom en synkronisering grupp hålls synkroniserade med varandra. En grupp för synkronisering måste innehålla minst en molnslutpunkt som representerar en Azure-filresursen. En grupp för synkronisering måste också innehålla en server-slutpunkt som representerar en sökväg på en Windows-server.
5. Filerna är nu synkroniseras över Azure-filresursen och lokal server.
6. Vid katastrofåterställning i din lokala miljö, utför en växling vid fel med hjälp av en [återställningsplanen](site-recovery-create-recovery-plans.md). Lägg till skript till [montera filresursen Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikera en virtuell IaaS filen server-dator med hjälp av Site Recovery

Vidta följande steg om du har lokala klienter som har åtkomst till virtuella IaaS file server. Annars fortsätter du till steg 3.

1. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverk.
2. Utöka lokala Active Directory.
3. [Ställ in katastrofåterställning](azure-to-azure-tutorial-enable-replication.md) för IaaS-filen server-dator till en sekundär region.


Mer information om återställning till en sekundär region finns [i den här artikeln](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikera en lokal filserver genom att använda Site Recovery

Följande steg beskriver replikering för en VMware-VM. Steg för att replikera en virtuell Hyper-V-dator, finns [självstudierna](tutorial-hyper-v-to-azure.md).

1. [Förbered Azure-resurser](tutorial-prepare-azure.md) för replikering av lokala datorer.
2. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverk. 
3. Utöka lokala Active Directory.
4. [Förbereda lokal VMware-servrar](tutorial-prepare-on-premises-vmware.md).
5. [Ställ in katastrofåterställning](tutorial-vmware-to-azure.md) till Azure för lokala virtuella datorer.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Utöka DFSR till en virtuell Azure IaaS-dator

1. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverk. 
2. Utöka lokala Active Directory.
3. [Skapa och etablera en filserver VM](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) på virtuella Azure-nätverket.
Kontrollera att den virtuella datorn har lagts till samma virtuella Azure-nätverket, som har cross-anslutning med den lokala miljön. 
4. Installera och [konfigurera DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) på Windows Server.
5. [Implementera ett DFS-namnområde](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Med DFS-namnområde implementerats kan redundans av delade mappar från produktionen som katastrofåterställningsplatser göras genom att uppdatera mappmål för DFS-namnområdet. När dessa DFS namnområdet ändringar replikeras via Active Directory användare är anslutna till en lämplig mappmål transparent.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Använd filsynkronisering för att replikera lokala filer
Du kan använda filsynkronisering replikera filer till molnet. Vid en katastrof och att den lokala filservern, kan du montera de önskade sökvägar från molnet och fortsätta att tjänstbegäranden från klientdatorer.
Integrera filsynkronisering med Site Recovery:

* Skydda filen server-datorer med hjälp av Site Recovery. Följ stegen i [självstudierna](tutorial-vmware-to-azure.md).
* Använd filsynkronisering replikera filer från datorn som fungerar som en filserver till molnet.
* Använda funktionen återställning plan i Site Recovery för att lägga till skript för att ansluta till Azure-filresursen på misslyckades över filservern VM i Azure.

Följ dessa steg om du vill använda filsynkronisering:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du väljer läsbehörighet geo-redundant lagring (rekommenderas) för storage-konton måste ha du läsbehörighet till dina data från den sekundära regionen vid en katastrof. Mer information finns i [strategi för Azure file share katastrofåterställning](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Distribuera filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) i din lokala filserver.
4. Skapa en grupp för synkronisering. Slutpunkter inom en synkronisering grupp hålls synkroniserade med varandra. En grupp för synkronisering måste innehålla minst en molnslutpunkt som representerar en Azure-filresursen. Gruppen synkronisering måste också innehålla en server-slutpunkt som representerar en sökväg på den lokala Windows-servern.
5. Filerna är nu synkroniseras över Azure-filresursen och lokal server.
6. Vid katastrofåterställning i din lokala miljö, utför en växling vid fel med hjälp av en [återställningsplanen](site-recovery-create-recovery-plans.md). Lägg till skript för att montera filresursen Azure och få åtkomst till resursen i den virtuella datorn.

> [!NOTE]
> Kontrollera att port 445 är öppen. Azure Files använder SMB-protokollet. SMB kommunicerar via TCP-port 445. Kontrollera om brandväggen inte blockerar TCP-port 445 från en klientdator.


## <a name="do-a-test-failover"></a>Gör ett redundanstest

1. Gå till Azure-portalen och välj Recovery-tjänsten-valvet.
2. Välj återställningsplanen som skapats för filen server-miljö.
3. Välj **Redundanstestningen**.
4. Välj återställningspunkten och virtuella Azure-nätverket att starta processen testa redundans.
5. När den sekundära miljön är in, utför du din verifieringar.
6. När validering är klar väljer du **Rensa redundanstestet** på återställningsplanen och testa redundans miljön har rensats.

Läs mer om hur du utför ett redundanstest [testa redundans till Site Recovery](site-recovery-test-failover-to-azure.md).

Anvisningar på gör testa redundans för Active Directory och DNS finns [Redundanstestning för Active Directory och DNS-](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Gör en redundansväxling

1. Gå till Azure-portalen och välj Recovery Services-valvet.
2. Välj återställningsplanen som skapats för filen server-miljö.
3. Välj **redundans**.
4. Välj återställningspunkt för att starta processen för växling vid fel.

Mer information om hur du utför en växling vid fel finns [redundans i Site Recovery](site-recovery-failover.md).
