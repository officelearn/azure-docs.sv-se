---
title: Skapa Batch-lösningar med Visual Studio - mallar i Azure | Microsoft Docs
description: Lär dig mer om Visual Studio-projektmallar hur du kan implementera och köra din beräkningsintensiva arbetsbelastningar i Azure Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5241c62e8b423b20477fc72c87303daf3d4ab43c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316757"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Använd Visual Studio-projektmallar att Batch lösningar

Den **Jobbhanteraren** och **aktivitet Processor Visual Studio-mallar** Batch ange i koden som hjälper dig att implementera och köra beräkningsintensiva arbetsbelastningar på Batch med minsta möjliga ansträngning. Det här dokumentet beskriver dessa mallar och vägledning för hur de används.

> [!IMPORTANT]
> Den här artikeln beskrivs bara information som gäller för dessa två mallar och förutsätter att du är bekant med Batch-tjänsten och viktiga begrepp som är relaterade till den: pooler, compute-noder, jobb och uppgifter, job manager-uppgifter, miljövariabler och annan relevant information. Du hittar mer information finns i [grunderna i Azure Batch](batch-technical-overview.md), [Batch funktionsöversikt för utvecklare](batch-api-basics.md), och [komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Översikt på hög nivå
Job Manager och uppgiften Processor mallar kan användas för att skapa två användbara komponenter:

* En manager projektaktivitet som implementerar en förgrening för jobbet som kan dela ett jobb upp på flera aktiviteter som kan köras fristående parallellt.
* En uppgift processor som kan användas för att utföra förbearbetning och efterbearbetning runt en kommandorad för programmet.

Till exempel i ett scenario med film återgivning skulle delningslisten jobbet göra en enda film jobbet till hundratals eller tusentals olika uppgifter som behandlar enskilda ramar separat. På motsvarande sätt uppgiftsprocessorn skulle starta återgivning programmet och alla beroende processer som krävs för att återge varje ram, samt utföra eventuella ytterligare åtgärder (till exempel kopiera den återgivna ramen till en lagringsplats).

> [!NOTE]
> Job Manager och uppgiften Processor mallarna är oberoende av varandra, så du kan välja att använda både eller endast en av dem, beroende på kraven för din beräknings-jobb och på dina inställningar.
> 
> 

I diagrammet nedan visas ett jobb för beräkning som använder de här mallarna går igenom tre steg:

1. Klientkoden (t.ex. program, webbtjänsten, etc.) skickar ett jobb till Batch-tjänsten i Azure, ange som dess jobbhanteraren uppgift hanteringsprogram jobb.
2. Batch-tjänsten körs projektaktiviteten manager på en beräkningsnod och delningslisten jobbet startar det angivna antalet uppgiften processor uppgifter på som många beräkningsnoderna som krävs, baserat på de parametrar och specifikationer i jobbet delningslisten koden.
3. Uppgiften processor uppgifterna köras fristående parallellt, att behandla indata och generera utdata.

![Diagram över hur klientkod samverkar med Batch-tjänsten][diagram01]

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda Batch-mallar, behöver du följande:

* En dator med Visual Studio 2015 installerat. Batch-mallar är för närvarande stöds endast för Visual Studio 2015.
* Batch-mallar, som är tillgängliga från den [Visual Studio-galleriet] [ vs_gallery] som Visual Studio-tillägg. Det finns två sätt att hämta mallar:
  
  * Installera mallar med hjälp av den **tillägg och uppdateringar** dialogrutan i Visual Studio (Mer information finns i [söka och med hjälp av Visual Studio-tillägg][vs_find_use_ext]). I den **tillägg och uppdateringar** dialogrutan rutan, söka och hämta följande två tillägg:
    
    * Azure Batch Job Manager med jobbet delningslisten
    * Azure Batch uppgiften Processor
  * Hämta mallar från galleriet online för Visual Studio: [projektmallar för Microsoft Azure Batch][vs_gallery_templates]
* Om du planerar att använda den [programpaket](batch-application-packages.md) funktion för att distribuera jobbhanteraren och uppgiften processor i gruppen compute-noder måste du länka ett lagringskonto till Batch-kontot.

## <a name="preparation"></a>Förberedelse
Vi rekommenderar att du skapar en lösning som kan innehålla din jobbhanteraren samt aktivitet processorn, eftersom detta kan göra det enklare att dela koden mellan jobbhanteraren och uppgiften processor program. Följ dessa steg om du vill skapa den här lösningen:

1. Öppna Visual Studio och välj **filen** > **ny** > **projekt**.
2. Under **mallar**, expandera **andra projekttyper**, klickar du på **Visual Studio-lösningar**, och välj sedan **tomt lösning**.
3. Ange ett namn som beskriver ditt program och syftet med den här lösningen (t.ex. ”LitwareBatchTaskPrograms”).
4. Klicka för att skapa den nya lösningen **OK**.

## <a name="job-manager-template"></a>Job Manager-mall
Mallen Job Manager hjälper dig att implementera en projektaktivitet manager som kan utföra följande åtgärder:

* Dela upp ett jobb i flera uppgifter.
* Skicka dessa aktiviteter ska köras på Batch.

> [!NOTE]
> Läs mer om jobb manager uppgifter [Batch funktionsöversikt för utvecklare](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Skapa en Job Manager med hjälp av mallen
Följ dessa steg om du vill lägga till en jobbhanteraren i lösningen som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer, klicka på **Lägg till** > **nytt projekt**.
3. Under **Visual C#**, klickar du på **moln**, och klicka sedan på **Azure Batch Job Manager med jobbet delningslisten**.
4. Ange ett namn som beskriver ditt program och identifierar det här projektet som jobbhanteraren (t.ex.) "LitwareJobManager").
5. Klicka för att skapa projektet **OK**.
6. Slutligen skapa projekt för att tvinga Visual Studio för att läsa in alla refererade NuGet-paket och kontrollera att projektet är giltigt innan du börjar att ändra den.

### <a name="job-manager-template-files-and-their-purpose"></a>Job Manager mallfilerna och deras syfte
När du skapar ett projekt med mallen Jobbhanterare genererar tre grupper av kodfiler:

* Den huvudsakliga programfilen (Program.cs). Innehåller startpunkt för programmet och översta undantagshantering. Du behöver inte normalt ändra den här.
* Framework-katalogen. Det innehåller filerna som är ansvarig för 'formaterad' arbete hanteringsprogram jobb – uppackning parametrar, lägga till aktiviteter i Batch-jobbet, osv. Du bör inte normalt måste du ändra de här filerna.
* Jobbet delningslisten filen (JobSplitter.cs). Detta är där kommer att placeras din programspecifika logik för att dela upp ett jobb i aktiviteter.

Naturligtvis kan du lägga till ytterligare filer som behövs för att stödja jobbet delningslisten koden, beroende på komplexa jobb dela logik.

Mallen genererar också standard .NET projektfiler, till exempel en .csproj filen app.config, Packages.config-fil, osv.

Resten av det här avsnittet beskrivs de olika filerna och deras kodstruktur och förklarar varje klass har.

![Visual Studio Solution Explorer visar Jobbhanteraren mall lösning][solution_explorer01]

**Framework-filer**

* `Configuration.cs`: Kapslar in inläsningen av jobbet konfigurationsdata som Batch-kontoinformation, länkade lagringskontouppgifter, jobb och information om aktiviteter och jobbparametrar. Det ger också tillgång till Batch-definierade miljövariabler (se miljöinställningar för aktiviteter i Batch-dokumentationen) via Configuration.EnvironmentVariable-klassen.
* `IConfiguration.cs`: Avlägsnar så att du kan enhet testa dina jobb delningslisten med hjälp av ett konfigurationsobjekt för falska eller fingerad implementering av klassen Configuration.
* `JobManager.cs`: Samordnar komponenter av job manager programmet. Den ansvarar för att initiera jobb delningslisten anropar delningslisten jobb och sändning av de uppgifter som returneras av jobbet delningslisten till migreringsaktivitetens.
* `JobManagerException.cs`: Representerar ett fel som kräver job manager att avsluta. JobManagerException används för att omsluta 'förväntade' fel där specifika diagnostisk information kan anges som en del av avslutning.
* `TaskSubmitter.cs`: Den här klassen ansvarar för att lägga till aktiviteter som returneras av delningslisten jobbet i Batch-jobbet. JobManager klassen mängder aktivitetssekvensen i batchar för effektiv men rimlig lägga till projektet, anropar sedan TaskSubmitter.SubmitTasks på en bakgrundstråd för varje grupp.

**Jobbet delningslisten**

`JobSplitter.cs`: Den här klassen innehåller programspecifika logik för att dela jobbet i aktiviteter. Ramen anropar metoden JobSplitter.Split för att hämta en serie uppgifter som läggs till i jobbet eftersom metoden returnerar dem. Det här är klassen där du matar in logiken för ditt jobb. Implementera metoden delning för att returnera en sekvens av CloudTask instanser som representerar de uppgifter som du vill partitionera jobbet.

**Standard projektfiler för .NET-kommandorad**

* `App.config`: Standard .NET-programkonfigurationsfilen.
* `Packages.config`: Standard NuGet-paketet beroende fil.
* `Program.cs`: Innehåller startpunkt för programmet och översta undantagshantering.

### <a name="implementing-the-job-splitter"></a>Implementera delningslisten jobb
När du öppnar mallprojektet Job Manager har projektet JobSplitter.cs filen öppnas som standard. Du kan implementera dela logik för aktiviteter i din arbetsbelastning med hjälp av Split() metoden visas nedan:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> Avsnittet kommenterade i den `Split()` metoden är endast avsnitt av Job Manager mall-koden som är avsedd att ändra genom att lägga till logiken som att dela dina jobb i olika uppgifter. Om du vill ändra ett annat avsnitt i mallen, kontrollera att du har bekantat med hur Batch fungerar och prova att använda några av de [Batch-kodexempel][github_samples].
> 
> 

Implementeringen Split() har åtkomst till:

* Jobbparametrarna via den `_parameters` fältet.
* CloudJob-objektet som representerar jobbet, via den `_job` fältet.
* CloudTask-objektet som representerar manager projektaktivitet den `_jobManagerTask` fältet.

Din `Split()` implementeringen inte behöver lägga till uppgifter i jobbet direkt. I stället koden ska returnera en sekvens av CloudTask objekt och dessa kommer att läggas till jobbet automatiskt av framework-klasser som anropar delningslisten jobb. Det är vanligt att använda C# 's iterator (`yield return`) att implementera jobbet delarna som detta gör att aktiviteter att starta körs så snart som möjligt, i stället för att väntar på att alla aktiviteter ska beräknas.

**Jobbet delningslisten misslyckades**

Om jobbet-delningslisten påträffar ett fel, bör det antingen:

* Avsluta sekvensen med C# `yield break` instruktionen, i vilket fall jobbhanteraren behandlas som slutförd; eller
* Utlös ett undantag i vilket fall jobbhanteraren behandlas som misslyckad och kan göras beroende på hur klienten har konfigurerats).

I båda fallen blir alla uppgifter redan returnerades av jobbet delningslisten och läggs till i Batch-jobbet kan köras. Klicka om du vill att detta ska hända, kan du:

* Avsluta jobbet innan man returneras från jobbet delningslisten
* Formulera hela aktiviteten till samlingen innan det returneras (det vill säga returnera en `ICollection<CloudTask>` eller `IList<CloudTask>` i stället för att implementera din jobbet delningslisten med C#-iterator)
* Använd aktiviteten beroenden så att alla aktiviteter som beror på slutförande av jobbhanteraren

**Jobbet manager återförsök**

Om jobbhanteraren misslyckas, kan du göra det av Batch-tjänsten beroende på försök igen klientinställningar. Detta är generellt sett är säkra, eftersom när ramen lägger till uppgifter i jobbet, ignoreras alla aktiviteter som redan finns. Men om det är dyrt beräkning av aktiviteter, kanske du inte vill innebära kostnaden för att beräkna aktiviteter som redan har lagts till jobb. däremot om kör igen inte är säkert att generera samma uppgift ID kommer beteendet 'Ignorera dubbletterna' inte koppla. I dessa fall bör du utforma din jobbet delningslisten att identifiera det arbete som redan har utförts och inte upprepa den, till exempel genom att utföra en CloudJob.ListTasks innan du börjar ge uppgifter.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Slutkoder och undantag i mallen Job Manager
Slutkoder och undantag tillhandahåller en mekanism för att fastställa resultatet av att köra ett program och de hjälper dig för att identifiera eventuella problem med körningen av programmet. Mallen Job Manager implementerar slutkoder och undantag som beskrivs i det här avsnittet.

En projektaktivitet manager som implementeras med hjälp av Job Manager-mallen returnerar tre möjliga slutkoder:

| Kod | Beskrivning |
| --- | --- |
| 0 |Jobbhanteraren för har slutförts. Jobbet delningslisten koden kördes kan slutföras och alla aktiviteter har lagts till i jobbet. |
| 1 |Projektaktiviteten manager misslyckades med ett undantag i en '' del av programmet. Undantaget översattes till en JobManagerException med diagnostisk information och, om möjligt förslag för att lösa felet. |
| 2 |Projektaktiviteten manager misslyckades med ett 'oväntat' undantag. Undantaget loggades till standardutdata, men jobbhanteraren kunde inte lägga till ytterligare information diagnostik eller reparation. |

Om jobbet manager uppgiften misslyckades kan vissa aktiviteter fortfarande har lagts till tjänsten innan felet inträffade. Dessa aktiviteter kommer att köras som vanligt. Se ”jobbet delningslisten misslyckades” ovan beskrivning av den här kodsökvägen.

All information som returneras av undantag skrivs i stdout.txt och stderr.txt-filer. Mer information finns i [felhantering](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Överväganden för klienten
Det här avsnittet beskrivs vissa implementering klientkrav när anropar en jobbhanteraren baserat på den här mallen. Se [så att skicka parametrar och miljövariabler från klientkoden](#pass-environment-settings) information om skicka parametrar och inställningar.

**Obligatoriska autentiseringsuppgifter**

För att lägga till aktiviteter i Azure Batch-jobbet kräver manager projektaktivitet din URL: en för Azure Batch-konto och nyckel. Du måste skicka dessa i miljövariabler som heter YOUR_BATCH_URL och YOUR_BATCH_KEY. Du kan ange dem i Job Manager aktivitetsinställningar i miljön. Till exempel i C# klient:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Autentiseringsuppgifter för lagring**

Normalt behöver klienten inte ange autentiseringsuppgifter för länkad lagring att manager projektaktivitet eftersom a mest jobbet cheferna behöver inte explicit åtkomst till länkade lagringskontot och (b) det länka lagringskontot ofta har angetts för alla aktiviteter som en gemensam miljö för jobbet. Om du inte använder länkade storage-konto via vanliga miljöinställningar och jobbhanteraren kräver åtkomst till länkad lagring, ska du ange autentiseringsuppgifter för länkad lagring på följande sätt:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Jobbet manager aktivitetsinställningar**

Klienten bör ange jobbhanteraren *killJobOnCompletion* flaggan till **FALSKT**.

Det är oftast säkert för klienten att ange *runExclusive* till **FALSKT**.

Klienten bör använda den *resourceFiles* eller *applicationPackageReferences* samling jobbet manager körbara (och dess nödvändiga DLL-filer) som distribueras till compute-nod.

Som standard kommer jobbhanteraren inte göras om det misslyckas. Beroende på dina jobb manager logik klienten kanske vill aktivera återförsök via *begränsningar*/*maxTaskRetryCount*.

**Inställningar för**

Om jobbet delningslisten avger åtgärder med beroenden, måste klienten ange jobbets usesTaskDependencies till true.

Det är ovanligt för klienter för att du vill lägga till jobb utöver jobbet delningslisten skapar aktiviteter i jobbet delningslisten modellen. Klienten bör därför normalt inställt jobbets *onAllTasksComplete* till **terminatejob**.

## <a name="task-processor-template"></a>Uppgiftsmallar för Processor
En uppgift Processor-mall kan du implementera en aktivitet processor som kan utföra följande åtgärder:

* Ställ in den information som krävs av varje Batch-aktivitet ska köras.
* Kör alla åtgärder som krävs för varje Batch-aktivitet.
* Spara aktiviteten utdata beständig lagring.

Även om en aktivitet processor inte krävs för att köra uppgifter på Batch, är viktiga fördelen med att använda en aktivitet processor att det tillhandahåller ett gränssnitt för att implementera alla åtgärder för körning av aktiviteten i en plats. Till exempel uppgift om du behöver köra flera program i kontexten för varje aktivitet, eller om du måste kopiera data till beständig lagring när du har slutfört varje.

De åtgärder som utförs av processor för aktiviteten kan vara som enkla eller komplexa, och många eller få som krävs av din arbetsbelastning. Dessutom genom att implementera alla-åtgärder i en aktivitet processor, kan du enkelt kan uppdatera eller lägga till åtgärder baserat på ändringar av program eller arbetsbelastningskraven. Men i vissa fall kanske en uppgift processor inte den bästa lösningen för din implementering som den kan lägga till onödiga komplexitet, till exempel när du kör jobb som går snabbt att starta från en enkel kommandorad.

### <a name="create-a-task-processor-using-the-template"></a>Skapa en uppgift-Processor med hjälp av mallen
Följ dessa steg om du vill lägga till en aktivitet processor i lösningen som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **nytt projekt**.
3. Under **Visual C#**, klickar du på **moln**, och klicka sedan på **Azure Batch uppgiften Processor**.
4. Ange ett namn som beskriver ditt program och identifierar det här projektet som aktiviteten-processor (t.ex.) "LitwareTaskProcessor").
5. Klicka för att skapa projektet **OK**.
6. Slutligen skapa projekt för att tvinga Visual Studio för att läsa in alla refererade NuGet-paket och kontrollera att projektet är giltigt innan du börjar att ändra den.

### <a name="task-processor-template-files-and-their-purpose"></a>Uppgiften Processor mallfilerna och deras syfte
När du skapar ett projekt med mallen uppgiften processor genererar tre grupper av kodfiler:

* Den huvudsakliga programfilen (Program.cs). Innehåller startpunkt för programmet och översta undantagshantering. Du behöver inte normalt ändra den här.
* Framework-katalogen. Det innehåller filerna som är ansvarig för 'formaterad' arbete hanteringsprogram jobb – uppackning parametrar, lägga till aktiviteter i Batch-jobbet, osv. Du bör inte normalt måste du ändra de här filerna.
* Uppgiften processor filen (TaskProcessor.cs). Detta är där kommer att placeras din programspecifika logik för att köra en aktivitet (vanligtvis genom att anropa till en befintlig körbara filen). Före och efterbearbetning kod, till exempel ladda ned ytterligare data eller överför resultat filer även här.

Naturligtvis kan du lägga till ytterligare filer som behövs för att stödja uppgiften processor koden, beroende på komplexa jobb dela logik.

Mallen genererar också standard .NET projektfiler, till exempel en .csproj filen app.config, Packages.config-fil, osv.

Resten av det här avsnittet beskrivs de olika filerna och deras kodstruktur och förklarar varje klass har.

![Visual Studio Solution Explorer visar aktivitet Processor mallen lösning][solution_explorer02]

**Framework-filer**

* `Configuration.cs`: Kapslar in inläsningen av jobbet konfigurationsdata som Batch-kontoinformation, länkade lagringskontouppgifter, jobb och information om aktiviteter och jobbparametrar. Det ger också tillgång till Batch-definierade miljövariabler (se miljöinställningar för aktiviteter i Batch-dokumentationen) via Configuration.EnvironmentVariable-klassen.
* `IConfiguration.cs`: Avlägsnar så att du kan enhet testa dina jobb delningslisten med hjälp av ett konfigurationsobjekt för falska eller fingerad implementering av klassen Configuration.
* `TaskProcessorException.cs`: Representerar ett fel som kräver job manager att avsluta. TaskProcessorException används för att omsluta 'förväntade' fel där specifika diagnostisk information kan anges som en del av avslutning.

**Uppgiften-Processor**

* `TaskProcessor.cs`: Kör aktiviteten. Ramen anropar metoden TaskProcessor.Run. Det här är klassen där du matar in programspecifika logiken för uppgiften. Implementera metoden Run till:
  * Analysera och kontrollera eventuella parametrar för aktiviteten
  * Skriv kommandoraden för några externa program som du vill anropa
  * Logga alla diagnostisk information som du kan behöva för felsökning
  * Starta en process som kommandoraden
  * Vänta tills processen för att avsluta
  * Avbilda slutkoden för processen för att avgöra om den lyckades eller misslyckades
  * Spara alla utdatafiler som du vill behålla till beständig lagring

**Standard projektfiler för .NET-kommandorad**

* `App.config`: Standard .NET-programkonfigurationsfilen.
* `Packages.config`: Standard NuGet-paketet beroende fil.
* `Program.cs`: Innehåller startpunkt för programmet och översta undantagshantering.

## <a name="implementing-the-task-processor"></a>Implementera aktivitet-processor
När du öppnar uppgiften Processor mallprojektet har projektet TaskProcessor.cs filen öppnas som standard. Du kan implementera kör logik för aktiviteter i din arbetsbelastning med hjälp av metoden Run() visas nedan:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Avsnittet kommenterade i Run()-metoden är endast avsnitt i aktiviteten Processor mall-koden som är avsedd att ändra genom att köra logik för aktiviteter i din arbetsbelastning. Om du vill ändra ett annat avsnitt på mallen du först bekanta dig med hur Batch fungerar genom att granska Batch-dokumentationen och testar en del av Batch-kodexempel.
> 
> 

Metoden Run() ansvarar för starta kommandoraden, starta en eller flera processer väntar på att alla processer ska slutföras, spara resultaten och slutligen returneras med slutkoden. Metoden Run() är där du implementera standardbearbetningslogiken för dina aktiviteter. Uppgiften processor framework anropar metoden Run(). Du behöver inte anropa den själv.

Implementeringen Run() har åtkomst till:

* Parametrarna för konsoluppgiften via den `_parameters` fältet.
* Jobb- och ID, via den `_jobId` och `_taskId` fält.
* Konfigurera aktivitet via den `_configuration` fältet.

**Aktivitet, fel**

Du kan avsluta Run()-metoden genom att ett undantag om fel uppstår, men detta lämnar den översta nivån Undantagshanteraren kontrollen över sluttid för aktiviteten. Om du vill kontrollera slutkoden så att du kan skilja mellan olika typer av fel, till exempel för att ställa diagnoser eller eftersom vissa feltillstånd bör avsluta jobbet och andra bör inte ska du avsluta Run()-metoden som returnerar slutkoden noll. Detta blir sluttid för aktiviteten.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Slutkoder och undantag i aktiviteten Processor mallen
Slutkoder och undantag tillhandahåller en mekanism för att fastställa resultatet av att köra ett program och de kan hjälpa dig identifiera eventuella problem med körning av programmet. Mallen uppgiften Processor implementerar slutkoder och undantag som beskrivs i det här avsnittet.

En uppgift processor uppgift som implementeras med hjälp av mallen för aktiviteten Processor returnerar tre möjliga slutkoder:

| Kod | Beskrivning |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Uppgiften processor körde kan slutföras. Observera att detta inte innebär att det program som du startade lyckades – endast att processorn aktiviteten anropa den och utföra eventuell efter bearbetning utan undantag. Innebörden av slutkoden beror på programmet som anropas – normalt slutkoden 0 betyder programmet lyckades och andra slutkoden programmet misslyckades. |
| 1 |Processorn aktiviteten misslyckades med ett undantag i en '' del av programmet. Undantaget översattes till en `TaskProcessorException` med diagnostisk information och, om möjligt förslag för att lösa felet. |
| 2 |Processorn aktiviteten misslyckades med ett 'oväntat-undantag. Undantaget loggades till standardutdata, men aktivitet-processorn kunde inte lägga till ytterligare diagnostik eller reparation information. |

> [!NOTE]
> Om du anropar används slutkoder 1 och 2 för att ange särskilda lägen, är sedan använda slutkoder 1 och 2 för aktiviteten processor fel tvetydig. Du kan ändra dessa felkoder för aktiviteten processor till olika koder genom att redigera ärenden undantag i filen Program.cs.
> 
> 

All information som returneras av undantag skrivs i stdout.txt och stderr.txt-filer. Mer information finns i felhantering, i Batch-dokumentationen.

### <a name="client-considerations"></a>Överväganden för klienten
**Autentiseringsuppgifter för lagring**

Om aktiviteten-processor använder Azure blob storage för att bevara utdata, till exempel med hjälp av filen konventioner hjälpbibliotek, så den behöver åtkomst till *antingen* moln lagringskontouppgifter *eller* en URL för blob-behållare som innehåller en signatur för delad åtkomst (SAS). Mallen innehåller stöd för att tillhandahålla autentiseringsuppgifter via vanliga miljövariabler. Klienten kan skicka autentiseringsuppgifterna lagring på följande sätt:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Lagringskontot är sedan tillgängliga i klassen TaskProcessor via den `_configuration.StorageAccount` egenskapen.

Om du föredrar att använda en behållar-URL med SAS du kan också skicka detta via en gemensam miljöinställning jobb, men aktivitet processor mallen innehåller för närvarande inte inbyggt stöd för detta.

**Lagringsinställningar**

Du rekommenderas att aktiviteten klient- eller manager skapar behållare som krävs av uppgifter innan du lägger till aktiviteter i jobbet. Detta är obligatoriskt om du använder en behållar-URL med SAS som en URL inte innehåller behörighet att skapa behållaren. Det rekommenderas även om du skickar lagringskontouppgifter, som sparas alla aktiviteter som att anropa CloudBlobContainer.CreateIfNotExistsAsync i behållaren.

## <a name="pass-parameters-and-environment-variables"></a>Skicka parametrar och miljövariabler
### <a name="pass-environment-settings"></a>Skicka miljöinställningar
En klient kan skicka information till manager projektaktivitet i form av inställningarna för miljön. Den här informationen kan sedan användas av manager projektaktivitet när uppgiften processor aktiviteter som körs som en del av jobbet för beräkning. Exempel på den information som du kan överföra som miljöinställningarna är:

* Namn och lagringskontonycklar
* URL: en för batch-konto
* Batch-kontonyckel

Batch-tjänsten har en enkel metod för att skicka miljöinställningar till en projektaktivitet manager med hjälp av den `EnvironmentSettings` egenskap i [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Till exempel för att hämta den `BatchClient` instans för Batch-kontot du skickar som miljövariabler från klienten code URL och autentiseringsuppgifter för delade nycklar för Batch-kontot. På samma sätt för att komma åt lagringskontot som är kopplad till Batch-kontot måste överföra du lagringskontonamn och lagringskontots åtkomstnyckel som miljövariabler.

### <a name="pass-parameters-to-the-job-manager-template"></a>Skicka parametrar till Job Manager-mall
I många fall är det användbart att skicka per jobbparametrar job manager aktiviteten att styra jobbet dela processen eller konfigurera uppgifter för jobbet. Du kan göra detta genom att ladda upp en JSON-fil med namnet parameters.json som en resurs för manager projektaktivitet. Parametrarna sedan kan bli tillgängliga i den `JobSplitter._parameters` i Job Manager-mall.

> [!NOTE]
> Inbyggda parametern hanteraren stöder endast string-string ordlistor. Om du vill skicka komplexa JSON-värden som parametervärden behöver du skicka dessa som strängar och analysera dem i jobbet delningslisten eller ändra av framework `Configuration.GetJobParameters` metod.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Skicka parametrar för aktiviteten Processor-mallen
Du kan också skicka parametrar till enskilda aktiviteter som implementeras med hjälp av mallen uppgiften Processor. Precis som med manager jobbmallen söker uppgiften processor mallen efter en resursfil med namnet

parameters.JSON och om att hitta den läser in den som parametrar ordlistan. Det finns ett par olika alternativ att skicka parametrar till aktiviteten processor uppgifter:

* Återanvända jobbparametrar JSON. Detta fungerar bra om parametrarna endast är de som jobbet hela (till exempel en render höjd och bredd). Gör det, när du skapar en CloudTask i jobbet delningslisten, lägga till en referens till filen parameters.json resursobjektet från jobbet manager aktivitetens ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) till den CloudTask ResourceFiles samling.
* Generera och överför en uppgiftsspecifika parameters.json dokument som en del av delningslisten jobbkörningen och referera till blobben i aktivitetens resurssamling filer. Detta är nödvändigt om olika aktiviteter har olika parametrar. Ett exempel kan vara ett 3D-rendering scenario där ram indexet har överförts till aktiviteten som en parameter.

> [!NOTE]
> Inbyggda parametern hanteraren stöder endast string-string ordlistor. Om du vill skicka komplexa JSON-värden som parametervärden behöver du skicka dessa som strängar och analysera dem i aktiviteten processor eller ändra av framework `Configuration.GetTaskParameters` metod.
> 
> 

## <a name="next-steps"></a>Nästa steg
### <a name="persist-job-and-task-output-to-azure-storage"></a>Spara jobb- och utdata till Azure Storage
Ett annat bra verktyg i utvecklingen av Batch-lösningar är [Azure Batch filen konventioner][nuget_package]. Använd det här .NET klass-biblioteket (för närvarande under förhandsgranskning) i Batch .NET-program att enkelt lagra och hämta utdata för aktiviteten till och från Azure Storage. [Spara Azure Batch-jobb- och utdata](batch-task-output.md) innehåller en fullständig beskrivning av biblioteket och dess användning.

### <a name="batch-forum"></a>Batch-Forum
Den [Azure Batch-Forum] [ forum] på MSDN är det bra att diskutera Batch och ställa frågor om tjänsten. HEAD på över för användbara ”Fäst” inlägg och publicera dina frågor när de uppstår när du skapar Batch-lösningar.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
