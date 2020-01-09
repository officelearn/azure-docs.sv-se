---
title: Översikt över Cloud-Init-stöd för virtuella Linux-datorer i Azure
description: Översikt över funktioner för Cloud-Init för att konfigurera en virtuell dator vid etablerings tiden i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/11/2019
ms.author: danis
ms.openlocfilehash: 7b3f64d0629ba5d7aaf85b854e1ee8e5a1410f94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458615"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-Init-stöd för virtuella datorer i Azure
Den här artikeln beskriver det stöd som finns för [Cloud-Init](https://cloudinit.readthedocs.io) för att konfigurera en virtuell dator (VM) eller skalnings uppsättningar för virtuella datorer vid etablerings tiden i Azure. Dessa Cloud-Init-skript körs vid första start när resurserna har etablerats av Azure.  

## <a name="cloud-init-overview"></a>Översikt över Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init anropas under den ursprungliga startprocessen, finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.  Mer information om hur du formaterar korrekt din `#cloud-config` filer, finns i den [cloud-init dokumentationswebbplats](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` är i base64-kodade textfiler.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Vi arbetar aktivt med våra godkända Linux-distribution partner för att få cloud-init aktiverat-avbildningarna i Azure marketplace. De här avbildningarna gör att dina Cloud-Init-distributioner och-konfigurationer fungerar sömlöst med virtuella datorer och skalnings uppsättningar för virtuella datorer. I följande tabell beskrivs de aktuella cloud-init aktiverat avbildningar tillgängligheten på Azure-plattformen:

| Utgivare | Erbjudande | SKU | Version | cloud-init är klara |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |senaste |ja | 
|Canonical |UbuntuServer |16.04-LTS |senaste |ja | 
|Canonical |UbuntuServer |14.04.5-LTS |senaste |ja |
|CoreOS |CoreOS |Stable |senaste |ja |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |förhandsversion |
|Oracle 7,7 |Oracle-Linux |77 – CI |7.7.01|förhandsversion |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ja |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |förhandsversion |
    
För närvarande har Azure Stack inte stöd för etablering av RHEL 7. x och CentOS 7. x med Cloud-init.

* För RHEL 7,6, Cloud-Init-paket, är det paket som stöds: *18.2-1. el7_6.2* 
* För RHEL 7,7 (för hands version), Cloud-Init-paket, är för hands versionen: *18.5 -3. el7*
* För CentOS 7,7 (för hands version), Cloud-Init-paket, är för hands versionen: *18.5 -3. el7. CentOS*
* För Oracle 7,7 (för hands version), Cloud-Init-paket, är för hands versions paketet: *18.5-3.0.1. el7*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Vad är skillnaden mellan Cloud-Init och Linux-agenten (WALA)?
WALA är en Azure Platform-Specific-agent som används för att etablera och konfigurera virtuella datorer och hantera Azure-tillägg. Vi förbättrar uppgiften att konfigurera virtuella datorer för att använda Cloud-Init i stället för Linux-agenten för att tillåta att befintliga Cloud-Init-kunder använder sina aktuella moln-init-skript.  Om du har befintliga investeringar i Cloud-Init-skript för att konfigurera Linux-system finns det **inga ytterligare inställningar som krävs** för att aktivera dem. 

Om du inte inkluderar Azure CLI `--custom-data`-växeln vid etablerings tiden tar WALA de minimala etablerings parametrarna för VM som krävs för att etablera den virtuella datorn och slutföra distributionen med standardvärdena.  Om du refererar till Cloud-Init `--custom-data`-växeln kommer det som finns i dina anpassade data (enskilda inställningar eller fullständigt skript) att åsidosätta standardvärdena för WALA. 

WALA-konfigurationer för virtuella datorer är tidsbegränsade att fungera inom den maximala etablerings tiden för VM.  Cloud-Init-konfigurationer som tillämpas på virtuella datorer har inga tidsbegränsningar och kommer inte att leda till att en distribution upphör genom timeout. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Distribuera en moln-init-aktiverad virtuell dator
Att distribuera en virtuell dator som är aktive rad som en moln-init är lika enkelt som att referera till en Cloud-Init-aktiverad distribution under distributionen.  Underhålls funktioner för Linux-distribution måste välja att aktivera och integrera Cloud-Init i de grundläggande Azure-publicerade avbildningarna. När du har bekräftat att den avbildning som du vill distribuera är Cloud-Init aktiverat, kan du använda Azure CLI för att distribuera avbildningen. 

Det första steget när du distribuerar den här avbildningen är att skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Nästa steg är att skapa en fil i det aktuella gränssnittet med namnet *init. txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 om du vill använda **nano** -redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Tryck på `ctrl-X` för att avsluta filen, skriv `y` för att spara filen och tryck på `enter` för att bekräfta fil namnet vid avslutning.

Det sista steget är att skapa en virtuell dator med kommandot [AZ VM Create](/cli/azure/vm) . 

Följande exempel skapar en virtuell dator med namnet *centos74* och skapar SSH-nycklar om de inte redan finns på en standard nyckel plats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

När den virtuella datorn har skapats visar Azure CLI information som är unik för din distribution. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.  Det tar lite tid för den virtuella datorn att skapas, paketen som ska installeras och appen att starta. Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. Du kan använda SSH i den virtuella datorn och använda stegen som beskrivs i avsnittet fel sökning för att Visa Cloud-Init-loggarna. 

## <a name="troubleshooting-cloud-init"></a>Felsöka Cloud-Init
När den virtuella datorn har etablerats kommer Cloud-Init att köras genom alla moduler och skript som definierats i `--custom-data` för att konfigurera den virtuella datorn.  Om du behöver felsöka fel eller utelämnanden från konfigurationen måste du söka efter modulnamnet (`disk_setup` eller `runcmd` till exempel) i den Cloud-Init-logg som finns i **/var/log/Cloud-init.log**.

> [!NOTE]
> Alla fel i moduler resulterar inte i ett oåterkalleligt Cloud-Init-övergripande konfigurations fel. Om skriptet t. ex. använder `runcmd`-modulen kommer Cloud-Init fortfarande att rapportera etableringen lyckades eftersom runcmd-modulen kördes.

Mer information om Cloud-Init-loggning finns i dokumentationen om [Cloud-Init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Nästa steg
För Cloud-Init-exempel på konfigurations ändringar, se följande dokument:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
