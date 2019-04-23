---
title: Red Hat Enterprise Linux-avbildningar i Azure | Microsoft Docs
description: Lär dig mer om Red Hat Enterprise Linux-avbildningar i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/18/2019
ms.author: borisb
ms.openlocfilehash: fb3c0e46324a22bdd95bf7d93c28e69c195927e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786048"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux-avbildningar i Azure
Den här artikeln beskrivs tillgängliga Red Hat Enterprise Linux (RHEL)-avbildningar på Azure Marketplace tillsammans med principer avseende deras namngivning och kvarhållning.

Information om principer för Red Hat-support för alla versioner av RHEL kan hittas på den [livscykel för Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) sidan.

>[!Important]
> RHEL-avbildningar för närvarande tillgängligt på Azure marketplace har stöd för Bring-Your-Own-prenumeration (BYOS) eller betala per användning (PAYG) licensiering modeller. Den [Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md) och dynamisk växla mellan BYOS och PAYG stöds inte. Växla licensieringsläge kräver omdistribuera den virtuella datorn från motsvarande bild.

>[!Note]
> Skicka ett supportärende hos Microsoft för eventuella problem som rör RHEL-avbildningar i Azure marketplace-galleriet.

## <a name="images-available-in-the-ui"></a>Bilder som är tillgängliga i Användargränssnittet
När du söker efter ”Red Hat” i Marketplace eller när du skapar en resurs i Azure-portalens användargränssnitt, visas en delmängd av tillgängliga RHEL-avbildningar och relaterade Red Hat-produkter. Du kan alltid hämta den fullständiga uppsättningen tillgängliga VM-avbildningar med Azure CLI/PowerShell/API.

Kör följande kommando om du vill se den fullständiga uppsättningen tillgängliga Red Hat-bilder i Azure

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Namngivningskonventioner
VM-avbildningar i Azure är ordnade efter utgivare, erbjudande, SKU och Version. Kombinationen av utgivare: erbjudandet: avbildningsreferenser är URN-avbildning och som identifierar unikt bilden som ska användas.

Till exempel `RedHat:RHEL:7-RAW:7.6.2018103108` refererar till en RHEL 7.6 raw-partitionerad avbildning bygger på den 31 oktober 2018.

Ett exempel på hur du skapar en virtuell dator 7.6 RHEL visas nedan.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Den ”senaste” monikern
Azure REST API tillåter användning av moniker ”senaste” för versionen i stället för den specifika versionen. Med hjälp av ”senaste” kommer att etablera den senaste avbildningen för den angivna utgivare, erbjudande och SKU.

Till exempel `RedHat:RHEL:7-RAW:latest` refererar till den senaste RHEL 7 family raw-partitionerad avbildningen tillgängliga.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> I allmänhet jämförelsen av versioner för att fastställa senast följer reglerna för den [CompareTo-metoden](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Aktuella namngivningskonvention
Alla publicerade RHEL-avbildningar använder modellen betala per användning och är anslutna till [Red Hat Update Infrastructure (RHUI) i Azure](https://aka.ms/rhui-update). På grund av punkter i RHUI, har en ny namngivningskonvention antagits för RHEL 7 family avbildningar. RHEL 6 family namngivningen har inte förändrats just nu.

Begränsningen är i faktumet att om en icke-selektivt `yum update` körs mot en virtuell dator ansluten till RHUI, RHEL-versionen uppdateras till senast i den aktuella familjen. Mer information finns i [den här länken](https://aka.ms/rhui-update). Detta kan resultera i förvirring när en etablerad RHEL 7.2-avbildning blir RHEL 7.6 efter en uppdatering. Du kan fortfarande etablera från en äldre avbildning som illustreras i exemplen ovan genom att uttryckligen ange versionen som krävs. Om versionen som krävs inte anges när du etablerar en ny avbildning för RHEL 7, kommer sedan den senaste avbildningen att tillhandahållas.

>[!NOTE]
> I RHEL for SAP uppsättning bilder förblir den RHEL-versionen har åtgärdats. Därför innehåller sina namngivningskonvention en viss version i SKU: N.

>[!NOTE]
> RHEL 6 bilduppsättning flyttades inte till de nya namnkonventionerna.

Följande erbjudanden är SKU: er är tillgängliga för allmänt bruk:

Erbjudande| SKU | Partitionering | Etablering | Anteckningar
:----|:----|:-------------|:-------------|:-----
RHEL | 7-RAW | RÅDATA | Linux-agent | RHEL 7 familj av avbildningar
| | 7-LVM | LVM | Linux-agent | RHEL 7 familj av avbildningar
| | 7-RAW-CI | RAW-CI | Cloud-init | RHEL 7 familj av avbildningar
| | 6.7 | RÅDATA | Linux-agent | RHEL 6.7 avbildningar, gamla namnkonventionen
| | 6.8 | RÅDATA | Linux-agent | Samma som ovan för RHEL 6.8
| | 6.9 | RÅDATA | Linux-agent | Samma som ovan för RHEL 6,9
| | 6.10 | RÅDATA | Linux-agent | Samma som ovan för RHEL 6.10
| | 7.2 | RÅDATA | Linux-agent | Samma som ovan för RHEL 7.2
| | 7.3 | RÅDATA | Linux-agent | Samma som ovan för RHEL 7.3
| | 7.4 | RÅDATA | Linux-agent | Samma som ovan för RHEL 7.4
| | 7.5 | RÅDATA | Linux-agent | Samma som ovan för RHEL 7.5
RHEL-SAP | 7.4 | LVM | Linux-agent | RHEL 7.4 för SAP HANA och appar
| | 7.5 | LVM | Linux-agent | RHEL 7.5 för SAP HANA och appar
RHEL-SAP-HANA | 6.7 | RÅDATA | Linux-agent | RHEL 6.7 för SAP HANA
| | 7.2 | LVM | Linux-agent | RHEL 7.2 för SAP HANA
| | 7.3 | LVM | Linux-agent | 7.3 RHEL for SAP HANA
RHEL-SAP-APPS | 6.8 | RÅDATA | Linux-agent | RHEL 6.8 for SAP Business Applications
| | 7.3 | LVM | Linux-agent | RHEL 7.3 for SAP Business Applications

### <a name="old-naming-convention"></a>Gamla namnkonventionen
RHEL 7-familj av avbildningar och avbildningar i RHEL 6-familjen används för specifika versioner i sina SKU: er tills naming convention ändringen som beskrivs ovan.

Du hittar numeriska SKU: er i listan över hela avbildningen. Microsoft och Red Hat skapar nya numeriska SKU: er när en ny mindre version kommer.

### <a name="other-available-offers-and-skus"></a>Andra tillgängliga erbjudanden och SKU: er
En fullständig lista över tillgängliga erbjudanden och SKU: er kan innehålla ytterligare bilder utanför som anges i tabellen ovan, till exempel `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Dessa erbjudanden kan användas för att tillhandahålla stöd för specifika marketplace-lösningar eller de kunde publiceras för förhandsversioner och testning. De kan ändras eller tas bort när som helst utan varning. Använd inte dem om inte deras närvaro offentligt har dokumenterats av Microsoft eller Red Hat.

## <a name="publishing-policy"></a>Publicera princip
Microsoft och Red Hat uppdatera avbildningar när nya delversioner blir tillgängliga som krävs för att åtgärda specifika CVEs eller för konfiguration av tillfällig principändringar/uppdateringar. Vi strävar efter att tillhandahålla uppdaterade bilder så snart som möjligt, inom tre arbetsdagar efter en Lanserad version eller tillgängligheten för en CVE-korrigering.

Vi uppdaterar endast den aktuella mindre versionen i en given bild-familj. Med lanseringen av en nyare delversion stoppa vi uppdaterar den lägre versionen av äldre. Med lanseringen av RHEL 7.6 kommer 7.5 RHEL-avbildningar till exempel inte längre att uppdateras.

>[!NOTE]
> Aktiva Azure-datorer som etablerats från RHEL betala per användning bilder som är anslutna till Azure-RHUI och kan ta emot uppdateringar och korrigeringar när de är från Red Hat och replikeras till Azure-RHUI (vanligtvis i mindre än 24 timmar efter den officiella versionen av Red Hat) . Dessa virtuella datorer kräver inte en ny publicerade avbildning för att hämta uppdateringarna och kunder har fullständig kontroll över när du vill initiera uppdateringen.

## <a name="image-retention-policy"></a>Bild bevarandeprincip
Våra aktuella principen är att behålla alla tidigare publicerade avbildningar. Vi förbehåller oss rätten att ta bort avbildningar som är kända för att orsaka problem av något slag. Till exempel kan avbildningar med felaktiga konfigurationer på grund av efterföljande plattform eller Komponentuppdateringar tas bort. Avbildningar som kan tas bort följer den aktuella Marketplace-principen för att tillhandahålla meddelanden upp till 30 dagar före borttagning av avbildningen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Red Hat Update Infrastructure [här](https://aka.ms/rhui-update).
* Information om principer för Red Hat-support för alla versioner av RHEL kan hittas på den [livscykel för Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) sidan.
