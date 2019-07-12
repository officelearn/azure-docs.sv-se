---
title: Red Hat Enterprise Linux-avbildningar i Azure | Microsoft Docs
description: Lär dig mer om Red Hat Enterprise Linux-avbildningar i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: f7ae82b0376489e21b35e4e94dce32805bea69c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708387"
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
Alla publicerade RHEL-avbildningar använder modellen betala per användning och är anslutna till [Red Hat Update Infrastructure (RHUI) i Azure](https://aka.ms/rhui-update). En ny namngivningskonvention har tagits i bruk för RHEL 7 family avbildningar där disken partitioneras system (rådata, LVM) har angetts i SKU i stället för versionen. Versionsnumret för RHEL-avbildningen innehåller antingen 7 RAW eller 7 LVM. RHEL 6 family namngivningen har inte förändrats just nu.

Det blir 2 typer av RHEL 7 avbildning SKU: er i den här namngivningskonventionen: SKU: er som anger den lägre versionen och SKU: er inte stöds. Om du vill användning som en 7-RAW eller 7 LVM-SKU anger du den lägre RHEL-versionen som du vill distribuera i version. Om du väljer den ”senaste” versionen, kommer du att etableras den senaste mindre versionen av RHEL.

>[!NOTE]
> I RHEL for SAP uppsättning bilder förblir den RHEL-versionen har åtgärdats. Därför innehåller sina namngivningskonvention en viss version i SKU: N.

>[!NOTE]
> RHEL 6 bilduppsättning flyttades inte till de nya namnkonventionerna.

## <a name="extended-update-support-eus"></a>Stöd för utökade uppdateringar (EUS)
Eftersom av April 2019 RHEL-avbildningar som är tillgängliga som är kopplade till utökade uppdatering Support (EUS)-databaser som standard. Mer information om RHEL EUS är tillgängliga i [Red Hats dokumentation](https://access.redhat.com/articles/rhel-eus).

Instruktioner för hur du växlar den virtuella datorn till EUS och mer information om EUS support slutet på sin livscykel datum finns [här](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS stöds inte på RHEL-tillägg. Detta innebär att om du installerar ett paket som är vanligtvis tillgängligt från RHEL-tillägg-kanal är det inte kommer kunna göra det på EUS. Red Hat tillägg produktlivscykelns beskrivs [här](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>För kunder som vill använda EUS avbildningar:
Kunder som vill använda avbildningar som är kopplade till EUS databaser bör använda RHEL-avbildningen som innehåller en lägre RHEL-versionsnumret i SKU. Dessa avbildningar kommer att vara raw-partitionerad (dvs. inte LVM).

Du kan till exempel se följande 2 RHEL 7.4 avbildningar tillgängliga:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
I det här fallet `RedHat:RHEL:7.4:7.4.2019041718` kommer att kopplas till EUS databaser som standard och `RedHat:RHEL:7-RAW:7.4.2018010506` kommer att kopplas till icke-EUS databaser som standard.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>För kunder som inte vill använda EUS avbildningar:
Om du inte vill använda en avbildning som är ansluten till EUS som standard, kan du distribuera med hjälp av en avbildning som inte innehåller en lägre versionsnumret i SKU.

#### <a name="rhel-images-with-eus"></a>RHEL-avbildningar med EUS
Tabellen nedan gäller för RHEL-avbildningar som innehåller en delversion i SKU.

>[!NOTE]
> Vid tidpunkten för skrivning har endast RHEL 7.4 och senare delversioner du EUS stöd. EUS stöds inte längre för RHEL < = 7.3.

Podverze |EUS bild-exempel              |EUS status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Avbildningar publicerade April 2019 och senare kommer att EUS som standard|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Avbildningar publicerade juni 2019 och senare kommer att EUS som standard |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Avbildningar publicerade maj 2019 och senare kommer att EUS som standard  |
RHEL 8.0      |Gäller inte                            | Inga EUS avbildningar för närvarande tillgänglig för tillfället                 |


## <a name="list-of-rhel-images-available"></a>Lista över RHEL-avbildningar som är tillgängliga
Följande erbjudanden är SKU: er är tillgängliga för allmänt bruk:

Erbjudande| SKU | Partitionering | Etablering | Anteckningar
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux-agent | RHEL 7 familj av bilder. <br> Inte kopplat till EUS lagringsplatser som standard.
|             | 7-LVM    | LVM    | Linux-agent | RHEL 7 familj av bilder. <br> Inte kopplat till EUS lagringsplatser som standard.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | RHEL 7 familj av bilder. <br> Inte kopplat till EUS lagringsplatser som standard.
|             | 6.7      | RAW    | Linux-agent | RHEL 6.7 avbildningar, gamla namnkonventionen
|             | 6.8      | RAW    | Linux-agent | Samma som ovan för RHEL 6.8
|             | 6.9      | RAW    | Linux-agent | Samma som ovan för RHEL 6,9
|             | 6.10     | RAW    | Linux-agent | Samma som ovan för RHEL 6.10
|             | 7.2      | RAW    | Linux-agent | Samma som ovan för RHEL 7.2
|             | 7.3      | RAW    | Linux-agent | Samma som ovan för RHEL 7.3
|             | 7.4      | RAW    | Linux-agent | Samma som ovan för RHEL 7.4. <br> Ansluten till EUS lagringsplatser som standard från och med April 2019
|             | 7.5      | RAW    | Linux-agent | Samma som ovan för RHEL 7.5. <br> Ansluten till EUS lagringsplatser som standard från och med juni 2019
|             | 7.6      | RAW    | Linux-agent | Samma som ovan för RHEL 7.6. <br> Ansluten till EUS lagringsplatser som standard från och med maj 2019
RHEL-SAP      | 7.4      | LVM    | Linux-agent | RHEL 7.4 för SAP HANA och appar
|             | 7.5      | LVM    | Linux-agent | RHEL 7.5 för SAP HANA och appar
RHEL-SAP-HANA | 6.7      | RAW    | Linux-agent | RHEL 6.7 för SAP HANA
|             | 7.2      | LVM    | Linux-agent | RHEL 7.2 för SAP HANA
|             | 7.3      | LVM    | Linux-agent | 7\.3 RHEL for SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Linux-agent | RHEL 6.8 for SAP Business Applications
|             | 7.3      | LVM    | Linux-agent | RHEL 7.3 for SAP Business Applications

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
