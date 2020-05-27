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
ms.date: 05/19/2019
ms.author: danis
ms.openlocfilehash: 9e42229b08d7817b64c66c4ab23877c837339475
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827326"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-Init-stöd för virtuella datorer i Azure
Den här artikeln beskriver det stöd som finns för [Cloud-Init](https://cloudinit.readthedocs.io) för att konfigurera en virtuell dator (VM) eller skalnings uppsättningar för virtuella datorer vid etablerings tiden i Azure. Dessa Cloud-Init-konfigurationer körs vid första start när resurserna har etablerats av Azure.  

VM-etablering är den process där Azure ska passera din virtuella dator skapa parameter värden, till exempel värdnamn, användar namn, lösen ord osv., och göra dem tillgängliga för den virtuella datorn när den startas. En etablerings agent kommer att använda dessa värden, konfigurera den virtuella datorn och rapportera tillbaka när åtgärden har slutförts. 

Azure har stöd för två etablerings agenter [Cloud-Init](https://cloudinit.readthedocs.io)och [Azure Linux-agenten (Wala)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Översikt över Cloud-Init
[Cloud-Init](https://cloudinit.readthedocs.io) är en metod som används ofta för att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom Cloud-Init anropas under den första start processen finns det inga ytterligare steg eller nödvändiga agenter för att tillämpa konfigurationen.  Mer information om hur du formaterar `#cloud-config` filer eller andra indata på rätt sätt finns på [webbplatsen för Cloud-Init-dokumentation](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`filer är textfiler kodade i base64.

Cloud-Init fungerar även över distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-Init använder automatiskt det inbyggda paket hanterings verktyget för distribution som du väljer.

Vi arbetar aktivt med våra godkända Linux distribution-partner för att få moln-init-aktiverade avbildningar tillgängliga på Azure Marketplace. De här avbildningarna gör att dina Cloud-Init-distributioner och-konfigurationer fungerar sömlöst med virtuella datorer och skalnings uppsättningar för virtuella datorer. Inlednings vis samarbetar vi med de godkända Linux distribution-partnerna och uppströms för att säkerställa moln-init-funktioner med operativ systemet på Azure, så uppdateras paketen och görs offentligt tillgängliga i distribution-paketets arkiv. 

Det finns två steg för att göra Cloud-Init tillgängligt för de påtecknade Linux distribution-OPERATIVSYSTEMen på Azure, paket support och avbildnings support:
* stöd för Cloud-Init-paket i Azure-dokument som Cloud-Init-paket och senare stöds eller finns i för hands version, så att du kan använda paketen med operativ systemet i en anpassad avbildning.
* dokumenten "image Cloud-Init Ready" om avbildningen redan har kon figurer ATS för att använda Cloud-init.


### <a name="canonical"></a>Canonical
| Utgivare/version| Erbjudande | SKU | Version | avbildnings moln – init Ready | stöd för Cloud-Init-paket i Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanoniskt 20,04 |UbuntuServer |18,04 – LTS |senaste |ja | ja |
|Kanoniskt 18,04 |UbuntuServer |18,04 – LTS |senaste |ja | ja |
|Kanoniskt 16,04|UbuntuServer |16.04-LTS |senaste |ja | ja |
|Kanoniskt 14,04|UbuntuServer |14.04.5-LTS |senaste |ja | ja |

### <a name="rhel"></a>RHEL
| Utgivare/version | Erbjudande | SKU | Version | avbildnings moln – init Ready | stöd för Cloud-Init-paket i Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ja | Ja-support från paket version: *18.2-1. el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | Ja (Observera att det här är en förhands gransknings bild och när alla RHEL 7,7-avbildningar har stöd för Cloud-Init tas detta bort 1 september 2020) | Ja-support från paket version: *18.5 -6. el7*|
|RedHat 7,7 (gen1)|RHEL |7,7 | 7.7.2020051912 | inga avbildnings uppdateringar i flygning, för att slutföra slutet av maj | Ja-support från paket version: *18.5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 77 – Gen2 | 7.7.2020051913 | inga avbildnings uppdateringar i flygning, för att slutföra slutet av maj | Ja-support från paket version: *18.5 -6. el7*|
|RedHat 7,7 (gen1)|RHEL |7-LVM | 7.7.2020051921 | inga avbildnings uppdateringar i flygning, för att slutföra slutet av maj | Ja-support från paket version: *18.5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 7lvm – Gen2 | 7.7.2020051922  | inga avbildnings uppdateringar i flygning, för att slutföra slutet av maj | Ja-support från paket version: *18.5 -6. el7*|
|RedHat 7,7 (gen1) |RHEL – BYOS | RHEL – lvm77 | 7.7.20200416 | inga avbildnings uppdateringar i flygning, för att slutföra slutet av maj  | Ja-support från paket version: *18.5 -6. el7*|
|RedHat 8,1 (gen1) |RHEL |8,1 – CI |8.1.2020042511 | Ja (Observera att det här är en förhands gransknings bild och när alla RHEL 8,1-avbildningar har stöd för Cloud-Init tas detta bort den 1 augusti 2020) | Nej, ETA för fullständig support juni 2020|
|RedHat 8,1 (Gen2) |RHEL |81 – CI-Gen2 |8.1.2020042524 | Ja (Observera att det här är en förhands gransknings bild och när alla RHEL 8,1-avbildningar har stöd för Cloud-Init tas detta bort den 1 augusti 2020) | Nej, ETA för fullständig support juni 2020 |

RedHat: RHEL 7,8-och 8,2-avbildningar (gen1 och Gen2) tillhandahålls med Cloud-init.

### <a name="centos"></a>CentOS

| Utgivare/version | Erbjudande | SKU | Version | avbildnings moln – init Ready | stöd för Cloud-Init-paket i Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Ja (Observera att det här är en förhands gransknings bild och när alla CentOS 7,7-avbildningar har stöd för Cloud-Init tas detta bort 1 september 2020) | Ja-support från paket version: *18.5 -3. el7. CentOS*|

* CentOS 7,7-avbildningar som kommer att bli moln-init-aktiverade uppdateras här i juni 2020 
* CentOS 7,8-avbildningar har tillhandahållits med Cloud-init.


### <a name="oracle"></a>Oracle

| Utgivare/version | Erbjudande | SKU | Version | avbildnings moln – init Ready | stöd för Cloud-Init-paket i Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle – Linux |77 – CI |7.7.01| förhands gransknings bild (Observera att det här är en förhands gransknings bild, och den när alla Oracle 7,7-avbildningar har stöd för Cloud-Init tas detta bort mid 2020. | Nej, i för hands versionen är paketet: *18.5-3.0.1. el7*

### <a name="suse-sles"></a>SuSE SLES
| Utgivare/version | Erbjudande | SKU | Version | avbildnings moln – init Ready | stöd för Cloud-Init-paket i Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|SUSE SLES 15 SP1 |SUSE |SLES-15-SP1-Basic |Cloud-Init-för hands version| Mer information finns i [SUSE Cloud-Init-bloggen](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) | Nej, i för hands versionen. |
|SUSE SLES 15 SP1 |SUSE |SLES-15-SP1-Basic |Gen2 – Cloud-Init – för hands version| Mer information finns i [SUSE Cloud-Init-bloggen](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) | Nej, i för hands versionen. |


### <a name="debian"></a>Debian
Vi arbetar för för hands versions stöd och förväntar dig uppdateringar i juni 2020.

För närvarande har Azure Stack stöd för etablering av moln-init-aktiverade avbildningar.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Vad är skillnaden mellan Cloud-Init och Linux-agenten (WALA)?
WALA är en Azure Platform-Specific-agent som används för att etablera och konfigurera virtuella datorer och hantera [Azure-tillägg](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Vi förbättrar konfigurationen av virtuella datorer så att de använder Cloud-Init i stället för Linux-agenten för att tillåta att befintliga Cloud-Init-kunder använder sina aktuella moln-init-skript eller nya kunder för att dra nytta av de omfattande konfigurations funktionerna för moln initiering. Om du har befintliga investeringar i Cloud-Init-skript för att konfigurera Linux-system finns det **inga ytterligare inställningar som krävs** för att aktivera Cloud-Init-processen. 

Cloud-Init kan inte bearbeta Azure-tillägg, så WALA krävs fortfarande i avbildningen för att bearbeta tillägg, men måste ha sin etablerings kod inaktive rad för de verifierade Linux distributioner-avbildningar som konverteras till att etableras av Cloud-Init, men de har WALA installerade och konfigurationen är korrekt.

Om du inte inkluderar Azure CLI-växeln vid etablerings tiden när du skapar en virtuell dator, `--custom-data` tar Cloud-Init-eller Wala de minsta etablerings parametrarna för virtuella datorer som krävs för att etablera den virtuella datorn och slutföra distributionen med standardvärdena.  Om du refererar till Cloud-Init-konfigurationen med `--custom-data` -växeln kommer det som finns i dina anpassade data att vara tillgängligt för Cloud-Init när den virtuella datorn startas.

Cloud-Init-konfigurationer som tillämpas på virtuella datorer har inga tidsbegränsningar och kommer inte att leda till att en distribution upphör genom timeout. Detta gäller inte för WALA, om du ändrar standardvärdet för WALA för att bearbeta anpassade data, kan det inte överskrida den totala tids gränsen för VM-etablering för 40mins, i så fall kommer den virtuella datorns skapande att Miss förklaras.

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
Tryck på `ctrl-X` för att stänga filen, Skriv `y` för att spara filen och klicka `enter` för att bekräfta fil namnet vid avslut.

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
När den virtuella datorn har etablerats kommer Cloud-Init att köras genom alla moduler och skript som definierats i för `--custom-data` att konfigurera den virtuella datorn.  Om du behöver felsöka fel eller utelämnanden från konfigurationen måste du söka efter modulnamnet ( `disk_setup` eller `runcmd` till exempel) i den Cloud-Init-logg som finns i **/var/log/Cloud-init.log**.

> [!NOTE]
> Alla fel i moduler resulterar inte i ett oåterkalleligt Cloud-Init-övergripande konfigurations fel. `runcmd`Om skriptet till exempel Miss lyckas med modulen, kommer Cloud-Init fortfarande att rapportera etableringen eftersom runcmd-modulen kördes.

Mer information om Cloud-Init-loggning finns i dokumentationen om [Cloud-Init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Nästa steg
För Cloud-Init-exempel på konfigurations ändringar, se följande dokument:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
 
