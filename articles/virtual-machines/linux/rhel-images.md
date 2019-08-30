---
title: Red Hat Enterprise Linux avbildningar i Azure | Microsoft Docs
description: Lär dig mer om Red Hat Enterprise Linux avbildningar i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 8/14/2019
ms.author: borisb
ms.openlocfilehash: e18f1db80b8fcb2c8846ce0c7294e1766cfa1ca3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091385"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux avbildningar i Azure
I den här artikeln beskrivs tillgängliga Red Hat Enterprise Linux-avbildningar (RHEL) på Azure Marketplace tillsammans med principer kring namngivning och kvarhållning.

Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .

>[!Important]
> RHEL-avbildningar som för närvarande är tillgängliga i Azure Marketplace stöder antingen BYOS-(Your-Your-Subscription-) eller licens modeller enligt principen betala per användning (PAYG). [Azure Hybrid Use](../windows/hybrid-use-benefit-licensing.md) -förmånen och dynamisk växling mellan BYOS och PAYG stöds inte. Växling av licens läge kräver omdistribution av den virtuella datorn från motsvarande avbildning.

>[!Note]
> För eventuella problem som rör RHEL-avbildningar i Azure Marketplace-galleriet kan du skicka ett support ärende till Microsoft.

## <a name="images-available-in-the-ui"></a>Tillgängliga bilder i användar gränssnittet
När du söker efter "Red Hat" i Marketplace eller när du skapar en resurs i Azure Portal användar gränssnitt, visas en delmängd av tillgängliga RHEL-avbildningar och relaterade Red Hat-produkter. Du kan alltid hämta en fullständig uppsättning tillgängliga VM-avbildningar med hjälp av Azure CLI/PowerShell/API.

Om du vill se en fullständig uppsättning Red Hat-avbildningar i Azure kör du följande kommando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Namngivnings konvention
VM-avbildningar i Azure ordnas efter utgivare, erbjudande, SKU och version. Kombinationen av utgivare: erbjudande: SKU: version är avbildningens URN och identifierar den avbildning som ska användas.

Till exempel `RedHat:RHEL:7-RAW:7.6.2018103108` hänvisar till en RHEL 7,6-partitionerad avbildning som skapats den 31 oktober 2018.

Ett exempel på hur du skapar en virtuell RHEL 7,6-dator visas nedan.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"Senaste" moniker
Azure REST API tillåter användningen av moniker "senaste" för version i stället för den aktuella versionen. Med "senaste" etablerar du den senaste tillgängliga avbildningen för den aktuella utgivaren, erbjudandet och SKU: n.

Till exempel `RedHat:RHEL:7-RAW:latest` syftar den senaste RHEL 7-seriens RAW-partitionerad avbildning tillgänglig.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> I allmänhet är jämförelsen av versioner att fastställa de senaste reglerna för [CompareTo-metoden](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Nuvarande namngivnings konvention
Alla aktuella publicerade RHEL-avbildningar använder modellen "betala per användning" och är anslutna till [Red Hat Update Infrastructure (RHUI) i Azure](https://aka.ms/rhui-update). En ny namngivnings konvention har antagits för avbildningar av RHEL 7-familjen där disk partitionerings schema (RAW, LVM) anges i SKU i stället för-versionen. RHEL-avbildnings versionen innehåller antingen 7-RAW eller 7-LVM. RHEL 6-familjens namn har inte ändrats för tillfället.

Det finns två typer av RHEL 7 avbildnings-SKU: er i denna namngivnings konvention: SKU: er som visar den lägre versionen och SKU: er som inte är det. Om du vill använda en 7-RAW-eller 7-LVM-SKU kan du ange den lägre version av RHEL som du vill distribuera i versionen. Om du väljer den senaste versionen kommer du att tillhandahålla den senaste del versionen av RHEL.

>[!NOTE]
> I RHEL för SAP-uppsättningar är RHEL-versionen fast. Därför innehåller deras namngivnings konvention en viss version i SKU: n.

>[!NOTE]
> RHEL 6 avbildnings uppsättningar flyttades inte till den nya namngivnings konventionen.

## <a name="extended-update-support-eus"></a>Stöd för utökad uppdatering (EUS)
Från och med april 2019 är RHEL-avbildningar tillgängliga som är kopplade till EUS-databaserna (Extended Update Support) som standard. Mer information om RHEL-EUS finns i [Red Hat-dokumentationen](https://access.redhat.com/articles/rhel-eus).

Anvisningar om hur du växlar den virtuella datorn till EUS och mer information om EUS support livs längd finns [här](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS stöds inte för RHEL-tillägg. Det innebär att om du installerar ett paket som vanligt vis är tillgängligt från RHEL extra-kanalen, kan du inte göra det på EUS. Den Red Hat extra produkt livs cykeln beskrivs [här](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>För kunder som vill använda EUS-avbildningar:
Kunder som vill använda bilder som är kopplade till EUS-databaser bör använda RHEL-avbildningen som innehåller ett lägre versions nummer för RHEL i SKU: n. Dessa bilder kommer att vara opartitionerade (dvs. inte LVM).

Du kan till exempel se följande 2 RHEL 7,4-avbildningar som är tillgängliga:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
I det här fallet `RedHat:RHEL:7.4:7.4.2019041718` kommer som standard att kopplas till EUs-databaser `RedHat:RHEL:7-RAW:7.4.2018010506` och bifogas till icke-EUs-databaser som standard.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>För kunder som inte vill använda EUS-avbildningar:
Om du inte vill använda en avbildning som är ansluten till EUS som standard distribuerar du med en avbildning som inte innehåller ett lägre versions nummer i SKU: n.

#### <a name="rhel-images-with-eus"></a>RHEL-avbildningar med EUS
Följande tabell gäller för RHEL-avbildningar som innehåller en lägre version i SKU: n.

>[!NOTE]
> Vid tidpunkten för skrivning har endast RHEL 7,4 och senare lägre versioner stöd för EUS. EUS stöds inte längre för RHEL < = 7,3.
>
> Mer information om RHEL EUS-tillgänglighet hittar du [här](https://access.redhat.com/support/policy/updates/errata).

Lägre version |Exempel på EUS-avbildning              |Status för EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Avbildningar som publicerats april 2019 och senare blir EUS som standard|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Avbildningar som publicerats 2019 och senare blir EUS som standard |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Publicerade avbildningar maj 2019 och senare blir EUS som standard  |
RHEL 8,0      |Gäller inte                            | Ingen EUS är tillgänglig från Red Hat                               |


## <a name="list-of-rhel-images-available"></a>Lista över tillgängliga RHEL-avbildningar
Följande erbjudanden är SKU: er som är tillgängliga för allmänt bruk:

Erbjudande| SKU | Partitionering | Etableras | Anteckningar
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux-agent | Bilder med RHEL 7-serien. <br> Är inte kopplat till EUS-databaser som standard.
|             | 7-LVM    | LVM    | Linux-agent | Bilder med RHEL 7-serien. <br> Är inte kopplat till EUS-databaser som standard.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Bilder med RHEL 7-serien. <br> Är inte kopplat till EUS-databaser som standard.
|             | 6.7      | RAW    | Linux-agent | RHEL 6,7-bilder, Gammal namngivnings konvention
|             | 6.8      | RAW    | Linux-agent | Samma som ovan för RHEL 6,8
|             | 6.9      | RAW    | Linux-agent | Samma som ovan för RHEL 6,9
|             | 6.10     | RAW    | Linux-agent | Samma som ovan för RHEL 6,10
|             | 7.2      | RAW    | Linux-agent | Samma som ovan för RHEL 7,2
|             | 7.3      | RAW    | Linux-agent | Samma som ovan för RHEL 7,3
|             | 7.4      | RAW    | Linux-agent | Samma som ovan för RHEL 7,4. <br> Bifogad till EUS-databaser som standard från april 2019
|             | 7.5      | RAW    | Linux-agent | Samma som ovan för RHEL 7,5. <br> Bifogad till EUS-databaser som standard från och med juni 2019
|             | 7,6      | RAW    | Linux-agent | Samma som ovan för RHEL 7,6. <br> Bifogad till EUS-databaser som standard från maj 2019
RHEL-SAP      | 7.4      | LVM    | Linux-agent | RHEL 7,4 för SAP HANA-och företags program
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 för SAP HANA-och företags program
RHEL – SAP-HANA | 6.7      | RAW    | Linux-agent | RHEL 6,7 för SAP HANA
|             | 7.2      | LVM    | Linux-agent | RHEL 7,2 för SAP HANA
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 för SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Linux-agent | RHEL 6,8 för SAP Business Applications
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 för SAP Business Applications
RHEL-HA       | 7.4      | RAW    | Linux-agent | RHEL 7,4 med HA-tillägget
|             | 7.5      | RAW    | Linux-agent | RHEL 7,5 med HA-tillägget
|             | 7,6      | RAW    | Linux-agent | RHEL 7,6 med HA-tillägget
RHEL – SAP-HA   | 7.4      | RAW    | Linux-agent | RHEL 7,4 för SAP med HA-tillägget
|             | 7.5      | RAW    | Linux-agent | RHEL 7,5 för SAP med HA-tillägget
|             | 7,6      | RAW    | Linux-agent | RHEL 7,6 för SAP med HA-tillägget

### <a name="old-naming-convention"></a>Gammal namngivnings konvention
RHEL 7-serien med avbildningar och RHEL 6-serien med bilder som använder vissa versioner i SKU: er fram till dess att namngivnings konventionens ändring förklaras ovan.

Du hittar numeriska SKU: er i listan över hela avbildningen. Microsoft och Red Hat kommer att skapa nya numeriska SKU: er när en ny del version hamnar ut.

### <a name="other-available-offers-and-skus"></a>Andra tillgängliga erbjudanden och SKU: er
Den fullständiga listan över tillgängliga erbjudanden och SKU: er kan innehålla ytterligare bilder utöver vad som anges i tabellen ovan, till exempel `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Dessa erbjudanden kan användas för att tillhandahålla support för vissa Marketplace-lösningar, eller så kan de publiceras för för hands versioner och test ändamål. De kan ändras eller tas bort när som helst utan varning. Använd dem inte om inte deras närvaro har dokumenterats offentligt av antingen Microsoft eller Red Hat.

## <a name="publishing-policy"></a>Publicerings princip
Microsoft och Red Hat uppdaterar bilder när nya del versioner släpps, efter behov för att adressera vissa CVEs eller för tillfälliga konfigurations ändringar/uppdateringar. Vi strävar efter att tillhandahålla uppdaterade avbildningar så snart som möjligt inom tre arbets dagar efter en lansering eller tillgänglighet av en CVE-korrigering.

Vi uppdaterar bara den aktuella del versionen i en specifik bild serie. I och med lanseringen av en nyare del version slutar vi att uppdatera den äldre lägre versionen. Till exempel, med versionen av RHEL 7,6, kommer RHEL 7,5-avbildningar inte längre att uppdateras.

>[!NOTE]
> Aktiva virtuella Azure-datorer som tillhandahålls från RHEL-avbildningar med betala per användning är anslutna till Azure-RHUI och kan ta emot uppdateringar och korrigeringar så fort de släpps av Red Hat och replikeras till Azure-RHUI (vanligt vis i mindre än 24 timmar efter den officiella versionen av Red Hat) . De här virtuella datorerna kräver inte en ny publicerad avbildning för att hämta uppdateringar och kunder har fullständig kontroll över när uppdateringen ska initieras.

## <a name="image-retention-policy"></a>Bevarande princip för avbildning
Vår nuvarande princip är att behålla alla tidigare publicerade avbildningar. Vi förbehåller oss rätten att ta bort avbildningar som är kända för att orsaka problem av något slag. Till exempel kan bilder med felaktiga konfigurationer på grund av efterföljande plattforms-eller komponent uppdateringar tas bort. Avbildningar som kan tas bort följer den aktuella Marketplace-principen för att tillhandahålla meddelanden upp till 30 dagar innan avbildningen tas bort.

## <a name="next-steps"></a>Nästa steg
* Läs mer om Azure Red Hats uppdaterings infrastruktur [här](https://aka.ms/rhui-update).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
