---
title: "Översikt över molntjänster init-stöd för Linux virtuella datorer i Azure | Microsoft Docs"
description: "Översikt över molntjänster init-funktioner i Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 3670676032eb71a5339bb1219cb794366b912147
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Använda molntjänster init för att anpassa en Linux VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) för att konfigurera en virtuell dator (VM) eller virtuella datorn anger skala (VMSS) vid etablering tid i Azure. Skripten molnet init körs vid den första starten när resurserna som har etablerats genom Azure.  

## <a name="cloud-init-overview"></a>Översikt över molntjänster initiering
[Molnet init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en Linux VM när den startas för första gången. Du kan använda molnet init för att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom molnet init anropas under den ursprungliga startprocessen, finns det inga ytterligare steg eller nödvändiga agenter att tillämpa konfigurationen.  Mer information om hur du formaterar korrekt din `#cloud-config` filer finns i [molnet init dokumentationsplatsen](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`fiels är textfiler i base64-kodade.

Molnet init fungerar även över distributioner. Exempelvis kan du inte använda **lgh get installera** eller **yum installera** att installera ett paket. I stället kan du definiera en lista över paket som ska installeras. Molnet init används automatiskt det ursprungliga paket hanteringsverktyget för distro som du väljer.

 Vi arbetar aktivt med våra påtecknade Linux distro partners för att molnet init aktiverat bilder som finns i Azure marketplace. Dessa avbildningar gör dina molntjänster init-distributioner och konfigurationer fungerar sömlöst med virtuella datorer och Virtuella skala uppsättningar (VMSS). I följande tabell beskrivs aktuella molnet init aktiverat bilder tillgängligheten på Azure-plattformen:

| Utgivare | Erbjudande | SKU | Version | redo för molnet initiering
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04 LTS |senaste |ja | 
|Canonical |UbuntuServer |14.04.5-LTS |senaste |ja |
|CoreOS |CoreOS |Stable |senaste |ja |
|OpenLogic |CentOS |7 CI |senaste |förhandsgranskning |
|Redhat |RHEL |7 RÅDATA CI |senaste |förhandsgranskning |

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Vad är skillnaden mellan moln init- och Linux-agenten (WALA)?
WALA är en Azure plattformsspecifika agent används för att etablera och konfigurera virtuella datorer och hantera Azure-tillägg. Vi förbättra uppgiften att konfigurera virtuella datorer för att använda molnet init i stället för Linux-agenten för att tillåta befintliga molnet init kunderna att använda sina aktuella molnet init-skript.  Om du har befintliga investeringar i molnet init-skript för att konfigurera Linux-system är **inga ytterligare inställningar krävs** så att de. 

Om du inte tar med kommandoradsväxeln AzureCLI `--custom-data` vid etablering tid WALA tar minimal VM etablering parametrar som krävs för att etablera den virtuella datorn och slutföra distributionen med standardinställningarna.  Om du refererar till molnet initiering `--custom-data` växla, oavsett finns i dina egna data (individuella inställningar eller fullständig skript) åsidosätter standardinställningarna WALA definieras. 

WALA konfigurationer av virtuella datorer är tid att fungera inom den maximala etablering VM-tid.  Molnet init-konfigurationer som tillämpas på virtuella datorer har inte tidsbegränsningar och kommer inte att orsaka en misslyckas av tidsfel vid distributionen. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Distribuera en moln-init aktiverad virtuell dator
Distribuera en virtuell dator i molnet init aktiverad är så enkelt som refererar till en moln-init-aktiverad distributionsplats under distributionen.  Sköter underhåll själva för Linux-distribution måste du välja att aktivera och integrera molnet init i deras grundläggande Azure publicerade bilder. När du har bekräftat bilden som du vill distribuera är moln init-aktiverad, kan du använda AzureCLI ska distribuera avbildningen. 

Det första steget i att distribuera den här avbildningen är att skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Nästa steg är att skapa en fil i din aktuella shell med namnet *moln init.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor cloud-init.txt` att skapa filen och se en lista över tillgängliga redigerare. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  -httpd
```
Tryck på `ctrl-X` avsluta filen, skriva `y` att spara filen och klicka på `enter` bekräfta filnamnet avslutas.

Det sista steget är att skapa en virtuell dator med den [az vm skapa](/cli/azure/vm#az_vm_create) kommando. 

I följande exempel skapas en virtuell dator med namnet *centos74* och skapar SSH-nycklar, om de inte redan finns på standardplatsen nyckel. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  Använd den `--custom-data` parametern för att skicka in din moln-init-konfigurationsfilen. Ange den fullständiga sökvägen till den *moln init.txt* config om du har sparat filen utanför arbetskatalogen finns. I följande exempel skapas en virtuell dator med namnet *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

När den virtuella datorn har skapats, visar Azure CLI information specifik för din distribution. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.  Det tar tid för den virtuella datorn skapas, paket för att installera och starta appen. Det finns bakgrundsaktiviteter för att fortsätta att köras när Azure CLI återgår till Kommandotolken. Du kan SSH till den virtuella datorn och använda de steg som beskrivs i avsnittet om felsökning för att visa molnet init-loggar. 

## <a name="troubleshooting-cloud-init"></a>Felsöka cloud initiering
När den virtuella datorn har etablerats, molnet init körs via alla moduler och skript som definierats i `--custom-data` för att kunna konfigurera den virtuella datorn.  Om du behöver felsöka eventuella fel och utelämnanden från konfigurationen måste du söka efter modulnamnet (`disk_setup` eller `runcmd` till exempel) i molnet init-log - finns i **/var/log/cloud-init.log**.

> [!NOTE]
> Inte alla modulfel resulterar i ett allvarligt molnet initiering övergripande configuration-fel. Till exempel med hjälp av den `runcmd` modulen, om skriptet inte molnet init rapporterar fortfarande etablering lyckades eftersom modulen runcmd utförs.

Mer information om molnet init loggning avser den [moln init-dokumentation](http://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Nästa steg
Molnet initiering exempel av konfigurationsändringar finns i följande dokument:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)