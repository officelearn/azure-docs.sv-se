---
title: 'Snabb start: manuell installation av en instans SAP HANA på Azure Virtual Machines | Microsoft Docs'
description: Snabb starts guide för manuell installation av SAP HANA med en instans på Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 630f094ffc6c57a0137d1abc46476f5abe64f616
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750374"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>Snabb start: manuell installation av en instans SAP HANA på Azure Virtual Machines
## <a name="introduction"></a>Introduktion
Den här guiden hjälper dig att konfigurera en instans SAP HANA på Azure Virtual Machines när du installerar SAP NetWeaver 7,5 och SAP HANA 1,0 SP12 manuellt. Den här hand boken handlar om hur du distribuerar SAP HANA på Azure. Den ersätter inte SAP-dokumentation. 

> [!NOTE]
> I den här guiden beskrivs distributioner av SAP HANA till virtuella Azure-datorer. Information om hur du distribuerar SAP HANA till HANA-stora instanser finns i [använda SAP på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Krav
Den här guiden förutsätter att du är bekant med IaaS-grunderna (Infrastructure as a Service) som:
 * Distribuera virtuella datorer (VM) eller virtuella nätverk via Azure Portal eller PowerShell.
 * Azures plattforms oberoende kommando rads gränssnitt (CLI), som innehåller alternativet att använda JavaScript Object Notation-mallar (JSON).

Den här guiden förutsätter också att du är bekant med:
* SAP HANA-och SAP-NetWeaver och hur de installeras lokalt.
* Installera och använda SAP HANA-och SAP-programinstanser på Azure.
* Följande begrepp och procedurer:
   * Planera för SAP-distribution på Azure, som innehåller Azure Virtual Network planera och Azure Storage användning. Se [SAP NetWeaver i Azure Virtual Machines-planering och implementerings guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Distributions principer och sätt att distribuera virtuella datorer i Azure. Se [Azure Virtual Machines Deployment for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Hög tillgänglighet för SAP NetWeaver ABAP SAP Central Services (ASCS), SAP Central Services (SCS) och Queue Replication Server (ERS) på Azure. Se [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Information om hur du kan förbättra effektiviteten i en multi-SID-installation av ASCS/SCS på Azure. Se [skapa en SAP NetWeaver multi-sid-konfiguration](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principer för att köra SAP-NetWeaver baserat på Linux-drivna virtuella datorer i Azure. Se [köra SAP-NetWeaver på Microsoft Azure SUSE Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Den här guiden innehåller vissa inställningar för Linux i virtuella Azure-datorer. Den innehåller också information om hur du kopplar Azure Storage-diskar korrekt till virtuella Linux-datorer.

De typer av virtuella Azure-datorer som kan användas för produktions scenarier finns i [SAP-dokumentationen för IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). För ej Production-scenarier är en större mängd inbyggda virtuella Azure VM-typer tillgängliga.
Mer information om konfiguration och åtgärder för virtuella datorer finns i [SAP HANA infrastruktur konfiguration och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
SAP HANA hög tillgänglighet finns i [SAP HANA hög tillgänglighet för Azure-Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).

Om du vill få en SAP HANA instans eller S/4HANA-eller BW/4HANA-systemet distribueras snabbt, kan du överväga att använda [bibliotek för SAP-molnprogram](https://cal.sap.com). Du hittar dokumentation om hur du distribuerar ett S/4HANA-system via SAP Cloud installation Library på Azure, till exempel i [den här hand boken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Allt du behöver är en Azure-prenumeration och en SAP-användare som kan registreras med SAP Cloud installation Library.

## <a name="additional-resources"></a>Ytterligare resurser
### <a name="sap-hana-backup"></a>SAP HANA säkerhets kopiering
Information om hur du säkerhetskopierar SAP HANA databaser på virtuella Azure-datorer finns i:
* [Säkerhets kopierings guide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide).
* [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level).
* [SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots).

### <a name="sap-cloud-appliance-library"></a>Bibliotek för SAP Cloud-utrustning
Information om hur du använder bibliotek för SAP Cloud-installationer för att distribuera S/4HANA eller BW/4HANA finns i [distribuera SAP S/4HANA eller BW/4HANA på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA-stödda operativ system
Information om SAP HANA-stödda operativ system finns i [SAP Note 2235581-SAP HANA: operativ system som stöds](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuella Azure-datorer har bara stöd för en delmängd av dessa operativ system. Följande operativ system stöds för att distribuera SAP HANA på Azure: 

* SUSE Linux Enterprise Server 12. x
* Red Hat Enterprise Linux 7.2

Mer information om SAP-dokumentation om SAP HANA och olika Linux-operativsystem finns i:

* [SAP anmärkning 171356: SAP-program på Linux: allmän information](https://launchpad.support.sap.com/#/notes/1984787).
* [SAP anmärkning 1944799: SAP HANA rikt linjer för installation av operativ systemet SLES](http://service.sap.com/sap/support/notes/1944799).
* [SAP obs 2205917: SAP HANA DB rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E).
* [SAP anmärkning 1391070: Linux UUID-lösningar](https://launchpad.support.sap.com/#/notes/1391070).
* [SAP anmärkning 2009879: SAP HANA rikt linjer för Red Hat Enterprise Linux (RHEL)-operativ system](https://launchpad.support.sap.com/#/notes/2009879).
* [SAP obs 2292690: SAP HANA DB: rekommenderade OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E).

### <a name="sap-monitoring-in-azure"></a>SAP-övervakning i Azure
Information om SAP-övervakning i Azure:

* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) diskuterar SAP Enhanced Monitoring med virtuella Linux-datorer i Azure. 
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) diskuterar information om SAPOSCOL i Linux. 
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) diskuterar viktiga övervaknings mått för SAP på Microsoft Azure.

### <a name="azure-vm-types"></a>Typer av virtuella Azure-datorer
Azure VM-typer och SAP-stödda arbets belastnings scenarier som används med SAP HANA dokumenteras i [SAP Certified IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azures VM-typer som är certifierade av SAP för SAP NetWeaver eller S/4HANA-program skiktet finns dokumenterade i [SAP anmärkning 1928533: SAP-program på Azure: produkter som stöds och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533/E).

> [!NOTE]
> SAP – Linux – Azure-integration stöds endast på Azure Resource Manager och inte i den klassiska distributions modellen. 

## <a name="manual-installation-of-sap-hana"></a>Manuell installation av SAP HANA

> [!IMPORTANT]
> Kontrol lera att det OS du väljer är SAP-certifierat för SAP HANA på de angivna VM-typerna som du använder. Listan med SAP HANA certifierade VM-typer och OS-versioner för dessa typer av virtuella datorer kan slås upp i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka i informationen om den VM-typ som anges för att få en fullständig lista över SAP HANA operativ system versioner som stöds för den aktuella VM-typen. I exemplet i det här dokumentet använde vi en SUSE Linux Enterprise Server (SLES) OS-version som inte stöds av SAP för SAP HANA på virtuella datorer i M-serien.
>

Den här guiden beskriver hur du installerar SAP HANA manuellt på virtuella Azure-datorer på två olika sätt:

* Använd SAP Software Provisioning Manager (SWPM) som en del av en distribuerad NetWeaver-installation i steget "installera databas instans".
* Använd verktyget SAP HANA Database Lifecycle Manager (HDBLCM) och installera NetWeaver.

Du kan också använda SWPM för att installera alla komponenter, till exempel SAP HANA, SAP-programservern och ASCS-instansen, i en enda virtuell dator. Stegen beskrivs i det här [SAP HANA blogg meddelandet](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Det här alternativet beskrivs inte i den här snabb starts guiden, men problemen som du måste tänka på är desamma.

Innan du påbörjar en installation rekommenderar vi att du läser avsnittet "förbereda virtuella Azure-datorer för manuell installation av SAP HANA" senare i den här hand boken. På så sätt kan du förhindra flera grundläggande misstag som kan uppstå när du använder en standard konfiguration för Azure VM.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Viktiga steg för SAP HANA installation när du använder SAP SWPM
I det här avsnittet listas viktiga steg för en manuell, SAP HANA installation av en instans när du använder SAP-SWPM för att utföra en distribuerad SAP NetWeaver 7,5-installation. De enskilda stegen beskrivs i detalj i skärm bilder senare i den här hand boken.

1. Skapa ett virtuellt Azure-nätverk som innehåller två test-VM: ar.
2. Distribuera de två virtuella Azure-datorerna med operativ system enligt Azure Resource Managers modellen. I det här exemplet används SUSE Linux Enterprise Server-och SLES för SAP-program 12 SP1. 
3. Anslut två Azure standard-eller Premium Storage-diskar, till exempel 75-GB-eller 500-GB-diskar, till den virtuella program serverns virtuella dator.
4. Koppla Premium Storage-diskar till den virtuella HANA DB-servern. Mer information finns i avsnittet "disk installation" senare i den här hand boken.
5. Anslut flera diskar beroende på storlek eller data flödes krav. Skapa sedan stripe-volymer. Använd antingen LVM (Logical Volume Management) eller ett mdadm-verktyg (Multiple enhet administration) på operativ system nivån i den virtuella datorn.
6. Skapa fil system för XFS på anslutna diskar eller logiska volymer.
7. Montera de nya fil systemen för XFS på operativ system nivå. Använd ett fil system för alla SAP-program. Använd det andra fil systemet för/sapmnt-katalogen och säkerhets kopieringar till exempel. Montera XFS-filsystem på Premium Storage-diskarna som/Hana och/usr/SAP. på den SAP HANA DB-servern. Den här processen är nödvändig för att förhindra att rot fil systemet fylls. Rot fil systemet är inte stort på virtuella Linux Azure-datorer. 
8. Ange de lokala IP-adresserna för de virtuella test datorerna i/etc/hosts-filen.
9. Ange **nomisslyckande** -parametern i/etc/fstab-filen.
10. Ange Linux-kernel-parametrar enligt den version av Linux OS som du använder. Mer information finns i SAP-anteckningar som diskuterar HANA och avsnittet "kernel-parametrar" i den här guiden.
11. Lägg till växlings utrymme.
12. Du kan också installera ett grafiskt skriv bord på de virtuella test datorerna. Annars använder du en fjärran sluten SAPinst installation.
13. Hämta SAP-programvaran från SAP-tjänstens marknads plats.
14. Installera SAP ASCS-instansen på den virtuella App Server-datorn.
15. Dela/sapmnt-katalogen bland de virtuella test datorerna med hjälp av NFS. Den virtuella program servern är NFS-servern.
16. Installera databas instansen, som innehåller HANA, genom att använda SWPM på den virtuella DB-servern.
17. Installera den primära program servern (PAS) på den virtuella program servern.
18. Starta SAP Management Console (SAP MC). Anslut till exempel med SAP GUI eller HANA Studio.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Viktiga steg för SAP HANA installation när du använder HDBLCM
I det här avsnittet listas viktiga steg för en manuell, SAP HANA installation av en instans när du använder SAP-HDBLCM för att utföra en distribuerad SAP NetWeaver 7,5-installation. De enskilda stegen beskrivs i detalj i skärm bilderna i den här guiden.

1. Skapa ett virtuellt Azure-nätverk som innehåller två test-VM: ar.
2. Distribuera två virtuella Azure-datorer med operativ system enligt Azure Resource Managers modellen. I det här exemplet används SLES och SLES för SAP-program 12 SP1.
3. Anslut två Azure standard-eller Premium-lagringsenheter, till exempel 75-GB-eller 500-GB-diskar, till den virtuella App Server-datorn.
4. Koppla Premium Storage-diskar till den virtuella HANA DB-servern. Mer information finns i avsnittet "disk installation" senare i den här hand boken.
5. Anslut flera diskar beroende på storlek eller data flödes krav. Skapa stripe-volymer med hjälp av antingen hantering av logiska volymer eller ett mdadm-verktyg på OS-nivån inuti den virtuella datorn.
6. Skapa fil system för XFS på anslutna diskar eller logiska volymer.
7. Montera de nya fil systemen för XFS på operativ system nivå. Använd ett fil system för alla SAP-program. Använd det andra fil systemet för/sapmnt-katalogen och säkerhets kopieringar till exempel. Montera XFS-filsystem på Premium Storage-diskarna som/Hana och/usr/SAP. på den SAP HANA DB-servern. Den här processen är nödvändig för att hjälpa till att förhindra att rot fil systemet fylls. Rot fil systemet är inte stort på virtuella Linux Azure-datorer.
8. Ange de lokala IP-adresserna för de virtuella test datorerna i/etc/hosts-filen.
9. Ange **nomisslyckande** -parametern i/etc/fstab-filen.
10. Ange kernel-parametrar enligt den version av Linux OS som du använder. Mer information finns i SAP-anteckningar som diskuterar HANA och avsnittet "kernel-parametrar" i den här guiden.
11. Lägg till växlings utrymme.
12. Du kan också installera ett grafiskt skriv bord på de virtuella test datorerna. Annars använder du en fjärran sluten SAPinst installation.
13. Hämta SAP-programvaran från SAP-tjänstens marknads plats.
14. Skapa en grupp, sapsys, med grupp-ID 1001, på den virtuella HANA DB-servern.
15. Installera SAP HANA på den virtuella DB-servern med hjälp av HANA Database Lifecycle Manager.
16. Installera SAP ASCS-instansen på den virtuella App Server-datorn.
17. Dela/sapmnt-katalogen bland de virtuella test datorerna med hjälp av NFS. Den virtuella program servern är NFS-servern.
18. Installera databas instansen, som innehåller HANA, genom att använda SWPM på den virtuella HANA DB-servern.
19. Installera den primära program servern på den virtuella program servern.
20. Starta SAP MC. Anslut via SAP GUI eller HANA Studio.

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>Förbered virtuella Azure-datorer för en manuell installation av SAP HANA
I det här avsnittet beskrivs följande ämnen:

* OS-uppdateringar
* Disk installation
* Kernel-parametrar
* Fil system
* /Etc/hosts-filen
* /Etc/fstab-filen

### <a name="os-updates"></a>OS-uppdateringar
Sök efter uppdateringar och korrigeringar för Linux OS innan du installerar ytterligare program vara. Genom att installera en korrigering kan du undvika ett samtal till support kontoret.

Kontrol lera att du använder:
* SUSE Linux Enterprise Server för SAP-program.
* Red Hat Enterprise Linux för SAP-program eller Red Hat Enterprise Linux för SAP HANA. 

Om du inte redan har gjort det kan du registrera operativ Systems distributionen med Linux-prenumerationen från Linux-leverantören. SUSE har OS-avbildningar för SAP-program som redan innehåller tjänster och som registreras automatiskt.

Här är ett exempel på hur du söker efter tillgängliga korrigeringsfiler för SUSE Linux med hjälp av kommandot **zypper** :

 `sudo zypper list-patches`

Beroende på typen av problem klassificeras korrigeringarna efter kategori och allvarlighets grad. Ofta använda värden för kategori: 
- Säkerhet
- Rekommenderad
- Valfritt
- Funktion
- Dokument
- yast

Ofta använda värden för allvarlighets grad:

- Kritiskt
- Viktig
- Mellan
- Låg
- Ospecificerad

**Zypper** -kommandot söker bara efter de uppdateringar som dina installerade paket behöver. Du kan till exempel använda det här kommandot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Du kan lägga till parametern `--dry-run` för att testa uppdateringen utan att faktiskt uppdatera systemet.


### <a name="disk-setup"></a>Disk installation
Rot fil systemet i en virtuell Linux-dator på Azure har en storleks begränsning. Så du måste koppla ytterligare disk utrymme till en virtuell Azure-dator för att kunna köra SAP. För virtuella Azure-datorer i Azure är det möjligt att använda Azure standard Storage-diskar. För SAP HANA DBMS Azure-VM: ar är användningen av Azure Premium Storage-diskar för produktion och implementeringar av inte produktion obligatorisk.

Enligt kraven för [SAP HANA TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)föreslås följande Azure Premium Storage-konfiguration: 

| VM-SKU | RAM |  /Hana/data och/Hana/log <br /> randigt med LVM eller mdadm | /hana/shared | /root-volym | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

I den föreslagna disk konfigurationen placeras HANA-datavolymen och logg volymen på samma uppsättning Azure Premium Storage-diskar som är stripe med LVM eller mdadm. Det är inte nödvändigt att definiera någon RAID-redundans eftersom Azure Premium Storage behåller tre avbildningar av diskarna för redundans. 

För att se till att du konfigurerar tillräckligt med lagrings utrymme, se [SAP HANA krav för TDI-lagring](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) [SAP HANA och installations guide för Server installation och uppdatering](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Överväg också att de olika lagrings volymerna för virtuella hård diskar (VHD) i de olika Azure Premium Storage-diskarna är dokumenterade i [högpresterande Premium Storage och hanterade diskar för virtuella datorer](../../windows/disks-types.md). 

Du kan lägga till mer Premium Storage-diskar till de virtuella HANA-DBMS-datorerna för att lagra databas-eller transaktions logg säkerhets kopior

Mer information om de två huvud verktyg som används för att konfigurera striping finns i:

* [Konfigurera programvaru-RAID på Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Konfigurera LVM på en virtuell Linux-dator i Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Mer information om hur du kopplar diskar till virtuella Azure-datorer som kör Linux som gäst operativ system finns i [lägga till en disk till en virtuell Linux-dator](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Med Azure Premium-SSD kan du definiera lägen för cachelagring av diskar. Inaktivera diskcachelagring för den stripe-uppsättning som innehåller/Hana/data och/Hana/log. För de andra volymerna, det vill säga diskar, ställer du in cacheläge på **ReadOnly**.

Du hittar exempel-JSON-mallar som ska användas för att skapa virtuella datorer i [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates).
Mallen VM-enkel-SLES är en grundläggande mall. Den innehåller ett lagrings avsnitt med ytterligare 100 GB data disk. Använd den här mallen som en bas. Du kan anpassa mallen efter din speciella konfiguration.

> [!NOTE]
> Det är viktigt att koppla Azure Storage-disken med hjälp av en UUID enligt beskrivningen i [köra SAP NetWeaver på Microsoft Azure SUSE Linux-datorer](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I test miljön är två Azure standard Storage-diskar anslutna till den virtuella SAP App Server-datorn, som visas i följande skärm bild. En disk lagrar alla SAP-program, till exempel NetWeaver 7,5, SAP GUI och SAP HANA, för installation. Den andra disken ser till att det finns tillräckligt med ledigt utrymme för ytterligare krav. Till exempel säkerhets kopierings-och test data och/sapmnt-katalogen, det vill säga SAP-profiler, måste delas mellan alla virtuella datorer som tillhör samma SAP-landskap.

![Fönstret SAP HANA app server-diskar visar två data diskar och deras storlek](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-parametrar
SAP HANA kräver vissa inställningar för linux-kernel. Dessa kernel-inställningar är inte en del av standard avbildningarna i Azure Gallery och måste anges manuellt. Beroende på om du använder SUSE eller Red Hat kan parametrarna vara olika. SAP-anteckningarna angav tidigare information om dessa parametrar. I skärm bilderna som visas användes SUSE Linux 12 SP1. 

SLES för SAP-program 12 allmän tillgänglighet och SLES för SAP-program 12 SP1 har ett nytt verktyg, **finjusterat-ADM**, som ersätter det gamla **sapconf** -verktyget. En särskild SAP HANA profil är tillgänglig för **finjusterad-ADM**. Om du vill justera systemet för SAP HANA anger du följande profil som en rot användare:

   `tuned-adm profile sap-hana`

Mer information om **finjusterad-ADM**finns i [SUSE-dokumentationen om finjusterad-ADM](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

I följande skärm bild kan du se hur **justerad-ADM** ändrade `transparent_hugepage` och `numa_balancing` värden, enligt de SAP HANA inställningar som krävs:

![Verktyget finjusterad-ADM ändrar värdena enligt nödvändiga SAP HANA inställningar](./media/hana-get-started/image005.jpg)

Om du vill att SAP HANA kernel-inställningarna ska vara permanenta använder du **grub2** på SLES 12. Mer information om **grub2**finns i avsnittet [konfigurations fil struktur](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen.

Följande skärm bild visar hur kernel-inställningarna ändrades i konfigurations filen och sedan kompilerades med hjälp av **grub2-mkconfig**:

![Kernel-inställningarna har ändrats i konfigurations filen och kompilerats med hjälp av grub2-mkconfig](./media/hana-get-started/image006.jpg)

Ett annat alternativ är att ändra inställningarna med hjälp av YaST och **Start Inläsaren**  >  inställningarna för**kernel-parametrar** :

![Fliken Inställningar för kernel-parametrar i YaST Boot Loader](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Fil system
Följande skärm bild visar två fil system som skapades på den virtuella SAP App Server-datorn ovanpå de två anslutna Azure standard Storage-diskarna. Båda fil systemen är av typen XFS och monteras till/sapdata och/sapsoftware.

Det är inte obligatoriskt att strukturera dina fil system på det här sättet. Du har andra alternativ för att strukturera disk utrymmet. Den viktigaste övervägandeheten är att förhindra att rot fil systemet inte kan få slut på ledigt utrymme.

![Två fil system som skapats på den virtuella SAP App Server-datorn](./media/hana-get-started/image008.jpg)

När du använder SAPinst med SWPM och alternativet **standard** installation installeras alla under/Hana och/usr/sap. för SAP HANA den virtuella DB-datorn under en databas installation. Standard platsen för säkerhets kopian av SAP HANA loggen är under/usr/SAP. Återigen är det viktigt att förhindra att rot fil systemet tar slut på lagrings utrymme. Kontrol lera att det finns tillräckligt med ledigt utrymme under/Hana och/usr/SAP innan du installerar SAP HANA med hjälp av SWPM.

En beskrivning av standardlayouten för fil system för SAP HANA finns i guiden för [installation och uppdatering av SAP HANA Server](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Ytterligare fil system som skapats på den virtuella SAP App Server-datorn](./media/hana-get-started/image009.jpg)

När du installerar SAP NetWeaver på en standard SLES/SLES för SAP-program 12 Azure Gallery-avbildning visas ett meddelande om att det inte finns något växlings utrymme, som du ser i följande skärm bild. Om du vill stänga meddelandet kan du lägga till en växlings fil manuellt med hjälp av **DD**, **mkswap**och **swapon**. Om du vill veta mer kan du söka efter "lägga till en växlings fil manuellt" i avsnittet [använda YaST partitioner](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen.

Ett annat alternativ är att konfigurera växlings utrymmet med hjälp av Linux VM-agenten. Mer information finns i [Användarguide för Azure Linux Agent](../../extensions/agent-linux.md).

![Popup-meddelande som uppmanar dig att det inte finns tillräckligt med växlings utrymme](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/Etc/hosts-filen
Innan du börjar installera SAP kontrollerar du att du inkluderar värdnamn och IP-adresser för de virtuella SAP-datorerna i/etc/hosts-filen. Distribuera alla virtuella SAP-datorer i ett virtuellt Azure-nätverk. Använd sedan de interna IP-adresserna, som du ser här:

![Värdnamn och IP-adresser för de virtuella SAP-datorer som anges i/etc/hosts-filen](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/Etc/fstab-filen

Det är bra att lägga till **nomisslyckande** -parametern i fstab-filen. På så sätt kan den virtuella datorn inte sluta svara i Start processen om något går fel med diskarna. Men kom ihåg att ytterligare disk utrymme kanske inte är tillgängligt och att processerna kan fylla i rot fil systemet. Om/Hana saknas startar SAP HANA inte.

![Lägg till nomisslyckande-parametern i fstab-filen](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Grafiskt GNOME Desktop på SLES 12/SLES för SAP-program 12
I det här avsnittet beskrivs hur du:

* Installera GNOME Desktop och xrdp på SLES 12/SLES för SAP-program 12.
* Kör Java-baserade SAP MC genom att använda Firefox på SLES 12/SLES för SAP-program 12.

Du kan också använda alternativ som xterminal eller VNC, som inte beskrivs i den här hand boken.

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installera GNOME Desktop och xrdp på SLES 12/SLES för SAP-program 12
Om du har en Windows-bakgrund kan du enkelt använda ett grafiskt skriv bord direkt i virtuella datorer i SAP Linux för att köra Firefox, SAPinst, SAP GUI, SAP MC eller HANA Studio. Sedan kan du ansluta till den virtuella datorn via Remote Desktop Protocol (RDP) från en Windows-dator. Beroende på företagets principer för att lägga till GUIs i produktions-och ej Production Linux-baserade system, kanske du vill installera GNOME på servern. Följ de här stegen för att installera GNOME Desktop på en Azure SLES 12/SLES för SAP-program 12 VM.

1. Installera GNOME Desktop genom att ange följande kommando, till exempel i ett fönster i listan:

   `zypper in -t pattern gnome-basic`

2. Installera xrdp för att tillåta en anslutning till den virtuella datorn via RDP:

   `zypper in xrdp`

3. Redigera/etc/sysconfig/windowmanager och Ställ in standard fönster hanteraren på GNOME:

   `DEFAULT_WM="gnome"`

4. Kör **chkconfig** för att se till att xrdp startar automatiskt efter en omstart:

   `chkconfig -level 3 xrdp on`

5. Om du har problem med RDP-anslutningen försöker du starta om, till exempel från ett fönster i listan:

   `/etc/xrdp/xrdp.sh restart`

6. Om en xrdp-omstart som nämns i föregående steg inte fungerar, söker du efter en. pid-fil:

   `check /var/run` 

   Sök efter `xrdp.pid`. Om du hittar det tar du bort det och försöker starta om igen.

### <a name="start-sap-mc"></a>Starta SAP MC
När du har installerat GNOME Desktop startar du den grafiska Java-baserade SAP MC från Firefox. Om den körs i en Azure-SLES 12/SLES för SAP-program 12 VM kan det Visa ett fel. Felet beror på det saknade plugin-programmet för Java-webbläsare.

URL: en för att starta SAP MC är `<server>:5<instance_number>13`.

Mer information finns i [starta den webbaserade SAP-hanteringskonsolen](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Följande skärm bild visar fel meddelandet som visas när plugin-programmet för Java-webbläsaren saknas:

![Fel meddelande som anger att plugin-programmet för Java-webbläsare saknas](./media/hana-get-started/image013.jpg)

Ett sätt att lösa problemet är att installera plugin-programmet som saknas genom att använda YaST, som du ser i följande skärm bild:

![Använda YaST för att installera plugin-programmet som saknas](./media/hana-get-started/image014.jpg)

När du anger URL: en för SAP-hanterings konsolen uppmanas du att aktivera plugin-programmet:

![Dialog ruta som begär aktivering av plugin](./media/hana-get-started/image015.jpg)

Du kan också få ett fel meddelande om en fil som saknas, JavaFX. Properties. Det rör sig om kravet på Oracle Java 1,8 för SAP GUI 7,4. Mer information finns i [SAP Note 2059429](https://launchpad.support.sap.com/#/notes/2059424).
IBM Java-versionen och openjdk-paketet som levereras med SLES/SLES för SAP-program 12 innehåller inte den nödvändiga JavaFX. Properties-filen. Lösningen är att ladda ned och installera Java SE 8 från Oracle.

Information om ett liknande problem med openjdk på openSUSE finns i diskussions tråden [SAPGui 7,4 Java for openSUSE 42,1 skottår](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Manuell installation av SAP HANA: SWPM
I serien med skärm dum par i det här avsnittet visas de viktigaste stegen för att installera SAP NetWeaver 7,5 och SAP HANA SP12 när du använder SWPM med SAPinst. Som en del av en NetWeaver 7,5-installation kan SWPM också installera HANA-databasen som en enda instans.

I en test miljö har vi installerat en app server för ABAP (Advanced Business Application Programming). Som du ser i följande skärm bild använde vi alternativet **distribuerat system** för att installera ASCS och primära program Server instanser i en virtuell Azure-dator. Vi använde SAP HANA som databas system i en annan virtuell Azure-dator.

![ASCS och primära Application Server-instanser som installerats med alternativet distribuerat system](./media/hana-get-started/image012.jpg)

När ASCS-instansen har installerats på den virtuella App Server-datorn identifieras den av en grön ikon i SAP-hanteringskonsolen. /Sapmnt-katalogen, som innehåller SAP-profil katalogen, måste delas med den virtuella datorn för SAP HANA DB-servern. DATABAS installations steget behöver åtkomst till den här informationen. Det bästa sättet att ge åtkomst är att använda NFS, som kan konfigureras med hjälp av YaST.

![SAP-hanteringskonsolen som visar ASCS-instansen som är installerad på den virtuella App Server-datorn med en grön ikon](./media/hana-get-started/image016.jpg)

/Sapmnt-katalogen delas via NFS på den virtuella App Server-datorn med hjälp av alternativen **RW** och **no_root_squash** . Standardvärdena är **ro** och **root_squash**, vilket kan leda till problem när du installerar databas instansen.

![Dela/sapmnt-katalogen via NFS med hjälp av alternativen RW och no_root_squash](./media/hana-get-started/image017b.jpg)

När nästa skärm bild visas måste/sapmnt-resursen från den virtuella App Server-datorn konfigureras på den virtuella datorn för SAP HANA DB-server med hjälp av **NFS-klienten** och YaST:

![/Sapmnt-resursen som kon figurer ATS med hjälp av NFS-klienten](./media/hana-get-started/image018b.jpg)

Om du vill utföra en distribuerad NetWeaver 7,5-installation, det vill säga en **databas instans**, loggar du in på den virtuella datorn för SAP HANA DB-server och startar SWPM:

![Installera en databas instans genom att logga in på den virtuella datorn för SAP HANA DB-servern och starta SWPM](./media/hana-get-started/image019.jpg)

När du har valt **standard** installation och sökvägen till installations mediet anger du ett db-sid, värd namnet, instans numret och databasens system administratörs lösen ord:

![Inloggnings sidan för SAP HANA databas system administratör](./media/hana-get-started/image035b.jpg)

Ange lösen ordet för DBACOCKPIT-schemat:

![Rutan Password-Entry för DBACOCKPIT-schemat](./media/hana-get-started/image036b.jpg)

Ange en fråga för SAPABAP1 schema lösen ord:

![Ange en fråga för schema lösen ordet för SAPABAP1](./media/hana-get-started/image037b.jpg)

När varje uppgift har slutförts visas en grön bock bredvid varje fas i databas installationen. Meddelandet "körning av... Databas instansen har slutförts visas.

![Uppgift-slutfört fönster med bekräftelse meddelande](./media/hana-get-started/image023.jpg)

När installationen är klar visas även databas instansen med en grön ikon i SAP Management Console. Den fullständiga listan med SAP HANA processer visas, till exempel hdbindexserver och hdbcompileserver.

![Fönstret SAP Management Console med en lista över SAP HANA processer](./media/hana-get-started/image024.jpg)

Följande skärm bild visar de delar av fil strukturen under/Hana/Shared-katalogen som SWPM skapade under HANA-installationen. Eftersom det inte finns något alternativ för att ange en annan sökväg är det viktigt att montera ytterligare disk utrymme i/Hana-katalogen före SAP HANA-installationen med hjälp av SWPM. Det här steget förhindrar att rot fil systemet inte kan köra ledigt utrymme.

![Fil strukturen i/Hana/Shared-katalogen som skapades under HANA-installationen](./media/hana-get-started/image025.jpg)

Den här skärm bilden visar fil strukturen i/usr/SAP-katalogen:

![/Usr/SAP katalog filens struktur](./media/hana-get-started/image026.jpg)

Det sista steget i den distribuerade ABAP-installationen är att installera den primära Application Server-instansen:

![ABAP-installation som visar den primära Application Server-instansen som sista steg](./media/hana-get-started/image027b.jpg)

När den primära Application Server-instansen och SAP-ANVÄNDARGRÄNSSNITTET har installerats använder **du transaktionen för att bekräfta** att SAP HANA installationen slutfördes korrekt:

![Bekräftelse av DBA-cockpit bekräfta att installationen har slutförts](./media/hana-get-started/image028b.jpg)

Som ett sista steg kanske du först vill installera HANA Studio i den virtuella SAP App Server-datorn. Anslut sedan till den SAP HANA-instans som körs på den virtuella DB-servern.

![Installera SAP HANA Studio i den virtuella SAP App Server-datorn](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Manuell installation av SAP HANA: HDBLCM
Förutom att installera SAP HANA som en del av en distribuerad installation med hjälp av SWPM, kan du installera den fristående HANA-installationen med hjälp av HDBLCM. Du kan till exempel installera SAP NetWeaver 7,5. Skärm bilderna i det här avsnittet visar hur den här processen fungerar.

Mer information om HANA HDBLCM-verktyget finns i:

* [Välj rätt SAP HANA HDBLCM för din uppgift](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm).
* [SAP HANA livs cykel hanterings verktyg](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm).
* [Installations-och uppdaterings guide för SAP HANA Server](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf).

Du vill undvika problem med standardinställningen för grupp-ID för `\<HANA SID\>adm user`, som skapas av HDBLCM-verktyget. Innan du installerar SAP HANA via HDBLCM definierar du en ny grupp som heter `sapsys` med hjälp av grupp-ID `1001`:

![Ny grupp "sapsys" som definieras med hjälp av grupp-ID 1001](./media/hana-get-started/image030.jpg)

När du startar HDBLCM för första gången visas en enkel start-meny. Välj objekt 1, **Installera nytt system**:

![Alternativet "Installera nytt system" i Start fönstret för HDBLCM](./media/hana-get-started/image031.jpg)

På följande skärm bild visas alla viktiga alternativ som du valde tidigare.

> [!IMPORTANT]
> Kataloger som har namnet för HANA-logg-och data volymer och installations Sök vägen, som är/Hana/Shared i det här exemplet, och/usr/SAP får inte vara en del av rot fil systemet. Dessa kataloger tillhör Azure-datadiskarna som är anslutna till den virtuella datorn. Mer information finns i avsnittet "disk installation". 

Den här metoden hjälper till att förhindra att rot fil systemet får slut på utrymme. Observera att HANA-systemadministratören har användar-ID `1005` och är en del av `sapsys` gruppen, med ID `1001`, som definierades före installationen.

![Lista över alla viktiga SAP HANAs komponenter som valts tidigare](./media/hana-get-started/image032.jpg)

Kontrol lera `\<HANA SID\>adm user` informationen i/etc/passwd-katalogen. Leta efter `azdadm`, som du ser i följande skärm bild:

![HANA \<HANA SID \>adm användar information som anges i katalogen/etc/passwd](./media/hana-get-started/image033.jpg)

När du har installerat SAP HANA med HDBLCM kan du se fil strukturen i SAP HANA Studio, som du ser i följande skärm bild. SAPABAP1-schemat, som innehåller alla SAP NetWeaver-tabeller, är inte tillgängligt ännu.

![SAP HANA fil struktur i SAP HANA Studio](./media/hana-get-started/image034.jpg)

När du har installerat SAP HANA kan du installera SAP-NetWeaver ovanpå den. Som du ser i följande skärm bild utfördes installationen som en distribuerad installation med hjälp av SWPM. Den här processen beskrivs i föregående avsnitt. När du installerar databas instansen med hjälp av SWPM anger du samma data med hjälp av HDBLCM. Du kan till exempel ange värd namnet, HANA SID och instans nummer. SWPM använder sedan den befintliga HANA-installationen och lägger till fler scheman.

![En distribuerad installation som utförs med hjälp av SWPM](./media/hana-get-started/image035b.jpg)

Följande skärm bild visar installations steget SWPM där du anger data om DBACOCKPIT-schemat:

![Installations steget SWPM där DBACOCKPIT schema data anges](./media/hana-get-started/image036b.jpg)

Ange data om SAPABAP1-schemat:

![Ange data om SAPABAP1-schemat](./media/hana-get-started/image037b.jpg)

När installationen av SWPM-databas instansen har slutförts kan du se SAPABAP1-schemat i SAP HANA Studio:

![SAPABAP1-schemat i SAP HANA Studio](./media/hana-get-started/image038b.jpg)

När SAP-app-servern och SAP GUI-installationen har avslut ATS verifierar du HANA DB-instansen med hjälp av transaktionen för att bekräfta **DBA-cockpit** :

![HANA DB-instansen verifierades med cockpit-transaktionen för DBA](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Nedladdning av SAP-program
Du kan hämta program vara från SAP Service Marketplace, som du ser i följande skärm bilder.

Ladda ned NetWeaver 7,5 för Linux/HANA:

 ![Installations-och uppgraderings fönster för SAP-tjänsten för nedladdning av NetWeaver 7,5](./media/hana-get-started/image001.jpg)

Hämta HANA SP12 Platform Edition:

 ![Installations-och uppgraderings fönster för SAP-tjänsten för nedladdning av HANA SP12 Platform Edition](./media/hana-get-started/image002.jpg)

