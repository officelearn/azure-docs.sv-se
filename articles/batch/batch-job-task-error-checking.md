---
title: Sök efter jobb-och aktivitets fel – Azure Batch | Microsoft Docs
description: Fel vid sökning efter och fel sökning av jobb och uppgifter
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 12/01/2019
ms.author: markscu
ms.openlocfilehash: c4e36d76bf85b9715a817dbeb7c690aa77f8d978
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852190"
---
# <a name="job-and-task-error-checking"></a>Fel kontroll av jobb och aktivitet

Det finns olika fel som kan uppstå när du lägger till jobb och uppgifter. Det är enkelt att identifiera felen för dessa åtgärder eftersom eventuella problem returneras direkt av API, CLI eller gränssnitt.  Det finns dock problem som kan uppstå senare när jobb och aktiviteter schemaläggs och körs.

Den här artikeln beskriver de fel som kan uppstå när jobb och uppgifter har skickats. Den visar och förklarar de fel som måste kontrol leras och hanteras.

## <a name="jobs"></a>Jobb

Ett jobb är en gruppering av en eller flera aktiviteter, de uppgifter som faktiskt anger vilka kommando rader som ska köras.

När du lägger till ett jobb kan du ange följande parametrar som kan påverka hur jobbet kan gå sönder:

- [Jobb begränsningar](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - Du kan också ange egenskapen `maxWallClockTime` för att ange den maximala tid som ett jobb kan vara aktivt eller körs. Om det överskrids avbryts jobbet med egenskapen `terminateReason` som angetts i [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) för jobbet.
- [Uppgift för jobb förberedelse](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Om det här alternativet anges körs en jobb förberedelse aktivitet första gången en aktivitet körs för ett jobb på en nod. Uppgiften att förbereda jobbet kan Miss klar, vilket leder till att aktiviteten inte körs och att jobbet inte slutförs.
- [Jobb publicerings aktivitet](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - En jobb publicerings aktivitet kan bara anges om en jobb förberedelse aktivitet har kon figurer ATS. När ett jobb avslutas körs jobb publicerings aktiviteten på varje pool där en jobb förberedelse aktivitet kördes. Det går inte att utföra en jobb publicerings uppgift, men jobbet fortsätter att flyttas till ett `completed` tillstånd.

### <a name="job-properties"></a>Jobb egenskaper

Följande jobb egenskaper bör kontrol leras för fel:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - Egenskapen `terminateReason` kan ha värden som anger att `maxWallClockTime`, som anges i jobb begränsningarna, överskreds och att jobbet avbröts. Den kan också anges för att indikera att en uppgift misslyckades om jobb `onTaskFailure`s egenskapen har angetts korrekt.
  - Egenskapen [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) anges om det har uppstått ett schemaläggnings fel.
 
### <a name="job-preparation-tasks"></a>Jobb förberedelse aktiviteter

Om en jobb förberedelse uppgift har angetts för ett jobb körs en instans av den aktiviteten första gången en aktivitet för jobbet körs på en nod. Jobb förberedelse aktiviteten som kon figurer ATS för jobbet kan ses som en uppgiftsmall, med att flera uppgifts instanser för jobb förberedelse körs, upp till antalet noder i en pool.

Uppgifts instanserna för jobb förberedelsen bör kontrol leras för att avgöra om det finns fel:
- När en jobb förberedelse aktivitet körs, flyttas aktiviteten som utlöste aktiviteten jobb till ett [tillstånd](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) med `preparing`. om jobb förberedelse aktiviteten Miss lyckas återgår den Utlös ande aktiviteten till `active` tillstånd och körs inte.  
- Alla instanser av uppgiften för jobb förberedelse som har körts kan hämtas från jobbet med hjälp av API: et för [förberedelse och publicering av uppgifts status](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Precis som med alla uppgifter finns det [körnings information](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) som är tillgänglig med egenskaper som `failureInfo`, `exitCode`och `result`.
- Om jobb förberedelse aktiviteterna Miss lyckas körs inte jobb uppgifter som utlöses, jobbet kommer inte att slutföras och kommer att fastna. Poolen kan komma att användas om det inte finns några andra jobb med aktiviteter som kan schemaläggas.

### <a name="job-release-tasks"></a>Jobb publicerings aktiviteter

Om en jobb publicerings uppgift har angetts för ett jobb körs en instans av jobb publicerings aktiviteten när ett jobb avbryts, och en instans av jobb lanserings aktiviteten körs på varje pool där jobb förberedelse aktiviteten kördes.  Aktivitets instanserna för jobb lansering bör kontrol leras för att avgöra om det finns fel:
- Alla instanser av jobb publicerings aktiviteten som körs kan hämtas från jobbet med hjälp av [aktivitets status för förberedelse och publicering](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)av API-lista. Precis som med alla uppgifter finns det [körnings information](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) som är tillgänglig med egenskaper som `failureInfo`, `exitCode`och `result`.
- Om en eller flera jobb lanserings aktiviteter Miss Miss kan jobbet fortfarande avslutas och flyttas till ett `completed` tillstånd.

## <a name="tasks"></a>Uppgifter

Jobb aktiviteter kan inte utföras av flera orsaker:

- Aktivitetens kommando rad Miss lyckas, returnerar en slutkod som inte är noll.
- Det finns `resourceFiles` angivna för en aktivitet, men det uppstod ett problem som avsåg en eller flera filer som inte hämtades.
- Det finns `outputFiles` angivna för en aktivitet, men det uppstod ett problem som innebar att en eller flera filer inte överfördes.
- Den förflutna tiden för aktiviteten, som anges av egenskapen `maxWallClockTime` i aktivitets [begränsningarna](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints), överskreds.

I samtliga fall måste följande egenskaper kontrol leras för fel och information om felen:
- Egenskapen tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) innehåller flera egenskaper som innehåller information om ett fel. [resultatet](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) visar om uppgiften misslyckades av någon anledning, med `exitCode` och `failureInfo` mer information om fel.
- Aktiviteten kommer alltid att flyttas till `completed` [tillstånd](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate), oberoende av om den lyckades eller misslyckades.

Effekten av aktivitets haverier på jobbet och eventuella aktivitets beroenden måste beaktas.  Egenskapen [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) kan anges för en uppgift för att konfigurera en åtgärd för beroenden och för jobbet.
- För-beroenden kontrollerar [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) om aktiviteterna som är beroende av den misslyckade aktiviteten är blockerade eller körs.
- För jobbet styr [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) om den misslyckade aktiviteten leder till jobbet som inaktive ras, avslutas eller lämnas oförändrat.

## <a name="next-steps"></a>Nästa steg

Kontrol lera att ditt program implementerar omfattande fel kontroll. Det kan vara viktigt att snabbt identifiera och diagnostisera problem.
