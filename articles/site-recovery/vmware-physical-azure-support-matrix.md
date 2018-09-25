---
title: Stödmatris för att replikera virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Sammanfattning av de operativsystem som stöds och komponenter för att replikera virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: raynew
ms.openlocfilehash: 0878bdd435b6ec9efccca2cc825242a65bd79909
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042235"
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Supportmatris för VMware och fysisk serverreplikering till Azure

Den här artikeln sammanfattar komponenter som stöds och inställningar för haveriberedskap för virtuella VMware-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Om du vill börja använda Azure Site Recovery med det enklaste distributionsscenariot som genom att gå till vår [självstudier](tutorial-prepare-azure.md). Du kan läsa mer om Azure Site Recovery-arkitekturen [här](vmware-azure-architecture.md).

## <a name="replication-scenario"></a>Replikeringsscenario

**Scenario** | **Detaljer**
--- | ---
VMwares virtuella datorer | Replikering av lokala virtuella VMware-datorer till Azure. Du kan distribuera det här scenariot i Azure portal eller med hjälp av [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Fysiska servrar | Replikering av lokala Windows-/ Linux fysiska servrar till Azure. Du kan distribuera det här scenariot i Azure-portalen.

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
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") ska installeras.
Windows Server-roller | Aktivera inte: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Grupprinciper| Aktivera inte: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till registerredigeringsverktygen. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Se till att du:<br/><br/> -Inte har en befintlig standardwebbplatsen <br> -Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen  <br> -Inte har redan befintliga webbplats/appen lyssnar på port 443<br>
Typ av nätverkskort | VMXNET3 (när distribueras som en VMware VM)
IP-adresstyp | Statisk
Portar | 443 används för kontrolkanalsorchestration)<br>9443 som används för dataöverföring

## <a name="replicated-machines"></a>Replikerade datorer

Site Recovery har stöd för replikering av alla arbetsbelastningar som körs på en dator som stöds.

**Komponent** | **Detaljer**
--- | ---
Datorinställningar | Datorer som replikeras till Azure måste uppfylla [krav för Azure](#azure-vm-requirements).
Windows-operativsystem | 64-bitars Windows Server 2016 (Server Core, Server med Skrivbordsmiljö), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1. </br></br>  [Windows Server 2008 med på minst SP2 - 32-bitars och 64-bitars](migrate-tutorial-windows-server-2008.md) (migrering). </br></br> Windows 2016 Nano Server stöds inte.
Linux-operativsystem | Red Hat Enterprise Linux: 5.2 till 5.11<b>\*\*</b>, 6.1 6.10<b>\*\*</b>, 7.0 7.5 <br/><br/>CentOS: 5.2 till 5.11<b>\*\*</b>, 6.1 6.10<b>\*\*</b>, 7.0 7.5 <br/><br/>Ubuntu 14.04 LTS server[ (kernel-versioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server[ (kernel-versioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (kernel-versioner som stöds)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (kernel-versioner som stöds)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Enterprise Linux 6.4, 6.5, 6.6, 6.7 som kör Red Hat kompatibla kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/></br>* *Uppgradera replikerade datorer från SUSE Linux Enterprise Server 11 SP3 till SP4 stöds inte. Om du vill uppgradera, inaktiverar du replikering och aktivera det igen efter uppgraderingen.*</br></br><b>\*\*</b> *Referera till [stöd för Linux-datorer i Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) att förstå stöd för Linux och teknik med öppen källkod i Azure. Azure Site Recovery kan du redundans och kör Linux-servrar i Azure, men Linux-leverantörer kan begränsa stödet till bara dessa versioner av deras distribution som inte har nått end of life.*


>[!NOTE]
>
> - På Linux-distributioner stöds endast lagerartiklar kernlar som är en del av distributionen delversion versionen/uppdatera.
>
> - Uppgradera skyddade datorer i större Linux distribution versioner inte stöds. Om du vill uppgradera, inaktiverar du replikering, uppgradera operativsystemet och aktiverar sedan replikeringen igen.
>
> - Servrar som kör Red Hat Enterprise Linux 5.2 5.11 eller CentOS 5.2 5.11 ska ha den [Linux Integration Services(LIS) komponenter](https://www.microsoft.com/en-us/download/details.aspx?id=55106) installerat för datorer att starta i Azure.

### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-versioner


**Versionen som stöds** | **Azure Site Recovery-Mobilitetstjänsten-versionen** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-Generic till 3.13.0-153-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-Generic till 3.13.0-153-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-130-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic till 3.13.0-149-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-127-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic till 3.13.0-144-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-119-generic |
|||
16.04 LTS | 9.19 | 4.4.0-21-Generic till 4.4.0-131-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-30-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-Generic till 4.4.0-130-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic |
16.04 LTS | 9.17 | 4.4.0-21-Generic till 4.4.0-127-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-43-generic |
16.04 LTS | 9.16 | 4.4.0-21-Generic till 4.4.0-119-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-38-generic |


### <a name="debian-kernel-versions"></a>Debian kernel-versioner


**Versionen som stöds** | **Azure Site Recovery-Mobilitetstjänsten-versionen** | **Kernelversion** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-amd64 till 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-amd64 till 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 till 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 till 3.16.0-5-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.6-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 kernel-versioner som stöds

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.96-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default till 4.4.138-94.39-default |

## <a name="linux-file-systemsguest-storage"></a>Linux-system/gäst fillagring

**Komponent** | **Stöds**
--- | ---
Filsystem | ext3, ext4, XFS.
Volymhanterare | LVM2. LVM stöds för data-diskar. Azure virtuella datorer har bara en enda OS-disk.
Paravirtualiserade lagringsenheter | Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.
Flera kö blockera-i/o-enheter | Stöds ej.
Fysiska servrar med lagringsstyrenhet HP CCISS | Stöds ej.
Kataloger | Dessa kataloger (om konfigurerad som separata partitioner /-filsystem) alla måste finnas i samma OS-disken på källservern: / (rot), / Boot, usr, / usr/local, /var, / etc.</br></br> / Boot bör finnas på en diskpartition och inte en LVM-volym.<br/><br/>
Krav för ledigt utrymme| 2 GB på/Root-partition <br/><br/> 250 MB på installationsmappen
XFSv5 | XFSv5 funktioner på XFS filsystem, till exempel metadata kontrollsumma stöds från Mobilitetstjänsten version 9.10 och uppåt. Använda verktyget xfs_info för att kontrollera XFS superblock för partitionen. Om ftype har angetts till 1, är XFSv5 funktioner används.

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
Azure Virtual Network-tjänstslutpunkter<br/> (utan Azure Storage-brandväggar) | Ja
Accelererat nätverk | Nej

## <a name="storage"></a>Storage
**Komponent** | **Stöds**
--- | ---
Värden NFS | Ja för VMware<br/><br/> Nej för fysiska servrar
SAN-nätverk (iSCSI/FC) värd | Ja
Värden virtuellt SAN-nätverk | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Värden multipath (MPIO) | Ja, testas med Microsoft DSM, EMC PowerPath 5.7 SP4 EMC PowerPath DSM för CLARiiON
Virtuella Värdvolymer (VVols) | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Gäst/server VMDK | Ja
Gäst/server EFI/UEFI| Delvis (migrering till Azure för Windows Server 2012 och senare VMware virtuella datorer endast) </br></br> Se kommentaren i slutet av tabellen
Gäst/server delad klusterdisk | Nej
Gäst/server krypterad disk | Nej
Gäst/server NFS | Nej
Gäst/server SMB 3.0 | Nej
Gäst/server RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar
Gäst/server disk > 1 TB | Ja<br/><br/>Upp till 4095 GB
Gäst/server disk med 4K logisk och 4 k fysisk sektorstorlek | Ja
Gäst/server-disk med 4K logisk och fysisk sektorstorlek för 512 byte | Ja
Gäst/server-volym med stripe-disk > 4 TB <br><br/>Hantering av logisk volym (LVM)| Ja
Gäst/server - lagringsutrymmen | Nej
Gäst/server frekvent Lägg till/ta bort disk | Nej
Gäst/server - Uteslut disk | Ja
Gäst/server multipath (MPIO) | Nej

> [!NOTE]
> UEFI-Start VMware-datorer som kör Windows Server 2012 eller senare kan migreras till Azure. Följande begränsningar gäller:

> - Stöds endast migrering till Azure. Växla tillbaka till den lokala VMware-platsen stöds inte.
> - Servern bör inte ha fler än fyra partitioner på operativsystemdisken.
> - Kräver Mobilitetstjänsten version 9.13 eller senare.
> - Stöds inte för fysiska servrar.

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
Azure Storage-brandväggar för virtuella nätverk som konfigurerats på mållagringskontot för lagring/cache (används för att lagra data för replikering) | Nej
Storage-konton för generell användning v2 (frekvent och lågfrekvent nivå) | Nej

## <a name="azure-compute"></a>Azure-beräkning

**Funktion** | **Stöds**
--- | ---
Tillgänglighetsuppsättningar | Ja
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


## <a name="vault-tasks"></a>Valvet uppgifter

**Åtgärd** | **Stöds**
--- | ---
Flytta valv mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Nej
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper<br/><br/> Inom och mellan prenumerationer | Nej


## <a name="download-latest-azure-site-recovery-components"></a>Hämta senaste Azure Site Recovery-komponenter

**Namn** | **Beskrivning** | **Senaste version Hämtningsinstruktioner**
--- | --- | --- | --- | ---
Konfigurationsservern | Samordnar kommunikationen mellan lokala VMware-servrar och Azure <br/><br/> Installerad på den lokala VMware-servrar | Ny installation klickar du på [här](vmware-azure-deploy-configuration-server.md). För att uppgradera befintliga komponenten till senaste versionen, klickar du på [här](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Processervern|Installeras som standard på konfigurationsservern. Den tar emot replikeringsdata; optimerar dem med cachelagring, komprimering och kryptering och skickar dem till Azure Storage. Allt eftersom distributionen växer kan du lägga till ytterligare, separat processervrar för att hantera större mängder replikeringstrafik.| Ny installation klickar du på [här](vmware-azure-set-up-process-server-scale.md). För att uppgradera befintliga komponenten till senaste versionen, klickar du på [här](vmware-azure-manage-process-server.md#upgrade-a-process-server).
Mobilitetstjänsten | Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure/sekundär plats<br/><br/> Installerad på VMware VM eller fysiska servrar som du vill replikera | Ny installation klickar du på [här](vmware-azure-install-mobility-service.md). För att uppgradera befintliga komponenten till senaste versionen, klickar du på [här](vmware-azure-install-mobility-service.md#update-mobility-service).

Mer information om de senaste funktionerna och korrigeringar, klickar du på [här](https://aka.ms/latest_asr_updates).


## <a name="next-steps"></a>Nästa steg
[Lär dig hur](tutorial-prepare-azure.md) du förbereder Azure för haveriberedskap för virtuella VMware-datorer.
