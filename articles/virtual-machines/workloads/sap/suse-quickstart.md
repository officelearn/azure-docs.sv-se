---
title: Testa SAP NetWeaver på Microsoft Azure SUSE Linux Virtual Machines | Microsoft Docs
description: Testa SAP NetWeaver på Microsoft Azure virtuella SUSE Linux-datorer
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
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
ms.author: hermannd
ms.openlocfilehash: 9bb13cde07141c576a0ec78c3b48c5a20d6dbb67
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657663"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Köra SAP NetWeaver på virtuella SUSE Linux-datorer i Microsoft Azure
Den här artikeln beskrivs olika saker att tänka på när du kör SAP NetWeaver på Microsoft Azure SUSE Linux virtuella datorer (VM). Från och med 19 maj, 2016 stöds officiellt SAP NetWeaver på SUSE Linux virtuella datorer i Azure. All information om Linux-versioner, SAP kernel-versioner och andra krav finns i SAP Obs 1928533 ”SAP-program i Azure: produkter som stöds och Azure VM typer”.
Ytterligare dokumentation om SAP på virtuella Linux-datorer finns här: [med SAP på Linux virtuella datorer (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Följande information hjälper dig att undvika vissa fallgropar.

## <a name="suse-images-on-azure-for-running-sap"></a>SUSE avbildningar i Azure för att köra SAP
Använda SUSE Linux Enterprise Server SLES 12 (SPx) eller SLES för SAP - Se även SAP-kommentar 1928533 för att köra SAP NetWeaver på Azure. En särskild SUSE avbildning finns i Azure Marketplace (”SLES 11 SP3 för SAP CAL”), men avbildningen är inte avsedd för användning. Använd inte den här avbildningen eftersom den är reserverad för den [SAP Molnbibliotek installation](https://cal.sap.com/) lösning.  

Du behöver använda Azure Resource Manager distribution ramverket för alla installationer på Azure. Om du vill söka efter SUSE SLES avbildningar och versioner genom att använda Azure PowerShell eller Azure-kommandoradsgränssnittet (CLI), använder du de kommandon som visas nedan. Du kan sedan använda utdata, till exempel att definiera den OS-avbildningen i en JSON-mall för att distribuera en ny SUSE Linux virtuell dator.
Dessa PowerShell-kommandon är giltig för Azure PowerShell version 1.0.1 och senare.

Det är fortfarande möjligt att använda standard SLES avbildningar för SAP-installationer, har vi rekommenderar för att använda den nya SLES för SAP bilder. Dessa avbildningar är nu tillgängliga i galleriet Azure-avbildning. Mer information om dessa avbildningar kan hittas i den motsvarande [Azure Marketplace sidan]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) eller [webbsida för SUSE vanliga frågor och svar om SLES SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


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
* Leta efter SUSE SLES erbjudanden:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Leta efter en viss version av en SLES SKU:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Installera WALinuxAgent på en virtuell dator SUSE
Agenten kallas WALinuxAgent är en del av SLES avbildningar i Azure Marketplace. Information om hur du installerar den manuellt (till exempel när du överför en SLES OS virtuell hårddisk (VHD) från lokala) finns i:

* [openSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP ”förbättrad övervakning”
SAP ”förbättrad övervakning” är en obligatorisk förutsättning för att köra SAP på Azure. Läs mer i SAP Observera 2191498 ”SAP på Linux med Azure: förbättrad övervakning”.

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Kopplar Azure datadiskar till en Azure Linux-dator
Aldrig montera Azure datadiskar på en Azure Linux-dator med hjälp av enhets-ID. Använd i stället universellt Unik identifierare (UUID). Var försiktig när du använder grafiska verktyg för att montera Azure datadiskar, t.ex. Kontrollera posterna i /etc/fstab.

Problemet med enhets-ID är att den kan ändras och sedan den virtuella Azure-datorn kan hänga i startprocessen. För att åtgärda problemet, kan du lägga till parametern nofail i /etc/fstab. Men var försiktig med nofail eftersom program kan använda monteringspunkten som innan du kan skriva till filsystemet rot om en extern Azure-datadisk inte monterade under starten av.

Det enda undantaget montering via UUID är kopplar en OS-disk i felsökningssyfte, enligt beskrivningen i avsnittet som följer.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Felsöka en SUSE VM som inte är tillgänglig längre
Det kan finnas situationer där en SUSE VM på Azure låser sig i startprocessen (till exempel med ett fel som rör montera diskar). Du kan kontrollera det här problemet med hjälp av funktionen Start diagnostik för virtuella datorer i Azure v2 i Azure-portalen. Mer information finns i [starta diagnostik](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Ett sätt att lösa problemet är att koppla OS-disken från den skadade virtuella datorn till en annan SUSE VM på Azure. Sedan göra ändringar som redigera /etc/fstab eller ta bort nätverket udev regler som beskrivs i nästa avsnitt.

Det finns en viktig sak att tänka på. Distribuera flera SUSE virtuella datorer från samma Azure Marketplace-avbildning (exempelvis SLES 11 SP4) medför OS-disk som alltid ska monteras av samma UUID. Därför använder UUID för att koppla en OS-disk från en annan virtuell dator som har distribuerats med hjälp av samma resultat för Azure Marketplace-avbildningen i två identiska UUID: er. Två identiska UUID: er orsak VM används för felsökning, startar från anslutna och skadat OS-disken i stället för den ursprungliga OS-disken.

Det finns två sätt att undvika problem:

* Använd en annan Azure Marketplace-avbildning för felsökning VM (till exempel SLES 11 SPx i stället för SLES 12).
* Inte bifoga skadade OS-disk från en annan virtuell dator med hjälp av UUID--Använd något annat.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Ladda upp en SUSE virtuell dator från lokal till Azure
En beskrivning av hur du överför en SUSE VM från lokal till Azure, se [förbereda en virtuell dator SLES eller openSUSE för Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Om du vill överföra en virtuell dator utan avetablering steg i slutet (till exempel för att behålla en befintlig installation av SAP, samt värdnamnet) kontrollerar du följande:

* Kontrollera att OS-disken är monterad med hjälp av UUID och inte enhets-ID. Ändra till UUID bara i /etc/fstab räcker inte för OS-disk. Dessutom Glöm inte att anpassa startprogrammet via YaST eller genom att redigera /boot/grub/menu.lst.
* Om du använder VHDX-formatet för SUSE OS-disken och konvertera det till VHD för överföring till Azure, är det troligt att nätverksenheten ändras från eth0 till eth1. Om du vill undvika problem när du senare starta på Azure, ändra tillbaka till eth0 enligt beskrivningen i [korrigerat eth0 i klonas SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Utöver det som beskrivs i artikeln, rekommenderar vi att du tar bort den här filen:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Du kan också installera Azure Linux-agenten (waagent) för att hjälpa dig att undvika problem, så länge det inte finns flera nätverkskort.

## <a name="deploying-a-suse-vm-on-azure"></a>Distribuera en SUSE virtuell dator på Azure
Du bör skapa nya SUSE virtuella datorer med hjälp av JSON-mallfilerna i den nya Azure Resource Manager-modellen. När mallen JSON-filen har skapats kan distribuera du den virtuella datorn med hjälp av följande CLI-kommando som ett alternativ till PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Läs mer om JSON-mallfilerna [redigera Azure Resource Manager-mallar](../../../resource-group-authoring-templates.md) och [Azure snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).

Mer information om CLI och Azure Resource Manager finns [använder Azure CLI för Mac, Linux och Windows med Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Nyckeln för SAP licens och maskinvara
För den officiella SAP-Azure-certifieringen introducerades en ny mekanism för att beräkna SAP maskinvarunyckeln som används för SAP-licens. SAP-kernel var tvungen att anpassas för att använda den nya algoritmen. Tidigare SAP kernel-versioner för Linux innehöll inte den här koden ändras. Därför kan i vissa situationer (till exempel Azure VM storleksändring), SAP maskinvarunyckel ändras och SAP-licensen har inte längre är giltig. En lösning finns nyare SAP Linux kärnor.  Detaljerad SAP kernel korrigeringsfiler dokumenteras i SAP-kommentar 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf paket / justerade adm
SUSE innehåller ett paket som kallas ”sapconf” som hanterar en uppsättning SAP-specifika inställningar. Mer information om det här paketet har och hur du installerar och använder den finns: [använder sapconf för att förbereda en SUSE Linux Enterprise Server att köra SAP-system](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) och [vad är sapconf eller hur du förbereder en SUSE Linux Enterprise Server för att köra SAP-system? ](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Under tiden kan finns det ett nytt verktyg, som ersätter 'sapconf - justerade adm'. En kan hitta mer information om det här verktyget följande två länkar:

- SLES dokumentation om 'justerade adm-profil sap-hana finns [här](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- Prestandajustering system för SAP-arbetsbelastningar med 'justerade-adm' - hittar [här](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) i kapitlet 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>NFS-resursens i distribuerade SAP-installationer
Om du har en distribuerad installation – till exempel där du vill installera databasen och SAP-programservrar i olika virtuella datorer – kan du dela katalogen /sapmnt via Network File System (NFS). Om du har problem med följande steg när du har skapat NFS-resurs för /sapmnt, kontrollera om ”no_root_squash” har angetts för resursen.

## <a name="logical-volumes"></a>Logiska volymer
Tidigare, om en krävs en stor logisk volym över flera diskar i Azure data (till exempel för SAP-databasen) rekommenderades att använda Raid-hanteringsverktyget MDADM eftersom Linux logiska volymen Manager (LVM) fullständigt inte verifierades ännu i Azure. Information om hur du ställer in Linux RAID på Azure med hjälp av mdadm finns [konfigurera programvarubaserad RAID på Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Under tiden stöds helt i Azure Linux logiska volymhanterare från och med början av maj 2016 och kan användas som ett alternativ till MDADM. Mer information om LVM på Azure läsa:  
[Konfigurera LVM på en virtuell Linux-dator i Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Azure SUSE databasen
Om du har ett problem med åtkomst till standard SUSE Azure-databasen kan använda du ett kommando för att återställa den. Sådana problem kan inträffa om du skapar en privat OS-avbildningen i en Azure-region och sedan kopiera avbildningen till en annan Azure region att distribuera nya virtuella datorer som är baserade på det här privata OS-avbildningen. Kör följande kommando inuti den virtuella datorn:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gör väldigt lätt desktop
Om du vill använda skrivbordet gör väldigt lätt för att installera ett komplett SAP demo-system i en enda virtuell dator – inklusive en SAP-GUI använder webbläsaren och SAP-hanteringskonsolen--denna ledtråd för att installera den på Azure SLES bilder:

   För SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   För SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>SAP-stöd för Oracle på Linux i molnet
Det finns en begränsning med stöd från Oracle på Linux i virtualiserade miljöer. Även om stöd för begränsningen inte är ett ämne som Azure-specifika, är det viktigt att förstå. Oracle stöder inte på SUSE eller Red Hat i ett offentligt moln som Azure SAP. Under tiden Kör Oracle-databas i Azure stöds fullt av SAP på Oracle Linux (Se SAP Observera 1928533). Om andra kombinationer krävs, kontakta Oracle direkt.

