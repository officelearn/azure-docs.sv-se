---
title: Stöd matrix för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Sammanfattning av de operativsystem som stöds och haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery-komponenter.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: raynew
ms.openlocfilehash: dc455b5256f9c04e1e0af2c1ff3fea04af54d90b
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149466"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Stöd matrix för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure

Den här artikeln sammanfattar komponenter som stöds och inställningar för haveriberedskap för virtuella VMware-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Om du vill börja använda Azure Site Recovery med det enklaste distributionsscenariot som genom att gå till vår [självstudier](tutorial-prepare-azure.md). Du kan läsa mer om Azure Site Recovery-arkitekturen [här](vmware-azure-architecture.md).

## <a name="deployment-scenario"></a>Scenario för distribution

**Scenario** | **Detaljer**
--- | ---
Haveriberedskap för virtuella VMware-datorer | Replikering av lokala virtuella VMware-datorer till Azure. Du kan distribuera det här scenariot i Azure portal eller med hjälp av [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Haveriberedskap för fysiska servrar | Replikering av lokala Windows-/ Linux fysiska servrar till Azure. Du kan distribuera det här scenariot i Azure-portalen.

## <a name="on-premises-virtualization-servers"></a>Lokala virtualiseringsservrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
VMware | vCenter Server 6.7 6.5, 6.0 eller 5.5 eller vSphere 6.7, 6.5, 6.0 eller 5.5 | Vi rekommenderar att du använder en vCenter-server.<br/><br/> Vi rekommenderar att vSphere-värdar och vCenter-servrar finns i samma nätverk som processervern. Som standard process server-komponenter som körs på konfigurationsservern, så blir det nätverket där du ställer in konfigurationsservern, om inte du ställa in en dedikerad processerver.
Fysiska | Gäller inte

## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurationsserver

Konfigurationsservern är en lokal dator som kör Site Recovery-komponenter, inklusive konfigurationsservern, processervern och huvudmålservern. För VMware-replikering konfigurera du konfigurationsservern med alla krav, med hjälp av en OVF-mall för att skapa en VMware-VM. För fysisk serverreplikering konfigurerar du configuration server-dator manuellt.

**Komponent** | **Krav**
--- |---
Processorkärnor | 8
RAM | 16 GB
Antal diskar | 3 diskar<br/><br/> Diskar är OS-disk, processerverns cachedisk och kvarhållningsenhet för återställning efter fel.
Ledigt diskutrymme | 600 GB utrymme som krävs för processerverns cacheminne.
Ledigt diskutrymme | 600 GB utrymme som krävs för kvarhållningsenhet.
Operativsystem  | Windows Server 2012 R2 eller Windows Server 2016 |
Nationella inställningar för operativsystem | Engelska (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") krävs inte för konfigurationsservern med versioner från [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery).
Windows Server-roller | Aktivera inte: <br/> - Active Directory Domain Services <br/>- Internet Information Services <br/> - Hyper-V |
Grupprinciper| Aktivera inte: <br/> -Förhindra åtkomst till Kommandotolken. <br/> -Förhindra åtkomst till registerredigeringsverktygen. <br/> -Förtroende för bifogade filer. <br/> -Aktivera körning av skript. <br/> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Se till att du:<br/><br/> -Inte har en befintlig standardwebbplatsen <br/> -Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen  <br/> -Inte har redan befintliga webbplats/appen lyssnar på port 443<br/>
Typ av nätverkskort | VMXNET3 (när distribueras som en VMware VM)
IP-adresstyp | Statisk
Portar | 443 används för kontrolkanalsorchestration)<br/>9443 som används för dataöverföring

## <a name="replicated-machines"></a>Replikerade datorer

Site Recovery har stöd för replikering av alla arbetsbelastningar som körs på en dator som stöds.

**Komponent** | **Detaljer**
--- | ---
Datorinställningar | Datorer som replikeras till Azure måste uppfylla [krav för Azure](#azure-vm-requirements).
Datorns arbetsbelastning | Site Recovery har stöd för replikering av alla arbetsbelastningar (exempelvis Active Directory, SQLServer osv.) som körs på en dator som stöds. [Läs mer](https://aka.ms/asr_workload).
Windows-operativsystem | 64-bitars Windows Server 2016 (Server Core, Server med Skrivbordsmiljö), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1. </br></br>  [Windows Server 2008 med på minst SP2 - 32-bitars och 64-bitars](migrate-tutorial-windows-server-2008.md) (migrering). </br></br> Windows 2016 Nano Server stöds inte.
Arkitektur för Linux-operativsystem | Endast 64-bitars system stöds. 32-bitars systemfiler stöds inte
Linux-operativsystem | Red Hat Enterprise Linux: 5.2 5.11<b>\*\*</b>, 6.1 6.10<b>\*\*</b>, 7.0 7.6 <br/><br/>CentOS: 5.2 5.11<b>\*\*</b>, 6.1 6.10<b>\*\*</b>, 7.0 7.6 <br/><br/>Ubuntu 14.04 LTS server [(kernel-versioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(kernel-versioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8 [(kernel-versioner som stöds)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [(kernel-versioner som stöds)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, 6.7, 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 som kör Red Hat kompatibla kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/></br>– Uppgradera replikerade datorer från SUSE Linux Enterprise Server 11 SP3 till SP4 stöds inte. Om du vill uppgradera, inaktiverar du replikering och aktivera det igen efter uppgraderingen.</br></br> - [Läs mer](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) om stöd för Linux och teknik med öppen källkod i Azure. Site Recovery dirigerar redundans för att köra Linux-servrar i Azure. Linux-leverantörer kan dock begränsa stöd för att endast distribution-versioner som inte har nått slutet på sin livscykel.<br/><br/> -På Linux-distributioner stöds endast lagerartiklar kernlar som är en del av distributionen delversion versionen/uppdatera.<br/><br/> – Uppgradera skyddade datorer i större Linux distribution versioner inte stöds. Om du vill uppgradera, inaktiverar du replikering, uppgradera operativsystemet och aktiverar sedan replikeringen igen.<br/><br/> -Servrar som kör Red Hat Enterprise Linux 5.2 5.11 eller CentOS 5.2 5.11 ska ha den [Linux Integration Services (LIS) komponenter](https://www.microsoft.com/download/details.aspx?id=55106) installerat att starta i Azure virtuella datorer.


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel versions


**Versionen som stöds** | **Azure Site Recovery Mobility Service version** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9,24] [9,24 UR] | 3.13.0-24-Generic till 3.13.0-167-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-143-generic,<br/>4.15.0-1023-Azure till 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic till 3.13.0-165-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-142-generic,<br/>4.15.0-1023-Azure till 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-Generic till 3.13.0-164-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.15.0-1023-Azure till 4.15.0-1036-azure |
14.04 LTS | [9.21][9.21 UR] | 3.13.0-24-Generic till 3.13.0-163-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.15.0-1023-Azure till 4.15.0-1035-azure |
|||
16.04 LTS | [9.23] [9,24 UR] | 4.4.0-21-Generic till 4.4.0-143-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-46-generic<br/>4.11.0-1009-Azure till 4.11.0-1018-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic till 4.4.0-142-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-45-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-43-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1036-azure|
16.04 LTS | [9.21][9.21 UR] | 4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-42-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1035-azure|
16.04 LTS | [9.20][9.20 UR] | 4.4.0-21-Generic till 4.4.0-138-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-38-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1025-azure|

### <a name="debian-kernel-versions"></a>Debian kernel-versioner


**Versionen som stöds** | **Azure Site Recovery Mobility Service version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9,24] [9.24 UR]| 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.21][9.21 UR],[9.22][9.22 UR],[9.23][9.23 UR], [9,24] [9.24 UR] | 3.16.0-4-amd64 till 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 kernel-versioner som stöds

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9,24] [9,24 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default till 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default till 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default till 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.21][9.21 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default till 4.4.156-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Linux-system/gäst fillagring

**Komponent** | **Stöds**
--- | ---
Filsystem | ext3, ext4, XFS
Volymhanterare | Innan du [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. LVM stöds. <br/> 2. / Boot på LVM-volym stöds inte. <br/> 3. Flera operativsystemdiskar stöds inte.<br/><br/>Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) och senare kan/Boot på LVM stöds. Flera operativsystemdiskar stöds inte.
Paravirtualiserade lagringsenheter | Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.
Flera kö blockera-i/o-enheter | Stöds ej.
Fysiska servrar med lagringsstyrenhet HP CCISS | Stöds ej.
Namngivningskonvention för enheten/montera punkt | Enhetens namn eller monteringspunktsnamn måste vara unika. Kontrollera att inga två enheter/monteringspunkter har skiftlägeskänsliga namn. </br> Exempel: Namnge två enheter av samma virtuella dator som *device1* och *Device1* tillåts inte.
Kataloger | Innan du [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), <br/> 1. Följande kataloger (om konfigurerad som separata partitioner /-filsystem) alla måste finnas i samma OS-disken på källservern: / (rot), / Boot, usr, / usr/local, /var, / etc.</br>2. / Boot bör finnas på en diskpartition och inte en LVM-volym.<br/><br/> Från [9.20 version](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) och senare, ovan begränsningar kan inte användas. / Boot på LVM volym över flera diskar stöds inte.
Startkatalog | Flera startdiskar på en virtuell dator stöds inte <br/><br/> Det går inte att skydda en dator utan startdisk

Kostnadsfri kraven på diskutrymme | 2 GB på/Root-partition <br/><br/> 250 MB på installationsmappen XFSv5 | XFSv5 funktioner på XFS filsystem, till exempel metadata kontrollsumma stöds från Mobilitetstjänsten version 9.10 och uppåt. Använda verktyget xfs_info för att kontrollera XFS superblock för partitionen. Om `ftype` har angetts till 1, och sedan XFSv5 funktionerna är i användning.

## <a name="vmdisk-management"></a>VM/Diskhantering

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på den replikerade virtuella datorn | Stöds.
Lägg till disk på den replikerade virtuella datorn | Inaktivera replikering för den virtuella datorn, lägga till disken och sedan återaktivera replikering. Lägger till en disk på en replikerande virtuella datorn stöds inte för närvarande.

## <a name="network"></a>Nätverk

**Komponent** | **Stöds**
--- | ---
Värdnätverket NIC-Teamindelning | Stöd för virtuella VMware-datorer. <br/><br/>Stöds inte för replikeringen av den fysiska datorn.
Värdnätverket VLAN | Ja.
Värdnätverket IPv4 | Ja.
Värdnätverket IPv6 | Nej.
Gäst/servernätverk NIC-Teamindelning | Nej.
Gäst/servernätverk IPv4 | Ja.
Gäst/servernätverk IPv6 | Nej.
Gäst/server network statisk IP-adress (Windows) | Ja.
Gäst/server network statisk IP-adress (Linux) | Ja. <br/><br/>Virtuella datorer är konfigurerade för att använda DHCP på återställning efter fel.
Gäst/server network flera nätverkskort | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure VM-nätverk (efter redundans)

**Komponent** | **Stöds**
--- | ---
Azure ExpressRoute | Ja
ILB | Ja
ELB | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Reserverad IP-adress | Ja
IPv4 | Ja
Behåll källans IP-adress | Ja
Azure Virtual Network-tjänstslutpunkter<br/> | Ja
Accelererat nätverk | Nej

## <a name="storage"></a>Storage
**Komponent** | **Stöds**
--- | ---
Dynamisk disk | Åtgärden systemdisken måste vara en standarddisk. <br/><br/>Datadiskar kan vara dynamiska diskar
Diskkonfiguration för docker | Nej
Host NFS | Ja för VMware<br/><br/> Nej för fysiska servrar
SAN-nätverk (iSCSI/FC) värd | Ja
Host vSAN | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Värden multipath (MPIO) | Ja, testas med Microsoft DSM, EMC PowerPath 5.7 SP4 EMC PowerPath DSM för CLARiiON
Virtuella Värdvolymer (VVols) | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Guest/server VMDK | Ja
Gäst/server delad klusterdisk | Nej
Gäst/server krypterad disk | Nej
Gäst/server NFS | Nej
Gäst/server SMB 3.0 | Nej
Gäst/server RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar
Gäst/server disk > 1 TB | Ja<br/><br/>Upp till 4095 GB<br/><br/> Disken måste vara större än 1 024 MB.
Gäst/server disk med 4K logisk och 4 k fysisk sektorstorlek | Ja
Gäst/server-disk med 4K logisk och fysisk sektorstorlek för 512 byte | Ja
Gäst/server-volym med stripe-disk > 4 TB <br/><br/>Hantering av logisk volym (LVM)| Ja
Gäst/server - lagringsutrymmen | Nej
Gäst/server frekvent Lägg till/ta bort disk | Nej
Gäst/server - Uteslut disk | Ja
Gäst/server multipath (MPIO) | Nej
Gäst/server EFI/UEFI-Startmetod | Stöd när du migrerar virtuella VMware-datorer eller fysiska servrar som kör Windows Server 2012 eller senare till Azure.<br/><br/> Du kan bara replikera datorer för migrering. Växla tillbaka till den lokala stöds inte.<br/><br/> Servern bör inte ha fler än fyra partitioner på operativsystemdisken.<br/><br/> Kräver Mobilitetstjänsten version 9.13 eller senare.<br/><br/> Endast NTFS stöds.

## <a name="replication-channels"></a>Replikering kanaler

|**Typ av replikering**   |**Stöds**  |
|---------|---------|
|Avlastade dataöverföringar (ODX)    |       Nej  |
|Seeding offline        |   Nej      |
| Azure Data Box | Nej


## <a name="azure-storage"></a>Azure-lagring

**Komponent** | **Stöds**
--- | ---
Lokalt redundant lagring | Ja
Geografiskt redundant lagring | Ja
Läsåtkomst till geografiskt redundant lagring | Ja
Lågfrekvent lagring | Nej
Frekvent lagring| Nej
Blockblob-objekt | Nej
Kryptering i vila (Storage Service Encryption)| Ja
Premium Storage | Ja
Import/export-tjänsten | Nej
Azure Storage-brandväggar för virtuella nätverk som konfigurerats på mållagringskontot för lagring/cache (används för att lagra data för replikering) | Ja
Storage-konton för generell användning v2 (frekvent och lågfrekvent nivå) | Nej

## <a name="azure-compute"></a>Azure-beräkning

**Funktion** | **Stöds**
--- | ---
Tillgänglighetsuppsättningar | Ja
Tillgänglighetszoner | Nej
HUB | Ja
Hanterade diskar | Ja

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery körs en kravkontroll misslyckas om vissa av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Kontrollera [operativsystem som stöds](#replicated-machines) för replikerade datorer. | Det går inte att kontrollera om stöds inte.
Gästen operativsystemets arkitektur | 64-bitars. | Det går inte att kontrollera om stöds inte.
Storleken på operativsystemdisken | Upp till 2 048 GB. | Det går inte att kontrollera om stöds inte.
Antal operativsystemdiskar | 1 | Det går inte att kontrollera om stöds inte.
Datadiskar | 64 eller mindre. | Det går inte att kontrollera om stöds inte.
Datadiskstorleken | Upp till 4095 GB | Det går inte att kontrollera om stöds inte.
Nätverkskort | Flera nätverkskort stöds. |
Delad VHD | Stöds ej. | Det går inte att kontrollera om stöds inte.
FC-disk | Stöds ej. | Det går inte att kontrollera om stöds inte.
BitLocker | Stöds ej. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator. |
VM-namn | Mellan 1 och 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Namnet på datorn måste börja och sluta med en bokstav eller siffra. |  Uppdatera värdet i egenskaperna för datorn i Site Recovery.

## <a name="azure-site-recovery-churn-limits"></a>Gränserna för Azure Site Recovery-omsättning

Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser är baserade på våra tester, men de täcker inte alla möjliga kombinationer av program-I/O. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. För bästa resultat rekommenderar vi starkt att [kör verktyget Distributionshanteraren](site-recovery-deployment-planner.md) och köra omfattande programtester med redundanstest med att få en bild av verklig prestanda för programmet.

**Replication Storage Target** (Lagringsmål för replikering) | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

**Källans dataomsättning** | **Övre gräns**
---|---
Genomsnittlig dataomsättning per virtuell dator| 25 MB/s
Högsta dataomsättning av alla diskar på en virtuella dator | 54 MB/s
Maximal dataomsättning per dag som stöds av en processerver | 2 TB

Det här är genomsnittliga värden baserade på en I/O-överlappning på 30 procent. Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen. Föregående antal antar en typisk eftersläpning på cirka fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.

## <a name="vault-tasks"></a>Valvet uppgifter

**Åtgärd** | **Stöds**
--- | ---
Flytta valv mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Nej
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Nej


## <a name="download-latest-azure-site-recovery-components"></a>Hämta senaste Azure Site Recovery-komponenter

**Namn** | **Beskrivning** | **Senaste version Hämtningsinstruktioner**
--- | --- | ---
Konfigurationsserver | Samordnar kommunikationen mellan lokala VMware-servrar och Azure <br/><br/> Installerad på den lokala VMware-servrar | Mer information finns i vår vägledning på [nyinstallation](vmware-azure-deploy-configuration-server.md) och [uppgradering av befintlig komponent till senaste versionen](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Processerver|Installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Allt eftersom distributionen växer kan du lägga till ytterligare, separat processervrar för att hantera större mängder replikeringstrafik.| Mer information finns i vår vägledning på [nyinstallation](vmware-azure-set-up-process-server-scale.md) och [uppgradering av befintlig komponent till senaste versionen](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Mobilitetstjänsten | Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure/sekundär plats<br/><br/> Installerad på VMware VM eller fysiska servrar som du vill replikera | Mer information finns i vår vägledning på [nyinstallation](vmware-azure-install-mobility-service.md) och [uppgradering av befintlig komponent till senaste versionen](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal).

Mer information om de senaste funktionerna finns [senaste viktig](https://aka.ms/ASR_latest_release_notes).


## <a name="next-steps"></a>Nästa steg
[Lär dig hur](tutorial-prepare-azure.md) du förbereder Azure för haveriberedskap för virtuella VMware-datorer.

[9.23 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
