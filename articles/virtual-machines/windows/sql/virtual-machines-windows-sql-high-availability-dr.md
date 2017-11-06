---
title: "Hög tillgänglighet och Haveriberedskap för SQLServer | Microsoft Docs"
description: "En beskrivning av de olika typerna av HADR strategier för SQL Server som körs i Azure Virtual Machines."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: a81b956107ef82f40ad5304808068a7573ca7d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines

Microsoft Azure-datorer (VM) med SQL Server kan sänka kostnaden för en hög tillgänglighet och haveriberedskap (HADR) databasen återställningslösning. De flesta lösningar för SQL Server HADR stöds i Azure-datorer, både som endast Azure och som hybridlösningar. I en endast Azure-lösning kan körs hela HADR systemet i Azure. I en hybrid-konfiguration körs en del av lösningen i Azure och andra delen körs lokalt i din organisation. Möjlighet att Azure-miljön kan du flytta helt eller delvis till Azure för att uppfylla budget och HADR kraven i din SQL Server-databassystem.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Förstå behovet av en HADR-lösning
Det är att säkerställa att Databassystemet har HADR-funktioner som kräver servicenivåavtal (SLA). Det faktum som Azure tillhandahåller mekanismer för hög tillgänglighet, såsom tjänsten återställning för molntjänster och återställning av felidentifiering för virtuella datorer garanterar inte själv kan du uppfylla SERVICENIVÅAVTALET för önskad. Dessa mekanismer skydda hög tillgänglighet för de virtuella datorerna, men inte hög tillgänglighet för SQL Server som körs i de virtuella datorerna. Det är möjligt för SQL Server-instansen misslyckas medan den virtuella datorn är ansluten och fungerar. Dessutom med hög tillgänglighet metoder som tillhandahålls av Azure tillåta driftstopp på de virtuella datorerna på grund av händelser, t.ex återställning från programvara eller maskinvarufel och uppgradering av operativsystemet.

Dessutom kanske inte Geo-Redundant lagring (GRS) i Azure som implementeras med en funktion som kallas geo-replikering, en lösning för tillräcklig katastrofåterställning för dina databaser. Eftersom geo-replikering skickar data asynkront, förloras de senaste uppdateringarna vid katastrofåterställning. Mer information om begränsningar för geo-replikering beskrivs i den [georeplikering stöds inte för data och loggfiler på separata diskar](#geo-replication-support) avsnitt.

## <a name="hadr-deployment-architectures"></a>HADR distribution arkitekturer
SQL Server HADR tekniker som stöds i Azure är:

* [Always On-Tillgänglighetsgrupper](https://technet.microsoft.com/library/hh510230.aspx)
* [Alltid på instanser för redundanskluster](https://technet.microsoft.com/library/ms189134.aspx)
* [Loggöverföring](https://technet.microsoft.com/library/ms187103.aspx)
* [SQL Server-säkerhetskopiering och återställning med Azure Blob Storage-tjänst](https://msdn.microsoft.com/library/jj919148.aspx)
* [Databasspegling](https://technet.microsoft.com/library/ms189852.aspx) – inte längre stöds i SQLServer 2016

Det är möjligt att kombinera tekniker tillsammans för att implementera en lösning för SQL Server som har funktioner för katastrofåterställning och hög tillgänglighet. Beroende på vilken teknik som du använder, kan en hybriddistribution kräver en VPN-tunnel med virtuella Azure-nätverket. I avsnitten nedan visar några exempel distributionen arkitekturerna.

## <a name="azure-only-high-availability-solutions"></a>Endast Azure-: lösningar för hög tillgänglighet

Du kan ha en lösning för hög tillgänglighet för SQL Server på en databasnivå med alltid på Tillgänglighetsgrupper - kallas Tillgänglighetsgrupper. Du kan också skapa en lösning för hög tillgänglighet på en instansnivå med alltid på Redundansklusterinstanser - redundans-instanser. För ytterligare redundans skapar du redundans på båda nivåerna genom att skapa Tillgänglighetsgrupper på redundans-instanser. 

| Teknologi | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighetsrepliker som körs i virtuella Azure-datorer i samma region ger hög tillgänglighet. Du måste konfigurera en domänkontrollant VM, eftersom Windows-redundanskluster kräver en Active Directory-domän.<br/> ![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Mer information finns i [konfigurera Tillgänglighetsgrupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **-Instanser för redundanskluster** |Failover Cluster instanser (FCI), som kräver delad lagring, kan skapas på 3 olika sätt.<br/><br/>1. En tvånods-kluster som kör Azure-dator med direktansluten lagring med hjälp av [Windows Server 2016 Storage Spaces Direct \(S2D\) ](virtual-machines-windows-portal-sql-create-failover-cluster.md) att tillhandahålla en programvarubaserad virtuellt SAN-nätverk.<br/><br/>2. Ett redundanskluster med två noder, körs i virtuella Azure-datorer med lagring som stöds av en tredje parts klusterlösningen. Ett exempel som använder SIOS DataKeeper, se [hög tillgänglighet för en filresurs med hjälp av redundanskluster och 3 tillverkare SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>3. Ett redundanskluster med två noder, körs i virtuella Azure-datorer med fjärr-iSCSI-mål som delad blocklagring via ExpressRoute. NetApp privat lagring (NPS) visar till exempel ett iSCSI-mål via ExpressRoute med Equinix till virtuella Azure-datorer.<br/><br/>För tredje parts delad lagring och lösningar för replikering, bör du kontakta leverantören för problem relaterade till åtkomst av data på växling vid fel.<br/><br/>Observera att med hjälp av FCI ovanpå [Azure File storage](https://azure.microsoft.com/services/storage/files/) stöds inte ännu, eftersom den här lösningen inte att använda Premium-lagring. Vi arbetar för att stödja detta snart. |

## <a name="azure-only-disaster-recovery-solutions"></a>Endast Azure-: lösningar för katastrofåterställning
Du kan ha en lösning för katastrofåterställning för SQL Server-databaser i Azure med hjälp av Tillgänglighetsgrupper, databasspegling eller säkerhetskopia och återställa med storage-blobbar.

| Teknologi | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Tillgänglighetsrepliker som körs i flera datacenter i virtuella Azure-datorer för katastrofåterställning. Den här lösningen mellan region skyddar mot fullständig webbplats avbrott. <br/> ![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Alla repliker ska vara i samma molntjänst och samma virtuella nätverk i en region. Eftersom varje region har ett separat virtuellt nätverk, kräver dessa lösningar VNet till VNet-anslutning. Mer information finns i [konfigurerar du en VNet-till-VNet-anslutning med hjälp av Azure portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Detaljerade instruktioner finns [och konfigurera en SQL Server-tillgänglighetsgrupp på Azure-datorer i olika regioner](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Databasspegling** |Huvudnamn och spegling och servrar som körs i olika Datacenter för katastrofåterställning. Du måste distribuera med servercertifikat eftersom en active directory-domän kan sträcka sig över flera datacenter.<br/>![Databasspegling](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Säkerhetskopiering och återställning med Azure Blob Storage-tjänst** |Produktionsdatabaserna som säkerhetskopierats direkt till blob storage i olika datacenter för katastrofåterställning.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Mer information finns i [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikering och redundans SQL Server till Azure med Azure Site Recovery** |Produktion SQL Server för en Azure-datacenter som replikeras direkt till Azure Storage för olika Azure-datacenter för katastrofåterställning.<br/>![Replikera med Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Mer information finns i [skydda SQL Server med SQL Server-katastrofåterställning och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>Hybrid-IT: Lösningar för katastrofåterställning
Du kan ha en lösning för katastrofåterställning för SQL Server-databaser i en hybrid-IT-miljö med hjälp av Tillgänglighetsgrupper, databasspegling, loggöverföring och säkerhetskopiering och återställning med Azure blogg lagring.

| Teknologi | Exempel arkitekturer |
| --- | --- |
| **Tillgänglighetsgrupper** |Vissa tillgänglighetsrepliker som körs i virtuella Azure-datorer och andra repliker som körs lokalt för katastrofåterställning för webbplatser. Produktionsplatsen kan vara antingen lokalt eller i ett Azure-datacenter.<br/>![Tillgänglighetsgrupper](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Eftersom alla tillgänglighetsrepliker måste vara i samma kluster för växling vid fel måste i klustret omfatta båda nätverken (ett flera undernät failover-kluster). Den här konfigurationen kräver en VPN-anslutning mellan Azure och det lokala nätverket.<br/><br/>För lyckad katastrofåterställning för dina databaser, bör du också installera en replika-domänkontroller på disaster recovery-plats.<br/><br/>Det är möjligt att använda guiden Lägg till replik i SSMS att lägga till en Azure replik till en befintlig alltid på tillgänglighetsgrupp. Mer information finns i självstudiekursen: utöka alltid på Tillgänglighetsgruppen till Azure. |
| **Databasspegling** |En partner som körs i en virtuell dator i Azure och den andra körs lokalt för katastrofåterställning för webbplatser som använder servercertifikat. Partners behöver inte finnas i samma Active Directory-domän och någon VPN-anslutning krävs.<br/>![Databasspegling](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Ett annat scenario för databasspegling innebär en partner som körs i en virtuell dator i Azure och den andra körs lokalt i samma Active Directory-domän för katastrofåterställning för webbplatser. En [VPN-anslutning mellan Azure-nätverket och det lokala nätverket](../../../vpn-gateway/vpn-gateway-site-to-site-create.md) krävs.<br/><br/>För lyckad katastrofåterställning för dina databaser, bör du också installera en replika-domänkontroller på disaster recovery-plats. |
| **Loggöverföring** |En server som körs i en virtuell dator i Azure och den andra körs lokalt för katastrofåterställning för webbplatser. Loggöverföring beror på Windows fildelning, så krävs en VPN-anslutning mellan Azure-nätverket och det lokala nätverket.<br/>![Loggöverföring](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>För lyckad katastrofåterställning för dina databaser, bör du också installera en replika-domänkontroller på disaster recovery-plats. |
| **Säkerhetskopiering och återställning med Azure Blob Storage-tjänst** |Lokalt produktionsdatabaserna säkerhetskopieras direkt till Azure blob storage för katastrofåterställning.<br/>![Säkerhetskopiering och återställning](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Mer information finns i [säkerhetskopiering och återställning av SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md). |
| **Replikering och redundans SQL Server till Azure med Azure Site Recovery** |Lokal produktion SQL Server replikeras direkt till Azure Storage för katastrofåterställning.<br/>![Replikera med Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Mer information finns i [skydda SQL Server med SQL Server-katastrofåterställning och Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Viktiga överväganden för SQL Server HADR i Azure
Azure virtuella datorer, lagring och nätverk har olika operativa egenskaper än en lokal, icke-virtualiserade IT-infrastruktur. En lyckad implementering av en HADR SQL Server-lösning i Azure måste du förstå dessa skillnader och utforma din lösning anpassas till dem.

### <a name="high-availability-nodes-in-an-availability-set"></a>Hög tillgänglighet noder i en tillgänglighetsuppsättning
Tillgänglighetsuppsättningar i Azure kan du placera noder för hög tillgänglighet i separata Feldomäner (FDs) och uppdatera domäner (UDs). Du måste distribuera dem i samma molntjänst för virtuella Azure-datorer ska placeras i samma tillgänglighetsuppsättning. Endast noder i samma molntjänst kan delta i samma tillgänglighetsuppsättning. Mer information finns i [Hantera tillgängligheten för Virtual Machines](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Failover cluster beteende i Azure-nätverk
Icke-RFC-kompatibel DHCP-tjänsten i Azure kan orsaka att skapa vissa redundans klusterkonfigurationer misslyckas på grund av klustrets nätverksnamn tilldelas en duplicerad IP-adress som samma IP-adressen som en av klusternoderna. Detta är ett problem när du implementerar Tillgänglighetsgrupper, som beror på funktionen Windows kluster för växling vid fel.

Föreställ dig när ett tvånods kluster skapas och tas online:

1. Klustret är online och sedan Nod1 begär en dynamiskt tilldelad IP-adress för klustrets nätverksnamn.
2. Ingen IP-adress än Nod1 's egna IP-adress ges av DHCP-tjänsten, eftersom DHCP-tjänsten känner av att begäran kommer från Nod1 sig själv.
3. Windows upptäcker att en dubblett-adress har tilldelats både Nod1 och Klusternätverksnamnet för växling vid fel och klustergrupp standard inte anslutas.
4. Standard-klustergrupp flyttar till nod2, behandlas Nod1 's IP-adress som IP-adress för klustret och tar klustret standardgruppen online.
5. När nod2 försöker upprätta en anslutning med Nod1, lämna paket riktat mot Nod1 aldrig nod2 eftersom det löser Nod1 's IP-adress till sig själv. Nod 2 kan inte upprätta en anslutning med Nod1 sedan förlorar kvorum och stängs av klustret.
6. Under tiden Nod1 kan skicka paket till nod2, men nod2 kan inte svara. Nod1 förlorar kvorum och stängs av klustret.

Det här scenariot kan undvikas genom att tilldela en oanvända statiska IP-adress, till exempel en länklokal IP-adress som 169.254.1.1, klustrets nätverksnamn för att hämta klustrets nätverksnamn online. För att förenkla den här processen, se [konfigurera Windows-redundanskluster i Azure för Tillgänglighetsgrupper](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Mer information finns i [och konfigurera Tillgänglighetsgrupper i Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Stöd för tillgänglighet lyssnare
Tillgänglighet tillgänglighetsgruppslyssnarnas stöds på Azure virtuella datorer som kör Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 och Windows Server 2016. Detta stöd är möjligt med hjälp av belastningsutjämnade slutpunkter aktiverat på den virtuella Azure-datorer som är tillgänglighet gruppnoder. Du måste följa särskilda konfigurationssteg för lyssnare ska fungera för båda klientprogram som körs i Azure som körs lokalt.

Det finns två huvudsakliga alternativ för att konfigurera din lyssnare: externa (offentliga) eller intern. Externa (offentliga) lyssnaren använder en internetuppkopplad belastningsutjämnare och associeras med en public Virtual IP (VIP) som är tillgänglig via internet. En intern lyssnare använder en intern belastningsutjämnare och har endast stöd för klienter i samma virtuella nätverk. För att läsa in typen belastningsutjämnare, måste du aktivera direkt Serverreturnering. 

Om Tillgänglighetsgruppen sträcker sig över flera Azure-undernät (till exempel en distribution som passerar Azure-regioner), klient-anslutningssträngen måste innehålla ”**MultisubnetFailover = True**”. Detta resulterar i parallella anslutningsförsök till replikerna i olika undernät. Anvisningar om hur du skapar en lyssnare finns

* [Konfigurera en ILB-lyssnare för Tillgänglighetsgrupper i Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Konfigurera en extern lyssnare för Tillgänglighetsgrupper i Azure](../classic/ps-sql-ext-listener.md).

Du kan fortfarande ansluta till varje tillgänglighetsreplik separat för genom att ansluta direkt till tjänstinstansen. Dessutom eftersom Tillgänglighetsgrupper är bakåtkompatibel med klienter för databasspegling, kan du ansluta till tillgänglighetsrepliker som databasspegling partners så länge replikerna konfigureras liknar databasspegling:

* En primär replik och en sekundär replik
* Den sekundära repliken är konfigurerad som inte kan läsas (**läsbara sekundära** alternativet **nr**)

Ett exempel klient-anslutningssträngen som motsvarar den här databasen spegling liknande konfigurationen med hjälp av ADO.NET eller SQL Server Native Client är nedan:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Mer information om klientanslutning finns:

* [Nyckelord för anslutningssträngar med SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Ansluta klienter till en Databasspeglingssession (SQLServer)](https://technet.microsoft.com/library/ms175484.aspx)
* [Ansluter till Availability Group Listener i Hybrid IT](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Tillgänglighetsgruppens lyssnare och klientanslutning programmet växling vid fel (SQLServer)](https://technet.microsoft.com/library/hh213417.aspx)
* [Använda databasspegling anslutningssträngar med Tillgänglighetsgrupper](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Nätverksfördröjningen i hybrid IT
Du ska distribuera lösningen HADR med antagandet att det kan finnas perioder med hög Nätverksfördröjningen mellan ditt lokala nätverk och Azure. När du distribuerar repliker till Azure, bör du använda asynkront genomförande i stället för synkront genomförande för Synkroniseringsläge. När distribuera servrar för databasspegling både lokalt och i Azure, använder du högpresterande läget i stället för läget för hög säkerhet.

### <a name="geo-replication-support"></a>Stöd för GEO-replikering
GEO-replikering i Azure-diskar som stöder inte datafilen och loggfilen av samma databas som ska lagras på separata diskar. GRS replikeras ändringar på varje disk oberoende av varandra och asynkront. Den här mekanismen garanterar att skriva ordning inom en enskild disk på Kopiera georeplikerad, men inte över geo-replikerade kopior av flera diskar. Om du konfigurerar en databas för att lagra dess datafilen och loggfilen på separata diskar kan återställda diskar efter en katastrof innehålla en uppdaterad kopia av filen än loggfilen som bryter write-ahead loggen i SQL Server och transa ACID-egenskaper ctions. Om du inte har alternativet att inaktivera geo-replikering för storage-konto bör du behålla alla data och loggfilen filer för en viss databas på samma disk. Om du måste använda mer än en disk på grund av storleken på databasen, måste du distribuera en katastrofåterställning ovan för att dataredundans.

## <a name="next-steps"></a>Nästa steg
Om du behöver skapa en virtuell Azure-dator med SQL Server finns [etablering av en SQL Server-dator på Azure](virtual-machines-windows-portal-sql-server-provision.md).

För att få bästa möjliga prestanda från SQL Server körs på en virtuell dator i Azure finns i [prestandarelaterade Metodtips för SQL Server i Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Andra avsnitt relaterade till SQL Server som körs i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Andra resurser
* [Installera en ny Active Directory-skog i Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Skapa Failover-kluster för Tillgänglighetsgrupper i Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

