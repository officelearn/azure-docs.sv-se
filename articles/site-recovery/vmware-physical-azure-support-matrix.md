---
title: Stödmatris för VMware/fysisk haveriberedskap i Azure Site Recovery
description: Sammanfattar stöd för haveriberedskap av virtuella datorer med VMware och fysisk server till Azure med Hjälp av Azure Site Recovery.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 2/24/2020
ms.author: raynew
ms.openlocfilehash: 05e60c5b008746bbfd72dbe7a2e14b18aa563671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371400"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Stödmatris för haveriberedskap av virtuella datorer och fysiska servrar till Azure

Den här artikeln sammanfattar komponenter och inställningar som stöds för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure med [Azure Site Recovery](site-recovery-overview.md).

- [Läs mer](vmware-azure-architecture.md) om arkitektur för virtuell dator-/fysisk serverkatastrofer för VMware-dator/fysisk server.
- Följ våra [tutorials](tutorial-prepare-azure.md) för att prova katastrofåterställning.

## <a name="deployment-scenarios"></a>Distributionsscenarier

**Scenario** | **Detaljer**
--- | ---
Katastrofåterställning av virtuella datorer med VMware | Replikering av lokala virtuella datorer med VMware till Azure. Du kan distribuera det här scenariot i Azure-portalen eller med hjälp av [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Haveriberedskap av fysiska servrar | Replikering av lokala Windows/Linux fysiska servrar till Azure. Du kan distribuera det här scenariot i Azure-portalen.

## <a name="on-premises-virtualization-servers"></a>Lokala virtualiseringsservrar

**Server** | **Krav** | **Detaljer**
--- | --- | ---
vCenter Server | Version 6.7, 6.5, 6.0 eller 5.5 | Vi rekommenderar att du använder en vCenter-server i distributionen av haveriberedskap.
vSphere värdar | Version 6.7, 6.5, 6.0 eller 5.5 | Vi rekommenderar att vSphere-värdar och vCenter-servrar finns i samma nätverk som processservern. Som standard körs processservern på konfigurationsservern. [Läs mer](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Konfigurationsserver för platsåterställning

Konfigurationsservern är en lokal dator som kör Site Recovery-komponenter, inklusive konfigurationsservern, processservern och huvudmålservern.

- För virtuella datorer med VMware anger du konfigurationsservern genom att hämta en OVF-mall för att skapa en virtuell virtuell VMware-dator.
- För fysiska servrar konfigurerar du konfigurationsservermaskinen manuellt.

**Komponent** | **Krav**
--- |---
Processorkärnor | 8
RAM | 16 GB
Antal diskar | 3 diskar<br/><br/> Diskar inkluderar OS-disken, processservercachedisken och kvarhållningsenheten för återställning av återställning efter fel.
Ledigt diskutrymme | 600 GB utrymme för processservercachen.
Ledigt diskutrymme | 600 GB utrymme för kvarhållningsenheten.
Operativsystem  | Windows Server 2012 R2 eller Windows Server 2016 med skrivbordsupplevelse <br/><br> Om du planerar att använda den här apparatens inbyggda huvudmål för återställning efter fel, se till att OS-versionen är samma eller högre än de replikerade objekten.|
Nationella inställningar för operativsystem | Engelska (en-us)
[PowerCLI (på nytt)](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Behövs inte för konfigurationsserver version [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) eller senare. 
Windows Server-roller | Aktivera inte Active Directory Domain Services. Internet Information Services (IIS) eller Hyper-V. 
Grupprinciper| - Förhindra åtkomst till kommandotolken. <br/> - Förhindra åtkomst till registerredigeringsverktyg. <br/> - Förtroendelogik för bifogade filer. <br/> - Aktivera skriptkörning. <br/> - [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Se till att du:<br/><br/> - Har inte en befintlig standard webbplats <br/> - Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Aktivera [FastCGI-inställning](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - Har inte befintlig webbplats / app lyssnar på port 443<br/>
Typ av nätverkskort | VMXNET3 (när det distribueras som en virtuell virtuell VMware-dator)
IP-adresstyp | Statisk
Portar | 443 används för kontrollkanalorkestrering<br/>9443 för datatransport

## <a name="replicated-machines"></a>Replikerade datorer

Site Recovery stöder replikering av alla arbetsbelastningar som körs på en dator som stöds.

> [!Note]
> I följande tabell visas stöd för datorer med BIOS-start. Se [avsnittet Lagring](#storage) för support på UEFI-baserade maskiner.

**Komponent** | **Detaljer**
--- | ---
Maskininställningar | Datorer som replikeras till Azure måste uppfylla [Azure-krav](#azure-vm-requirements).
Arbetsbelastning för datorer | Site Recovery stöder replikering av alla arbetsbelastningar som körs på en dator som stöds. [Läs mer](https://aka.ms/asr_workload).
Windows Server 2019 | Stöds från [uppdaterings samlade 34](https://support.microsoft.com/help/4490016) (version 9.22 av mobilitetstjänsten) och framåt.
Windows Server 2016 64-bitars | Stöds för Server Core, Server med skrivbordsupplevelse.
Windows Server 2012 R2 / Windows Server 2012 | Stöds.
Windows Server 2008 R2 med SP1 och framåt. | Stöds.<br/><br/> Från version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av Mobility-serviceagenten behöver du [underhålla stackuppdatering (SSU)](https://support.microsoft.com/help/4490628) och [SHA-2-uppdatering](https://support.microsoft.com/help/4474419) installerad på datorer som kör Windows 2008 R2 med SP1 eller senare. SHA-1 stöds inte från september 2019, och om SHA-2-kodsignering inte är aktiverat installeras/uppgraderas inte agenttillägget som förväntat. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).
Windows Server 2008 med SP2 eller senare (64-bitars/32-bitars) |  Stöds endast för migrering. [Läs mer](migrate-tutorial-windows-server-2008.md).<br/><br/> Från version [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av Mobility-serviceagenten behöver du [underhålla stackuppdatering (SSU)](https://support.microsoft.com/help/4493730) och [SHA-2-uppdatering](hhttps://support.microsoft.com/help/4474419) installerad på Windows 2008 SP2-datorer. ISHA-1 stöds inte från september 2019, och om SHA-2-kodsignering inte är aktiverat installeras/uppgraderas inte agenttillägget som förväntat. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).
Windows 10, Windows 8.1, Windows 8 | Stöds.
Windows 7 med SP1 64-bitars | Stöds från [samlad uppdatering 36](https://support.microsoft.com/help/4503156) (version 9.22 av mobilitetstjänsten) och framåt. </br></br> Från [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) av Mobility-serviceagenten behöver du [underhålla stackuppdatering (SSU)](https://support.microsoft.com/help/4490628) och [SHA-2-uppdatering](https://support.microsoft.com/help/4474419) installerad på Windows 7 SP1-datorer.  SHA-1 stöds inte från september 2019, och om SHA-2-kodsignering inte är aktiverat installeras/uppgraderas inte agenttillägget som förväntat. Läs mer om [SHA-2-uppgradering och krav](https://aka.ms/SHA-2KB).
Linux | Endast 64-bitarssystem stöds. 32-bitars system stöds inte.<br/><br/>Varje Linux-server bör ha [LINUX Integration Services (LIS) komponenter](https://www.microsoft.com/download/details.aspx?id=55106) installerade. Det krävs för att starta servern i Azure efter test redundans/redundans. Om LIS-komponenter saknas, se till att installera [komponenterna](https://www.microsoft.com/download/details.aspx?id=55106) innan replikering för datorerna att starta i Azure. <br/><br/> Site Recovery orkestrerar redundans för att köra Linux-servrar i Azure. Linux-leverantörer kan dock begränsa stödet till endast distributionsversioner som inte har nått uttjänt.<br/><br/> På Linux-distributioner stöds endast de lagerkärnor som ingår i distributionsdelversionens version/uppdatering.<br/><br/> Det går inte att uppgradera skyddade datorer i större Linux-distributionsversioner. Om du vill uppgradera inaktivera, inaktivera replikering, uppgradera operativsystemet och sedan aktivera replikering igen.<br/><br/> [Läs mer](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) om stöd för Linux och teknik med öppen källkod i Azure.
Linux Red Hat Företag | 5,2 till 5,11</b><br/> 6,1 till 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1 <br/> Servrar som kör Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 har inte [linuxintegrationskomponenter (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) förinstallerade. Se till att installera [komponenterna](https://www.microsoft.com/download/details.aspx?id=55106) innan replikering aktiveras för datorerna att starta i Azure.
Linux: CentOS | 5,2 till 5,11</b><br/> 6,1 till 6,10</b><br/> 7,0 till 7,6<br/> <br/> 8,0 till 8,1<br/><br/> Servrar som kör CentOS 5.2-5.11 & 6.1-6.10 har inte [linuxintegrationskomponenter (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) förinstallerade. Se till att installera [komponenterna](https://www.microsoft.com/download/details.aspx?id=55106) innan replikering aktiveras för datorerna att starta i Azure.
Ubuntu | Ubuntu 14.04 LTS-server [(granska kärnversioner som stöds)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS-server [(granska kärnversioner som stöds)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS-server [(granska kärnversioner som stöds)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(granska kärnversioner som stöds)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(granska kärnversioner som stöds)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(granska kärnversioner som stöds)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Det går inte att uppgradera replikerade datorer från SUSE Linux Enterprise Server 11 SP3 till SP4. Om du vill uppgradera inaktiverar du replikering och aktiverar igen efter uppgraderingen.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Köra Den Red Hat-kompatibla kärnan eller Unbreakable Enterprise Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) 

> [!Note]
> För var och en av Windows-versionerna stöder Azure Site Recovery endast [LTSC-versioner (Long-Term Service Channel).](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc)  [Halvårsvisa](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) kanalutgåvor stöds för närvarande inte just nu.

### <a name="ubuntu-kernel-versions"></a>Ubuntu-kärnversioner

**Release som stöds** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-generisk till 3.13.0-170-generisk,<br/>3.16.0-25-generisk till 3.16.0-77-generisk,<br/>3.19.0-18-generisk till 3.19.0-80-generisk,<br/>4.2.0-18-generisk till 4.2.0-42-generisk,<br/>4.4.0-21-generisk till 4.4.0-148-generisk,<br/>4.15.0-1023-azure till 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-generisk till 4.4.0-171-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-74-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-generisk till 4.4.0-170-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-72-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-generisk till 4.4.0-166-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-66-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-generisk till 4.4.0-164-generisk,<br/>4.8.0-34-generisk till 4.8.0-58-generisk,<br/>4.10.0-14-generisk till 4.10.0-42-generisk,<br/>4.11.0-13-generisk till 4.11.0-14-generisk,<br/>4.13.0-16-generisk till 4.13.0-45-generisk,<br/>4.15.0-13-generisk till 4.15.0-64-generisk<br/>4.11.0-1009-azure till 4.11.0-1016-azure,<br/>4.13.0-1005-azure till 4.13.0-1018-azure <br/>4.15.0-1012-azure till 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generisk till 4.15.0-74-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-37-generisk </br> 5.3.0-19-generisk till 5.3.0-24-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1028-azure </br> 5.3.0-1007-azure till 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-generisk till 4.15.0-72-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-37-generisk </br> 5.3.0-19-generisk till 5.3.0-24-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1025-azure </br> 5.3.0-1007-azurblå|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-generisk till 4.15.0-66-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-32-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-generisk till 4.15.0-62-generisk </br> 4.18.0-13-generisk till 4.18.0-25-generisk </br> 5.0.0-15-generisk till 5.0.0-27-generisk </br> 4.15.0-1009-azure till 4.15.0-1037-azure </br> 4.18.0-1006-azure till 4.18.0-1025-azure </br> 5.0.0-1012-azure till 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Debiankärnversioner


**Release som stöds** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
Debian 7 | [9.29,][9.29 UR] [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR]| 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR] | 3.16.0-4-amd64 till 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 till 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12-filversioner som stöds

**Frisläpp** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | [9.32][9.32 UR] | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | [9.31][9.31 UR] | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | [9.30][9.30 UR] | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.26-azure  |
SUSE Linux Enterprise Server 12 (SP1, SP2,SP3,SP4) | [9.29][9.29 UR] | Alla [SUSE 12 SP1,SP2,SP3,SP4-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12) stöds.</br></br> 4.4.138-4.7-azure till 4.4.180-4.31-azure,</br>4.12.14-6.3-azure till 4.12.14-6.23-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15-filversioner som stöds

**Frisläpp** | **Version av mobilitetstjänster** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 och 15 SP1 | 9.32 | Alla [SUSE 15- och 15-kärnor](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) stöds.</br></br> 4.12.14-5.5-azure till 4.12.14-8.22-azure |

## <a name="linux-file-systemsguest-storage"></a>Linux-filsystem/gästlagring

**Komponent** | **Stöds**
--- | ---
Filsystem | ext3, ext4, XFS, BTRFS (villkor gäller enligt denna tabell)
Volymhanterare | - LVM stöds.<br/> - /boot på LVM stöds från [Samlad uppdatering 31](https://support.microsoft.com/help/4478871/) (version 9.20 av mobilitetstjänsten) och framåt. Det stöds inte i tidigare mobilitetstjänstversioner.<br/> - Flera OS-diskar stöds inte.
Paravirtualiserade lagringsenheter | Enheter som exporteras av paravirtualiserade drivrutiner stöds inte.
I/o-enheter för block med flera köer | Stöds inte.
Fysiska servrar med HP CCISS-lagringsstyrenheten | Stöds inte.
Namngivningskonvention för enhet/monteringspunkt | Enhetens namn eller monteringspunktsnamn ska vara unikt.<br/> Se till att inga två enheter/monteringspunkter har skiftlägeskänsliga namn. Till exempel stöds inte namngivningsenheter för samma virtuella dator som *device1* och *Device1.*
Kataloger | Om du kör en version av mobilitetstjänsten tidigare än version 9.20 (släppt i [Samlad uppdatering 31)](https://support.microsoft.com/help/4478871/)gäller dessa begränsningar:<br/><br/> - Dessa kataloger (om de ställs in som separata partitioner /fil-system) måste vara på samma OS-disk på källservern: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - Katalogen /boot ska finnas på en diskpartition och inte vara en LVM-volym.<br/><br/> Från version 9.20 och framåt gäller inte dessa begränsningar. 
Startkatalog | - Startdiskar får inte vara i GPT-partitionsformat. Det här är en begränsning av Azure-arkitekturen. GPT-diskar stöds som datadiskar.<br/><br/> Flera startdiskar på en virtuell dator stöds inte<br/><br/> - /boot på en LVM-volym över mer än en disk stöds inte.<br/> - En maskin utan startdiskett kan inte replikeras.
Krav på ledigt utrymme| 2 GB på rotpartitionen /root <br/><br/> 250 MB i installationsmappen
XFSv5 | XFSv5-funktioner i XFS-filsystem, till exempel metadatakontrollsumma, stöds (Mobilitetstjänst version 9.10 och framåt).<br/> Använd verktyget xfs_info för att kontrollera XFS-superblocket för partitionen. Om `ftype` är inställd på 1, då XFSv5 funktioner används.
Btrfs | BTRFS stöds från [Samlad uppdatering 34](https://support.microsoft.com/help/4490016) (version 9.22 av mobilitetstjänsten) och framåt. BTRFS stöds inte om:<br/><br/> - Undervolymen BTRFS-filsystem ändras efter att skydd har aktiverats.</br> - BTRFS filsystem är spridda över flera diskar.</br> - BTRFS filsystem stöder RAID.

## <a name="vmdisk-management"></a>Hantering av vm/disk

**Åtgärd** | **Detaljer**
--- | ---
Ändra storlek på disk på replikerad virtuell dator | Stöds på källdatorns virtuella dator före redundans, direkt i vm-egenskaperna. Du behöver inte inaktivera/återaktivera replikering.<br/><br/> Om du ändrar källdatorns virtuella dator efter redundans hämtas inte ändringarna.<br/><br/> Om du ändrar diskstorleken på Den virtuella Azure-datorn efter redundans, när du växlar tillbaka, skapar Site Recovery en ny virtuell dator med uppdateringarna.
Lägga till disk på replikerad virtuell dator | Stöds inte.<br/> Inaktivera replikering för den virtuella datorn, lägg till disken och aktivera replikering igen.

## <a name="network"></a>Nätverk

**Komponent** | **Stöds**
--- | ---
Värdnätverk NIC Teaming | Stöds för virtuella datorer med VMware. <br/><br/>Stöds inte för fysisk datorreplikering.
Värdnätverk VLAN | Ja.
Värdnätverk IPv4 | Ja.
Värdnätverk IPv6 | Nej.
NIC-teaming för gäst-/servernätverk | Nej.
Gäst-/servernätverk IPv4 | Ja.
Gäst-/servernätverk IPv6 | Nej.
Statisk IP för gäst-/servernätverk (Windows) | Ja.
Gäst/servernätverk statisk IP (Linux) | Ja. <br/><br/>Virtuella datorer är konfigurerade för att använda DHCP vid återställning av återställning efter fel.
Gäst-/servernätverk flera nätverkskort | Ja.


## <a name="azure-vm-network-after-failover"></a>Azure VM-nätverk (efter redundans)

**Komponent** | **Stöds**
--- | ---
Azure ExpressRoute | Ja
ILB (på andra sätt) | Ja
Elb | Ja
Azure Traffic Manager | Ja
Multi-NIC | Ja
Reserverad IP-adress | Ja
IPv4 | Ja
Behåll källans IP-adress | Ja
Tjänstslutpunkter för virtuellt nätverk i Azure<br/> | Ja
Snabbare nätverk | Inga

## <a name="storage"></a>Lagring
**Komponent** | **Stöds**
--- | ---
Dynamisk disk | OS-disken måste vara en enkel disk. <br/><br/>Datadiskar kan vara dynamiska diskar
Konfiguration av Docker-disk | Inga
Värd NFS | Ja för VMware<br/><br/> Nej för fysiska servrar
Värd för SAN (iSCSI/FC) | Ja
Värd vSAN | Ja för VMware<br/><br/> Saknas för fysiska servrar
Värd för multipath (MPIO) | Ja, testad med Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM för CLARiiON
Värdens virtuella volymer (VVols) | Ja för VMware<br/><br/> Saknas för fysiska servrar
VMDK för gäst/server | Ja
Delade klusterdisk för gäst/server | Inga
Gäst-/serverkrypterad disk | Inga
Gäst/server NFS | Inga
Gäst/server iSCSI | För migrering - Ja<br/>För haveriberedskap - Nej, iSCSI kommer att återställas som en ansluten disk till den virtuella datorn
Gäst/server SMB 3.0 | Inga
Gäst/server RDM | Ja<br/><br/> Saknas för fysiska servrar
Gäst-/serverdiskett > 1 TB | Ja, disken måste vara större än 1024 MB<br/><br/>Upp till 8 192 GB vid replikerande till hanterade diskar (9,26 version och framåt)<br></br> Upp till 4 095 GB vid replikerande till lagringskonton
Gäst-/serverdisk med 4K-logisk och 4k fysisk sektorstorlek | Inga
Gäst-/serverdisk med 4K logisk och 512 byte fysisk sektorstorlek | Inga
Gäst-/servervolym med randig disk >4 TB <br/><br/>Logisk volymhantering (LVM)| Ja
Gäst/server - Lagringsutrymmen | Inga
Snabbtillägg/ta bort disk för snabbtillägg/ta bort gäst/server | Inga
Gäst/server - exkluderar disk | Ja
Gäst/server multipath (MPIO) | Inga
GPT-partitioner för gäst/server | Fem partitioner stöds från [Samlad uppdatering 37](https://support.microsoft.com/help/4508614/) (version 9.25 av mobilitetstjänsten) och framåt. Tidigare stöddes fyra.
ReFS | Resilient File System stöds med Mobilitetstjänst version 9.23 eller senare
Gäst/server EFI/UEFI-start | - Stöds för Windows Server 2012 eller senare, SLES 12 SP4 och RHEL 8.0 med mobilitetsagent version 9.30 och framåt<br/> - Säker UEFI-starttyp stöds inte. 

## <a name="replication-channels"></a>Replikeringskanaler

|**Typ av replikering**   |**Stöds**  |
|---------|---------|
|Avläst dataöverföringar (ODX)    |       Inga  |
|Offline-seedning        |   Inga      |
| Azure Data Box | Inga

## <a name="azure-storage"></a>Azure Storage

**Komponent** | **Stöds**
--- | ---
Lokalt redundant lagring | Ja
Geografiskt redundant lagring | Ja
Geo-redundant lagring med läsbehörighet (RA-GRS) | Ja
Kyl lagring | Inga
Varm lagring| Inga
Blockblobar | Inga
Kryptering i vila (SSE)| Ja
Kryptering i vila (CMK)| Ja (via Powershell Az 3.3.0 modul och framåt)
Premium Storage | Ja
Tjänsten Importera/exportera | Inga
Azure Storage-brandväggar för virtuella nätverk | Ja.<br/> Konfigurerad på mållagrings-/cachelagringskonto (används för att lagra replikeringsdata).
Generella v2-lagringskonton (heta och svala nivåer) | Ja (Transaktionskostnaderna är betydligt högre för V2 jämfört med V1)

## <a name="azure-compute"></a>Azure-beräkning

**Funktion** | **Stöds**
--- | ---
Tillgänglighetsuppsättningar | Ja
Tillgänglighetszoner | Inga
Nav | Ja
Hanterade diskar | Ja

## <a name="azure-vm-requirements"></a>Virtuella Azure VMware-datorer

Lokala virtuella datorer som replikeras till Azure måste uppfylla azure vm-kraven som sammanfattas i den här tabellen. När Site Recovery kör en förutsättningskontroll för replikering misslyckas kontrollen om vissa av kraven inte uppfylls.

**Komponent** | **Krav** | **Detaljer**
--- | --- | ---
Gästoperativsystem | Verifiera [operativsystem som stöds](#replicated-machines) för replikerade datorer. | Kontrollen misslyckas om den inte stöds.
Arkitektur för gästoperativsystem | 64-bitars. | Kontrollen misslyckas om den inte stöds.
Storlek på operativsystemdisk | Upp till 2 048 GB. | Kontrollen misslyckas om den inte stöds.
Antal operativsystemdiskar | 1 | Kontrollen misslyckas om den inte stöds.
Antal datadiskar | 64 eller mindre. | Kontrollen misslyckas om den inte stöds.
Storlek på datadisk | Upp till 8 192 GB vid replikerande till hanterad disk (9,26 version och framåt)<br></br>Upp till 4 095 GB vid replikerande till lagringskonto| Kontrollen misslyckas om den inte stöds.
Nätverkskort | Flera kort stöds. |
Delad VHD | Stöds inte. | Kontrollen misslyckas om den inte stöds.
FC-disk | Stöds inte. | Kontrollen misslyckas om den inte stöds.
BitLocker | Stöds inte. | BitLocker måste inaktiveras innan du aktiverar replikering för en dator. |
VM-namn | Från 1 till 63 tecken.<br/><br/> Begränsat till bokstäver, siffror och bindestreck.<br/><br/> Maskinnamnet måste börja och sluta med en bokstav eller ett nummer. |  Uppdatera värdet i datoregenskaperna i Site Recovery.

## <a name="resource-group-limits"></a>Begränsningar för resursgrupp

Om du vill förstå hur många virtuella datorer som kan skyddas under en enda resursgrupp läser du artikeln om [prenumerationsbegränsningar och kvoter](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Gränser för omsättning

Följande tabell innehåller gränserna för Azure Site Recovery. 
- Dessa gränser baseras på våra tester, men täcker inte alla möjliga app-I/O-kombinationer.
- De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program.
- För bästa resultat rekommenderar vi starkt att du kör [verktyget Deployment Planner](site-recovery-deployment-planner.md)och utför omfattande programtester med hjälp av testundans för att få den sanna prestandabilden för din app.

**Replikeringsmål** | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total omsättning av källdiskdata per dag**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk


**Källans dataomsättning** | **Övre gräns**
---|---
Högsta dataomsättning av alla diskar på en virtuella dator | 54 MB/s
Maximal dataomsättning per dag som stöds av en processerver | 2 TB

- Det här är genomsnittliga värden baserade på en I/O-överlappning på 30 procent.
- Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen.
- Dessa siffror antar en typisk eftersläpning på cirka fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.

## <a name="vault-tasks"></a>Arkivuppgifter

**Åtgärd** | **Stöds**
--- | ---
Flytta valv över resursgrupper | Inga
Flytta valv inom och mellan prenumerationer | Inga
Flytta lagring, nätverk, virtuella Azure-datorer mellan resursgrupper | Inga
Flytta lagring, nätverk, virtuella Azure-datorer inom och mellan prenumerationer. | Inga


## <a name="obtain-latest-components"></a>Hämta de senaste komponenterna

**Namn** | **Beskrivning** | **Detaljer**
--- | --- | ---
Konfigurationsserver | Installerad lokalt.<br/> Samordnar kommunikationen mellan lokala VMware-servrar eller fysiska datorer och Azure. | - [Läs mer om](vmware-physical-azure-config-process-server-overview.md) konfigurationsservern.<br/> - [Läs mer om](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) att uppgradera till den senaste versionen.<br/> - [Läs mer om](vmware-azure-deploy-configuration-server.md) hur du konfigurerar konfigurationsservern. 
Bearbeta server | Installeras som standard på konfigurationsservern.<br/> Tar emot replikeringsdata, optimerar den med cachelagring, komprimering och kryptering och skickar den till Azure.<br/> När distributionen växer kan du lägga till ytterligare processservrar för att hantera större volymer replikeringstrafik. | - [Läs mer om](vmware-physical-azure-config-process-server-overview.md) processservern.<br/> - [Läs mer om](vmware-azure-manage-process-server.md#upgrade-a-process-server) att uppgradera till den senaste versionen.<br/> - [Läs mer om](vmware-physical-large-deployment.md#set-up-a-process-server) hur du konfigurerar utskalningsprocessservrar.
Mobilitetstjänst | Installerad på virtuell dator för VMware eller fysiska servrar som du vill replikera.<br/> Koordinerar replikering mellan lokala VMware-servrar/fysiska servrar och Azure.| - [Läs mer om](vmware-physical-mobility-service-overview.md) mobilitetstjänsten.<br/> - [Läs mer om](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) att uppgradera till den senaste versionen.<br/> 



## <a name="next-steps"></a>Nästa steg
[Lär dig hur du](tutorial-prepare-azure.md) förbereder Azure för haveriberedskap av virtuella datorer med VMware.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
