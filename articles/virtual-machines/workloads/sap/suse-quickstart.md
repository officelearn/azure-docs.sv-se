---
title: Testa SAP NetWeaver på SUSE Linux-datorer för Microsoft Azure | Microsoft Docs
description: Testa SAP NetWeaver på Microsoft Azure virtuella SUSE Linux-datorer
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 032ab2a221f64d01af25056a4eff3ee3384de0c3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157232"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Köra SAP NetWeaver på virtuella SUSE Linux-datorer i Microsoft Azure
Den här artikeln beskrivs olika saker att tänka på när du kör SAP NetWeaver på Microsoft Azure SUSE Linux-datorer (VM). Från och med den 19 maj 2016 stöds officiellt SAP NetWeaver på SUSE Linux virtuella datorer på Azure. All information om Linux-versioner, SAP kernel-versioner och andra krav finns i SAP anteckning 1928533 ”SAP-program i Azure: Produkter som stöds och typer av Azure virtuella datorer ”.
Ytterligare dokumentation om SAP på virtuella Linux-datorer finns här: [Använd SAP på Linux-datorer (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Följande information hjälper dig att undvika vissa fallgropar.

## <a name="suse-images-on-azure-for-running-sap"></a>SUSE-avbildningar på Azure för att köra SAP
För att köra SAP NetWeaver på Azure, använda SUSE Linux Enterprise Server SLES 12 (SPx) eller SLES för SAP - också se SAP-kommentar 1928533. En särskild SUSE-avbildning som är i Azure Marketplace (”SLES 11 SP3 för SAP CAL”), men avbildningen är inte avsedd för allmän användning. Använd inte den här bilden eftersom den är reserverad för den [SAP Cloud Appliance Library](https://cal.sap.com/) lösning.  

Du måste använda ramverk för Azure Resource Manager-distribution för alla installationer på Azure. Om du vill söka efter SUSE SLES bilder och versioner genom att använda Azure PowerShell eller kommandoradsgränssnittet (CLI), använder du de kommandon som visas nedan. Du kan sedan använda utdata, till exempel att definiera den OS-avbildningen i en JSON-mall för att distribuera en ny SUSE Linux virtuell dator.
Dessa PowerShell-kommandon är giltiga för Azure PowerShell-version 1.0.1 och senare.

Det är fortfarande möjligt att använda standardbilder för SLES för SAP-installationer, rekommenderar vi för att använda den nya SLES för SAP-avbildningar. Dessa avbildningar är nu tillgängliga i Azures avbildningsgalleri. Mer information om dessa avbildningar kan hittas på motsvarande [Azure Marketplace-sidan]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) eller [webbsida för SUSE vanliga frågor och svar om SLES for SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Sök efter befintliga utgivare, inklusive SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Sök efter befintliga erbjudanden från SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Sök efter SUSE SLES-erbjudanden:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Sök efter en viss version av en SLES-SKU:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Installera WALinuxAgent i en SUSE-VM
Agenten kallas WALinuxAgent är en del av SLES-avbildningar på Azure Marketplace. Information om hur du installerar den manuellt (till exempel när du överför en SLES OS virtuell hårddisk (VHD) från en lokal plats) finns i:

* [openSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP ”utökad övervakning”
SAP ”utökad övervakning” är en obligatorisk förutsättning för att köra SAP på Azure. Kontrollera informationen i SAP Observera 2191498 ”SAP på Linux med Azure: Förbättrad övervakning ”.

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Koppla Azure-datadiskar till en virtuell Linux-dator
Aldrig montera Azure datadiskar till en virtuell Linux-dator med hjälp av enhets-ID. Använd istället universell unik identifierare (UUID). Var försiktig när du använder grafiska verktyg för att montera en Azure-datadiskar, till exempel. Kontrollera posterna i/etc/fstab.

Problem med enhets-ID är att den kan ändras och sedan Azure VM kan låser sig i startprocessen. För att lösa problemet, kan du lägga till parametern nofail i/etc/fstab. Men var försiktig med nofail eftersom program kan använda monteringspunkten som innan och kan skriva till filsystemet roten om en extern Azure-datadisk inte har monterats under alternativet för start.

Det enda undantaget montering via UUID är ansluta en OS-disk i felsökningssyfte, enligt beskrivningen i avsnittet som följer.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Felsöka en SUSE VM som inte är tillgänglig längre
Det kan finnas situationer där en SUSE VM på Azure låser sig i startprocessen (till exempel med ett fel som rör montera diskar). Du kan kontrollera det här problemet med hjälp av funktionen Start diagnostik för Azure Virtual Machines v2 i Azure-portalen. Mer information finns i [Startdiagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Ett sätt att lösa problemet är att koppla OS-disken från den skadade virtuella datorn till en annan SUSE VM på Azure. Sedan gör lämpliga ändringar som redigera/etc/fstab eller ta bort regler för udev, enligt beskrivningen i nästa avsnitt.

Det finns en viktig sak att tänka på. Distribuera flera virtuella SUSE-datorer från samma Azure Marketplace-avbildning (exempelvis SLES 11 SP4) orsakar OS-disken som alltid ska monteras av samma UUID. Därför använder UUID för att bifoga en OS-disk från en annan virtuell dator som har distribuerats med hjälp av samma resultat för Azure Marketplace-avbildning i två identiska UUID: N. Två identiska UUID: N orsak den virtuella datorn används för felsökning, starta från anslutna och skadade OS-disken i stället för den ursprungliga OS-disken.

Det finns två sätt att undvika problem:

* Använd en annan Azure Marketplace-avbildning för Virtuellt felsökningsdatorn (till exempel SLES 11 SPx i stället för SLES 12).
* Inte koppla skadade disken med Operativsystemet från en annan virtuell dator med hjälp av UUID--Använd något annat.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Ladda upp en SUSE-dator från en lokal plats till Azure
En beskrivning av stegen för att ladda upp en SUSE VM från en lokal plats till Azure finns i [Förbered en SLES- eller openSUSE-dator för Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Om du vill ladda upp en virtuell dator utan avetablering steg i slutet (till exempel för att hålla en befintlig SAP-installation, samt värdnamnet) kan du kontrollera följande:

* Kontrollera att OS-disken är monterad med UUID och inte enhets-ID. Ändra till UUID just-in-/ etc/fstab räcker inte för OS-disken. Dessutom Glöm inte att anpassa startprogrammet via YaST eller genom att redigera /boot/grub/menu.lst.
* Om du använder VHDX-formatet för SUSE-OS-disken och konvertera den till virtuell Hårddisk för uppladdning till Azure, är det troligt att nätverksenheten ändras från eth0 till eth1. Om du vill undvika problem när du senare startar på Azure, ändra tillbaka till eth0 enligt beskrivningen i [åtgärda eth0 i klonas SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Förutom det som beskrivs i artikeln, rekommenderar vi att du tar bort den här filen:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Du kan även installera Azure Linux-agenten (waagent) så att du slipper problem, så länge det inte finns flera nätverkskort.

## <a name="deploying-a-suse-vm-on-azure"></a>Distribution av SUSE virtuella datorer på Azure
Du bör skapa nya virtuella SUSE-datorer med hjälp av JSON-mallfiler i den nya Azure Resource Manager-modellen. När JSON-mallfil har skapats kan distribuera du den virtuella datorn med hjälp av följande CLI-kommando som ett alternativ till PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Läs mer om JSON-mallfiler [redigera Azure Resource Manager-mallar](../../../resource-group-authoring-templates.md) och [Azures snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).

Mer information om Azure klassiskt CLI och Azure Resource Manager finns i [använder den klassiska Azure CLI för Mac, Linux och Windows med Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>SAP licens- och nyckel
För SAP-Azure officiell certifiering introducerades en ny mekanism för att beräkna SAP maskinvarunyckeln som används för SAP-licens. SAP-kernel var tvungen att anpassas till att kontrollera användning av den nya algoritmen. Tidigare SAP kernel-versioner för Linux innehöll inte ändringar i den här koden. Därför kan i vissa situationer (till exempel Azure VM storleksändring), ändras maskinvarunyckel SAP och SAP-licensen har inte längre vara giltiga. En lösning tillhandahålls med nyare SAP Linux-kernel.  Detaljerad SAP kernel korrigeringar dokumenteras i SAP-kommentar 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf paket / anpassade adm
SUSE innehåller ett paket som heter ”sapconf” som hanterar en uppsättning SAP-specifika inställningar. Mer information om vad det här paketet gör och hur du installerar och använder den finns:  [Använda sapconf för att förbereda en SUSE Linux Enterprise Server för att köra SAP-system](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) och [vad är sapconf eller hur du förbereder en SUSE Linux Enterprise Server för att köra SAP-system?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Under tiden kan finns det ett nytt verktyg, som ersätter 'sapconf - justerad adm ”. En kan hitta mer information om det här verktyget efter två länkar:

- SLES dokumentation om ”anpassade adm-profil sap-hana finns [här](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_saptune.html) 

- Justering system för SAP-arbetsbelastningar med ”justerad-adm' - finns [här](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) i kapitlet 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>NFS-resursens i distribuerat SAP-installationer
Om du har en distribuerad installation – till exempel där du vill installera databasen och SAP-programservrar i separata virtuella datorer – kan du dela katalogen /sapmnt via Network File System (NFS). Kontrollera om ”no_root_squash” har angetts för resursen om du har problem med följande steg när du har skapat NFS-resurs för /sapmnt.

## <a name="logical-volumes"></a>Logiska volymer
Tidigare, om en krävs ett stort logiska över flera diskar i Azure-data (till exempel för SAP-databasen) rekommenderades att använda Raid-hanteringsverktyg MDADM eftersom Linux logiska Volume Manager (LVM) inte har helt har verifierats ännu på Azure. Läs hur du konfigurerar Linux-RAID på Azure med hjälp av mdadm i [konfigurera programvaru-RAID på Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Under tiden kan har fullt stöd för Azure Linux Logical Volume Manager från och med början av maj 2016 och kan användas som ett alternativ till MDADM. Mer information om LVM på Azure läser du:  
[Konfigurera LVM på en Linux-dator i Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Azure SUSE-lagringsplats
Om du har ett problem med åtkomst till standard Azure SUSE-lagringsplatsen kan använda du ett kommando för att återställa den. Dessa problem kan inträffa om du skapar en privat OS-avbildning i en Azure-region och sedan kopiera avbildningen till en annan Azure-region du distribuerar nya virtuella datorer som baseras på den här privata OS-avbildning. Kör följande kommando på den virtuella datorn:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gör väldigt lätt desktop
Om du vill använda desktop gör väldigt lätt för att installera en fullständig SAP demo-system i en enda virtuell dator – inklusive SAP-Gränssnittet, använda webbläsaren och SAP-hanteringskonsolen--det här tipset för att installera den på Azure SLES-avbildningar:

   För SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   För SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>SAP-stöd för Oracle på Linux i molnet
Det finns en begränsning med stöd från Oracle på Linux i virtualiserade miljöer. Även om den här begränsningen för support inte är ett Azure-specifika avsnitt, är det viktigt att förstå. SAP har inte stöd för Oracle på SUSE eller Red Hat i ett offentligt moln som Azure. Under tiden som kör Oracle DB i Azure stöds fullt ut av SAP i Oracle Linux (Se SAP anteckning 1928533). Om andra kombinationer krävs kan du kontakta Oracle direkt.

