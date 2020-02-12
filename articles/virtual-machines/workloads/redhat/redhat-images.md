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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134396"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Översikt över Red Hat Enterprise Linux avbildningar
I den här artikeln beskrivs tillgängliga Red Hat Enterprise Linux-avbildningar (RHEL) på Azure Marketplace tillsammans med principer kring namngivning och kvarhållning.

Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) . Information om priser finns i [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> RHEL-avbildningar som för närvarande är tillgängliga i Azure Marketplace stöder antingen BYOS-(Your-Your-Subscription-) eller licens modeller enligt principen betala per användning (PAYG). [Azure Hybrid Use-förmånen](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) och dynamisk växling mellan BYOS och PAYG stöds inte. Växling av licens läge kräver omdistribution av den virtuella datorn från motsvarande avbildning.

>[!NOTE]
> För eventuella problem som rör RHEL-avbildningar på Azure Marketplace kan du skicka ett support ärende till Microsoft.

## <a name="viewing-images-available-in-azure"></a>Visa bilder som är tillgängliga i Azure
När du söker efter "Red Hat" i Marketplace eller när du skapar en resurs i Azure Portal användar gränssnitt, ser du bara en delmängd av alla tillgängliga RHEL-avbildningar. Du kan alltid hämta en fullständig uppsättning tillgängliga VM-avbildningar med hjälp av Azure CLI/PowerShell/API.

Om du vill se en fullständig uppsättning Red Hat-avbildningar i Azure kör du följande kommando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Namngivnings konvention
VM-avbildningar i Azure ordnas efter utgivare, erbjudande, SKU och version. Kombinationen av utgivare: erbjudande: SKU: version är avbildningens URN och identifierar den avbildning som ska användas.

`RedHat:RHEL:7-LVM:7.6.2018103108` till exempel refererar till en RHEL 7,6-LVM avbildning skapad den 31 oktober 2018.

Ett exempel på hur du skapar en virtuell RHEL 7,6-dator visas nedan.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"Senaste" moniker
Azure-REST API tillåter användningen av moniker "nyaste" för version i stället för den aktuella versionen. Med "senaste" etablerar du den senaste tillgängliga avbildningen för den aktuella utgivaren, erbjudandet och SKU: n.

`RedHat:RHEL:7-LVM:latest` avser till exempel den senaste LVM-partitionerade avbildningen i RHEL 7-familjen.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> I allmänhet är jämförelsen av versioner att fastställa de senaste reglerna för [CompareTo-metoden](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Denna bild versions jämförelse görs genom att jämföra värdena som ett [versions](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) objekt, inte som en sträng.

## <a name="rhel-6-image-types"></a>RHEL 6-avbildnings typer
För RHEL 6. x-bilder är bild typerna följande:

|Utgivare | Erbjudande | SKU-värde | Version | Detaljer
|----------|-------|-----------|---------|--------
|Redhat | RHEL | Lägre version (t. ex. 6,9) | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 6.9.2018010506) | Alla standard RHEL 6. x-bilder följer denna konvention
|Redhat | RHEL – BYOS | RHEL – raw69 | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 6.9.20181023) | Den här avbildningen är en RHEL 6,9 BYOS-avbildning.
|Redhat | RHEL | RHEL-SAP-APPS | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 6.8.2017053118) | Det här är en avbildning av RHEL 6,8 för SAP-program. Det har rätt att få åtkomst till SAP-programdatabaser och bas RHEL-databaser.
|Redhat | RHEL | RHEL – SAP-HANA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 6.7.2017053121) | Det här är en RHEL 6,7 för SAP HANA-avbildning. Den har rätt att komma åt SAP HANA-databaser och bas RHEL-lagringsplatser.

## <a name="rhel-7-image-types"></a>Bild typer för RHEL 7
För RHEL 7. x-bilder finns det några olika bild typer. I följande tabell visas de olika uppsättningar med avbildningar vi erbjuder. Du kan visa en fullständig lista med AZ CLI-kommandot `az vm image list --publisher redhat --all`.

>[!NOTE]
> Om inget annat anges är alla avbildningar LVM och kommer att ansluta till vanliga RHEL-databaser (dvs. inte EUS, inte E4S). Vi går vidare till att bara publicera LVM-partitionerade bilder men är öppna för feedback på det här beslutet. Information om stöd för utökad uppdatering och uppdaterings tjänster för SAP finns på [sidan Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata).

|Utgivare | Erbjudande | SKU-värde | Version | Detaljer
|----------|-------|------------|---------|--------
|Redhat | RHEL | Lägre version (t. ex. 7,6) | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019102813) | Avbildningar som publicerats före april 2019 kommer att kopplas till standard RHEL-databaser. Avbildningar som publicerats efter 2019 april kommer att kopplas till EUS-databaser (Extended Update Support) för Red Hat för att tillåta versions låsning av en viss del version. Kunder som vill ha vanliga databaser bör använda de bilder som innehåller 7-LVM eller 7-RAW i SKU-värdet (information nedan). RHEL 7,7 och senare är LVM-partitionerade. Alla andra bilder i den här kategorin är RAW-partitionerade.
|Redhat | RHEL | 7-RAW | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019102813) | De här avbildningarna är opartitionerade (dvs. inga logiska volymer har lagts till).
|Redhat | RHEL | 7-RAW-CI | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019072418) | De här avbildningarna är opartitionerade (dvs. inga logiska volymer har lagts till) och kommer att använda Cloud-Init för etablering.
|Redhat | RHEL | 7-LVM | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019062414) | De här avbildningarna är LVM-partitionerade.
|Redhat | RHEL – BYOS | RHEL – {LVM, RAW} | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.7.20190819) | Dessa bilder är RHEL 7 BYOS-avbildningar. De är inte kopplade till några databaser och kommer inte att debiteras för RHEL Premium-avgiften. Om du är intresse rad av RHEL BYOS-avbildningar [begär du åtkomst](https://aka.ms/rhel-byos). SKU-värden slutar med den lägre versionen och anger om avbildningen är RAW eller LVM-partitionerad. Till exempel indikerar ett SKU-värde för RHEL-lvm77 en LVM-partitionad RHEL 7,7-avbildning.
|Redhat | RHEL | RHEL-SAP | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019071300) | De här avbildningarna är RHEL för SAP-avbildningar. De har rätt att komma åt SAP HANA-och program databaser samt RHEL E4S-databaser. Faktureringen omfattar RHEL Premium och SAP Premium ovanpå bas beräknings avgiften.
|Redhat | RHEL | RHEL – SAP-HA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019062320) | De här avbildningarna är RHEL för SAP med bilder med hög tillgänglighet och uppdaterings tjänster. De har rätt att komma åt SAP HANA-och program Arkiv och databaser för hög tillgänglighet samt RHEL E4S-databaser. Faktureringen omfattar RHEL Premium, SAP Premium och HA Premium utöver bas beräknings avgiften.
|Redhat | RHEL | RHEL-HA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.6.2019062019) | Detta är RHEL-avbildningar som också har behörighet att komma åt det hög tillgängliga tillägget. De kommer att debiteras något extra ovanpå RHEL och bas beräknings avgiften på grund av de ytterligare bidragen för HA.
|Redhat | RHEL | RHEL-SAP-APPS | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.3.2017053118) | De här avbildningarna är inaktuella som SAP-program och SAP HANA-databaser har kombinerats till SAP-databaserna. Dessa är RHEL för SAP-program. De har rätt att få åtkomst till SAP-programdatabaser och bas RHEL-databaser.
|Redhat | RHEL | RHEL – SAP-HANA | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 7.3.2018051421) | De här avbildningarna är inaktuella som SAP-program och SAP HANA-databaser har kombinerats till SAP-databaserna. Detta är RHEL for SAP HANA bilder. De har rätt att komma åt SAP HANA-databaser och bas RHEL-lagringsplatser.

## <a name="rhel-8-image-types"></a>RHEL 8-bildtyper
Information om typer av RHEL 8-avbildningar finns nedan.

|Utgivare | Erbjudande | SKU-värde | Version | Detaljer
|----------|-------|------------|---------|--------
|Redhat | RHEL | 8 | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 8.0.20191023) | De här avbildningarna är RHEL 8,0 LVM-partitionerade avbildningar anslutna till Red Hat-standarddatabaser.
|Redhat | RHEL | 8-Gen2 | Sammanfogade värden för den lägre RHEL-versionen och publicerings datumet (t. ex. 8.0.20191024) | De här avbildningarna är Hyper-V generation 2 RHEL 8,0 LVM-partitionerade avbildningar anslutna till Red Hat-standarddatabaser. Mer information om generation 2 virtuella datorer i Azure finns [här](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="rhel-longer-support-add-ons"></a>RHEL har längre stöd för tillägg

### <a name="extended-update-support-eus"></a>Stöd för utökad uppdatering (EUS)
Från och med april 2019 är RHEL-avbildningar tillgängliga som är kopplade till EUS-databaserna (Extended Update Support) som standard. Mer information om RHEL-EUS finns i [Red Hat-dokumentationen](https://access.redhat.com/articles/rhel-eus).

Det går att växla till EUS-databaser och stöds. Anvisningar om hur du växlar den virtuella datorn till EUS och mer information om EUS support livs längd finns [här](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS stöds inte för RHEL-tillägg. Det innebär att om du installerar ett paket som vanligt vis är tillgängligt från RHEL extra-kanalen, kan du inte göra det på EUS. Den Red Hat extra produkt livs cykeln beskrivs [här](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiating-between-regular-and-eus-images"></a>Differentiering mellan vanliga och EUS bilder.
Kunder som vill använda bilder som är kopplade till EUS-databaser bör använda RHEL-avbildningen som innehåller ett lägre versions nummer för RHEL i SKU: n.

Till exempel kan du se följande två tillgängliga RHEL 7,4-avbildningar:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
I det här fallet kommer `RedHat:RHEL:7.6:7.6.2019102813` att kopplas till EUS-databaser som standard (SKU-värdet 7,4) och `RedHat:RHEL:7-LVM:7.6.2019062414` kopplas till icke-EUS-databaser som standard (SKU-värdet 7-LVM).

Om du vill använda vanliga (icke-EUS) databaser distribuerar du med en avbildning som inte innehåller ett lägre versions nummer i SKU: n.

#### <a name="rhel-images-with-eus"></a>RHEL-avbildningar med EUS
Följande tabell gäller för RHEL-avbildningar som är anslutna till EUS-databaser.

>[!NOTE]
> Vid tidpunkten för skrivning har endast RHEL 7,4 och senare lägre versioner stöd för EUS. EUS stöds inte längre för RHEL < = 7,3.
>
> Mer information om RHEL EUS-tillgänglighet hittar du [här](https://access.redhat.com/support/policy/updates/errata).

Lägre version |Exempel på EUS-avbildning              |Status för EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | Avbildningar som publicerats april 2019 och senare blir EUS som standard|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Avbildningar som publicerats 2019 och senare blir EUS som standard |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | Publicerade avbildningar maj 2019 och senare blir EUS som standard  |
RHEL 8,0      |Saknas                            | Ingen EUS är tillgänglig från Red Hat                               |

### <a name="update-services-for-sap-e4s"></a>Uppdatera tjänster för SAP (E4S)
De senaste RHEL för SAP-avbildningarna kommer att anslutas till uppdaterings tjänsterna för SAP Solutions-prenumerationer (E4S). Mer information om E4S finns i Red Hat- [dokumentationen](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions).

#### <a name="rhel-images-with-e4s"></a>RHEL-avbildningar med E4S
Bilder från följande erbjudanden som skapats efter december 2019 kommer att anslutas till E4S-databaser.

* RHEL – SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL för SAP med HA och uppdaterings tjänster)

## <a name="other-available-offers-and-skus"></a>Andra tillgängliga erbjudanden och SKU: er
Den fullständiga listan över tillgängliga erbjudanden och SKU: er kan innehålla ytterligare bilder utöver vad som anges i tabellen ovan, till exempel `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Dessa erbjudanden kan användas för att tillhandahålla support för vissa Marketplace-lösningar, eller så kan de publiceras för för hands versioner och test ändamål. De kan ändras eller tas bort när som helst utan varning. Använd dem inte om inte deras närvaro har dokumenterats offentligt av antingen Microsoft eller Red Hat.

## <a name="publishing-policy"></a>Publicerings princip
Microsoft och Red Hat uppdaterar bilder när nya del versioner släpps, efter behov för att adressera vissa CVEs eller för tillfälliga konfigurations ändringar/uppdateringar. Vi strävar efter att tillhandahålla uppdaterade avbildningar så snart som möjligt inom tre arbets dagar efter en lansering eller tillgänglighet av en CVE-korrigering.

Vi uppdaterar bara den aktuella del versionen i en specifik bild serie. I och med lanseringen av en nyare del version slutar vi att uppdatera den äldre lägre versionen. Till exempel, med versionen av RHEL 7,6, kommer RHEL 7,5-avbildningar inte längre att uppdateras.

>[!NOTE]
> Aktiva virtuella Azure-datorer som tillhandahålls från RHEL-avbildningar med betala per användning är anslutna till Azure-RHUI och kan ta emot uppdateringar och korrigeringar så fort de släpps av Red Hat och replikeras till Azure-RHUI (vanligt vis i mindre än 24 timmar efter den officiella versionen av Red Hat) . De här virtuella datorerna kräver inte en ny publicerad avbildning för att hämta uppdateringar och kunder har fullständig kontroll över när uppdateringen ska initieras.

## <a name="image-retention-policy"></a>Bevarande princip för avbildning
Vår nuvarande princip är att behålla alla tidigare publicerade avbildningar. Vi förbehåller oss rätten att ta bort avbildningar som är kända för att orsaka problem av något slag. Till exempel kan bilder med felaktiga konfigurationer på grund av efterföljande plattforms-eller komponent uppdateringar tas bort. Avbildningar som kan tas bort följer den aktuella Marketplace-principen för att tillhandahålla meddelanden upp till 30 dagar innan avbildningen tas bort.

## <a name="next-steps"></a>Nästa steg
* Visa en fullständig lista över [RHEL-avbildningar i Azure](./redhat-imagelist.md).
* Läs mer om Azure Red Hats uppdaterings infrastruktur [här](https://aka.ms/rhui-update).
* Läs mer om [RHEL BYOS-erbjudandet](./byos.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .
