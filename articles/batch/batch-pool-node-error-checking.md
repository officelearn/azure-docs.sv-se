---
title: Sök efter pool- och nodfel - Azure Batch
description: Den här artikeln beskriver de bakgrundsåtgärder som kan uppstå, tillsammans med fel att söka efter och hur du undviker dem när du skapar pooler och noder.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: a68d812a044c776819d169d5bf179f011d06390f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472953"
---
# <a name="check-for-pool-and-node-errors"></a>Sök efter pool- och nodfel

När du skapar och hanterar Azure Batch-pooler sker vissa åtgärder omedelbart. Vissa åtgärder är dock asynkrona och körs i bakgrunden, tar flera minuter att slutföra.

Att identifiera fel för åtgärder som utförs omedelbart är enkelt eftersom eventuella fel returneras omedelbart av API, CLI eller UI.

Den här artikeln beskriver de bakgrundsåtgärder som kan utföras för pooler och poolnoder. Den anger hur du kan upptäcka och undvika fel.

## <a name="pool-errors"></a>Poolfel

### <a name="resize-timeout-or-failure"></a>Ändra storlek på timeout eller fel

När du skapar en ny pool eller ändrar storlek på en befintlig pool anger du målantalet noder.  Åtgärden skapa eller ändra storlek slutförs omedelbart, men den faktiska allokeringen av nya noder eller borttagningen av befintliga noder kan ta flera minuter.  Du anger tidsgränsen för storleksfördelning i API:et [för att skapa](https://docs.microsoft.com/rest/api/batchservice/pool/add) eller ändra [storlek.](https://docs.microsoft.com/rest/api/batchservice/pool/resize) Om Batch inte kan hämta målantalet noder under tidsgränsen för storleksfördelning går poolen i ett stabilt tillstånd och rapporter ändrar storlek på fel.

Egenskapen [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) för den senaste utvärderingen visar de fel som uppstod.

Vanliga orsaker till att fel ändra storlek är:

- Tidsgränsen för att ändra storlek på timeout är för kort
  - Under de flesta omständigheter är standardtidsgränsen på 15 minuter tillräckligt lång för att poolnoder ska kunna allokeras eller tas bort.
  - Om du allokerar ett stort antal noder rekommenderar vi att du anger tidsgränsen för storleksfördelning till 30 minuter. Till exempel när du ändrar storlek på mer än 1 000 noder från en Azure Marketplace-avbildning, eller till mer än 300 noder från en anpassad VM-avbildning.
- Otillräcklig kärnkvot
  - Ett batchkonto är begränsat i antalet kärnor som det kan allokera över alla pooler. Batch slutar allokera noder när kvoten har uppnåtts. Du [kan öka](https://docs.microsoft.com/azure/batch/batch-quota-limit) kärnkvoten så att Batch kan allokera fler noder.
- Otillräckliga IP-adresser för undernät när en [pool finns i ett virtuellt nätverk](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Ett virtuellt nätverksundernät måste ha tillräckligt med otilldelade IP-adresser för att allokera till varje begärd poolnod. Annars kan noderna inte skapas.
- Otillräckliga resurser när en [pool finns i ett virtuellt nätverk](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Du kan skapa resurser som belastningsutjämnare, offentliga IPs och nätverkssäkerhetsgrupper i samma prenumeration som batchkontot. Kontrollera att prenumerationskvoterna är tillräckliga för dessa resurser.
- Stora pooler med anpassade VM-avbildningar
  - Stora pooler som använder anpassade VM-avbildningar kan ta längre tid att allokera och ändra storlek på tidsutskrifter kan uppstå.  Se [Skapa en pool med det delade bildgalleriet](batch-sig-images.md) för rekommendationer om gränser och konfiguration.

### <a name="automatic-scaling-failures"></a>Automatiska skalningsfel

Du kan också ange att Azure Batch automatiskt ska skala antalet noder i en pool. Du definierar parametrarna för den [automatiska skalningsformeln för en pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Batch-tjänsten använder formeln för att regelbundet utvärdera antalet noder i poolen och ange ett nytt målnummer. Följande typer av problem kan uppstå:

- Den automatiska skalningsutvärderingen misslyckas.
- Den resulterande storleksåtgärden misslyckas och time out.
- Ett problem med den automatiska skalningsformeln leder till felaktiga målvärden för nod. Ändra storlek antingen fungerar eller time out.

Du kan få information om den senaste automatiska skalningsutvärderingen med hjälp av egenskapen [autoScaleRun.](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) Den här egenskapen rapporterar utvärderingstiden, värdena och resultatet samt eventuella prestandafel.

[Poolen ändrar storlek på fullständig händelse](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) samlar in information om alla utvärderingar.

### <a name="delete"></a>Ta bort

När du tar bort en pool som innehåller noder tas noderna bort först. Sedan tas själva poolobjektet bort. Det kan ta några minuter innan poolnoderna tas bort.

Batch anger [att pooltillståndet](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) **tas bort** under borttagningsprocessen. Anropande programmet kan identifiera om poolborttagningen tar för lång tid med hjälp av egenskaperna **state** and **stateTransitionTime.**

## <a name="pool-compute-node-errors"></a>Fel vid poolberäkningsnod

Även om Batch har allokerat noder i en pool kan olika problem orsaka att vissa noder är felaktiga och inte kan köra aktiviteter. Dessa noder ådrar sig fortfarande avgifter, så det är viktigt att identifiera problem för att undvika att betala för noder som inte kan användas. Förutom vanliga nodfel är det användbart att känna till det aktuella [jobbtillståndet](/rest/api/batchservice/job/get#jobstate) för felsökning.

### <a name="start-task-failures"></a>Starta aktivitetsfel

Du kanske vill ange en valfri [startuppgift](/rest/api/batchservice/pool/add#starttask) för en pool. Precis som med alla aktiviteter kan du använda en kommandorad och resursfiler för att hämta från lagring. Startaktiviteten körs för varje nod när den har startats. Egenskapen **waitForSuccess** anger om Batch väntar tills startaktiviteten har slutförts innan alla aktiviteter schemaläggs till en nod.

Vad händer om du har konfigurerat noden för att vänta på att aktiviteten har slutförts ska slutföras, men startaktiviteten misslyckas? I så fall kommer noden inte att kunna användas, men kommer fortfarande att debiteras avgifter.

Du kan identifiera startaktivitetsfel med hjälp av [egenskaperna result](/rest/api/batchservice/computenode/get#taskexecutionresult) och [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) för den översta [startTaskInfo-noden.](/rest/api/batchservice/computenode/get#starttaskinformation)

En misslyckad startaktivitet gör också att Batch ställer in [nodtillståndet](/rest/api/batchservice/computenode/get#computenodestate) för **starttaskmisslyckades** om **waitForSuccess** har angetts till **true**.

Som med alla uppgifter kan det finnas många orsaker till att startaktiviteten misslyckas.  Felsökning genom att kontrollera stdout- , stderr och eventuella ytterligare uppgiftsspecifika loggfiler.

Startaktiviteter måste återdeltagare, eftersom det är möjligt att startaktiviteten körs flera gånger på samma nod. Startaktiviteten körs när en nod görs om eller startas om. I sällsynta fall körs en startuppgift efter att en händelse orsakade en omstart av en nod, där en av operativsystemet eller efemära diskar gjordes om medan den andra inte var det. Eftersom batchstartaktiviteter (som alla batchuppgifter) körs från den tillfälliga disken är detta normalt inte ett problem, men i vissa fall där startuppgiften installerar ett program på operativsystemets disk och behåller andra data på efemära disken, kan detta orsaka eftersom saker och ting inte är synkroniserade. Skydda ditt program därefter om du använder båda diskarna.

### <a name="application-package-download-failure"></a>Nedladdning av programpaket misslyckades

Du kan ange ett eller flera programpaket för en pool. Batch hämtar de angivna paketfilerna till varje nod och komprimerar filerna efter att noden har startats, men innan aktiviteter schemaläggs. Det är vanligt att använda en startuppgiftskommandorad tillsammans med programpaket. Om du till exempel vill kopiera filer till en annan plats eller köra installationen.

Egenskapen [nodfel](/rest/api/batchservice/computenode/get#computenodeerror) rapporterar ett misslyckande med att hämta och avkomprimera ett programpaket. nodtillståndet är inställt på **ouppnämbar**.

### <a name="container-download-failure"></a>Hämtningsfel för behållare

Du kan ange en eller flera behållarreferenser på en pool. Batch hämtar de angivna behållarna till varje nod. Egenskapen [nodfel](/rest/api/batchservice/computenode/get#computenodeerror) rapporterar ett misslyckande med att hämta en behållare och anger nodtillståndet till **ouppnämbar**.

### <a name="node-in-unusable-state"></a>Nod i outbyggbart tillstånd

Azure Batch kan ange [nodtillståndet](/rest/api/batchservice/computenode/get#computenodestate) till **obyggbart** av många skäl. När nodtillståndet är inställt på **oåtersättligt**kan aktiviteter inte schemaläggas till noden, men det medför fortfarande avgifter.

Noder i ett **oanvändbart** tillstånd, men utan [fel](/rest/api/batchservice/computenode/get#computenodeerror) innebär att Batch inte kan kommunicera med den virtuella datorn. I det här fallet försöker Batch alltid återställa den virtuella datorn. Batch kommer inte automatiskt att försöka återställa virtuella datorer som inte kunde installera programpaket eller behållare trots att deras tillstånd är **oanvändbart**.

Om Batch kan fastställa orsaken rapporterar [egenskapen](/rest/api/batchservice/computenode/get#computenodeerror) nod egenskapen den.

Ytterligare exempel på orsaker till **ouppnämbara** noder är:

- En anpassad VM-avbildning är ogiltig. Till exempel en bild som inte är korrekt förberedd.

- En virtuell dator flyttas på grund av ett infrastrukturfel eller en uppgradering på låg nivå. Batch återställer noden.

- En VM-avbildning har distribuerats på maskinvara som inte stöder den. Försöker till exempel köra en CentOS HPC-avbildning på en [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) virtuell dator.

- De virtuella datorerna finns i ett [virtuellt Azure-nätverk](batch-virtual-network.md)och trafiken har blockerats till viktiga portar.

- De virtuella datorerna finns i ett virtuellt nätverk, men utgående trafik till Azure-lagring är blockerad.

- De virtuella datorerna finns i ett virtuellt nätverk med en kund-DNS-konfiguration och DNS-servern kan inte lösa Azure-lagring.

### <a name="node-agent-log-files"></a>Loggfiler för nodagent

Batch-agentprocessen som körs på varje poolnod kan tillhandahålla loggfiler som kan vara användbara om du behöver kontakta support om ett poolnodproblem. Loggfiler för en nod kan överföras via Azure-portalen, Batch Explorer eller ett [API](/rest/api/batchservice/computenode/uploadbatchservicelogs). Det är användbart att ladda upp och spara loggfilerna. Därefter kan du ta bort noden eller poolen för att spara kostnaden för de löpnoderna.

### <a name="node-disk-full"></a>Noddisken är full

Den tillfälliga enheten för en virtuell poolnod används av Batch för jobbfiler, uppgiftsfiler och delade filer.

- Programpaketfiler
- Aktivitetsresursfiler
- Programspecifika filer som hämtats till en av batchmapparna
- Stdout- och stderr-filer för varje körning av aktivitetsprogram
- Programspecifika utdatafiler

Vissa av dessa filer skrivs bara en gång när poolnoder skapas, till exempel poolprogrampaket eller poolstarta aktivitetsresursfiler. Även om bara skriven en gång när noden skapas, om dessa filer är för stora de kunde fylla den tillfälliga enheten.

Andra filer skrivs ut för varje aktivitet som körs på en nod, till exempel stdout och stderr. Om ett stort antal aktiviteter körs på samma nod och/eller aktivitetsfilerna är för stora kan de fylla den tillfälliga enheten.

Storleken på den tillfälliga enheten beror på den virtuella datorns storlek. En faktor när du väljer en vm-storlek är att se till att den tillfälliga enheten har tillräckligt med utrymme.

- I Azure-portalen när du lägger till en pool kan den fullständiga listan över VM-storlekar visas och det finns en kolumn för resursdiskstorlek.
- De artiklar som beskriver alla vm-storlekar har tabeller med kolumnen "Temp Storage". till exempel [beräkningsoptimerade VM-storlekar](/azure/virtual-machines/windows/sizes-compute)

För filer som skrivs ut av varje uppgift kan en kvarhållningstid anges för varje aktivitet som avgör hur länge aktivitetsfilerna sparas innan de rensas automatiskt. Kvarhållningstiden kan minskas för att sänka lagringskraven.


Om den tillfälliga disken tar på utrymme (eller är mycket nära att ta på utrymme), kommer noden att flytta till [Oanvändarbart](/rest/api/batchservice/computenode/get#computenodestate) tillstånd och ett nodfel rapporteras som säger att disken är full.

### <a name="what-to-do-when-a-disk-is-full"></a>Så här gör du när en disk är full

Ta reda på varför disken är full: Om du inte är säker på vad som tar upp utrymme på noden, rekommenderas att fjärr till noden och undersöka manuellt var utrymmet har tagit vägen. Du kan också använda [API:et för batchlistefiler](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode) för att granska filer i batchhanterade mappar (till exempel aktivitetsutdata). Observera att det här API:et endast listar filer i batchhanterade kataloger och om dina uppgifter skapade filer någon annanstans kommer du inte att se dem.

Kontrollera att alla data du behöver har hämtats från noden eller överförts till ett varaktigt arkiv. Alla mildrande av disken-full problemet innebär att ta bort data för att frigöra utrymme.

### <a name="recovering-the-node"></a>Återställa noden

1. Om poolen är en [C.loudServiceConfiguration-pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) kan du avbilda noden igen via [batch-api:et för omavbildning](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage). Detta kommer att rengöra hela disken. Omavbildning stöds för närvarande inte för [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration) pooler.

2. Om poolen är en [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)kan du ta bort noden från poolen med hjälp av [API:et för ta bort noder](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes). Sedan kan du odla poolen igen för att ersätta den dåliga noden med en ny.

3.  Ta bort gamla slutförda jobb eller gamla slutförda aktiviteter vars aktivitetsdata fortfarande finns på noderna. Om du vill ha en ledtråd om vilka jobb/uppgifter som finns på noderna kan du titta i [samlingen RecentTasks](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation) på noden eller på [filerna på noden](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode). Om du tar bort jobbet tas alla aktiviteter i jobbet bort, och om du tar bort aktiviteterna i jobbet utlöses data i aktivitetskatalogerna på den nod som ska tas bort, vilket frigör utrymme. När du har frigjort tillräckligt med utrymme, starta om noden och det bör flytta ut ur "Oanvändarbart" tillstånd och till "Inaktiv" igen.

## <a name="next-steps"></a>Nästa steg

Kontrollera att du har ställt in programmet så att det implementerar omfattande felkontroll, särskilt för asynkrona åtgärder. Det kan vara viktigt att snabbt upptäcka och diagnostisera problem.
