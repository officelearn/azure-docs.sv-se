---
title: Red Hat Enterprise Linux-avbildningar i Azure | Microsoft-dokument
description: Lär dig mer om Red Hat Enterprise Linux-avbildningar i Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239864"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Översikt över Red Hat Enterprise Linux-avbildningar

I den här artikeln beskrivs tillgängliga Red Hat Enterprise Linux (RHEL) avbildningar på Azure Marketplace och principer kring deras namngivning och kvarhållning.

Information om Supportpolicyer för Red Hat för alla versioner av RHEL finns i [Red Hat Enterprise Linux livscykel](https://access.redhat.com/support/policy/updates/errata). Information om priser finns i [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> RHEL-avbildningar som för närvarande är tillgängliga i Azure Marketplace stöder antingen byos-prenumerationsmodeller (bring-your-own-subscription) eller licensieringsmodeller för användningsbaserad betalning. [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) och dynamisk växling mellan BYOS och användningsbaserad licensiering stöds inte. Om du vill växla licensieringsläge måste du distribuera om den virtuella datorn från motsvarande avbildning.

>[!NOTE]
> Om du har problem med RHEL-avbildningar i Azure Marketplace kan du lämna in en supportbiljett till Microsoft.

## <a name="view-images-available-in-azure"></a>Visa tillgängliga avbildningar i Azure

När du söker efter "Red Hat" på Azure Marketplace eller när du skapar en resurs i Azure-portalgränssnittet visas bara en delmängd av alla tillgängliga RHEL-avbildningar. Du kan alltid hämta den fullständiga uppsättningen tillgängliga VM-avbildningar med hjälp av Azure CLI, PowerShell och API.

Om du vill se hela uppsättningen tillgängliga Red Hat-avbildningar i Azure kör du följande kommando:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Namnkonvention

VM-avbildningar i Azure är ordnade efter utgivare, erbjudande, SKU och version. Kombinationen av Publisher:Offer:SKU:Version är bilden URN och identifierar unikt den bild som ska användas.

Refererar `RedHat:RHEL:8-LVM:8.1.20200318` till exempel till en RHEL 8.1 LVM-partitionerad avbildning byggd den 18 mars 2020.

Ett exempel på hur du skapar en virtuell RHEL 8.1-dator visas här.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>Den "senaste" moniker

Azure REST API tillåter användning av moniker "senaste" för versionen i stället för den specifika versionen. Med hjälp av "senaste" bestämmelser den senaste tillgängliga bilden för den givna utgivaren, erbjudandet och SKU.

Refererar `RedHat:RHEL:8-LVM:latest` till exempel till den senaste LVM-avbildningen för RHEL 8-familjen.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> I allmänhet följer jämförelsen av versioner för att bestämma den senaste reglerna för [Metoden CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Den här bildversionsjämningen görs genom att jämföra värdena som ett [versionsobjekt,](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) inte som en sträng.

## <a name="rhel-6-image-types"></a>RHEL 6-bildtyper

För RHEL 6.x-bilder visas bildtyperna i följande tabell.

|Utgivare | Erbjudande | SKU-värde | Version | Information
|----------|-------|-----------|---------|--------
|Redhat | RHEL | Delversion (till exempel 6.9) | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 6.9.2018010506) | Alla standardbilder på RHEL 6.x följer denna konvention.
|Redhat | rhel-byos | rhel-rå69 | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 6.9.20181023) | Den här bilden är en RHEL 6.9 BYOS-bild.
|Redhat | RHEL | RHEL-SAP-APPAR | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 6.8.2017053118) | Den här bilden är en RHEL 6.8 för SAP Applications-avbildning. Den har rätt att komma åt SAP Applications-databaser och grundläggande RHEL-databaser.
|Redhat | RHEL | RHEL-SAP-HANA | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 6.7.2017053121) | Den här bilden är en RHEL 6.7 för SAP HANA-avbildning. Den har rätt att komma åt SAP HANA-databaser och bas-RHEL-databaser.

## <a name="rhel-7-image-types"></a>RHEL 7-bildtyper

För RHEL 7.x-bilder finns det några olika bildtyper. Följande tabell visar de olika uppsättningar av bilder vi erbjuder. Om du vill se en fullständig `az vm image list --publisher redhat --all`lista använder du kommandot Azure CLI .

>[!NOTE]
> Om inget annat anges är alla avbildningar LVM-partitionerade och ansluter till vanliga RHEL-databaser. Det vill säga, databaserna är inte Extended Update Support (EUS) och är inte Update Services for SAP (E4S). Framöver går vi över till att publicera endast LVM-partitionerade bilder men är öppna för feedback på detta beslut. Mer information om Extended Update Support and Update Services for SAP finns i [Red Hat Enterprise Linux livscykel](https://access.redhat.com/support/policy/updates/errata).

|Utgivare | Erbjudande | SKU-värde | Version | Information
|----------|-------|------------|---------|--------
|Redhat | RHEL | Delversion (till exempel 7.6) | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019102813) | Bilder som publicerats före april 2019 bifogas standard RHEL-databaser. Bilder som publiceras efter april 2019 bifogas Red Hats EUS-arkiv för att tillåta versionslåsning av en viss delversion. Kunder som vill ha regelbundna databaser bör använda bilderna som innehåller 7-LVM eller 7-RAW i SKU-värdet (detaljer följer). RHEL 7.7 och senare bilder är LVM-partitionerade. Alla andra bilder i den här kategorin är råpartitionerade.
|Redhat | RHEL | 7-RÅ | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019102813) | Dessa avbildningar är råpartitionerade (till exempel har inga logiska volymer lagts till).
|Redhat | RHEL | 7-RAW-CI | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019072418) | Dessa avbildningar är råpartitionerade (till exempel inga logiska volymer har lagts till) och använder cloud-init för etablering.
|Redhat | RHEL | 7-LVM | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019062414) | Dessa avbildningar är LVM-partitionerade.
|Redhat | rhel-byos | rhel-{lvm,rå} | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.7.20190819) | Dessa bilder är RHEL 7 BYOS bilder. De är inte anslutna till några databaser och kommer inte att debitera RHEL premieavgift. Om du är intresserad av RHEL BYOS-avbildningarna [begär du åtkomst](https://aka.ms/rhel-byos). SKU-värden slutar med delversionen och anger om bilden är rå eller LVM-partitionerad. Ett SKU-värde på rhel-lvm77 anger till exempel en LVM-partitionerad RHEL 7.7-bild.
|Redhat | RHEL | RHEL-SAP | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019071300) | Dessa avbildningar är RHEL för SAP-avbildningar. De har rätt att komma åt SAP HANA- och Applications-databaserna samt RHEL E4S-arkiven. Fakturering inkluderar RHEL-premien och SAP-premien ovanpå basberäkningsavgiften.
|Redhat | RHEL | RHEL-SAP-HA | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019062320) | Dessa avbildningar är RHEL för SAP med avbildningar med hög tillgänglighet och uppdateringstjänster. De har rätt att komma åt SAP HANA- och Applications-databaserna och lagringsplatserna med hög tillgänglighet samt RHEL E4S-arkiv. Fakturering inkluderar RHEL-premium, SAP-premium och hög tillgänglighetspremie ovanpå basberäkningsavgiften.
|Redhat | RHEL | RHEL-HA | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.6.2019062019) | Dessa bilder är RHEL-bilder som också har rätt att komma åt tillägget Hög tillgänglighet. De tar ut något extra utöver RHEL och basberäkningsavgift på grund av tilläggspremien hög tillgänglighet.
|Redhat | RHEL | RHEL-SAP-APPAR | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.3.2017053118) | Dessa avbildningar är inaktuella eftersom SAP-programmen och SAP HANA-databaserna har kombinerats till SAP-databaserna. Dessa avbildningar är RHEL för SAP Applications-avbildningar. De har rätt att komma åt SAP Applications-databaser och grundläggande RHEL-databaser.
|Redhat | RHEL | RHEL-SAP-HANA | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 7.3.2018051421) | Dessa avbildningar är inaktuella eftersom SAP-programmen och SAP HANA-databaserna har kombinerats till SAP-databaserna. Dessa avbildningar är RHEL för SAP HANA-avbildningar. De har rätt att komma åt SAP HANA-databaser och bas-RHEL-databaser.

## <a name="rhel-8-image-types"></a>RHEL 8-bildtyper

Information om RHEL 8-bildtyper finns nedan.

|Utgivare | Erbjudande | SKU-värde | Version | Information
|----------|-------|------------|---------|--------
|Redhat | RHEL | 8 | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 8.0.20191023) | Dessa bilder är RHEL 8 LVM-partitionerade bilder som är anslutna till vanliga Red Hat-databaser.
|Redhat | RHEL | 8-gen2 | Sammanfogade värden för RHEL-delversionen och det datum som publicerats (till exempel 8.0.20191024) | Dessa bilder är Hyper-V Generation 2 RHEL 8 LVM-partitionerade bilder anslutna till vanliga Red Hat-databaser. Mer information om virtuella generation 2-datorer i Azure finns i [Support för virtuella generationer 2 på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="rhel-longer-support-add-ons"></a>RHEL längre stödtillägg

### <a name="extended-update-support"></a>Utökat uppdateringsstöd

Från och med april 2019 är RHEL-bilder tillgängliga som är kopplade till EUS-databaserna som standard. Mer information om RHEL EUS finns i [Red Hats dokumentation](https://access.redhat.com/articles/rhel-eus).

Det är möjligt att byta till EUS-arkiv och stöds. Instruktioner om hur du byter vm till EUS och mer information om uttjäntdatum för EUS stöder uttjänt, finns i [RHEL EUS och versionslåsning av virtuella RHEL-datorer](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS stöds inte på RHEL Extras. Det innebär att om du installerar ett paket som vanligtvis är tillgängligt från RHEL Extras-kanalen kan du inte göra det i EUS. Mer information om Red Hat Extras produktlivscykel finns i [Red Hat Enterprise Linux Extras livscykel](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Skilja mellan vanliga bilder och EUS-bilder

Kunder som vill använda bilder som är kopplade till EUS-databaser bör använda RHEL-avbildningen som innehåller ett RHEL-delversionsnummer i SKU.

Du kan till exempel se följande två RHEL 7.4-bilder tillgängliga.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

I det `RedHat:RHEL:7.6:7.6.2019102813` här fallet är kopplad till EUS-databaser som standard. SKU-värdet är 7,4. Och `RedHat:RHEL:7-LVM:7.6.2019062414` är kopplad till databaser som inte är EUS-databaser som standard. SKU-värdet är 7-LVM.

Om du vill använda vanliga (icke-EUS)-databaser använder du en bild som inte innehåller ett mindre versionsnummer i SKU.To use regular (non-EUS) repositories, use an image that doesn't contain a minor version number in the SKU.

#### <a name="rhel-images-with-eus"></a>RHEL-bilder med EUS

Informationen i följande tabell gäller för RHEL-bilder som är anslutna till EUS-databaser.

>[!NOTE]
> I skrivande stund är det bara RHEL 7.4 och senare mindre versioner som har STÖD för EUS. EUS stöds inte längre för RHEL <=7.3.
>
> Mer information om RHEL EUS-tillgänglighet finns i [Red Hat Enterprise Linux livscykel](https://access.redhat.com/support/policy/updates/errata).

Delversion |Exempel på EUS-bild              |EUS-status                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Bilder publicerade april 2019 och senare är EUS som standard.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Bilder publicerade juni 2019 och senare är EUS som standard. |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Bilder som publiceras i maj 2019 och senare är EUS som standard. |
RHEL 8.0      |Ej tillämpligt                            | Det finns inget EUS tillgängligt från Red Hat.                               |

### <a name="update-services-for-sap"></a>Uppdatera tjänster för SAP

De senaste RHEL för SAP-avbildningar ansluts till Update Services for SAP Solutions-prenumerationerna (E4S). Mer information om E4S finns i [Red Hat-dokumentationen](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions).

#### <a name="rhel-images-with-e4s"></a>RHEL-bilder med E4S

Bilder från följande erbjudanden som skapats efter december 2019 är anslutna till E4S-databaser:

* RHEL-SAP (RHEL för SAP)
* RHEL-SAP-HA (RHEL för SAP med hög tillgänglighet och uppdateringstjänster)

## <a name="other-available-offers-and-skus"></a>Andra tillgängliga erbjudanden och SKU:er

Den fullständiga listan över tillgängliga erbjudanden och SKU:er kan innehålla ytterligare bilder utöver vad som anges i föregående tabell. Ett exempel är `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Dessa erbjudanden kan användas för att ge stöd för specifika marketplace-lösningar. Eller så kan de publiceras för förhandsgranskningar och testning. De kan ändras eller tas bort när som helst utan förvarning. Använd dem inte om inte deras närvaro dokumenteras offentligt av antingen Microsoft eller Red Hat.

## <a name="publishing-policy"></a>Publiceringspolicy

Microsoft och Red Hat uppdatera avbildningar som nya delversioner släpps, som krävs för att åtgärda specifika vanliga sårbarheter och exponeringar (CVEs) eller för tillfälliga konfigurationsändringar eller uppdateringar. Vi strävar efter att tillhandahålla uppdaterade bilder så snart som möjligt inom tre arbetsdagar efter en release eller tillgänglighet av en CVE fix.

Vi uppdaterar endast den aktuella mindre versionen i en viss bildfamilj. Med lanseringen av en nyare delversion slutar vi uppdatera den äldre delversionen. Till exempel, med lanseringen av RHEL 7.6, RHEL 7.5 bilder uppdateras inte längre.

>[!NOTE]
> Aktiva Virtuella Azure-datorer som etablerats från RHEL pay-as-you-go-avbildningar är anslutna till Azure RHUI och kan ta emot uppdateringar och korrigeringar så fort de släpps av Red Hat och replikeras till Azure RHUI. Tidpunkten är vanligtvis mindre än 24 timmar efter den officiella utgåvan av Red Hat. Dessa virtuella datorer kräver inte en ny publicerad bild för att få uppdateringarna. Kunderna har full kontroll över när uppdateringen ska initieras.

## <a name="image-retention-policy"></a>Princip för bildlagring

Nuvarande policy är att behålla alla tidigare publicerade bilder. Vi förbehåller oss rätten att ta bort bilder som är kända för att orsaka problem av något slag. Avbildningar med felaktiga konfigurationer på grund av efterföljande plattforms- eller komponentuppdateringar kan till exempel tas bort. Avbildningar som kan tas bort följer den aktuella Azure Marketplace-principen för att tillhandahålla meddelanden upp till 30 dagar innan avbildningen tas bort.

## <a name="next-steps"></a>Nästa steg

* Information om hur du visar hela listan över RHEL-avbildningar i Azure finns [i RHEL-avbildningar (Red Hat Enterprise Linux) som är tillgängliga i Azure](./redhat-imagelist.md).
* Mer information om Azure Red Hat Update Infrastructure finns i [Red Hat Update Infrastructure for on-demand RHEL VMs i Azure](https://aka.ms/rhui-update).
* Mer information om RHEL BYOS-erbjudandet finns i [Red Hat Enterprise Linux bring-your-own-subscription Gold Images i Azure](./byos.md).
* Information om Supportpolicyer för Red Hat för alla versioner av RHEL finns i [Red Hat Enterprise Linux livscykel](https://access.redhat.com/support/policy/updates/errata).
