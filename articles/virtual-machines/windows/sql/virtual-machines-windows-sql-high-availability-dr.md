---
title: Hög tillgänglighet och katastrofåterställning för SQLServer | Microsoft Docs
description: En beskrivning av de olika typerna av HADR strategier för SQL Server som körs i Azure Virtual Machines.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 463ef5f4a655617074915078fb4ced9e596f8957
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257722"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines

Microsoft Azure-datorer (VM) med SQL Server kan sänka kostnaden för en hög tillgänglighet och katastrofåterställning (HADR) databasen återställningslösning. De flesta lösningar för SQL Server HADR stöds i Azure-datorer, både som endast finns i Azure och hybridlösningar. I en endast Azure-lösning kan körs hela HADR systemet i Azure. En hybridkonfiguration körs en del av lösningen i Azure och andra delen körs lokalt i din organisation. Flexibiliteten i Azure-miljön kan du flytta helt eller delvis till Azure för att uppfylla budget och HADR kraven för din SQL Server-databassystem.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Förstå behovet av en HADR-lösning
Det är upp till dig att se till att din databassystem har HADR-funktioner som kräver att servicenivåavtal (SLA). Det faktum att Azure tillhandahåller mekanismer för hög tillgänglighet, till exempel service återställning för molntjänster och återställning av felidentifiering för virtuella datorer, garanterar inte själva du uppnår önskat serviceavtalet. Dessa mekanismer skydda tillgängligheten för de virtuella datorerna, men inte hög tillgänglighet för SQL Server som körs på de virtuella datorerna. Det är möjligt för SQL Server-instansen till misslyckas medan den virtuella datorn är ansluten och felfri. Dessutom till och med hög tillgänglighet mekanismer som tillhandahålls av Azure tillåter driftstopp för de virtuella datorerna på grund av händelser, t.ex återställning från programvara eller maskinvarufel och uppgradering av operativsystemet.

Geografiskt Redundant lagring (GRS) i Azure, som implementeras med en funktion som kallas geo-replikering, kanske dessutom inte en tillräcklig haveriberedskapslösning för dina databaser. Eftersom geo-replikering skickar data asynkront, förlorade de senaste uppdateringarna i händelse av katastrof. Mer information om geo-replikering begränsningar beskrivs i den [Geo-replikering stöds inte för data och loggfiler på separata diskar](#geo-replication-support) avsnittet.

## <a name="hadr-deployment-architectures"></a>HADR distribution arkitekturer
SQL Server HADR tekniker som stöds i Azure är:

* [Always On-Tillgänglighetsgrupper](https://technet.microsoft.com/library/hh510230.aspx)
* [Alltid på instanser för redundanskluster](https://technet.microsoft.com/library/ms189134.aspx)
* [Loggöverföring](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänsten](https://msdn.microsoft.com/library/jj919148.aspx)
* [Databasspegling](https://technet.microsoft.com/library/ms189852.aspx) – inaktuell i SQLServer 2016

Det är möjligt att kombinera teknikerna tillsammans för att implementera en lösning för SQL Server som har både hög tillgänglighet och haveriberedskap. Beroende på den teknik som du använder kan en hybriddistribution kräva en VPN-tunnel med virtuella Azure-nätverket. I avsnitten nedan visar några av exempelarkitekturer för distributionen.

## <a name="azure-only-high-availability-solutions"></a>Endast Azure: lösningar för hög tillgänglighet

Du kan ha en lösning med hög tillgänglighet för SQL Server på en databasnivå med Always On-Tillgänglighetsgrupper - kallas Tillgänglighetsgrupper. Du kan också skapa en lösning med hög tillgänglighet på en instansnivå med alltid på Redundansklusterinstanser - redundans-instanser. För ytterligare redundans kan du skapa redundans på båda nivåerna genom att skapa Tillgänglighetsgrupper på redundans-instanser. 

| Teknologi | Exempelarkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglig replik som körs i virtuella Azure-datorer i samma region ger hög tillgänglighet. Du måste konfigurera en domänkontrollant VM, eftersom Windows-redundanskluster kräver en Active Directory-domän.<br/> ![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Mer information finns i [konfigurera Tillgänglighetsgrupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instanser för redundanskluster** |Redundans kluster instanser (FCI), som kräver delad lagring kan skapas på 3 olika sätt.<br/><br/>1. Ett redundanskluster med två noder som körs på virtuella Azure-datorer med direktansluten lagring med hjälp av [Lagringsdirigering för Windows Server 2016 \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) till en programvarubaserad virtuellt SAN-nätverk.<br/><br/>2. Ett redundanskluster med två noder, som körs på virtuella Azure-datorer med lagring som stöds av en tredje parts klusterlösningen. Ett exempel som använder SIOS DataKeeper, se [hög tillgänglighet för en filresurs med hjälp av redundanskluster och 3 tillverkare SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Ett redundanskluster med två noder, som körs på virtuella Azure-datorer med fjärr-iSCSI-mål som delad blocklagring via ExpressRoute. Till exempel visar NetApp Private Storage (NPS) en iSCSI-målet via ExpressRoute med Equinix till virtuella Azure-datorer.<br/><br/>För tredje parts delad lagring och replikering datalösningar kontaktar du leverantören för eventuella problem som rör åtkomst till data vid redundans.<br/><br/>Observera att med hjälp av FCI ovanpå [Azure File storage](https://azure.microsoft.com/services/storage/files/) stöds inte ännu, eftersom den här lösningen inte använda Premium Storage. Vi arbetar för att stödja detta snart. |

## <a name="azure-only-disaster-recovery-solutions"></a>Endast Azure: lösningar för Haveriberedskap
Du kan ha en lösning för katastrofåterställning för SQL Server-databaser i Azure med Tillgänglighetsgrupper, databasspegling eller säkerhetskopia och återställa med storage-blobbar.

| Teknologi | Exempelarkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighetsreplikerna som körs på flera datacenter i virtuella datorer i Azure för haveriberedskap. Den här interregionala lösningen skyddar mot fullständig eventuellt strömavbrott. <br/> ![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Alla repliker som ska vara i samma molntjänst och samma virtuella nätverk inom en region. Eftersom varje region har ett separat virtuellt nätverk ska kräva dessa lösningar VNet till VNet-anslutning. Mer information finns i [konfigurera en VNet-till-VNet-anslutning med Azure-portalen](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Detaljerade anvisningar finns i [och konfigurera en SQL Server-tillgänglighetsgrupp på Azure virtuella datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Databasspegling** |Huvudnamn och spegling och servrar som körs i olika Datacenter för haveriberedskap. Du måste distribuera med servercertifikat eftersom active directory-domänen inte kan omfatta flera datacenter.<br/>![Databasspegling](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Säkerhetskopiering och återställning med Azure Blob Storage-tjänsten** |Produktionsdatabaserna som säkerhetskopieras direkt till blob storage i ett annat datacenter för haveriberedskap.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Mer information finns i [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikera och Redundansväxla SQL Server till Azure med Azure Site Recovery** |Produktion SQL Server för en Azure-datacenter som replikeras direkt till Azure Storage för olika Azure-datacenter för haveriberedskap.<br/>![Replikera med Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Mer information finns i [skydda SQL Server med SQL Server-haveriberedskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid-IT: Lösningar för Haveriberedskap
Du kan ha en lösning för katastrofåterställning för SQL Server-databaser i en hybrid-IT-miljö med Tillgänglighetsgrupper, databasspegling, loggöverföring och säkerhetskopiering och Återställ med Azure BLOB-lagring.

| Teknologi | Exempelarkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Vissa tillgänglighetsrepliker som körs i virtuella Azure-datorer och andra repliker som körs lokalt för skriptkörning katastrofåterställning. Produktionsplatsen kan vara antingen lokalt eller i ett Azure-datacenter.<br/>![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Eftersom alla tillgänglighetsrepliker måste vara i samma kluster för växling vid fel, måste klustret omfatta båda nätverken (ett flera undernät failover-kluster). Den här konfigurationen kräver en VPN-anslutning mellan Azure och lokala nätverk.<br/><br/>För lyckad haveriberedskap för dina databaser, bör du också installera en replikeringsdomänkontrollant på katastrofåterställningsplatsen.<br/><br/>Det är möjligt att använda guiden Lägg till repliken i SSMS att lägga till en Azure-replik i en befintlig Always On Availability Group. Mer information finns i självstudien: utöka din ständigt aktiverad tillgänglighetsgrupp till Azure. |
| **Databasspegling** |En partner som körs i en Azure-dator och de andra körs lokalt för skriptkörning haveriberedskap med hjälp av servercertifikat. Partner behöver inte finnas i samma Active Directory-domän och ingen VPN-anslutning krävs.<br/>![Databasspegling](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Ett annat scenario för databasspegling innebär en partner som körs i en Azure-dator och de andra som körs lokalt i samma Active Directory-domänen för skriptkörning katastrofåterställning. En [VPN-anslutning mellan virtuella Azure-nätverket och det lokala nätverket](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) krävs.<br/><br/>För lyckad haveriberedskap för dina databaser, bör du också installera en replikeringsdomänkontrollant på katastrofåterställningsplatsen. |
| **Loggöverföring** |En server som körs i en Azure-dator och de andra körs lokalt för skriptkörning katastrofåterställning. Loggöverföring beror på Windows fildelning, så det krävs en VPN-anslutning mellan virtuella Azure-nätverket och det lokala nätverket.<br/>![Loggöverföring](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>För lyckad haveriberedskap för dina databaser, bör du också installera en replikeringsdomänkontrollant på katastrofåterställningsplatsen. |
| **Säkerhetskopiering och återställning med Azure Blob Storage-tjänsten** |Lokala produktionsdatabaserna säkerhetskopieras direkt till Azure blob storage för katastrofåterställning.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Mer information finns i [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikera och Redundansväxla SQL Server till Azure med Azure Site Recovery** |Lokala produktion SQL Server replikeras direkt till Azure Storage för katastrofåterställning.<br/>![Replikera med Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Mer information finns i [skydda SQL Server med SQL Server-haveriberedskap och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Att tänka på för SQL Server-HADR i Azure
Azure virtuella datorer, lagring och nätverk ha andra operativa egenskaper än en lokal, icke-virtualiserade IT-infrastruktur. En lyckad implementering av en HADR SQL Server-lösning i Azure måste du förstå dessa skillnader och utforma din lösning anpassas till dem.

### <a name="high-availability-nodes-in-an-availability-set"></a>Noder med hög tillgänglighet i en tillgänglighetsuppsättning
Tillgänglighetsuppsättningar i Azure kan du placera noder med hög tillgänglighet i separata Feldomäner (fd) och Uppdateringsdomäner (ud). För virtuella Azure-datorer ska placeras i samma tillgänglighetsuppsättning måste du distribuera dem i samma molntjänst. Endast noder i samma molntjänst kan delta i samma tillgänglighetsuppsättning. Mer information finns i [Hantera tillgängligheten för Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Kluster-funktion i Azure-nätverk
Icke-RFC-kompatibel DHCP-tjänsten i Azure kan orsaka skapandet av vissa redundans klusterkonfigurationer misslyckas på grund av klustrets nätverksnamn tilldelas en duplicerad IP-adress, till exempel samma IP-adress som en av noderna i klustret. Det här är ett problem när du implementerar Tillgänglighetsgrupper, som är beroende av funktionen för redundanskluster i Windows.

Föreställ dig när ett tvånods kluster skapas och tas online:

1. Klustret är online och Nod1 begär en dynamiskt tilldelad IP-adress för klustrets nätverksnamn.
2. Ingen IP-adress än Nod1 's egna IP-adress anges av DHCP-tjänsten eftersom DHCP-tjänsten känner av att begäran kommer från Nod1 själva.
3. Windows upptäcker att en dubblett-adress har tilldelats både Nod1 och nätverksnamn för failover-kluster och kluster standardgruppen inte går att använda.
4. Standard-klustergrupp flyttar till nod2 behandlas Nod1 's IP-adress som klustrets IP-adress och tar klustret standardgruppen online.
5. När nod2 försöker upprätta en anslutning med Nod1, lämna paket riktat mot Nod1 aldrig nod2 eftersom det löser Nod1 's IP-adress till sig själv. Nod2 det går inte att upprätta en anslutning med Nod1 sedan förlorar kvorum och stänger av klustret.
6. Under tiden kan Nod1 kan skicka paket till nod2, men nod2 kan inte svara. Nod1 förlorar kvorum och stänger av klustret.

Det här scenariot kan undvikas genom att tilldela en oanvänd statiska IP-adress, till exempel en länk-lokala IP-adress som 169.254.1.1, Klusternätverksnamnet för att hämta klustrets nätverksnamn online. För att förenkla den här processen kan se [konfigurerar Windows failover-kluster i Azure för Tillgänglighetsgrupper](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Mer information finns i [och konfigurera Tillgänglighetsgrupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Support för tillgänglighet grupp lyssnare
Availability-grupplyssnare stöds på virtuella Azure-datorer som kör Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Det här stödet är möjligt med hjälp av belastningsutjämnade slutpunkter har aktiverats på virtuella Azure-datorer som är tillgängliga gruppnoder. Du måste följa särskilda konfigurationssteg för lyssnare ska fungera för båda klientprogram som körs i Azure samt de som körs lokalt.

Det finns två huvudsakliga alternativ för att konfigurera din lyssnare: externa (offentliga) eller intern. Den externa (offentliga) lyssnaren använder en belastningsutjämnare mot internet och är associerad med en offentlig virtuell IP (VIP) som kan nås via internet. En intern lyssnare använder en intern belastningsutjämnare och har bara stöd för klienter i samma virtuella nätverk. Läsa in typ av belastningsutjämnare för antingen, måste du aktivera direkt Serverreturnering. 

Om Tillgänglighetsgruppen sträcker sig över flera Azure-undernät (till exempel en distribution som korsar Azure-regioner), klient-anslutningssträngen måste innehålla ”**MultisubnetFailover = True**”. Detta resulterar i parallella anslutningsförsök till kopior i olika undernät. Anvisningar om hur du konfigurerar en lyssnare finns i

* [Konfigurera en ILB-lyssnare för Tillgänglighetsgrupper i Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurera en extern lyssnare för Tillgänglighetsgrupper i Azure](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md).

Du kan fortfarande ansluta till varje tillgänglighetsreplik separat för genom att ansluta direkt till tjänstinstansen. Dessutom eftersom Tillgänglighetsgrupper är bakåtkompatibel med klienter för databasspegling, kan du ansluta till tillgänglighetsrepliker som databasspegling partner så länge replikerna konfigureras liknar databasspegling:

* En primär replik och en sekundär replik
* Den sekundära repliken är konfigurerad som inte kan läsas (**läsbara sekundära** alternativet inställt på **nr**)

Ett exempel klient-anslutningssträng som motsvarar det här spegling-liknande konfiguration med hjälp av ADO.NET eller SQL Server Native Client är nedan:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Mer information om klientanslutning finns:

* [Med SQL Server Native Client nyckelord för anslutningssträngar](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ansluta klienter till en Databasspeglingssession (SQLServer)](https://technet.microsoft.com/library/ms175484.aspx)
* [Ansluta till Lyssningsfunktion i Hybrid-IT](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Tillgänglighetsgruppens lyssnare och klientanslutning programmet växling vid fel (SQLServer)](https://technet.microsoft.com/library/hh213417.aspx)
* [Använda databasspegling anslutningssträngar med Tillgänglighetsgrupper](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Svarstid för nätverk i hybrid-IT
Distribuera din HADR-lösning med antagandet att det kan finnas tidsperioder med hög Nätverksfördröjningen mellan ditt lokala nätverk och Azure. När du distribuerar repliker till Azure, bör du använda asynkron incheckning i stället för synkront genomförande för Synkroniseringsläge. När distribuera servrar för databasspegling både lokalt och i Azure, använda läget för hög prestanda i stället för läget för hög säkerhet.

### <a name="geo-replication-support"></a>Stöd för GEO-replikering
GEO-replikering i Azure-diskar stöder inte datafilen och loggfilen av samma databas som ska lagras på olika diskar. GRS replikerar ändringar på varje disk oberoende av varandra och asynkront. Den här mekanismen garanterar skrivning ordning inom en enskild disk på den geo-replikerade kopian, men inte mellan geo-replikerade kopior av flera diskar. Om du konfigurerar en databas för att lagra dess datafilen och loggfilen på olika diskar, kan de återställda diskarna efter en katastrof innehålla en uppdaterad kopia av datafilen än loggfil, vilket bryter write-ahead loggen i SQL Server och hä ACID-egenskaper ctions. Om du inte har alternativet att inaktivera geo-replikering på storage-konto bör du behålla alla data och loggfiler lagras för en viss databas på samma disk. Om du måste använda mer än en disk på grund av databasens storlek, måste du distribuera en av haveriberedskapslösningar ovan för att dataredundans.

## <a name="next-steps"></a>Nästa steg
Om du vill skapa en Azure virtuell dator med SQL Server finns i [etablera en SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

För att få bästa möjliga prestanda från SQL Server som körs på en Azure VM, finns i riktlinjerna i [Prestandametodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Andra ämnen som rör som kör SQL Server i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Andra resurser
* [Installera en ny Active Directory-skog i Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Skapa Failover-kluster för Tillgänglighetsgrupper i Azure VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

