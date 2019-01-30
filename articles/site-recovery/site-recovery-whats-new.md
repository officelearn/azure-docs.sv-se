---
title: Vad är nytt i Azure Site Recovery | Microsoft Docs
description: Innehåller en översikt över nya funktioner som introduceras i Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: raynew
ms.openlocfilehash: 61e66a19b625141c69a9473373d3d5d808e18fde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211125"
---
# <a name="whats-new-in-site-recovery"></a>Nyheter i Site Recovery

Den [Azure Site Recovery](site-recovery-overview.md) tjänsten uppdateras och förbättras kontinuerligt. Som hjälper dig att hålla dig uppdaterad, den här artikeln innehåller information om de senaste versioner, nya funktioner och nytt innehåll. Den här sidan uppdateras regelbundet.

Om du har förslag på funktioner för Site Recovery kan vi vill gärna [höra dina synpunkter](https://feedback.azure.com/forums/256299-site-recovery).

## <a name="q1-2019"></a>Q1 2019

### <a name="linux-support"></a>Linux-support

[Uppdatera samlad 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) ger en uppdatering till Site Recovery-agenter och leverantörer. Uppdateringarna lägger till stöd för Linux på följande sätt:

- **Haveriberedskap för virtuella Azure-datorer**: RedHat arbetsstation 6/7. nya kernel-versioner för Ubuntu, Debian och SUSE.
- **Haveriberedskap för VMware-datorer/fysiska servrar till Azure**: Red Hat Enterprise Linux 7.6; RedHat arbetsstation 6/7. Oracle Linux 6.10/7.6 nya kernel-versioner Ubuntu, Debian och SUSE.



## <a name="q4-2018"></a>Q4 2018

## <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Priskalkylator för Azure VM-katastrofåterställning

Haveriberedskap för virtuella datorer i Azure tillkommer VM licensieringskostnaderna och kostnader för lagring och nätverk. Azure tillhandahåller en [priskalkylatorn](https://aka.ms/a2a-cost-estimator) kan du ta reda på dessa kostnader. Site Recovery erbjuder nu en [Prisexempel uppskattning](https://aka.ms/a2a-cost-estimator) som priser för en exempeldistribution baserad på en trelagers-app med sex virtuella datorer med 12 Standard HDD-diskar och 6 Premium SSD-diskar. Exemplet förutsätter en dataändring på 10 GB per dag för standard och 20 GB för premium. Du kan ändra variabler för att beräkna kostnaderna för din specifika distribution. Du kan ange hur många virtuella datorer, antal och typer av hanterade diskar och de förväntade totala dataändringshastighet förväntat över virtuella datorer. Du kan dessutom använda en komprimering faktor för att beräkna kostnader för bandbredd. [Läs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) meddelandet.

### <a name="support-for-azure-vms-in-zones"></a>Stöd för virtuella Azure-datorer i zoner

Azure tillgänglighetszoner är unika, fysiska platser inom en Azure-region. Varje zon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster. Du kan nu aktivera replikering för en Azure-dator och ange mål för redundansväxling till en enda VM-instans, en virtuell dator i en tillgänglighetsuppsättning eller en virtuell dator i en tillgänglighetszon. Inställningen påverkar inte replikering. [Läs](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) meddelandet.
 
### <a name="disaster-recovery-for-encrypted-vms"></a>Återställa krypterade virtuella datorer

Site Recovery stöder virtuella datorer i Azure krypteras med Azure Disk Encryption (ADE) med Azure AD-app. [Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="disaster-recovery-for-vms-using-accelerated-networking"></a>Haveriberedskap för virtuella datorer med accelererat nätverk

Accelererat nätverk aktivera i/o-virtualisering (SR-IOV) till en virtuell dator, förbättra nätverkets prestanda. När du aktiverar replikering för en Azure-dator, identifierar Site Recovery om accelererat nätverk är aktiverad. Om det är, efter redundans Site Recovery automatiskt konfigurerar accelererat nätverk på målet repliken Azure-dator, för både [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) och [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms). [Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="automatic-updates-for-the-mobility-service-extension"></a>Automatiska uppdateringar för mobilitetstjänsttillägget

Site Recovery läggs ett alternativ för automatiska uppdateringar till mobilitetstjänsttillägget. Mobilitetstjänsttillägget installeras på varje Azure virtuell dator som replikeras av Site Recovery. När du aktiverar replikering kan välja du om du vill tillåta Site Recovery för att hantera uppdateringar av tillägget. Uppdateringar kräver inte en omstart av virtuella datorer och påverkar inte replikering. [Läs mer](azure-to-azure-autoupdate.md).

### <a name="support-for-standard-ssd-disks"></a>Stöd för standard SSD-diskar

Azure introduceras [Standard Solid tillstånd-hårddiskar (SSD)](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) att tillhandahålla en kostnadseffektiv lösning för appar som webbservrar som behöver konsekvent prestanda, men har inte hög disk-IOPS. De kombinera element i premium SSD och HDD-standarddiskar. Site Recovery erbjuder haveriberedskap för virtuella Azure-datorer med hjälp av Standard SSD-disk. Som standard bevaras disktypen efter redundansväxlingen till målregionen.

### <a name="support-for-azure-storage-firewall"></a>Support för Azure storage-brandväggen

Du kan skydda Azure storage-konton till en specifik uppsättning nätverk genom att aktivera brandväggsregler för kontot. Du konfigurerar storage-konton för att neka trafik från interna nätverk och internet som standard och sedan bevilja åtkomst till trafik från specifika virtuella nätverk. Site Recovery stöder replikering för virtuella datorer med ohanterade diskar till brandväggen aktiverad lagringskonton till en sekundär region. Du kan välja storage-konton med brandväggar som aktiverats i målregionen för ohanterade diskar. Du kan också begränsa åtkomsten till cachelagringskontot genom att begränsa nätverksåtkomst till endast nätverk som virtuella datorer finns. Observera att du måste [Tillåt åtkomst](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) för betrodda Microsoft-tjänster.

## <a name="q3-2018"></a>Q3 2018 

### <a name="linux-support"></a>Linux-support

#### <a name="update-rollup-28"></a>Samlad uppdatering 28

[Uppdatera samlad 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) ger en uppdatering till Site Recovery-agenter och leverantörer. Uppdateringarna lägger till stöd för Linux på följande sätt:

- **Haveriberedskap för virtuella Azure-datorer**: Red Hat Enterprise Linux 6.10; CentOS 6.10; Linux-baserade virtuella datorer att använda partitionstypen GUID partition table (GPT) i äldre BIOS-kompatibilitetsläge stöds nu.
- **Haveriberedskap för VMware-datorer/fysiska servrar till Azure**: Red Hat Enterprise Linux 6.10; CentOS 6.10; Linux-baserade virtuella datorer att använda partitionstypen GUID partition table (GPT) i äldre BIOS-kompatibilitetsläge stöds nu.

#### <a name="update-rollup-27"></a>Samlad uppdatering 27

[Uppdatera samlad 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) ger en uppdatering till Site Recovery-agenter och leverantörer. Uppdateringarna lägger till stöd för Linux på följande sätt:

- **Haveriberedskap för virtuella Azure-datorer**: Red Hat Enterprise Linux 7.5
- **Haveriberedskap för VMware-datorer/fysiska servrar till Azure**: SUSE Linux Enterprise Server 12, Red Hat Enterprise Linux 7.5

### <a name="support-for-azure-vms-running-on-windows-server-2016"></a>Stöd för virtuella Azure-datorer som körs på Windows Server 2016

Azure virtuella datorer som körs på Windows Server 2016 kan replikeras mellan Azure-regioner med Azure Site Recovery.

### <a name="support-for-azure-vms-running-storage-spaces-direct"></a>Stöd för virtuella Azure-datorer som kör Lagringsdirigering

[Lagringsdirigering](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) (tillgänglig från Windows Server 2016 och senare) för att gruppera enheter i en lagringspool och använder sedan kapacitet från poolen för att skapa lagringsutrymmen. Lagringsutrymmen kan användas på en fristående virtuell dator på en [gästkluster för virtuella Azure-datorer](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) med hjälp av lokal lagring på varje nod i klustret eller delat lagringsutrymme i klustret.

## <a name="next-steps"></a>Nästa steg

Håll dig uppdaterad med vår uppdateringar på den [Azure uppdaterar](https://azure.microsoft.com/updates/?product=site-recovery) sidan.


