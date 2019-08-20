---
title: Stöd mat ris för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Sammanfattar stöd för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: raynew
ms.openlocfilehash: fd24d0d9f05855cf22da547f95b16da0a8d2c788
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617653"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Stöd mat ris för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure

I den här artikeln sammanfattas komponenter och inställningar som stöds för haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

- [Lär dig mer](vmware-azure-architecture.md) om haveri beredskap för virtuella VMware-datorer/fysiska servrar.
- Följ våra [självstudier](tutorial-prepare-azure.md) för att testa haveri beredskap.

## <a name="deployment-scenarios"></a>Distributionsscenarier

**Scenario** | **Detaljer**
--- | ---
Haveri beredskap för virtuella VMware-datorer | Replikering av lokala virtuella VMware-datorer till Azure. Du kan distribuera det här scenariot i Azure Portal eller med hjälp av [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Haveri beredskap för fysiska servrar | Replikering av lokala Windows/Linux fysiska servrar till Azure. Du kan distribuera det här scenariot i Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Lokala virtualiseringslösningar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
vCenter Server | Version 6,7, 6,5, 6,0 eller 5,5 | Vi rekommenderar att du använder en vCenter-Server i återställnings distributionen för haveri beredskap.
vSphere-värdar | Version 6,7, 6,5, 6,0 eller 5,5 | Vi rekommenderar att vSphere-värdar och vCenter-servrar finns i samma nätverk som processervern. Som standard körs processervern på konfigurations servern. [Läs mer](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurations Server

Konfigurations servern är en lokal dator som kör Site Recovery-komponenter, inklusive konfigurations servern, processervern och huvud mål servern.

- För virtuella VMware-datorer ställer du in konfigurations servern genom att hämta en OVF-mall för att skapa en virtuell VMware-dator.
- För fysiska servrar konfigurerar du Configuration Server-datorn manuellt.

**Komponent** | **Krav**
--- |---
Processorkärnor | 8
RAM | 16 GB
Antal diskar | 3 diskar<br/><br/> Diskarna innehåller OS-disken, cache-disken för processervern och lagrings enheten för återställning efter fel.
Ledigt disk utrymme | 600 GB utrymme för processervern.
Ledigt disk utrymme | 600 GB utrymme för lagrings enheten.
Operativsystem  | Windows Server 2012 R2 eller Windows Server 2016 med Skriv bords miljö |
Nationella inställningar för operativsystem | Engelska (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Krävs inte för konfigurations Server version [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) eller senare. 
Windows Server-roller | Aktivera inte Active Directory Domain Services; Internet Information Services (IIS) eller Hyper-V. 
Grup principer| -Förhindra åtkomst till kommando tolken. <br/> -Förhindra åtkomst till verktyg för redigering av registret. <br/> – Förtroende logik för bifogade filer. <br/> – Aktivera skript körning. <br/> - [Lära sig mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Kontrol lera att du:<br/><br/> -Har ingen befintlig standard webbplats <br/> -Aktivera [Anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -inställning  <br/> -Har inte redan befintlig webbplats/app-lyssning på port 443<br/>
Typ av nätverkskort | VMXNET3 (när den distribueras som en virtuell VMware-dator)
IP-adresstyp | Statisk
Portar | 443 som används för kontroll av kanal dirigering<br/>9443 för data transport

## <a name="replicated-machines"></a>Replikerade datorer

Site Recovery stöder replikering av alla arbets belastningar som körs på en dator som stöds.

**Komponent** | **Detaljer**
--- | ---
Dator inställningar | Datorer som replikeras till Azure måste uppfylla [Azure-kraven](#azure-vm-requirements).
Dator arbets belastning | Site Recovery stöder replikering av alla arbets belastningar som körs på en dator som stöds. [Läs mer](https://aka.ms/asr_workload).
Windows | – Windows Server 2019 (stöds från samlad [uppdatering 34](https://support.microsoft.com/help/4490016) (version 9,22 av mobilitets tjänsten) och senare.<br/> – Windows Server 2016 (64-bitars Server Core, server med Skriv bords miljö)<br/> – Windows Server 2012 R2, Windows Server 2012<br/> – Windows Server 2008 R2 med minst SP1.<br/> – Windows Server 2008, 64 och 32-bitars med minst SP2]. Stöds endast för migrering. [Läs mer](migrate-tutorial-windows-server-2008.md).<br/> -Windows 10, Windows 8,1, Windows 8, Windows 7 64-bitars (stöds från samlad [uppdatering 36](https://support.microsoft.com/help/4503156) (version 9,22 av mobilitets tjänsten och senare). Windows 7 RTM stöds inte. 
Linux | Endast 64-bitars system stöds. 32-bitars system stöds inte.<br/><br/>Alla Linux-servrar måste ha en installation av [Linux Integration Services (LIS)-komponenter](https://www.microsoft.com/download/details.aspx?id=55106) . Det krävs för att starta-servern i Azure efter redundanstestning/redundans. Om LIS-komponenter saknas, se till att installera [komponenterna](https://www.microsoft.com/download/details.aspx?id=55106) innan du aktiverar replikering för de datorer som ska startas i Azure. <br/><br/> Site Recovery dirigerar redundans till att köra Linux-servrar i Azure. Linux-leverantörer kan dock begränsa stödet till endast distributions versioner som inte har uppnått livs längd.<br/><br/> I Linux-distributioner stöds bara de lager kärnor som ingår i distributionens lägre versions version/uppdatering.<br/><br/> Det finns inte stöd för att uppgradera skyddade datorer över stora versioner av Linux-distribution. För att uppgradera, inaktivera replikering, uppgradera operativ systemet och aktivera sedan replikering igen.<br/><br/> [Läs mer](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) om stöd för Linux och teknik med öppen källkod i Azure.
Linux Red Hat Enterprise | 5,2 till 5,11</b><br/> 6,1 till 6,10</b><br/> 7,0 till 7,6<br/> <br/> Servrar som kör Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 har inte välinstallerade [komponenterna för Linux integrations tjänster (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Se till att installera [komponenterna](https://www.microsoft.com/download/details.aspx?id=55106) innan du aktiverar replikering för datorerna för start i Azure.
Linux: CentOS | 5,2 till 5,11</b><br/> 6,1 till 6,10</b><br/> 7,0 till 7,6<br/> <br/> Servrar som kör CentOS 5.2 – 5.11 & 6.1 – 6.10 har inte välinstallerade [komponenter för Linux Integration Services (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) . Se till att installera [komponenterna](https://www.microsoft.com/download/details.aspx?id=55106) innan du aktiverar replikering för datorerna för start i Azure.
Ubuntu | Ubuntu 14,04 LTS [-Server (granska stödda kernel-versioner)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16,04 LTS [-Server (granska stödda kernel-versioner)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(granska stödda kernel-versioner)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(granska de kernel-versioner som stöds)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Det finns inte stöd för att uppgradera replikerade datorer från SUSE Linux Enterprise Server 11 SP3 till SP4. Om du vill uppgradera inaktiverar du replikeringen och aktiverar den igen efter uppgraderingen.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> Köra Red Hat-kompatibel kernel eller Enterprise kernel release 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-versioner


**Version som stöds** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
14,04 LTS | [9,27][9.27 UR]| 3.13.0-24-genered to 3.13.0-170-Generic,<br/>3.16.0-25-genered to 3.16.0-77-Generic,<br/>3.19.0-18-genered till 3.19.0-80-genered,<br/>4.2.0-18-genered to 4.2.0-42-Generic,<br/>4.4.0-21-genered to 4.4.0-148-genered,<br/>4.15.0 – 1023 – Azure till 4.15.0 – 1045 – Azure |
14,04 LTS | [9.26][9.26 UR]| 3.13.0-24-genered to 3.13.0-170-Generic,<br/>3.16.0-25-genered to 3.16.0-77-Generic,<br/>3.19.0-18-genered till 3.19.0-80-genered,<br/>4.2.0-18-genered to 4.2.0-42-Generic,<br/>4.4.0-21-genered to 4.4.0-148-genered,<br/>4.15.0 – 1023 – Azure till 4.15.0 – 1045 – Azure |
14,04 LTS | [9,25][9.25 UR]  | 3.13.0 – 24-generisk till 3.13.0-169-Generic,<br/>3.16.0-25-genered to 3.16.0-77-Generic,<br/>3.19.0-18-genered till 3.19.0-80-genered,<br/>4.2.0-18-genered to 4.2.0-42-Generic,<br/>4.4.0 – 21-genered till 4.4.0-146--generisk,<br/>4.15.0 – 1023 – Azure till 4.15.0 – 1042 – Azure |
14,04 LTS | [9.24][9.24 UR] | 3.13.0 – 24-generisk till 3.13.0-167-Generic,<br/>3.16.0-25-genered to 3.16.0-77-Generic,<br/>3.19.0-18-genered till 3.19.0-80-genered,<br/>4.2.0-18-genered to 4.2.0-42-Generic,<br/>4.4.0 – 21-Generic to 4.4.0-143-Generic,<br/>4.15.0 – 1023 – Azure till 4.15.0 – 1040 – Azure |
|||
16,04 LTS | [9,27][9.27 UR] | 4.4.0 – 21-genered to 4.4.0-154-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-54-generic<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1050 – Azure|
16,04 LTS | [9.26][9.26 UR] | 4.4.0-21-genered to 4.4.0-148-genered,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-50-genered<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1045 – Azure|
16,04 LTS | [9,25][9.25 UR] | 4.4.0 – 21-genered till 4.4.0-146--generisk,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-48-genered<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0-1042 – Azure|
16,04 LTS | [9.24][9.24 UR] | 4.4.0 – 21-Generic to 4.4.0-143-Generic,<br/>4.8.0-34-Generic to 4.8.0-58-Generic,<br/>4.10.0-14-generic to 4.10.0-42-Generic,<br/>4.11.0-13-genered to 4.11.0-14-generic,<br/>4.13.0-16-genered to 4.13.0-45-genered,<br/>4.15.0-13-genered to 4.15.0-46-generic<br/>4.11.0-1009 – Azure till 4.11.0-1016-Azure,<br/>4.13.0-1005 – Azure till 4.13.0 – 1018 – Azure <br/>4.15.0-1012 – Azure till 4.15.0 – 1040 – Azure|

### <a name="debian-kernel-versions"></a>Debian Kernel-versioner


**Version som stöds** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9,24][9.24 UR], [9,25][9.25 UR],[9,26][9.26 UR], [9,27][9.27 UR]| 3.2.0 – 4-amd64 till 3.2.0-6-amd64, 3.16.0 -0. bpo. 4-amd64 |
|||
Debian 8 | [9,27][9.27 UR] | 3.16.0 – 4-amd64 till 3.16.0-9-amd64, 4.9.0 -0. bpo. 4-amd64 till 4.9.0 -0. bpo. 9-amd64 |
Debian 8 | [9,24][9.24 UR], [9,25][9.25 UR], [9,26][9.26 UR] | 3.16.0 – 4-amd64 till 3.16.0-8-amd64, 4.9.0 -0. bpo. 4-amd64 till 4.9.0 -0. bpo. 8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 stödda kernel-versioner

**Lansering** | **Mobilitets tjänst version** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,27][9.27 UR] | SP1 3.12.49-11-standard till 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69 – standard till 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default till 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default till 4.4.180-94.97-default</br></br>SP3 4.4.138-4,7-Azure till 4.4.180-4.31 – Azure</br></br>SP4 4.12.14-94.41 – standard till 4.12.14-95.19-default</br>SP4 4.12.14 – 6.3 – Azure till 4.12.14 – 6.15 – Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-standard till 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69 – standard till 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default till 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default till 4.4.178-94.91-default</br></br>SP3 4.4.138-4,7-Azure till 4.4.178-4.28 – Azure</br></br>SP4 4.12.14-94.41 – standard till 4.12.14-95.16-default</br>SP4 4.12.14 – 6.3 – Azure till 4.12.14 – 6,9 – Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,25][9.25 UR] | SP1 3.12.49-11-standard till 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69 – standard till 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default till 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default till 4.4.176-94.88-default</br></br>SP3 4.4.138-4,7-Azure till 4.4.176-4,25 – Azure</br></br>SP4 4.12.14-94.41 – standard till 4.12.14-95.13-default</br>SP4 4.12.14 – 6.3 – Azure till 4.12.14 – 6,9 – Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-standard till 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69 – standard till 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default till 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default till 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41 – standard till 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Linux-filsystem/gäst lagring

**Komponent** | **Stöds**
--- | ---
Fil system | ext3, ext4, XFS
Volym hanterare | -LVM stöds.<br/> -/Boot på LVM stöds från [Samlad uppdatering 31](https://support.microsoft.com/help/4478871/) (version 9,20 av mobilitets tjänsten) och senare. Den stöds inte i tidigare mobilitets tjänst versioner.<br/> -Flera operativ system diskar stöds inte.
Paravirtualiserade lagrings enheter | Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.
Block-i/o-enheter med flera köer | Stöds ej.
Fysiska servrar med HP CCISS Storage Controller | Stöds ej.
Namngivnings konvention för enhet/monterings punkt | Enhets namnet eller monterings punktens namn måste vara unikt.<br/> Se till att inga enheter/monterings punkter har Skift läges känsliga namn. Det finns till exempel inte stöd för namngivning av enheter för samma virtuella dator som *device1* och *device1* .
Kataloger | Om du kör en tidigare version av mobilitets tjänsten än version 9,20 (Publicerad i [Samlad uppdatering 31](https://support.microsoft.com/help/4478871/)) gäller följande begränsningar:<br/><br/> – Dessa kataloger (om de har ställts in som separata partitioner/fil system) måste finnas på samma OS-disk på käll servern:/(rot),/boot,/usr,/usr/local,/var,/etc.</br> -/Boot-katalogen bör finnas på en diskpartition och inte vara en LVM-volym.<br/><br/> Dessa begränsningar gäller inte från version 9,20 och senare. 
Start Katalog | – Start diskarna får inte vara i GPT-format. Detta är en begränsning för Azure-arkitekturen. GPT-diskar stöds som data diskar.<br/><br/> Det finns inte stöd för flera start diskar på en virtuell dator<br/><br/> -/Boot på en LVM-volym över mer än en disk stöds inte.<br/> -En dator utan en start disk kan inte replikeras.
Krav på ledigt utrymme| 2 GB på/root-partitionen <br/><br/> 250 MB på installationsmappen
XFSv5 | XFSv5-funktioner på XFS-filsystem, till exempel kontroll summa för metadata, stöds (mobilitets tjänst version 9,10 och senare).<br/> Använd verktyget xfs_info för att kontrol lera XFS-superblocket för partitionen. Om `ftype` är inställt på 1 används XFSv5-funktionerna.
BTRFS | BTRFS stöds från samlad [uppdatering 34](https://support.microsoft.com/help/4490016) (version 9,22 av mobilitets tjänsten) och senare. BTRFS stöds inte om:<br/><br/> -BTRFS fil systemets del volym ändras efter att skyddet Aktiver ATS.</br> -BTRFS fil system sprids över flera diskar.</br> -Fil systemet BTRFS har stöd för RAID.

## <a name="vmdisk-management"></a>Hantering av virtuell dator/disk

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på replikerad virtuell dator | Stöds.
Lägg till disk på replikerad virtuell dator | Stöds ej.<br/> Inaktivera replikering för den virtuella datorn, Lägg till disken och aktivera sedan replikering igen.

## <a name="network"></a>Nätverk

**Komponent** | **Stöds**
--- | ---
Värd nätverk NIC Teaming | Stöds för virtuella VMware-datorer. <br/><br/>Stöds inte för replikering av fysisk dator.
Värd nätverkets VLAN | Ja.
Värd nätverk IPv4 | Ja.
Värd nätverk IPv6 | Nej.
NIC-Teamning för gäst/Server-nätverk | Nej.
Gäst-/Server nätverk IPv4 | Ja.
Gäst-/Server nätverk IPv6 | Nej.
Statisk IP för gäst-/Server nätverk (Windows) | Ja.
Gäst-/Server nätverk statisk IP (Linux) | Ja. <br/><br/>Virtuella datorer konfigureras för att använda DHCP vid återställning efter fel.
Gäst-/Server nätverk flera nätverkskort | Ja.


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
Behåll Källans IP-adress | Ja
Tjänstslutpunkter för virtuellt nätverk i Azure<br/> | Ja
Accelererat nätverk | Nej

## <a name="storage"></a>Storage
**Komponent** | **Stöds**
--- | ---
Dynamisk disk | OS-disken måste vara en standard disk. <br/><br/>Data diskar kan vara dynamiska diskar
Konfiguration av Docker-disk | Nej
Värd-NFS | Ja för VMware<br/><br/> Nej för fysiska servrar
Värd-SAN (iSCSI/FC) | Ja
Värd virtuellt San | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Värd-multisökväg (MPIO) | Ja, testad med Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM för CLARiiON
Virtuella värd volymer (VVols) | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Gäst/Server VMDK | Ja
Gäst-/Server delad kluster disk | Nej
Gäst/Server-krypterad disk | Nej
Gäst-/Server-NFS | Nej
Gäst-/Server-iSCSI | För migrering – Ja<br/>Vid haveri beredskap – nej kommer iSCSI att återställas efter fel som en ansluten disk till den virtuella datorn
Gäst/Server SMB 3,0 | Nej
Gäst/Server-RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar
Gäst-/Server disk > 1 TB | Ja, disken måste vara större än 1024 MB<br/><br/>Upp till 8 192 GB vid replikering till hanterade diskar (9,26-version och senare)<br></br> Upp till 4 095 GB vid replikering till lagrings konton
Gäst/Server-disk med fysisk och fysisk sektor storlek för 4K | Nej
Gäst-/Server disk med en fysisk sektor storlek på 4K och 512 byte | Nej
Gäst-/Server volym med Striped disk > 4 TB <br/><br/>Hantering av logiska volymer (LVM)| Ja
Gäst/Server – lagrings utrymmen | Nej
Gäst/Server Hot Lägg till/ta bort disk | Nej
Gäst/Server – exkludera disk | Ja
Multisökväg för gäst/Server (MPIO) | Nej
GPT-partitioner för gäst/Server | Det finns stöd för fem partitioner från samlad [uppdatering 37](https://support.microsoft.com/help/4508614/) (version 9,25 av mobilitets tjänsten) och senare. Tidigare fanns stöd för fyra.
Gäst/Server-EFI/UEFI-start | – Stöds när du kör mobilitets tjänst version 9,13 eller senare.<br/> – Stöds när du migrerar virtuella VMware-datorer eller fysiska servrar som kör Windows Server 2012 eller senare till Azure.<br/> -Du kan bara replikera virtuella datorer för migrering. Återställning efter fel till lokal plats stöds inte.<br/> -Endast NTFS stöds <br/> -Start typen för säker UEFI stöds inte. <br/> – Disk sektor storleken ska vara 512 byte per fysisk sektor.

## <a name="replication-channels"></a>Kanaler för replikering

|**Typ av replikering**   |**Stöds**  |
|---------|---------|
|Avlästa data överföringar (ODX)    |       Nej  |
|Seeding offline        |   Nej      |
| Azure Data Box | Nej

## <a name="azure-storage"></a>Azure-lagring

**Komponent** | **Stöds**
--- | ---
Lokalt redundant lagring | Ja
Geografiskt redundant lagring | Ja
Geo-redundant lagring med Läs behörighet | Ja
Cool Storage | Nej
Frekvent lagring| Nej
Blockblob-objekt | Nej
Kryptering vid vila (SSE)| Ja
Premium Storage | Ja
Import/export-tjänst | Nej
Azure Storage brand väggar för virtuella nätverk | Ja.<br/> Konfigurerat på mål lagring/cache lagrings konto (används för att lagra replikeringsdata).
Allmänna-syfte v2-lagrings konton (frekventa och låg frekventa nivåer) | Ja (transaktions kostnader är betydligt högre för v2 jämfört med v1)

## <a name="azure-compute"></a>Azure-beräkning

**Funktion** | **Stöds**
--- | ---
Tillgänglighetsuppsättningar | Ja
Tillgänglighetszoner | Nej
) | Ja
Hanterade diskar | Ja

## <a name="azure-vm-requirements"></a>Krav för virtuell Azure-dator

Lokala virtuella datorer som replikeras till Azure måste uppfylla de krav på virtuella Azure-datorer som sammanfattas i den här tabellen. När Site Recovery kör en krav kontroll för replikering kommer kontrollen att Miss klar om några av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gäst operativ system | Kontrol lera att [operativ system som stöds](#replicated-machines) för replikerade datorer. | Kontrollen Miss lyckas om den inte stöds.
Gäst operativ systemets arkitektur | 64-bitars. | Kontrollen Miss lyckas om den inte stöds.
Storlek på operativ system disk | Upp till 2 048 GB. | Kontrollen Miss lyckas om den inte stöds.
Antal operativ system diskar | 1 | Kontrollen Miss lyckas om den inte stöds.
Antal data diskar | 64 eller mindre. | Kontrollen Miss lyckas om den inte stöds.
Data disk storlek | Upp till 8 192 GB vid replikering till hanterad disk (9,26-version och senare)<br></br>Upp till 4 095 GB vid replikering till lagrings konto| Kontrollen Miss lyckas om den inte stöds.
Nätverkskort | Flera nätverkskort stöds. |
Delad virtuell hård disk | Stöds ej. | Kontrollen Miss lyckas om den inte stöds.
FC-disk | Stöds ej. | Kontrollen Miss lyckas om den inte stöds.
BitLocker | Stöds ej. | BitLocker måste inaktive ras innan du aktiverar replikering för en dator. |
VM-namn | Mellan 1 och 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Dator namnet måste börja och sluta med en bokstav eller en siffra. |  Uppdatera värdet i dator egenskaperna i Site Recovery.

## <a name="churn-limits"></a>Omsättnings gränser

Följande tabell innehåller gränserna för Azure Site Recovery. 
- Dessa gränser är baserade på våra tester, men de gäller inte för alla möjliga kombinationer av app-I/O.
- De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program.
- För bästa resultat rekommenderar vi starkt att du kör [verktyget Deployment Planner](site-recovery-deployment-planner.md)och utför omfattande program testning med hjälp av redundanstest för att få den sanna prestanda bilden för din app.

**Mål för replikering** | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
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
- Dessa tal förutsätter en typisk efter släpning på ungefär fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.

## <a name="vault-tasks"></a>Valv aktiviteter

**Åtgärd** | **Stöds**
--- | ---
Flytta valv över resurs grupper | Nej
Flytta valvet inom och över prenumerationer | Nej
Flytta lagring, nätverk, virtuella Azure-datorer över resurs grupper | Nej
Flytta lagring, nätverk, virtuella Azure-datorer inom och över prenumerationer. | Nej


## <a name="obtain-latest-components"></a>Hämta de senaste komponenterna

**Namn** | **Beskrivning** | **Detaljer**
--- | --- | ---
Konfigurationsserver | Installerat lokalt.<br/> Koordinerar kommunikationen mellan lokala VMware-servrar eller fysiska datorer och Azure. | - [Läs mer om](vmware-physical-azure-config-process-server-overview.md) konfigurations servern.<br/> - [Lär dig mer om](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) att uppgradera till den senaste versionen.<br/> - [Lär dig hur](vmware-azure-deploy-configuration-server.md) du konfigurerar konfigurations servern. 
Processerver | Installeras som standard på konfigurationsservern.<br/> Tar emot replikeringsdata, optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure.<br/> När distributionen växer kan du lägga till ytterligare process servrar för att hantera större volymer av replikeringstrafiken. | - [Lär dig mer om](vmware-physical-azure-config-process-server-overview.md) processervern.<br/> - [Lär dig mer om](vmware-azure-manage-process-server.md#upgrade-a-process-server) att uppgradera till den senaste versionen.<br/> - [Lär dig mer om](vmware-physical-large-deployment.md#set-up-a-process-server) att konfigurera skalbara process servrar.
Mobilitets tjänst | Installerat på virtuella VMware-datorer eller fysiska servrar som du vill replikera.<br/> Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure.| - [Lär dig mer om](vmware-physical-mobility-service-overview.md) mobilitets tjänsten.<br/> - [Lär dig mer om](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) att uppgradera till den senaste versionen.<br/> 



## <a name="next-steps"></a>Nästa steg
[Lär dig hur](tutorial-prepare-azure.md) du förbereder Azure för haveri beredskap för virtuella VMware-datorer.

[9.27 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
