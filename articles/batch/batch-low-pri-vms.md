---
title: "Köra Azure Batch arbetsbelastningar på virtuella datorer kostnadseffektiv låg prioritet | Microsoft Docs"
description: "Lär dig hur du etablerar låg prioritet virtuella datorer för att minska kostnaden för Azure Batch-arbetsbelastningar."
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 01/26/2018
ms.author: markscu
ms.openlocfilehash: 8490bd8c18930c025902a247e6c1df8a0716ed76
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="use-low-priority-vms-with-batch"></a>Med Batch VM med låg prioritet

Azure Batch har låg prioritet virtuella datorer (VM) för att minska kostnaden för Batch-arbetsbelastningar. VM med låg prioritet möjliggör nya typer av Batch-arbetsbelastningar genom att tillhandahålla mycket datorkraft som också är ekonomiskt.

Låg prioritet VMs utnyttja överflödiga kapacitet i Azure. När du anger låg prioritet virtuella datorer i din pooler kan Azure Batch automatiskt använda den här överskott när det är tillgängligt.

Förhållandet för med låg prioritet virtuella datorer är dessa virtuella datorer kan avbrytas om någon överskott kapacitet finns i Azure. Därför är låg prioritet mest lämpliga för vissa typer av arbetsbelastningar. Använd VM med låg prioritet för batch- och asynkron bearbetning arbetsbelastningar där slutförandetid jobbet är flexibel och vad som ska distribueras till många virtuella datorer.

Låg prioritet VMs erbjuds till en betydligt mindre pris jämfört med dedikerade virtuella datorer. Information om priser, se [Batch priser](https://azure.microsoft.com/pricing/details/batch/).


> [!IMPORTANT]
> Låg prioritet är endast tillgänglig för arbetsbelastningar som körs i en Batch. 
>
>

## <a name="use-cases-for-low-priority-vms"></a>Användningsfall för VM med låg prioritet

Angivna egenskaper med låg prioritet virtuella datorer, vilka arbetsbelastningar kan och inte kan använda dem? I allmänhet är batch bearbetningsbelastningar passa, eftersom jobben är uppdelad i många parallella aktiviteter eller det finns många jobb som skalats ut och distribueras över flera virtuella datorer.

-   Om du vill maximera användningen av överskott kapacitet i Azure kan lämplig jobb skala ut.

-   Ibland virtuella datorer kanske inte tillgänglig eller frånkopplas, vilket leder till minskad kapacitet för jobb och kan leda till avbrott i aktiviteten och repriser. Jobb måste därför vara flexibel tidpunkt som de kan ta för att köra.

-   Jobb med längre uppgifter kan påverkas mer om avbryts. Om tidskrävande uppgifter implementera kontrollpunkter för att spara förloppet som de körs, minska påverkan av avbrott. Uppgifter med kortare körningstider tenderar fungerar bäst med låg prioritet virtuella datorer, eftersom effekten av avbrott är mycket mindre.

-   Långvariga MPI-jobb som använder flera virtuella datorer är inte bra att använda VM med låg prioritet, eftersom en frånkopplas virtuell dator kan leda till att hela jobbet behöva köra igen.

Det är några exempel på batch bearbetning användningsfall och lämpligt för att använda VM med låg prioritet:

-   **Utveckling och testning**: I synnerhet om storskaliga lösningar utvecklas betydande besparingar kan genomföras. Kan dra nytta av alla typer av testning, men stora belastningen testning och regression testning är bra använder.

-   **Kompletta kapacitet på begäran**: VM med låg prioritet kan användas för att komplettera regular dedikerade virtuella datorer – när det är tillgängligt, jobb kan skalas och därför slutföra snabbare för lägre kostnad; när det är inte tillgänglig, baslinje för dedikerade virtuella datorer fortfarande är tillgänglig .

-   **Flexibla jobbet körningstid**: om det finns flexibilitet i tid jobben har slutförts, och sedan potentiella fall kapaciteten som kan tillåtas, men med låg prioritet VMs jobb ofta köra snabbare och för en lägre kostnad.

Batch-pooler kan konfigureras för att använda låg prioritet virtuella datorer på flera sätt, beroende på flexibilitet vid körningstid för jobb:

-   VM med låg prioritet kan endast användas i en pool. I det här fallet återställer Batch alla preempted kapacitet när det är tillgängligt. Den här konfigurationen är det billigaste sättet att köra jobb, som användes för virtuella datorer bara låg prioritet.

-   VM med låg prioritet kan användas tillsammans med en fast baslinje för dedikerade virtuella datorer. Fast antal dedikerade virtuella datorer garanterar alltid är vissa kapacitet att behålla en jobbet fortskrider.

-   Det kan vara dynamiska blandning av dedikerade och låg prioritet virtuella datorer, så att de billigare låg prioritet virtuella datorerna används endast när det är tillgängligt, men fullständig prisvärda dedikerade virtuella datorer skalas vid behov. Den här konfigurationen håller en minimal mängd kapacitet för att hålla jobb fortskrider.

## <a name="batch-support-for-low-priority-vms"></a>Batch-stöd för virtuella datorer låg prioritet

Azure Batch innehåller flera funktioner som gör det enkelt att använda och dra nytta av låg prioritet virtuella datorer:

-   Batch-pooler kan innehålla både dedikerade virtuella datorer och låg prioritet virtuella datorer. Numret på varje typ av virtuell dator kan anges när en programpool skapas eller ändras när som helst för en befintlig pool med hjälp av åtgärden explicit ändra storlek eller Autoskala. Jobb- och skicka kan förblir oförändrat oavsett VM-typer i poolen. Du kan också konfigurera en pool för att använda helt VM med låg prioritet för att köra jobb som billigt som möjligt, men rotationsrutan upp dedikerade virtuella datorer om kapacitet sjunker under en minsta tröskelvärdet för att hålla jobb som körs.

-   Batch-pooler söka automatiskt mål antalet VM med låg prioritet. Om virtuella datorer som frånkopplas försöker Batch ersätta förlorade kapacitet och återgå till målet.

-   När uppgifter avbryts Batch identifierar och ställs i kö igen automatiskt att åtgärderna ska köras igen.

-   Låg prioritet virtuella datorer har en separat vCPU kvot som skiljer sig från en för dedikerade virtuella datorer. 
    Kvoten för låg prioritet virtuella datorer är högre än kvoten för dedikerade virtuella datorer, eftersom VM med låg prioritet billigare. Mer information finns i [Batch-tjänsten kvoter och gränser](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Låg prioritet virtuella datorer stöds inte för närvarande för Batch-konton som skapats i [prenumeration användarläge](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Skapa och uppdatera pooler

En Batch-pool kan innehålla både dedikerade och låg prioritet virtuella datorer (även kallat compute-noder). Du kan ange antalet datornoderna mål för virtuella datorer både dedikerade och låg prioritet. Mål antalet noder som anger hur många virtuella datorer du vill ha i poolen.

Till exempel om du vill skapa en pool dedikerade med hjälp av Azure-molntjänst virtuella datorer med ett mål för 5 virtuella datorer och 20 låg prioritet för virtuella datorer:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Om du vill skapa en pool dedikerade med virtuella Azure-datorer (i det här fallet virtuella Linux-datorer) med ett mål för 5 virtuella datorer och 20 låg prioritet för virtuella datorer:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
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

Du kan få det aktuella antalet noder för virtuella datorer både dedikerade och låg prioritet:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Poolen noder har en egenskap som anger om noden är en virtuell dator för dedikerade eller låg prioritet:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

När en eller flera noder i en pool frånkopplas returnerar en lista över noderna åtgärd på poolen fortfarande de noderna. Det aktuella antalet noder med låg prioritet förblir oförändrat, men dessa noder har sina statusen inställd på den **tillfälligt** tillstånd. Batch försöker hitta ersättning av virtuella datorer och, om det lyckas, noderna gå igenom **skapa** och sedan **Start** tillstånd innan den blir tillgänglig för körning av aktiviteten, precis som nya noder.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skala en pool som innehåller VM med låg prioritet

Som med pooler som endast består av dedikerade virtuella datorer, är det möjligt att skala en pool som innehåller låg prioritet virtuella datorer genom att anropa metoden ändra storlek eller Autoskala.

Åtgärden Ändra storlek poolen tar en valfri andra parameter som uppdaterar värdet för **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Poolen Autoskala formeln stöder låg prioritet virtuella datorer på följande sätt:

-   Du kan hämta eller ange värdet på variabeln tjänst definierade **$TargetLowPriorityNodes**.

-   Du kan hämta värdet för variabeln tjänst definierade **$CurrentLowPriorityNodes**.

-   Du kan hämta värdet för variabeln tjänst definierade **$PreemptedNodeCount**. 
    Den här variabeln returnerar antalet noder i preempted tillstånd och gör att du kan skala upp eller ned antalet dedicerade noder, beroende på antalet frånkopplas noder som inte är tillgängliga.

## <a name="jobs-and-tasks"></a>Jobb och uppgifter

Jobb och uppgifter kräver lite ytterligare konfiguration för låg prioritet noder. endast stöder är följande:

-   Egenskapen JobManagerTask för ett jobb har en ny egenskap **AllowLowPriorityNode**. 
    När den här egenskapen är true kan du schemalägga projektaktiviteten manager på en dedikerad eller låg prioritet nod. Om den här egenskapen är false kommer projektaktiviteten manager till en dedikerad nod.

-   En [miljövariabeln](batch-compute-node-environment-variables.md) är tillgänglig för programmet för en aktivitet så att den kan avgöra om den körs på en nod med låg prioritet eller dedikerade. Miljövariabeln är AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Hantera avstängning

Virtuella datorer kan ibland avbrytas; När avbrott inträffar kan gör Batch följande:

-   Preempted virtuella datorer har det tillståndet som uppdateras till **tillfälligt**.
-   Om aktiviteter körs på de virtuella datorerna som frånkopplas nod, har sedan aktiviteterna placerats i kö och kör igen.
-   Den virtuella datorn är effektivt bort, vilket leder till förlust av alla data som lagras lokalt på den virtuella datorn.
-   Poolen försöker kontinuerligt nå målet antalet låg prioritet noder som är tillgängliga. När ersättningskapacitet hittas noderna hålla sina ID: N, men ominitieras, gå igenom **skapa** och **Start** tillstånd innan de är tillgängliga för schemaläggning.
-   Avstängningen antal är tillgängliga som ett mått i Azure-portalen.

## <a name="metrics"></a>Mått

Nya är tillgängliga i den [Azure-portalen](https://portal.azure.com) för låg prioritet noder. Dessa är:

- Antalet noder med låg prioritet
- Antal kärnor med låg prioritet 
- Antalet frånkopplas noder

Visa mått i Azure-portalen:

1. Navigera till Batch-kontot i portalen och visa inställningarna för Batch-kontot.
2. Välj **mått** från den **övervakning** avsnitt.
3. Välj det mått som du vill ha av den **tillgängliga mått** lista.

![Mätvärden för noder med låg prioritet](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
