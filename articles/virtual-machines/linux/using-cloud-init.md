---
title: Översikt över molninit-stöd för virtuella Linux-datorer i Azure
description: Översikt över moln-init-funktioner för att konfigurera en virtuell dator vid etableringstillfället i Azure.
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
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465047"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>molninit-stöd för virtuella datorer i Azure
I den här artikeln beskrivs det stöd som finns för [cloud-init](https://cloudinit.readthedocs.io) för att konfigurera en virtuell dator (VM) eller skalningsuppsättningar för virtuella datorer vid etablering av Azure. Dessa cloud-init-konfigurationer körs vid första start när resurserna har etablerats av Azure.  

Vm-etablering är den process där Azure kommer att skicka ner din VM Skapa parametervärden, till exempel värdnamn, användarnamn, lösenord etc., och göra dem tillgängliga för den virtuella datorn när den startar upp. En "etableringsagent" förbrukar dessa värden, konfigurerar den virtuella datorn och rapporterar tillbaka när den är klar. 

Azure stöder två etableringsagenter [cloud-init](https://cloudinit.readthedocs.io)och [Azure Linux Agent (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>översikt över molnet init
[cloud-init](https://cloudinit.readthedocs.io) är en allmänt använd metod för att anpassa en Linux-VM som den startar för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init anropas under den första startprocessen finns det inga ytterligare steg eller nödvändiga agenter för att tillämpa konfigurationen.  Mer information om hur du `#cloud-config` formaterar dina filer eller andra indata på rätt sätt finns [på molninitdokumentationswebbplatsen](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`filer är textfiler som kodas i base64.

cloud-init fungerar också över distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. cloud-init använder automatiskt det inbyggda pakethanteringsverktyget för de distributioner du väljer.

Vi arbetar aktivt med våra godkända Linux-distributioner för att ha moln-init-aktiverade avbildningar tillgängliga på Azure-marknadsplatsen. Dessa avbildningar gör att dina molninitdistributioner och konfigurationer fungerar sömlöst med virtuella datorer och skalningsuppsättningar för virtuella datorer. Inledningsvis samarbetar vi med de godkända Linux-distributionerna och uppströms för att säkerställa moln-init-funktioner med OS på Azure, sedan paketen uppdateras och görs tillgängliga för allmänheten i distributionspaketets databaser. 

Det finns två steg för att göra cloud-init tillgängligt för de godkända Linux-distributionerna på Azure, paketsupport och sedan bildstöd:
* "cloud-init package support on Azure" dokument som cloud-init-paket och framåt stöds eller förhandsgranskas, så att du kan använda dessa paket med operativsystemet i en anpassad avbildning.
* "image cloud-init ready" dokument om avbildningen redan är konfigurerad för att använda cloud-init.


### <a name="canonical"></a>Canonical
| Utgivare / Version| Erbjudande | SKU | Version | bild cloud-init redo | support för cloud-init-paket på Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanoniska 18,04 |UbuntuServer |18.04-LTS |senaste |ja | ja |
|Kanoniska 16,04|UbuntuServer |16.04-LTS |senaste |ja | ja |
|Kanoniska 14,04|UbuntuServer |14.04.5-LTS |senaste |ja | ja |

### <a name="rhel"></a>RHEL
| Utgivare / Version | Erbjudande | SKU | Version | bild cloud-init redo | support för cloud-init-paket på Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ja | Ja - stöd från paketversionen: *18.2-1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | ja (observera att detta är en förhandsgranskningsbild, och när alla RHEL 7.7-bilder stöder cloud-init, kommer detta att tas bort i mitten av 2020, kommer meddelande ges) | ja - stöd från paketversion: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-RÅ | Saknas| nej - bilduppdateringar för att slutföra slutet av april 2020| ja - stöd från paketversion: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-LVM | Saknas| nej - bilduppdateringar för att slutföra slutet av april| ja - stöd från paketversion: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7.7 | Saknas| nej - bilduppdateringar för att slutföra slutet av april | ja - stöd från paketversion: *18.5-3.el7*|
|RedHat 7,7 |rhel-byos | rhel-lvm77 | Saknas|nej - bilduppdateringar för att slutföra slutet av april  | ja - stöd från paketversion: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Utgivare / Version | Erbjudande | SKU | Version | bild cloud-init redo | support för cloud-init-paket på Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |ja (observera att detta är en förhandsgranskningsbild, och när alla CentOS 7.7-bilder stöder cloud-init, kommer detta att tas bort i mitten av 2020, kommer meddelande ges) | ja - stöd från paketversion: *18.5-3.el7.centos*|

* CentOS 7.7 bilder som kommer att cloud-init aktiverat uppdateras här i mars 2020 

### <a name="oracle"></a>Oracle

| Utgivare / Version | Erbjudande | SKU | Version | bild cloud-init redo | support för cloud-init-paket på Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Orakel 7.7 |Oracle-Linux |77-ci |7.7.01| förhandsgranskningsbild (observera att detta är en förhandsgranskningsbild, och det när alla Oracle 7.7-bilder stöder cloud-init, kommer detta att tas bort i mitten av 2020, kommer meddelande ges) | nej, i förhandsversion är paketet: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Vi arbetar för närvarande med att förhandsgranska support, förvänta uppdateringar i februari och mars 2020.

Azure Stack stöder etablering av molninitaktiverade avbildningar.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Vad är skillnaden mellan cloud-init och Linux Agent (WALA)?
WALA är en Azure-plattformsspecifik agent som används för att etablera och konfigurera virtuella datorer och hantera [Azure-tillägg](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Vi förbättrar uppgiften att konfigurera virtuella datorer för att använda cloud-init istället för Linux-agenten för att befintliga moln-init-kunder ska kunna använda sina nuvarande moln-init-skript, eller nya kunder för att dra nytta av de omfattande konfigurationsfunktionerna för moln init. Om du har befintliga investeringar i moln-init-skript för att konfigurera Linux-system, finns det **inga ytterligare inställningar som krävs** för att aktivera cloud-init bearbeta dem. 

cloud-init kan inte bearbeta Azure-tillägg, så WALA krävs fortfarande i avbildningen för att bearbeta tillägg, men måste ha sin etableringskod inaktiverad, för godkända Linux-distributioner avbildningar som konverteras till etablering av cloud-init, kommer de att ha WALA installeras och ställer in den på rätt sätt.

När du skapar en virtuell dator, `--custom-data` om du inte inkluderar Azure CLI-växeln vid etableringstillfället, tar cloud-init eller WALA de minimala vm-etableringsparametrar som krävs för att etablera den virtuella datorn och slutföra distributionen med standardvärdena.  Om du refererar till molninitkonfigurationen med växeln, `--custom-data` kommer det som finns i dina anpassade data att vara tillgängligt för cloud-init när den virtuella datorn startar.

moln-init-konfigurationer som tillämpas på virtuella datorer har inga tidsbegränsningar och kommer inte att orsaka att en distribution misslyckas genom att ta time out. Detta gäller inte för WALA, om du ändrar WALA-standardvärden för att bearbeta anpassade data, kan den inte överskrida den totala tidstillåtna tidstillåtna för virtuella datorer på 40mins, om så är fallet misslyckas den virtuella datorn Skapa.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Distribuera en molninitaktiverad virtuell dator
Distribuera en moln-init aktiverad virtuell dator är lika enkelt som att referera till en cloud-init aktiverad distribution under distributionen.  Linux-distributionsansvariga måste välja att aktivera och integrera cloud-init i sina bas Azure publicerade avbildningar. När du har bekräftat att avbildningen som du vill distribuera är moln-init-aktiverad kan du använda Azure CLI för att distribuera avbildningen. 

Det första steget i distributionen av den här avbildningen är att skapa en resursgrupp med kommandot [az group create.](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Nästa steg är att skapa en fil i det aktuella skalet, med namnet *cloud-init.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell som inte finns på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att **nano** använda nanoredigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Tryck `ctrl-X` för att avsluta `y` filen, skriv `enter` för att spara filen och tryck för att bekräfta filnamnet vid avslutningen.

Det sista steget är att skapa en virtuell dator med kommandot [az vm create.](/cli/azure/vm) 

I följande exempel skapas en virtuell dator med namnet *centos74* och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *centos74:*

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

När den virtuella datorn har skapats visar Azure CLI information som är specifik för distributionen. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.  Det tar lite tid för den virtuella datorn att skapas, paketen att installera och appen startar. Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. Du kan SSH i den virtuella datorn och använda stegen i avsnittet Felsökning för att visa moln-init loggar. 

## <a name="troubleshooting-cloud-init"></a>Felsöka moln-init
När den virtuella datorn har etablerats, kommer cloud-init att `--custom-data` köras genom alla moduler och skript som definierats för att konfigurera den virtuella datorn.  Om du behöver felsöka eventuella fel eller utelämnanden från konfigurationen`disk_setup` `runcmd` måste du söka efter modulnamnet (eller till exempel) i moln-init-loggen - som finns i **/var/log/cloud-init.log**.

> [!NOTE]
> Alla modulfel resulterar inte i ett allvarligt konfigurationsfel i molnet init. Om skriptet `runcmd` till exempel misslyckas kommer cloud-init fortfarande att rapportera etableringen lyckades eftersom runcmd-modulen kördes.

Mer information om cloud-init-loggning finns i dokumentationen för [cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Nästa steg
Exempel på konfigurationsändringar i molnet finns i följande dokument:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
 
