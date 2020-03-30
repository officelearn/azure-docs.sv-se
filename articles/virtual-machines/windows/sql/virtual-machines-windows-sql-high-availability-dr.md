---
title: Hög tillgänglighet och haveriberedskap för SQL Server | Microsoft-dokument
description: En diskussion om de olika typerna av HADR-strategier för SQL Server som körs i Virtuella Azure-datorer.
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
ms.openlocfilehash: f58bb534728660b85f7d16910dde7a37914fd571
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249768"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Hög tillgänglighet och haveriberedskap för SQL Server i virtuella Azure-datorer

Microsoft Azure virtuella datorer (VMs) med SQL Server kan hjälpa till att sänka kostnaden för en hadr-databaslösning (High Availability and Disaster Recovery). De flesta SQL Server HADR-lösningar stöds i virtuella Azure-datorer, både som Azure-only och som hybridlösningar. I en Azure-lösning körs hela HADR-systemet i Azure. I en hybridkonfiguration körs en del av lösningen i Azure och den andra delen körs lokalt i din organisation. Flexibiliteten i Azure-miljön gör att du kan flytta helt eller delvis till Azure för att uppfylla budget- och HADR-kraven i dina SQL Server-databassystem.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Förstå behovet av en HADR-lösning
Det är upp till dig att se till att ditt databassystem har DE HADR-funktioner som servicenivåavtalet (SLA) kräver. Det faktum att Azure tillhandahåller mekanismer med hög tillgänglighet, till exempel tjänstläkning för molntjänster och felåterställningsidentifiering för virtuella datorer, garanterar inte själv att du kan uppfylla önskat serviceavtal. Dessa mekanismer skyddar den höga tillgängligheten för de virtuella datorerna men inte den höga tillgängligheten för SQL Server som körs inuti de virtuella datorerna. Det är möjligt för SQL Server-instansen att misslyckas medan den virtuella datorn är online och felfri. Dessutom tillåter även de mekanismer med hög tillgänglighet som tillhandahålls av Azure driftstopp för de virtuella datorerna på grund av händelser som återställning från programvaru- eller maskinvarufel och uppgraderingar av operativsystemet.

Dessutom kanske geo redundant lagring (GRS) i Azure, som implementeras med en funktion som kallas geo-replikering, inte vara en lämplig lösning för haveriberedskap för dina databaser. Eftersom geo-replikering skickar data asynkront kan de senaste uppdateringarna gå förlorade i händelse av katastrof. Mer information om geo-replikeringsbegränsningar ingår i [avsnittet Geo-replikering som inte stöds för data och loggfiler på separata diskar.](#geo-replication-support)

## <a name="hadr-deployment-architectures"></a>HADR-distributionsarkitekturer
SQL Server HADR-tekniker som stöds i Azure omfattar:

* [Alltid på tillgänglighetsgrupper](https://technet.microsoft.com/library/hh510230.aspx)
* [Alltid vid redundansklusteinstanser](https://technet.microsoft.com/library/ms189134.aspx)
* [Logga frakt](https://technet.microsoft.com/library/ms187103.aspx)
* [Sql Server säkerhetskopiering och återställning med Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148.aspx)
* [Databasspegling](https://technet.microsoft.com/library/ms189852.aspx) – föråldrad i SQL Server 2016

Det är möjligt att kombinera teknikerna tillsammans för att implementera en SQL Server-lösning som har både hög tillgänglighet och funktioner för haveriberedskap. Beroende på vilken teknik du använder kan en hybriddistribution kräva en VPN-tunnel med det virtuella Azure-nätverket. Avsnitten nedan visar några av exempeldistributionsarkitekturerna.

## <a name="azure-only-high-availability-solutions"></a>Endast Azure: Lösningar för hög tillgänglighet

Du kan ha en lösning med hög tillgänglighet för SQL Server på databasnivå med Alltid på tillgänglighetsgrupper - så kallade tillgänglighetsgrupper. Du kan också skapa en lösning med hög tillgänglighet på instansnivå med Alltid vid redundansklusteinstanser - redundansklusteinstanser. För ytterligare redundans kan du skapa redundans på båda nivåerna genom att skapa tillgänglighetsgrupper på redundansklusterinstanser. 

| Teknologi | Exempel på arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighetsrepliker som körs i virtuella Azure-datorer i samma region ger hög tillgänglighet.  Du måste konfigurera en virtuell domänkontrollant, eftersom Windows redundanskluster kräver en Active Directory-domän.<br/><br/> För högre redundans och tillgänglighet kan virtuella Azure-datorer distribueras i olika [tillgänglighetszoner](../../../availability-zones/az-overview.md) som dokumenteras i [översikten över tillgänglighetsgruppen](virtual-machines-windows-portal-sql-availability-group-overview.md). Om virtuella SQL Server-datorer i en tillgänglighetsgrupp distribueras i tillgänglighetszoner använder du [standardbelastningsutjämnaren](../../../load-balancer/load-balancer-standard-overview.md) för lyssnare som dokumenteras i dessa artiklar - [Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) & [Azure Quickstart-mallar](virtual-machines-windows-sql-availability-group-quickstart-template.md).<br/> ![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-ha-always-on.png)<br/>Mer information finns [i Konfigurera tillgänglighetsgrupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Klusterinstanser för redundanstr** |Redundansklusteinstanser (FCI), som kräver delad lagring, kan skapas på fyra olika sätt.<br/><br/>1. Ett redundanskluster med två noder som körs i virtuella Azure-datorer med ansluten lagring med [Windows Server \(2016 Storage Spaces Direct S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) för att tillhandahålla ett programvarubaserat virtuellt SAN.<br/><br/> 2. Ett redundanskluster för tvånoder som körs i virtuella Azure-datorer med [Premium File Share](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md). Premium-filresurser är SSD-stödda filresurser med konsekvent låg latens som stöds fullt ut för användning med Redundansklusterinstans.<br/><br/>3. Ett redundanskluster med två noder som körs i virtuella Azure-datorer med lagring som stöds av en klusterlösning från tredje part. Ett specifikt exempel som använder SIOS DataKeeper finns i [Hög tillgänglighet för en filresurs med redundanskluster och programvara från tredje part SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>4. Ett redundanskluster för tvånoder som körs i virtuella Azure-datorer med delad blocklagring för fjärr iSCSI-mål via ExpressRoute. NetApp Private Storage (NPS) exponerar till exempel ett iSCSI-mål via ExpressRoute med Equinix till Virtuella Azure-datorer.<br/><br/>För lösningar för delad lagring och datareplikering från tredje part bör du kontakta leverantören för eventuella problem som rör åtkomst till data vid redundans.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Endast Azure: Lösningar för haveriberedskap
Du kan ha en katastrofåterställningslösning för DINA SQL Server-databaser i Azure med hjälp av tillgänglighetsgrupper, databasspegling eller säkerhetskopiering och återställning med lagringsblobar.

| Teknologi | Exempel på arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighetsrepliker som körs över flera datacenter i virtuella Azure-datorer för haveriberedskap. Den här lösningen mellan regioner skyddar mot fullständigt plats avbrott. <br/> ![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-alwayson.png)<br/>Inom en region bör alla repliker finnas inom samma molntjänst och samma virtuella nätverk. Eftersom varje region har ett separat virtuella nätverk kräver dessa lösningar VNet till VNet-anslutning. Mer information finns i [Konfigurera en VNet-till-VNet-anslutning med Azure-portalen](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Detaljerade instruktioner finns i [Konfigurera en SQL Server-tillgänglighetsgrupp på virtuella Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Spegla databasen** |Huvud- och spegel och servrar som körs i olika datacenter för haveriberedskap. Du måste distribuera med hjälp av servercertifikat. SQL Server database mirroring is not supported for SQL Server 2008 nor SQL Server 2008 R2 on an Azure VM. <br/>![Spegla databasen](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-dbmirroring.png) |
| **Säkerhetskopiering och återställning med Azure Blob Storage Service** |Produktionsdatabaser säkerhetskopieras direkt till blob-lagring i ett annat datacenter för haveriberedskap.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-backup-restore.png)<br/>Mer information finns i [Säkerhetskopiering och återställning för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikera och redundans SQL Server till Azure med Azure Site Recovery** |Produktion AV SQL Server för ett Azure-datacenter som replikeras direkt till Azure Storage för olika Azure-datacenter för haveriberedskap.<br/>![Replikera med Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure-only-dr-standalone-sqlserver-asr.png)<br/>Mer information finns i [Skydda SQL Server med SQL Server-haveriberedskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid IT: Lösningar för katastrofåterställning
Du kan ha en katastrofåterställningslösning för DINA SQL Server-databaser i en hybrid-IT-miljö med hjälp av tillgänglighetsgrupper, databasspegling, loggleverans och säkerhetskopiering och återställning med Azure-blogglagring.

| Teknologi | Exempel på arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Vissa tillgänglighetsrepliker som körs i virtuella Azure-datorer och andra repliker som körs lokalt för haveriberedskap på flera platser. Produktionsplatsen kan vara antingen lokalt eller i ett Azure-datacenter.<br/>![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-alwayson.png)<br/>Eftersom alla tillgänglighetsrepliker måste finnas i samma redundanskluster måste klustret spänna över båda nätverken (ett redundanskluster med flera undernät). Den här konfigurationen kräver en VPN-anslutning mellan Azure och det lokala nätverket.<br/><br/>För en lyckad haveriberedskap av dina databaser bör du också installera en replikdomänkontrollant på haveriberedskapsplatsen.<br/><br/>Det är möjligt att använda guiden Lägg till replik i SSMS för att lägga till en Azure-replik i en befintlig grupp för alltid på tillgänglighet. Mer information finns i Självstudiekurs: Utöka din alltid på tillgänglighetsgrupp till Azure. |
| **Spegla databasen** |En partner som körs i en virtuell Azure-dator och den andra som körs lokalt för haveriberedskap på flera platser med hjälp av servercertifikat. Partner behöver inte vara i samma Active Directory-domän och ingen VPN-anslutning krävs.<br/>![Spegla databasen](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-dbmirroring.png)<br/>Ett annat databasspeglingsscenario innebär att en partner som körs i en virtuell Azure-dator och den andra körs lokalt i samma Active Directory-domän för haveriberedskap mellan platser. En [VPN-anslutning mellan det virtuella Azure-nätverket och det lokala nätverket](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) krävs.<br/><br/>För en lyckad haveriberedskap av dina databaser bör du också installera en replikdomänkontrollant på haveriberedskapsplatsen. SQL Server database mirroring is not supported for SQL Server 2008 nor SQL Server 2008 R2 on an Azure VM. |
| **Logga frakt** |En server som körs i en virtuell Azure-dator och den andra som körs lokalt för haveriberedskap på flera platser. Loggleverans beror på Windows-fildelning, så en VPN-anslutning mellan det virtuella Azure-nätverket och det lokala nätverket krävs.<br/>![Logga frakt](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-log-shipping.png)<br/>För en lyckad haveriberedskap av dina databaser bör du också installera en replikdomänkontrollant på haveriberedskapsplatsen. |
| **Säkerhetskopiering och återställning med Azure Blob Storage Service** |Lokala produktionsdatabaser säkerhetskopierade direkt till Azure blob-lagring för haveriberedskap.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-backup-restore.png)<br/>Mer information finns i [Säkerhetskopiering och återställning för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikera och redundans SQL Server till Azure med Azure Site Recovery** |Lokal produktion SQL Server replikeras direkt till Azure Storage för haveriberedskap.<br/>![Replikera med Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid-dr-standalone-sqlserver-asr.png)<br/>Mer information finns i [Skydda SQL Server med SQL Server-haveriberedskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="free-dr-replica-in-azure"></a>Gratis DR-replik i Azure

Om du har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)kan du implementera DR-planer (Hybrid Disaster Recovery) med SQL Server utan att ådra dig ytterligare licenskostnader för den passiva DR-instansen.

I avbildningen nedan använder installationen SQL Server som körs på en Virtuell Azure-dator med 12 kärnor som en katastrofåterställningsreplik för en lokal SQL Server-distribution med 12 kärnor. Tidigare måste du licensiera 12 kärnor i SQL Server för lokal och Azure Virtual Machine-distribution. Den nya fördelen erbjuder passiva replikfördelar som körs på en virtuell Azure-dator. Nu behöver du bara licensiera 12 kärnor av SQL Server som körs lokalt så länge som katastrofåterställningskriterierna för den passiva repliken på Azure Virtual Machine är uppfyllda.

![Gratis DR-replik i Azure](media/virtual-machines-windows-sql-high-availability-dr/free-dr-replica-azure.png)

Mer information finns i [produktlicensavtalsvillkoren](https://www.microsoft.com/licensing/product-licensing/products). 

Om du vill aktivera den här förmånen navigerar du till [den virtuella sql server-resursen](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), väljer **Konfigurera** under inställningar och väljer sedan alternativet **Haveriberedskap** under **SQL Server License**. Markera kryssrutan för att bekräfta att den här VIRTUELLA SQL Server-datorn används som en passiv replik och välj sedan **Använd** för att spara inställningarna. 

![Konfigurera DR-replik i Azure](media/virtual-machines-windows-sql-high-availability-dr/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Viktiga överväganden för SQL Server HADR i Azure
Virtuella azure-datorer, lagring och nätverk har andra operativa egenskaper än en lokal, icke-virtualiserad IT-infrastruktur. En lyckad implementering av en HADR SQL Server-lösning i Azure kräver att du förstår dessa skillnader och utformar din lösning för att hantera dem.

### <a name="high-availability-nodes-in-an-availability-set"></a>Noder med hög tillgänglighet i en tillgänglighetsuppsättning
Tillgänglighetsuppsättningar i Azure gör att du kan placera de högtillgänglighetsnoderna i separata feldomäner (FD: er) och uppdatera domäner (UDs). Varje virtuell dator i tillgänglighetsuppsättningen tilldelas en uppdateringsdomän och en feldomän av den underliggande Azure-plattformen. Den här konfigurationen i ett datacenter säkerställer att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse och uppfyller 99,95 % Azure SLA. Om du vill konfigurera konfigurationen av hög tillgänglighet placerar du alla deltagande virtuella SQL-datorer i samma tillgänglighetsuppsättning för att undvika program- eller dataförlust under en underhållshändelse. Endast noder i samma molntjänst kan delta i samma tillgänglighetsuppsättning. Mer information finns i [Hantera tillgängligheten för Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="high-availability-nodes-in-an-availability-zone"></a>Noder med hög tillgänglighet i en tillgänglighetszon
Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. Den fysiska separationen av tillgänglighetszoner inom en region skyddar program och data från datacenterfel genom att se till att minst en virtuell dator är tillgänglig och uppfyller 99,99 % Azure SLA. Om du vill konfigurera hög tillgänglighet placerar du deltagande virtuella SQL-datorer spridda över tillgängliga tillgänglighetszoner i regionen. Det kommer att finnas ytterligare avgifter för vm-till-VM-dataöverföring mellan tillgänglighetszoner. Mer information finns i [Tillgänglighetszoner](/azure/availability-zones/az-overview). 


### <a name="failover-cluster-behavior-in-azure-networking"></a>Redundansklusterbeteende i Azure-nätverk
Den icke-RFC-kompatibla DHCP-tjänsten i Azure kan orsaka att vissa redundansklusterkonfigurationer misslyckas på grund av att klusternätverksnamnet tilldelas en dubblett-IP-adress, till exempel samma IP-adress som en av klusternoderna. Det här är ett problem när du implementerar tillgänglighetsgrupper, vilket beror på windows-funktionen för redundanskluster.

Tänk på scenariot när ett kluster med två noder skapas och onlineas:

1. Klustret är online och sedan begär NODE1 en dynamiskt tilldelad IP-adress för klusternätverksnamnet.
2. Ingen annan IP-adress än NODE1:s egen IP-adress anges av DHCP-tjänsten, eftersom DHCP-tjänsten erkänner att begäran kommer från NODE1 själv.
3. Windows upptäcker att en dubblettadress har tilldelats både NODE1 och redundansklusternätverkets namn, och standardklustergruppen kan inte anslutas.
4. Standardklustergruppen flyttas till NODE2, som behandlar NODE1:s IP-adress som klustrets IP-adress och för standardklustergruppen online.
5. När NODE2 försöker upprätta anslutning med NODE1 lämnar paket som är riktade till NODE1 aldrig NODE2 eftersom node1:s IP-adress lösers till sig själv. NODE2 kan inte upprätta anslutning med NODE1 och förlorar sedan kvorum och stänger av klustret.
6. Under tiden kan NODE1 skicka paket till NODE2, men NODE2 kan inte svara. NODE1 förlorar kvorum och stänger av klustret.

Det här scenariot kan undvikas genom att tilldela en oanvänd statisk IP-adress, till exempel en länklokal IP-adress som 169.254.1.1, till klusternätverksnamnet för att koppla klusternätverksnamnet till. Information om hur du förenklar den här processen finns [i Konfigurera Windows redundanskluster i Azure för tillgänglighetsgrupper](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Mer information finns [i Konfigurera tillgänglighetsgrupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Stöd för tillgänglighetsgrupplyssnare
Lyssnare för tillgänglighetsgrupper stöds på virtuella Azure-datorer med Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Den här supporten möjliggörs med hjälp av belastningsbalanserade slutpunkter som är aktiverade på azure-virtuella datorer som är tillgänglighetsgruppnoder. Du måste följa särskilda konfigurationssteg för att lyssnarna ska fungera för både klientprogram som körs i Azure samt de som körs lokalt.

Det finns två huvudalternativ för att ställa in lyssnaren: extern (offentlig) eller intern. Den externa (offentliga) lyssnaren använder en internetvänd belastningsutjämnare och är associerad med en offentlig virtuell IP (VIP) som är tillgänglig via Internet. En intern lyssnare använder en intern belastningsutjämnare och stöder endast klienter inom samma virtuella nätverk. För någon av belastningsutjämnadstypen måste du aktivera direkt serverretur. 

Om tillgänglighetsgruppen sträcker sig över flera Azure-undernät (till exempel en distribution som korsar Azure-regioner), måste klientanslutningssträngen innehålla "**MultisubnetFailover=True**". Detta resulterar i parallella anslutningsförsök till replikerna i de olika undernäten. Instruktioner om hur du konfigurerar en lyssnare finns i

* [Konfigurera en ILB-lyssnare för tillgänglighetsgrupper i Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurera en extern lyssnare för tillgänglighetsgrupper i Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Du kan fortfarande ansluta till varje tillgänglighetsreplik separat genom att ansluta direkt till tjänstinstansen. Eftersom tillgänglighetsgrupper är bakåtkompatibla med databasspeglingsklienter kan du ansluta till tillgänglighetsrepliker som databasspeglingspartner så länge replikerna är konfigurerade på samma sätt som databasspegling:

* En primär replik och en sekundär replik
* Den sekundära repliken är konfigurerad som läsbar **(Läsbar sekundär** alternativ inställd på **Nr)**

En exempelklientanslutningssträng som motsvarar den här databasspeglingsliknande konfigurationen med ADO.NET eller inbyggd SQL Server-klient finns nedan:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Mer information om klientanslutning finns i:

* [Använda nyckelord för anslutningssträng med inbyggd SQL Server-klient](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ansluta klienter till en SQL-server (Database Mirroring Session)](https://technet.microsoft.com/library/ms175484.aspx)
* [Ansluta till tillgänglighet grupplyssnare i Hybrid IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Tillgänglighetsgruppavlyssnare, klientanslutning och SQL Server (Application Redundans)](https://technet.microsoft.com/library/hh213417.aspx)
* [Använda anslutningssträngar för databasspegling med tillgänglighetsgrupper](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Nätverksfördröjning i hybrid-IT
Du bör distribuera din HADR-lösning med antagandet att det kan finnas tidsperioder med hög nätverksfördröjning mellan det lokala nätverket och Azure. När du distribuerar repliker till Azure bör du använda asynkrona commit istället för synkron commit för synkroniseringsläget. När du distribuerar databasspeglingsservrar både lokalt och i Azure använder du högpresterande läge i stället för högsäkerhetsläget.

### <a name="geo-replication-support"></a>Stöd för georeplikering
Geo-replikering i Azure-diskar stöder inte datafilen och loggfilen i samma databas som ska lagras på separata diskar. GRS replikerar ändringar på varje disk oberoende och asynkront. Den här mekanismen garanterar skrivordern i en enda disk på den geore replikerade kopian, men inte över geore replikerade kopior av flera diskar. Om du konfigurerar en databas för att lagra datafilen och dess loggfil på separata diskar, kan de återställda diskarna efter en katastrof innehålla en mer aktuell kopia av datafilen än loggfilen, som bryter skriv-ahead loggen i SQL Server och ACID-egenskaperna för Transaktioner. Om du inte har möjlighet att inaktivera georeplikering på lagringskontot bör du behålla alla data och loggfiler för en viss databas på samma disk. Om du måste använda mer än en disk på grund av databasens storlek måste du distribuera en av katastrofåterställningslösningarna ovan för att säkerställa dataredundans.

## <a name="next-steps"></a>Nästa steg
Om du behöver skapa en virtuell Azure-dator med SQL Server läser [du Etablera en virtuell SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

Om du vill få bästa möjliga prestanda från SQL Server som körs på en virtuell Azure-dator läser du vägledningen i [metodtips för prestanda för SQL Server i virtuella Azure-datorer](virtual-machines-windows-sql-performance.md).

Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Andra resurser
* [Installera en ny Active Directory-skog i Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Skapa redundanskluster för tillgänglighetsgrupper i Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

