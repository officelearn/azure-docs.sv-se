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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128876"
---
## <a name="limitations"></a>Begränsningar

- Skalningsuppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.

## <a name="benefits"></a>Fördelar 

Att reservera hela värden ger följande fördelar:

-   Maskinvaruisolering på den fysiska servernivån. Inga andra virtuella datorer kommer att placeras på dina värdar. Dedikerade värdar distribueras i samma datacenter och delar samma nätverk och underliggande lagringsinfrastruktur som andra, icke-isolerade värdar.
-   Kontroll över underhållshändelser som initierats av Azure-plattformen. Även om de flesta underhållshändelser har liten eller ingen inverkan på dina virtuella datorer, finns det vissa känsliga arbetsbelastningar där varje sekund av paus kan ha en inverkan. Med dedikerade värdar kan du anmäla dig till ett underhållsfönster för att minska påverkan på din tjänst.
-   Med Azure hybrid-förmånen kan du ta med egna licenser för Windows och SQL till Azure. Genom att använda hybridfördelarna får du ytterligare fördelar. Mer information finns i [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupper, värdar och virtuella datorer  

![Visa de nya resurserna för dedikerade värdar.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

En **värdgrupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värdgrupp i en region och en tillgänglighetszon och lägger till värdar i den.

En **värd** är en resurs som mappas till en fysisk server i ett Azure-datacenter. Den fysiska servern allokeras när värden skapas. En värd skapas i en värdgrupp. En värd har en SKU som beskriver vilka VM-storlekar som kan skapas. Varje värd kan vara värd för flera virtuella datorer, av olika storlekar, så länge de är från samma storleksserie.

När du skapar en virtuell dator i Azure kan du välja vilken dedikerad värd som ska användas för din virtuella dator. Du har full kontroll över vilka virtuella datorer som placeras på dina värdar.


## <a name="high-availability-considerations"></a>Överväganden om hög tillgänglighet 

För hög tillgänglighet bör du distribuera flera virtuella datorer, spridda över flera värdar (minst 2). Med Azure Dedicated Hosts har du flera alternativ för att etablera din infrastruktur för att forma dina felisoleringsgränser.

### <a name="use-availability-zones-for-fault-isolation"></a>Använd tillgänglighetszoner för felisolering

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. En värdgrupp skapas i en enda tillgänglighetszon. När alla värdar har skapats placeras de inom den zonen. För att uppnå hög tillgänglighet mellan zoner måste du skapa flera värdgrupper (en per zon) och sprida dina värdar därefter.

Om du tilldelar en värdgrupp till en tillgänglighetszon måste alla virtuella datorer som skapats på den värden skapas i samma zon.

### <a name="use-fault-domains-for-fault-isolation"></a>Använd feldomäner för felisolering

En värd kan skapas i en specifik feldomän. Precis som virtuell dator i en skalningsuppsättning eller tillgänglighetsuppsättning placeras värdar i olika feldomäner på olika fysiska rack i datacentret. När du skapar en värdgrupp måste du ange antalet feldomäner. När du skapar värdar i värdgruppen tilldelar du feldomänen för varje värd. De virtuella datorerna kräver ingen feldomäntilldelning.

Feldomäner är inte samma sak som samlokalisering. Att ha samma feldomän för två värdar betyder inte att de är i närheten av varandra.

Feldomäner begränsas till värdgruppen. Du bör inte göra några antaganden om anti-tillhörighet mellan två värdgrupper (om de inte är i olika tillgänglighetszoner).

Virtuella datorer som distribueras till värdar med olika feldomäner har sina underliggande hanterade disktjänster på flera lagringsstämplar för att öka skyddet för felisolering.

### <a name="using-availability-zones-and-fault-domains"></a>Använda tillgänglighetszoner och feldomäner

Du kan använda båda funktionerna tillsammans för att uppnå ännu mer felisolering. I det här fallet anger du antalet tillgänglighetszoner och feldomäner för varje värdgrupp, tilldelar en feldomän till var och en av dina värdar i gruppen och tilldelar en tillgänglighetszon till var och en av dina virtuella datorer

Exempelmallen För Resurshanteraren som finns [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) används zoner och feldomäner för att sprida värdar för maximal återhämtning i en region.

## <a name="maintenance-control"></a>Kontroll av underhåll

Infrastrukturen som stöder dina virtuella datorer kan ibland uppdateras för att förbättra tillförlitlighet, prestanda, säkerhet och för att starta nya funktioner. Azure-plattformen försöker minimera effekten av plattformsunderhåll när det är möjligt, men kunder med *underhållskänsliga* arbetsbelastningar kan inte tolerera ens några sekunder att den virtuella datorn måste frysas eller kopplas från för underhåll.

**Underhållskontroll** ger kunderna en möjlighet att hoppa över regelbundna plattformsuppdateringar som schemalagts på sina dedikerade värdar och sedan tillämpa det vid den tidpunkt de själva väljer inom ett rullande fönster på 35 dagar.

> [!NOTE]
>  Underhållskontrollen är för närvarande i offentlig förhandsversion. Mer information finns i **Kontrollera uppdateringar med underhållskontroll med [CLI](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) eller [PowerShell](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-powershell?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)**.

## <a name="capacity-considerations"></a>Överväganden för kapaciteter

När en dedikerad värd har etablerats tilldelar Azure den till den fysiska servern. Detta garanterar tillgängligheten för kapaciteten när du behöver etablera den virtuella datorn. Azure använder hela kapaciteten i regionen (eller zonen) för att välja en fysisk server för din värd. Det innebär också att kunderna kan förvänta sig att kunna öka sina dedikerade värdavtryck utan att det rör sig om att få på utrymme i klustret.

## <a name="quotas"></a>Kvoter

Det finns en standardkvotgräns på 3000 virtuella processorer för dedikerade värdar per region. Men antalet värdar som du kan distribuera begränsas också av kvoten för den virtuella datorns storleksfamilj som används för värden. En prenumeration **med användningsbaserad** betalning kan till exempel bara ha en kvot på 10 virtuella processorer tillgängliga för Dsv3-storleksserien i regionen östra USA. I det här fallet måste du begära en kvotökning till minst 64 virtuella processorer innan du kan distribuera en dedikerad värd. Välj knappen **Öka begäran** i det övre högra hörnet för att lämna in en begäran om det behövs.

![Skärmbild av sidan användning och kvoter i portalen](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Mer information finns i [VCPU-kvoter för virtuell dator](/azure/virtual-machines/windows/quotas).

Kostnadsfria utvärderings- och MSDN-prenumerationer har ingen kvot för Azure Dedicated Hosts.

## <a name="pricing"></a>Prissättning

Användare debiteras per dedikerad värd, oavsett hur många virtuella datorer som distribueras. I ditt månadsutdrag ser du en ny fakturerbar resurstyp av värdar. De virtuella datorerna på en dedikerad värd kommer fortfarande att visas i ditt uttalande, men kommer att ha ett pris på 0.

Värdpriset anges baserat på VM-familjen, typen (maskinvarustorlek) och region. Ett värdpris är relativt sett till den största vm-storleken som stöds på värden.

Programvarulicensiering, lagring och nätverksanvändning faktureras separat från värden och virtuella datorer. Det finns ingen ändring av dessa fakturerbara objekt.

Mer information finns i [Azure Dedicated Host pricing](https://aka.ms/ADHPricing).

Du kan också spara på kostnader med en [reserverad instans av Azure Dedikerad värdar](../articles/virtual-machines/prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Storlekar och hårdvarugenerationer

En SKU har definierats för en värd och representerar vm-storleksserien och typen. Du kan blanda flera virtuella datorer av olika storlekar i en enda värd så länge de är av samma storleksserie. 

*Typen* är maskinvarugenereringen. Olika maskinvarutyper för samma VM-serie kommer att vara från olika CPU-leverantörer och har olika CPU-generationer och antal kärnor. 

Storlekarna och maskinvarutyperna varierar beroende på region. Läs [värdprissidan](https://aka.ms/ADHPricing) om du vill veta mer.


## <a name="host-life-cycle"></a>Värdens livscykel


Azure övervakar och hanterar hälsotillståndet för dina värdar. Följande tillstånd returneras när du frågar din värd:

| Hälsotillstånd   | Beskrivning       |
|----------|----------------|
| Värd tillgänglig     | Det finns inga kända problem med din värd.   |
| Värd under utredning  | Vi har problem med värden som vi undersöker. Detta är ett övergångstillstånd som krävs för att Azure ska kunna identifiera omfattning och grundorsak för problemet som identifierats. Virtuella datorer som körs på värden kan påverkas. |
| Värd väntande deallocate   | Azure kan inte återställa värden tillbaka till ett felfritt tillstånd och be dig att distribuera om dina virtuella datorer från den här värden. Om `autoReplaceOnFailure` är aktiverat är dina virtuella datorer *tjänstläkt* till felfri maskinvara. Annars kan den virtuella datorn köras på en värd som håller på att misslyckas.|
| Värdavtal utlokaliserade  | Alla virtuella datorer har tagits bort från värden. Du debiteras inte längre för den här värden eftersom maskinvaran togs ur rotation.   |

