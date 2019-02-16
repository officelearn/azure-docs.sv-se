---
title: 'Snabbstart: Manuell installation av en instans SAP HANA på Azure Virtual Machines | Microsoft Docs'
description: Snabbstartsguide för manuell installation av en instans SAP HANA på Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 561eff75ef4268acd3f737f7aaa92ccaacfda7f3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328732"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Snabbstart: Manuell installation av en instans SAP HANA på Azure Virtual Machines
## <a name="introduction"></a>Introduktion
Den här guiden kan du ställa in en enda instans SAP HANA på Azure virtuella datorer (VM) när du installerar 7.5 för SAP NetWeaver och SAP HANA 1.0 SP12 manuellt. Den här guiden fokuserar på är om distribution av SAP HANA på Azure. Ersätter inte SAP-dokumentationen. 

>[!Note]
>Den här guiden beskriver distributioner av SAP HANA i Azure virtuella datorer. Information om hur du distribuerar SAP HANA på stora HANA-instanser finns i [med SAP på virtuella Azure-datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Förutsättningar
Den här guiden förutsätter att du är bekant med denna infrastruktur som en tjänst (IaaS)-grunder som:
 * Hur du distribuerar virtuella datorer eller virtuella nätverk via Azure portal eller PowerShell.
 * Azures plattformsoberoende kommandoradsgränssnitt (CLI), inklusive möjligheten att använda mallar i JavaScript Object Notation (JSON).

Den här guiden förutsätter också att du är bekant med:
* SAP HANA och SAP NetWeaver och hur du installerar dem lokalt.
* Installera och använda SAP HANA och instanser av SAP-programmet på Azure.
* Följande koncept och procedurer:
   * Planera för SAP-distribution på Azure, inklusive Azure Virtual Network planerings- och användning av Azure Storage. Se [SAP NetWeaver på Azure Virtual Machines (VMs) – planerings-och implementering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Distribution av principer och sätt att distribuera virtuella datorer i Azure. Se [Azure Virtual Machines-distribution för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Hög tillgänglighet för SAP NetWeaver ASCS (ABAP SAP Central Services), SCS (SAP Central Services) och ÄNDARE (sätta replikering Server) på Azure. Se [hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Information om hur du förbättrar effektiviteten i att använda en multi-SID-installation av ASCS/SCS på Azure. Se [skapa en konfiguration för SAP NetWeaver – flera SÄKERHETSIDENTIFIERARE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principer för att köra SAP NetWeaver baserat på Linux-drivna virtuella datorer i Azure. Se [som kör SAP NetWeaver på Microsoft Azure SUSE Linux Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Den här guiden innehåller specifika inställningar för Linux i Azure virtuella datorer och information om hur du kopplar korrekt Azure storage-diskar till virtuella Linux-datorer.

Vilka typer av virtuella Azure-datorer som kan användas för produktionsscenarier visas i den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). För icke-produktionsscenarion finns en mängd olika interna Azure VM-typer.
För mer information om VM-konfiguration och drift läser dokumentet [konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Hög tillgänglighet för SAP HANA, se [SAP HANA, hög tillgänglighet för Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Om du vill hämta en instans av SAP HANA eller S/4HANA eller BW/4HANA-system som distribuerats i mycket snabbt, bör du överväga användning av [SAP Cloud Appliance Library](http://cal.sap.com). Det finns mer information om hur du distribuerar till exempel ett S/4HANA-system via SAP CAL på Azure i [den här guiden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Allt du behöver är en Azure-prenumeration och en SAP-användare som kan registreras med SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Ytterligare resurser
### <a name="sap-hana-backup"></a>SAP HANA-säkerhetskopia
Information om säkerhetskopiering av SAP HANA-databaser på Azure Virtual Machines finns i:
* [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Information om hur du använder SAP Cloud Appliance Library distribuera S/4HANA eller BW/4HANA finns i [distribuera SAP S/4HANA eller BW/4HANA på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA – stöd för operativsystem
Information om operativsystem som stöds av SAP HANA, finns i [SAP Support Obs! #2235581 – SAP HANA: Operativsystem som stöds](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuella Azure-datorer stöder endast en delmängd av dessa operativsystem. Följande operativsystem stöds för att distribuera SAP HANA på Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

För ytterligare SAP-dokumentationen om SAP HANA och olika Linux-operativsystem, se:

* [SAP Support Obs! #171356 - SAP-program på Linux:  Allmän Information](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP Support Obs! #1944799 – SAP HANA riktlinjer för Installation av operativsystemet SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP Support-kommentar #2205917 – SAP HANA-Databasobjekt rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP Support-kommentar #1984787 - SUSE Linux Enterprise Server 12:  Installationsinformation](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP Support-kommentar #1391070 - UUID för Linux-lösningar](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP Support Obs! #2009879 – SAP HANA riktlinjer för Red Hat Enterprise Linux (RHEL)-operativsystem](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: Rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP övervakning i Azure
Information om SAP övervakning i Azure finns i:

* [SAP-kommentar 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Den här anteckningen diskuterar SAP ”förbättrad övervakning” med virtuella Linux-datorer på Azure. 
* [SAP-kommentar 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Den här anteckningen beskriver information om SAPOSCOL på Linux. 
* [SAP-kommentar 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Den här anteckningen beskrivs viktiga mått för övervakning för SAP på Microsoft Azure.

### <a name="azure-vm-types"></a>Azure VM-typer
Azure VM-typer och stöd för SAP-arbetsbelastningsscenarier används med SAP HANA finns dokumenterade i [SAP-certifierad IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure VM-typer som är certifierade av SAP för SAP NetWeaver och S/4HANA programnivån finns dokumenterade i [SAP anteckning 1928533 - SAP-program i Azure: Produkter som stöds och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>SAP-Linux Azure-integration stöds endast på Azure Resource Manager och inte den klassiska distributionsmodellen. 

## <a name="manual-installation-of-sap-hana"></a>Manuell installation av SAP HANA

> [!IMPORTANT]
> Kontrollera att Operativsystemet som du väljer är SAP-certifierade för SAP HANA på de specifika VM-typer som du använder. Listan över SAP HANA-certifierade VM-typer och OS-versioner för de som kan sökas i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka på information om den typ av virtuell dator i listan för att få en fullständig lista över SAP HANA OS-versioner som stöds för en viss typ av virtuell dator. Tänk på att i det här exemplet i det här dokumentet vi använde en SLES OS-version som inte stöds av SAP för SAP HANA på virtuella datorer i M-serien.
>

Den här guiden beskriver hur du manuellt installera SAP HANA på Azure virtuella datorer på två olika sätt:

* Genom att använda SAP Software etablering Manager (SWPM) som en del av en distribuerad installation NetWeaver i ”installera databasinstansen” steg
* Med hjälp av SAP HANA-databas livscykel Enhetshanteraren, HDBLCM och sedan installera NetWeaver

Du kan också använda SWPM för att installera alla komponenter (SAP HANA, SAP-programservern och ASCS-instans) i en enda virtuell dator, enligt beskrivningen i det här [SAP HANA-bloggmeddelande](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Det här alternativet är inte beskrivs i den här snabbstartsguiden, men de problem som du måste ta hänsyn till är desamma.

Innan du startar en installation, rekommenderar vi att du läser den ”förbereda Azure virtuella datorer för manuell installation av SAP HANA” senare i den här handboken. Då kan du förhindra flera grundläggande fel som kan uppstå när du använder bara en virtuell Azure-dator standardkonfiguration.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Viktiga steg för SAP HANA-installationen när du använder SAP SWPM
Det här avsnittet innehåller viktiga steg för att installera en manuell, enskild instans SAP HANA när du använder SAP SWPM för att utföra en distribuerad SAP NetWeaver 7.5-installation. De enskilda stegen beskrivs i detalj i skärmbilder senare i den här guiden.

1. Skapa ett Azure-nätverk som innehåller två test virtuella datorer.
2. Distribuera två virtuella Azure-datorer med operativsystem (i vårt exempel, SUSE Linux Enterprise Server (SLES) och SLES för SAP-program 12 SP1), enligt Azure Resource Manager-modellen.
3. Koppla två Azure standard eller premium storage-diskar (till exempel 75 GB eller 500 GB-diskar) till programservern VM.
4. Koppla premium storage-diskar till HANA-Databasobjekt server-dator. Mer information finns i installationsavsnittet ”Disk” senare i den här guiden.
5. Beroende på storleken eller dataflödet krav, lägga till flera diskar och skapa sedan stripe-volymer genom att använda hantering av logisk volym eller en administrationsverktyget för flera enheter (MDADM) på operativsystemsnivån inuti den virtuella datorn.
6. Skapa XFS filsystem på anslutna diskar eller logiska volymer.
7. Montera de nya XFS filsystem på operativsystemsnivån. Använd ett filsystem för SAP-program. Använda andra filsystemet /sapmnt katalog-och säkerhetskopior, till exempel. Montera filsystem XFS på premium-lagringsdiskar som /hana och /usr/sap på SAP HANA-Databasobjekt-servern. Den här processen är nödvändigt att förhindra att rot-filsystem, som inte är stor på Azure Virtual Machines för Linux, fyller upp.
8. Ange lokala IP-adresserna för testet virtuella datorer i/etc/hosts-filen.
9. Ange den **nofail** parameter i fstab-filen/etc /.
10. Ange parametrarna för Linux-kernel enligt Linux OS-versionen som du använder. Mer information hittar du i lämplig SAP viktig information om HANA och avsnittet ”Kernel parametrar” i den här guiden.
11. Lägg till växlingsutrymme.
12. Du kan också installera en grafisk desktop på virtuella datorer för testning. Annars kan du använda en fjärrinstallation av SAPinst.
13. Hämta SAP-program från SAP Service Marketplace.
14. Installera SAP ASCS-instans på applikationsserver-dator.
15. Dela katalogen /sapmnt bland testet virtuella datorer med hjälp av NFS. Programservern VM är den NFS-servern.
16. Installera den databasinstans, inklusive HANA, med hjälp av SWPM på DB-server-dator.
17. Installera den primära servern (PROVIDERADRESSER) på VM-programservern.
18. Starta SAP-hanteringskonsolen (SAP MC). Ansluta med SAP-Gränssnittet eller HANA Studio, till exempel.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Viktiga steg för SAP HANA-installationen när du använder HDBLCM
Det här avsnittet innehåller viktiga steg för att installera en manuell, enskild instans SAP HANA när du använder SAP HDBLCM för att utföra en distribuerad SAP NetWeaver 7.5-installation. De enskilda stegen beskrivs i detalj i skärmbilderna i den här guiden.

1. Skapa ett Azure-nätverk som innehåller två test virtuella datorer.
2. Distribuera två virtuella Azure-datorer med operativsystem (i vårt exempel SLES- och SLES för SAP-program 12 SP1) enligt Azure Resource Manager-modellen.
3. Koppla två Azure standard eller premium storage-diskar (till exempel 75 GB eller 500 GB-diskar) till appservern VM.
4. Koppla premium storage-diskar till HANA-Databasobjekt server-dator. Mer information finns i installationsavsnittet ”Disk” senare i den här guiden.
5. Beroende på storleken eller dataflödet krav, lägga till flera diskar och skapa stripe-volymer genom att använda hantering av logisk volym eller en administrationsverktyget för flera enheter (MDADM) på operativsystemsnivån inuti den virtuella datorn.
6. Skapa XFS filsystem på anslutna diskar eller logiska volymer.
7. Montera de nya XFS filsystem på operativsystemsnivån. Använd ett filsystem för SAP-program och Använd en /sapmnt katalog-och säkerhetskopior, till exempel. Montera filsystem XFS på premium-lagringsdiskar som /hana och /usr/sap på SAP HANA-Databasobjekt-servern. Den här processen är nödvändigt för att förhindra att rot-filsystem, som inte är stor på Azure Virtual Machines för Linux, fyller upp.
8. Ange lokala IP-adresserna för testet virtuella datorer i/etc/hosts-filen.
9. Ange den **nofail** parameter i fstab-filen/etc /.
10. Ange kernel-parametrarna enligt Linux OS-versionen som du använder. Mer information hittar du i lämplig SAP viktig information om HANA och avsnittet ”Kernel parametrar” i den här guiden.
11. Lägg till växlingsutrymme.
12. Du kan också installera en grafisk desktop på virtuella datorer för testning. Annars kan du använda en fjärrinstallation av SAPinst.
13. Hämta SAP-program från SAP Service Marketplace.
14. Skapa en grupp, sapsys, med grupp-ID 1001 på HANA-Databasobjekt server-dator.
15. Installera SAP HANA på DB-server-dator med hjälp av HANA Database livscykel Manager (HDBLCM).
16. Installera SAP ASCS-instans på applikationsserver-dator.
17. Dela katalogen /sapmnt bland testet virtuella datorer med hjälp av NFS. Programservern VM är den NFS-servern.
18. Installera den databasinstans, inklusive HANA, med hjälp av SWPM på HANA-Databasobjekt server-dator.
19. Installera den primära servern (PROVIDERADRESSER) på VM-programservern.
20. Starta SAP MC. Ansluta via SAP-Gränssnittet eller HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Förbereda virtuella Azure-datorer för en manuell installation av SAP HANA
Det här avsnittet innehåller följande avsnitt:

* OS-uppdateringar
* Disk-installationen
* Kernel parametrar
* Filsystem
* I/etc/hosts-filen
* Fstab-filen/etc /

### <a name="os-updates"></a>OS-uppdateringar
Sök efter Linux OS-uppdateringar och korrigeringar innan du installerar ytterligare programvara. Genom att installera en uppdatering, kanske du kan undvika ett anrop till supporten.

Kontrollera att du använder:
* SUSE Linux Enterprise Server för SAP-program.
* Red Hat Enterprise Linux för SAP-program eller Red Hat Enterprise Linux for SAP HANA. 

Om du inte redan gjort registrera OS-distributionen med Linux prenumerationen från Linux-försäljare. Observera att SUSE OS-avbildningar för SAP-program som redan omfattar tjänster och som är registrerade automatiskt.

Här är ett exempel på sökning efter tillgänglig korrigeringsfiler för SUSE Linux med hjälp av den **zypper** kommando:

 `sudo zypper list-patches`

Beroende på vilken typ av problem klassificeras korrigeringar efter kategori och allvarlighetsgrad. Vanliga värden för kategorin är: **security**, **rekommenderas**, **valfritt**, **funktionen**, **dokumentet**, eller **yast**.
Vanliga värden för allvarlighetsgrad är: **kritiska**, **viktiga**, **måttlig**, **låg**, eller **Ospecificerad**.

Den **zypper** kommandot söker bara efter de uppdateringar som behöver dina installerade paket. Du kan till exempel använda det här kommandot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Du kan lägga till parametern `--dry-run` att testa uppdateringen utan att faktiskt uppdatering av systemet.


### <a name="disk-setup"></a>Disk-installationen
Rot-filsystem i en Linux-VM på Azure har en storleksbegränsning. Därför är det nödvändigt att koppla ytterligare diskutrymme till en Azure virtuell dator för att köra SAP. För SAP-programservern Azure virtuella datorer vara användning av Azure standardlagringsdiskar tillräckliga. För SAP HANA DBMS virtuella Azure-datorer är dock att använda Azure Premium Storage-diskar för produktions- och icke-produktion implementeringar obligatoriskt.

Utifrån den [lagringskrav för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), föreslås följande Azure Premium Storage-konfiguration: 

| SKU FÖR VIRTUELL DATOR | RAM |  / hana/data och/hana/log <br /> stripe används med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

I den föreslagna diskkonfigurationen placeras HANA datavolym och loggvolymen på samma uppsättning Azure premium storage-diskar stripe med LVM eller MDADM. Du behöver inte definiera alla RAID-nivå för redundans eftersom Azure Premium Storage behåller tre bilder på diskarna för redundans. För att säkerställa att du konfigurerar tillräckligt med lagringsutrymme, finns det [lagringskrav för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) och [Update Guide och SAP HANA Server Installation](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Även överväga annan virtuell hårddisk (VHD) dataflöde volymer av olika Azure premium storage-diskar som beskrivs i [högpresterande Premium Storage och hanterade diskar för virtuella datorer](../../windows/disks-types.md). 

Du kan lägga till mer premium-lagringsdiskar HANA DBMS virtuella datorer för att lagra säkerhetskopior av transaktionsloggen.

Mer information om de två huvudsakliga verktyg som används för att konfigurera striping finns i följande artiklar:

* [Konfigurera programvaru-RAID på Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurera LVM på en Linux-dator i Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Läs mer på kopplar diskar till virtuella Azure-datorer som kör Linux som en gäst-OS, [lägga till en disk i en Linux VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure premium SSD-enheter kan du definiera diskcachelagringstypen lägen. För stripe håller /hana/data och /hana/log, ska diskcachelagring vara inaktiverat. För andra volymer (diskar), cachelagring läge ska vara inställd på **ReadOnly**.

Du hittar exempel JSON-mallar för att skapa virtuella datorer genom att gå till [Azure-Snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates).
Vm-enkel-sles-mallen är en grundläggande mall. Den innehåller ett avsnitt för lagring, med en ytterligare 100 GB data-disk. Den här mallen kan användas som bas. Du kan anpassa mallen till din specifika konfiguration.

>[!Note]
>Det är viktigt att ansluta Azure storage-disk med hjälp av en UUID som beskrivs i [kör SAP NetWeaver på Microsoft Azure virtuella SUSE Linux-datorer](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I testmiljön har två Azure standardlagringsdiskar anslutna till SAP applikationsserver-dator, enligt följande skärmbild. En disk lagras alla SAP-program (inklusive NetWeaver 7.5, SAP-Gränssnittet och SAP HANA) för installation. Den andra disken sett till att tillräckligt med ledigt utrymme skulle vara tillgänglig för ytterligare krav (till exempel säkerhetskopiering och testa data) och för katalogen /sapmnt (det vill säga SAP-profiler) som ska delas mellan alla virtuella datorer som tillhör samma SAP-landskap.

![SAP HANA server diskar fönstret visar två datadiskar och deras storlek](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel parametrar
SAP HANA kräver specifika Linux-kernel inställningar, som inte är en del av standard Azure gallery-bilder och måste anges manuellt. Parametrarna kan vara olika beroende på om du använder SUSE eller Red Hat. I SAP Notes ovan ger information om dessa parametrar. Skärmbilderna visas, har SUSE Linux 12 SP1 använts. 

SLES för SAP-program 12 GA- och SLES för SAP-program 12 SP1 har du ett nytt verktyg **anpassade adm**, som ersätter gammalt **sapconf** verktyget. En speciell profil för SAP HANA är tillgänglig för **anpassade adm**. Ange följande för att justera systemet för SAP HANA som en rotanvändare:

   `tuned-adm profile sap-hana`

Mer information om **anpassade adm**, finns i den [SUSE-dokumentationen om anpassade adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

I följande skärmbild ser du hur **anpassade adm** ändras den `transparent_hugepage` och `numa_balancing` värden, enligt inställningarna som krävs SAP HANA.

![Anpassade adm-verktyget ändras värdet enligt SAP HANA-inställningar som krävs](./media/hana-get-started/image005.jpg)

Om du vill göra inställningar för SAP HANA-kernel permanenta, använda **grub2** på SLES 12. Mer information om **grub2**går du till den [Configuration filstruktur](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen för.

Följande skärmbild visar hur inställningarna för kernel har ändrats i konfigurationsfilen och sedan kompileras med hjälp av **grub2 mkconfig**:

![Inställningarna för kernel har ändrats i konfigurationsfilen och kompileras med hjälp av grub2 mkconfig](./media/hana-get-started/image006.jpg)

Ett annat alternativ är att ändra inställningarna med hjälp av YaST och **startprogrammet** > **Kernel parametrar** inställningar:

![Inställningsfliken Kernel parametrar i YaST startprogrammet](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Filsystem
I följande skärmbild visas två filsystem som har skapats på SAP-appservern VM ovanpå två anslutna Azure standard storage-diskar. Båda filsystem är av typen XFS och är monterade /sapdata och /sapsoftware.

Det är inte obligatoriskt att strukturera ditt filsystem på det här sättet. Du har andra alternativ för att strukturera diskutrymmet. Den mest viktigt överväganden är att förhindra att filsystemet rot körs utanför lediga utrymmet.

![Två filsystem som skapats på SAP applikationsserver-dator](./media/hana-get-started/image008.jpg)

Om SAP HANA DB VM under installationen av databasen när du använder SAPinst (SWPM) och **vanliga** installationsalternativ, allt installeras under /hana och /usr/sap. Standardplatsen för SAP HANA-loggsäkerhetskopiering är under /usr/sap. Igen, eftersom det är viktigt att förhindra att filsystemet rot slut på lagringsutrymme och se till att det finns tillräckligt med ledigt utrymme under /hana och /usr/sap innan du installerar SAP HANA med hjälp av SWPM.

En beskrivning av standard filsystemet layouten för SAP HANA, finns i den [Update Guide och SAP HANA Server Installation](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Ytterligare filsystem som skapats på SAP applikationsserver-dator](./media/hana-get-started/image009.jpg)

När du installerar SAP NetWeaver på en standard SLES/SLES för SAP-program 12 Azure-galleriet bilden visas ett meddelande som säger att det finns inga växlingsutrymme som visas i följande skärmbild. Om du vill ignorera det här meddelandet, du kan manuellt lägga till en växlingsfil med hjälp av **dd**, **mkswap**, och **swapon**. Läs, Sök efter ”lägga till en växlingsfil manuellt” i den [med YaST Partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen för.

Ett annat alternativ är att konfigurera växlingsutrymme genom att använda Linux VM-agenten. Mer information finns i den [Användarguide för Azure Linux Agent](../../extensions/agent-linux.md).

![Popup-meddelande som talar om att det finns inte tillräckligt med växlingsutrymme](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>I/etc/hosts-filen
Innan du börjar installera SAP kan du kontrollera att du inkluderar värdnamn och IP-adresser för de virtuella SAP-datorerna i filen/etc/hosts. Distribuera alla SAP virtuella datorer inom en Azure-nätverk och sedan använda de interna IP-adresserna som visas här:

![Värdnamn och IP-adresser för de virtuella datorerna SAP som anges i filen/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Fstab-filen/etc /

Det kan vara bra att lägga till den **nofail** parameter fstab-filen. Det här sättet om något går fel med diskar, den virtuella datorn inte låser sig i startprocessen. Men kom ihåg att ytterligare diskutrymme inte kanske är tillgänglig och processer blir fullt rot-filsystemet. SAP HANA startar inte om /hana saknas.

![Lägg till parametern nofail i fstab-filen](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Den grafiska gör väldigt lätt skrivbordet på SLES 12/SLES för SAP-program 12
Det här avsnittet innehåller följande avsnitt:

* Installera desktop gör väldigt lätt och xrdp på SLES 12/SLES för SAP-program 12
* Kör Java-baserade SAP MC med Firefox i SLES 12/SLES för SAP-program 12

Du kan också använda alternativ såsom Xterminal eller VNC (som inte beskrivs i den här guiden).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installera desktop gör väldigt lätt och xrdp på SLES 12/SLES för SAP-program 12
Om du har en bakgrund för Windows kan använda du enkelt en grafisk desktop direkt i SAP Linux-datorer att köra Firefox, SAPinst, SAP-Gränssnittet, SAP MC eller HANA Studio och ansluta till den virtuella datorn via Remote Desktop Protocol (RDP) från en Windows-dator. Beroende på företagets principer om att lägga till grafiska användargränssnitt till produktions- och icke-produktion Linux-baserade system kan du installera gör väldigt lätt på din server. Installera gör väldigt lätt skrivbordet på en Azure-SLES 12/SLES för SAP-program 12 virtuell dator:

1. Installera gör väldigt lätt skrivbordet genom att ange följande kommando (till exempel i ett PuTTY fönster):

   `zypper in -t pattern gnome-basic`

2. Installera xrdp för att tillåta en anslutning till den virtuella datorn via RDP:

   `zypper in xrdp`

3. Redigera /etc/sysconfig/windowmanager och inställd gör väldigt lätt hanteraren för standard-fönstret:

   `DEFAULT_WM="gnome"`

4. Kör **chkconfig** att se till att xrdp startas automatiskt efter en omstart:

   `chkconfig -level 3 xrdp on`

5. Om du har ett problem med RDP-anslutningen kan försöka starta om (från ett PuTTY fönster, till exempel):

   `/etc/xrdp/xrdp.sh restart`

6. Om en omstart för xrdp som nämns i föregående steg inte fungerar kan du söka efter en .pid-fil:

   `check /var/run` 

   Leta efter `xrdp.pid`. Om du hittar det kan ta bort den och försök att starta om igen.

### <a name="starting-sap-mc"></a>Från SAP MC
När du har installerat gör väldigt lätt skrivbordet kan från Firefox och grafiska Java-baserade SAP MC när du kör i en Azure-SLES 12/SLES för SAP-program 12 VM visas ett fel på grund av saknade Java-webbläsaren plugin-programmet.

URL: en att starta SAP-MC är `<server>:5<instance_number>13`.

Mer information finns i [startar den webbaserade SAP-hanteringskonsolen](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

I följande skärmbild visas ett felmeddelande som visas när den Java-webbläsartillägg saknas:

![Felmeddelande som anger saknas Java-webbläsartillägg](./media/hana-get-started/image013.jpg)

Ett sätt att lösa problemet är att installera det saknade plugin-programmet med hjälp av YaST, enligt följande skärmbild:

![Använder YaST för att installera plugin-programmet som saknas](./media/hana-get-started/image014.jpg)

När du anger URL: en för SAP Management-konsolen igen, visas ett meddelande där du uppmanas att aktivera plugin-programmet:

![Dialogrutan begär aktivering av plugin-programmet](./media/hana-get-started/image015.jpg)

Du kan också få ett felmeddelande om en saknad fil javafx.properties. Detta beror på behovet av Oracle Java 1.8 SAP GUI 7.4. (Se [SAP-kommentar 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Varken IBM Java-versionen eller openjdk paketet levereras med SLES/SLES för SAP-program 12 innehåller nödvändiga javafx.properties-filen. Lösningen är att hämta och installera Java SE 8 från Oracle.

Information om ett liknande problem med openjdk på openSUSE finns i diskussionstråd [SAPGui 7.4 Java för openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Manuell installation av SAP HANA: SWPM
Serie skärmbilderna i det här avsnittet visar viktiga steg för att installera 7.5 för SAP NetWeaver och SAP HANA SP12 när du använder SWPM (SAPinst). Som en del av en NetWeaver 7.5-installation installera SWPM även HANA-databas som en enskild instans.

I en testmiljö för exemplet installerade vi bara ett Advanced Business Application Programming (ABAP) app-servern. I följande skärmbild visas som vi använde den **Distributed System** alternativet att installera ASCS och primära server-instanser i en virtuell dator i Azure och SAP HANA som databassystem i en annan virtuell Azure-dator.

![ASCS och primära server-instanser som installerats med hjälp av alternativet Distributed System](./media/hana-get-started/image012.jpg)

När ASCS-instansen har installerats på applikationsserver-dator och är inställd på ”green” i hanteringskonsolen för SAP (visas i följande skärmbild), måste katalogen /sapmnt (inklusive SAP användarprofilens katalog) delas med SAP HANA-Databasobjekt server-dator. Installationssteget DB behöver åtkomst till den här informationen. Det bästa sättet att ge åtkomst är att använda NFS, som kan konfigureras med hjälp av YaST.

![SAP-hanteringskonsolen som visar ASCS-instans installerad på applikationsserver-dator och inställd på ”green”](./media/hana-get-started/image016.jpg)

På appservern VM katalogen /sapmnt bör delas via NFS med hjälp av den **rw** och **no_root_squash** alternativ. Standardvärdena är **ro** och **root_squash**, vilket kan leda till problem när du installerar databasinstansen.

![Dela katalogen /sapmnt via NFS med hjälp av alternativen rw och no_root_squash](./media/hana-get-started/image017b.jpg)

Så som på nästa skärmbild visas /sapmnt resursen från appservern VM måste konfigureras på SAP HANA-Databasobjekt server-dator med hjälp av **NFS-klienten** (och YaST).

![/Sapmnt resursen konfigureras med hjälp av NFS-klienten](./media/hana-get-started/image018b.jpg)

Att utföra en distribuerad NetWeaver 7.5-installation (**databasinstansen**), som visas i följande skärmbild, logga in till SAP HANA-Databasobjekt server-dator och starta SWPM.

![Installera en databasinstans genom att logga in till SAP HANA-Databasobjekt server-dator och starta SWPM](./media/hana-get-started/image019.jpg)

När du har valt **vanliga** installation och sökvägen till installationsmediet, ange ett DB-SID, värdnamn, instansnummer och DB lösenord.

![SAP HANA system administratör logga in databas](./media/hana-get-started/image035b.jpg)

Ange lösenordet för DBACOCKPIT schemat:

![Lösenord-rutan för DBACOCKPIT-schema](./media/hana-get-started/image036b.jpg)

Ange en fråga för SAPABAP1 schemat lösenord:

![Ange en fråga för SAPABAP1 schemat lösenordet](./media/hana-get-started/image037b.jpg)

När varje uppgift är slutförd visas en grön bock bredvid varje fas av installationsprocessen DB. Meddelandet ”körning av... Databas instansen har slutförts ”visas.

![Uppgift slutförd fönstret bekräftelsemeddelande](./media/hana-get-started/image023.jpg)

Efter installationen bör SAP-hanteringskonsolen också visa DB-instans som ”green” och visa en fullständig lista över SAP HANA-processer (hdbindexserver, hdbcompileserver och så vidare).

![SAP-hanteringskonsolen fönstret med listan över SAP HANA-processer](./media/hana-get-started/image024.jpg)

Följande skärmbild visar delarna av filstruktur under katalogen /hana/shared SWPM skapas under installationen av HANA. Eftersom det finns inget alternativ för att ange en annan sökväg, är det viktigt att montera ytterligare diskutrymme under katalogen /hana innan installationen av SAP HANA med hjälp av SWPM. Detta förhindrar att filsystemet rot slut på ledigt utrymme.

![Filstruktur för /hana/shared directory skapades under HANA-installation](./media/hana-get-started/image025.jpg)

Den här skärmbilden visar filstrukturen för /usr/sap katalogen:

![/ Usr/sap filen katalogstrukturen](./media/hana-get-started/image026.jpg)

Det sista steget i den distribuerade ABAP-installationen är att installera den primära server-instansen:

![ABAP installation som visar primära server-instans som det sista steget](./media/hana-get-started/image027b.jpg)

När den primära server-instansen och SAP-Gränssnittet har installerats kan du använda den **DBA Cockpit** transaktion att bekräfta att SAP HANA-installationen har slutförts korrekt:

![DBA Cockpit-fönstret som bekräftar att installationen lyckades](./media/hana-get-started/image028b.jpg)

Som ett sista steg kanske du vill först installerar HANA Studio i SAP applikationsserver-dator och sedan ansluta till SAP HANA-instans som körs på DB-server-dator:

![Installera SAP HANA Studio i SAP applikationsserver VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Manuell installation av SAP HANA: HDBLCM
Förutom att installera SAP HANA som en del av en distribuerad installation med hjälp av SWPM, kan du installera fristående HANA först med hjälp av HDBLCM. Du kan sedan installera SAP NetWeaver 7.5, till exempel. Skärmbilderna i det här avsnittet visar hur processen fungerar.

Mer information om verktyget HANA HDBLCM finns:

* [Välja rätt SAP HANA HDBLCM för uppgiften](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Hanteringsverktyg för SAP HANA-livscykel](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)
* [Guide för uppdatering och SAP HANA serverinstallation](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Att undvika problem med en grupp-ID: T standardinställningen för den `\<HANA SID\>adm user` (som skapats av verktyget HDBLCM) definiera en ny grupp med namnet `sapsys` med hjälp av grupp-ID `1001` innan du installerar SAP HANA via HDBLCM:

![Ny grupp ”sapsys” definieras med hjälp av grupp-ID 1001](./media/hana-get-started/image030.jpg)

När du startar HDBLCM första gången, visas en enkel start-menyn. Välj objekt 1, **installera nya**, enligt följande skärmbild:

![”Installera nya system” alternativ i fönstret HDBLCM start](./media/hana-get-started/image031.jpg)

Följande skärmbild visar de viktiga alternativ som du har valt tidigare.

> [!IMPORTANT]
> Kataloger som namnges för HANA log och datavolymer, samt installationssökvägen (/ hana/delas i det här exemplet) och /usr/sap, får inte vara en del av filsystemet rot. Dessa kataloger som hör till Azure-datadiskar som kopplats till den virtuella datorn (beskrivs i avsnittet ”Disk-installation”). Den här metoden förhindrar filsystemet rot slut på utrymme. I följande skärmbild ser du att systemadministratören HANA har användar-ID `1005` och är en del av den `sapsys` grupp (ID `1001`) som definierades innan installationen.

![Lista över alla nyckelkomponenter för SAP HANA som tidigare har valt](./media/hana-get-started/image032.jpg)

Du kan kontrollera den `\<HANA SID\>adm user` (`azdadm` i följande skärmbild) information om i/etc/passwd katalogen:

![HANA \<HANA SID\>adm användarinformation som visas i/etc/passwd katalogen](./media/hana-get-started/image033.jpg)

När du har installerat SAP HANA med hjälp av HDBLCM ser filstrukturen i SAP HANA Studio enligt följande skärmbild. Schemat SAPABAP1 som innehåller alla SAP NetWeaver-tabeller är inte tillgängligt ännu.

![Filstruktur för SAP HANA i SAP HANA-Studio](./media/hana-get-started/image034.jpg)

När du har installerat SAP HANA kan du installera SAP NetWeaver ovanpå den. I följande skärmbild visas installationen har utförts som en distribuerad installation med hjälp av SWPM (som beskrivs i föregående avsnitt). När du installerar databasinstansen med hjälp av SWPM kan ange du samma data med hjälp av HDBLCM (till exempel värdnamn, HANA SID och instansnummer). SWPM sedan använder den befintliga HANA-installationen och lägger till flera scheman.

![En distribuerad installation utförs med hjälp av SWPM](./media/hana-get-started/image035b.jpg)

Följande skärmbild visar installationssteget SWPM där du anger DBACOCKPIT schemadata:

![SWPM installationssteget där DBACOCKPIT schemadata anges](./media/hana-get-started/image036b.jpg)

Ange information om SAPABAP1 schemat:

![Att ange information om SAPABAP1 schemat](./media/hana-get-started/image037b.jpg)

Du kan se SAPABAP1 schemat i SAP HANA-Studio när instansen för SWPM databasinstallation har slutförts:

![SAPABAP1 schemat i SAP HANA-Studio](./media/hana-get-started/image038b.jpg)

Slutligen, efter SAP applikationsserver och SAP GUI-installationer har slutförts, du kan kontrollera HANA DB-instans med hjälp av den **DBA Cockpit** transaktion:

![HANA-DB-instans som verifierats med DBA Cockpit-transaktion](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Nedladdning av programvara för SAP
Du kan ladda ned programvara från SAP Service Marketplace, enligt följande skärmbilder.

Ladda ned NetWeaver 7.5 för Linux/HANA:

 ![SAP Service Installation och uppgradering fönster för att ladda ned NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Ladda ner HANA SP12 plattform versionen:

 ![SAP Service Installation och uppgradering fönster för att ladda ned HANA SP12 plattform Edition](./media/hana-get-started/image002.jpg)

