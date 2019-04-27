---
title: Översikt över cloud-init-stöd för Linux-datorer i Azure | Microsoft Docs
description: Översikt över cloud-init-funktioner i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 6dd1dd0ce2395e2b06d80385ffd299835a280526
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614030"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init-stöd för virtuella datorer i Azure
Den här artikeln förklarar stödet finns för [cloud-init](https://cloudinit.readthedocs.io) för att konfigurera en virtuell dator (VM) eller en virtuell dator av skalningsuppsättningar (VMSS) etableringstid i Azure. Skripten cloud-init körs vid den första starten när resurserna har etablerats med Azure.  

## <a name="cloud-init-overview"></a>Översikt över Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init anropas under den ursprungliga startprocessen, finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.  Mer information om hur du formaterar korrekt din `#cloud-config` filer, finns i den [cloud-init dokumentationswebbplats](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` är i base64-kodade textfiler.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

 Vi arbetar aktivt med våra godkända Linux-distribution partner för att få cloud-init aktiverat-avbildningarna i Azure marketplace. Dessa avbildningar kommer att göra dina cloud-init-distributioner och konfigurationer fungerar sömlöst med virtuella datorer och VM Scale Sets (VMSS). I följande tabell beskrivs de aktuella cloud-init aktiverat avbildningar tillgängligheten på Azure-plattformen:

| Utgivare | Erbjudande | SKU | Version | cloud-init är klara |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |senaste |ja | 
|Canonical |UbuntuServer |17.10 |senaste |ja | 
|Canonical |UbuntuServer |16.04-LTS |senaste |ja | 
|Canonical |UbuntuServer |14.04.5-LTS |senaste |ja |
|CoreOS |CoreOS |Stable |senaste |ja |
|OpenLogic |CentOS |7-CI |senaste |förhandsversion |
|Redhat |RHEL |7-RAW-CI |senaste |förhandsversion |

För närvarande stöder inte Azure Stack etableringen av RHEL 7.4 och CentOS 7.4 med cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Vad är skillnaden mellan cloud-init- och Linux-agenten (WALA)?
WALA är en Azure plattformsspecifika agent som används för att etablera och konfigurera virtuella datorer och hantera Azure-tillägg. Förbättrar vi Konfigurera virtuella datorer för att använda cloud-init i stället för Linux-agenten för att tillåta att befintliga cloud-init-kunder att använda sina aktuella cloud-init-skript.  Om du har befintliga investeringar i cloud-init-skript för att konfigurera Linux-system, finns **inga ytterligare inställningar som krävs för** så att de. 

Om du inte använder Azure CLI `--custom-data` växeln på etableringstid, WALA har minimal VM etablering parametrar som krävs för att etablera den virtuella datorn och slutföra distributionen med standardinställningarna.  Om du refererar till cloud-init `--custom-data` växla, det finns i dina anpassade data (individuella inställningar eller fullständigt skript) åsidosätter standardinställningarna WALA. 

WALA konfigurationer av virtuella datorer är begränsad tid att arbeta i den största virtuella datorn etableringstid.  Cloud-init-konfigurationer som tillämpas på virtuella datorer har inte tidsbegränsningar och orsakar inte en distribution misslyckas av tiden går ut. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Distribuera en cloud-init-aktiverad virtuell dator
Distribuera en virtuell dator för cloud-init aktiverat är lika enkelt som refererar till en cloud-init aktiverat distribution under distributionen.  Sköter underhåll själva för Linux-distribution måste du välja att aktivera och integrera cloud-init i sina grundläggande Azure publicerade avbildningar. När du har bekräftat att den avbildning du vill distribuera är cloud-init aktiverat, kan du använda Azure CLI ska distribuera avbildningen. 

Det första steget i att distribuera den här avbildningen är att skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Nästa steg är att skapa en fil i ditt nuvarande gränssnitt med namnet *cloud-init.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att använda den **nano** redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Tryck på `ctrl-X` avsluta filen, skriva `y` att spara filen och klicka på `enter` bekräfta filnamnet vid avslut.

Det sista steget är att skapa en virtuell dator med den [az vm skapa](/cli/azure/vm) kommando. 

I följande exempel skapas en virtuell dator med namnet *centos74* och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

När den virtuella datorn har skapats visar Azure CLI information specifikt för distributionen. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.  Det tar lite tid för den virtuella datorn skapas, paketen installeras och appen startar. Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. Du kan SSH till den virtuella datorn och Använd de steg som beskrivs i avsnittet om felsökning för att visa cloud-init-loggar. 

## <a name="troubleshooting-cloud-init"></a>Felsöka cloud-init
När den virtuella datorn har etablerats, cloud-init körs via alla moduler och skript som definierats i `--custom-data` för att konfigurera den virtuella datorn.  Om du behöver felsöka eventuella fel eller försummelser från konfigurationen måste du söka efter modulnamnet (`disk_setup` eller `runcmd` till exempel) i loggen för cloud-init - finns i **/var/log/cloud-init.log**.

> [!NOTE]
> Inte alla modulfel resulterar i ett allvarligt cloud-init övergripande configuration-fel. Till exempel med hjälp av den `runcmd` modulen, om skriptet misslyckas cloud-init fortfarande rapporterar etableringen är klar eftersom modulen runcmd körs.

Mer information om cloud-init loggning finns i den [cloud-init-dokumentation](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Nästa steg
Cloud-init-exempel på ändringar i konfigurationen, finns i följande dokument:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
