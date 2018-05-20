---
title: Stöd matrix för att replikera virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Sammanfattar de operativsystem som stöds och komponenter för att replikera virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: raynew
ms.openlocfilehash: a7e0455d92635b7767227685b622bdae303f9621
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Stöd matrix för VMware och fysiska servrar replikering till Azure

Den här artikeln sammanfattar stöds komponenter och inställningar för katastrofåterställning av virtuella VMware-datorer till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="replication-scenario"></a>Scenario för replikering

**Scenario** | **Detaljer**
--- | ---
VMwares virtuella datorer | Replikeringen av lokala virtuella VMware-datorer till Azure. Du kan distribuera det här scenariot i Azure-portalen eller genom att använda [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Fysiska servrar | Replikeringen av lokala Windows-/ Linux fysiska serversto Azure. Du kan distribuera det här scenariot i Azure-portalen.

## <a name="on-premises-virtualization-servers"></a>Lokala virtualiseringsservrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
VMware | vCenter Server 6.5 6.0, eller 5.5 eller vSphere 6.5, 6.0 eller 5.5 | Vi rekommenderar att du använder en vCenter-server.<br/><br/> Vi rekommenderar att vSphere-värdar och vCenter-servrar finns i samma nätverk som processervern. Som standard körs process-serverkomponenter på konfigurationsservern, så att det här nätverket där du ställer in konfigurationsservern och om du inte anger en dedikerad processerver.
Fysiska | Gäller inte

## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurationsservern

Konfigurationsservern är en lokal dator som kör Site Recovery-komponenter, inklusive konfigurationsservern, processervern och huvudmålservern. För VMware-replikering upprättar du konfigurationsservern med alla krav, med ett OVF-mall för att skapa en VMware VM. För replikering av fysisk server konfigurerar du configuration server-datorn manuellt.

**Komponent** | **Krav**
--- |---
Processorkärnor | 8
RAM | 12 GB
Antal diskar | 3 diskar<br/><br/> Diskar innehåller OS-disk, disk för processen server cache och kvarhållningsenhetens för återställning efter fel.
Ledigt diskutrymme | 600 GB diskutrymme som krävs för processen serverns cacheminne.
Ledigt diskutrymme | 600 GB diskutrymme som krävs för kvarhållningsenhetens.
Operativsystem  | Windows Server 2012 R2 eller Windows Server 2016 |
Nationella inställningar för operativsystem | Engelska (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") ska installeras.
Windows Server-roller | Aktivera inte: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Grupprinciper| Aktivera inte: <br> -Förhindra åtkomst till Kommandotolken. <br> -Förhindra åtkomst till verktyg för redigering av registret. <br> -Förtroende för bifogade filer. <br> -Aktivera körning av skript. <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Se till att du:<br/><br/> -Inte har en befintlig standardwebbplatsen <br> -Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställning  <br> -Inte har redan befintliga webbplats/app lyssnar på port 443<br>
NIC-typ | VMXNET3 (när distribueras som en VM VMware)
IP-adresstyp | Statisk
Portar | 443 används för kontrollen kanal orchestration)<br>9443 som används för datatransport

## <a name="replicated-machines"></a>Replikerade datorer

Site Recovery har stöd för replikering av alla arbetsbelastningar som körs på en dator som stöds.

**Komponent** | **Detaljer**
--- | ---
Inställningarna för datorn | Datorer som replikeras till Azure måste uppfylla [krav för Azure](#azure-vm-requirements).
Windows-operativsystem | 64-bitars Windows Server 2016 (Server Core, Server med Skrivbordsmiljö), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 med på minst SP1. Windows 2016 Nano Server stöds inte.
Linux-operativsystem | Red Hat Enterprise Linux: 5.2 till 5.11, 6.1 6,9, 7.0 7.4 <br/><br/>CentOS: 5.2 till 5.11, 6.1 6,9, 7.0 7.4 <br/><br/>Ubuntu 14.04 LTS server[ (kernel-versioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server[ (kernel-versioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (kernel-versioner som stöds)](#debian-kernel-versions)<br/><br/>Oracle Enterprise Linux 6.4, 6.5 med Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>Uppgradera replikerade datorer från SP3 till SP4 stöds inte. Uppgradera genom att inaktivera replikering och aktivera det igen efter uppgraderingen.

>[!NOTE]
>
> - På Linux-distributioner stöds bara lager kernlar som är en del av distributionen delversion versionen/uppdatera.
>
> - Uppgradering av skyddade datorer över större Linux distribution versioner inte stöds. Om du vill uppgradera, inaktivera replikering, uppgradera operativsystemet och sedan aktivera replikering igen.
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-versioner


**Versionen som stöds** | **Azure Site Recovery-Mobilitetstjänsten-versionen** | **Kernel-version** |
--- | --- | --- |
14.04 LTS | 9.12 | 3.13.0-24-Generic till 3.13.0-132-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic till 3.13.0-137-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-Generic till 3.13.0-142-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-116-generic |
14.04 LTS | 9.15 | 3.13.0-24-Generic till 3.13.0-144-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-119-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic till 4.4.0-96-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic till 4.4.0-104-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-Generic till 4.4.0-116-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-36-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1011-azure |
16.04 LTS | 9.15 | 4.4.0-21-Generic till 4.4.0-119-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-38-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1012-azure |



### <a name="debian-kernel-versions"></a>Debian kernel-versioner


**Versionen som stöds** | **Azure Site Recovery-Mobilitetstjänsten-versionen** | **Kernel-version** |
--- | --- | --- |
Debian 7 | 9.14, 9.15 | 3.2.0-4-amd64 till 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14, 9.15 | 3.16.0-4-amd64 till 3.16.0-5-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.5-amd64 |


## <a name="linux-file-systemsguest-storage"></a>Linux-system/gäst fillagring

**Komponent** | **Stöds**
--- | ---
Filsystem | ext3, ext4, XFS.
Volymhanterare | LVM2.
Paravirtualized lagringsenheter | Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.
Flera kön blockera-i/o-enheter | Stöds ej.
Fysiska servrar med lagringsstyrenheten HP CCISS | Stöds ej.
Kataloger | Dessa kataloger (om konfigurerad som separata partitioner /-filsystem) måste vara på samma OS-disk på källservern: / (rot), / Boot/usr, /usr/local, /var, / etc.</br></br> / Boot ska finnas på en diskpartition och inte en LVM volym.<br/><br/>
Krav på ledigt diskutrymme| 2 GB på/Root-partition <br/><br/> 250 MB på installationsmappen
XFSv5 | XFSv5 funktioner på XFS filsystem, till exempel metadata kontrollsumma, stöds från Mobilitetstjänsten version 9.10 och framåt. Använda verktyget xfs_info för att kontrollera XFS superblock för partitionen. Om ftype har angetts till 1, är funktioner för XFSv5 används.



## <a name="network"></a>Nätverk

**Komponent** | **Stöds**
--- | ---
Värd för nätverket NIC-Teamindelning | Stöd för virtuella VMware-datorer. <br/><br/>Stöds inte för replikeringen av den fysiska datorn.
Värd för nätverk VLAN | Ja.
Värd för nätverket IPv4 | Ja.
Värd för nätverket IPv6 | Nej.
NIC Teaming gäst-server-nätverk | Nej.
Gäst-server-nätverk IPv4 | Ja.
Gäst-server-nätverk IPv6 | Nej.
Gästen/server network statiska IP-Adressen (Windows) | Ja.
Gästen/server network statiska IP-Adressen (Linux) | Ja. <br/><br/>Virtuella datorer är konfigurerade för att använda DHCP på återställning.
Gästen/server network flera nätverkskort | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure VM-nätverk (efter växling vid fel)

**Komponent** | **Stöds**
--- | ---
Azure ExpressRoute | Ja
ILB | Ja
ELB | Ja
Azure Traffic Manager | Ja
Flera nätverkskort | Ja
Reserverad IP-adress | Ja
IPv4 | Ja
Behåll källans IP-adress | Ja
Azure Virtual Network service-slutpunkter<br/> (utan Azure Storage brandväggar) | Ja
Accelererat nätverk | Nej

## <a name="storage"></a>Storage
**Komponent** | **Stöds**
--- | ---
Värden NFS | Ja för VMware<br/><br/> Nej för fysiska servrar
SAN-nätverk (iSCSI/FC) värd | Ja
Värden virtuellt SAN-nätverk | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Värden multipath (MPIO) | Ja, testas med Microsoft DSM, EMC PowerPath 5.7 SP4 EMC PowerPath DSM för CLARiiON
Virtuella Värdvolymerna (VVols) | Ja för VMware<br/><br/> Ej tillämpligt för fysiska servrar
Gästen/server VMDK | Ja
Gästen/server EFI/UEFI| Partiell (migrering till Azure för Windows Server 2012 och senare VMware endast virtuella datorer) </br></br> Se anmärkningen i slutet av tabellen
Delad klusterdisk för gäst-server | Nej
Krypterade disk i gäst-server | Nej
Gästen/server NFS | Nej
Gästen/server SMB 3.0 | Nej
Gästen/server RDM | Ja<br/><br/> Ej tillämpligt för fysiska servrar
Gästen/server disk > 1 TB | Ja<br/><br/>Upp till 4,095 GB
Gästen/server disk med 4K logisk och 4 k fysisk sektorstorlek | Ja
Gästen/server-disk med 4K logisk och fysisk sektorstorlek för 512 byte | Ja
Gästen/server-volym med stripe disk > 4 TB <br><br/>Logisk volym management (LVM)| Ja
Gästen/server - lagringsutrymmen | Nej
Gästen/server varm Lägg till/ta bort disken | Nej
Gästen/server - utelämna disk | Ja
Gästen/server multipath (MPIO) | Nej

> [!NOTE]
> UEFI Start VMware-datorer som kör Windows Server 2012 eller senare kan migreras till Azure. Följande begränsningar gäller:

> - Endast migrering till Azure stöds. Återställning till det lokala VMware-platsen stöds inte.
> - Servern bör inte ha fler än fyra partitioner på OS-disk.
> - Kräver 9.13 eller senare version av Mobilitetstjänsten.
> - Stöds inte för fysiska servrar.

## <a name="azure-storage"></a>Azure-lagring

**Komponent** | **Stöds**
--- | ---
Lokalt redundant lagring | Ja
Geografiskt redundant lagring | Ja
Geo-redundant lagring med läsbehörighet | Ja
Lågfrekvent | Nej
Frekvent| Nej
Blockblob-objekt | Nej
Kryptering i vila (Storage Service-kryptering)| Ja
Premium Storage | Ja
Import/export service | Nej
Azure Storage-brandväggar för virtuella nätverk som konfigurerats på mål-storage-cache lagringskontot (används för att lagra data för replikering) | Nej
Generella v2 storage-konton (både frekvent och lågfrekvent nivå) | Nej

## <a name="azure-compute"></a>Azure-beräkning

**Funktion** | **Stöds**
--- | ---
Tillgänglighetsuppsättningar | Ja
HUBBEN | Ja
Hanterade diskar | Ja

## <a name="azure-vm-requirements"></a>Krav för Azure VM

Lokala virtuella datorer som du replikerar till Azure måste uppfylla kraven för Azure VM som sammanfattas i den här tabellen. När Site Recovery körs en kravkontroll misslyckas om vissa av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystemet | Kontrollera [operativsystem](#replicated machines). | Det går inte att kontrollera om stöds inte.
Gästen operativsystemets arkitektur | 64-bitars. | Det går inte att kontrollera om stöds inte.
Operativsystemdisken | Upp till 2 048 GB. | Det går inte att kontrollera om stöds inte.
Operativsystemet disk antal | 1 | Det går inte att kontrollera om stöds inte.  
Datadiskar | 64 eller mindre. | Det går inte att kontrollera om stöds inte.  
Datadiskstorleken | Upp till 4,095 GB | Det går inte att kontrollera om stöds inte.
Nätverkskort | Stöd för flera kort. |
Delad virtuell Hårddisk | Stöds ej. | Det går inte att kontrollera om stöds inte.
FC-disk | Stöds ej. | Det går inte att kontrollera om stöds inte.
BitLocker | Stöds ej. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator. |
VM-namn | Mellan 1 och 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Datornamnet måste börja och sluta med en bokstav eller siffra. |  Uppdatera värdet i datoregenskaperna i Site Recovery.


## <a name="vault-tasks"></a>Valvet uppgifter

**Åtgärd** | **Stöds**
--- | ---
Flytta valvet mellan resursgrupper<br/><br/> Inom och över prenumerationer | Nej
Flytta lagring, nätverk, virtuella datorer i Azure över resursgrupper<br/><br/> Inom och över prenumerationer | Nej


## <a name="mobility-service"></a>Mobilitetstjänsten

**Namn** | **Beskrivning** | **senaste versionen** | **Detaljer**
--- | --- | --- | --- | ---
Azure Site Recovery enhetlig installation | Samordnar kommunikationen mellan lokala VMware-servrar och Azure <br/><br/> Installerad på lokal VMware-servrar | 9.12.4653.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)
Mobilitetstjänsten | Samordnar replikering mellan lokala VMware-servrar/fysiska servrar och Azure/sekundär plats<br/><br/> Installerad på VMware VM eller fysiska servrar som du vill replikera | 9.12.4653.1 (tillgänglig från portalen) | [Senaste funktionerna och korrigeringarna](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Nästa steg
[Lär dig hur](tutorial-prepare-azure.md) att förbereda Azure för återställning av virtuella VMware-datorer.
