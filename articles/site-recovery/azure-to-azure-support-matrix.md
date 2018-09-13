---
title: Azure Site Recovery-stödmatris för replikering från Azure till Azure | Microsoft Docs
description: Sammanfattning av de operativsystem som stöds och konfigurationer för Azure Site Recovery-replikering av virtuella Azure-datorer (VM) från en region till en annan för disaster recovery (DR) behov.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: sujayt
ms.openlocfilehash: 105c1d97a812841e82a0c364ec7dda097c0dd399
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44717377"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Stöd matrix för replikering från en Azure-region till en annan



Den här artikeln sammanfattar konfigurationer som stöds och komponenter när du replikerar och återställa virtuella Azure-datorer från en region till en annan region med hjälp av den [Azure Site Recovery](site-recovery-overview.md) service.

## <a name="user-interface-options"></a>Alternativ för användargränssnitt

**Användargränssnitt** |  **Stöds / stöds inte**
--- | ---
**Azure Portal** | Stöds
**PowerShell** | [Replikering från Azure till Azure med PowerShell](azure-to-azure-powershell.md)
**REST API** | Stöds för närvarande inte
**CLI** | Stöds för närvarande inte


## <a name="resource-support"></a>Resursstöd för

**Flytta resurstypen** | **Detaljer**
--- | --- | ---
**Flytta valv mellan resursgrupper** | Stöds inte<br/><br/> Du kan inte flytta ett Recovery services-valv mellan resursgrupper.
**Flytta resurser för beräkning/lagringsnätverk mellan resursgrupper** | Stöds ej.<br/><br/> Om du flyttar en virtuell dator eller tillhörande komponenter, till exempel lagringsnätverk/när den replikerar, måste du inaktivera replikering och återaktivera replikering för den virtuella datorn.
**Replikera virtuella Azure-datorer från en prenumeration till en annan för katastrofåterställning** | Stöd för inom samma Azure Active Directory-klientorganisation. Stöds inte för klassiska virtuella datorer.
**Migrera virtuella datorer mellan regioner i stöds geografiska kluster (inom och mellan prenumerationer)** | Stöd för inom samma Azure Active Directory-klient för ”Resource manager-distributionsmodellen” virtuella datorer. För virtuella datorer i klassiska distributionsmodellen stöds inte.
**Migrera virtuella datorer i samma region** | Stöds ej.


## <a name="support-for-replicated-machine-os-versions"></a>Stöd för replikerade datorn OS-versioner

Den nedan support som gäller för alla arbetsbelastningar som körs på den nämnda OS.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, Server med Skrivbordsmiljö) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 med på minst SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server stöds inte.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6,9, 6.10, 7.0, 7.1, 7.2, 7.3,7.4, 7.5
- Ubuntu 14.04 LTS Server [ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (kernel-versioner som stöds)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (kernel-versioner som stöds)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1, SP2, SP3 [ (kernel-versioner som stöds)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Enterprise Linux 6.4, 6.5 som kör Red Hat kompatibla kernel eller Unbreakable Enterprise Kernel version 3 (UEK3)

(För att replikera datorer från SLES 11 SP3 till SLES 11 SP4 kan inte uppgradera. Om en replikerad dator har uppgraderats från SLES 11SP3 till SLES 11 SP4, måste du du inaktivera replikering och skydda datorn igen efter uppgraderingen.)

>[!NOTE]
>
> Ubuntu-servrar använder lösenordsbaserad autentisering och logga in och för att konfigurera molnbaserade virtuella datorer med cloud-init-paketet kan ha lösenordsbaserad inloggning inaktiveras vid redundans (beroende på cloudinit konfiguration.) Lösenordsbaserad inloggning kan återaktiveras på den virtuella datorn genom att återställa lösenordet från inställningsmenyn (under stöd + felsökning avsnitt) för den redundansväxlade virtuella datorn på Azure portal.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ubuntu kernel-versioner som stöds för Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | 9.19 | 3.13.0-24-Generic till 3.13.0-153-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-Generic till 3.13.0-151-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-Generic till 3.13.0-147-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-124-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic till 3.13.0-144-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-119-generic |
|||
16.04 LTS | 9.19 | 4.4.0-21-Generic till 4.4.0-131-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-30-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-Generic till 4.4.0-128-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-Generic till 4.4.0-124-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-41-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1016-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic till 4.4.0-119-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-38-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian kernel-versioner som stöds för virtuella Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | 3.2.0-4-amd64 till 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | 3.16.0-4-amd64 till 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17, 9.18 | 3.16.0-4-amd64 till 3.16.0-6-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 till 3.16.0-5-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.5-amd64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 12 kernel-versioner som stöds för Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.18 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default till 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.17 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default till 4.4.126-94.22-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Filsystem som stöds och Gäst lagringskonfigurationer på Azure virtuella datorer som kör Linux-operativsystem

* Filsystem: ext3 ext4, ReiserFS (Suse Linux Enterprise Server endast), XFS
* Volymhanterare: LVM2
* Programvaran MultiPath: enheten Mapper

## <a name="region-support"></a>Regionsstöd

Du kan replikera och återställa virtuella datorer mellan alla två regioner inom samma geografiska kluster.

**Geografisk kluster** | **Azure-regioner**
-- | --
Sydamerika | Kanada, östra, Kanada, centrala, södra centrala USA, USA, västra centrala, USA, östra, USA, östra 2, västra USA, västra USA 2, centrala USA, norra centrala USA
Europa | Storbritannien, västra, Storbritannien, södra, Nordeuropa, Västeuropa, Frankrike, centrala och Frankrike, södra
Asien | Södra Indien, centrala Indien, Sydostasien, östra Asien, östra, Japan, Japan västra, centrala Korea, södra
Australien   | Östra Australien, sydöstra Australien
Azure Government    | Virginia (USA-förvaltad region), Iowa (USA-förvaltad region), USA-förvaltad region Arizona, US GOV Texas, US DOD, östra, US DOD, centrala
Tyskland | Tyskland, centrala, Tyskland, nordöstra
Kina | Östra Kina, norra Kina

>[!NOTE]
>
> För regionen södra Brasilien, du bara replikera och redundansväxla till en av södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA, västra USA 2 och USA, norra centrala regioner och växla tillbaka.

## <a name="support-for-vmdisk-management"></a>Stöd för VM/Diskhantering

**Åtgärd** | **Detaljer**
-- | ---
Ändra storlek på disk på den replikerade virtuella datorn | Stöds
Lägg till disk i replikerade virtuella datorn | Stöds ej. Du måste inaktivera replikering för den virtuella datorn, Lägg till disk och aktiverar sedan replikeringen igen.


## <a name="support-for-compute-configuration"></a>Stöd för beräkningskonfigurationen

**Konfiguration** | **Stöds/stöds ej** | **Kommentarer**
--- | --- | ---
Storlek | Alla Azure VM-storlekar med minst 2 CPU-kärnor och 1 GB RAM-minne | Referera till [storlekar för Azure virtuella datorer](../virtual-machines/windows/sizes.md)
Tillgänglighetsuppsättningar | Stöds | Om du använder alternativet under ”Aktivera replikering” steg i portalen är tillgänglighetsuppsättningen automatiskt skapa baserat på konfigurationen av datakällan region. Du kan ändra tillgänglighetsuppsättning för målet i ”replikerat objekt > Inställningar > beräkning och nätverk > tillgänglighetsuppsättning” när som helst.
Tillgänglighetszoner | Stöds inte | Virtuella datorer som distribueras i tillgänglighetszoner stöds inte för närvarande.
Hybrid Använd förmånen (HUB) virtuella datorer | Stöds | Om den Virtuella källdatorn har HUB-licens, använder redundanstest eller Failover VM också HUB-licens.
Skalningsuppsättningar för virtuella datorer | Stöds inte |
Galleriavbildningar för Azure - Microsoft publicerat | Stöds | Stöds så länge som den virtuella datorn körs på ett operativsystem som stöds av Site Recovery
Azure-galleriet-avbildningar – från tredje part publicerats | Stöds | Stöd för så länge som den virtuella datorn körs på ett operativsystem som stöds av Site Recovery.
Anpassade avbildningar - från tredje part publicerats | Stöds | Stöd för så länge som den virtuella datorn körs på ett operativsystem som stöds av Site Recovery.
Virtuella datorer migreras med hjälp av Site Recovery | Stöds | Om så är en VMware/fysisk dator migreras till Azure med Site Recovery, måste du avinstallera den äldre versionen av mobilitetstjänsten och starta om datorn innan du replikerar till en annan Azure-region.

## <a name="support-for-storage-configuration"></a>Stöd för lagringskonfiguration

**Konfiguration** | **Stöds/stöds ej** | **Kommentarer**
--- | --- | ---
Maxstorleken för OS-disk | 2 048 GB | Referera till [diskar som används av virtuella datorer.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Maximal datadiskstorleken | 4 095 GB | Referera till [diskar som används av virtuella datorer.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Antal datadiskar | Upp till 64 som stöds av en viss Azure VM-storlek | Referera till [storlekar för Azure virtuella datorer](../virtual-machines/windows/sizes.md)
Temporär disk | Alltid undantogs från replikering | Temporär disk har exkluderats från repliken alltid. Du bör inte placera någon beständiga data på temporär disk enligt Azure-vägledning. Referera till [temporär disk på Azure Virtual Machines](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) för mer information.
Förändringstakten för data på disken | Maximalt 10 Mbit/s per disk för Premium-lagring och 2 Mbit/s per disk för standardlagring | Om den genomsnittliga dataändringshastigheten på disken är längre än 10 Mbit/s (för Premium) och 2 Mbit/s (för Standard) kontinuerligt, replikering inte att fånga. Om det är en databearbetning burst och förändringstakten för data som är större än 10 Mbit/s (för Premium) och 2 Mbit/s (för Standard) under en viss tid och handlar om, fånga replikering upp. I det här fallet kan du se något fördröjda återställningspunkter.
Diskar på lagringskonton av standardtyp | Stöds |
Diskar på premium storage-konton | Stöds | Om en virtuell dator har diskar som är fördelade på premium- och standardlagringskonton, kan du välja ett annat mållagringskonto för varje disk att se till att du har samma lagringskonfiguration för i målregionen
Standard Managed disks | Stöd i Azure-regioner där Azure Site Recovery stöds. |  
Premium Managed disks | Stöd i Azure-regioner där Azure Site Recovery stöds. |
Lagringsutrymmen | Stöds |         
Kryptering i vila (SSE) | Stöds | SSE är standardinställningen på storage-konton.   
Azure Disk Encryption (ADE) | Stöds inte |
Frekvent Lägg till/ta bort disk | Stöds inte | Om du lägger till eller ta bort datadisk på den virtuella datorn, måste du inaktivera replikering och aktiverar replikering igen för den virtuella datorn.
Uteslut disk | Stöds inte|   Temporär disk är undantagen som standard.
Lagringsdirigering  | Stöds inte|
Skalbar filserver  | Stöds inte|
LRS | Stöds |
GRS | Stöds |
RA-GRS | Stöds |
ZRS | Stöds inte |  
Frekventa och lågfrekventa lagring | Stöds inte | Virtuella diskar stöds inte på frekventa och lågfrekventa lagring
Azure Storage-brandväggar för virtuella nätverk  | Nej | Finns inte stöd för att tillåta åtkomst till specifika Azure-nätverk på cachelagringskonton som används för att lagra replikerade data.
Storage-konton för generell användning V2 (både frekvent och lågfrekvent nivå) | Nej | Transaktionen kostnaderna ökar avsevärt jämfört med generell användning V1-lagringskonton

>[!IMPORTANT]
> Se till att du ser Virtuella disken skalbarhets- och prestandamål för [Linux](../virtual-machines/linux/disk-scalability-targets.md) eller [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuella datorer för att undvika eventuella prestandaproblem. Om du följer standardinställningarna skapar Site Recovery begärda diskar och lagringskonton baserat på käll-konfigurationen. Om du anpassar och välja egna inställningar, kontrollerar du att du följer disk-mål för skalbarhet och prestanda för dina virtuella källdatorer.

## <a name="support-for-network-configuration"></a>Stöd för nätverkskonfiguration
**Konfiguration** | **Stöds/stöds ej** | **Kommentarer**
--- | --- | ---
Nätverksgränssnitt (NIC) | Upp till maximalt antal nätverkskort som stöds av en viss Azure VM-storlek | Nätverkskort skapas när den virtuella datorn skapas som en del av redundanstest eller redundans. Antal nätverkskort på den virtuella datorn som redundansväxlingen beror på hur många nätverkskort källan virtuella datorn har vid tidpunkten för att aktivera replikering. Om du lägger till/ta bort nätverkskort efter att ha aktiverat replikering, påverkar inte antalet nätverkskort på den virtuella datorn som redundansväxlingen.
Internet-lastbalanserare | Stöds | Du måste associera förkonfigurerade belastningsutjämnaren med hjälp av en azure automationsskript i en återställningsplan.
Den interna belastningsutjämnaren | Stöds | Du måste associera förkonfigurerade belastningsutjämnaren med hjälp av en azure automationsskript i en återställningsplan.
Offentlig IP-adress| Stöds | Du måste koppla en befintlig offentlig IP-adress till nätverkskortet eller skapa en och associera med nätverkskortet med hjälp av en azure automationsskript i en återställningsplan.
NSG på nätverkskortet (Resource Manager)| Stöds | Du måste associera NSG på nätverkskortet med hjälp av en azure automationsskript i en återställningsplan.  
NSG på undernätet (Resource Manager och klassisk)| Stöds | Du måste associera NSG i undernätet med hjälp av en azure automationsskript i en återställningsplan.
NSG på VM (klassisk)| Stöds | Du måste associera NSG på nätverkskortet med hjälp av en azure automationsskript i en återställningsplan.
Reserverad IP (statisk IP-adress) / behåller käll-IP | Stöds | Om nätverkskortet på den Virtuella källdatorn har statiska IP-konfiguration och målundernätet har den samma IP som är tillgängliga, tilldelas den virtuella datorn vid redundansväxlingen. Om målundernätet inte har samma IP-Adressen tillgängliga, är en av de tillgängliga IP-adresserna i undernätet reserverad för den här virtuella datorn. Du kan ange en fast IP-Adressen för ditt val i ”replikerat objekt > Inställningar > beräkning och nätverk > nätverksgränssnitt '. Du kan markera nätverkskortet och ange undernätet och IP-Adressen för ditt val.
Dynamisk IP| Stöds | Om nätverkskortet på den Virtuella källdatorn har dynamisk IP-konfiguration, nätverkskortet på redundansen VM är också dynamiskt som standard. Du kan ange en fast IP-Adressen för ditt val i ”replikerat objekt > Inställningar > beräkning och nätverk > nätverksgränssnitt '. Du kan markera nätverkskortet och ange undernätet och IP-Adressen för ditt val.
Traffic Manager-integrering | Stöds | Du kan förkonfigurera traffic manager så att trafiken dirigeras till slutpunkten i källregionen regelbundet och till slutpunkten i målregionen vid redundans.
Azure-hanterade DNS | Stöds |
Anpassad DNS  | Stöds |    
Via oautentiserad proxyserver | Stöds | Referera till [nätverk vägledningsdokumentet.](site-recovery-azure-to-azure-networking-guidance.md)    
Autentiserad Proxy | Stöds inte | Om den virtuella datorn använder en autentiserad proxyserver för utgående anslutningar, kan inte replikeras med Azure Site Recovery.    
VPN för plats till plats med en lokal (med eller utan ExpressRoute)| Stöds | Se till att Udr och NSG: er konfigureras så att Site recovery trafiken inte dirigeras till den lokala. Referera till [nätverk vägledningsdokumentet.](site-recovery-azure-to-azure-networking-guidance.md)  
Anslutning mellan virtuella nätverk | Stöds | Referera till [nätverk vägledningsdokumentet.](site-recovery-azure-to-azure-networking-guidance.md)  
Slutpunkter för virtuellt nätverk | Stöds | Azure Storage-brandväggar för virtuella nätverk stöds inte. Finns inte stöd för att tillåta åtkomst till specifika Azure-nätverk på cachelagringskonton som används för att lagra replikerade data.
Accelererat nätverk | Stöds | Accelererat nätverk måste vara aktiverat på den Virtuella källdatorn. [Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).


## <a name="next-steps"></a>Nästa steg
- Läs mer om [nätverk vägledning för att replikera virtuella Azure-datorer](site-recovery-azure-to-azure-networking-guidance.md)
- Börja skydda dina arbetsbelastningar genom [replikering av virtuella Azure-datorer](site-recovery-azure-to-azure.md)
