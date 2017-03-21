---
title: "Metodtips för Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver metodtips för Azure Site Recovery-distribution"
services: site-recovery
documentationCenter: 
author: rayne-wiselman"
manager: cfreeman
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-support-matrix-to-azure
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: a53e2a09fb2aabe96dd5347ea7509815d92bfdb8
ms.lasthandoff: 03/15/2017

---

# <a name="get-ready-to-deploy-azure-site-recovery"></a>Förbereda för distribution av Azure Site Recovery

Den här artikeln beskriver hur du förbereder för Azure Site Recovery-distribution.

## <a name="protecting-hyper-v-virtual-machines"></a>Skydda virtuella Hyper-V-datorer

Du har ett par distributionsalternativ för att skydda virtuella Hyper-V-datorer. Du kan replikera lokala virtuella Hyper-V-datorer till Azure, eller till ett sekundärt datacenter. Det finns olika krav för varje distribution.

**Krav** | **Replikera till Azure (med VMM)** | **Replikera virtuella Hyper-V-datorer till Azure (ingen VMM)** | **Replikera virtuella Hyper-V-datorer till en sekundär plats (med VMM)** | **Detaljer**
---|---|---|---|---
**VMM** | VMM körs på System Center 2012 R2 <br/><br/>Minst ett VMM-moln som innehåller en eller flera VMM-värdgrupper. | Ej tillämpligt | VMM-servrarna på de primära och sekundära platserna körs på minst System Center 2012 SP1 med de senaste uppdateringarna. <br/><br/> Minst ett moln på varje VMM-server. Moln ska vara konfigurerade med Hyper-V-kapacitetsprofilen.<br/><br/> Källmolnet bör ha minst en VMM-värdgrupp. | Valfri. Du behöver inte ha System Center VMM distribuerat för att kunna replikera virtuella Hyper-V-datorer till Azure, men om du har det måste du se till att VMM-servern är korrekt konfigurerad. Det omfattar att se till att du kör rätt VMM-version och att moln har konfigurerats.
**Hyper-V** | Minst en Hyper-V-värdserver på den lokala platsen som kör Windows Server 2012 R2 eller senare | Minst en Hyper-V-server på käll- och målplatserna som kör minst Windows Server 2012 med de senaste uppdateringarna installerade och är ansluten till Internet.<br/><br/> Hyper-V-servrarna måste finnas i en värdgrupp i ett VMM-moln. | Minst en Hyper-V-server på käll- och målplatserna som kör minst Windows Server 2012 med de senaste uppdateringarna installerade och är ansluten till Internet.<br/><br/> Hyper-V-servrarna måste finnas i en värdgrupp i ett VMM-moln. |
**Virtuella datorer** | Minst en virtuell dator på Hyper-V-värdservern | Minst en virtuell dator på Hyper-V-värdservern i källans VMM-moln | Minst en virtuell dator på Hyper-V-värdservern i källans VMM-moln. |  VM-replikering till Azure måste uppfylla kraven för virtuella datorer i Azure
**Azure-konto** | Du behöver ett Azure-konto och en prenumeration på Site Recovery-tjänsten. | Du behöver ett Azure-konto och en prenumeration på Site Recovery-tjänsten. | Ej tillämpligt | Om du inte har ett konto kan du börja med en kostnadsfri utvärderingsversion.
**Azure Storage** | Du behöver en prenumeration för ett Azure Storage-konto som har geo-replikering aktiverat. | Du behöver en prenumeration för ett Azure Storage-konto som har geo-replikering aktiverat. | Ej tillämpligt | Kontot ska finnas i samma region som Azure Site Recovery-valvet och vara kopplat till samma prenumeration.
**Nätverk** | Konfigurera nätverksmappning för att se till att alla datorer som redundansväxlar i samma Azure-nätverk kan ansluta till varandra, oavsett vilken återställningsplan de finns i. Om en nätverksgateway har konfigurerats i Azure-målnätverket kan virtuella datorer dessutom ansluta till andra lokala virtuella datorer. Om du inte konfigurerar nätverksmappning kan endast datorer som redundansväxlar i samma återställningsplan ansluta. | Ej tillämpligt |  <br/><br/>Konfigurera nätverksmappning för att se till att virtuella datorer är anslutna till lämpliga nätverk efter redundansväxling, och att replikerade virtuella datorer är optimalt placerade på Hyper-V-värdservrar. Om du inte konfigurerar nätverksmappning kommer inte replikerade datorer att vara anslutna till något virtuellt datornätverk efter redundansväxling. |  Om du vill konfigurera nätverksmappning med VMM måste du kontrollera att logiska och virtuella datornätverk i VMM är korrekt konfigurerade.
**Leverantörer och agenter** | Under distributionen installerar du Azure Site Recovery-providern på VMM-servrar. Du installerar Azure Recovery Services-agenten på Hyper-V-servrar i VMM-moln. | Under distributionen installerar du både Azure Site Recovery-leverantören och Azure Recovery Services-agenten på Hyper-V-värdservern eller klustret| Under distributionen installerar du Azure Site Recovery-providern på VMM-servrar. Du installerar Azure Recovery Services-agenten på Hyper-V-servrar i VMM-moln. | Leverantörer och agenter ansluter till Site Recovery över internet via en krypterad HTTPS-anslutning. Du behöver inte lägga till brandväggsundantag eller skapa en specifik proxy för leverantörsanslutningen.
**Internetanslutning** | Endast VMM-servrar måste ha en internetanslutning | Endast Hyper-V-värdservrar måste ha en internetanslutning | Endast VMM-servrar måste ha en internetanslutning | Inget behöver installeras på virtuella datorer och de ansluter inte direkt till internet.



## <a name="protect-vmware-virtual-machines-or-physical-servers"></a>Skydda virtuella VMware-datorer eller fysiska servrar

Det finns ett par distributionsalternativ för att skydda virtuella VMware-datorer eller fysiska Windows-/Linux-servrar. Du kan replikera dem till Azure, eller till ett sekundärt datacenter. Det finns olika krav för varje distribution.

**Krav** | **Replikera virtuella VMware-datorer/fysiska servrar till Azure)** | ***Replikera virtuella VMware-datorer/fysiska servrar till en sekundär plats**  
---|---|---
**Primär plats** | **Processerver**: en dedikerad Windows-server (fysisk eller virtuell) | **Processerver**: en dedikerad Windows-server (fysisk eller virtuell VMware-maskin)<br/><br/>  
**Sekundär lokal plats** | Ej tillämpligt | **Konfigurationsserver**: en dedikerad Windows-server (fysisk eller virtuell) <br/><br/> **Huvudmålserver**: en dedikerad server (fysisk eller virtuell). Konfigurera med Windows för att skydda Windows-datorer, eller Linux för att skydda Linux.
**Azure** | **Prenumeration**: du behöver en prenumeration för Site Recovery-tjänsten. <br/><br/> **Lagringskonto**: du behöver ett lagringskonto med geo-replikering aktiverad. Kontot ska finnas i samma region som Site Recovery-valvet och vara kopplat till samma prenumeration. <br/><br/> **Konfigurationsserver**: du måste konfigurera konfigurationsservern som en virtuell Azure-dator <br/><br/> **Huvudmålserver**: du måste konfigurera huvudmålservern som en virtuell Azure-dator <br/><br/> Konfigurera med Windows för att skydda Windows-datorer, eller Linux för att skydda Linux.<br/><br/> **Virtuellt Azure-nätverk**: du behöver ett virtuellt Azure-nätverk där konfigurationsservern och huvudmålservern distribueras. Det bör finnas i samma prenumeration och region som Azure Site Recovery-valvet | Ej tillämpligt  
**Virtuella datorer/fysiska servrar** | Minst en virtuell VMware-dator eller fysisk Windows-/Linux-server.<br/><br/>Mobilitetstjänsten installeras på varje dator under distributionen| Minst en virtuell VMware-dator eller fysisk Windows-/Linux-server.<br/><br/> Unified-agenten installeras på varje dator under distributionen.




## <a name="azure-virtual-machine-requirements"></a>Krav för virtuell Azure-dator

Du kan distribuera Site Recovery för att replikera virtuella datorer och fysiska servrar som kör ett operativsystem som stöds av Azure. Detta omfattar de flesta versioner av Windows och Linux. Du måste se till att lokala virtuella datorer som du vill skydda följer kraven för Azure.


## <a name="optimizing-your-deployment"></a>Optimera distributionen

Använd följande tips för att optimera och skala distributionen.

- **Volymstorlek för operativsystem**: när du replikerar en virtuell dator till Azure måste operativsystemets volym vara mindre än 1 TB. Om du har fler volymer än det kan du flytta dem manuellt till en annan disk innan distributionen inleds.
- **Datadiskstorlek**: om du replikerar till Azure kan du ha upp till 32 datadiskar på en virtuell dator, vardera på högst 1 TB. Du kan effektivt replikera och redundansväxla en virtuell dator på ~32 TB.
- **Begränsningar för återställningsplan**: Site Recovery kan skalas till tusentals virtuella datorer. Återställningsplaner är utformade som en modell för program som ska redundansväxla tillsammans, och därför begränsar vi antalet datorer i en återställningsplan till 50.
- **Begränsningar för Azure-tjänster**: varje Azure-prenumeration har en uppsättning standardgränser för kärnor, molntjänster osv. Vi rekommenderar att du kör ett redundanstest för att verifiera tillgängligheten för resurser i prenumerationen. Du kan ändra dessa gränser via Azure-supporten.
- **Kapacitetsplanering**: planera för skalning och prestanda.
- **Replikeringsbandbredd**: om du har för lite replikeringsbandbredd kan du tänka på följande:
    - **ExpressRoute**: Site Recovery fungerar med Azure ExpressRoute och WAN-optimerare, till exempel Riverbed.
    - **Replikeringstrafik**: Site Recovery utför en smart inledande replikering med endast datablock och inte hela den virtuella hårddisken. Endast ändringar replikeras under pågående replikering.
    - **Nätverkstrafik**: du kan styra nätverkstrafiken som används för replikering genom att konfigurera Windows QoS med en princip som baseras på IP-adressen och porten.  Om du dessutom replikerar till Azure Site Recovery använder du Azure Backup-agenten. Du kan konfigurera begränsning för den agenten.
- **Mål för återställningstid**: om du vill mäta målet för återställningstid (RTO) som du kan förvänta dig med Site Recovery föreslår vi att du kör ett redundanstest och tittar på Site Recovery-jobben för att analysera hur lång tid det tar att genomföra åtgärderna. Om redundansväxling sker till Azure rekommenderar vi att du automatiserar alla manuella åtgärder genom att integrera med Azure Automation och återställningsplaner för att få bästa mål för återställningstid.
- **Mål för återställningspunkt**: Site Recovery har stöd för näst intill synkront mål för återställningspunkt (RPO) när du replikerar till Azure. Detta förutsätter tillräckligt mycket bandbredd mellan datacentret och Azure.

