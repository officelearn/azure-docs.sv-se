---
title: Stöd för fysisk servermigrering i Azure Migrate
description: Läs mer om stöd för fysisk servermigrering i Azure Migrate.
ms.topic: conceptual
ms.custom: fasttrack-edit
ms.date: 01/07/2020
ms.openlocfilehash: 8f8b94ab77a1eef8e771384f5d69da98a1d7ae6c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520290"
---
# <a name="support-matrix-for-physical-server-migration"></a>Stödmatris för fysisk servermigrering

Den här artikeln sammanfattar supportinställningar och begränsningar för migrering av fysiska servrar med [Azure Migrate: Servermigrering](migrate-services-overview.md#azure-migrate-server-migration-tool) . Om du letar efter information om hur du bedömer fysiska servrar för migrering till Azure läser du [utvärderingssupportmatrisen](migrate-support-matrix-physical.md).


## <a name="overview"></a>Översikt

Du kan migrera lokala datorer som fysiska servrar med hjälp av agentbaserad replikering. Med det här verktyget kan du migrera ett brett utbud av datorer till Azure:

- Lokala fysiska servrar.
- Virtuella datorer virtualiserade av plattformar som Xen, KVM.
- Virtuella datorer med hyper-V eller virtuella datorer med VMware om du av någon anledning inte vill använda standardflödena [Hyper-V](tutorial-migrate-hyper-v.md) eller [VMware.](server-migrate-overview.md)
- Virtuella datorer som körs i privata moln.
- Virtuella datorer som körs i offentliga moln som Amazon Web Services (AWS) eller Google Cloud Platform (GCP).


## <a name="migration-limitations"></a>Migreringsbegränsningar

Du kan välja upp till 10 datorer samtidigt för replikering. Om du vill migrera fler datorer replikeras du i grupper om 10.


## <a name="physical-server-requirements"></a>Krav för fysisk server

Tabellen sammanfattar stöd för fysiska servrar som du vill migrera med hjälp av agentbaserad migrering.

**Support** | **Detaljer**
--- | ---
**Arbetsbelastning för datorer** | Azure Migrate stöder migrering av alla arbetsbelastningar (t.ex.
**Operativsystem** | Den senaste informationen finns i [operativsystemets stöd](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines) för webbplatsåterställning. Azure Migrate ger stöd för identiska operativsystem.
**Linux-filsystem/gästlagring** | Den senaste informationen finns i [stöd för Linux-filsystemet](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) för Site Recovery. Azure Migrate ger identiska Linux-filsystem stöd.
**Nätverk/lagring** | Den senaste informationen finns i [nätverks-](../site-recovery/vmware-physical-azure-support-matrix.md#network) och [lagringsförutsättningarna](../site-recovery/vmware-physical-azure-support-matrix.md#storage) för webbplatsåterställning. Azure Migrate innehåller identiska nätverks-/lagringskrav.
**Krav för Azure** | Den senaste informationen finns i [Azure-nätverket,](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover) [lagrings-](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)och [beräkningskraven](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute) för site recovery. Azure Migrate har identiska krav för fysisk servermigrering.
**Mobilitetstjänst** | Mobilitetsserviceagenten måste installeras på varje dator som du vill migrera.
**UEFI-start** | Den migrerade datorn i Azure konverteras automatiskt till en BIOS-start azure VM. Endast server som kör Windows Server 2012 och senare stöds.<br/><br/> OS-disken ska ha upp till fyra partitioner och volymerna ska formateras med NTFS.
**Måldisk** | Datorer kan bara migreras till hanterade diskar (standard HDD, premium SSD) i Azure.
**Diskstorlek** | 2 TB OS-disk; 8 TB för datadiskar.
**Diskgränser** |  Upp till 63 diskar per dator.
**Krypterade diskar/volymer** |  Datorer med krypterade diskar/volymer stöds inte för migrering.
**Delat diskkluster** | Stöds inte.
**Oberoende diskar** | Stöds.
**Genomströmningsdiskar** | Stöds.
**NFS** | NFS-volymer monterade som volymer på datorerna replikeras inte.
**iSCSI-mål** | Datorer med iSCSI-mål stöds inte för agentlös migrering.
**Multipath IO** | Stöds inte.
**Lagring vMotion** | Stöds
**Nätverkskort i team** | Stöds inte.
**IPv6** | Stöds inte.



## <a name="replication-appliance-requirements"></a>Installationskrav för replikering

Om du ställer in replikeringsverktyget manuellt på en fysisk server kontrollerar du att den uppfyller de krav som sammanfattas i tabellen. När du konfigurerar Azure Migrate replication-enheten som en virtuell VMware-dator med hjälp av OVA-mallen som finns i Azure Migrate-hubben konfigureras installationen av Windows Server 2016 och uppfyller supportkraven. 

- Läs mer om [krav på replikeringsverktyget](migrate-replication-appliance.md#appliance-requirements).
- MySQL måste installeras på apparaten. Läs mer om [installationsalternativ](migrate-replication-appliance.md#mysql-installation).
- Läs mer om [webbadresser](migrate-replication-appliance.md#url-access) som replikeringsverktyget behöver komma åt.

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Alla lokala virtuella datorer som replikeras till Azure måste uppfylla azure vm-kraven som sammanfattas i den här tabellen. När Site Recovery kör en förutsättningskontroll för replikering misslyckas kontrollen om vissa av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Verifierar operativsystem som stöds.<br/> Du kan migrera alla arbetsbelastningar som körs på ett operativsystem som stöds. | Kontrollen misslyckas om den inte stöds.
Arkitektur för gästoperativsystem | 64-bitars. | Kontrollen misslyckas om den inte stöds.
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen misslyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen misslyckas om den inte stöds.
Antal datadiskar | 64 eller mindre. | Kontrollen misslyckas om den inte stöds.
Storlek på datadisk | Upp till 4 095 GB | Kontrollen misslyckas om den inte stöds.
Nätverkskort | Flera kort stöds. |
Delad VHD | Stöds inte. | Kontrollen misslyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen misslyckas om den inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator.
VM-namn | Från 1 till 63 tecken.<br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Maskinnamnet måste börja och sluta med en bokstav eller ett nummer. |  Uppdatera värdet i datoregenskaperna i Site Recovery.
Ansluta efter migrering-Windows | Så här ansluter du till virtuella Azure-datorer som kör Windows efter migreringen:<br/> - Innan migrering aktiverar RDP på den lokala virtuella datorn. Kontrollera att TCP- och UDP-regler har lagts till för den **offentliga** profilen och att RDP tillåts i **Windows-brandväggen** > **Tillåtna appar** för alla profiler.<br/> För VPN-åtkomst på plats till plats aktiverar du RDP och tillåter RDP i **Windows-brandväggen** -> **Tillåtna appar och funktioner** för **domännätverk och privata** nätverk. Kontrollera dessutom att operativsystemets SAN-princip är inställd på **OnlineAll**. [Läs mer](prepare-for-migration.md). |
Anslut efter migrering-Linux | Så här ansluter du till virtuella Azure-datorer efter migreringen med SSH:<br/> Kontrollera att tjänsten Secure Shell är inställd på Start på den lokala datorn före migreringen och att brandväggsregler tillåter en SSH-anslutning.<br/> Efter redundans, på den virtuella Azure-datorn, tillåt inkommande anslutningar till SSH-porten för nätverkssäkerhetsgruppsreglerna på den misslyckade över den virtuella datorn och för Det Azure-undernät som det är anslutet till. Lägg dessutom till en offentlig IP-adress för den virtuella datorn. |  


## <a name="next-steps"></a>Nästa steg

[Migrera](tutorial-migrate-physical-virtual-machines.md) fysiska servrar.
