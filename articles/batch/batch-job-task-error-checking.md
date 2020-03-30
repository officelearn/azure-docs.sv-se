---
title: Sök efter jobb- och aktivitetsfel - Azure Batch | Microsoft-dokument
description: Fel för att söka efter och felsöka jobb och uppgifter
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087017"
---
# <a name="job-and-task-error-checking"></a>Jobb- och uppgiftsfelkontroll

Det finns olika fel som kan uppstå när du lägger till jobb och aktiviteter. Det är enkelt att identifiera fel för dessa åtgärder eftersom eventuella fel returneras omedelbart av API, CLI eller UI.  Det finns dock fel som kan inträffa senare när jobb och aktiviteter schemaläggs och körs.

Den här artikeln beskriver de fel som kan uppstå när jobb och aktiviteter har skickats. Den listar och förklarar de fel som måste kontrolleras och hanteras.

## <a name="jobs"></a>Jobb

Ett jobb är en gruppering av en eller flera aktiviteter, de aktiviteter som faktiskt anger de kommandorader som ska köras.

När du lägger till ett jobb kan följande parametrar anges som kan påverka hur jobbet kan misslyckas:

- [Jobbbegränsningar](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Egenskapen `maxWallClockTime` kan eventuellt anges för att ange den maximala tid ett jobb kan vara aktivt eller körs. Om det överskrids avslutas jobbet `terminateReason` med egenskapen som anges i [körningenInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) för jobbet.
- [Uppgift om förberedelse av arbete](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Om det anges körs en jobbförberedelseaktivitet första gången en aktivitet körs för ett jobb på en nod. Jobbförberedelseaktiviteten kan misslyckas, vilket leder till att aktiviteten inte körs och jobbet inte slutförs.
- [Uppgift om projektfrisläppning](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - En jobbfrisättningsaktivitet kan bara anges om en jobbförberedelseaktivitet har konfigurerats. När ett jobb avslutas körs jobbfrisläppningsaktiviteten på var och en av poolnoderna där en jobbförberedelseaktivitet kördes. En jobbfrisättningsaktivitet kan misslyckas, men `completed` jobbet flyttas fortfarande till ett tillstånd.

### <a name="job-properties"></a>Egenskaper för jobb

Följande jobbegenskaper bör kontrolleras för fel:

- [" utförandeInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)":
  - Egenskapen `terminateReason` kan ha värden `maxWallClockTime`som anger att den , som anges i jobbbegränsningarna, har överskridits och därför avslutades jobbet. Den kan också ställas in för att `onTaskFailure` ange att en aktivitet misslyckades om jobbegenskapen har angetts på rätt sätt.
  - Egenskapen [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) anges om det har förekommit ett schemaläggningsfel.
 
### <a name="job-preparation-tasks"></a>Jobbförberedelseuppgifter

Om en jobbförberedelseaktivitet har angetts för ett projekt körs en instans av aktiviteten första gången en aktivitet för jobbet körs på en nod. Den jobbförberedelseuppgift som konfigurerats för jobbet kan ses som en aktivitetsmall, med flera uppgiftsinstanser för jobbförberedelser som körs, upp till antalet noder i en pool.

Uppgiftsinstanserna för jobbförberedelser bör kontrolleras för att avgöra om det fanns fel:
- När en jobbförberedelseaktivitet körs flyttas aktiviteten som utlöste jobbförberedelseaktiviteten till ett [tillstånd](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) av `preparing`; Om jobbförberedelseaktiviteten sedan misslyckas återgår `active` den utlösande aktiviteten till tillståndet och körs inte.  
- Alla instanser av jobbförberedelseaktiviteten som har körts kan erhållas från jobbet med [api:et för förberedelse och frisläppning](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) av aktivitet. Precis som med alla uppgifter finns det `failureInfo` [körningsinformation](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) tillgänglig med egenskaper som , `exitCode`och `result`.
- Om jobbförberedelseaktiviteter misslyckas kommer de utlösande jobbaktiviteterna inte att köras, jobbet slutförs inte och kommer att fastna. Poolen kan gå outnyttjad om det inte finns några andra jobb med aktiviteter som kan schemaläggas.

### <a name="job-release-tasks"></a>Jobbutgåva uppgifter

Om en jobbfrisättningsaktivitet har angetts för ett jobb körs en instans av jobbfrisättningsaktiviteten när ett jobb avslutas på var och en av poolnoderna där en jobbförberedelseaktivitet kördes.  Uppgiftsinstanserna för jobbrelease bör kontrolleras för att avgöra om det fanns fel:
- Alla instanser av jobbfrisättningsaktiviteten som körs kan erhållas från jobbet med hjälp av [API-listförberedelse och utgivningsaktivitetsstatus](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus). Precis som med alla uppgifter finns det `failureInfo` [körningsinformation](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) tillgänglig med egenskaper som , `exitCode`och `result`.
- Om en eller flera jobbfrigivningsaktiviteter misslyckas kommer jobbet `completed` fortfarande att avslutas och flyttas till ett tillstånd.

## <a name="tasks"></a>Aktiviteter

Jobbaktiviteter kan misslyckas av flera skäl:

- Aktivitetskommandoraden misslyckas och returneras med en utgångskod som inte är noll.
- Det `resourceFiles` har angetts för en aktivitet, men det uppstod ett fel som innebar att en eller flera filer inte hämtades.
- Det `outputFiles` har angetts för en aktivitet, men det uppstod ett fel som innebar att en eller flera filer inte laddades upp.
- Den förflutna tiden för aktiviteten, som anges `maxWallClockTime` av egenskapen i [aktivitetsbegränsningarna,](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)har överskridits.

I samtliga fall måste följande egenskaper kontrolleras för fel och information om felen:
- Egenskapen tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) innehåller flera egenskaper som innehåller information om ett fel. [resultatet](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) anger om aktiviteten misslyckades av `exitCode` någon `failureInfo` anledning, med och ger mer information om felet.
- Aktiviteten flyttas alltid till `completed` [staten](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), oberoende av om den lyckades eller misslyckades.

Effekten av aktivitetsfel på jobbet och eventuella aktivitetssamband måste beaktas.  Egenskapen [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) kan anges för en aktivitet för att konfigurera en åtgärd för samband och för jobbet.
- För samband styr [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) om de aktiviteter som är beroende av den misslyckade aktiviteten blockeras eller körs.
- För jobbet styr [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) om den misslyckade aktiviteten leder till att jobbet inaktiveras, avslutas eller lämnas oförändrat.

### <a name="task-command-line-failures"></a>Fel på aktivitetskommandoraden

När aktivitetskommandoraden körs skrivs `stderr.txt` `stdout.txt`utdata till och . Dessutom kan programmet skriva till programspecifika loggfiler.

Om den poolnod som en aktivitet har körts kvar finns kan loggfilerna hämtas och visas. Azure-portalen listar och kan till exempel visa loggfiler för en uppgift eller en poolnod. Flera API:er gör det också möjligt att lista och hämta aktivitetsfiler, till exempel [Hämta från uppgift](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

På grund av att pooler och poolnoder ofta är efemära, med noder som kontinuerligt läggs till och tas bort, rekommenderas att loggfiler sparas. [Uppgiftsutdatafiler](https://docs.microsoft.com/azure/batch/batch-task-output-files) är ett praktiskt sätt att spara loggfiler i Azure Storage.

### <a name="output-file-failures"></a>Fel i utdatafilen
På varje filöverföring skriver Batch två loggfiler till `fileuploadout.txt` `fileuploaderr.txt`beräkningsnoden och . Du kan undersöka dessa loggfiler om du vill veta mer om ett specifikt fel. I de fall där filöverföringen aldrig gjordes, till exempel på grund av att själva uppgiften inte kunde köras, kommer dessa loggfiler inte att finnas.  

## <a name="next-steps"></a>Nästa steg

Kontrollera att ditt program implementerar omfattande felkontroll. Det kan vara viktigt att snabbt upptäcka och diagnostisera problem.
