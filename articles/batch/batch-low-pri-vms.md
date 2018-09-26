---
title: Kör Azure Batch-arbetsbelastningar på kostnadseffektiv lågprioriterade virtuella datorer | Microsoft Docs
description: Lär dig hur du etablerar lågprioriterade virtuella datorer för att minska kostnaden för Azure Batch-arbetsbelastningar.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.openlocfilehash: d42cef944c3b971804ef1417a3877bf919784a02
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093011"
---
# <a name="use-low-priority-vms-with-batch"></a>Använda lågprioriterade virtuella datorer med Batch

Azure Batch erbjuder lågprioriterade virtuella datorer (VM) för att minska kostnaden för Batch-arbetsbelastningar. Lågprioriterade virtuella datorer gör nya typer av Batch-arbetsbelastningar som är möjligt genom att aktivera mycket beräkningskraft som ska användas för en mycket låg kostnad.
 
Lågprioriterade virtuella datorer kan du dra nytta av överkapacitet i Azure. När du anger lågprioriterade virtuella datorer i dina pooler, kan Azure Batch använda den här överskott när det är tillgängligt.
 
Nackdelen för att använda lågprioriterade virtuella datorer är att dessa virtuella datorer kanske inte tillgänglig för att allokera eller kan avbrytas när som helst, beroende på tillgänglig kapacitet. Därför lågprioriterade virtuella datorer som är mest lämpliga för vissa typer av arbetsbelastningar. Använda lågprioriterade virtuella datorer för batch- och asynkron bearbetning arbetsbelastningar där tid för slutförande av jobbet är flexibelt och om arbetet är fördelat på flera virtuella datorer.
 
Virtuella datorer med låg prioritet erbjuds till ett betydligt lägre pris jämfört med dedikerade virtuella datorer. Information om prissättning finns i [Batchpriser](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Användningsområden för lågprioriterade virtuella datorer

Angivna egenskaperna för lågprioriterade virtuella datorer, vilka arbetsbelastningar kan och inte kan använda dem? I allmänhet är arbetsbelastningar för händelsebearbetning i batch ett bra alternativ när jobb delas upp i många parallella uppgifter eller det finns många jobb som skalas ut och distribueras över flera virtuella datorer.

-   För att maximera användningen av överkapacitet i Azure, kan lämplig jobb skala ut.

-   Ibland virtuella datorer kanske inte tillgänglig eller frånkopplas, vilket leder till minskad kapacitet för jobb och kan leda till avbrott i aktiviteten och repriser. Jobb måste därför vara flexibel i den tid som de kan vidta för att köra.

-   Jobb med längre uppgifter kan påverka mer om avbruten. Om tidskrävande uppgifter implementera kontrollpunkter för att spara förloppet när de kör, minskas effekten av avbrott. Aktiviteter med kortare körningstider brukar fungerar bäst med lågprioriterade virtuella datorer, eftersom effekten av avbrott är mycket mindre.

-   Långvariga MPI-jobb som använder flera virtuella datorer är inte lämplig för användning av lågprioriterade virtuella datorer, eftersom en allokeringar virtuell dator kan leda till hela projektet behöva köra igen.

Några exempel på batch-bearbetning användningsfall och bäst om du vill använda lågprioriterade virtuella datorer är:

-   **Utveckling och testning**: I synnerhet om lösningar för storskaliga håller på att utvecklas, stora besparingar kan realiseras. Alla typer av testning kan vara till fördel, men storskaliga belastningstest och regression testning är bra använder.

-   **Kompletta kapacitet på begäran**: lågprioriterade virtuella datorer som kan användas för att komplettera regular dedikerade virtuella datorer – när det är tillgängligt, jobb kan skala och därför slutföra snabbare till lägre kostnad, när det är inte tillgänglig, baslinje för dedikerade virtuella datorer fortfarande är tillgänglig .

-   **Körningstiden för flexibla jobben**: om det finns flexibilitet i tid jobben har slutförts, och sedan potentiella fall kapacitet kan tillåtas, men med tillägget av lågprioriterade virtuella datorer jobb ofta köras snabbare och till en lägre kostnad.

Batch-pooler kan konfigureras för att använda lågprioriterade virtuella datorer på flera sätt, beroende på flexibilitet vid körningstid för jobb:

-   Lågprioriterade virtuella datorer kan endast användas i en pool. I det här fallet återställer Batch alla preempted kapacitet när det är tillgängligt. Den här konfigurationen är det billigaste sättet att köra jobb, eftersom endast lågprioriterade virtuella datorer används.

-   Lågprioriterade virtuella datorer kan användas tillsammans med en fast baslinje för dedikerade virtuella datorer. Fast antal dedikerade virtuella datorer garanterar att det finns alltid vissa att jobbet fortskrider.

-   Det kan vara dynamisk blandning av dedikerade och lågprioriterade virtuella datorer, så att de billigare lågprioriterade virtuella datorerna används endast när det är tillgängligt, men de fullständiga prisvärda dedikerade virtuella datorerna är skalas upp vid behov. Den här konfigurationen håller en minimal mängd kapacitet för att hålla jobben fortskrider.

## <a name="batch-support-for-low-priority-vms"></a>Batch-stöd för virtuella datorer med låg prioritet

Azure Batch får du flera funktioner som gör det enkelt att förbruka och dra nytta av lågprioriterade virtuella datorer:

-   Batch-pooler kan innehålla både dedikerade virtuella datorer och virtuella datorer med låg prioritet. Antalet för varje typ av virtuell dator kan anges när en pool skapas eller ändras när som helst för en befintlig pool använder explicit storleksändringen eller använda automatisk skalning. Jobb- och bidrag kan förblir oförändrat oavsett VM-typer i poolen. Du kan också konfigurera en pool för att använda helt lågprioriterade virtuella datorer för att köra jobb billigt som möjligt och skapa dedikerade virtuella datorer om kapaciteten sjunker under en minsta tröskelvärdet, att hålla jobb som körs.

-   Batch-pooler söker automatiskt efter antalet lågprioriterade virtuella datorer. Om virtuella datorer frånkopplas försöker Batch ersätta den förlorade kapaciteten och återgå till målet.

-   När uppgifter avbryts Batch identifierar och ställs i kö igen automatiskt aktiviteter som ska köras igen.

-   Virtuella datorer med låg prioritet har en separat vCPU-kvot som skiljer sig från den för dedikerade virtuella datorer. 
    Kvoten för virtuella datorer med låg prioritet är högre än kvoten för dedikerade virtuella datorer, eftersom lågprioriterade virtuella datorer kostar mindre. Mer information finns i [Batch-tjänsten kvoter och begränsningar](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Lågprioriterade virtuella datorer stöds inte för närvarande för Batch-konton som skapats i [användarprenumerationsläge](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Skapa och uppdatera pooler

En Batch-pool kan innehålla både dedikerade och lågprioriterade virtuella datorer (även kallat compute-noder). Du kan ange antalet beräkningsnoder för både dedikerade och lågprioriterade virtuella datorer. Antalet noder anger hur många virtuella datorer du vill ha i poolen.

Till exempel för att skapa en pool dedikerade med virtuella datorer i Azure cloud Services med ett mål på 5 virtuella datorer och 20 virtuella datorer med låg prioritet:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Om du vill skapa en pool dedikerade med virtuella Azure-datorer (i det här fallet virtuella Linux-datorer) med ett mål på 5 virtuella datorer och 20 virtuella datorer med låg prioritet:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Du kan hämta det aktuella antalet noder för både dedikerade och lågprioriterade virtuella datorer:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Poolnoder har en egenskap som anger om noden är en dedikerad eller låg prioritet virtuell dator:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

När en eller flera noder i poolen frånkopplas, returnerar en liståtgärden noder i poolen fortfarande dessa noder. Aktuellt antal lågprioritetsnoder förblir oförändrat, men dessa noder har sina-tillståndet angett till den **tillfälligt** tillstånd. Batch försöker hitta ersättning av virtuella datorer och, om detta lyckas noderna går igenom **skapa** och sedan **startar** tillstånd innan den blir tillgänglig för körning av aktiviteten, precis som nya noder.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skala en pool som innehåller virtuella datorer med låg prioritet

Som med pooler som endast består av dedikerade virtuella datorer, är det möjligt att skala en pool som innehåller lågprioriterade virtuella datorer genom att anropa metoden storleksändring eller genom att använda automatisk skalning.

Storleksändringen pool tar en andra valfri parameter som uppdaterar värdet för **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Autoskalningsformel pool stöder lågprioriterade virtuella datorer på följande sätt:

-   Du kan hämta eller ange värdet för variabeln tjänstdefinierade **$TargetLowPriorityNodes**.

-   Du kan hämta värdet för variabeln tjänstdefinierade **$CurrentLowPriorityNodes**.

-   Du kan hämta värdet för variabeln tjänstdefinierade **$PreemptedNodeCount**. 
    Den här variabeln returnerar antalet noder i preempted tillstånd och gör att du kan skala upp eller ned antalet dedikerade noder, beroende på antalet allokeringar noder som inte är tillgängliga.

## <a name="jobs-and-tasks"></a>Jobb och aktiviteter

Jobb och uppgifter kräver lite ytterligare konfiguration för lågprioriterade noder. endast stöder är följande:

-   JobManagerTask-egenskapen för ett jobb har en ny egenskap **AllowLowPriorityNode**. 
    Job manager-aktiviteten kan schemaläggas på en dedikerad eller låg prioritet nod när den här egenskapen är true. Om den här egenskapen är false, schemaläggs job manager-aktiviteten till en särskild nod.

-   En [miljövariabeln](batch-compute-node-environment-variables.md) är tillgänglig för en aktivitetsprogrammet så att den kan fastställa om den körs på en nod med låg prioritet eller dedikerade. Miljövariabeln är AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Hantering av avstängning

Virtuella datorer kan ibland avbrytas; När avbrott inträffar kan gör Batch följande:

-   De preempted virtuella datorerna har enheternas status uppdateras till **tillfälligt**.
-   Om uppgifter körs på de virtuella datorerna som frånkopplas nod, är sedan dessa uppgifter placeras i kö och kör igen.
-   Den virtuella datorn är effektivt bort, vilket leder till förlust av data som lagras lokalt på den virtuella datorn.
-   Poolen försöker hela tiden når målantalet noder med låg prioritet. När ersättningskapacitet har hittats noderna behålla deras ID men ominitieras, gå igenom **skapa** och **startar** tillstånd innan de blir tillgängliga för schemaläggning.
-   Avstängningen är tillgängligt som ett mått i Azure-portalen.

## <a name="metrics"></a>Mått

Nya mått är tillgängliga i den [Azure-portalen](https://portal.azure.com) för noder med låg prioritet. De här måtten är:

- Antal noder med låg prioritet
- Antal kärnor med låg prioritet 
- Allokeringar Nodantal

Visa mått i Azure portal:

1. Gå till ditt Batch-konto i portalen och visa inställningarna för ditt Batch-konto.
2. Välj **mått** från den **övervakning** avsnittet.
3. Välj de mått som du vill ha av den **tillgängliga mått** lista.

![Mått för noder med låg prioritet](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
