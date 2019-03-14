---
title: Kontrollera pool och nod – Azure Batch
description: Fel att söka efter och hur man undviker dem när du skapar pooler och noder
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 8d8df9935e935ac8d5a1194cfab103a006cf5546
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791349"
---
# <a name="check-for-pool-and-node-errors"></a>Kontrollera pool och nod

När du skapar och hanterar Azure Batch-pooler, sker vissa åtgärder omedelbart. Men är vissa åtgärder asynkrona och kan köras i bakgrunden. De kan ta flera minuter att slutföra.

Det är enkelt att identifiera fel för åtgärder som börjar gälla direkt eftersom fel returneras direkt av API: et, CLI eller Användargränssnittet.

Den här artikeln beskriver bakgrundsåtgärder som kan uppstå för pooler och poolnoder. Den anger hur du kan identifiera och undvika fel.

## <a name="pool-errors"></a>Pool-fel

### <a name="resize-timeout-or-failure"></a>Ändra storlek på timeout eller fel

När du skapar en ny pool eller ändra storlek på en befintlig pool, anger du antalet noder.  Åtgärden har slutförts omedelbart, men den faktiska tilldelningen av nya noder eller borttagning av befintliga noderna kan ta flera minuter.  Du anger tidsgräns för storleksändring i den [skapa](https://docs.microsoft.com/rest/api/batchservice/pool/add) eller [ändra storlek på](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API. Om Batch går inte att hämta antalet noder under storleksändring tidsperioden, avbryts åtgärden. Poolen hamnar i ett stabilt tillstånd och rapporter ändra storlek på fel.

Den [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) -egenskapen för de senaste utvärderingen rapporterar en timeout för storleksändring och visar eventuella fel som inträffat.

Vanliga orsaker för storleksändring timeout är:

- Tidsgräns för storleksändring är för kort
  - I de flesta fall är standardvärdet för timeout högst 15 minuter lång tid för noderna i poolen tilldelas eller tas bort.
  - Om du allokerar ett stort antal noder, rekommenderar vi att ändra storlek på timeout-värdet till 30 minuter. Till exempel när du storleksändring att fler än 1 000 noder från en Azure Marketplace-avbildning eller mer än 300 noder från en anpassad virtuell datoravbildning.
- Otillräcklig kärnkvot
  - Ett Batch-konto är begränsad i antalet kärnor som den kan allokera över alla pooler. Batch stoppar allokera noder när den kvoten har nåtts. Du [kan öka](https://docs.microsoft.com/azure/batch/batch-quota-limit) kärnkvoten så att Batch kan allokera fler noder.
- Inte tillräckligt med undernätet IP-adresser när en [pool är i ett virtuellt nätverk](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Ett virtuellt nätverksundernät måste ha tillräckligt med otilldelade IP-adresser som tilldelas till alla begärda pool-noder. I annat fall kan noderna inte skapas.
- Inte tillräckligt med resurser när en [pool är i ett virtuellt nätverk](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Du kan skapa resurser, till exempel belastningsutjämnare, offentliga IP-adresser och nätverkssäkerhetsgrupper i samma prenumeration som Batch-kontot. Kontrollera att prenumerationskvoter är tillräckliga för de här resurserna.
- Stora pooler med anpassade VM-avbildningar
  - Stora pooler som använder anpassade avbildningar av Virtuella datorer kan ta längre tid att allokera och ändra storlek på timeout-fel kan uppstå.  Se [använda en anpassad avbildning för att skapa en pool med virtuella datorer](https://docs.microsoft.com/azure/batch/batch-custom-images) rekommendationer för gränser och konfigurering.

### <a name="automatic-scaling-failures"></a>Automatisk skalning fel

Du kan också ange att automatiskt skala antalet noder i en pool med Azure Batch. Du kan definiera parametrar för den [formel för en pool för automatisk skalning](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Batch-tjänsten använder formeln för att regelbundet utvärdera antalet noder i poolen och ange ett nytt mål-nummer. Följande typer av problem kan uppstå:

- Det går inte att automatisk skalning utvärderingen.
- Den resulterande storleksändringen misslyckas och tidsgränsen uppnås.
- Ett problem med automatisk skalningsformel leder till felaktig nod målvärden. Ändra storleken fungerar eller överskrider tidsgränsen.

Du kan få information om den senaste utvärderingen för automatisk skalning med hjälp av den [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) egenskapen. Den här egenskapen rapporterar utvärderingstiden, värden och resultatet och eventuella prestandafel.

Den [poolen ändrar storlek på Sluthändelse](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) samlar in information om alla utvärderingar.

### <a name="delete"></a>Ta bort

När du tar bort en pool som innehåller noder första batchen tar bort noder. Sedan tar den bort själva poolobjektet. Det kan ta några minuter för noderna i poolen som ska tas bort.

Batch-uppsättningar på [lagringspoolen tillstånd](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) till **tar bort** under borttagningsprocessen. Det anropande programmet kan identifiera om borttagningen pool tar lång tid genom att använda den **tillstånd** och **stateTransitionTime** egenskaper.

## <a name="pool-compute-node-errors"></a>Nodfel för poolen beräkning

Även om Batch har allokerar noder i en pool, kan olika problem som orsaka vissa noder är skadad och kan inte användas. Dessa noder avgifter. Det är viktigt att identifiera problem så att du inte betala för oanvändbara noder.

### <a name="start-task-failure"></a>Uppgiften startfel

Du kanske vill ange en valfri [startaktivitet](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) för en pool. Precis som med andra aktiviteter kan använda du en kommandorad och resursfiler för att ladda ned från storage. Startaktiviteten körs för varje nod när den startas. Den **waitForSuccess** egenskapen anger om Batch ska vänta tills startaktiviteten har slutförts innan det schemalägger aktiviteter till en nod.

Vad händer om du har konfigurerat noden att vänta på slutförande av aktivitet lyckas start, men den fungerar inte uppgiften start? I så fall noden är oanvändbara men fortfarande tillkommer avgifter.

Du kan identifiera Aktivitetsfel för start med hjälp av den [resultatet](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) och [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) egenskaperna för den översta [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) nodegenskap.

En misslyckad startaktivitet orsakar även Batch ange nod [tillstånd](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) till **starttaskfailed** om du skulle ställa in **waitForSuccess** till **SANT**.

Precis som med andra aktiviteter kan finnas det flera orsaker till att starta aktiviteten misslyckas.  Felsök genom att kontrollera att stdout, stderr och alla ytterligare uppgiftsspecifika loggfiler.

### <a name="application-package-download-failure"></a>Hämtning av programfel-paket

Du kan ange en eller flera programpaket för en pool. Batch laddar ned filer för angivet paket till varje nod och expanderar filerna när noden har startats men innan aktiviteterna schemaläggs. Det är vanligt att använda Kommandotolken start uppgiften tillsammans med programpaket. Till exempel att kopiera filer till en annan plats eller för att köra installationsprogrammet.

Noden [fel](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) egenskapen rapporterar inte går att ladda ned och expandera ett programpaket. Batch anger node-tillstånd till **oanvändbara**.

### <a name="node-in-unusable-state"></a>Nod i oanvändbar

Azure Batch kan ställa in den [Nodtillstånd](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) till **oanvändbara** av flera orsaker. Med Nodtillstånd inställd **oanvändbara**, aktiviteter kan inte schemaläggas till noden, men det tillkommer fortfarande avgifter.

Batch försöker alltid att återställa oanvändbara noder, men kanske eller kanske inte är möjligt beroende på orsaken.

Om Batch kan ta reda på orsaken, noden [fel](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) egenskapen rapporterar den.

Ytterligare exempel på orsaker till **oanvändbara** noder inkluderar:

- En anpassad virtuell datoravbildning är ogiltig. Till exempel en bild som förbereds inte korrekt.
- En virtuell dator flyttas på grund av en infrastruktur-fel eller en uppgradering av på låg nivå. Batch återställer noden.

### <a name="node-agent-log-files"></a>Noden agentens loggfiler

Processen för Batch-agenten som körs på varje nod i poolen kan ge loggfiler som kan vara användbart om du behöver kontakta supporten om problemet en pool-nod. Loggfiler för en nod kan överföras via Azure-portalen, Batch Explorer eller en [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Det är användbart för att ladda upp och spara filerna. Därefter kan du ta bort noden eller poolen för att spara kostnaden för att köra noderna.

## <a name="next-steps"></a>Nästa steg

Kontrollera att du har ställt in ditt program för att implementera omfattande felkontroll, särskilt för asynkrona åtgärder. Det kan vara viktigt att snabbt identifiera och diagnostisera problem.
