---
title: Köra arbets belastningar på kostnads effektiva virtuella datorer med låg prioritet – Azure Batch | Microsoft Docs
description: Lär dig hur du etablerar virtuella datorer med låg prioritet för att minska kostnaderna för Azure Batch arbets belastningar.
services: batch
author: mscurrell
manager: gwallace
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 33d448bc95f4cb12f5a06232cbab168a43d522c1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095197"
---
# <a name="use-low-priority-vms-with-batch"></a>Använda lågprioriterade virtuella datorer med Batch

Azure Batch erbjuder virtuella datorer med låg prioritet (VM) för att minska kostnaderna för batch-arbetsbelastningar. Virtuella datorer med låg prioritet gör nya typer av batch-arbetsbelastningar möjliga genom att göra det möjligt att använda en stor mängd beräknings kraft för en mycket låg kostnad.
 
Virtuella datorer med låg prioritet drar nytta av överskotts kapacitet i Azure. När du anger virtuella datorer med låg prioritet i dina pooler kan Azure Batch använda detta överskott, om det är tillgängligt.
 
Kompromissen med att använda virtuella datorer med låg prioritet är att de virtuella datorerna kanske inte är tillgängliga för tilldelning eller kan avbrytas när som helst, beroende på tillgänglig kapacitet. Därför är de flesta virtuella datorer med låg prioritet lämplig för vissa typer av arbets belastningar. Använd virtuella datorer med låg prioritet för batch-och asynkron bearbetning av arbets belastningar där jobbets slut för ande tid är flexibelt och arbetet distribueras över flera virtuella datorer.
 
Virtuella datorer med låg prioritet erbjuds till ett avsevärt minskat pris jämfört med dedikerade virtuella datorer. Pris information finns i [batch-priser](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Användnings fall för virtuella datorer med låg prioritet

Vilka egenskaper för virtuella datorer med låg prioritet, vilka arbets belastningar kan och inte kan använda dem? I allmänhet är batch-arbetsbelastningar en bra anpassning, eftersom jobb delas upp i många parallella uppgifter eller om det finns många jobb som skalas ut och distribueras över flera virtuella datorer.

-   För att maximera användningen av överskotts kapacitet i Azure kan lämpliga jobb skalas ut.

-   Ibland kanske virtuella datorer inte är tillgängliga eller har avbrutits, vilket resulterar i minskad kapacitet för jobb och kan leda till uppgifts avbrott och omkörning. Jobb måste därför vara flexibla i den tid de kan ta att köra.

-   Jobb med längre aktiviteter kan påverkas mer om de avbryts. Om tids krävande uppgifter implementerar kontroll punkter för att spara förlopp när de körs, minskas effekten av avbrott. Uppgifter med kortare körnings tider tenderar att fungera bäst med virtuella datorer med låg prioritet, eftersom avbrottnas effekt är mycket mindre.

-   Långvariga MPI-jobb som använder flera virtuella datorer är inte väl lämpade att använda virtuella datorer med låg prioritet, eftersom en virtuell dator som har varit aktive rad kan leda till hela jobbet som måste köras igen.

Några exempel på användnings områden för batch-bearbetning som lämpar sig väl för att använda virtuella datorer med låg prioritet:

-   **Utveckling och testning**: I synnerhet, om storskaliga lösningar utvecklas, kan betydande besparingar realiseras. Alla typer av testning kan vara till fördel, men storskaliga belastnings test och Regressions testning är bra användnings områden.

-   **Komplett kapacitet på begäran**: Virtuella datorer med låg prioritet kan användas för att komplettera vanliga dedikerade virtuella datorer – när de är tillgängliga kan jobben skalas och därför slutföras snabbare för lägre kostnader. När det inte är tillgängligt förblir bas linjen för dedikerade virtuella datorer tillgängliga.

-   **Flexibel jobb körnings tid**: Om det är flexibelt i tid då jobben måste slutföras, kan potentiella droppar i kapacitet tolereras. men med att lägga till virtuella datorer med låg prioritet körs de ofta snabbare och en lägre kostnad.

Batch-pooler kan konfigureras för att använda virtuella datorer med låg prioritet på några sätt, beroende på flexibiliteten i jobb körnings tiden:

-   Virtuella datorer med låg prioritet kan bara användas i en pool. I det här fallet återställer batch alla kapaciteter som har åsidosatts när de är tillgängliga. Den här konfigurationen är det billigaste sättet att köra jobb eftersom bara virtuella datorer med låg prioritet används.

-   Virtuella datorer med låg prioritet kan användas tillsammans med en fast bas linje för dedikerade virtuella datorer. Det fasta antalet dedikerade virtuella datorer garanterar att det alltid finns en kapacitet för att behålla jobbets förlopp.

-   Det kan finnas en dynamisk blandning av dedikerade och låg prioritet för virtuella datorer, så att de billigare virtuella datorerna endast används när de är tillgängliga, men med full priss ätts de dedikerade virtuella datorerna vid behov. Den här konfigurationen ger en minimal mängd kapacitet som är tillgänglig för att hålla jobben förloppet.

## <a name="batch-support-for-low-priority-vms"></a>Batch-stöd för virtuella datorer med låg prioritet

Azure Batch innehåller flera funktioner som gör det enkelt att använda och dra nytta av virtuella datorer med låg prioritet:

-   Batch-pooler kan innehålla både dedikerade virtuella datorer och virtuella datorer med låg prioritet. Antalet olika typer av virtuella datorer kan anges när en pool skapas, eller ändras när som helst för en befintlig pool, med hjälp av den explicita storleks ändringen eller med automatisk skalning. Det går inte att ändra jobb-och uppgifts sändning, oavsett vilka VM-typer som finns i poolen. Du kan också konfigurera en pool för att fullständigt använda virtuella datorer med låg prioritet för att köra jobb så billigt som möjligt, men att skapa dedikerade virtuella datorer om kapaciteten sjunker under ett minimi tröskelvärde för att fortsätta köra jobb.

-   Batch-pooler söker automatiskt efter mål antalet virtuella datorer med låg prioritet. Om de virtuella datorerna avbryts försöker batchen ersätta den förlorade kapaciteten och återgår till målet.

-   När aktiviteter avbryts identifierar batch och automatiskt köa uppgifter som ska köras igen.

-   Virtuella datorer med låg prioritet har en separat vCPU-kvot som skiljer sig från den för dedikerade virtuella datorer. 
    Kvoten för virtuella datorer med låg prioritet är högre än kvoten för dedikerade virtuella datorer, eftersom kostnader för låg prioritets datorer är lägre. Mer information finns i [batch-tjänstens kvoter och begränsningar](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Virtuella datorer med låg prioritet stöds för närvarande inte för batch-konton som skapats i [användar prenumerations läge](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Skapa och uppdatera pooler

En batch-pool kan innehålla både dedikerade och lågprioriterade virtuella datorer (kallas även Compute-noder). Du kan ange mål antalet för datornoderna för både dedikerade virtuella datorer och låg prioritet. Mål antalet noder anger antalet virtuella datorer som du vill ha i poolen.

Om du till exempel vill skapa en pool med virtuella datorer i Azure Cloud service med målet 5 dedikerade virtuella datorer och 20 låg prioritets datorer:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Så här skapar du en pool med virtuella Azure-datorer (i det här fallet virtuella Linux-datorer) med ett mål på 5 dedikerade virtuella datorer och 20 låg prioritets datorer:

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

Du kan hämta det aktuella antalet noder för både dedikerade och låg prioritets datorer:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Noder i poolen har en egenskap som anger om noden är en dedikerad eller låg prioritets dator:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

När en eller flera noder i en pool blockeras, returnerar en List Nodes-åtgärd på poolen fortfarande noderna. Det aktuella antalet noder med låg prioritet förblir oförändrat, men de noderna har statusen inställd på väntetillstånd. Batch försöker hitta de virtuella datorerna och, om de lyckas, gå igenom **skapandet** och **Starta** sedan tillstånd innan de blir tillgängliga för uppgifts körning, precis som nya noder.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skala en pool som innehåller virtuella datorer med låg prioritet

Precis som med pooler som bara består av dedikerade virtuella datorer, är det möjligt att skala en pool som innehåller låg prioritets datorer genom att anropa metoden för att ändra storlek eller genom att använda autoskalning.

Åtgärden ändra storlek på poolen tar en andra valfri parameter som uppdaterar värdet för **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Autoskalning-formeln för pooler stöder virtuella datorer med låg prioritet enligt följande:

-   Du kan hämta eller ange värdet för den användardefinierade variabeln **$TargetLowPriorityNodes**.

-   Du kan hämta värdet för den användardefinierade variabeln **$CurrentLowPriorityNodes**.

-   Du kan hämta värdet för den användardefinierade variabeln **$PreemptedNodeCount**. 
    Den här variabeln returnerar antalet noder i det äldre läget och gör att du kan skala upp eller ned antalet dedikerade noder, beroende på antalet nekade noder som inte är tillgängliga.

## <a name="jobs-and-tasks"></a>Jobb och uppgifter

Jobb och aktiviteter kräver lite ytterligare konfiguration för noder med låg prioritet. den enda supporten är följande:

-   JobManagerTask-egenskapen för ett jobb har en ny egenskap, **AllowLowPriorityNode**. 
    När den här egenskapen är true kan jobb hanterarens aktivitet schemaläggas antingen på en dedikerad eller låg prioritets nod. Om den här egenskapen är falsk, är jobb Manager-aktiviteten endast schemalagd till en särskild nod.

-   En [miljö variabel](batch-compute-node-environment-variables.md) är tillgänglig för ett aktivitets program så att den kan avgöra om den körs på en låg prioritets-eller dedikerad nod. Miljövariabeln är AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Hantera avstängningen

Virtuella datorer kan ibland vara blockerade. När avstängningen händer gör batch följande:

-   De virtuella datorernas tillstånd har uppdateratstill att blockeras.
-   Om aktiviteter kördes på de virtuella datorerna i den tidigare noden, köas dessa aktiviteter och körs igen.
-   Den virtuella datorn tas bort effektivt, vilket leder till förlust av data som lagras lokalt på den virtuella datorn.
-   Poolen försöker kontinuerligt nå mål antalet tillgängliga noder med låg prioritet. När ersättnings kapaciteten hittas behåller noderna sina ID: n, men de initieras på nytt, så att du kan **skapa** och **Starta** tillstånd innan de är tillgängliga för schemaläggning.
-   Antalet avstängningen är tillgängliga som ett mått i Azure Portal.

## <a name="metrics"></a>Mått

Nya mått är tillgängliga i [Azure Portal](https://portal.azure.com) för noder med låg prioritet. Dessa mått är:

- Antal noder med låg prioritet
- Antal kärnor med låg prioritet 
- Antal misslyckade noder

Visa mått i Azure Portal:

1. Navigera till ditt batch-konto i portalen och visa inställningarna för ditt batch-konto.
2. Välj **mått** från avsnittet **övervakning** .
3. Välj de mått som du vill använda i listan **tillgängliga mått** .

![Mått för noder med låg prioritet](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
