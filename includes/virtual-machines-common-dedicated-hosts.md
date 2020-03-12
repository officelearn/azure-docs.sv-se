---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 2daaf9bbdf90029f0aad4333ab94e2d1d1d3d7ff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128876"
---
## <a name="limitations"></a>Begränsningar

- Skalnings uppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.

## <a name="benefits"></a>Fördelar 

Att reservera hela värden ger följande fördelar:

-   Maskin varu isolering på den fysiska server nivån. Inga andra virtuella datorer kommer att placeras på värdarna. Dedikerade värdar distribueras i samma data Center och delar samma nätverk och underliggande lagrings infrastruktur som andra icke-isolerade värdar.
-   Kontroll över underhålls händelser som initieras av Azure-plattformen. Majoriteten av underhålls händelser har liten inverkan på dina virtuella datorer, men det finns vissa känsliga arbets belastningar där varje sekund paus kan påverka. Med dedikerade värdar kan du välja en underhålls period för att minska påverkan på tjänsten.
-   Med Azure Hybrid-förmånen kan du ta med dina egna licenser för Windows och SQL till Azure. Med hybrid förmånerna får du ytterligare fördelar. Mer information finns i [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupper, värdar och virtuella datorer  

![Vy över de nya resurserna för dedikerade värdar.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

En **värd grupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värd grupp i en region och en tillgänglighets zon och lägger till värdar i den.

En **värd** är en resurs som är mappad till en fysisk server i ett Azure-datacenter. Den fysiska servern allokeras när värden skapas. En värd skapas i en värd grupp. En värd har en SKU som beskriver vilka VM-storlekar som kan skapas. Varje värd kan vara värd för flera virtuella datorer, med olika storlekar, så länge de är från samma storleks serie.

När du skapar en virtuell dator i Azure kan du välja vilken dedikerad värd som ska användas för den virtuella datorn. Du har fullständig kontroll över vilka virtuella datorer som placeras på värdarna.


## <a name="high-availability-considerations"></a>Överväganden för hög tillgänglighet 

För hög tillgänglighet bör du distribuera flera virtuella datorer, sprida över flera värdar (minst 2). Med Azures dedikerade värdar har du flera alternativ för att etablera infrastrukturen för att forma gränser för fel isolering.

### <a name="use-availability-zones-for-fault-isolation"></a>Använd Tillgänglighetszoner för fel isolering

Tillgänglighets zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. En värd grupp skapas i en enda tillgänglighets zon. När den har skapats placeras alla värdar i den zonen. Om du vill uppnå hög tillgänglighet i flera zoner måste du skapa flera värd grupper (en per zon) och sprida värdarna efter behov.

Om du tilldelar en värd grupp till en tillgänglighets zon måste alla virtuella datorer som skapas på värden skapas i samma zon.

### <a name="use-fault-domains-for-fault-isolation"></a>Använd fel domäner för fel isolering

En värd kan skapas i en angiven fel domän. Precis som VM i en skalnings uppsättning eller tillgänglighets uppsättning placeras värdar i olika fel domäner i olika fysiska rack i data centret. När du skapar en värd grupp måste du ange antalet fel domäner. När du skapar värdar i värd gruppen tilldelar du fel domänen för varje värd. De virtuella datorerna kräver ingen tilldelning av fel domäner.

Fel domäner är inte samma som samplacering. Att ha samma fel domän för två värdar innebär inte att de är i närheten av varandra.

Fel domäner är begränsade till värd gruppen. Du bör inte göra några antaganden om anti-tillhörighet mellan två värd grupper (om de inte finns i olika tillgänglighets zoner).

Virtuella datorer som distribueras till värdar med olika fel domäner har sina underliggande Managed disks-tjänster på flera stämplar, för att öka skyddet av fel isolering.

### <a name="using-availability-zones-and-fault-domains"></a>Använda Tillgänglighetszoner-och fel domäner

Du kan använda båda funktionerna tillsammans för att uppnå ännu mer fel isolering. I det här fallet ska du ange tillgänglighets zonen och antalet fel domäner i för varje värd grupp, tilldela en feldomän till var och en av dina värdar i gruppen och tilldela en tillgänglighets zon till var och en av dina virtuella datorer

Exempel mal len Resource Manager som hittas [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) använder zoner och fel domäner för att sprida värdar för maximal återhämtning i en region.

## <a name="maintenance-control"></a>Kontroll av underhåll

Den infrastruktur som stöder dina virtuella datorer kan ibland uppdateras för att förbättra tillförlitligheten, prestandan, säkerheten och för att starta nya funktioner. Azure-plattformen försöker minimera effekten av plattforms underhåll närhelst det är möjligt, men kunder med *underhålls känsliga* arbets belastningar kan inte tolerera några sekunder på att den virtuella datorn måste frysas eller kopplas från för underhåll.

Med **underhålls kontrollen** får kunderna möjlighet att hoppa över regelbundna plattforms uppdateringar som är schemalagda på sina dedikerade värdar, och sedan använda dem vid den tidpunkt som de väljer i ett rullande 35-dagars fönster.

> [!NOTE]
>  Underhålls kontrollen är för närvarande en offentlig för hands version. Mer information finns i **styra uppdateringar med underhålls kontroll med [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) eller [PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)** .

## <a name="capacity-considerations"></a>Överväganden för kapacitet

När en dedikerad värd har allokerats tilldelar Azure den till den fysiska servern. Detta garanterar att kapaciteten är tillgänglig när du behöver etablera den virtuella datorn. Azure använder hela kapaciteten i regionen (eller zonen) för att välja en fysisk server för värden. Det innebär också att kunderna kan vänta på att kunna utöka sina dedikerade värddatorer utan att det är svårt att köra slut på utrymme i klustret.

## <a name="quotas"></a>Kvoter

Det finns en standard kvot gräns på 3000 virtuella processorer för dedikerade värdar, per region. Men antalet värdar som du kan distribuera begränsas också av kvoten för den virtuella datorns storleks familj som används för värden. En prenumeration där **du betalar per** användning kan till exempel bara ha en kvot på 10 virtuella processorer som är tillgängliga för Dsv3-storleks serien i regionen USA, östra. I så fall måste du begära en kvot ökning till minst 64 virtuella processorer innan du kan distribuera en dedikerad värd. Välj knappen **öka** i det övre högra hörnet för att skicka en begäran om det behövs.

![Skärm bild av sidan användning och kvoter i portalen](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Mer information finns i [vCPU kvoter för virtuella datorer](/azure/virtual-machines/windows/quotas).

Den kostnads fria utvärderings versionen och MSDN-prenumerationen har inte kvot för Azure-dedikerade värdar.

## <a name="pricing"></a>Priser

Användare debiteras per dedikerad värd, oavsett hur många virtuella datorer som distribueras. I din månads instruktion visas en ny fakturerbar resurs typ för värdar. De virtuella datorerna på en dedikerad värd visas fortfarande i din instruktion, men kommer att ha priset 0.

Värd priset anges baserat på VM-serien, typ (maskin varu storlek) och region. Ett värd pris är i förhållande till den största VM-storlek som stöds på värden.

Program varu licensiering, lagring och nätverks användning faktureras separat från värden och de virtuella datorerna. Det finns ingen ändring av de fakturerbara artiklarna.

Mer information finns i pris information för [Azure-dedikerad värd](https://aka.ms/ADHPricing).

Du kan också spara pengar med en [reserverad instans av Azure-dedikerade värdar](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Storlekar och maskin varu generationer

En SKU definieras för en värd och den representerar den virtuella datorns storleks serie och typ. Du kan blanda flera virtuella datorer av olika storlekar inom en enda värd så länge de är i samma storleks serie. 

*Typen* är maskin varu generationen. Olika maskin varu typer för samma VM-serie kommer från olika CPU-leverantörer och har olika processor generationer och antalet kärnor. 

Storlekarna och maskin varu typerna varierar beroende på region. Mer information hittar du på [prissättnings sidan](https://aka.ms/ADHPricing) för värden.


## <a name="host-life-cycle"></a>Värd livs cykel


Azure övervakar och hanterar dina värdars hälso status. Följande tillstånd kommer att returneras när du frågar din värd:

| Hälsotillstånd   | Beskrivning       |
|----------|----------------|
| Värd tillgänglig     | Det finns inga kända problem med värden.   |
| Värd under undersökning  | Vi har problem med värden som vi tittar på. Detta är ett över gångs tillstånd som krävs för att Azure ska kunna testa och identifiera omfattningen och rotor saken för det problem som identifierats. Virtuella datorer som körs på värden kan påverkas. |
| Väntande värd tilldelning   | Azure kan inte återställa värden till felfritt tillstånd och be dig att distribuera om de virtuella datorerna från den här värden. Om `autoReplaceOnFailure` är aktive rad, är de virtuella datorerna *som är tjänstekonverterade* till felfritt maskin vara. Annars kanske den virtuella datorn körs på en värd som inte fungerar.|
| Värd friallokerad  | Alla virtuella datorer har tagits bort från värden. Du debiteras inte längre för den här värden eftersom maskin varan togs bort från rotationen.   |

