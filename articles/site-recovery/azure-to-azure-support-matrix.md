---
title: Stödmatris för azure vm-haveriberedskap med Azure Site Recovery
description: Sammanfattar stöd för Azure VMs-haveriberedskap till en sekundär region med Azure Site Recovery.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 0b4a654093e0842e66e1f8b0924edfa6c9c215e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276656"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Stödmatris för azure vm-haveriberedskap mellan Azure-regioner

Den här artikeln sammanfattar support och förutsättningar för haveriberedskap för virtuella Azure-datorer från en Azure-region till en annan med hjälp av [Azure Site Recovery-tjänsten.](site-recovery-overview.md)


## <a name="deployment-method-support"></a>Stöd för distributionsmetod

**Distribution** |  **Support**
--- | ---
**Azure-portal** | Stöds.
**Powershell** | Stöds. [Läs mer](azure-to-azure-powershell.md)
**REST API** | Stöds.
**CLI** | Stöds för närvarande inte


## <a name="resource-support"></a>Resursstöd

**Resursåtgärd** | **Detaljer**
--- | ---
**Flytta valv mellan resursgrupper** | Stöds inte
**Flytta beräknings-/lagrings-/nätverksresurser mellan resursgrupper** | Stöds inte.<br/><br/> Om du flyttar en virtuell dator eller associerade komponenter som lagring/nätverk efter att den virtuella datorn har replikerats måste du inaktivera och sedan återaktivera replikering för den virtuella datorn.
**Replikera virtuella Azure-datorer från en prenumeration till en annan för haveriberedskap** | Stöds inom samma Azure Active Directory-klient.
**Migrera virtuella datorer mellan regioner inom geografiska kluster som stöds (inom och mellan prenumerationer)** | Stöds inom samma Azure Active Directory-klient.
**Migrera virtuella datorer inom samma region** | Stöds inte.

## <a name="region-support"></a>Stöd för regionen

Du kan replikera och återställa virtuella datorer mellan två regioner inom samma geografiska kluster. Geografiska kluster definieras med datafördröjning och suveränitet i åtanke.


**Geografiskt kluster** | **Azure-regioner**
-- | --
Amerika | Östra Kanada, Kanada Central, Södra centrala USA, Västra centrala USA, Östra USA, Östra USA 2, Västra USA, Västra USA 2, CENTRALA USA, Norra centrala USA
Europa | Storbritannien Väst, Storbritannien Syd, Nordeuropa, Västeuropa, Frankrike Central, Frankrike Syd, Sydafrika Väst, Sydafrika Nord, Norge Öst, Norge Väst
Asien | Södra Indien, Centrala Indien, Västra Indien, Sydostasien, Östasien, Östra Japan, Japan Väst, Korea Central, Korea Syd, UAE Central, UAE North
Australien    | Östra Australien, Sydöstra Australien, Centrala Australien, Centrala Australien 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD Öst, US DOD Central
Tyskland    | Tyskland Central, Tyskland Nordost
Kina | Kina Öst, Kina Nord, Kina North2, Kina East2
Begränsade regioner reserverade för katastrofåterställning i landet |Tyskland Norr reserverad för Tyskland Västcentralen, Schweiz Väst reserverad för Schweiz North, Frankrike Syd reserverad för Frankrike Centrala kunder

>[!NOTE]
>
> - För **South i Brasilien**kan du replikera och växla över till dessa regioner: Södra centrala USA, Västra centrala USA, Östra USA, Östra USA 2, Västra USA, Västra USA 2 och Norra centrala USA.
> - Södra Brasilien kan endast användas som en källregion från vilken virtuella datorer kan replikeras med site recovery. Det kan inte fungera som en målregion. Detta beror på latensproblem på grund av geografiska avstånd. Observera att om du växlar över från Södra Brasilien som källregion till ett mål stöds återställning i Södra Brasilien från målregionen.
> - Du kan arbeta inom regioner som du har rätt åtkomst till.
> - Om den region där du vill skapa ett valv inte visas kontrollerar du att prenumerationen har åtkomst för att skapa resurser i den regionen.
> - Om du inte kan se en region i ett geografiskt kluster när du aktiverar replikering kontrollerar du att din prenumeration har behörighet att skapa virtuella datorer i den regionen.



## <a name="cache-storage"></a>Cachelagring

Den här tabellen sammanfattar stöd för cachelagringskontot som används av Site Recovery under replikering.

**Inställning** | **Support** | **Detaljer**
--- | --- | ---
V2-lagringskonton för allmänt ändamål (hot- och cool-nivå) | Stöds | Användning av GPv2 rekommenderas inte eftersom transaktionskostnaderna för V2 är betydligt högre än V1-lagringskonton.
Premium Storage | Stöds inte | Standardlagringskonton används för cachelagring för att optimera kostnaderna.
Azure Storage-brandväggar för virtuella nätverk  | Stöds | Om du använder brandväggsaktiverat cachelagringskonto eller mållagringskonto kontrollerar du [att du tillåter betrodda Microsoft-tjänster](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Se också till att du tillåter åtkomst till minst ett undernät till käll-Vnet.


## <a name="replicated-machine-operating-systems"></a>Operativsystem för replikerade datorer

Site Recovery stöder replikering av virtuella Azure-datorer som kör de operativsystem som anges i det här avsnittet. Observera att om en redan replikerande dator senare uppgraderas (eller nedgraderas) till en annan huvudkärna, måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.

### <a name="windows"></a>Windows


**Operativsystem** | **Detaljer**
--- | ---
Windows Server 2019 | Stöds för Server Core, Server med skrivbordsupplevelse.
Windows Server 2016  | Server Core, Server med skrivbordsupplevelse som stöds.
Windows Server 2012 R2 | Stöds.
Windows Server 2012 | Stöds.
Windows Server 2008 R2 med SP1/SP2 | Stöds.<br/><br/> Från version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av tillägg till mobilitetstjänsten för virtuella Azure-datorer måste du installera en [SSU-uppdatering (Windows Service stack update)](https://support.microsoft.com/help/4490628) och [SHA-2](https://support.microsoft.com/help/4474419) på datorer som kör Windows Server 2008 R2 SP1/SP2.  SHA-1 stöds inte från september 2019, och om SHA-2-kodsignering inte är aktiverat installeras/uppgraderas inte agenttillägget som förväntat. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).
Windows 10 (x64) | Stöds.
Windows 8.1 (x64) | Stöds.
Windows 8 (x64) | Stöds.
Windows 7 (x64) med SP1 och framåt | Från version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av tillägget Mobilitetstjänst för virtuella Azure-datorer måste du installera en [SSU-uppdatering (Windows Service Stack Update)](https://support.microsoft.com/help/4490628) och [SHA-2](https://support.microsoft.com/help/4474419) på datorer som kör Windows 7 med SP1.  SHA-1 stöds inte från september 2019, och om SHA-2-kodsignering inte är aktiverat installeras/uppgraderar inte agenttillägget som förväntat.. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).



#### <a name="linux"></a>Linux

**Operativsystem** | **Detaljer**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8.0,](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS-server | [Kärnversioner som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16,04 LTS-server | [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och inloggning, och cloud-init-paketet för att konfigurera virtuella datorer i molnet, kan ha lösenordsbaserad inloggning inaktiverad vid redundans (beroende på cloudinit-konfigurationen). Lösenordsbaserad inloggning kan återaktiveras på den virtuella datorn genom att återställa lösenordet från menyn Support > Felsökning > Inställningar (av den misslyckade över den virtuella datorn i Azure-portalen.
Ubuntu 18,04 LTS-server | [Kernel-version som stöds](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Kärnversioner som stöds](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Kärnversioner som stöds](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Kärnversioner som stöds)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 och 15 SP1. [(Kärnversioner som stöds)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3 (på andra)<br/><br/> Det går inte att uppgradera replikeringsmaskiner från SP3 till SP4. Om en replikerad dator har uppgraderats måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.
SUSE Linux Enterprise Server 11 | SP4 (på andra)
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Köra Den Red Hat-kompatibla kärnan eller Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5)


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ubuntu-kärnversioner som stöds för virtuella Azure-datorer

**Frisläpp** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-generisk till 4.4.0-171-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-74-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-generisk till 4.4.0-170-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-72-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generisk till 4.4.0-166-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-66-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-generisk till 4.4.0-164-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-64-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20-generisk till 4.15.0-74-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-37-generisk </br> 5.3.0-19-generisk till 5.3.0-24-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1028-azure </br> 5.3.0-1007-azure till 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20-generisk till 4.15.0-72-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-37-generisk </br> 5.3.0-19-generisk till 5.3.0-24-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1025-azure </br> 5.3.0-1007-azurblå|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generisk till 4.15.0-66-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-32-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generisk till 4.15.0-64-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-29-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debiankärnversioner som stöds för virtuella Azure-datorer

**Frisläpp** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 till 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 till 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 12-kärnversioner som stöds för virtuella Azure-datorer

**Frisläpp** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | 9.32 | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | 9.31 | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | 9.30 | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | 9.29 | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 15-kärnversioner som stöds för virtuella Azure-datorer

**Frisläpp** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 och 15 SP1 | 9.32 | Alla [SUSE 15- och 15-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) stöds.</br></br> 4.12.14-5.5-azure till 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikerade maskiner - Linux filsystem/gästlagring

* Filsystem: ext3, ext4, XFS, BTRFS
* Volymhanterare: LVM2
* Multipath programvara: Device Mapper


## <a name="replicated-machines---compute-settings"></a>Replikerade datorer - beräkningsinställningar

**Inställning** | **Support** | **Detaljer**
--- | --- | ---
Storlek | Alla Azure VM-storlekar med minst 2 CPU-kärnor och 1 GB RAM | Verifiera [Azure-storlekar för virtuella datorer](../virtual-machines/windows/sizes.md).
Tillgänglighetsuppsättningar | Stöds | Om du aktiverar replikering för en Virtuell Azure-dator med standardalternativen skapas en tillgänglighetsuppsättning automatiskt, baserat på källregioninställningarna. Du kan ändra dessa inställningar.
Tillgänglighetszoner | Stöds |
Förmån för hybridanvändning (HUB) | Stöds | Om käll-VM har en HUB-licens aktiverad, använder en test redundans eller misslyckades över den virtuella datorn också HUB-licensen.
Skalningsuppsättningar för virtuella datorer | Stöds inte |
Azure-galleriavbildningar – Microsoft publicerade | Stöds | Stöds om den virtuella datorn körs på ett operativsystem som stöds.
Azure Gallery-avbildningar – Tredje part publicerad | Stöds | Stöds om den virtuella datorn körs på ett operativsystem som stöds.
Anpassade bilder - Tredje part publicerad | Stöds | Stöds om den virtuella datorn körs på ett operativsystem som stöds.
Virtuella datorer migrerade med platsåterställning | Stöds | Om en virtuell dator eller en fysisk dator i VMware migrerades till Azure med site recovery måste du avinstallera den äldre versionen av Mobilitetstjänsten som körs på datorn och starta om datorn innan du replikerar den till en annan Azure-region.
RBAC-principer | Stöds inte | Rbac-principer (Role based Access Control) på virtuella datorer replikeras inte till den virtuella redundansdatorn i målregionen.
Tillägg | Stöds inte | Tillägg replikeras inte till den virtuella redundansdatorn i målregionen. Den måste installeras manuellt efter redundans.

## <a name="replicated-machines---disk-actions"></a>Replikerade datorer - diskåtgärder

**Åtgärd** | **Detaljer**
-- | ---
Ändra storlek på disk på replikerad virtuell dator | Stöds på källdatorns virtuella dator före redundans. Du behöver inte inaktivera/återaktivera replikering.<br/><br/> Om du ändrar källdatorns virtuella dator efter redundans hämtas inte ändringarna.<br/><br/> Om du ändrar diskstorleken på Den virtuella Azure-datorn efter redundansen hämtas inte ändringarna av Site Recovery och återställningen av återställningen av återställningen av återställningen kommer att vara den ursprungliga vm-storleken.
Lägga till en disk i en replikerad virtuell dator | Stöds

## <a name="replicated-machines---storage"></a>Replikerade maskiner - lagring

Den här tabellen sammanfattade stöd för Azure VM OS-disk, datadisk och tillfällig disk.

- Det är viktigt att observera vm-diskgränserna och målen för [virtuella Linux-](../virtual-machines/linux/disk-scalability-targets.md) och [Windows-datorer](../virtual-machines/windows/disk-scalability-targets.md) för att undvika prestandaproblem.
- Om du distribuerar med standardinställningarna skapar Site Recovery automatiskt diskar och lagringskonton baserat på källinställningarna.
- Om du anpassar dig måste du se till att du följer riktlinjerna.

**Komponent** | **Support** | **Detaljer**
--- | --- | ---
Högsta storlek på OS-disk | 2048 GB | [Läs mer](../virtual-machines/windows/managed-disks-overview.md) om VM-diskar.
Tillfällig disk | Stöds inte | Den tillfälliga disken är alltid utesluten från replikering.<br/><br/> Lagra inga beständiga data på den temporära disken. [Läs mer](../virtual-machines/windows/managed-disks-overview.md).
Maximal storlek på datadisk | 8192 GB för hanterade diskar<br></br>4095 GB för ohanterade diskar|
Minsta storlek på datadisk | Ingen begränsning för ohanterat diskar. 2 GB för hanterade diskar |
Högsta antal datadiskar | Upp till 64, i enlighet med stöd för en specifik Azure VM-storlek | [Läs mer](../virtual-machines/windows/sizes.md) om VM-storlekar.
Ändringshastighet för datadisk | Maximalt 10 MBps per disk för premiumlagring. Maximalt 2 MBps per disk för standardlagring. | Om den genomsnittliga dataförändringshastigheten på disken är kontinuerligt högre än den maximala, kommer replikeringen inte att komma ikapp.<br/><br/>  Men om det maximala överskrids sporadiskt kan replikering komma ikapp, men du kan se något försenade återställningspunkter.
Datadisk - standardlagringskonto | Stöds |
Datadisk - premiumlagringskonto | Stöds | Om en virtuell dator har diskar spridda över premium- och standardlagringskonton kan du välja ett annat mållagringskonto för varje disk, för att säkerställa att du har samma lagringskonfiguration i målregionen.
Hanterad disk - standard | Stöds i Azure-regioner där Azure Site Recovery stöds. |
Hanterad disk - premium | Stöds i Azure-regioner där Azure Site Recovery stöds. |
Standard SSD | Stöds |
Redundans | LRS och GRS stöds.<br/><br/> ZRS stöds inte.
Sval och varm förvaring | Stöds inte | VM-diskar stöds inte vid lågfrekvent och frekvent lagring
Lagringsutrymmen | Stöds |
Kryptering i vila (SSE) | Stöds | SSE är standardinställningen för lagringskonton.
Kryptering i vila (CMK) | Stöds | Både programvaru- och HSM-nycklar stöds för hanterade diskar
Azure Disk Encryption (ADE) för Windows OS | Stöds för virtuella datorer med hanterade diskar. | Virtuella datorer med ohanterade diskar stöds inte. <br/><br/> HSM-skyddade nycklar stöds inte. |
Azure Disk Encryption (ADE) för Linux OS | Stöds för virtuella datorer med hanterade diskar. | Virtuella datorer med ohanterade diskar stöds inte. <br/><br/> HSM-skyddade nycklar stöds inte. |
Hot lägg till    | Stöds | Aktivera replikering för en datadisk som du lägger till i en replikerad Azure VM stöds för virtuella datorer som använder hanterade diskar.
Ta bort disk    | Stöds inte | Om du tar bort datadisken på den virtuella datorn måste du inaktivera replikering och aktivera replikering igen för den virtuella datorn.
Uteslut disk | Stöd. Du måste använda [PowerShell](azure-to-azure-exclude-disks.md) för att konfigurera. |    Tillfälliga diskar är undantagna som standard.
Lagringsutrymmen direkt  | Stöds för krasch konsekvent återställningspunkter. Programkonsekventa återställningspunkter stöds inte. |
Utskalningsfilserver  | Stöds för krasch konsekvent återställningspunkter. Programkonsekventa återställningspunkter stöds inte. |
Drbd | Diskar som ingår i en DRBD-konfiguration stöds inte. |
LRS | Stöds |
GRS | Stöds |
RA-GRS | Stöds |
ZRS | Stöds inte |
Cool och hot storage | Stöds inte | Virtuella datordiskar stöds inte vid lågkylning och frekvent lagring
Azure Storage-brandväggar för virtuella nätverk  | Stöds | Om du begränsar den virtuella nätverksåtkomsten till lagringskonton aktiverar du [Tillåt betrodda Microsoft-tjänster](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
V2-lagringskonton för allmänt ändamål (både hot- och coolnivå) | Stöds | Transaktionskostnaderna ökar avsevärt jämfört med V1-lagringskonton för allmänt ändamål
Generation 2 (UEFI-start) | Stöds

>[!IMPORTANT]
> För att undvika prestandaproblem, se till att du följer VM disk skalbarhet och prestanda mål för [Linux](../virtual-machines/linux/disk-scalability-targets.md) eller [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuella datorer. Om du använder standardinställningarna skapar Site Recovery de diskar och lagringskonton som krävs, baserat på källkonfigurationen. Om du anpassar och väljer dina egna inställningar följer du diskskalbarhets- och prestandamålen för käll-virtuella datorer.

## <a name="limits-and-data-change-rates"></a>Gränser och dataändringshastigheter

I följande tabell sammanfattas begränsningar för webbplatsåterställning.

- Dessa gränser är baserade på våra tester, men uppenbarligen inte täcker alla möjliga ansökan I / O-kombinationer.
- Faktiska resultat kan variera beroende på din app I/O mix.
- Det finns två gränser att tänka på, per diskdataomsättning och per virtuell datordataomsättning.

**Lagringsmål** | **Genomsnittlig källdisk I/O** |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total omsättning av källdiskdata per dag**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

## <a name="replicated-machines---networking"></a>Replikerade datorer - nätverk
**Inställning** | **Support** | **Detaljer**
--- | --- | ---
NIC | Högsta antal som stöds för en viss Azure VM-storlek | Nätverkskort skapas när den virtuella datorn skapas under redundans.<br/><br/> Antalet nätverkskort på den virtuella redundansdatorn beror på antalet nätverkskort på källdatorns virtuella datorer när replikeringen aktiverades. Om du lägger till eller tar bort ett nätverkskort efter att replikering har aktiverats påverkar det inte antalet nätverkskort på den replikerade virtuella datorn efter redundans. Observera också att ordningen på nätverkskort efter redundans inte garanteras vara densamma som den ursprungliga ordningen.
Internet-lastbalanserare | Stöds | Associera den förkonfigurerade belastningsutjämnaren med ett Azure Automation-skript i en återställningsplan.
Intern belastningsutjämnare | Stöds | Associera den förkonfigurerade belastningsutjämnaren med ett Azure Automation-skript i en återställningsplan.
Offentlig IP-adress | Stöds | Associera en befintlig offentlig IP-adress med nätverkskortet. Du kan också skapa en offentlig IP-adress och associera den med nätverkskortet med ett Azure Automation-skript i en återställningsplan.
NSG på nätverkskortet | Stöds | Associera NSG med nätverkskortet med ett Azure Automation-skript i en återställningsplan.
NSG på undernät | Stöds | Associera NSG med undernätet med ett Azure Automation-skript i en återställningsplan.
Reserverad (statisk) IP-adress | Stöds | Om nätverkskortet på källdatorn har en statisk IP-adress och målundernätet har samma IP-adress tillgänglig, tilldelas det till den misslyckade över den virtuella datorn.<br/><br/> Om målundernätet inte har samma IP-adress tillgänglig är en av de tillgängliga IP-adresserna i undernätet reserverad för den virtuella datorn.<br/><br/> Du kan också ange en fast IP-adress och ett undernät i gränssnitt**för** > inställningar för **replikerade objekt** > **Inställningar Beräkning och Nätverksnätverk** > **Network interfaces**.
Dynamisk IP-adress | Stöds | Om nätverkskortet på källan har dynamisk IP-adressering är nätverkskortet på den misslyckade över den virtuella datorn också dynamisk som standard.<br/><br/> Du kan ändra detta till en fast IP-adress om det behövs.
Flera IP-adresser | Stöds inte | När du växlar över en virtuell dator som har ett nätverkskort med flera IP-adresser behålls endast den primära IP-adressen för nätverkskortet i källregionen. Om du vill tilldela flera IP-adresser kan du lägga till virtuella datorer i en [återställningsplan](recovery-plan-overview.md) och bifoga ett skript för att tilldela ytterligare IP-adresser till planen, eller så kan du göra ändringen manuellt eller med ett skript efter redundans.
Traffic Manager     | Stöds | Du kan konfigurera Traffic Manager så att trafiken dirigeras till slutpunkten i källregionen regelbundet och till slutpunkten i målregionen vid redundans.
Azure DNS | Stöds |
Anpassad DNS    | Stöds |
Oautentiserade proxy | Stöds | [Läs mer](site-recovery-azure-to-azure-networking-guidance.md)
Autentiserat ombud | Stöds inte | Om den virtuella datorn använder en autentrad proxy för utgående anslutning kan den inte replikeras med Azure Site Recovery.
VPN-anslutning från plats till plats till lokalt<br/><br/>(med eller utan ExpressRoute)| Stöds | Se till att UDRs och NSG:er är konfigurerade på ett sådant sätt att platsens återställningstrafik inte dirigeras till lokala enheter. [Läs mer](site-recovery-azure-to-azure-networking-guidance.md)
VNET till VNET-anslutning    | Stöds | [Läs mer](site-recovery-azure-to-azure-networking-guidance.md)
Tjänstslutpunkter för virtuellt nätverk | Stöds | Om du begränsar den virtuella nätverksåtkomsten till lagringskonton kontrollerar du att de betrodda Microsoft-tjänsterna får åtkomst till lagringskontot.
Snabbare nätverk | Stöds | Accelererade nätverk måste aktiveras på käll-VM. [Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Nästa steg
- Läs [nätverksvägledning](site-recovery-azure-to-azure-networking-guidance.md) för replikering av virtuella Azure-datorer.
- Distribuera haveriberedskap genom [att replikera virtuella Azure-datorer](site-recovery-azure-to-azure.md).
