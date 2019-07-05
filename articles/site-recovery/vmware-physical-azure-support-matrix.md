---
title: Stöd matrix för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Sammanfattar stödet för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: raynew
ms.openlocfilehash: 5dc98048099264942552862498b5137b4954c200
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491648"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Stöd matrix för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure

Den här artikeln sammanfattar komponenter som stöds och inställningar för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

- [Läs mer](vmware-azure-architecture.md) om virtuella VMware-datorer/fysiska servern disaster recovery-arkitekturen.
- Följ våra [självstudier](tutorial-prepare-azure.md) du prova att använda katastrofåterställning.

## <a name="deployment-scenarios"></a>Distributionsscenarier

**Scenario** | **Detaljer**
--- | ---
Haveriberedskap för virtuella VMware-datorer | Replikering av lokala virtuella VMware-datorer till Azure. Du kan distribuera det här scenariot i Azure portal eller med hjälp av [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Haveriberedskap för fysiska servrar | Replikering av lokala Windows-/ Linux fysiska servrar till Azure. Du kan distribuera det här scenariot i Azure-portalen.

## <a name="on-premises-virtualization-servers"></a>Lokala virtualiseringsservrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
vCenter Server | Version 6.7, 6.5, 6.0 eller 5.5 | Vi rekommenderar att du använder en vCenter-server i din Dr-distribution.
vSphere-värdar | Version 6.7, 6.5, 6.0 eller 5.5 | Vi rekommenderar att vSphere-värdar och vCenter-servrar finns i samma nätverk som processervern. Som standard körs processervern på konfigurationsservern. [Läs mer](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurationsserver

Konfigurationsservern är en lokal dator som kör Site Recovery-komponenter, inklusive konfigurationsservern, processervern och huvudmålservern.

- För virtuella VMware-datorer anger du konfigurationsservern genom att ladda ned en OVF-mall för att skapa ett VMware VM.
- För fysiska servrar ställa du in configuration server-dator manuellt.

**Komponent** | **Krav**
--- |---
Processorkärnor | 8
RAM | 16 GB
Antal diskar | 3 diskar<br/><br/> Diskar är OS-disk, processerverns cachedisk och kvarhållningsenhet för återställning efter fel.
Ledigt diskutrymme | 600 GB ledigt utrymme för processerverns cacheminne.
Ledigt diskutrymme | 600 GB ledigt utrymme för kvarhållningsenhetens.
Operativsystem  | Windows Server 2012 R2 eller Windows Server 2016 med Skrivbordsmiljö |
Nationella inställningar för operativsystem | Engelska (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Inte behövs för configuration server-version [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) eller senare. 
Windows Server-roller | Aktivera inte Active Directory Domain Services; Internet Information Services (IIS) eller Hyper-V. 
Grupprinciper| -Förhindra åtkomst till Kommandotolken. <br/> -Förhindra åtkomst till registerredigeringsverktygen. <br/> -Förtroende för bifogade filer. <br/> -Aktivera körning av skript. <br/> - [Lära sig mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Se till att du:<br/><br/> -Inte har en befintlig standardwebbplatsen <br/> -Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen  <br/> -Inte har redan befintliga webbplats/appen lyssnar på port 443<br/>
Typ av nätverkskort | VMXNET3 (när distribueras som en VMware VM)
IP-adresstyp | Statisk
Portar | 443 används för kontrolkanalsorchestration<br/>9443 för dataöverföring

## <a name="replicated-machines"></a>Replikerade datorer

Site Recovery har stöd för replikering av alla arbetsbelastningar som körs på en dator som stöds.

**Komponent** | **Detaljer**
--- | ---
Datorinställningar | Datorer som replikeras till Azure måste uppfylla [krav för Azure](#azure-vm-requirements).
Datorns arbetsbelastning | Site Recovery har stöd för replikering av alla arbetsbelastningar som körs på en dator som stöds. [Läs mer](https://aka.ms/asr_workload).
Windows | – Windows Server 2019 (stöds från [Update Rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 av mobilitetstjänsten) och senare.<br/> – Windows Server 2016 (64-bitars Server Core, Server med Skrivbordsmiljö)<br/> - Windows Server 2012 R2, Windows Server 2012<br/> – Windows Server 2008 R2 med på minst SP1.<br/> -Windows Server 2008, 64- och 32-bitars med minst SP2]. Stöd för migrering endast. [Läs mer](migrate-tutorial-windows-server-2008.md).<br/> -Windows 10, Windows 8.1, Windows 8, Windows 7 64-bitars (stöds från [Update Rollup 36](https://support.microsoft.com/help/4503156) (version 9.22 av mobilitetstjänsten och senare). Windows 7 RTM stöds inte. 
Linux | Endast 64-bitars system stöds. 32-bitars systemfiler stöds inte.<br/><br/>Alla Linux-servrar bör ha [Linux Integration Services (LIS) komponenter](https://www.microsoft.com/download/details.aspx?id=55106) installerad. Det krävs för att starta servern i Azure efter redundans/redundanstest. Om LIS komponenter saknas, kontrollerar du om du vill installera den [komponenter](https://www.microsoft.com/download/details.aspx?id=55106) innan du aktiverar replikering för virtuella datorer att starta i Azure. <br/><br/> Site Recovery dirigerar redundans för att köra Linux-servrar i Azure. Linux-leverantörer kan dock begränsa stöd för att endast distribution-versioner som inte har nått slutet på sin livscykel.<br/><br/> På Linux-distributioner stöds endast lagerartiklar kernlar som är en del av distributionen delversion versionen/uppdatera.<br/><br/> Uppgradera skyddade datorer i större Linux distribution versioner inte stöds. Om du vill uppgradera, inaktiverar du replikering, uppgradera operativsystemet och aktiverar sedan replikeringen igen.<br/><br/> [Läs mer](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) om stöd för Linux och öppen källkod-teknik i Azure.
Linux Red Hat Enterprise | 5.2 5.11</b><br/> 6.1 6.10</b><br/> 7.0 7.6<br/> <br/> Servrar som kör Red Hat Enterprise Linux 5.2 5.11 & 6.1 6.10 har inte [Linux Integration Services (LIS) komponenter](https://www.microsoft.com/download/details.aspx?id=55106) förinstallerat. Se till att installera den [komponenter](https://www.microsoft.com/download/details.aspx?id=55106) innan du aktiverar replikering för virtuella datorer att starta i Azure.
Linux: CentOS | 5.2 5.11</b><br/> 6.1 6.10</b><br/> 7.0 7.6<br/> <br/> Servrar som kör CentOS 5.2 5.11 & 6.1 6.10 har inte [Linux Integration Services (LIS) komponenter](https://www.microsoft.com/download/details.aspx?id=55106) förinstallerat. Se till att installera den [komponenter](https://www.microsoft.com/download/details.aspx?id=55106) innan du aktiverar replikering för virtuella datorer att starta i Azure.
Ubuntu | Ubuntu 14.04 LTS server [(granska stöd kernel-versioner)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(granska stöd kernel-versioner)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(granska stöd kernel-versioner)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(granska stöd kernel-versioner)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Uppgradera replikerade datorer från SUSE Linux Enterprise Server 11 SP3 till SP4 stöds inte. Om du vill uppgradera, inaktiverar du replikering och återaktivera efter uppgraderingen.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Kör Red Hat kompatibla kernel eller Unbreakable Enterprise Kernel version 3, 4 och 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel versions


**Versionen som stöds** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9.24][9.25 UR]  | 3.13.0-24-Generic till 3.13.0-169-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-146-generic,<br/>4.15.0-1023-Azure till 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-Generic till 3.13.0-167-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-143-generic,<br/>4.15.0-1023-Azure till 4.15.0-1040-azure |
14.04 LTS | [9.23][9.23 UR] | 3.13.0-24-Generic till 3.13.0-165-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-142-generic,<br/>4.15.0-1023-Azure till 4.15.0-1037-azure |
14.04 LTS | [9.22][9.22 UR] | 3.13.0-24-Generic till 3.13.0-164-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.15.0-1023-Azure till 4.15.0-1036-azure |
|||
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-Generic till 4.4.0-146-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-48-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-Generic till 4.4.0-143-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-46-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1040-azure|
16.04 LTS | [9.23][9.23 UR] | 4.4.0-21-Generic till 4.4.0-142-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-45-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1037-azure|
16.04 LTS | [9.22][9.22 UR] | 4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-43-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1036-azure|

### <a name="debian-kernel-versions"></a>Debian kernel-versioner


**Versionen som stöds** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR]| 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 till 3.16.0-8-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR] | 3.16.0-4-amd64 till 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 kernel-versioner som stöds

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default till 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure till 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure till 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default till 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | [9.23][9.23 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default till 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3) | [9.22][9.22 UR] | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default till 4.4.162-94.72-default |


## <a name="linux-file-systemsguest-storage"></a>Linux-system/gäst fillagring

**Komponent** | **Stöds**
--- | ---
Filsystem | ext3, ext4, XFS
Volymhanterare | -LVM stöds.<br/> - / Start på LVM stöds från [Update Rollup 31](https://support.microsoft.com/help/4478871/) (version 9.20 mobilitetstjänsten) och senare. Det stöds inte i tidigare versioner av Mobility service.<br/> – Flera OS-diskar stöds inte.
Paravirtualiserade lagringsenheter | Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.
Flera kö blockera-i/o-enheter | Stöds ej.
Fysiska servrar med lagringsstyrenhet HP CCISS | Stöds ej.
Namngivningskonvention för enheten/montera punkt | Enhetens namn eller monteringspunktsnamn måste vara unika.<br/> Kontrollera att inga två enheter/monteringspunkter har skiftlägeskänsliga namn. Till exempel namngivning av enheter för samma virtuella dator som *device1* och *Device1* stöds inte.
Kataloger | Om du kör en version av mobilitetstjänsten tidigare än version 9.20 (introducerades i [Update Rollup 31](https://support.microsoft.com/help/4478871/)), och sedan dessa begränsningar gäller:<br/><br/> – Dessa kataloger (om konfigurera som separata partitioner-filsystem) måste finnas på samma OS-disken på källservern: / (rot), / Boot, usr, / usr/local, /var, / etc.</br> -Katalogen/Boot bör finnas på en diskpartition och inte en LVM-volym.<br/><br/> Från version 9.20 och senare gäller inte dessa begränsningar. 
Startkatalog | – Flera startdiskar stöds på en virtuell dator <br/> - / Start på en LVM-volym över flera diskar stöds inte.<br/> -En dator utan en startdisk kan inte replikeras.
Krav för ledigt utrymme| 2 GB på/Root-partition <br/><br/> 250 MB på installationsmappen
XFSv5 | XFSv5 funktioner på XFS filsystem, till exempel metadata kontrollsumma är stöds (mobilitetstjänstversionen 9.10 och senare).<br/> Använda verktyget xfs_info för att kontrollera XFS superblock för partitionen. Om `ftype` har angetts till 1, och sedan XFSv5 funktionerna är i användning.
BTRFS | BTRFS stöds från [Update Rollup 34](https://support.microsoft.com/help/4490016) (version 9.22 av mobilitetstjänsten) och senare. BTRFS stöds inte om:<br/><br/> -BTRFS file system subvolume ändras när du har aktiverat skydd.</br> -BTRFS filsystemet är utspridd över flera diskar.</br> -BTRFS filsystemet har stöd för RAID.

## <a name="vmdisk-management"></a>VM/Diskhantering

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på den replikerade virtuella datorn | Stöds.
Lägg till disk på den replikerade virtuella datorn | Stöds ej.<br/> Inaktivera replikering för den virtuella datorn, Lägg till disk och aktivera sedan replikering igen.

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
Tjänstslutpunkter för virtuellt nätverk i Azure<br/> | Ja
Snabbare nätverk | Nej

## <a name="storage"></a>Storage
**Komponent** | **Stöds**
--- | ---
Dynamisk disk | OS-disken måste vara en standarddisk. <br/><br/>Datadiskar kan vara dynamiska diskar
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
Gäst/server iSCSI | Nej
Gäst/server SMB 3.0 | Nej
Gäst/server RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar
Gäst/server disk > 1 TB | Ja<br/><br/>Upp till 4095 GB<br/><br/> Disken måste vara större än 1 024 MB.
Gäst/server disk med 4K logisk och 4 k fysisk sektorstorlek | Nej
Gäst/server-disk med 4K logisk och fysisk sektorstorlek för 512 byte | Nej
Gäst/server-volym med stripe-disk > 4 TB <br/><br/>Hantering av logisk volym (LVM)| Ja
Gäst/server - lagringsutrymmen | Nej
Gäst/server frekvent Lägg till/ta bort disk | Nej
Gäst/server - Uteslut disk | Ja
Gäst/server multipath (MPIO) | Nej
Gäst/server GPT-partitioner | Fem partitioner som stöds från [Update Rollup 37](https://support.microsoft.com/help/4508614/) (version 9,25 av mobilitetstjänsten) och senare. Tidigare stöddes fyra.
Gäst/server EFI/UEFI-Startmetod | -Stöd när du kör mobilitetstjänstversionen 9.13 eller senare.<br/> -Stöd när du migrerar virtuella VMware-datorer eller fysiska servrar som kör Windows Server 2012 eller senare till Azure.<br/> -Du kan bara replikera datorer för migrering. Växla tillbaka till den lokala stöds inte.<br/> – Endast NTFS stöds & säkra UEFI boot stöds inte. <br/> -Sektor diskstorleken ska vara 512 byte per fysisk sektor.

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
Kryptering i vila (SSE)| Ja
Premium Storage | Ja
Import/export-tjänsten | Nej
Azure Storage-brandväggar för virtuella nätverk | Ja.<br/> Konfigurerad på mållagringskontot för lagring/cache (används för att lagra data för replikering).
Generell användning v2-konton (frekventa och lågfrekventa nivåer) | Ja (transaktion kostnader är betydligt högre för V2 jämfört med V1)

## <a name="azure-compute"></a>Azure-beräkning

**Funktion** | **Stöds**
--- | ---
Tillgänglighetsuppsättningar | Ja
Tillgänglighetszoner | Nej
HUB | Ja
Hanterade diskar | Ja

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Lokala virtuella datorer som replikeras till Azure måste uppfylla kraven för virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en kravkontroll för replikering, misslyckas kontrollen om vissa av kraven inte uppfylls.

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

## <a name="churn-limits"></a>Omsättningen gränser

Följande tabell innehåller gränserna för Azure Site Recovery. 
- Dessa gränser är baserade på våra tester, men omfattar inte alla möjliga app i/o-kombinationer.
- De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program.
- För bästa resultat rekommenderar vi att du kör den [planeringsverktyget](site-recovery-deployment-planner.md), och köra omfattande programtester med redundanstestningen att hämta en bild av verklig prestanda för din app.

**Replikeringsmål** | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
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

- Det här är genomsnittliga värden baserade på en I/O-överlappning på 30 procent.
- Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen.
- Dessa antal antar en typisk eftersläpning på cirka fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.

## <a name="vault-tasks"></a>Valvet uppgifter

**Åtgärd** | **Stöds**
--- | ---
Flytta valv mellan resursgrupper | Nej
Flytta valv inom och mellan prenumerationer | Nej
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper | Nej
Flytta lagring, nätverk, virtuella Azure-datorer inom och mellan olika prenumerationer. | Nej


## <a name="obtain-latest-components"></a>Hämta senaste komponenter

**Name** | **Beskrivning** | **Detaljer**
--- | --- | ---
Konfigurationsservern | Installerad lokalt.<br/> Samordnar kommunikationen mellan lokala VMware-servrar eller fysiska datorer och Azure. | - [Lär dig mer om](vmware-physical-azure-config-process-server-overview.md) konfigurationsservern.<br/> - [Lär dig mer om](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) uppgradera till den senaste versionen.<br/> - [Lär dig mer om](vmware-azure-deploy-configuration-server.md) konfigurerar konfigurationsservern. 
Processervern | Installeras som standard på konfigurationsservern.<br/> Tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar det till Azure.<br/> Allt eftersom distributionen växer kan du lägga till ytterligare processervrar för att hantera större mängder replikeringstrafik. | - [Lär dig mer om](vmware-physical-azure-config-process-server-overview.md) processervern.<br/> - [Lär dig mer om](vmware-azure-manage-process-server.md#upgrade-a-process-server) uppgradera till den senaste versionen.<br/> - [Lär dig mer om](vmware-physical-large-deployment.md#set-up-a-process-server) konfigurerar skala ut processervrar.
Mobilitetstjänsten | Installerad på VMware VM eller fysiska servrar som du vill replikera.<br/> Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure.| - [Lär dig mer om](vmware-physical-mobility-service-overview.md) mobilitetstjänsten.<br/> - [Lär dig mer om](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) uppgradera till den senaste versionen.<br/> 



## <a name="next-steps"></a>Nästa steg
[Lär dig hur](tutorial-prepare-azure.md) du förbereder Azure för haveriberedskap för virtuella VMware-datorer.

[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
