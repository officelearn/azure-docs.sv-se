---
title: Kör arbetsbelastningar på kostnadseffektiva virtuella datorer med låg prioritet – Azure Batch | Microsoft-dokument
description: Lär dig hur du etablerar virtuella datorer med låg prioritet för att minska kostnaden för Azure Batch-arbetsbelastningar.
services: batch
author: mscurrell
manager: evansma
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9f4b9ed9254eaf950311dd27d5716c4681707614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053915"
---
# <a name="use-low-priority-vms-with-batch"></a>Använda lågprioriterade virtuella datorer med Batch

Azure Batch erbjuder virtuella datorer med låg prioritet för att minska kostnaden för batcharbetsbelastningar. Virtuella datorer med låg prioritet gör det möjligt för nya typer av batcharbetsbelastningar genom att aktivera en stor mängd beräkningskraft som ska användas till en mycket låg kostnad.
 
Virtuella datorer med låg prioritet utnyttjar överskottskapacitet i Azure. När du anger virtuella datorer med låg prioritet i dina pooler kan Azure Batch använda det här överskottet när det är tillgängligt.
 
Avvägningen för att använda virtuella datorer med låg prioritet är att dessa virtuella datorer kanske inte är tillgängliga för att tilldelas eller kan föregripas när som helst, beroende på tillgänglig kapacitet. Därför är virtuella datorer med låg prioritet mest lämpade för vissa typer av arbetsbelastningar. Använd virtuella datorer med låg prioritet för batch- och asynkrona bearbetningsarbetsbelastningar där jobbets slutförandetid är flexibel och arbetet fördelas över många virtuella datorer.
 
Virtuella datorer med låg prioritet erbjuds till ett betydligt lägre pris jämfört med dedikerade virtuella datorer. Prisinformation finns i [Batch-priser](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Spot-virtuella datorer](https://azure.microsoft.com/pricing/spot/) är nu tillgängliga för [virtuella datorer med en instans](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) och [vm-skalningsuppsättningar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot). Spot virtuella datorer är en utveckling av virtuella datorer med låg prioritet, men skiljer sig åt i att prissättningen kan variera och ett valfritt högsta pris kan ställas in vid tilldelning av spot-virtuella datorer.
>
> Azure Batch-pooler börjar stödja spot-virtuella datorer inom några månader efter att de är allmänt tillgängliga, med nya versioner av [batch-API:erna och verktygen](https://docs.microsoft.com/azure/batch/batch-apis-tools). När spot-vm-stöd är tillgängligt kommer virtuella datorer med låg prioritet att vara inaktuella – de kommer att fortsätta att stödjas med hjälp av aktuella API:er och verktygsversioner i minst 12 månader, för att ge tillräckligt med tid för migrering till Spot-virtuella datorer. 
>
> Spot-virtuella datorer stöds inte för [cloud service-konfigurationspooler.](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) Om du vill använda Spot-virtuella datorer måste molntjänstpooler migreras till konfigurationspooler för [virtuella](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) datorer.


## <a name="use-cases-for-low-priority-vms"></a>Användningsfall för virtuella datorer med låg prioritet

Med tanke på egenskaperna hos virtuella datorer med låg prioritet, vilka arbetsbelastningar kan och inte kan använda dem? I allmänhet är batchbearbetningsarbetsbelastningar bra, eftersom jobb delas upp i många parallella aktiviteter eller om det finns många jobb som skalas ut och distribueras över många virtuella datorer.

-   För att maximera användningen av överskottskapacitet i Azure kan lämpliga jobb skala ut.

-   Ibland kanske virtuella datorer inte är tillgängliga eller föregriper, vilket resulterar i minskad kapacitet för jobb och kan leda till avbrott och repriser av aktiviteten. Jobben måste därför vara flexibla under den tid de kan ta att köra.

-   Jobb med längre aktiviteter kan påverkas mer om de avbryts. Om tidskrävande uppgifter implementerar kontrollpunkter för att spara förloppet när de körs, minskas effekten av avbrott. Uppgifter med kortare körningstider tenderar att fungera bäst med virtuella datorer med låg prioritet, eftersom effekten av avbrott är mycket mindre.

-   Långvariga MPI-jobb som använder flera virtuella datorer är inte väl lämpade att använda virtuella datorer med låg prioritet, eftersom en förbestämt virtuell dator kan leda till att hela jobbet måste köras igen.

Några exempel på filbearbetningsärenden som är väl lämpade för att använda virtuella datorer med låg prioritet är:

-   **Utveckling och testning**: I synnerhet om storskaliga lösningar utvecklas kan betydande besparingar realiseras. Alla typer av tester kan dra nytta, men storskaliga belastningstester och regressionstestning är bra användningsområden.

-   **Komplettera kapacitet på begäran**: Lågprioriterade virtuella datorer kan användas för att komplettera vanliga dedikerade virtuella datorer - när de är tillgängliga kan jobben skalas och därför slutföras snabbare till lägre kostnad. När den inte är tillgänglig förblir baslinjen för dedikerade virtuella datorer tillgängliga.

-   **Flexibel tid för utförande av jobb**: Om det finns flexibilitet i den tid jobb måste slutföra, kan potentiella kapacitetsfall tolereras; Men med tillägg av lågprioriterade virtuella datorer jobb körs ofta snabbare och till en lägre kostnad.

Batchpooler kan konfigureras för att använda virtuella datorer med låg prioritet på några olika sätt, beroende på flexibiliteten i jobbkörningstiden:

-   Virtuella datorer med låg prioritet kan endast användas i en pool. I det här fallet återställer Batch all föregripen kapacitet när den är tillgänglig. Den här konfigurationen är det billigaste sättet att köra jobb, eftersom endast virtuella datorer med låg prioritet används.

-   Virtuella datorer med låg prioritet kan användas tillsammans med en fast baslinje för dedikerade virtuella datorer. Det fasta antalet dedikerade virtuella datorer säkerställer att det alltid finns en viss kapacitet att hålla ett jobb framåt.

-   Det kan finnas en dynamisk blandning av dedikerade och lågprioriterade virtuella datorer, så att billigare virtuella datorer med låg prioritet endast används när de är tillgängliga, men dedicerade virtuella datorer med full pris skalas upp när det behövs. Den här konfigurationen håller en minimal kapacitet tillgänglig för att hålla jobben framåt.

## <a name="batch-support-for-low-priority-vms"></a>Batchstöd för virtuella datorer med låg prioritet

Azure Batch innehåller flera funktioner som gör det enkelt att använda och dra nytta av virtuella datorer med låg prioritet:

-   Batchpooler kan innehålla både dedikerade virtuella datorer och virtuella datorer med låg prioritet. Antalet för varje typ av virtuell dator kan anges när en pool skapas eller ändras när som helst för en befintlig pool, med hjälp av explicit storleksanering eller automatisk skala. Jobb- och uppgiftsinlämning kan vara oförändrade, oavsett vm-typerna i poolen. Du kan också konfigurera en pool för att helt använda virtuella datorer med låg prioritet för att köra jobb så billigt som möjligt, men snurra upp dedikerade virtuella datorer om kapaciteten sjunker under ett lägsta tröskelvärde för att hålla jobben igång.

-   Batchpooler söker automatiskt målantalet virtuella datorer med låg prioritet. Om virtuella datorer är föregripna försöker batchen att ersätta den förlorade kapaciteten och återgå till målet.

-   När aktiviteter avbryts identifierar batch och requeues automatiskt aktiviteter för att köras igen.

-   Virtuella datorer med låg prioritet har en separat vCPU-kvot som skiljer sig från den för dedikerade virtuella datorer. 
    Kvoten för virtuella datorer med låg prioritet är högre än kvoten för dedikerade virtuella datorer, eftersom virtuella datorer med låg prioritet kostar mindre. Mer information finns i [Batch-tjänstkvoter och begränsningar](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Virtuella datorer med låg prioritet stöds för närvarande inte för batchkonton som skapats i [användarprenumerationsläge](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Skapa och uppdatera pooler

En batchpool kan innehålla både dedikerade och lågprioriterade virtuella datorer (kallas även beräkningsnoder). Du kan ange målantalet beräkningsnoder för både dedikerade och lågprioriterade virtuella datorer. Målantalet noder anger antalet virtuella datorer som du vill ha i poolen.

Om du till exempel vill skapa en pool med virtuella azure-molntjänstds med ett mål på 5 dedikerade virtuella datorer och 20 virtuella datorer med låg prioritet:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Så här skapar du en pool med virtuella Azure-datorer (i det här fallet virtuella Linux-datorer) med ett mål på 5 dedikerade virtuella datorer och 20 virtuella datorer med låg prioritet:

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

Poolnoder har en egenskap som anger om noden är en dedikerad eller lågprioriterad virtuell dator:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

När en eller flera noder i en pool är föregripna returnerar en listnoder åtgärd på poolen fortfarande dessa noder. Det aktuella antalet noder med låg prioritet förblir oförändrat, men dessa noder har sitt tillstånd inställt på **tillståndet Preempted.** Batch försöker hitta ersättnings-virtuella datorer och, om det lyckas, noderna gå igenom **Skapa** och sedan **starta** lägen innan de blir tillgängliga för körning av aktiviteter, precis som nya noder.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skala en pool med lågprioriterade virtuella datorer

Precis som med pooler som enbart består av dedikerade virtuella datorer är det möjligt att skala en pool som innehåller virtuella datorer med låg prioritet genom att anropa metoden Ändra storlek eller genom automatisk skalning.

Åtgärden för storleksstorlek tar en andra valfri parameter som uppdaterar värdet **för targetLowPriorityNodes:**

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Formeln för automatisk skalning av poolen stöder virtuella datorer med låg prioritet enligt följande:

-   Du kan hämta eller ange värdet för den tjänstdefinierade variabeln **$TargetLowPriorityNodes**.

-   Du kan hämta värdet för den tjänstdefinierade variabeln **$CurrentLowPriorityNodes**.

-   Du kan hämta värdet för den tjänstdefinierade variabeln **$PreemptedNodeCount**. 
    Den här variabeln returnerar antalet noder i det förbegripna tillståndet och låter dig skala upp eller ned antalet dedikerade noder, beroende på antalet förbegripna noder som inte är tillgängliga.

## <a name="jobs-and-tasks"></a>Jobb och uppgifter

Jobb och uppgifter kräver lite ytterligare konfiguration för noder med låg prioritet. det enda stödet är följande:

-   Egenskapen JobManagerTask för ett jobb har en ny egenskap, **AllowLowPriorityNode**. 
    När den här egenskapen är true kan jobbhanterarens aktivitet schemaläggas på antingen en dedikerad nod med låg prioritet. Om den här egenskapen är falsk schemaläggs jobbhanterarens aktivitet endast till en dedikerad nod.

-   En [miljövariabel](batch-compute-node-environment-variables.md) är tillgänglig för ett aktivitetsprogram så att den kan avgöra om den körs på en lågprioriterad eller dedikerad nod. Miljövariabeln är AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Hantering av förebyggande

Virtuella datorer kan ibland föregripas. när förköp inträffar gör Batch följande:

-   De förutbmmade virtuella datorerna har uppdaterat sitt tillstånd till **Preempted**.
-   Om aktiviteter kördes på de förutbordrade virtuella nod-datorerna, köas dessa aktiviteter igen och körs igen.
-   Den virtuella datorn tas effektivt bort, vilket leder till förlust av data som lagras lokalt på den virtuella datorn.
-   Poolen försöker kontinuerligt nå målantalet tillgängliga noder med låg prioritet. När ersättningskapacitet hittas behåller noderna sina ID:n, men initieras om och går igenom **skapa** och **starta** lägen innan de är tillgängliga för schemaläggning av aktiviteter.
-   Antal förförde utgifter är tillgängliga som ett mått i Azure-portalen.

## <a name="metrics"></a>Mått

Nya mått är tillgängliga i [Azure-portalen](https://portal.azure.com) för noder med låg prioritet. Dessa mått är:

- Antal nodrad med låg prioritet
- Kärnantal med låg prioritet 
- Antal föregripna nod

Så här visar du mått i Azure-portalen:

1. Navigera till ditt Batch-konto i portalen och visa inställningarna för ditt Batch-konto.
2. Välj **Mått** i avsnittet **Övervakning.**
3. Välj de mått du önskar i listan **Tillgängliga mått.**

![Mått för noder med låg prioritet](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* Börja med att planera övergången från virtuella datorer med låg prioritet till Spot-virtuella datorer. Om du använder virtuella datorer med låg prioritet med konfigurationspooler för **Molntjänsten** planerar du att gå till konfigurationspooler **för virtuella** datorer.
