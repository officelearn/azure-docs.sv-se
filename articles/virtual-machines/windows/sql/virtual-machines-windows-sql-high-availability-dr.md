---
title: Hög tillgänglighet och haveri beredskap för SQL Server | Microsoft Docs
description: En beskrivning av de olika typerna av HADR-strategier för SQL Server som körs i Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 2e6df3c9dc80700faa23aa85c66fd42260ee2606
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330067"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines

Microsoft Azure Virtual Machines (VM) med SQL Server kan minska kostnaderna för en databas lösning för hög tillgänglighet och haveri beredskap (HADR). De flesta SQL Server HADR-lösningar stöds i Azure Virtual Machines, både i Azure och som hybrid lösningar. I en Azure-lösning körs hela HADR-systemet i Azure. I en hybrid konfiguration körs en del av lösningen i Azure och den andra delen körs lokalt i din organisation. Med flexibiliteten i Azure-miljön kan du flytta helt eller delvis till Azure för att uppfylla budget-och HADR krav för dina SQL Server databas system.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Förstå behovet av en HADR-lösning
Det är upp till dig att se till att ditt databas system har de HADR-funktioner som krävs för service nivå avtalet (SLA). Det faktum att Azure tillhandahåller mekanismer för hög tillgänglighet, till exempel tjänst återställning för moln tjänster och identifiering av haveri återställning för Virtual Machines, garanterar inte att du kan uppfylla det önskade service avtalet. Dessa metoder skyddar de virtuella datorernas hög tillgänglighet, men inte hög tillgänglighet för SQL Server som körs i de virtuella datorerna. Det är möjligt att SQL Server-instansen kraschar medan den virtuella datorn är online och felfri. Dessutom kan de funktioner för hög tillgänglighet som tillhandahålls av Azure tillåta drift stopp av de virtuella datorerna på grund av händelser som återställning från program varu-eller maskin varu problem och uppgraderingar av operativ system.

Dessutom kan Geo redundant lagring (GRS) i Azure, som implementeras med en funktion som kallas geo-replikering, inte vara en lämplig katastrof återställnings lösning för dina databaser. Eftersom geo-replikering skickar data asynkront kan de senaste uppdateringarna gå förlorade i händelse av en katastrof. Mer information om begränsningar för geo-replikering beskrivs i avsnittet [geo-replikering stöds inte för data-och loggfiler på separata diskar](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>HADR distributions arkitekturer
SQL Server HADR-tekniker som stöds i Azure är:

* [Always on-tillgänglighetsgrupper](https://technet.microsoft.com/library/hh510230.aspx)
* [Always on Cluster instances](https://technet.microsoft.com/library/ms189134.aspx)
* [Logg överföring](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server säkerhets kopiering och återställning med Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx)
* [Databas spegling](https://technet.microsoft.com/library/ms189852.aspx) -inaktuell i SQL Server 2016

Det är möjligt att kombinera teknikerna tillsammans för att implementera en SQL Server lösning som har både hög tillgänglighet och haveri beredskap. Beroende på vilken teknik du använder kan en hybrid distribution kräva en VPN-tunnel med det virtuella Azure-nätverket. I avsnitten nedan visas några exempel på distributions arkitekturer.

## <a name="azure-only-high-availability-solutions"></a>Endast Azure: lösningar med hög tillgänglighet

Du kan ha en lösning med hög tillgänglighet för SQL Server på en databas nivå med Always on-tillgänglighets grupper – som kallas tillgänglighets grupper. Du kan också skapa en lösning för hög tillgänglighet på en instans nivå med Always on Cluster instances-instanser för redundanskluster. För ytterligare redundans kan du skapa redundans på båda nivåerna genom att skapa tillgänglighets grupper för kluster instanser för växling vid fel. 

| Teknik | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighets grupper** |Tillgänglighets repliker som körs i virtuella Azure-datorer i samma region ger hög tillgänglighet. För högre redundans och tillgänglighet kan virtuella Azure-datorer distribueras i olika [tillgänglighets zoner](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview) som dokumenteras [här](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Om de virtuella datorerna i en tillgänglighets grupp har distribuerats i tillgänglighets zoner använder du SQL Server en [standard belastnings utjämning](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-standard-overview) för lyssnare som dokumenteras i dessa artiklar – [Azure SQL VM CLI](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-availability-group-cli) & [Azure snabb starts mallar](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-availability-group-quickstart-template). Du måste konfigurera en virtuell domänkontrollant, eftersom Windows-redundanskluster kräver en Active Directory-domän.<br/> ![Availability grupper @ no__t-1<br/>Mer information finns i [Konfigurera tillgänglighets grupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instanser av kluster för växling vid fel** |Instanser av kluster för växling vid fel (FCI), som kräver delad lagring, kan skapas på tre olika sätt.<br/><br/>1. ett redundanskluster med två noder som körs i virtuella Azure-datorer med bifogad lagring med hjälp av [Windows Server 2016 Lagringsdirigering \(S2D @ no__t-2](virtual-machines-windows-portal-sql-create-failover-cluster.md) för att tillhandahålla en PROGRAMVARUBASERAD virtuell San-Server.<br/><br/>2. ett redundanskluster med två noder som körs i virtuella Azure-datorer med lagrings utrymme som stöds av en kluster lösning från tredje part. För ett särskilt exempel som använder SIOS DataKeeper, se [hög tillgänglighet för en fil resurs med hjälp av redundanskluster och program vara från tredje part SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. ett redundanskluster med två noder som körs i virtuella Azure-datorer med fjärran sluten iSCSI-mål med delad block lagring via ExpressRoute. NetApp Private Storage (NPS) visar till exempel ett iSCSI-mål via ExpressRoute med Equinix till virtuella Azure-datorer.<br/><br/>För delade lösningar från tredje part och lösningar för datareplikering bör du kontakta leverantören för eventuella problem som rör åtkomst till data vid redundans.<br/><br/>Observera att användning av FCI ovanpå [Azure File Storage](https://azure.microsoft.com/services/storage/files/) inte stöds ännu, eftersom den här lösningen inte använder Premium Storage. Vi arbetar för att stödja detta snart. |

## <a name="azure-only-disaster-recovery-solutions"></a>Endast Azure: katastrof återställnings lösningar
Du kan ha en katastrof återställnings lösning för dina SQL Server databaser i Azure med hjälp av tillgänglighets grupper, databas spegling eller säkerhets kopiering och återställning med Storage-blobbar.

| Teknik | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighets grupper** |Tillgänglighets repliker som körs över flera data Center i virtuella Azure-datorer för haveri beredskap. Denna lösning för flera regioner skyddar mot slutförd plats störning. <br/> ![Availability grupper @ no__t-1<br/>I en region bör alla repliker finnas inom samma moln tjänst och samma VNet. Eftersom varje region har ett separat VNet, kräver de här lösningarna VNet till VNet-anslutning. Mer information finns i [Konfigurera en VNet-till-VNET-anslutning med hjälp av Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Detaljerade anvisningar finns i [Konfigurera en SQL Server tillgänglighets grupp på Azure Virtual Machines i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Databas spegling** |Huvud servrar och spegling och servrar som körs i olika data Center för haveri beredskap. Du måste distribuera med hjälp av Server certifikat. SQL Server databas spegling stöds inte för SQL Server 2008 eller SQL Server 2008 R2 på en virtuell Azure-dator. <br/>![Databas spegling](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Säkerhets kopiering och återställning med Azure Blob Storage-tjänsten** |Produktions databaser som säkerhets kopie ras direkt till Blob Storage i ett annat data Center för haveri beredskap.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Mer information finns i [säkerhets kopiering och återställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikera och redundansväxla SQL Server till Azure med Azure Site Recovery** |Produktions SQL Server av ett Azure-datacenter replikeras direkt till Azure Storage av olika Azure-datacenter för haveri beredskap.<br/>![Replicate med Azure Site Recovery @ no__t-1<br/>Mer information finns i [skydda SQL Server att använda SQL Server haveri beredskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid IT: lösningar för katastrof återställning
Du kan ha en katastrof återställnings lösning för SQL Server databaser i en hybrid IT-miljö med tillgänglighets grupper, databas spegling, logg överföring och säkerhets kopiering och återställning med Azure blogg lagring.

| Teknik | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighets grupper** |Vissa tillgänglighets repliker som körs i virtuella Azure-datorer och andra repliker som körs lokalt för haveri beredskap mellan platser. Produktions platsen kan vara antingen lokalt eller i ett Azure-datacenter.<br/>![Tillgänglighets grupper](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Eftersom alla tillgänglighets repliker måste finnas i samma redundanskluster måste klustret omfatta båda nätverken (ett redundanskluster med flera undernät). Den här konfigurationen kräver en VPN-anslutning mellan Azure och det lokala nätverket.<br/><br/>För lyckad haveri beredskap för dina databaser bör du också installera en replik-domänkontrollant på webbplatsen för haveri beredskap.<br/><br/>Det går att använda guiden Lägg till replik i SSMS för att lägga till en Azure-replik till en befintlig Always on-tillgänglighetsgruppen. Mer information finns i Självstudier: utöka din Always on Availability-grupp till Azure. |
| **Databas spegling** |En partner som körs i en virtuell Azure-dator och den andra som körs lokalt för haveri beredskap mellan platser med hjälp av Server certifikat. Partners behöver inte vara i samma Active Directory domän och ingen VPN-anslutning krävs.<br/>![Database spegling @ no__t-1<br/>Ett annat scenario för databas spegling omfattar en partner som körs i en virtuell Azure-dator och den andra som körs lokalt i samma Active Directory domän för haveri beredskap mellan platser. Det krävs en [VPN-anslutning mellan det virtuella Azure-nätverket och det lokala nätverket](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) .<br/><br/>För lyckad haveri beredskap för dina databaser bör du också installera en replik-domänkontrollant på webbplatsen för haveri beredskap. SQL Server databas spegling stöds inte för SQL Server 2008 eller SQL Server 2008 R2 på en virtuell Azure-dator. |
| **Logg överföring** |En server som körs på en virtuell Azure-dator och den andra som körs lokalt för haveri beredskap mellan platser. Logg överföring är beroende av Windows fildelning, så en VPN-anslutning mellan det virtuella Azure-nätverket och det lokala nätverket krävs.<br/>![Logg överföring](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>För lyckad haveri beredskap för dina databaser bör du också installera en replik-domänkontrollant på webbplatsen för haveri beredskap. |
| **Säkerhets kopiering och återställning med Azure Blob Storage-tjänsten** |Lokala produktions databaser som säkerhets kopie ras direkt till Azure Blob Storage för haveri beredskap.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Mer information finns i [säkerhets kopiering och återställning för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikera och redundansväxla SQL Server till Azure med Azure Site Recovery** |Lokal produktions SQL Server replikeras direkt till Azure Storage för haveri beredskap.<br/>![Replicate med Azure Site Recovery @ no__t-1<br/>Mer information finns i [skydda SQL Server att använda SQL Server haveri beredskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Viktiga överväganden för SQL Server HADR i Azure
Virtuella Azure-datorer, lagring och nätverk har olika operativa egenskaper än en lokal, icke-virtualiserad IT-infrastruktur. En lyckad implementering av en HADR SQL Server-lösning i Azure kräver att du förstår dessa skillnader och utformar din lösning för att hantera dem.

### <a name="high-availability-nodes-in-an-availability-set"></a>Noder med hög tillgänglighet i en tillgänglighets uppsättning
Med tillgänglighets uppsättningar i Azure kan du placera noderna med hög tillgänglighet i separata fel domäner (fd) och uppdaterings domäner (UDs). Varje virtuell dator i din tillgänglighets uppsättning tilldelas en uppdaterings domän och en feldomän av den underliggande Azure-plattformen. Den här konfigurationen i ett Data Center garanterar att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhålls händelse och uppfyller 99,95% Azure SLA. Om du vill konfigurera inställningar för hög tillgänglighet placerar du alla deltagande SQL-Virtual Machines i samma tillgänglighets uppsättning för att undvika program-eller data förlust under en underhålls händelse. Endast noder i samma moln tjänst kan ingå i samma tillgänglighets uppsättning. Mer information finns i [Hantera tillgängligheten för Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Noder med hög tillgänglighet i en tillgänglighets zon
Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. Den fysiska separeringen av Tillgänglighetszoner inom en region skyddar program och data från data Center problem genom att se till att minst en virtuell dator är tillgänglig och uppfyller 99,99% Azure SLA. Om du vill konfigurera hög tillgänglighet placerar du deltagande SQL Virtual Machines sprids över tillgängliga Tillgänglighetszoner i regionen. Det kommer att finnas ytterligare avgifter för VM-till-VM-dataöverföringar mellan olika tillgänglighets zoner. Mer information finns i [tillgänglighets zoner](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Funktions sätt för redundanskluster i Azure-nätverk
En icke-RFC-kompatibel DHCP-tjänst i Azure kan orsaka att vissa konfigurationer av redundanskluster inte kan skapas, på grund av att kluster nätverks namnet tilldelas en dubblett av en IP-adress, till exempel samma IP-adress som en av klusternoderna. Detta är ett problem när du implementerar tillgänglighets grupper som är beroende av funktionen Windows-redundanskluster.

Överväg scenariot när ett kluster med två noder skapas och kopplas från:

1. Klustret är online och NOD1 begär en dynamiskt tilldelad IP-adress för klustrets nätverks namn.
2. Ingen annan IP-adress än NODE1's egen IP-adress har angetts av DHCP-tjänsten, eftersom DHCP-tjänsten känner av att begäran kommer från NOD1.
3. Windows upptäcker att en duplicerad adress är tilldelad både till NOD1 och nätverks namnet för redundansklustret och att standard kluster gruppen inte kan anslutas.
4. Standard kluster gruppen flyttas till NOD2, som behandlar NODE1's IP-adress som kluster-IP-adress och ansluter till standard kluster gruppen online.
5. När NOD2 försöker upprätta en anslutning med NOD1 lämnar paket som dirigeras till NOD1 aldrig NOD2 eftersom den löser NODE1's IP-adress till sig själv. NOD2 kan inte upprätta någon anslutning till NOD1 och sedan förlora kvorum och stänga av klustret.
6. Under tiden kan NOD1 skicka paket till NOD2, men NOD2 kan inte svara. NOD1 förlorar kvorum och stänger av klustret.

Det här scenariot kan undvikas genom att tilldela en oanvänd statisk IP-adress, till exempel en länk lokal IP-adress som 169.254.1.1, till kluster nätverks namnet för att ta klustrets nätverks namn online. För att förenkla den här processen, se [Konfigurera Windows-redundanskluster i Azure för tillgänglighets grupper](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Mer information finns i [Konfigurera tillgänglighets grupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Stöd för tillgänglighets grupps lyssnare
Tillgänglighets grupps lyssnare stöds på virtuella Azure-datorer som kör Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det här stödet möjliggörs med hjälp av belastningsutjämnade slut punkter som är aktiverade på de virtuella Azure-datorer som är tillgänglighets grupps noder. Du måste följa särskilda konfigurations steg för att lyssnarna ska fungera för både klient program som körs i Azure och de som körs lokalt.

Det finns två huvud alternativ för att konfigurera din lyssnare: extern (offentlig) eller intern. Den externa (offentliga) lyssnaren använder en belastningsutjämnare mot Internet och är associerad med en offentlig virtuell IP-adress (VIP) som är tillgänglig via Internet. En intern lyssnare använder en intern belastningsutjämnare och stöder bara klienter inom samma Virtual Network. För antingen belastnings Utjämnings typ måste du aktivera direkt Server RETUR. 

Om tillgänglighets gruppen sträcker sig över flera Azure-undernät (till exempel en distribution som korsar Azure-regioner) måste klient anslutnings strängen innehålla "**MultisubnetFailover = True**". Detta resulterar i parallella anslutnings försök till replikerna i de olika under näten. Instruktioner för hur du konfigurerar en lyssnare finns i

* [Konfigurera en ILB-lyssnare för tillgänglighets grupper i Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurera en extern lyssnare för tillgänglighets grupper i Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Du kan fortfarande ansluta till varje tillgänglighets replik separat genom att ansluta direkt till tjänst instansen. Eftersom tillgänglighets grupper är bakåtkompatibla med databas speglings klienter, kan du också ansluta till tillgänglighets replikerna som databas speglings partner så länge replikerna har kon figurer ATS liknande databas spegling:

* En primär replik och en sekundär replik
* Den sekundära repliken är konfigurerad som icke läsbar (**läsbar sekundär** alternativ uppsättning till **Nej**)

Ett exempel på en klient anslutnings sträng som motsvarar den här databas speglings konfigurationen med ADO.NET eller SQL Server Native Client finns nedan:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Mer information om klient anslutningar finns i:

* [Använda nyckelord för anslutnings strängen med SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ansluta klienter till en databas speglings session (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Ansluter till tillgänglighets gruppens lyssnare i hybrid IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Tillgänglighets grupps lyssnare, klient anslutning och programredundans (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Använda anslutnings strängar för databas spegling med tillgänglighets grupper](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Nätverks fördröjning i hybrid IT
Du bör distribuera din HADR-lösning med antagandet att det kan finnas tids perioder med hög nätverks fördröjning mellan ditt lokala nätverk och Azure. När du distribuerar repliker till Azure bör du använda asynkron incheckning i stället för synkron incheckning i Synkroniseringsläge. När du distribuerar databas speglings servrar både lokalt och i Azure använder du läget för hög prestanda i stället för hög säkerhets läge.

### <a name="geo-replication-support"></a>Stöd för geo-replikering
Geo-replikering i Azure-diskar har inte stöd för data filen och logg filen för samma databas som ska lagras på separata diskar. GRS replikerar ändringar på varje disk oberoende av varandra och asynkront. Den här mekanismen garanterar Skriv ordningen inom en enskild disk på den geo-replikerade kopian, men inte över geo-replikerade kopior av flera diskar. Om du konfigurerar en databas för att lagra data filen och dess loggfil på separata diskar kan de återställda diskarna efter en katastrof innehålla en uppdaterad kopia av data filen än logg filen, vilket bryter loggen för Skriv åtgärder i SQL Server och egenskaperna för att skriva över i förväg. ctions. Om du inte har alternativet att inaktivera geo-replikering på lagrings kontot bör du behålla alla data och loggfiler för en specifik databas på samma disk. Om du måste använda mer än en disk på grund av databasens storlek måste du distribuera en av de katastrof återställnings lösningar som anges ovan för att säkerställa dataredundans.

## <a name="next-steps"></a>Nästa steg
Om du behöver skapa en virtuell Azure-dator med SQL Server, se [etablering av en SQL Server virtuell dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

För att få bästa möjliga prestanda från SQL Server som körs på en virtuell Azure-dator kan du läsa mer i avsnittet [prestanda metod tips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på Azure-Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Övriga resurser
* [Installera en ny Active Directory skog i Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Skapa redundanskluster för tillgänglighets grupper i Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

