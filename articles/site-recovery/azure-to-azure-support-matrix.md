---
title: Azure Site Recovery stöd matrix för replikering från Azure till Azure | Microsoft Docs
description: Sammanfattar de operativsystem som stöds och konfigurationer för Azure Site Recovery replikering av virtuella Azure-datorer (VM) från en region till en annan för disaster recovery (DR) behov.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: sujayt
ms.openlocfilehash: 19c439e1182086b91d05f8bb23bc6c07c34a12a2
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716320"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Stöd matrix för replikering från en Azure-region till en annan



Den här artikeln beskriver konfigurationer som stöds och komponenter för Azure Site Recovery när replikering och återställa virtuella Azure-datorer från en region till en annan region.

## <a name="user-interface-options"></a>Alternativ för användargränssnitt

**Användargränssnittet** |  **Stöds / stöds inte**
--- | ---
**Azure Portal** | Stöds
**Klassisk portal** | Stöds inte
**PowerShell** | [Azure Azure replikering med PowerShell](azure-to-azure-powershell.md)
**REST API** | Stöds för närvarande inte
**CLI** | Stöds för närvarande inte


## <a name="resource-move-support"></a>Resursen move-support

**Flytta resurstypen** | **Stöds / stöds inte** | **Kommentarer**  
--- | --- | ---
**Flytta valvet mellan resursgrupper** | Stöds inte |Du kan inte flytta Recovery services-ventilen över resursgrupper.
**Flytta bearbetning, lagring och nätverk mellan resursgrupper** | Stöds inte |Om du flyttar en virtuell dator (eller andra komponenter, till exempel lagring och nätverk) när du har aktiverat replikering, måste du inaktivera replikering och aktivera replikering för den virtuella datorn igen.



## <a name="support-for-deployment-models"></a>Stöd för distributionsmodeller

**Distributionsmodell** | **Stöds / stöds inte** | **Kommentarer**  
--- | --- | ---
**Klassisk** | Stöds | Du kan endast replikera en klassisk virtuell dator och återställa den som en klassisk virtuell dator. Du kan inte återställa den som en virtuell dator i Resource Manager. Om du distribuerar en klassisk virtuell dator utan ett virtuellt nätverk och direkt till en Azure-region, stöds den inte.
**Resource Manager** | Stöds |

>[!NOTE]
>
> 1. Replikera virtuella Azure-datorer från en prenumeration till en annan för för katastrofåterställning stöds inte.
> 2. Migrera Azure virtuella datorer över prenumerationer stöds inte.
> 3. Migrera Azure virtuella datorer i samma region stöds inte.
> 4. Migrera virtuella Azure-datorer från klassiska distributionsmodellen till Resource manager-distributionsmodellen inte stöds.

## <a name="support-for-replicated-machine-os-versions"></a>Stöd för replikerade datorn OS-versioner

Den nedan stöd gäller för alla arbetsbelastningar som körs på nämnda OS.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core, Server med Skrivbordsmiljö) *
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 med på minst SP1

>[!NOTE]
>
> \* Windows Server 2016 Nano Server stöds inte.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6,9, 7.0, 7.1, 7.2, 7.3,7.4
- CentOS 6.5, 6.6, 6.7, 6.8, 6,9, 7.0, 7.1, 7.2, 7.3,7.4
- Ubuntu 14.04 LTS Server [ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (kernel-versioner som stöds)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (kernel-versioner som stöds)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (kernel-versioner som stöds)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Oracle Enterprise Linux 6.4, 6.5 Red Hat kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(Uppgradering av replikera datorer från SLES 11 SP3 till SLES 11 SP4 stöds inte. Om en replikerad dator har uppgraderats från SLES 11SP3 till SLES 11 SP4, måste du inaktivera replikering och skydda datorn igen efter uppgraderingen.)

>[!NOTE]
>
> Ubuntu servrar använder lösenordsbaserad autentisering och inloggning och använder molnet init-paketet för att konfigurera molnet virtuella datorer kan ha lösenordsbaserade inloggning inaktiveras vid växling vid fel (beroende på cloudinit konfiguration.) Lösenordsbaserade inloggningen kan återaktiveras på den virtuella datorn genom att återställa lösenordet på inställningsmenyn (under stöd + felsökning avsnitt) för den över virtuell dator på Azure-portalen.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ubuntu kernel-versioner som stöds för virtuella Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | 9.13 | 3.13.0-24-Generic till 3.13.0-137-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-Generic till 3.13.0-141-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-112-generic |
14.04 LTS | 9.15 | 3.13.0-24-Generic till 3.13.0-143-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-116-generic |
14.04 LTS | 9.16 | 3.13.0-24-Generic till 3.13.0-144-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-119-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic till 4.4.0-104-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-Generic till 4.4.0-112-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-32-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1009-azure |
16.04 LTS | 9.15 | 4.4.0-21-Generic till 4.4.0-116-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-37-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1012-azure |
16.04 LTS | 9.16 | 4.4.0-21-Generic till 4.4.0-119-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-38-generic,<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1012-azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian kernel-versioner som stöds för virtuella Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
Debian 7 | 9.14, 9.15, 9.16 | 3.2.0-4-amd64 till 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14, 9.15, 9.16 | 3.16.0-4-amd64 till 3.16.0-5-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.5-amd64 |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Filsystem som stöds och Gäst lagringskonfigurationer på Azure virtuella datorer som kör Operativsystemet Linux

* Filsystem: ext3 ext4, ReiserFS (Suse Linux Enterprise Server bara), XFS
* Volymhanterare: LVM2
* Programvara för flera sökvägar: enheten Mapper

## <a name="region-support"></a>Region-support

Du kan replikera och återställa virtuella datorer mellan de två regionerna inom samma geografiska kluster.

**Geografisk kluster** | **Azure-regioner**
-- | --
Nordamerika | Kanada, Öst, Kanada Central, södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA, västra USA 2, centrala USA, norra centrala USA
Europa | Storbritannien, Väst, Storbritannien, Syd, Nordeuropa, Västeuropa, Frankrike Frankrike Central Syd
Asien | Södra Indien, centrala Indien, Sydostasien, östra Asien, östra samt västra och Korea-Central, Korea Syd
Australien   | Östra Australien, sydost
Azure Government    | USA GOV Virginia, USA GOV Iowa, USA GOV Arizona, USA GOV Texas, USA DOD Öst, DOD USA, Central
Tyskland | Tyskland Central, Tyskland nordöst
Kina | Kina Öst, Kina Nord

>[!NOTE]
>
> För södra region, kan du bara replikera och växla över till en av södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA, västra USA 2 och norra centrala USA regioner och återställas.


## <a name="support-for-compute-configuration"></a>Stöd för beräkningskonfigurationen

**Konfiguration** | **Stöds/stöds ej** | **Kommentarer**
--- | --- | ---
Storlek | Alla Virtuella Azure-datorstorleken med minst 2 CPU-kärnor och 1 GB RAM-minne | Referera till [storlekar för virtuella Azure-datorn](../virtual-machines/windows/sizes.md)
Tillgänglighetsuppsättningar | Stöds | Om du använder alternativet ”Aktivera replikering' steget i portalen är tillgänglighetsuppsättningen automatiskt skapa baserat på källan region konfiguration. Du kan ändra tillgänglighetsuppsättning för målet i ' replikerade objekt > Inställningar > beräkning och nätverk > tillgänglighetsuppsättning som helst.
Hybrid Använd förmånen (NAV) virtuella datorer | Stöds | Om den Virtuella källdatorn har hubb licens aktiverad, använder testa redundans eller Failover VM också HUB-licens.
Skalningsuppsättningar för virtuella datorer | Stöds inte |
Azure Galleriavbildningar - Microsoft publicerat | Stöds | Så länge som den virtuella datorn körs på ett operativsystem som stöds av Site Recovery som stöds
Azure-galleriet bilder - publicerade från tredje part | Stöds | Stöd för så länge som den virtuella datorn körs på ett operativsystem som stöds av Site Recovery.
Anpassad bilder - publicerade från tredje part | Stöds | Stöd för så länge som den virtuella datorn körs på ett operativsystem som stöds av Site Recovery.
Virtuella datorer migreras med hjälp av Site Recovery | Stöds | Om det är en VMware/fysiska datorn har migrerat till Azure med Site Recovery måste du avinstallera den äldre versionen av mobilitetstjänsten och starta om datorn innan du replikerar till en annan Azure-region.

## <a name="support-for-storage-configuration"></a>Stöd för konfiguration för lagring

**Konfiguration** | **Stöds/stöds ej** | **Kommentarer**
--- | --- | ---
Maximal storlek för OS-disk | 2 048 GB | Referera till [diskar som används av virtuella datorer.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Maximal datadiskstorleken | 4095 GB | Referera till [diskar som används av virtuella datorer.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Antalet datadiskar | Upp till 64 som stöds av en viss Azure VM-storlek | Referera till [storlekar för virtuella Azure-datorn](../virtual-machines/windows/sizes.md)
Diskutrymme | Alltid uteslutas från replikering | Tillfällig disklagring har exkluderats från replikering alltid. Du bör inte spärra beständiga data diskutrymme enligt vägledning för Azure. Referera till [diskutrymme på Azure Virtual Machines](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) för mer information.
Förändringstakten för data på disken | Högst 10 Mbit/s per disk för Premium-lagring och 2 Mbit/s per disk för standardlagring | Om förändringstakten genomsnittlig data på disken är utöver 10 Mbit/s (för Premium) och 2 Mbit/s (för Standard) kontinuerligt, kommer inte replikeringen att fånga upp. Om det är en databearbetning burst och förändringstakten för data som är större än 10 Mbit/s (för Premium) och 2 Mbit/s (för Standard) under en viss tid och kommer, fånga replikering upp. I det här fallet kan du se något fördröjd återställningspunkter.
Diskar på standard storage-konton | Stöds |
Diskar på premium storage-konton | Stöds | Om en virtuell dator har diskar som är fördelade på premium- och standard storage-konton, kan du välja ett annat mål storage-konto för varje disk så du får samma lagringskonfigurationen i målregionen
Hanterad standarddiskar | Stöds i Azure-regioner som Azure Site Recovery stöds. Offentliga moln stöds inte för närvarande.  |  
Hanterad premiumdiskar | Stöds i Azure-regioner som Azure Site Recovery stöds. Offentliga moln stöds inte för närvarande. |
Lagringsutrymmen | Stöds |         
Kryptering i vila (SSE) | Stöds | Du kan välja ett lagringskonto för SSE aktiverad för cache och mål för lagringskonton.     
Azure Disk Encryption (ADE) | Stöds inte |
Varm Lägg till/ta bort disken | Stöds inte | Om du lägger till eller ta bort datadisk på den virtuella datorn, måste du inaktivera replikering och aktivera replikering för den virtuella datorn igen.
Uteslut disk | Stöds inte|   Tillfällig disklagring har exkluderats som standard.
Lagringsutrymmen direkt  | Stöds inte|
Skalbar filserver  | Stöds inte|
LRS | Stöds |
GRS | Stöds |
RA-GRS | Stöds |
ZRS | Stöds inte |  
Kall och het lagring | Stöds inte | Virtuella diskar stöds inte på kall och het lagring
Azure Storage-brandväggar för virtuella nätverk  | Nej | Att tillåta åtkomst till specifika virtuella Azure-nätverk på cache storage-konton som används för att lagra replikerade data stöds inte.
Generella V2 storage-konton (både frekvent och lågfrekvent nivå) | Nej | Transaktionen kostnaderna ökar avsevärt jämfört med generella V1 storage-konton

>[!IMPORTANT]
> Se till att du upptäcker att Virtuella disken skalbarhets- och prestandamål för [Linux](../virtual-machines/linux/disk-scalability-targets.md) eller [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuella datorer för att undvika eventuella prestandaproblem. Om du följer standardinställningarna skapar Site Recovery krävs diskar och storage-konton baserat på käll-konfigurationen. Se till att du följer disk skalbarhets- och prestandamål för dina virtuella källdatorer om du anpassar och välja egna inställningar.

## <a name="support-for-network-configuration"></a>Stöd för konfigurering av nätverk
**Konfiguration** | **Stöds/stöds ej** | **Kommentarer**
--- | --- | ---
Nätverksgränssnitt (NIC) | Upp till maximalt antal nätverkskort som stöds av en viss Azure VM-storlek | Nätverkskort skapas när den virtuella datorn skapas som en del av testa redundans eller Redundansåtgärden. Antalet nätverkskort på redundans VM beror på antalet nätverkskort källan VM har vid tidpunkten för att aktivera replikering. Om du lägger till/ta bort NIC när du har aktiverat replikering, påverkar inte antalet nätverkskort på redundans VM.
Internet-belastningsutjämnare | Stöds | Du måste associera förkonfigurerade belastningsutjämnaren med hjälp av en azure automation-skript i en återställningsplan.
Interna belastningsutjämnare | Stöds | Du måste associera förkonfigurerade belastningsutjämnaren med hjälp av en azure automation-skript i en återställningsplan.
Offentlig IP-adress| Stöds | Du måste koppla en befintlig offentlig IP-adress till NIC eller skapa en och koppla till det nätverkskortet med hjälp av en azure automation-skript i en återställningsplan.
NSG på nätverkskortet (Resource Manager)| Stöds | Du måste koppla NSG till nätverkskort med en azure automatiseringsskriptet i en återställningsplan.  
NSG för undernätet (Resource Manager och klassisk)| Stöds | Du måste koppla NSG till undernätet med hjälp av en azure automation-skript i en återställningsplan.
NSG för den virtuella datorn (klassisk)| Stöds | Du måste koppla NSG till nätverkskort med en azure automatiseringsskriptet i en återställningsplan.
Reserverade IP: N (statisk IP) / behålla käll-IP | Stöds | Om nätverkskortet på den Virtuella källdatorn har en statisk IP-konfiguration och mål-undernät har samma IP-Adressen tillgänglig, tilldelas redundans VM. Om mål-undernätet inte har samma IP-Adressen finns är en tillgänglig IP-adresser i undernätet reserverad för den här virtuella datorn. Du kan ange en fast IP-adress för valfritt ' replikerade objekt > Inställningar > beräkning och nätverk > nätverksgränssnitt '. Du kan markera nätverkskortet och ange undernät och IP-önskat.
Dynamisk IP| Stöds | Om nätverkskortet på den Virtuella källdatorn har dynamisk IP-konfiguration, nätverkskortet på failover VM är också dynamisk som standard. Du kan ange en fast IP-adress för valfritt ' replikerade objekt > Inställningar > beräkning och nätverk > nätverksgränssnitt '. Du kan markera nätverkskortet och ange undernät och IP-önskat.
Traffic Manager-integrering | Stöds | Du konfigurera din traffic manager så att trafiken dirigeras till slutpunkten i källan region regelbundet och slutpunkten i målregionen vid redundans.
Azure hanterade DNS | Stöds |
Anpassad DNS  | Stöds |    
Oautentiserad Proxy | Stöds | Referera till [nätverk riktlinjerna.](site-recovery-azure-to-azure-networking-guidance.md)    
Autentiserad proxyserver | Stöds inte | Om den virtuella datorn använder en autentiserad proxyserver för utgående anslutningar, kan inte replikeras med hjälp av Azure Site Recovery.    
Plats-till-plats-VPN med lokalt (med eller utan ExpressRoute)| Stöds | Se till att udr: er och NSG: er har konfigurerats så att Site recovery trafik inte dirigeras till lokalt. Referera till [nätverk riktlinjerna.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuella nätverk på VNET-anslutning | Stöds | Referera till [nätverk riktlinjerna.](site-recovery-azure-to-azure-networking-guidance.md)  
Slutpunkter för virtuellt nätverk | Stöds | Azure Storage-brandväggar för virtuella nätverk stöds inte. Att tillåta åtkomst till specifika virtuella Azure-nätverk på cache storage-konton som används för att lagra replikerade data stöds inte.
Accelererat nätverk | Stöds inte | En virtuell dator med snabbare nätverk aktiverat kan replikeras, men redundans VM inte snabbare nätverk aktiverad. Snabbare nätverksfunktioner inaktiveras även för Virtuella källdatorn för återställning efter fel.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [nätverk vägledning för att replikera virtuella Azure-datorer](site-recovery-azure-to-azure-networking-guidance.md)
- Börja skydda dina arbetsbelastningar av [replikering av virtuella Azure-datorer](site-recovery-azure-to-azure.md)
