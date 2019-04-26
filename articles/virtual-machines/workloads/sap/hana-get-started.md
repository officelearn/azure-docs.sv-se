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
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60205051"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Snabbstart: Manuell installation av en instans SAP HANA på Azure Virtual Machines
## <a name="introduction"></a>Introduktion
Den här guiden kan du ställa in en enda instans SAP HANA på Azure virtuella datorer när du installerar 7.5 för SAP NetWeaver och SAP HANA 1.0 SP12 manuellt. Den här guiden fokuserar på är om hur du distribuerar SAP HANA på Azure. Det ersätter inte SAP-dokumentationen. 

> [!NOTE]
> Den här guiden beskriver distributioner av SAP HANA i Azure virtuella datorer. Information om hur du distribuerar SAP HANA på stora HANA-instanser finns i [Använd SAP på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Nödvändiga komponenter
Den här guiden förutsätter att du är bekant med denna infrastruktur som en tjänst (IaaS)-grunder som:
 * Hur du distribuerar virtuella datorer (VM) eller virtuella nätverk via Azure portal eller PowerShell.
 * Azures plattformsoberoende kommandoradsgränssnitt (CLI), som innehåller alternativet att använda mallar i JavaScript Object Notation (JSON).

Den här guiden förutsätter också att du är bekant med:
* SAP HANA och SAP NetWeaver och hur du installerar dem lokalt.
* Så här att installera och använda SAP HANA och instanser av SAP-programmet på Azure.
* Följande koncept och procedurer:
   * Planera för distribution av SAP på Azure, inklusive Azure Virtual Network planerings- och användning av Azure Storage. Se [SAP NetWeaver på Azure Virtual Machines - guiden för planering och implementering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Distribution av principer och sätt att distribuera virtuella datorer i Azure. Se [Azure Virtual Machines-distribution för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Hög tillgänglighet för SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) och sätta replikering Server (ERS) på Azure. Se [hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Information om hur du förbättrar effektiviteten i en multi-SID-installation av ASCS/SCS på Azure. Se [skapa en konfiguration för SAP NetWeaver – flera SÄKERHETSIDENTIFIERARE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principer för att köra SAP NetWeaver baserat på Linux-drivna virtuella datorer i Azure. Se [kör SAP NetWeaver på Microsoft Azure SUSE Linux Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Den här guiden innehåller specifika inställningar för Linux på Azure virtuella datorer. Du får också information om hur du kopplar korrekt Azure storage-diskar till virtuella Linux-datorer.

Vilka typer av virtuella Azure-datorer som kan användas för produktionsscenarier visas i den [SAP-dokumentationen för IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Scenarier med icke-produktionsmiljöer finns en mängd olika interna Azure VM-typer.
Mer information om konfiguration av virtuell dator och åtgärder finns i [konfigurationer för SAP HANA-infrastruktur och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
Hög tillgänglighet för SAP HANA, se [SAP HANA, hög tillgänglighet för Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Om du vill hämta en instans av SAP HANA eller S/4HANA eller BW/4HANA-system som distribueras snabbt kan du använda [SAP Cloud Appliance Library](https://cal.sap.com). Det finns mer information om hur du distribuerar ett S/4HANA-system via SAP Cloud Appliance Library på Azure, till exempel i [den här guiden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Allt du behöver är en Azure-prenumeration och en SAP-användare som kan registreras med SAP Cloud Appliance Library.

## <a name="additional-resources"></a>Ytterligare resurser
### <a name="sap-hana-backup"></a>SAP HANA-säkerhetskopia
Information om hur du säkerhetskopierar SAP HANA-databaser på virtuella Azure-datorer finns i:
* [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
Information om hur du använder SAP Cloud Appliance Library distribuera S/4HANA eller BW/4HANA finns i [distribuera SAP S/4HANA eller BW/4HANA på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA – stöd för operativsystem
Information om operativsystem som stöds av SAP HANA, finns i [SAP anteckning 2235581 – SAP HANA: Operativsystem som stöds](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuella Azure-datorer stöder endast en delmängd av dessa operativsystem. Följande operativsystem stöds för att distribuera SAP HANA på Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

För ytterligare SAP-dokumentationen om SAP HANA och olika Linux-operativsystem, se:

* [SAP-kommentar 171356: SAP-program på Linux: Allmän information](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP-kommentar 1944799: SAP HANA-riktlinjer för installation av operativsystemet SLES](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html).
* [SAP-kommentar 2205917: SAP HANA-Databasobjekt rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP-kommentar 1391070: Linux UUID lösningar](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP Note 2009879: SAP HANA-riktlinjer för operativsystemet Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP-kommentar 2292690: SAP HANA DB: Rekommenderade inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP övervakning i Azure
Information om övervakning av SAP i Azure:

* [SAP anteckning 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) beskrivs SAP förbättrad övervakning med virtuella Linux-datorer på Azure. 
* [SAP anteckning 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) beskriver information om SAPOSCOL på Linux. 
* [SAP anteckning 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) diskuterar nyckelvärden övervakning för SAP på Microsoft Azure.

### <a name="azure-vm-types"></a>Azure VM-typer
Azure VM-typer och stöd för SAP-arbetsbelastningsscenarier används med SAP HANA finns dokumenterade i [SAP-certifierad IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure VM-typer som är certifierade av SAP för SAP NetWeaver och S/4HANA programnivån finns dokumenterade i [SAP anteckning 1928533: SAP-program i Azure: Produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> SAP-Linux Azure-integration stöds endast på Azure Resource Manager och inte den klassiska distributionsmodellen. 

## <a name="manual-installation-of-sap-hana"></a>Manuell installation av SAP HANA

> [!IMPORTANT]
> Kontrollera att Operativsystemet som du väljer är SAP-certifierade för SAP HANA på de specifika VM-typer som du använder. Listan över SAP HANA-certifierade VM-typer och OS-versioner för dessa typer av virtuella datorer kan sökas i [IaaS-plattformar för SAP HANA-certifierade](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka på information om den typ av virtuell dator i listan om du vill hämta den fullständiga listan med SAP HANA – stöd för OS-versioner för en viss typ av virtuell dator. Vi använde en SUSE Linux Enterprise Server (SLES) OS-version som inte stöds av SAP för SAP HANA på virtuella datorer i M-serien för det här exemplet i det här dokumentet.
>

Den här guiden beskriver hur du manuellt installera SAP HANA på Azure virtuella datorer på två olika sätt:

* Använda SAP Software etablering Manager (SWPM) som en del av en distribuerad installation NetWeaver i ”installera databasinstansen” steg.
* Använd verktyget manager (HDBLCM) livscykel för SAP HANA-databas och sedan installera NetWeaver.

Du kan också använda SWPM för att installera alla komponenter, till exempel SAP HANA, SAP-programservern och ASCS-instans i en enda virtuell dator. Stegen beskrivs i det här [SAP HANA-bloggmeddelande](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Det här alternativet är inte beskrivs i den här snabbstartsguiden, men de problem som du måste tänka på är desamma.

Innan du startar en installation, rekommenderar vi att du läser den ”Förbered Azure-datorer för manuell installation av SAP HANA” senare i den här handboken. Då kan du förhindra flera grundläggande fel som kan uppstå när du använder bara en virtuell Azure-dator standardkonfiguration.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Viktiga steg för SAP HANA-installationen när du använder SAP SWPM
Det här avsnittet innehåller viktiga steg för att installera en manuell, enskild instans SAP HANA när du använder SAP SWPM för att utföra en distribuerad SAP NetWeaver 7.5-installation. De enskilda stegen beskrivs i detalj i skärmbilder senare i den här guiden.

1. Skapa ett Azure-nätverk som innehåller två test virtuella datorer.
2. Distribuera två virtuella Azure-datorer med operativsystem enligt Azure Resource Manager-modellen. Det här exemplet använder SUSE Linux Enterprise Server- och SLES för SAP-program 12 SP1. 
3. Koppla två Azure standard eller premium-lagringsdiskar, till exempel 75 GB eller 500 GB diskar till programservern VM.
4. Koppla premium storage-diskar till HANA-Databasobjekt server-dator. Mer information finns i avsnittet ”Disk-installation” senare i den här guiden.
5. Beroende på storleken eller dataflödet krav, lägga till flera diskar. Skapa sedan stripe-volymer. Använd antingen logiska volume management (LVM) eller ett verktyg för administration (mdadm) av flera enheter på operativsystemsnivån inuti den virtuella datorn.
6. Skapa XFS filsystem på anslutna diskar eller logiska volymer.
7. Montera de nya XFS filsystem på operativsystemsnivån. Använd ett filsystem för SAP-program. Använda andra filsystemet /sapmnt katalog-och säkerhetskopior, till exempel. Montera filsystem XFS på premium-lagringsdiskar som /hana och /usr/sap på SAP HANA-Databasobjekt-servern. Den här processen är nödvändigt att förhindra att filsystemet rot fyller upp. Rot-filsystemet är inte stora på Azure Virtual Machines för Linux. 
8. Ange lokala IP-adresserna för testet virtuella datorer i/etc/hosts-filen.
9. Ange den **nofail** parameter i fstab-filen/etc /.
10. Ange parametrarna för Linux-kernel enligt Linux OS-versionen som du använder. Mer information finns i SAP Notes om HANA och avsnittet ”Kernel parametrar” i den här guiden.
11. Lägg till växlingsutrymme.
12. Du kan också installera en grafisk desktop på virtuella datorer för testning. Annars kan du använda en fjärrinstallation av SAPinst.
13. Hämta SAP-program från SAP Service Marketplace.
14. Installera SAP ASCS-instans på applikationsserver-dator.
15. Dela katalogen /sapmnt bland testet virtuella datorer med hjälp av NFS. Programservern VM är den NFS-servern.
16. Installera den databasinstans som innehåller HANA, genom att använda SWPM på DB-server-dator.
17. Installera den primära servern (PROVIDERADRESSER) på VM-programservern.
18. Starta SAP-hanteringskonsolen (SAP MC). Ansluta med SAP-Gränssnittet eller HANA Studio, till exempel.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Viktiga steg för SAP HANA-installationen när du använder HDBLCM
Det här avsnittet innehåller viktiga steg för att installera en manuell, enskild instans SAP HANA när du använder SAP HDBLCM för att utföra en distribuerad SAP NetWeaver 7.5-installation. De enskilda stegen beskrivs i detalj i skärmbilderna i den här guiden.

1. Skapa ett Azure-nätverk som innehåller två test virtuella datorer.
2. Distribuera två virtuella Azure-datorer med operativsystem enligt Azure Resource Manager-modellen. Det här exemplet använder SLES- och SLES för SAP-program 12 SP1.
3. Koppla två Azure standard eller premium-lagringsdiskar, till exempel 75 GB eller 500 GB diskar till appservern VM.
4. Koppla premium storage-diskar till HANA-Databasobjekt server-dator. Mer information finns i avsnittet ”Disk-installation” senare i den här guiden.
5. Beroende på storleken eller dataflödet krav, lägga till flera diskar. Skapa stripe-volymer genom att använda hantering av logisk volym eller ett mdadm verktyg på operativsystemsnivån inuti den virtuella datorn.
6. Skapa XFS filsystem på anslutna diskar eller logiska volymer.
7. Montera de nya XFS filsystem på operativsystemsnivån. Använd ett filsystem för SAP-program. Använda andra filsystemet /sapmnt katalog-och säkerhetskopior, till exempel. Montera filsystem XFS på premium-lagringsdiskar som /hana och /usr/sap på SAP HANA-Databasobjekt-servern. Den här processen är nödvändigt för att förhindra att filsystemet rot fyller upp. Rot-filsystemet är inte stora på Azure Virtual Machines för Linux.
8. Ange lokala IP-adresserna för testet virtuella datorer i/etc/hosts-filen.
9. Ange den **nofail** parameter i fstab-filen/etc /.
10. Ange kernel-parametrarna enligt Linux OS-versionen som du använder. Mer information finns i SAP Notes om HANA och avsnittet ”Kernel parametrar” i den här guiden.
11. Lägg till växlingsutrymme.
12. Du kan också installera en grafisk desktop på virtuella datorer för testning. Annars kan du använda en fjärrinstallation av SAPinst.
13. Hämta SAP-program från SAP Service Marketplace.
14. Skapa en grupp, sapsys, med grupp-ID 1001 på HANA-Databasobjekt server-dator.
15. Installera SAP HANA på DB-server-dator med hjälp av HANA database livscykel manager.
16. Installera SAP ASCS-instans på applikationsserver-dator.
17. Dela katalogen /sapmnt bland testet virtuella datorer med hjälp av NFS. Programservern VM är den NFS-servern.
18. Installera den databasinstans som innehåller HANA, genom att använda SWPM på HANA-Databasobjekt server-dator.
19. Installera den primära servern på VM-programservern.
20. Starta SAP MC. Ansluta via SAP-Gränssnittet eller HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Förbereda virtuella Azure-datorer för en manuell installation av SAP HANA
Det här avsnittet innehåller följande avsnitt:

* OS-uppdateringar
* Disk-installationen
* Kernel parametrar
* Filsystem
* I/etc/hosts-filen
* Fstab-filen/etc /

### <a name="os-updates"></a>OS-uppdateringar
Sök efter Linux OS-uppdateringar och korrigeringar innan du installerar ytterligare programvara. Genom att installera en uppdatering kan du undvika ett anrop till supporten.

Kontrollera att du använder:
* SUSE Linux Enterprise Server för SAP-program.
* Red Hat Enterprise Linux för SAP-program eller Red Hat Enterprise Linux for SAP HANA. 

Om du inte redan gjort det, kan du registrera OS-distributionen med Linux prenumerationen från Linux-försäljare. SUSE har OS-avbildningar för SAP-program som redan omfattar tjänster och som är registrerade automatiskt.

Här är ett exempel på hur du kan söka efter tillgängliga uppdateringar för SUSE Linux med hjälp av den **zypper** kommando:

 `sudo zypper list-patches`

Beroende på vilken typ av problem klassificeras korrigeringar efter kategori och allvarlighetsgrad. Vanliga värden för kategorin är: 
- Säkerhet
- Rekommenderas
- Valfri
- Funktion
- Dokument
- yast

Vanliga värden för allvarlighetsgrad är:

- Kritisk
- Viktigt
- Mellan
- Låg
- Ospecificerat

Den **zypper** kommandot söker bara efter de uppdateringar som behöver dina installerade paket. Du kan till exempel använda det här kommandot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Du kan lägga till parametern `--dry-run` att testa uppdateringen utan att faktiskt uppdatering av systemet.


### <a name="disk-setup"></a>Disk-installationen
Rot-filsystem i en Linux-VM på Azure har en storleksbegränsning. Därför måste du koppla ytterligare diskutrymme till en Azure-dator för att köra SAP. För SAP-programservern Azure virtuella datorer vara användning av Azure standardlagringsdiskar tillräckligt. För SAP HANA DBMS virtuella Azure-datorer är användning av Azure premium storage-diskar för implementeringar av produktions- och icke-produktionsmiljöer obligatoriskt.

Utifrån den [lagringskrav för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), föreslås följande konfiguration i Azure premium storage: 

| SKU FÖR VIRTUELL DATOR | RAM |  / hana/data och/hana/log <br /> stripe används med LVM eller mdadm | / hana/delade | / Root volym | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

I den föreslagna diskkonfigurationen placeras HANA datavolym och loggvolymen på samma uppsättning Azure premium storage-diskar stripe med LVM eller mdadm. Du behöver inte definiera alla RAID-nivå för redundans eftersom Azure premium storage behåller tre bilder på diskarna för redundans. 

För att säkerställa att du konfigurerar tillräckligt med lagringsutrymme, se [lagringskrav för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) och [SAP HANA server installation och uppdatering guiden](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Även överväga annan virtuell hårddisk (VHD) dataflöde volymer av olika Azure premium storage-diskar som beskrivs i [högpresterande premium storage och hanterade diskar för virtuella datorer](../../windows/disks-types.md). 

Du kan lägga till mer premium-lagringsdiskar HANA DBMS virtuella datorer att lagra säkerhetskopior av transaktionsloggen.

Mer information om de två huvudsakliga verktyg som används för att konfigurera striping finns:

* [Konfigurera programvaru-RAID på Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Konfigurera LVM på en Linux-dator i Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Läs mer om hur du kopplar diskar till virtuella Azure-datorer som kör Linux som en gäst-OS, [lägga till en disk i en Linux VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Du kan definiera diskcachelagringstypen lägen med Azure premium SSD. Inaktivera diskcachelagring för stripe uppsättningen som innehåller /hana/data och /hana/log. För andra volymer, det vill säga diskar, konfigurera cachelagring läget till **ReadOnly**.

Du hittar exempel JSON-mallar för att skapa virtuella datorer i den [Azures snabbstartsmallar](https://github.com/Azure/azure-quickstart-templates).
Vm-enkel-sles-mallen är en grundläggande mall. Den innehåller ett avsnitt för lagring, med en ytterligare 100 GB data-disk. Använd den här mallen som utgångspunkt. Du kan anpassa mallen till din specifika konfiguration.

> [!NOTE]
> Det är viktigt att ansluta Azure storage-disk med hjälp av en UUID som beskrivs i [kör SAP NetWeaver på Microsoft Azure virtuella SUSE Linux-datorer](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I testmiljön, är två Azure standardlagringsdiskar anslutna till SAP applikationsserver-dator, som visas i följande skärmbild. En disk lagrar alla SAP programvaran, som NetWeaver 7.5, SAP-Gränssnittet och SAP HANA för installation. Den andra disken garanterar att tillräckligt med ledigt utrymme är tillgängligt för ytterligare krav. Säkerhetskopiering och testa data och katalogen /sapmnt, det vill säga SAP profiler, måste till exempel delas av alla virtuella datorer som tillhör samma SAP-landskap.

![SAP HANA server diskar fönstret visar två datadiskar och deras storlek](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel parametrar
SAP HANA kräver specifika inställningar för Linux-kernel. De här inställningarna för kernel inte ingår i standard Azure gallery-bilder och måste anges manuellt. Parametrarna kan vara olika beroende på om du använder SUSE eller Red Hat. SAP-information som visas tidigare ger information om dessa parametrar. Skärmbilderna visas, har SUSE Linux 12 SP1 använts. 

SLES för SAP-program 12 allmän tillgänglighet och SLES för SAP-program 12 SP1 har du ett nytt verktyg **anpassade adm**, som ersätter gammalt **sapconf** verktyget. En speciell profil för SAP HANA är tillgänglig för **anpassade adm**. Ange följande profil för att justera systemet för SAP HANA som en rotanvändare:

   `tuned-adm profile sap-hana`

Mer information om **anpassade adm**, finns i den [SUSE-dokumentationen om anpassade adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

I följande skärmbild ser du hur **anpassade adm** ändras den `transparent_hugepage` och `numa_balancing` värden, enligt inställningarna som krävs SAP HANA:

![Anpassade adm-verktyget ändras värdet enligt SAP HANA-inställningar som krävs](./media/hana-get-started/image005.jpg)

Om du vill göra inställningar för SAP HANA-kernel permanenta, använda **grub2** på SLES 12. Mer information om **grub2**, finns i den [Configuration filstruktur](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen för.

Följande skärmbild visar hur inställningarna för kernel har ändrats i konfigurationsfilen och sedan kompileras med hjälp av **grub2 mkconfig**:

![Inställningarna för kernel har ändrats i konfigurationsfilen och kompileras med hjälp av grub2 mkconfig](./media/hana-get-started/image006.jpg)

Ett annat alternativ är att ändra inställningarna med hjälp av YaST och **startprogrammet** > **Kernel parametrar** inställningar:

![Inställningsfliken Kernel parametrar i YaST startprogrammet](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Filsystem
I följande skärmbild visas två filsystem som har skapats på SAP-appservern VM ovanpå två anslutna Azure standard storage-diskar. Båda filsystem är av typen XFS och är monterade /sapdata och /sapsoftware.

Det är inte obligatoriskt att strukturera ditt filsystem på så sätt. Du har andra alternativ för hur du ska strukturera diskutrymmet. Den mest viktigt överväganden är att förhindra att filsystemet rot körs utanför lediga utrymmet.

![Två filsystem som skapats på SAP applikationsserver-dator](./media/hana-get-started/image008.jpg)

För SAP HANA DB VM, under installationen av en databas, när du använder SAPinst med SWPM och **vanliga** installationsalternativ, allt installeras under /hana och /usr/sap. Standardplatsen för SAP HANA-loggsäkerhetskopiering är under /usr/sap. Igen, är det viktigt att förhindra att filsystemet rot slut på lagringsutrymme. Se till att det finns tillräckligt med ledigt diskutrymme under /hana och /usr/sap innan du installerar SAP HANA med hjälp av SWPM.

En beskrivning av standard filsystemet layouten för SAP HANA, finns i den [SAP HANA server installation och uppdatering guiden](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Ytterligare filsystem som skapats på SAP applikationsserver-dator](./media/hana-get-started/image009.jpg)

När du installerar SAP NetWeaver på en standard SLES/SLES för SAP-program 12 Azure-galleriet bilden visar ett meddelande som säger att det finns inga växlingsutrymme som visas i följande skärmbild. Om du vill ignorera det här meddelandet, du kan manuellt lägga till en växlingsfil med hjälp av **dd**, **mkswap**, och **swapon**. Läs, Sök efter ”lägga till en växlingsfil manuellt” i den [med YaST partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen för.

Ett annat alternativ är att konfigurera växlingsutrymme genom att använda Linux VM-agenten. Mer information finns i [Användarguide för Azure Linux Agent](../../extensions/agent-linux.md).

![Popup-meddelande som talar om att det finns inte tillräckligt med växlingsutrymme](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>I/etc/hosts-filen
Innan du börjar installera SAP kan du kontrollera att du inkluderar värdnamn och IP-adresser för de virtuella SAP-datorerna i filen/etc/hosts. Distribuera alla SAP virtuella datorer inom en Azure-nätverk. Använd sedan de interna IP-adresserna som visas här:

![Värdnamn och IP-adresser för de virtuella datorerna SAP som anges i filen/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Fstab-filen/etc /

Det kan vara bra att lägga till den **nofail** parameter fstab-filen. Det här sättet om något går fel med diskar, den virtuella datorn inte svarar inte i startprocessen. Men kom ihåg att ytterligare diskutrymme inte kanske är tillgänglig och processer blir fullt rot-filsystemet. SAP HANA startar inte om /hana saknas.

![Lägg till parametern nofail i fstab-filen](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Den grafiska gör väldigt lätt skrivbordet på SLES 12/SLES för SAP-program 12
Det här avsnittet beskrivs hur du:

* Installera desktop gör väldigt lätt och xrdp på SLES 12/SLES för SAP-program 12.
* Kör Java-baserade SAP MC genom att använda Firefox på SLES 12/SLES för SAP-program 12.

Du kan också använda alternativ, till exempel Xterminal eller VNC, som inte beskrivs i den här guiden.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installera desktop gör väldigt lätt och xrdp på SLES 12/SLES för SAP-program 12
Om du har en bakgrund för Windows kan använda du enkelt en grafisk desktop direkt i SAP Linux-datorer för att köra Firefox, SAPinst, SAP-Gränssnittet, SAP MC eller HANA Studio. Du kan sedan ansluta till den virtuella datorn via Remote Desktop Protocol (RDP) från en Windows-dator. Beroende på företagets principer om att lägga till grafiska till produktions- och icke-produktionsmiljöer Linux-baserat system, kanske du vill installera gör väldigt lätt på servern. Följ dessa steg för att installera gör väldigt lätt skrivbordet på en Azure-SLES 12/SLES för SAP-program 12 virtuella datorn.

1. Installera gör väldigt lätt skrivbordet genom att ange följande kommando, till exempel i ett PuTTY fönster:

   `zypper in -t pattern gnome-basic`

2. Installera xrdp för att tillåta en anslutning till den virtuella datorn via RDP:

   `zypper in xrdp`

3. Redigera /etc/sysconfig/windowmanager och inställd gör väldigt lätt hanteraren för standard-fönstret:

   `DEFAULT_WM="gnome"`

4. Kör **chkconfig** att se till att xrdp startas automatiskt efter en omstart:

   `chkconfig -level 3 xrdp on`

5. Om du har ett problem med RDP-anslutning du försök starta om, till exempel från ett PuTTY fönster:

   `/etc/xrdp/xrdp.sh restart`

6. Om en omstart för xrdp som nämns i föregående steg inte fungerar kan du söka efter en .pid-fil:

   `check /var/run` 

   Leta efter `xrdp.pid`. Om du hittar det kan ta bort den och försök att starta om igen.

### <a name="start-sap-mc"></a>Starta SAP MC
När du har installerat gör väldigt lätt desktop starta grafiska Java-baserade SAP MC från Firefox. Om den körs i en Azure-SLES 12/SLES för SAP-program 12 VM, kan ett fel visas. Felet inträffar på grund av saknade Java webbläsartillägg.

URL: en att starta SAP-MC är `<server>:5<instance_number>13`.

Mer information finns i [startar den webbaserade SAP-hanteringskonsolen](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Följande skärmbild visas ett felmeddelande som visas när den Java-webbläsartillägg saknas:

![Felmeddelande som anger saknas Java-webbläsartillägg](./media/hana-get-started/image013.jpg)

Ett sätt att lösa problemet är att installera det saknade plugin-programmet med hjälp av YaST, enligt följande skärmbild:

![Använder YaST för att installera plugin-programmet som saknas](./media/hana-get-started/image014.jpg)

När du kör SAP Management-konsolens URL på nytt uppmanas du att aktivera plugin-programmet:

![Dialogrutan begär aktivering av plugin-programmet](./media/hana-get-started/image015.jpg)

Du kan också få ett felmeddelande om en saknad fil javafx.properties. Den relaterar till behovet av Oracle Java 1.8 SAP GUI 7.4. Mer information finns i [SAP anteckning 2059429](https://launchpad.support.sap.com/#/notes/2059424).
IBM Java-versionen och det openjdk-paket som levereras med SLES/SLES för SAP-program 12 omfattar inte den nödvändiga javafx.properties-filen. Lösningen är att hämta och installera Java SE 8 från Oracle.

Information om ett liknande problem med openjdk på openSUSE finns i diskussionstråd [SAPGui 7.4 Java för openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Manuell installation av SAP HANA: SWPM
Serie skärmbilderna i det här avsnittet visar viktiga steg för hur du installerar 7.5 för SAP NetWeaver och SAP HANA SP12 när du använder SWPM med SAPinst. Som en del av en NetWeaver 7.5-installation installera SWPM också HANA-databas som en enskild instans.

I en testmiljö för exemplet installerade vi en Advanced Business Application Programming (ABAP) app-servern. I följande skärmbild visas som vi använde den **Distributed System** alternativet för att installera ASCS och primära server-instanser i en virtuell Azure-dator. Vi använde SAP HANA som databassystem i en annan virtuell Azure-dator.

![ASCS och primära server-instanser som installerats med hjälp av alternativet Distributed System](./media/hana-get-started/image012.jpg)

När den ASCS-instansen har installerats på applikationsserver-dator, identifieras den med en grön ikon i SAP-hanteringskonsolen. Katalogen /sapmnt, vilket innefattar SAP användarprofilens katalog, måste delas med SAP HANA-Databasobjekt server-dator. Installationssteget DB behöver åtkomst till den här informationen. Det bästa sättet att ge åtkomst är att använda NFS, som kan konfigureras med hjälp av YaST.

![SAP-hanteringskonsolen som visar den ASCS-instans som är installerade på AppServer virtuell dator med en grön ikon](./media/hana-get-started/image016.jpg)

På appservern VM katalogen /sapmnt delas via NFS med hjälp av den **rw** och **no_root_squash** alternativ. Standardvärdena är **ro** och **root_squash**, vilket kan leda till problem när du installerar databasinstansen.

![Dela katalogen /sapmnt via NFS med hjälp av alternativen rw och no_root_squash](./media/hana-get-started/image017b.jpg)

Så som på nästa skärmbild visas /sapmnt resursen från appservern VM måste konfigureras på SAP HANA-Databasobjekt server-dator med hjälp av **NFS-klienten** och YaST:

![/Sapmnt resursen konfigureras med hjälp av NFS-klienten](./media/hana-get-started/image018b.jpg)

Att utföra en distribuerad NetWeaver 7.5 installation, det vill säga en **databasinstansen**, logga in till SAP HANA-Databasobjekt server-dator och börja SWPM:

![Installera en databasinstans genom att logga in till SAP HANA-Databasobjekt server-dator och starta SWPM](./media/hana-get-started/image019.jpg)

När du har valt **vanliga** installation och sökvägen till installationsmediet, ange ett DB-SID, värdnamn, instansnummer och DB lösenord:

![SAP HANA system administratör logga in databas](./media/hana-get-started/image035b.jpg)

Ange lösenordet för DBACOCKPIT schemat:

![Lösenord-rutan för DBACOCKPIT-schema](./media/hana-get-started/image036b.jpg)

Ange en fråga för SAPABAP1 schemat lösenord:

![Ange en fråga för SAPABAP1 schemat lösenordet](./media/hana-get-started/image037b.jpg)

När varje uppgift är slutförd visas en grön bock bredvid varje fas av installationsprocessen DB. Meddelandet ”körning av... Databas instansen har slutförts ”visas.

![Uppgift slutförd fönstret bekräftelsemeddelande](./media/hana-get-started/image023.jpg)

Efter installationen visas också DB-instans med en grön ikon i konsolen för hantering av SAP. En fullständig lista över SAP HANA-processer, till exempel hdbindexserver och hdbcompileserver visas.

![SAP-hanteringskonsolen fönstret med listan över SAP HANA-processer](./media/hana-get-started/image024.jpg)

Följande skärmbild visar delarna av filstruktur under katalogen /hana/shared SWPM skapas under installationen av HANA. Eftersom det finns inget alternativ för att ange en annan sökväg, är det viktigt att montera ytterligare diskutrymme under katalogen /hana innan installationen av SAP HANA med hjälp av SWPM. Det här steget förhindrar att filsystemet rot körs utanför lediga utrymmet.

![Filstruktur för /hana/shared directory skapades under HANA-installation](./media/hana-get-started/image025.jpg)

Den här skärmbilden visar filstrukturen för /usr/sap katalogen:

![/ Usr/sap filen katalogstrukturen](./media/hana-get-started/image026.jpg)

Det sista steget i den distribuerade ABAP-installationen är att installera den primära server-instansen:

![ABAP installation som visar primära server-instans som det sista steget](./media/hana-get-started/image027b.jpg)

När den primära server-instansen och SAP-Gränssnittet har installerats kan du använda den **DBA Cockpit** transaktion att bekräfta att SAP HANA-installationen slutförts korrekt:

![DBA Cockpit-fönstret som bekräftar att installationen lyckades](./media/hana-get-started/image028b.jpg)

Som ett sista steg kan du först installerar HANA Studio i SAP applikationsserver-dator. Därefter ansluta till SAP HANA-instans som körs på DB-server-dator.

![Installera SAP HANA Studio i SAP applikationsserver VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Manuell installation av SAP HANA: HDBLCM
Förutom att installera SAP HANA som en del av en distribuerad installation med hjälp av SWPM, kan du installera fristående HANA först med hjälp av HDBLCM. Du kan sedan installera SAP NetWeaver 7.5, till exempel. Skärmbilderna i det här avsnittet visar hur processen fungerar.

Mer information om verktyget HANA HDBLCM finns:

* [Välj rätt SAP HANA-HDBLCM för uppgiften](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [Hanteringsverktyg för SAP HANA livscykel](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [SAP HANA server installation och uppdatering guiden](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Du vill undvika problem med en grupp-ID: T standardinställningen för den `\<HANA SID\>adm user`, som har skapats av verktyget HDBLCM. Innan du installerar SAP HANA via HDBLCM måste du definiera en ny grupp med namnet `sapsys` med hjälp av grupp-ID `1001`:

![Ny grupp ”sapsys” definieras med hjälp av grupp-ID 1001](./media/hana-get-started/image030.jpg)

När du startar HDBLCM för första gången, starta en enkel visar. Välj objekt 1, **installera nya**:

![”Installera nya system” alternativ i fönstret HDBLCM start](./media/hana-get-started/image031.jpg)

Följande skärmbild visar de viktiga alternativ som du har valt tidigare.

> [!IMPORTANT]
> Kataloger som namnges för HANA log och datavolymer och installationssökvägen, vilket är /hana/shared i det här exemplet och /usr/sap får inte vara en del av filsystemet rot. Dessa kataloger som hör till Azure-datadiskar som kopplats till den virtuella datorn. Mer information finns i avsnittet ”Disk-installation”. 

Den här metoden förhindrar filsystemet rot slut på utrymme. Observera att systemadministratören HANA har användar-ID `1005` och är en del av den `sapsys` med ID `1001`, som definierades innan installationen.

![Lista över alla nyckelkomponenter för SAP HANA som tidigare har valt](./media/hana-get-started/image032.jpg)

Kontrollera den `\<HANA SID\>adm user` information i/etc/passwd katalogen. Leta efter `azdadm`, enligt följande skärmbild:

![HANA \<HANA SID\>adm användarinformation som visas i/etc/passwd katalogen](./media/hana-get-started/image033.jpg)

När du har installerat SAP HANA med hjälp av HDBLCM ser filstrukturen i SAP HANA Studio enligt följande skärmbild. Schemat SAPABAP1 som innehåller alla SAP NetWeaver-tabeller är inte tillgängligt ännu.

![Filstruktur för SAP HANA i SAP HANA-Studio](./media/hana-get-started/image034.jpg)

När du har installerat SAP HANA kan du installera SAP NetWeaver ovanpå den. I följande skärmbild visas installationen har utförts som en distribuerad installation med hjälp av SWPM. Den här processen beskrivs i föregående avsnitt. När du installerar databasinstansen med hjälp av SWPM kan ange du samma data med hjälp av HDBLCM. Exempelvis kan ange du värdnamn, HANA SID och instansnummer. SWPM sedan använder den befintliga HANA-installationen och lägger till flera scheman.

![En distribuerad installation utförs med hjälp av SWPM](./media/hana-get-started/image035b.jpg)

Följande skärmbild visar installationssteget SWPM där du anger DBACOCKPIT schemadata:

![SWPM installationssteget där DBACOCKPIT schemadata anges](./media/hana-get-started/image036b.jpg)

Ange information om SAPABAP1 schemat:

![Att ange information om SAPABAP1 schemat](./media/hana-get-started/image037b.jpg)

När SWPM databasen instans installationen är klar kan se du SAPABAP1 schemat i SAP HANA-Studio:

![SAPABAP1 schemat i SAP HANA-Studio](./media/hana-get-started/image038b.jpg)

Slutligen när SAP applikationsserver och SAP GUI installationer är klar kontrollerar HANA DB-instans med hjälp av den **DBA Cockpit** transaktion:

![HANA-DB-instans som verifierats med DBA Cockpit-transaktion](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Nedladdning av programvara för SAP
Du kan ladda ned programvara från SAP Service Marketplace, enligt följande skärmbilder.

Ladda ned NetWeaver 7.5 för Linux/HANA:

 ![SAP-installation och uppgradering fönster för att ladda ned NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Ladda ner HANA SP12 plattform versionen:

 ![SAP-installation och uppgradering fönster för att ladda ned HANA SP12 plattform Edition](./media/hana-get-started/image002.jpg)

