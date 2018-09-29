---
title: Söker efter Batch-pool och nod-fel
description: Fel att söka efter och hur du undviker när du skapar pooler och noder
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435225"
---
# <a name="checking-for-pool-and-node-errors"></a>Söker efter fel i poolen och nod

När du skapar och hanterar Batch-pooler, det finns åtgärder som sker omedelbart och det finns asynkrona åtgärder som inte är omedelbart, körs i bakgrunden och kan ta flera minuter att slutföra.

Det är enkelt, att upptäcka fel för åtgärder som börjar gälla direkt som fel ska returneras direkt från den API, CLI eller Användargränssnittet.

Den här artikeln beskriver bakgrundsåtgärder kan ske för pooler och poolnoder – anger hur fel kan identifieras och hur felen kan undvikas.

## <a name="pool-errors"></a>Pool-fel

### <a name="resize-timeout-or-failure"></a>Ändra storlek på timeout eller fel

När skapar en ny pool eller ändra storlek på en befintlig pool, mål för antal noder har angetts.  Åtgärden slutförs direkt, men den faktiska tilldelningen av nya noder och ta bort befintliga noderna sker i bakgrunden över vad kan ta flera minuter.  En tidsgräns för storleksändring har angetts i den [skapa](https://docs.microsoft.com/rest/api/batchservice/pool/add) eller [ändra storlek på](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API – om inte går att hämta antalet noder i tidsgränsen storleksändring sedan åtgärden avbryts till poolen som kommer att ett stabilt tillstånd och problem med att ändra storlek på.

En tidsgräns för storleksändring rapporteras av den [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) -egenskapen för senaste utvärdering, som visar ett eller flera fel som inträffat.

Vanliga orsaker för storleksändring timeout är:
- Tidsgräns för storleksändring är för kort
  - Standardvärdet för timeout är 15 minuter, vilket är normalt gott om tid för noderna i poolen tilldelas eller tas bort.
  - När du tilldelar ett stort antal noder (över 1 000 noder från en Marketplace-avbildning) eller över 300 noder från en anpassad avbildning när poolen skapas eller ändra storlek på, rekommenderas en 30 minuters timeout.
- Otillräcklig kärnkvot
  - Ett batch-konto har en kvot för antalet kärnor som är fördelade över alla pooler.  Batch kommer inte att tilldela noder när den kvoten har nåtts.  Kärnkvoten [kan du öka](https://docs.microsoft.com/azure/batch/batch-quota-limit) att aktivera flera noder som ska allokeras.
- Inte tillräckligt med undernätet IP-adresser när en [pool är i ett virtuellt nätverk](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Ett virtuellt nätverksundernät måste ha tillräckligt med otilldelade IP-adresser som tilldelas till alla noderna i poolen som efterfrågas, annars noderna kan inte skapas.
- Inte tillräckligt med resurser när en [pool är i ett virtuellt nätverk](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Resurser, till exempel belastningsutjämnare, offentliga IP-adresser och NSG: er skapas i den prenumeration som används för att skapa Batch-kontot.  Prenumerationskvoter för dessa resurser måste vara tillräcklig.
- Med hjälp av en anpassad virtuell datoravbildning för stora pooler
  - Stora pooler med hjälp av anpassade avbildningar kan tar längre tid att tilldela och ändra storlek på timeout-fel kan inträffa.  Begränsningar och rekommendationer finns i en [artikel](https://docs.microsoft.com/azure/batch/batch-custom-images). 

### <a name="auto-scale-failures"></a>Autoskala fel

I stället för att uttryckligen ange målantalet noder för en pool i poolen skapades eller ändra storlek på, skalas antalet noder i poolen automatiskt.  En [formel för automatisk skalning kan skapas för en pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling), som kommer att utvärderas med regelbundna intervall kan konfigureras att ange antalet noder i poolen.  Följande typer av problem kan uppstå och har identifierats:

- Autoskala utvärderingen kan misslyckas.
- Den resulterande storleksändringen kan misslyckas och timeout.
- Det kan finnas ett problem med automatisk skalning formeln, vilket leder till felaktig nod målvärden, med storleksändring arbeta eller tidsgränsen uppnås.

Information om den senaste Autoskala utvärderingen hämtas med hjälp av den [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) egenskapen, som rapporterar om tiden för utvärderingen, värden och resultatet av utvärderingen och eventuella fel som utför utvärderingen.

Information om alla utvärderingar läggs automatiskt till av en [poolen ändrar storlek på Sluthändelse](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Ta bort

Om vi antar att det finns noder i poolen, sedan en pool ta bort åtgärden leder till noderna tas bort först, följt av själva poolobjektet.  Det kan ta några minuter för noderna i poolen som ska tas bort.

Den [lagringspoolen tillstånd](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) ställs in på att tas bort under ta bort.  Det anropande programmet kan identifiera om pool delete tar för lång med hjälp av egenskaperna ”state” och ”stateTransitionTime”.

## <a name="pool-compute-node-errors"></a>Nodfel för poolen beräkning

Noder kan fördelas har i en pool, men olika problem som kan leda till noderna som feltillstånd och inte användas.  När noderna har allokerats i en pool kan de avgifter och det är därför viktigt att identifiera problem för att undvika att betala för noder som inte får återanvändas.

### <a name="start-task-failure"></a>Uppgiften startfel

En valfri [startaktivitet](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) kan anges för en pool.  Precis som med andra aktiviteter kan kan en kommandorad och resursfiler att ladda ned från storage anges.  Startuppgiften har angetts för poolen, men körs på varje nod – när varje nod har startats sedan startaktiviteten körs.  En ytterligare egenskap för den [startaktivitet](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask), waitForSuccess, anger om Batch ska vänta tills startaktiviteten har slutförts innan övriga uppgifter till en nod schemaläggs.

Om startaktiviteten misslyckas och uppgiftskonfigurationen start angetts att vänta på slutförande, sedan noden kan inte användas och du kommer fortfarande att debiteras.

Starta Aktivitetsfel kan identifieras med hjälp av den [resultatet](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) och [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) egenskaperna för den översta [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) nodegenskap.

En misslyckad startaktivitet leder också till noden [tillstånd](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) anges till ”starttaskfailed”, men endast om ”waitForSuccess” har angetts till 'true'.

Precis som med andra aktiviteter kan finnas det flera orsaker till att starta aktiviteten misslyckas.  Om du vill felsöka, bör det kontrolleras att stdout, stderr och alla ytterligare uppgiftsspecifika loggfiler.

### <a name="application-package-download-failure"></a>Hämtning av programfel-paket

En eller flera programpaket kan du kan också anges för en pool med angivet paketfilerna laddas ned till varje nod och okomprimerade när noden har startats, men innan aktiviteterna schemaläggs.  Det är vanligt att använda Kommandotolken start uppgiften tillsammans med programpaket, för att kopiera filer till en annan plats eller kör installationsprogrammet, till exempel.

Om det gick inte att ladda ned och expandera ett programpaket som ska rapporteras av noden [fel](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) egenskapen.  Tillstånd för nätverksnoder sätts till ”oanvändbara'.

### <a name="node-in-unusable-state"></a>Nod i oanvändbar

Den [Nodtillstånd](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) kan anges till oanvändbara av flera orsaker.  När det är oanvändbara, aktiviteter kan inte schemaläggas till noden, men noden kommer fortfarande att debiteras.

Batch försöker alltid återställa oanvändbara noder, men kanske eller kanske inte är möjligt, beroende på orsaken.

Om orsaken kan fastställas, det rapporteras av noden [fel](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) egenskapen.

Några andra exempel på orsaker till 'oanvändbara' noder:

- Ogiltig anpassad avbildning; inte förberetts korrekt, till exempel.
- Infrastruktur-fel eller på låg nivå uppgradering som leder till den underliggande virtuella datorn flyttas; Batch ska återställa noden.

### <a name="node-agent-log-files"></a>Noden agentens loggfiler

Om det är nödvändigt att kontakta support för en pool node-problem kan loggfiler från Batch agent-processen som körs på varje nod i poolen hämtas.  Loggfilerna för en nod kan överföras via Azure-portalen, Batch Explorer eller en [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs).  Ladda upp och sparar loggfilerna kan vara mycket användbara som noden eller pool kan tas bort om du vill spara kostnaden för noderna som körs.

## <a name="next-steps"></a>Nästa steg

Se till att ditt program har implementerat omfattande felkontroll, särskilt för asynkrona åtgärder så att problem kan identifieras snabbt och diagnostiseras.
