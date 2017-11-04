---
title: "Krav för replikering till Azure med hjälp av Azure Site Recovery | Microsoft Docs"
description: "Läs mer om krav för att replikera virtuella datorer och fysiska datorer till Azure med hjälp av Azure Site Recovery-tjänsten."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/19/2017
ms.author: raynew
ms.openlocfilehash: cb7c3892dc0acbffdec636e5e7b3ce063660153c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Krav för replikering lokalt till Azure med hjälp av Site Recovery

> [!div class="op_single_selector"]
> * [Replikera från Azure till Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replikera från lokal till Azure](site-recovery-prereq.md)

Azure Site Recovery kan hjälpa dig stöder din affärskontinuitet och haveriberedskap (BCDR) genom att samordna replikeringen för en Azure virtuell dator (VM) till en annan Azure-region. Site Recovery replikerar också lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till ett sekundärt datacenter. Om ett avbrott uppstår på den primära platsen, kan du växla över till en sekundär plats för att hålla program och arbetsbelastningar som är tillgängliga. När den primära platsen återgår till normal drift kan du växla tillbaka till den primära platsen. Mer information om Platsåterställning finns [vad är Site Recovery?](site-recovery-overview.md).

I den här artikeln sammanfattar vi förutsättningarna för börjar Site Recovery replikering från en lokal dator till Azure.

Du kan skicka kommentarer längst ned i artikeln. Du kan också begära tekniska frågor i den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Krav för Azure

**Krav** | **Detaljer**
--- | ---
**Azure-konto** | En [Microsoft Azure-konto](http://azure.microsoft.com/). Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
**Site Recovery-tjänsten** | Mer information om priser för Azure Site Recovery-tjänsten finns [priserna för Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Azure Storage** | Du behöver ett Azure Storage-konto för att lagra replikerade data. Lagringskontot måste finnas i samma region som Azure Recovery Services-valvet. Virtuella Azure-datorer skapas när redundansväxlingen.<br/><br/> Beroende på vilken resursmodell som du vill använda för Virtuella Azure-redundans, du kan konfigurera ett konto med hjälp av den [Azure Resource Manager-distributionsmodellen](../storage/common/storage-create-storage-account.md) eller [klassiska distributionsmodellen](../storage/common/storage-create-storage-account.md).<br/><br/>Du kan använda [geo-redundant lagring](../storage/common/storage-redundancy.md#geo-redundant-storage) eller lokalt redundant lagring. Vi rekommenderar geo-redundant lagring. Med geo-redundant lagring är dina data flexibla i händelse av ett regionalt avbrott, eller om den primära regionen inte kan återställas.<br/><br/> Du kan använda Azure storage-standardkonto eller du kan använda Azure Premium-lagring. [Premium-lagring](https://docs.microsoft.com/azure/storage/storage-premium-storage) används vanligtvis för virtuella datorer som behöver en konsekvent höga i/o-prestanda och låg latens. Med premium-lagring, en virtuell dator kan vara värd för I/O-intensiva arbetsbelastningar. Om du använder premiumlagring för replikerade data behöver du även ett standardlagringskonto. Ett standardlagringskonto lagrar replikeringsloggar som samlar in löpande ändringar av lokala data.<br/><br/>
**Begränsningar för lagring** | Du kan inte flytta storage-konton som du använder i Site Recovery till en annan resursgrupp eller flytta till eller med en annan prenumeration.<br/><br/> För närvarande kan replikeras till premium-lagringskonton i centrala Indien och södra Indien är inte tillgänglig.
**Azure-nätverk** | Du behöver ett Azure nätverk som virtuella Azure-datorer ansluta till efter redundansväxlingen. Azure-nätverket måste finnas i samma region som Recovery Services-valvet.<br/><br/> I Azure-portalen kan du skapa ett Azure-nätverk med hjälp av [Resource Manager-distributionsmodellen](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) eller [klassiska distributionsmodellen](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Om du replikerar från System Center Virtual Machine Manager (VMM) till Azure måste du konfigurera nätverksmappning mellan VMM VM-nätverk och Azure-nätverk. Detta säkerställer att virtuella datorer i Azure ansluter till lämpliga nätverk efter redundansväxling.
**Begränsningar för** | Du kan inte flytta nätverkskonton som du använder i Site Recovery till en annan resursgrupp eller flytta till eller med en annan prenumeration.
**Nätverksmappning** | Om du replikerar Microsoft Hyper-V virtuella datorer i VMM-moln, måste du konfigurera nätverksmappning. Detta säkerställer att virtuella Azure-datorer ansluta till lämpliga nätverk när de skapas efter växling vid fel.

> [!NOTE]
> I följande avsnitt beskrivs kraven för olika komponenter i kundens miljö. Mer information om stöd för specifika konfigurationer finns i [supportmatrisen](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>Återställning av virtuella VMware-datorer eller fysiska Windows- eller Linux-servrar till Azure
Följande komponenter krävs för återställning av virtuella VMware-datorer eller fysiska Windows- eller Linux-servrar. Dessa är utöver de som beskrivs i [krav för Azure](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Konfigurationsservern eller ytterligare processervern

Konfigurera en lokal dator som konfigurationsservern att dirigera kommunikation mellan lokal plats och Azure. Den nedan tabell pratar om system- och programvarukraven för en virtuell dator som kan konfigureras som en konfigurationsserver.

> [!IMPORTANT]
> När du distribuerar en konfigurationsserver för att skydda virtuella VMware-datorer, rekommenderar vi att du distribuerar den som en **hög tillgänglighet (HA)** virtuella datorn.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>Krav på VMware vCenter eller vSphere värdar

| **Komponent** | **Krav** |
| --- | --- |
| **vSphere** | Du måste ha en eller flera VMware vSphere-hypervisor-program.<br/><br/>Hypervisorer måste köra vSphere version 6.0, 5.5 eller 5.1, med de senaste uppdateringarna.<br/><br/>Vi rekommenderar att vSphere-värdar och vCenter-servrar vara på samma nätverk som processervern. Om du har konfigurerat en dedikerad processervern är detta nätverk där konfigurationsservern finns. |
| **vCenter** | Vi rekommenderar att du distribuerar en VMware vCenter-server för att hantera vSphere-värdar. Den måste igång vCenter version 6.0 eller 5.5, med de senaste uppdateringarna.<br/><br/>**Begränsning**: Site Recovery har inte stöd för replikering mellan olika instanser av vCenter vMotion. Lagring DRS och Storage vMotion även stöds inte på Huvudmålet VM efter en skyddar åtgärd.|

### <a name="replicated-machine-prerequisites"></a>Förutsättningar för replikerad dator

| **Komponent** | **Krav** |
| --- | --- |
| **Lokala datorer** (VMwares virtuella datorer) | Replikerade virtuella datorer måste ha VMware-verktyg installerat och körs.<br/><br/> Virtuella datorer måste uppfylla [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) för att skapa en Azure VM.<br/><br/>Diskutrymme på varje skyddad dator får inte vara mer än 1,023 GB. <br/><br/>En minsta 2 GB ledigt utrymme på installationsenheten krävs för installationen.<br/><br/>Om du vill aktivera konsekvens för flera öppnas port 20004 på den lokala VM-brandväggen.<br/><br/>Datornamn måste vara mellan 1 och 63 tecken (du kan använda bokstäver, siffror och bindestreck). Namnet måste börja med en bokstav eller siffra och sluta med en bokstav eller siffra. <br/><br/>När du har aktiverat replikering för en dator kan ändra du namnet på Azure.<br/><br/> |
| **Windows-datorer** (fysisk eller VMware) | Datorn måste köra något av följande stöds 64-bitars operativsystem: <br/>– Windows Server 2016 (Server Core, Server med Skrivbordsmiljö)<br/>– Windows Server 2012 R2<br/>– Windows Server 2012<br/>-Windows Server 2008 R2 med SP1 eller senare<br/><br/> Operativsystemet måste installeras på enhet C. OS-disken måste vara en standarddisk för Windows, och inte dynamiska. Datadisken kan vara dynamisk.<br/><br/>|
| **Linux-datorer** (fysisk eller VMware) | Datorn måste köra något av följande stöds 64-bitars operativsystem: <br/>-Red Hat Enterprise Linux 5.2 5.11 6.1 6,9, 7.0 7.3<br/>-CentOS 5.2 5.11 6.1 6,9, 7.0 7.3<br/>-Ubuntu 14.04 LTS server (en lista över stöds kernel-versioner för Ubuntu finns [operativsystem](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-Ubuntu 16.04 LTS server (en lista över stöds kernel-versioner för Ubuntu finns [operativsystem](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>-Debian 7 eller Debian 8<br/>-Oracle Enterprise Linux 6.5 eller 6.4, Red Hat-kompatibel kernel eller Unbreakable Enterprise Kernel version 3 (UEK3)<br/>-SUSE Linux Enterprise Server 11 SP4 eller SUSE Linux Enterprise Server 11 SP3<br/><br/>/ Etc/hosts filer på skyddade datorer måste ha poster som matchar namnet på lokala värddatorn till IP-adresser som är kopplade till alla nätverkskort.<br/><br/>Efter växling vid fel, om du vill ansluta till en virtuell Azure-dator som kör Linux med hjälp av en klient med SSH (Secure Shell) se till att SSH-tjänsten på den skyddade datorn är inställd att starta automatiskt på datorn startas. Kontrollera också att brandväggsreglerna tillåter en SSH-anslutning för den skyddade datorn.<br/><br/>Värdnamn, monteringspunkter, enhetsnamn, och Linux-system sökvägar och filnamn (t.ex, / etc / och/usr) finnas endast på engelska.<br/><br/>Följande kataloger (om konfigurerad som separata partitioner eller filsystem) måste vara på samma disk (OS-disk) på källservern:<br/>- / (rot)<br/>- / Starta<br/>-/ usr<br/>-/ usr/lokal<br/>-/var<br/>- / m.m.<br/><br/>XFS v5 funktioner, till exempel metadata kontrollsumma stöds för närvarande inte av Site Recovery på XFS filsystem. Se till att din XFS filsystem inte använder några v5-funktioner. Du kan använda verktyget xfs_info för att kontrollera XFS superblock för partitionen. Om **ftype** är inställd på **1**, XFS v5 funktionerna används.<br/><br/>Verktyget lsof måste vara installerade och tillgängliga på Red Hat Enterprise Linux 7 och CentOS 7-servrar.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Återställning av virtuella Hyper-V-datorer till Azure (utan VMM)

Följande komponenter krävs för katastrofåterställning för Hyper-V virtuella datorer i VMM-moln. Dessa är utöver de som beskrivs i [krav för Azure](#azure-requirements).

| **Krav** | **Detaljer** |
| --- | --- |
| **Hyper-V-värd** |En eller flera lokala servrar måste köra Windows Server 2012 R2 med de senaste uppdateringarna och Hyper-V-rollen aktiverad eller Microsoft Hyper-V Server 2012 R2.<br/><br/>Hyper-V-servrar måste ha en eller flera virtuella datorer.<br/><br/>Hyper-V-servrar måste vara ansluten till Internet, antingen direkt eller via en proxyserver.<br/><br/>Hyper-V-servrar måste ha korrigeringar som beskrivs i Knowledge Base-artikel [2961977](https://support.microsoft.com/kb/2961977) installerad.
|**Provider och agent**| Under distributionen av Site Recovery installerar du Azure Site Recovery-providern. Providerinstallationen av installeras även Azure Recovery Services-agenten på varje Hyper-V-server som kör virtuella datorer som du vill skydda. <br/><br/>Alla Hyper-V-servrar i ett Site Recovery-valv måste ha samma versioner av providern och agenten.<br/><br/>Providern måste ansluta till Site Recovery via Internet. Trafik kan skickas direkt eller via en proxyserver. HTTPS-baserade proxyservrar stöds inte. Proxyservern måste tillåta åtkomst till följande webbadresser:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Om du har IP-adressbaserade brandväggsregler på servern, kontrollerar du att reglerna tillåter kommunikation till Azure.<br/><br/> Tillåt den [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653), och HTTPS (port 443).<br/><br/> Tillåt IP-adressintervall för Azure-regionen för din prenumeration och västra USA (används för hantering av kontrollen och identitet).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Katastrofåterställning för Hyper-V virtuella datorer i VMM-moln till Azure

Följande komponenter krävs för katastrofåterställning för Hyper-V virtuella datorer i VMM-moln. Dessa är utöver de som beskrivs i [krav för Azure](#azure-requirements).

| **Krav** | **Detaljer** |
| --- | --- |
| **Virtual Machine Manager** |Du måste ha en eller flera VMM-servrar som körs på System Center 2012 R2 eller senare. Varje VMM-servern måste ha en eller flera moln har konfigurerats. <br/><br/>Ett moln måste ha:<br/>-En eller flera VMM-värdgrupper.<br/>-En eller flera servrar med Hyper-V-värd eller kluster i varje värdgrupp.<br/><br/>Läs mer om hur du konfigurerar VMM-moln, [hur du skapar ett moln i Virtual Machine Manager 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx). |
| **Hyper-V** |Hyper-V-värdservrar måste köra minst Windows Server 2012 R2 med Hyper-V-rollen aktiverad, eller Microsoft Hyper-V Server 2012 R2. De senaste uppdateringarna måste vara installerade.<br/><br/> En Hyper-V-servern måste ha en eller flera virtuella datorer.<br/><br/> En Hyper-V-värdserver eller kluster som innehåller virtuella datorer som du vill replikera måste hanteras i VMM-moln.<br/><br/>Hyper-V-servrar måste vara ansluten till Internet, antingen direkt eller via en proxyserver.<br/><br/>Hyper-V-servrar måste ha korrigeringar som beskrivs i Knowledge Base-artikel [2961977](https://support.microsoft.com/kb/2961977) installerad.<br/><br/>Hyper-V-värdservrar behöver Internetåtkomst för datareplikering till Azure. |
| **Provider och agent** |Installera Azure Site Recovery-providern på VMM-servern under distributionen av Azure Site Recovery. Installera Recovery Services-agenten på Hyper-V-värdar. Providern och agenten måste du ansluta till Azure direkt via Internet eller via en proxyserver. HTTPS-baserade proxyservrar stöds inte. Proxyservern på VMM-servern och Hyper-V-värdar måste tillåta åtkomst till: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Om du har IP-adressbaserade brandväggsregler på VMM-servern kontrollerar du att reglerna tillåter kommunikation till Azure.<br/><br/> Tillåt den [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653) och HTTPS (port 443).<br/><br/>Tillåt IP-adressintervall för Azure-regionen för din prenumeration och västra USA (används för hantering av kontrollen och identitet).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Förutsättningar för replikerad dator

| **Komponent** | **Detaljer** |
| --- | --- |
| **Skyddade virtuella datorer** | Site Recovery har stöd för alla operativsystem som stöds av [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Virtuella datorer måste uppfylla de [krav för Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) för att skapa en Azure VM. Datornamn måste vara mellan 1 och 63 tecken (du kan använda bokstäver, siffror och bindestreck). Namnet måste börja med en bokstav eller siffra och sluta med en bokstav eller siffra. <br/><br/>Du kan ändra namnet på virtuella datorn när du har aktiverat replikering för den virtuella datorn. <br/><br/> Diskkapacitet för varje skyddad dator får inte vara mer än 1,023 GB. En virtuell dator kan ha upp till 16 diskar (upp till 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Katastrofåterställning för Hyper-V virtuella datorer i VMM-moln till en plats som ägs av kunden

Följande komponenter krävs för katastrofåterställning för Hyper-V virtuella datorer i VMM-moln till en plats som ägs av kunden. Dessa är utöver de som beskrivs i [krav för Azure](#azure-requirements).

| **Komponent** | **Detaljer** |
| --- | --- |
| **Virtual Machine Manager** |  Vi rekommenderar att du distribuerar en VMM-server på både den primära platsen och den sekundära platsen.<br/><br/> Om du vill replikera mellan moln på en VMM-server behöver du minst två moln som konfigurerats på VMM-servern.<br/><br/> VMM-servrar måste köra minst System Center 2012 SP1, med de senaste uppdateringarna.<br/><br/> Varje VMM-servern måste ha ett eller flera moln. Alla moln måste ha Hyper-V-kapacitetsprofilen angivet. <br/><br/>Moln måste ha en eller flera VMM-värdgrupper. Läs mer om hur du konfigurerar VMM-moln, [förbereda för distribution av Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Hyper-V-servrar måste köra minst Windows Server 2012 med Hyper-V-rollen aktiverad och ha de senaste uppdateringarna installerade.<br/><br/> En Hyper-V-servern måste ha en eller flera virtuella datorer.<br/><br/>  Hyper-V-värdservrar måste finnas i värdgrupper i de primära och sekundära VMM-moln.<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 R2, rekommenderar vi att du installerar uppdateringen som beskrivs i Knowledge Base-artikel [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Om du kör Hyper-V i ett kluster på Windows Server 2012 och har en statisk IP-adress-kluster skapas klusterutjämning inte automatiskt. Du måste konfigurera klusterutjämningen manuellt. Läs mer om klusterutjämningen [konfigurera rollen replik för kluster till kluster-replikering](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Leverantör** | Under distributionen av Site Recovery installerar du Azure Site Recovery-providern på VMM-servrar. Providern kommunicerar med Site Recovery via HTTPS (port 443) för att samordna replikeringen. Datareplikering sker mellan primära och sekundära Hyper-V-servrarna via LAN eller via en VPN-anslutning.<br/><br/> Providern som körs på VMM-servern måste ha åtkomst till följande webbadresser:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Site Recovery-providern måste tillåta brandväggen kommunikation från VMM-servrar för att den [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653), och tillåta protokollet HTTPS (port 443). |


## <a name="url-access"></a>URL-åtkomst
Dessa URL: er måste vara tillgänglig från VMware, VMM och Hyper-V-värdservrar:

|**URL: EN** | **VMM till VMM** | **VMM till Azure** | **Hyper-V till Azure** | **VMware till Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | Tillåt | Tillåt | Tillåt | Tillåt |
|``*.backup.windowsazure.com`` | Krävs inte | Tillåt | Tillåt | Tillåt |
|``*.hypervrecoverymanager.windowsazure.com`` | Tillåt | Tillåt | Tillåt | Tillåt |
|``*.store.core.windows.net`` | Tillåt | Tillåt | Tillåt | Tillåt |
|``*.blob.core.windows.net`` | Krävs inte | Tillåt | Tillåt | Tillåt |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Krävs inte | Krävs inte | Krävs inte | Tillåt för SQL-hämtning |
|``time.windows.com`` | Tillåt | Tillåt | Tillåt | Tillåt|
|``time.nist.gov`` | Tillåt | Tillåt | Tillåt | Tillåt |
