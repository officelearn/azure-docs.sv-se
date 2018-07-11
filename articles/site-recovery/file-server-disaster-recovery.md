---
title: Skydda en filserver med hjälp av Azure Site Recovery
description: Den här artikeln beskriver hur du skyddar en filserver med hjälp av Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 0b6d5dccbce30c55e259e4bb3f8ae4194a02b646
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916891"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Skydda en filserver med hjälp av Azure Site Recovery 

[Azure Site Recovery](site-recovery-overview.md) bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att hålla dina företagsprogram igång och köra dem vid planerade och oplanerade avbrott. Site Recovery hanterar och samordnar haveriberedskap för lokala datorer och virtuella Azure-datorer (VM). Haveriberedskap innehåller replikering, redundans och återställning av olika arbetsbelastningar.

Den här artikeln beskriver hur du skyddar en filserver med Site Recovery och gör andra rekommendationer som passar olika miljöer. 

- [Replikera Azure IaaS-filservrar](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replikera en lokal filserver med hjälp av Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Filen server-arkitektur
Syftet med ett öppet distribuerat fildelning system är att tillhandahålla en miljö där en grupp geografiskt distribuerade användare kan samarbeta för att arbeta effektivt på filer och garanteras att deras integritet krav gäller. En typisk den lokala filen server-ekosystemet som stöder ett stort antal samtidiga användare och ett stort antal innehållsposter använder Distributed File System (DFSR Replication) för replikering schemaläggning och bandbreddsbegränsning. 

DFSR använder en komprimeringsalgoritm kända som Remote Differential komprimering (RDC) som kan användas för att effektivt uppdatera filer i ett nätverk med begränsad bandbredd. Den identifierar tillägg, borttagningar och omflyttningar av data i filer. DFSR har aktiverats för att replikera de ändrade filblocken när filer uppdateras. Det finns även filen server-miljöer, där dagliga säkerhetskopior tas i arbetsbelastningen tidsinställningar där haveriberedskap efter behov. DFSR är inte implementerad.

Följande diagram illustrerar filen server-miljö med DFSR implementeras.
                
![DFSR-arkitektur](media/site-recovery-file-server/dfsr-architecture.JPG)

I diagrammet ovan delta flera filservrar som kallas medlemmar aktivt vid replikering av filer mellan en replikeringsgrupp. Innehållet i den replikerade mappen är tillgängliga för alla klienter som skickar begäranden till någon av medlemmarna, även om en medlem kopplas från.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Disaster recovery rekommendationer för filservrar

* **Replikera en filserver med hjälp av Site Recovery**: filservrar kan replikeras till Azure med Site Recovery. När en eller flera lokala filservrar är tillgängliga kan för återställning av virtuella datorer göras tillgänglig i Azure. De virtuella datorerna kan sedan hantera begäranden från klienter, lokalt, anges det är plats-till-plats VPN-anslutning och Active Directory har konfigurerats i Azure. Du kan använda den här metoden när det gäller en DFSR konfigurerade miljö eller en enkel fil servermiljö med inga DFSR. 

* **Utöka DFSR till en Azure IaaS VM**: I en klustrad filserver servermiljö med DFSR implementeras kan du utöka lokala DFSR till Azure. En Azure-dator aktiveras sedan att utföra filserverrollen. 

    * När beroenden för plats-till-plats VPN-anslutning och Active Directory ska hanteras och DFSR är på plats, när en eller flera lokala filservrar är otillgängligt kan kan klienter ansluta till Azure-dator, vilket fungerar begäranden.

    * Du kan använda den här metoden om dina virtuella datorer har konfigurationer som inte stöds av Site Recovery. Ett exempel är en delad klusterdisk som ibland används i filen server-miljöer. DFSR fungerar också bra i miljöer med låg bandbredd med medelhög omsättningsfrekvensen. Du behöver tänka på den extra kostnaden för att ha en Azure VM och igång hela tiden. 

* **Använda Azure File Sync för att replikera dina filer**: Om du planerar att använda molnet eller redan använder en Azure-dator, kan du använda Azure File Sync. Azure File Sync erbjuder synkronisering av fullständigt hanterade filresurser i molnet, som är tillgängliga via vanliga [Server Message Block](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) protokollet (SMB). Azure-filresurser kan sedan monteras samtidigt av molnet eller lokala distributioner av Windows, Linux och macOS. 

Följande diagram hjälper dig att avgöra vilken strategi för att använda för din servermiljö för filen.

![Beslutsträd](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Faktorer att tänka på i ditt beslut om haveriberedskap till Azure

|Miljö  |Rekommendation  |Saker att tänka på |
|---------|---------|---------|
|Filen server-miljö med eller utan DFSR|   [Använd Site Recovery för replikering](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    Site Recovery stöder inte delade diskkluster eller nätverksansluten lagring (NAS). Om de här konfigurationerna används i din miljö kan du använda någon av de andra metoderna efter behov. <br> Site Recovery stöder inte SMB 3.0. Den replikerade virtuella datorn innehåller ändringar bara när ändringar av filerna uppdateras i den ursprungliga platsen för filerna.
|Filen server-miljö med DFSR     |  [Utöka DFSR till en virtuell Azure IaaS-dator](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      DFSR fungerar bra i mycket bandbredd crunched miljöer. Den här metoden kräver en Azure-dator som är igång och körs hela tiden. Du behöver att kostnaden för den virtuella datorn i planeringen.         |
|Azure IaaS-VM     |     [File Sync ](#use-azure-file-sync-service-to-replicate-your-files)   |     Om du använder File Sync i ett katastrofåterställningsscenario måste du vidta manuella åtgärder för att se till att filresurserna är tillgängliga på klientdatorn på ett transparent sätt under en redundansväxling. File Sync kräver port 445 är öppen från klientdatorn.     |


### <a name="site-recovery-support"></a>Site Recovery-stöd
Eftersom Site Recovery-replikering är oberoende av program, förväntas de här rekommendationerna gäller för följande scenarier.
| Källa    |Till en sekundär plats    |Till Azure
|---------|---------|---------|
|Azure| -|Ja|
|Hyper-V|   Ja |Ja
|VMware |Ja|   Ja
|Fysisk server|   Ja |Ja
 

> [!IMPORTANT]
> Innan du fortsätter med någon av följande tre metoder, se till att dessa beroenden är åtgärdat.

**Plats-till-plats-anslutning**: en direkt anslutning mellan den lokala platsen och Azure-nätverket måste upprättas för att tillåta kommunikation mellan servrar. Använd en säker plats-till-plats VPN-anslutning till en Azure-nätverk som används som katastrofåterställningsplatsen. Mer information finns i [upprätta en plats-till-plats VPN-anslutning mellan en lokal plats och Azure-nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**: DFSR beror på Active Directory. Det innebär att Active Directory-skog med lokala domänkontrollanter utökas till platsen för katastrofåterställning i Azure. Även om du inte använder DFSR, om de avsedda användarna måste beviljas åtkomst eller verifieras för åtkomst, måste du vidta dessa åtgärder. Mer information finns i [utöka lokala Active Directory till Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Disaster recovery-rekommendation för Azure IaaS-datorer

Om du konfigurerar och hanterar haveriberedskap för filservrar som finns på Azure IaaS-VM, kan du välja mellan två alternativ, baserat på om du vill flytta till [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Använda File Sync](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Använd Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Använda File Sync för att replikera filer som lagras på en virtuell IaaS-dator

Azure Files kan användas för att fullständigt ersätta eller komplettera traditionella lokala filservrar eller NAS-enheter. Azure-filresurser kan också replikeras med-filsynkronisering till Windows-servrar, antingen lokalt eller i molnet, för högpresterande och distribuerad cachelagring av data där den används. Följande steg beskriver disaster recovery-rekommendation för virtuella Azure-datorer som utför samma funktioner som traditionella filservrar:
* Skydda datorer med hjälp av Site Recovery. Följ stegen i [replikera en virtuell Azure-dator till en annan](azure-to-azure-quickstart.md).
* Använda File Sync för att replikera filer från den virtuella datorn som fungerar som filserver till molnet.
* Använd Site Recovery [återställningsplanen](site-recovery-create-recovery-plans.md) funktionen för att lägga till skript till [montera Azure-filresursen](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

Följande steg beskriver kortfattat så använder File Sync:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du har valt läsåtkomst till geografiskt redundant lagring för dina lagringskonton får du tillgång till läsåtkomst till dina data från den sekundära regionen vid en katastrof. Mer information finns i [strategier för Azure file share haveriberedskap](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Starta filsynkronisering](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) på filservern Azure.
4. Skapa en synkroniseringsgrupp. Slutpunkter inom en synkroniseringsgrupp hålls synkroniserade med varandra. En synkroniseringsgrupp måste innehålla minst en molnslutpunkt som representerar en Azure-filresurs. En synkroniseringsgrupp måste också innehålla en serverslutpunkt, som representerar en sökväg på en Windows-server.
5. Dina filer nu synkroniseras i Azure-filresursen och den lokala servern.
6. Utför en redundansväxling i händelse av en katastrof i din lokala miljö med hjälp av en [återställningsplanen](site-recovery-create-recovery-plans.md). Lägg till skript till [montera Azure-filresursen](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) och få åtkomst till resursen i den virtuella datorn.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replikera en virtuell IaaS-filen server-dator med hjälp av Site Recovery

Om du har lokala klienter som har åtkomst till den IaaS fil server-datorn kan ta alla följande steg. Annars går du till steg 3.

1. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverket.
2. Utöka lokala Active Directory.
3. [Ställ in katastrofåterställning](azure-to-azure-tutorial-enable-replication.md) för IaaS-filen server-dator till en sekundär region.


Läs mer på haveriberedskap till en sekundär region [i den här artikeln](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replikera en lokal filserver med hjälp av Site Recovery

Följande steg beskriver replikering för en VMware-VM. Steg för att replikera en virtuell Hyper-V-dator, se [den här självstudien](tutorial-hyper-v-to-azure.md).

1. [Förbereda Azure-resurser](tutorial-prepare-azure.md) för replikering av lokala datorer.
2. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverket. 
3. Utöka lokala Active Directory.
4. [Förbereda lokala VMware-servrar](tutorial-prepare-on-premises-vmware.md).
5. [Ställ in katastrofåterställning](tutorial-vmware-to-azure.md) till Azure för lokala virtuella datorer.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Utöka DFSR till en virtuell Azure IaaS-dator

1. Upprätta en plats-till-plats VPN-anslutning mellan den lokala platsen och Azure-nätverket. 
2. Utöka lokala Active Directory.
3. [Skapa och etablera en virtuell dator för filserver](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) på virtuella Azure-nätverket.
Kontrollera att den virtuella datorn har lagts till samma Azure-nätverk, som har flera anslutningar till den lokala miljön. 
4. Installera och [konfigurera DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) på Windows Server.
5. [Implementera ett DFS-namnområde](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Med DFS-namnområdet implementeras, kan växling av delade mappar från produktion till katastrofåterställningsplatser göras genom att uppdatera mappmål för DFS-namnområdet. När du har dessa DFS namnområdet ändringar replikeras via Active Directory, användare är anslutna till lämpliga mappmål transparent.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Använda File Sync för att replikera dina lokala filer
Du kan använda File Sync för att replikera filer till molnet. I händelse av en katastrof och otillgängliga på den lokala filservern kan du montera de önskade platserna från molnet och fortsätter att betjäna förfrågningar från klientdatorer.
Integrera File Sync med Site Recovery:

* Skydda filen server-datorer med hjälp av Site Recovery. Följ stegen i [den här självstudien](tutorial-vmware-to-azure.md).
* Använda File Sync för att replikera filer från den datorn som fungerar som en server till molnet.
* Använd funktionen recovery plan i Site Recovery att lägga till skript för att montera Azure-filresursen på den redundansväxlade filservern virtuell dator i Azure.

Följ dessa steg om du vill använda File Sync:

1. [Skapa ett lagringskonto i Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du har valt läsåtkomst till geografiskt redundant lagring (rekommenderas) för storage-konton kan ha läsbehörighet till dina data från den sekundära regionen vid en katastrof. Mer information finns i [strategier för Azure file share haveriberedskap](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
2. [Skapa en filresurs](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Distribuera File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) i den lokala servern.
4. Skapa en synkroniseringsgrupp. Slutpunkter inom en synkroniseringsgrupp hålls synkroniserade med varandra. En synkroniseringsgrupp måste innehålla minst en molnslutpunkt som representerar en Azure-filresurs. Synkroniseringsgruppen måste också innehålla en serverslutpunkt, som representerar en sökväg på en lokal Windows server.
5. Dina filer nu synkroniseras i Azure-filresursen och den lokala servern.
6. Utför en redundansväxling i händelse av en katastrof i din lokala miljö med hjälp av en [återställningsplanen](site-recovery-create-recovery-plans.md). Lägg till skript för att montera Azure-filresursen och få åtkomst till resursen i den virtuella datorn.

> [!NOTE]
> Kontrollera att port 445 är öppen. Azure Files använder SMB-protokollet. SMB kommunicerar via TCP-port 445. Kontrollera om brandväggen inte blockerar TCP-port 445 från en klientdator.


## <a name="do-a-test-failover"></a>Gör ett redundanstest

1. Gå till Azure-portalen och välj Recovery Service-valv.
2. Välj återställningsplan som skapats för filen server-miljö.
3. Välj **Redundanstest**.
4. Välj återställningspunkten och virtuella Azure-nätverket du startar test redundans.
5. När den sekundära miljön är igång, utför du ditt verifieringar.
6. När validering är klar väljer du **Rensa redundanstestning** på återställningsplanen och redundanstestet miljön är rensad.

Läs mer om hur du utför ett redundanstest [testa redundans till Site Recovery](site-recovery-test-failover-to-azure.md).

Anvisningar om hur du gör redundanstestningen för Active Directory och DNS finns i [testa redundans överväganden för Active Directory och DNS-](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Gör en redundansväxling

1. Gå till Azure-portalen och välj Recovery Services-valvet.
2. Välj återställningsplan som skapats för filen server-miljö.
3. Välj **redundans**.
4. Välj återställningspunkten du startar redundans.

Läs mer om hur du utför en redundansväxling [redundans i Site Recovery](site-recovery-failover.md).
