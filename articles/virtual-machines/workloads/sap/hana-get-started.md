---
title: 'Snabbstart: Manuell installation av single instance SAP HANA på Azure Virtual Machines | Microsoft Docs'
description: Snabbstartsguide för manuell installation av single instance SAP HANA på Azure Virtual Machines
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 45c7e1b76f64db142fc8fdca85b1e1fa9aca6a42
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Snabbstart: Manuell installation av single instance SAP HANA på Azure Virtual Machines
## <a name="introduction"></a>Introduktion
Den här guiden hjälper dig att konfigurera en enskild instans SAP HANA på virtuella Azure-datorer (VM) när du installerar SAP NetWeaver 7.5 och SAP HANA 1.0 SP12 manuellt. Den här guiden fokuserar på distribuera SAP HANA på Azure. Ersätter inte SAP-dokumentationen. 

>[!Note]
>Den här guiden beskriver distributioner av SAP HANA i virtuella Azure-datorer. Information om hur du distribuerar SAP HANA till HANA stora instanser finns [med SAP på virtuella Azure-datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started).
 
## <a name="prerequisites"></a>Förutsättningar
Den här handboken förutsätts att du är bekant med denna infrastruktur som en tjänst (IaaS) grunderna som:
 * Hur du distribuerar virtuella datorer eller virtuella nätverk via Azure-portalen eller PowerShell.
 * Azure plattformsoberoende kommandoradsgränssnittet (CLI), inklusive möjligheten att använda mallar för JavaScript Object Notation (JSON).

Den här guiden förutsätter också att du är bekant med:
* SAP HANA och SAP NetWeaver och hur du installerar dem lokalt.
* Installera och SAP HANA och SAP programinstanser på Azure.
* Följande koncept och procedurer:
   * Planera för SAP-distribution på Azure, inklusive Azure Virtual Network planerings- och användningen av Azure Storage. Se [SAP NetWeaver på Azure virtuella datorer (VM) - guide för planering och implementering](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide).
   * Distribution av principer och sätt att distribuera virtuella datorer i Azure. Se [distribution av virtuella datorer i Azure för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide).
   * Hög tillgänglighet för SAP NetWeaver ASCS (ABAP SAP centrala Services), SCS (SAP centrala Services) och ÄNDARE (utvärderas inleverans lösning) på Azure. Se [hög tillgänglighet för SAP NetWeaver på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide).
   * Information om hur du förbättrar effektiviteten i utnyttjar en multi-SID-installation av ASCS/SCS på Azure. Se [skapa en konfiguration för SAP NetWeaver multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid). 
   * Principer för SAP NetWeaver som körs baserat på Linux-driven virtuella datorer i Azure. Se [körs SAP NetWeaver på Microsoft Azure SUSE Linux virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart). Den här guiden innehåller specifika inställningar för Linux i virtuella Azure-datorer och information om hur du kopplar korrekt Azure storage-diskar till Linux virtuella datorer.

Just nu är certifierats Azure Virtual Machines av SAP för SAP HANA skala upp konfigurationer. Skalbar konfigurationer med SAP HANA arbetsbelastningar stöds inte ännu. För SAP HANA med hög tillgänglighet i fall av skala upp konfigurationer finns [hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VM)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

Om du vill hämta en SAP HANA-instans eller S/4HANA eller BW/4HANA system som distribueras i mycket snabb tid bör du överväga användning av [SAP installation Molnbibliotek](http://cal.sap.com). Det finns mer information om hur du distribuerar till exempel en S/4HANA systemet via SAP CAL på Azure i [handboken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Allt du behöver ha är en Azure-prenumeration och en SAP-användare som kan registreras med SAP Molnbibliotek för installation.

## <a name="additional-resources"></a>Ytterligare resurser
### <a name="sap-hana-backup"></a>SAP HANA-säkerhetskopiering
Information om säkerhetskopiering av databaser för SAP HANA på Azure Virtual Machines finns i:
* [Säkerhetskopiering guide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [SAP HANA säkerhetskopia av ögonblicksbilder för lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Molnbibliotek installation
Information om hur du använder SAP Molnbibliotek installation för att distribuera S/4HANA eller BW/4HANA finns [distribuera SAP S/4HANA eller BW/4HANA på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA-stödda operativsystem
Information om operativsystem som stöds av SAP HANA finns [SAP stöd Obs #2235581 - SAP HANA: operativsystem som stöds](https://launchpad.support.sap.com/#/notes/2235581/E). Virtuella Azure-datorer stöder endast en delmängd av dessa operativsystem. Följande operativsystem stöds för att distribuera SAP HANA i Azure: 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

För ytterligare SAP-dokumentation om SAP HANA och olika Linux-operativsystem, se:

* [Stöd för SAP-kommentar #171356 - SAP-program på Linux: allmän Information](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP stöd Obs #1944799 - SAP HANA riktlinjer för Installation av operativsystemet SLES](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [Stöd för SAP-kommentar #2205917 - SAP HANA DB rekommenderade OS-inställningar för SLES 12 för SAP-program](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP-stöd kommentar #1984787 - SUSE Linux Enterprise Server 12: Installationsinformation](https://launchpad.support.sap.com/#/notes/1984787)
* [Stöd för SAP-kommentar #1391070 - lösningar för Linux-UUID](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP stöd Obs #2009879 - SAP HANA riktlinjer för Red Hat Enterprise Linux (RHEL)-operativsystem](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: rekommenderas OS-inställningar för RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)

### <a name="sap-monitoring-in-azure"></a>SAP övervakning i Azure
Information om SAP övervakning i Azure finns i:

* [SAP-kommentar 2191498](https://launchpad.support.sap.com/#/notes/2191498/E). Detta beskrivs SAP ”förbättrad övervakning” med Linux virtuella datorer i Azure. 
* [SAP-kommentar 1102124](https://launchpad.support.sap.com/#/notes/1102124/E). Detta beskrivs information om SAPOSCOL på Linux. 
* [SAP-kommentar 2178632](https://launchpad.support.sap.com/#/notes/2178632/E). Detta beskrivs övervakning nyckelvärden för SAP på Microsoft Azure.

### <a name="azure-vm-types"></a>Azure VM-typer
Azure VM-typer och SAP-stödda arbetsbelastningsscenarier som används med SAP HANA dokumenteras i [SAP certifierade IaaS plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). 

Azure VM-typer som är certifierade av SAP för SAP NetWeaver- eller programnivå S/4HANA dokumenteras i [SAP Obs 1928533 - SAP-program i Azure: produkter och Virtuella Azure-typer](https://launchpad.support.sap.com/#/notes/1928533/E).

>[!Note]
>Azure-SAP-Linux integration stöds bara på Azure Resource Manager och inte den klassiska distributionsmodellen. 

## <a name="manual-installation-of-sap-hana"></a>Manuell installation av SAP HANA
Den här handboken beskrivs hur du manuellt installera SAP HANA på virtuella Azure-datorer på två olika sätt:

* Med hjälp av SAP programvara etablering Manager (SWPM) som en del av en distribuerad installation NetWeaver i ”installera databasinstans” steg
* Med hjälp av SAP HANA-databas livscykel Enhetshanteraren, HDBLCM och sedan installera NetWeaver

Du kan också använda SWPM för att installera alla komponenter (SAP HANA SAP-programserver och ASCS-instans) i en enda virtuell dator, enligt beskrivningen i det här [SAP HANA-bloggen meddelande](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/). Det här alternativet är inte beskrivs i den här snabbstartsguide, men de problem som du måste ta hänsyn till är samma.

Innan du startar en installation, rekommenderar vi att du läser den ”förbereda Azure virtuella datorer för manuell installation av SAP HANA” senare i den här handboken. Då kan du förhindra flera grundläggande fel som kan uppstå när du använder endast en Azure VM standardkonfiguration.

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>Viktiga steg för SAP HANA-installationen när du använder SAP SWPM
Det här avsnittet innehåller viktiga steg för installation av en manuell, single instance SAP HANA när du använder SAP SWPM för att utföra en distribuerad installation SAP NetWeaver 7.5. Enskilda stegen beskrivs i detalj i skärmbilder senare i den här guiden.

1. Skapa ett Azure-nätverk som innehåller två test virtuella datorer.
2. Distribuera två virtuella Azure-datorer med operativsystem (i vårt exempel SUSE Linux Enterprise Server (SLES) och SLES för SAP program 12 SP1), enligt Azure Resource Manager-modellen.
3. Bifoga två Azure standard eller premium-lagringsdiskar (till exempel 75 GB eller 500 GB diskar) för VM-programservern.
4. Koppla premium lagringsdiskar till HANA DB servern VM. Mer information finns i avsnittet ”Disk installation” senare i den här guiden.
5. Beroende på storleken eller dataflöde krav, bifoga flera diskar och skapa stripe-volymer genom att använda hantering av logisk volym eller en administrationsverktyget för flera enheter (MDADM) på nivån OS inuti den virtuella datorn.
6. Skapa XFS filsystem på anslutna diskar eller logiska volymer.
7. Montera nya XFS filsystemen på OS-nivå. Använda en filsystem för SAP-programvaran. Använda andra filsystemet /sapmnt katalog-och säkerhetskopior, till exempel. Montera filsystem XFS på diskar med lagringsutrymme premium som /hana och /usr/sap på SAP HANA DB-servern. Den här processen är nödvändigt att förhindra att rot-filsystem, som inte är stor på Linux Azure Virtual Machines, fyller.
8. Ange den lokala IP-adresser test virtuella datorer i filen/etc/hosts.
9. Ange den **nofail** parameter i fstab-filen/etc /.
10. Ange parametrarna för Linux-kernel enligt Linux OS-versionen som du använder. Mer information finns i de lämpliga SAP anteckningar diskuterar HANA och avsnittet ”Kernel parametrar” i den här guiden.
11. Lägg till växlingsutrymme.
12. Du kan också installera en grafisk desktop på testdatorn virtuella datorer. Annars Använd en fjärrinstallation av SAPinst.
13. Hämta SAP-program från SAP Service Marketplace.
14. Installera SAP ASCS-instansen på app-servern VM.
15. Dela katalogen /sapmnt mellan test virtuella datorer med hjälp av NFS. Programserver VM är den NFS-servern.
16. Installera den databasinstans, inklusive HANA, med hjälp av SWPM på DB servern VM.
17. Installera den primära servern (PROVIDERADRESSER) på programservern VM.
18. Starta SAP-hanteringskonsolen (SAP MC). Ansluta till SAP-Gränssnittet eller HANA Studio, t.ex.

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>Viktiga steg för SAP HANA-installationen när du använder HDBLCM
Det här avsnittet innehåller viktiga steg för installation av en manuell, single instance SAP HANA när du använder SAP HDBLCM för att utföra en distribuerad installation SAP NetWeaver 7.5. Enskilda stegen beskrivs i detalj i skärmdumpar i den här guiden.

1. Skapa ett Azure-nätverk som innehåller två test virtuella datorer.
2. Distribuera två virtuella Azure-datorer med operativsystem (i vårt exempel SLES och SLES för SAP program 12 SP1) enligt Azure Resource Manager-modellen.
3. Koppla två Azure standard eller premium-lagringsdiskar (till exempel 75 GB eller 500 GB diskar) till app-servern VM.
4. Koppla premium lagringsdiskar till HANA DB servern VM. Mer information finns i avsnittet ”Disk installation” senare i den här guiden.
5. Beroende på storleken eller dataflöde, bifoga flera diskar och skapa stripe-volymer genom att använda hantering av logisk volym eller en administrationsverktyget för flera enheter (MDADM) på nivån OS inuti den virtuella datorn.
6. Skapa XFS filsystem på anslutna diskar eller logiska volymer.
7. Montera nya XFS filsystemen på OS-nivå. Använda en /sapmnt katalog-och säkerhetskopior, till exempel använder en filsystem för SAP-programvaran. Montera filsystem XFS på diskar med lagringsutrymme premium som /hana och /usr/sap på SAP HANA DB-servern. Den här processen är nödvändigt för att förhindra att rot-filsystem, som inte är stor på Linux Azure Virtual Machines, fyller upp.
8. Ange den lokala IP-adresser test virtuella datorer i filen/etc/hosts.
9. Ange den **nofail** parameter i fstab-filen/etc /.
10. Ange parametrarna för kernel enligt Linux OS-versionen som du använder. Mer information finns i de lämpliga SAP anteckningar diskuterar HANA och avsnittet ”Kernel parametrar” i den här guiden.
11. Lägg till växlingsutrymme.
12. Du kan också installera en grafisk desktop på testdatorn virtuella datorer. Annars Använd en fjärrinstallation av SAPinst.
13. Hämta SAP-program från SAP Service Marketplace.
14. Skapa en grupp, sapsys, med grupp-ID 1001 på servern HANA DB VM.
15. Installera SAP HANA på DB servern VM via HANA databasen livscykel Manager (HDBLCM).
16. Installera SAP ASCS-instansen på app-servern VM.
17. Dela katalogen /sapmnt mellan test virtuella datorer med hjälp av NFS. Programserver VM är den NFS-servern.
18. Installera den databasinstans, inklusive HANA, med hjälp av SWPM på servern HANA DB VM.
19. Installera den primära servern (PROVIDERADRESSER) på programservern VM.
20. Starta SAP MC. Ansluta till SAP-Gränssnittet eller HANA Studio.

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>Förbereder virtuella Azure-datorer för en manuell installation av SAP HANA
Det här avsnittet beskrivs i följande avsnitt:

* OS-uppdateringar
* Installationen av disk
* Kernel-parametrar
* Filsystem
* Den/etc/hosts-filen
* Fstab-filen/etc /

### <a name="os-updates"></a>OS-uppdateringar
Sök efter Linux OS-uppdateringar och korrigeringar innan du installerar ytterligare programvara. Genom att installera en korrigering, kanske du kan undvika ett anrop till supportavdelningen.

Kontrollera att du använder:
* SUSE Linux Enterprise Server för SAP-program.
* Red Hat Enterprise Linux för SAP-program eller Red Hat Enterprise Linux för SAP HANA. 

Om du inte redan gjort registrera OS-distributionen med prenumerationen Linux från Linux-leverantören. Observera att SUSE operativsystemsavbildningar för SAP-program som redan innehåller tjänster och som är registrerade automatiskt.

Här är ett exempel på sökning efter tillgängliga korrigeringar för SUSE Linux med hjälp av den **zypper** kommando:

 `sudo zypper list-patches`

Beroende på vilken typ av problem är korrigeringsprogram som klassificerade efter kategori och allvarlighetsgrad. Vanliga värden för kategori: **säkerhet**, **rekommenderas**, **valfria**, **funktionen**, **dokument**, eller **yast**.
Vanliga värden för allvarlighetsgrad: **kritiska**, **viktiga**, **måttlig**, **låg**, eller **ospecificerade**.

Den **zypper** kommandot söks endast de uppdateringar som kräver din installerade paket. Du kan till exempel använda det här kommandot:

`sudo zypper patch  --category=security,recommended --severity=critical,important`

Du kan lägga till parametern `--dry-run` att testa uppdateringen utan att faktiskt uppdatera systemet.


### <a name="disk-setup"></a>Installationen av disk
Filsystemet roten i en Linux VM på Azure har en storleksbegränsning. Därför är det nödvändigt att koppla ytterligare diskutrymme på en Azure-dator för att köra SAP. Användning av Azure standardlagring diskar kan vara tillräcklig för SAP programserver virtuella Azure-datorer. Men för SAP HANA DBMS Azure virtuella datorer kan är användningen av Azure Premium Storage diskar för produktion och icke-produktion implementeringar obligatorisk.

Baserat på de [lagringskraven för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), lagringskonfigurationen Azure Premium föreslås: 

| VM-SKU | RAM |  / hana/data och loggfilen/hana / <br /> stripe-volymer med LVM eller MDADM | / hana/delade | / Root volym | / usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

I den föreslagna diskkonfigurationen placeras HANA datavolym och loggvolym på samma uppsättning med diskar med lagringsutrymme Azure premium stripe med LVM eller MDADM. Du behöver inte definiera alla RAID-nivå för redundans eftersom Azure Premium-lagring för att hålla tre bilder diskar för redundans. Kontrollera att du konfigurerar tillräckligt med utrymme genom att kontakta den [lagringskraven för SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) och [uppdatering Guide och SAP HANA-serverinstallation](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm). Överväg också annan virtuell hårddisk (VHD) genomströmning volymer olika Azure premium storage diskar enligt beskrivningen i [Premium-lagring med hög prestanda och hanterade diskar för virtuella datorer](https://docs.microsoft.com/azure/storage/storage-premium-storage). 

Du kan lägga till fler diskar i premium-lagring HANA DBMS virtuella datorer för att lagra säkerhetskopior av databasen eller transaktionsloggen.

Mer information om de två huvudsakliga verktyg som används för att konfigurera striping finns i följande artiklar:

* [Konfigurera programvarubaserad RAID på Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Konfigurera LVM på en virtuell Linux-dator i Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Mer information om att diskar till Azure virtuella datorer som kör Linux som gäst OS finns [lägga till en disk till en Linux-VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Premium-lagring kan du definiera disk cachelagring lägen. För stripe uppsättningen /hana/data och /hana/log, ska cachelagring på disk inaktiveras. För andra volymer (diskar), cachelagring läge ska anges till **ReadOnly**.

Mer information finns i [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../../windows/premium-storage.md).

Exempel JSON-mallar för att skapa virtuella datorer, finns på [Azure Quickstart mallar](https://github.com/Azure/azure-quickstart-templates).
Vm-enkel-sles mallen är en grundläggande mall. Den innehåller ett avsnitt för lagring, med en ytterligare 100 GB data-disk. Den här mallen kan användas som bas. Du kan anpassa mallen till din specifika konfiguration.

>[!Note]
>Det är viktigt att ansluta disken Azure storage med hjälp av en UUID som beskrivs i [kör SAP NetWeaver på Microsoft Azure SUSE Linux virtuella datorer](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

I testmiljön bifogades två Azure standardlagring diskar till SAP app-servern VM, som visas i följande skärmbild. En disk lagras alla SAP-program (inklusive NetWeaver 7.5, SAP-GUI och SAP HANA) för installation. Den andra disken sett till att tillräckligt med ledigt utrymme är tillgängligt för ytterligare krav (till exempel säkerhetskopierings- och data) och /sapmnt katalog (det vill säga SAP-profiler) delas av alla virtuella datorer som tillhör samma SAP liggande.

![SAP HANA app server diskar fönster som visar två diskar och deras storlek](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>Kernel-parametrar
SAP HANA kräver särskilda Linux kernel-inställningar som inte är en del av avbildningarna standard Azure-galleriet och måste anges manuellt. Parametrarna kan vara olika beroende på om du använder SUSE eller Red Hat. SAP-anteckningar ovan ger information om dessa parametrar. I skärmbilderna visas, användes SUSE Linux 12 SP1. 

SLES för SAP program 12 GA och SLES för SAP program 12 SP1 har du ett nytt verktyg **justerade adm**, som ersätter gammalt **sapconf** verktyget. En speciell profil för SAP HANA är tillgänglig för **justerade adm**. Ange följande för att finjustera systemet för SAP HANA som rotanvändare:

   `tuned-adm profile sap-hana`

Mer information om **justerade adm**, finns det [SUSE dokumentationen om justerade adm](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip).

I följande skärmbild visas hur **justerade adm** ändras den `transparent_hugepage` och `numa_balancing` värden, enligt de nödvändiga inställningarna för SAP HANA.

![Verktyget justerade adm ändrar värden enligt nödvändiga SAP HANA-inställningar](./media/hana-get-started/image005.jpg)

Använd för att göra inställningar för SAP HANA-kernel permanent **grub2** på SLES 12. Mer information om **grub2**, gå till den [Configuration filstruktur](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen.

Följande skärmbild visar hur kernel-inställningarna har ändrats i konfigurationsfilen och sedan kompileras med hjälp av **grub2 mkconfig**:

![Kernel-inställningar har ändrats i konfigurationsfilen och kompileras med hjälp av grub2 mkconfig](./media/hana-get-started/image006.jpg)

Ett annat alternativ är att ändra inställningarna med hjälp av YaST och **startprogram** > **Kernel parametrar** inställningar:

![Fliken Kernel parametrar inställningar i YaST startprogram](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>Filsystem
Följande skärmbild visar två filsystem som har skapats på SAP-app-servern VM ovanpå två anslutna Azure standardlagring diskar. Båda filsystem är av typen XFS och är monterade /sapdata och /sapsoftware.

Det är inte obligatoriskt att strukturera din filsystem i det här sättet. Du har andra alternativ för att strukturera diskutrymmet. Det mest viktigt övervägandet är att förhindra att filsystemet rot utrymmet tar slut.

![Två filsystem som skapats på SAP app-servern VM](./media/hana-get-started/image008.jpg)

Om SAP HANA DB VM under installationen av databasen när du använder SAPinst (SWPM) och **vanliga** installationsalternativ, allt installeras under /hana och /usr/sap. Standardplatsen för SAP HANA loggsäkerhetskopiering är under /usr/sap. Igen, eftersom det är viktigt att förhindra att filsystemet rot slut lagringsutrymme, kontrollerar du att det finns tillräckligt med ledigt utrymme under /hana och /usr/sap innan du installerar SAP HANA med hjälp av SWPM.

En beskrivning av standard filsystemet layouten för SAP HANA finns i [uppdatering Guide och SAP HANA-serverinstallation](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm).

![Ytterligare filsystem som skapats på SAP app-servern VM](./media/hana-get-started/image009.jpg)

När du installerar SAP NetWeaver på en standard SLES/SLES för SAP program 12 Azure-galleriet avbildningen visas ett meddelande om att det inte finns några växlingsutrymme som visas i följande skärmbild. För att stänga det här meddelandet, du kan manuellt lägga till en växlingsfil med hjälp av **dd**, **mkswap**, och **swapon**. Att lära dig hur, söka efter ”lägga till en växlingsfil manuellt” i den [med YaST Partitioneraren](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip) i SUSE-dokumentationen.

Ett annat alternativ är att konfigurera växlingsutrymme med hjälp av Linux VM-agenten. Mer information finns i [användarhandboken för Azure Linux-agenten](../../extensions/agent-linux.md).

![Popup-meddelande som talar om att det finns inte tillräckligt växlingsutrymme](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>Den/etc/hosts-filen
Innan du börjar installera SAP, kontrollera att du inkluderar värdnamn och IP-adresserna för de virtuella datorerna SAP i filen/etc/hosts. Distribuera SAP VMs inom ett virtuellt Azure-nätverk och sedan använda de interna IP-adresserna som visas här:

![Värdnamn och IP-adresserna för de virtuella datorerna SAP som anges i filen/etc/hosts](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>Fstab-filen/etc /

Är det bra att lägga till den **nofail** parameter till fstab-filen. Det här sättet om något går fel med diskar, den virtuella datorn inte låser sig i startprocessen. Men kom ihåg att ytterligare diskutrymme inte kanske är tillgänglig och processer kan fylla filsystemet rot. SAP HANA startar inte om /hana saknas.

![Lägg till parametern nofail i filen fstab](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>Den grafiska gör väldigt lätt skrivbordet på SLES 12/SLES för SAP program 12
Det här avsnittet beskrivs i följande avsnitt:

* Installera desktop gör väldigt lätt och xrdp på SLES 12/SLES för SAP program 12
* Kör Java-baserad SAP MC med Firefox i SLES 12/SLES för SAP program 12

Du kan också använda alternativ, till exempel Xterminal eller VNC (som inte beskrivs i den här guiden).

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>Installera desktop gör väldigt lätt och xrdp på SLES 12/SLES för SAP program 12
Du kan enkelt använda en grafisk skrivbord direkt i SAP Linux virtuella datorer att köra Firefox SAPinst, SAP GUI, SAP MC eller HANA Studio och ansluta till den virtuella datorn via Remote Desktop Protocol (RDP) från en Windows-dator om du har en Windows-bakgrund. Beroende på företagets principer för hur du lägger till grafiska användargränssnitt till produktion och icke-produktion Linux-baserade system, kanske du vill installera gör väldigt lätt på servern. Du installerar den gör väldigt lätt på ett Azure SLES 12/SLES för SAP program 12 VM:

1. Installera gör väldigt lätt skrivbordet genom att ange följande kommando (till exempel i ett PuTTY fönster):

   `zypper in -t pattern gnome-basic`

2. Installera xrdp för att tillåta en anslutning till den virtuella datorn via RDP:

   `zypper in xrdp`

3. Redigera /etc/sysconfig/windowmanager och ställa in fönstret standardhanteraren gör väldigt lätt:

   `DEFAULT_WM="gnome"`

4. Kör **chkconfig** att se till att xrdp startas automatiskt efter en omstart:

   `chkconfig -level 3 xrdp on`

5. Om du har problem med RDP-anslutning kan du försöka starta om (från PuTTY fönster, till exempel):

   `/etc/xrdp/xrdp.sh restart`

6. Om en omstart för xrdp som nämns i föregående steg inte fungerar kan du kontrollera en .pid-fil:

   `check /var/run` 

   Leta efter `xrdp.pid`. Om du hittar den ta bort den och försök att starta om igen.

### <a name="starting-sap-mc"></a>Från SAP MC
När du har installerat gör väldigt lätt skrivbordet kanske startar grafiska Java-baserad SAP MC från Firefox när det körs i en Azure SLES 12/SLES för SAP program 12 VM visas ett fel på grund av saknade Java-webbläsaren plugin-programmet.

URL som ska startas SAP MC är `<server>:5<instance_number>13`.

Mer information finns i [från hanteringskonsolen webbaserade SAP](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm).

Följande skärmbild visar ett felmeddelande som visas när Java-webbläsaren plugin saknas:

![Felmeddelande som anger saknas Java-webbläsaren plugin-program](./media/hana-get-started/image013.jpg)

Ett sätt att lösa problemet är att installera det saknade plugin-program med hjälp av YaST, som visas i följande skärmbild:

![Använder YaST för att installera plugin-programmet som saknas](./media/hana-get-started/image014.jpg)

När du har angett SAP Management-konsolens URL, visas ett meddelande där du uppmanas att aktivera plugin-programmet:

![Dialogrutan begär plugin-aktivering](./media/hana-get-started/image015.jpg)

Du kan också få ett felmeddelande om en saknad fil javafx.properties. Detta beror på behovet av Oracle Java 1.8 SAP GUI 7.4. (Se [SAP-kommentar 2059429](https://launchpad.support.sap.com/#/notes/2059424).) Varken IBM Java-version eller openjdk paketet levereras med SLES/SLES för SAP program 12 innehåller nödvändiga javafx.properties-filen. Lösningen är att hämta och installera Java SE 8 från Oracle.

Information om ett liknande problem med openjdk openSUSE finns i konversation [SAPGui 7.4 Java för openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306).

## <a name="manual-installation-of-sap-hana-swpm"></a>Manuell installation av SAP HANA: SWPM
Serien med skärmbilder i det här avsnittet visar viktiga steg för att installera SAP NetWeaver 7.5 och SAP HANA SP12 när du använder SWPM (SAPinst). Som en del av en installation av NetWeaver 7.5 Installera SWPM även HANA-databas som en enda instans.

I en testmiljö där exempel installerat vi en avancerad Business Application Programming (ABAP) app-servern. I följande skärmbild visas som vi använde den **distribuerade System** alternativet att installera ASCS och primära server-instanser i en virtuell dator i Azure och SAP HANA som databassystem i en annan Azure VM.

![ASCS och primär server programinstanser installeras med hjälp av alternativet Distributed System](./media/hana-get-started/image012.jpg)

När ASCS-instansen är installerad på app-servern VM och är inställd på ”grön” i konsolen för hantering av SAP (visas i följande skärmbild), delas katalogen /sapmnt (inklusive katalogen SAP profil) med SAP HANA DB servern VM. DB-installationssteget behöver åtkomst till den här informationen. Det bästa sättet att ge åtkomst är att använda NFS, som kan konfigureras med hjälp av YaST.

![SAP-hanteringskonsolen visar ASCS-instans installerad på app-servern VM och inställd på ”grönt”](./media/hana-get-started/image016.jpg)

På app-serverns Virtuella katalogen /sapmnt bör delas via NFS med hjälp av den **rw** och **no_root_squash** alternativ. Standardvärdena är **ro** och **root_squash**, vilket kan leda till problem när du installerar databasinstansen.

![Dela katalogen /sapmnt via NFS med hjälp av alternativen rw och no_root_squash](./media/hana-get-started/image017b.jpg)

Som i nästa skärmbild visas /sapmnt resursen från app-serverns Virtuella måste konfigureras på servern för SAP HANA DB VM med hjälp av **NFS-klienten** (och YaST).

![Resursen /sapmnt konfigureras med hjälp av NFS-klient](./media/hana-get-started/image018b.jpg)

Utföra en distribuerad installation NetWeaver 7.5 (**databasinstans**), som visas i följande skärmbild, logga in på servern VM för SAP HANA DB och starta SWPM.

![Installera en databasinstans genom att logga in på servern VM för SAP HANA DB och starta SWPM](./media/hana-get-started/image019.jpg)

När du har valt **vanliga** installation och sökvägen till installationsmediet, ange en DB-SID, värdnamn, instans-numret och DB lösenord.

![SAP HANA system administratör inloggning databas](./media/hana-get-started/image035b.jpg)

Ange lösenordet för DBACOCKPIT schemat:

![Lösenord-rutan för DBACOCKPIT schemat](./media/hana-get-started/image036b.jpg)

Ange en fråga för SAPABAP1 schemat lösenord:

![Ange en fråga för SAPABAP1 schemat lösenord](./media/hana-get-started/image037b.jpg)

Efter varje aktivitet har slutförts visas en grön bockmarkering bredvid varje fas av installationsprocessen DB. Meddelandet ”körning av... Databasen instans har slutförts ”visas.

![Slutföra uppgiften fönstret bekräftelsemeddelande](./media/hana-get-started/image023.jpg)

Efter installationen bör hanteringskonsolen SAP också visa DB-instans som ”grönt” och visa en fullständig lista över processer för SAP HANA (hdbindexserver, hdbcompileserver och så vidare).

![SAP-hanteringskonsolen fönster med listan över SAP HANA-processer](./media/hana-get-started/image024.jpg)

Följande skärmbild visar delarna av filstruktur under katalogen /hana/shared SWPM skapas under installationen av HANA. Eftersom det inte går att ange en annan sökväg, är det viktigt att montera ytterligare diskutrymme under katalogen /hana innan SAP HANA-installationen med hjälp av SWPM. Detta förhindrar att filsystemet rot slut på ledigt utrymme.

![Filstruktur för /hana/shared katalogen skapas under installationen av HANA](./media/hana-get-started/image025.jpg)

Den här skärmbilden visar katalogen /usr/sap filstruktur:

![/ Usr/sap filen katalogstrukturen](./media/hana-get-started/image026.jpg)

Det sista steget i distribuerade ABAP installationen är att installera den primära server-instansen:

![ABAP installationen visar primära server-instans som det sista steget](./media/hana-get-started/image027b.jpg)

När primära server-instansen och SAP GUI installeras, använda den **DBA Cockpit** transaktion att bekräfta att SAP HANA-installationen har slutförts korrekt:

![DBA Cockpit fönstret bekräftar att installationen lyckades](./media/hana-get-started/image028b.jpg)

Som ett sista steg kan du vill först installerar HANA Studio i SAP app-serverns virtuella datorn och sedan ansluta till SAP HANA-instans som körs på servern DB VM:

![Installera SAP HANA Studio i SAP app-servern VM](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>Manuell installation av SAP HANA: HDBLCM
Utöver de SAP HANA som en del av en distribuerad installation med hjälp av SWPM, kan du installera HANA fristående först med hjälp av HDBLCM. Du kan sedan installera SAP NetWeaver 7.5, t.ex. Skärmbilderna i det här avsnittet visar hur den här processen fungerar.

Mer information om verktyget HANA HDBLCM finns:

* [Att välja rätt SAP HANA HDBLCM för uppgift](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [Hanteringsverktyg för SAP HANA livscykel](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [Guide för uppdatering och SAP HANA-serverinstallation](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

Du undviker problem med en grupp-ID standardinställning för den `\<HANA SID\>adm user` (som skapats av verktyget HDBLCM) definiera en ny grupp som kallas `sapsys` med hjälp av grupp-ID `1001` innan du installerar SAP HANA via HDBLCM:

![Ny grupp ”sapsys” definierats med hjälp av grupp-ID 1001](./media/hana-get-started/image030.jpg)

När du startar HDBLCM första gången visas ett enkelt start-menyn. Välj objekt 1, **installera nya**som visas i följande skärmbild:

![”Installera nya system” alternativ i fönstret HDBLCM start](./media/hana-get-started/image031.jpg)

Följande skärmbild visar de viktiga alternativ som du valde tidigare.

> [!IMPORTANT]
> Kataloger som heter för HANA loggen och datavolymer, samt installationssökvägen (hana/delade i det här exemplet) och /usr/sap, får inte vara en del av filsystemet rot. Dessa kataloger som hör till Azure data diskarna som kopplats till den virtuella datorn (beskrivs i avsnittet ”Disk installation”). Den här metoden förhindrar filsystemet rot utrymmet tar slut. I följande skärmbild kan du se att systemadministratören HANA har användar-ID `1005` och är en del av den `sapsys` grupp (ID `1001`) som har definierats innan installationen.

![Lista över alla nyckelkomponenterna för SAP HANA som tidigare markerade certifikatmallen](./media/hana-get-started/image032.jpg)

Du kan kontrollera den `\<HANA SID\>adm user` (`azdadm` i följande skärmbild) information i/etc/passwd katalogen:

![HANA \<HANA SID\>adm användarinformation som anges i/etc/passwd katalogen](./media/hana-get-started/image033.jpg)

När du har installerat SAP HANA med hjälp av HDBLCM ser filstruktur i SAP HANA Studio som visas i följande skärmbild. Schemat SAPABAP1 som innehåller tabellerna för SAP NetWeaver, är inte tillgänglig ännu.

![Filstruktur för SAP HANA i SAP HANA Studio](./media/hana-get-started/image034.jpg)

När du har installerat SAP HANA kan du installera SAP NetWeaver ovanpå den. I följande skärmbild visas utfördes installationen som en distribuerad installation med hjälp av SWPM (enligt beskrivningen i föregående avsnitt). När du installerar databasinstansen med hjälp av SWPM kan du ange samma data med hjälp av HDBLCM (till exempel värdnamn, HANA SID och instansnummer). SWPM sedan använder den befintliga installationen HANA och lägger till flera scheman.

![En distribuerad installation utförs med hjälp av SWPM](./media/hana-get-started/image035b.jpg)

Följande skärmbild visar installationssteget SWPM där du kan ange information om DBACOCKPIT schemat:

![SWPM installationssteget där DBACOCKPIT schemadata anges](./media/hana-get-started/image036b.jpg)

Ange information om SAPABAP1 schemat:

![Ange data om SAPABAP1 schemat](./media/hana-get-started/image037b.jpg)

När SWPM databasen instans installationen är klar kan du se SAPABAP1 schemat i SAP HANA Studio:

![SAPABAP1 schemat i SAP HANA Studio](./media/hana-get-started/image038b.jpg)

Slutligen när SAP app-servern och SAP GUI-installation har slutförts, du kan verifiera HANA DB-instans med hjälp av den **DBA Cockpit** transaktion:

![HANA DB-instans som verifieras med DBA Cockpit-transaktion](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>Hämtar för SAP-program
Du kan ladda ned programvara från SAP Service Marketplace enligt följande skärmbilderna.

Hämta NetWeaver 7.5 för Linux/HANA:

 ![SAP Service Installation och uppgradering fönster för att ladda ned NetWeaver 7.5](./media/hana-get-started/image001.jpg)

Ladda ner HANA SP12 plattform versionen:

 ![SAP Service Installation och uppgradering fönster för att ladda ned HANA SP12 plattform Edition](./media/hana-get-started/image002.jpg)

