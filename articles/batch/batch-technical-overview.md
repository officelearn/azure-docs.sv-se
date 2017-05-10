---
title: "Azure Batch kör storskaliga parallella beräkningslösningar i molnet | Microsoft Docs"
description: "Lär dig mer om hur du använder Azure Batch-tjänsten för storskaliga parallella arbetsbelastningar och HPC-arbetsbelastningar"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 8a850f8b1cccda631786f89f2c88647604fba9ed
ms.contentlocale: sv-se
ms.lasthandoff: 03/15/2017


---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Köra parallella arbetsbelastningar med Batch

Azure Batch är en plattformstjänst för att effektivt köra storskaliga parallella program och HPC-program (databehandling med höga prestanda) i molnet. Azure Batch schemalägger beräkningsintensivt arbete för körning i en hanterad samling virtuella datorer. Tjänsten kan automatiskt skala beräkningsresurser efter jobbens behov.

Med Azure Batch kan du enkelt definiera Azure-beräkningsresurser för att köra dina program parallellt och i stor skala. Det finns inget behov av att manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk eller en komplex infrastruktur för jobb- och aktivitetsschemaläggning. Azure Batch automatiserar eller förenklar dessa uppgifter åt dig.

## <a name="use-cases-for-batch"></a>Användningsfall för Batch
Batch är en hanterad Azure-tjänst som används för *batchbearbetning* eller *batchbehandling* – dvs. körning av ett stort antal liknande uppgifter för ett visst önskat resultat. Batchbehandling används framför allt av organisationer som regelbundet bearbetar, omvandlar och analyserar stora mängder data.

Batch fungerar bra med parallella program och arbetsbelastningar. Parallella arbetsbelastningar är sådana som lätt delas in i flera aktiviteter som utför arbete samtidigt på flera datorer.

![Parallella aktiviteter][1]<br/>

Några exempel på arbetsbelastningar som vanligtvis bearbetas med den här tekniken är:

* Finansiell riskmodellering.
* Analys av klimat- och hydrologidata
* Rendering, analys och bearbetning av bilder
* Mediekodning och transkodning
* Genetisk sekvensanalys
* Analyser av teknisk spänning
* Programvarutestning

Batch kan också utföra parallella beräkningar med ett reduceringssteg i slutet, samt köra mer komplexa HPC-arbetsbelastningar som [MPI-program (Message Passing Interface)](batch-mpi.md).

En jämförelse mellan Batch och andra HPC-lösningsalternativ i Azure finns i [Batch- och HPC-lösningar](batch-hpc-solutions.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Scenario: Skala ut en parallell arbetsbelastning
En vanlig lösning som använder Batch-API:erna för att interagera med Batch-tjänsten är att skala ut parallellt arbete – till exempel renderingen av bilder i 3D-scener – i en pool med beräkningsnoder. Den här poolen med beräkningsnoder kan vara din ”renderingsservergrupp” som till exempel tillhandahåller flera hundratals eller flera tusen kärnor till ditt återgivningsjobb.

Följande diagram illustrerar ett vanligt Batch-arbetsflöde med ett klientprogram eller en värdbaserad tjänst som använder Batch för att köra en parallell arbetsbelastning.

![Arbetsflöde för Batch-lösning][2]

I det här vanliga scenariot bearbetar programmet eller tjänsten en beräkningsarbetsbelastning i Azure Batch genom att utföra följande steg:

1. Ladda upp **indatafilerna** och **programmen** som ska bearbeta dessa filer till ditt Azure Storage-konto. Indatafilerna kan vara data som ska bearbetas i ditt program, till exempel modellering av finansdata eller videofiler som ska kodas. Programfilerna kan vara program som används för databearbetning, till exempel ett program för 3D-rendering eller ett mediekodningsprogram.
2. Skapa en Batch-**pool** med beräkningsnoder i ditt Batch-konto. Dessa noder är de virtuella datorer som ska köra dina uppgifter. Du kan ange egenskaper som [nodstorlek](../cloud-services/cloud-services-sizes-specs.md), operativsystem och platsen i Azure Storage för programmet som ska installeras när noderna ansluter till poolen (det program som du överförde i steg 1). Du kan också konfigurera poolen så att den [skalas automatiskt](batch-automatic-scaling.md) baserat på arbetsbelastningen som dina aktiviteter genererar. Automatisk skalning justerar antalet datornoder i poolen dynamiskt.
3. Skapa ett Batch-**jobb** som ska köra arbetsbelastningen i poolen med beräkningsnoder. När du skapar ett jobb kan du associera det med en Batch-pool.
4. Lägg till **aktiviteter** till jobbet. När du lägger till aktiviteter till ett jobb schemalägger Batch-tjänsten automatiskt aktiviteterna för körning på beräkningsnoderna i poolen. Varje aktivitet använder det program som du överförde för att bearbeta indatafilerna.
   
   * 4a. Innan en aktivitet körs kan den hämta data (indatafilerna) som den ska bearbeta till den beräkningsnod som den är associerad med. Om programmet inte redan har installerats på noden (se steg 2) kan det hämtas här i stället. När nedladdningarna har slutförts körs aktiviteterna på deras tilldelade noder.
5. När aktiviteterna utförs kan du köra frågor mot Batch för att övervaka jobbets och aktiviteternas förlopp. Ditt klientprogram eller -tjänst kommunicerar med Batch-tjänsten över HTTPS. Eftersom du kan övervaka tusentals aktiviteter som körs på tusentals beräkningsnoder, se till att [effektivt fråga Batch-tjänsten](batch-efficient-list-queries.md).
6. När aktiviteterna slutförs kan de ladda upp resultatdata till Azure Storage. Du kan också hämta filer direkt från filsystemet på en beräkningsnod.
7. När övervakningen upptäcker att aktiviteterna i jobbet har slutförts kan klientprogrammet eller tjänsten hämta utdata för vidare bearbetning eller utvärdering.

Tänk på att det här bara är ett av många sätt att använda Batch på, och att det här scenariot bara beskriver några få av alla de tillgängliga funktionerna. Du kan till exempel köra [flera aktiviteter parallellt](batch-parallel-node-tasks.md) på varje beräkningsnod och du kan [köra särskilda aktiviteter](batch-job-prep-release.md) som förbereder noderna för jobben och som rensar upp när allt är klart.

## <a name="next-steps"></a>Nästa steg
Nu när du har en högnivåöversikt över Batch-tjänsten är det dags att gå vidare och se hur du kan använda den för att bearbeta dina beräkningsintensiva parallella arbetsbelastningar.

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
* [Komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md) innehåller information om hur du använder C# och Batch .NET-biblioteket för att köra en enkel arbetsbelastning med ett vanligt Batch-arbetsflöde. Den här artikeln bör vara en av dina självklara utgångspunkter när du lär dig hur man använder Batch-tjänsten. Det finns även en [Python-version](batch-python-tutorial.md) av självstudien.
* Hämta [kodexemplet på GitHub][github_samples] om du vill se hur både C# och Python kan användas med Batch för att schemalägga och bearbeta exempelarbetsbelastningar.
* Ta en titt på [utbildningsvägen för Batch][learning_path] om du vill veta mer om de tillgängliga resurserna när du lär dig hur du arbetar med Batch.


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

