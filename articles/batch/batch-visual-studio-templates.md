---
title: Skapa lösningar med Visual Studio-mallar – Azure Batch | Microsoft Docs
description: Lär dig hur Visual Studio-projektmallar kan hjälpa dig att implementera och kör dina beräkningsintensiva arbetsbelastningar på Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538131"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Använd Visual Studio-projektmallar för att ge Batch-lösningar

Den **Jobbhanteraren** och **uppgift Processor Visual Studio-mallarna** för Batch tillhandahåller kod för att hjälpa dig att implementera och kör dina beräkningsintensiva arbetsbelastningar på Batch med minsta möjliga ansträngning. Det här dokumentet beskriver dessa mallar och innehåller anvisningar för hur de används.

> [!IMPORTANT]
> Den här artikeln beskrivs bara information som gäller för dessa två mallar och förutsätter att du är bekant med Batch-tjänsten och viktiga begrepp relaterade till det: pooler, compute-noder, jobb och aktiviteter, job manager-aktiviteter, miljövariabler och andra relevanta information. Du hittar mer information finns i [grunderna i Azure Batch](batch-technical-overview.md) och [Batch-funktionsöversikten för utvecklare](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Översikt på hög nivå
Job Manager och uppgiften Processor-mallar kan användas för att skapa två användbara komponenter:

* En job manager-aktivitet som implementerar en förgrening för jobb som kan dela ett jobb upp på flera aktiviteter som kan köras oberoende av varandra, parallellt.
* En uppgift processor som kan användas för att utföra före och efter bearbetning runt en kommandorad för programmet.

Till exempel i ett scenario med film rendering skulle delningslist för jobbet göra ett enda film-jobb till hundratals eller tusentals olika uppgifter som skulle bearbeta enskilda bildrutor separat. På motsvarande sätt uppgiftsprocessorn skulle anropa rendering programmet och alla beroende processer som krävs för att återge var och en ram, samt utföra eventuella ytterligare åtgärder (till exempel, kopiera den renderade bildrutan till en lagringsplats).

> [!NOTE]
> Job Manager och uppgiften Processor mallarna är oberoende av varandra, så att du kan välja att använda både eller bara en av dem beroende på kraven för din beräknings-jobb och på dina inställningar.
> 
> 

I diagrammet nedan visas ett jobb för beräkning som använder dessa mallar går igenom tre steg:

1. Klientkoden (t.ex. program, webbtjänst, etc.) skickar ett jobb till Batch-tjänsten på Azure, ange eftersom dess Jobbhanterare uppgift job manager-program.
2. Batch-tjänsten kör job manager-aktivitet på en beräkningsnod och delningslist för jobbet öppnas det angivna antalet uppgift processor uppgifter på eftersom många compute-noder efter behov, baserat på de parametrar och specifikationer i jobbet delare koden.
3. Uppgiften processor uppgifter köras oberoende av varandra, parallellt att bearbeta indata och genererar utdata.

![Diagram som visar hur klientkod interagerar med Batch-tjänsten][diagram01]

## <a name="prerequisites"></a>Förutsättningar
För att använda Batch-mallar, behöver du följande:

* En dator med Visual Studio 2015 installerad. Batch-mallar finns för närvarande stöds endast för Visual Studio 2015.
* Batch-mallar, som är tillgängliga från den [Visual Studio-galleriet] [ vs_gallery] som Visual Studio-tillägg. Det finns två sätt att hämta mallar:
  
  * Installera mallarna med hjälp av den **tillägg och uppdateringar** dialogrutan i Visual Studio (Mer information finns i [söka efter och med hjälp av Visual Studio-tillägg][vs_find_use_ext]). I den **tillägg och uppdateringar** dialogrutan rutan, söka och hämta följande två tillägg:
    
    * Azure Batch Job Manager med delningslist för jobbet
    * Azure Batch uppgift-Processor
  * Hämta mallar från galleriet online för Visual Studio: [Projektmallar för Microsoft Azure Batch][vs_gallery_templates]
* Om du planerar att använda den [programpaket](batch-application-packages.md) funktion för att distribuera jobbhanteraren och uppgiften processor till Batch-beräkningsnoder, du behöver länka ett lagringskonto till Batch-kontot.

## <a name="preparation"></a>Förberedelse
Vi rekommenderar att du skapar en lösning som kan innehålla chefen jobbet som aktiviteten processorn, eftersom detta kan göra det enklare att dela kod mellan Jobbhanterare och uppgiften processor program. Följ dessa steg om du vill skapa den här lösningen:

1. Öppna Visual Studio och välj **filen** > **New** > **projekt**.
2. Under **mallar**, expandera **övriga projekttyper**, klickar du på **Visual Studio-lösningar**, och välj sedan **tom lösning**.
3. Skriv ett namn som beskriver ditt program och syftet med den här lösningen (t.ex. ”LitwareBatchTaskPrograms”).
4. Klicka för att skapa den nya lösningen **OK**.

## <a name="job-manager-template"></a>Job Manager-mall
Job Manager-mall kan du implementera en job manager-aktivitet som kan utföra följande åtgärder:

* Dela upp ett jobb i flera aktiviteter.
* Skicka aktiviteterna ska köras på Batch.

> [!NOTE]
> Mer information om job manager-aktiviteter finns i [Batch-funktionsöversikten för utvecklare](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Skapa en Job Manager med hjälp av mallen
Följ dessa steg för att lägga till en Jobbhanterare i lösningen som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer, klicka på **Lägg till** > **nytt projekt**.
3. Under **Visual C#**, klickar du på **molnet**, och klicka sedan på **Azure Batch Job Manager med jobbet delare**.
4. Skriv ett namn som beskriver ditt program och som identifierar det här projektet som jobbhanteraren (t.ex.) "LitwareJobManager").
5. Klicka för att skapa projektet **OK**.
6. Slutligen skapa projekt för att tvinga Visual Studio för att läsa in alla refererade NuGet-paket och kontrollera att projektet är giltig innan du börjar att ändra den.

### <a name="job-manager-template-files-and-their-purpose"></a>Job Manager-mallfiler och deras syfte
När du skapar ett projekt med hjälp av Job Manager-mall, skapar tre grupper av kodfiler:

* Den huvudsakliga programfilen (Program.cs). Innehåller startpunkt för programmet och översta undantagshantering. Du bör inte normalt behöver ändra den här.
* Framework-katalogen. Det innehåller filerna som är ansvarig för 'formaterad' arbetet som utförs av hanteringsprogram jobb – uppackning av parametrar, lägger till aktiviteter till Batch-jobb, osv. Du bör inte normalt måste du ändra de här filerna.
* Jobbet delare filen (JobSplitter.cs). Det här är placerar du programspecifika logik för att dela ett jobb i uppgifter.

Naturligtvis kan du lägga till ytterligare filer som krävs för jobbet delare koden, beroende på komplexiteten för jobbet dela logik.

Mallen genererar även standard .NET projektfiler, till exempel en filen .csproj, app.config, packages.config osv.

Resten av det här avsnittet beskrivs de olika filerna och deras kodstruktur och förklaras vad varje klass gör.

![Visual Studio Solution Explorer och visar Jobbhanteraren mall lösning][solution_explorer01]

**Framework-filer**

* `Configuration.cs`: Kapslar in inläsningen av jobbet konfigurationsdata som Batch-kontoinformation, autentiseringsuppgifterna för länkade lagringskontot, jobb och aktivitetsinformation och jobbparametrar. Det ger också åtkomst till Batch-definierade miljövariabler (se miljöinställningar för aktiviteter i Batch-dokumentation) via Configuration.EnvironmentVariable-klassen.
* `IConfiguration.cs`: Avlägsnar implementeringen av klassen konfiguration, så att du kan enhetstest din delningslist för jobbet med hjälp av en förfalskad eller fingerad konfigurationsobjektet.
* `JobManager.cs`: Samordnar komponenterna i job manager-program. Den är ansvarig för att initiera jobb delare, anropar delningslist för jobb och skicka de uppgifter som returneras av delningslist för jobbet att uppgiften inlämnare.
* `JobManagerException.cs`: Representerar ett fel som kräver jobbhanteraren avslutas. JobManagerException används för att omsluta 'förväntade' fel där specifika diagnostisk information kan anges som en del av uppsägning.
* `TaskSubmitter.cs`: Den här klassen är ansvarig för att lägga till aktiviteter som returneras av delningslist för jobbet till Batch-jobbet. Klass-aggregeringar JobManager aktivitetssekvensen i batchar för effektiv men snabbt lägga till i jobbet anropar sedan TaskSubmitter.SubmitTasks i en bakgrundstråd för varje batch.

**Delningslist för jobbet**

`JobSplitter.cs`: Den här klassen innehåller programspecifika logik för att dela upp jobbet i uppgifter. Ramverket anropar metoden JobSplitter.Split för att få en serie aktiviteter som läggs till i jobbet som dem returnerar-metoden. Det här är klassen där du matar in logiken för jobbet. Implementera Split-metoden för att returnera en sekvens av CloudTask instanser som representerar de uppgifter som du vill partitionera jobbet.

**Standard projektfiler för .NET-kommandorad**

* `App.config`: Standard .NET-programkonfigurationsfilen.
* `Packages.config`: NuGet-paketet beroende standardfil.
* `Program.cs`: Innehåller startpunkt för programmet och översta undantagshantering.

### <a name="implementing-the-job-splitter"></a>Implementera delningslist för jobbet
När du öppnar Job Manager-mallprojektet har projektet filen JobSplitter.cs öppnas som standard. Du kan implementera dela logiken för uppgifter i din arbetsbelastning med hjälp av Split() metoden visas nedan:

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
> Avsnittet kommenterade i den `Split()` metoden är det enda avsnittet kod för Job Manager-mall som är avsedd att ändra genom att lägga till logiken som att dela upp dina jobb i olika uppgifter. Om du vill ändra ett annat avsnitt i mallen, kontrollera att du har bekantat med hur Batch fungerar, och prova några av de [Batch-kodexempel][github_samples].
> 
> 

Implementeringen av Split() har åtkomst till:

* Jobbparametrarna via den `_parameters` fält.
* CloudJob-objektet som representerar jobbet den `_job` fält.
* CloudTask-objektet som representerar job manager-aktivitet den `_jobManagerTask` fält.

Din `Split()` implementering behöver inte lägga till aktiviteter i jobbet direkt. I stället koden ska returnera en sekvens av CloudTask objekt, och dessa läggs till i jobbet automatiskt av framework-klasser som anropar delningslist för jobbet. Det är vanligt att använda C# 's Iteratorn (`yield return`) funktionen för att implementera jobbet delarna som på så sätt kan aktiviteterna starta körs så snart som möjligt i stället för att vänta tills alla aktiviteter ska beräknas.

**Delningslist för jobbfel**

Om din delningslist för jobbet påträffar ett fel, bör det antingen:

* Avsluta sekvensen med C# `yield break` -instruktionen, i vilket fall jobbhanteraren behandlas som slutförd; eller
* Utlös ett undantag, där fallet jobbhanteraren behandlas som misslyckades och kan göras beroende på hur klienten har konfigurerats).

I båda fallen blir alla aktiviteter som redan returneras av delningslist för jobbet och läggs till i Batch-jobb kan köras. Sedan om du inte vill det, kan du:

* Jobbet avbryts innan man returneras från delningslist för jobbet
* Formulera samlingen hela aktiviteten innan den returneras (det vill säga returnera en `ICollection<CloudTask>` eller `IList<CloudTask>` i stället för att implementera din delningslist för jobbet med hjälp av ett C#-Iteratorn)
* Använd aktivitetsberoenden för att göra alla aktiviteter som är beroende av jobbhanteraren slutförs

**Job manager återförsök**

Om jobbhanteraren misslyckas, kan den göras av Batch-tjänsten beroende på klientinställningar för återförsök. Detta är i allmänhet är säkra, eftersom när ramverket lägger till uppgifter i jobbet, ignoreras alla aktiviteter som redan finns. Men om det är dyrt att beräkna uppgifter, kanske du inte vill resultera i kostnader för omberäknar aktiviteter som redan har lagts till jobb. däremot om kör igen inte är säkert att generera samma uppgift ID: N kommer sedan beteendet ”Ignorera dubbletter” inte startar. I dessa fall bör du utforma dina jobb delningslist för att identifiera det arbete som redan har gjorts och inte upprepa den, till exempel genom att utföra en CloudJob.ListTasks innan du börjar ge uppgifter.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Slutkoder för aktiviteter och undantag i Job Manager-mall
Slutkoder för aktiviteter och undantag tillhandahåller en mekanism för att fastställa resultatet av att köra ett program och de kan hjälpa till att identifiera eventuella problem med körningen av programmet. Job Manager-mallen implementerar slutkoder för aktiviteter och undantag som beskrivs i det här avsnittet.

En job manager-aktivitet som implementeras med Job Manager-mall kan returnera tre möjliga slutkoder för aktiviteter:

| Kod | Beskrivning |
| --- | --- |
| 0 |Jobbhanteraren har slutförts. Jobbet delare koden körde kan slutföras och alla aktiviteter har lagts till i jobbet. |
| 1 |Job manager-aktiviteten misslyckades med ett undantag i en '' del av programmet. Undantaget har översatts till en JobManagerException med diagnostisk information och, om möjligt, förslag för att åtgärda felet. |
| 2 |Job manager-aktiviteten misslyckades med undantaget ”oväntat”. Undantaget loggades till standardutdata, men jobbhanteraren kunde inte lägga till ytterligare diagnostik- eller reparation information. |

När det gäller jobbet manager uppgift misslyckades, vissa uppgifter kan fortfarande har lagts till tjänsten innan felet inträffade. Dessa aktiviteter ska köra som vanligt. Se ”delare jobbfel” ovan beskrivning av den här koden sökvägen.

All information som returneras av undantag skrivs till stdout.txt och stderr.txt-filer. Mer information finns i [felhantering](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Överväganden för klienten
Det här avsnittet beskrivs vissa krav för implementering av klienten vid en Jobbhanterare baserat på den här mallen. Se [hur du skickar parametrar och miljövariabler från klientkoden](#pass-environment-settings) mer information om att skicka parametrar och miljöinställningar.

**Obligatorisk autentiseringsuppgifter**

Job manager-aktiviteten kräver ditt Azure Batch-kontots URL och nyckel för att lägga till aktiviteter till Azure Batch-jobb. Du måste skicka dessa i miljövariabler som heter YOUR_BATCH_URL och YOUR_BATCH_KEY. Du kan ange dessa i Jobbhanterare miljöinställningar för uppgiften. Till exempel i en C# klient:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Storage-autentiseringsuppgifter**

Normalt klienten behöver inte ange autentiseringsuppgifter för länkade storage-konto att job manager-aktiviteten eftersom (a) de flesta jobbet cheferna behöver inte uttryckligen åtkomst länkade storage-kontot och (b) det länkade storage-kontot är ofta levereras till alla aktiviteter som en vanliga miljöinställning för jobbet. Om du inte använder det länkade storage-kontot via vanliga miljöinställningar och jobbhanteraren kräver åtkomst till länkat storage, bör du ange autentiseringsuppgifter för länkad lagring på följande sätt:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Job manager-aktivitetsinställningar**

Klienten bör ange jobbhanteraren *killJobOnCompletion* flaggan till **FALSKT**.

Det är vanligtvis säkert för att klienten ska ange *runExclusive* till **FALSKT**.

Klienten bör använda den *resourceFiles* eller *applicationPackageReferences* samling att jobbet manager körbara (och dess nödvändiga DLL-filer) som distribuerats till Beräkningsnoden.

Som standard jobbhanteraren kunde inte genomföras om det misslyckas. Beroende på din job manager-logik klienten kanske vill aktivera återförsök via *begränsningar*/*maxTaskRetryCount*.

**Inställningar för**

Om jobbet delare genererar uppgifter med beroenden, måste klienten ange usesTaskDependencies för jobbets till true.

Det är ovanligt för klienter som vill lägga till aktiviteter till jobb utöver delningslist för jobbet skapar i jobbet delningslist för modellen. Klienten bör därför vanligtvis ange jobbets *onAllTasksComplete* till **terminatejob**.

## <a name="task-processor-template"></a>Uppgiften Processor mall
En mall för uppgiften Processor hjälper dig att implementera en uppgift processor som kan utföra följande åtgärder:

* Ställ in den information som krävs av varje Batch-aktivitet ska köras.
* Kör alla åtgärder som krävs av varje Batch-aktivitet.
* Spara utdata för aktiviteten till beständig lagring.

Även om en uppgift processor inte krävs för att köra aktiviteter på Batch, är den främsta fördelen med att använda en uppgift processor att det ger en Omslutning för att implementera alla Uppgiftsåtgärder för körning på en plats. Till exempel uppgift om du behöver köra flera program i kontexten för varje aktivitet, eller om du vill kopiera data till beständig lagring när du har slutfört varje.

De åtgärder som utförs av processor för aktiviteten kan vara enkla eller komplexa, och så många eller så lite som krävs av din arbetsbelastning. Dessutom genom att implementera alla-åtgärder i en uppgift processor, kan du enkelt uppdatera eller lägga till åtgärder baserat på ändringar i program eller arbetsbelastning krav. Men i vissa fall kanske en uppgift processor inte den bästa lösningen för din implementering som den kan lägga till onödig komplexitet, till exempel när du kör jobb som kan startas snabbt från en enkel kommandorad.

### <a name="create-a-task-processor-using-the-template"></a>Skapa en uppgift-Processor med hjälp av mallen
Följ dessa steg för att lägga till en uppgift processor i lösningen som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer, klicka på **Lägg till**, och klicka sedan på **nytt projekt**.
3. Under **Visual C#**, klickar du på **molnet**, och klicka sedan på **Azure Batch uppgift Processor**.
4. Skriv ett namn som beskriver ditt program och som identifierar det här projektet som uppgiften-processor (t.ex.) ”LitwareTaskProcessor”).
5. Klicka för att skapa projektet **OK**.
6. Slutligen skapa projekt för att tvinga Visual Studio för att läsa in alla refererade NuGet-paket och kontrollera att projektet är giltig innan du börjar att ändra den.

### <a name="task-processor-template-files-and-their-purpose"></a>Uppgift mallfilerna för Processor och deras syfte
När du skapar ett projekt med hjälp av uppgiften processor mall, skapar tre grupper av kodfiler:

* Den huvudsakliga programfilen (Program.cs). Innehåller startpunkt för programmet och översta undantagshantering. Du bör inte normalt behöver ändra den här.
* Framework-katalogen. Det innehåller filerna som är ansvarig för 'formaterad' arbetet som utförs av hanteringsprogram jobb – uppackning av parametrar, lägger till aktiviteter till Batch-jobb, osv. Du bör inte normalt måste du ändra de här filerna.
* Uppgiften processor filen (TaskProcessor.cs). Det här är placerar du programspecifika logik för att köra en aktivitet (vanligtvis genom att anropa till en befintlig körbar fil). Före- och efterbearbetning kod, till exempel ladda ned ytterligare data eller ladda upp filer för resultatet, anges också här.

Naturligtvis kan du lägga till ytterligare filer som krävs för uppgiften processor koden, beroende på komplexiteten för jobbet dela logik.

Mallen genererar även standard .NET projektfiler, till exempel en filen .csproj, app.config, packages.config osv.

Resten av det här avsnittet beskrivs de olika filerna och deras kodstruktur och förklaras vad varje klass gör.

![Visual Studio Solution Explorer visar uppgift Processor mall-lösning][solution_explorer02]

**Framework-filer**

* `Configuration.cs`: Kapslar in inläsningen av jobbet konfigurationsdata som Batch-kontoinformation, autentiseringsuppgifterna för länkade lagringskontot, jobb och aktivitetsinformation och jobbparametrar. Det ger också åtkomst till Batch-definierade miljövariabler (se miljöinställningar för aktiviteter i Batch-dokumentation) via Configuration.EnvironmentVariable-klassen.
* `IConfiguration.cs`: Avlägsnar implementeringen av klassen konfiguration, så att du kan enhetstest din delningslist för jobbet med hjälp av en förfalskad eller fingerad konfigurationsobjektet.
* `TaskProcessorException.cs`: Representerar ett fel som kräver jobbhanteraren avslutas. TaskProcessorException används för att omsluta 'förväntade' fel där specifika diagnostisk information kan anges som en del av uppsägning.

**Uppgift-Processor**

* `TaskProcessor.cs`: Kör aktiviteten. Ramverket anropar metoden TaskProcessor.Run. Det här är klassen där du matar in programspecifika logiken för uppgiften. Implementera metoden Run till:
  * Parsa och validera alla uppgiftsparametrar
  * Compose-kommandoraden för alla externa program som du vill anropa
  * Logga alla diagnostisk information som du kan behöva för felsökning
  * Starta en process med hjälp av kommandoraden
  * Vänta tills processen avslutas
  * Avbilda slutkoden för processen för att avgöra om den lyckades eller misslyckades
  * Spara alla utdatafiler som du vill behålla till beständig lagring

**Standard projektfiler för .NET-kommandorad**

* `App.config`: Standard .NET-programkonfigurationsfilen.
* `Packages.config`: NuGet-paketet beroende standardfil.
* `Program.cs`: Innehåller startpunkt för programmet och översta undantagshantering.

## <a name="implementing-the-task-processor"></a>Implementera uppgift-processor
När du öppnar uppgiften Processor-mallprojektet har projektet filen TaskProcessor.cs öppnas som standard. Du kan implementera kör logiken för uppgifter i din arbetsbelastning med hjälp av metoden Run() visas nedan:

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
> Avsnittet kommenterade i Run()-metoden är endast del av uppgiften Processor mallkoden som hjälper dig att ändra genom att köra logiken för uppgifter i din arbetsbelastning. Om du vill ändra ett annat avsnitt i mallen kan du först bekanta dig med hur Batch fungerar genom att granska Batch-dokumentation och prova några av Batch-kodexempel.
> 
> 

Run()-metoden ansvarar för att starta från kommandoraden, starta en eller flera processer att vänta tills alla processen skulle slutföras, spara resultaten och slutligen returnerar en felkod. Run()-metoden är där du implementera standardbearbetningslogiken för dina aktiviteter. Uppgiften processor framework anropar Run()-metoden. Du behöver inte anropa den själv.

Implementeringen av Run() har åtkomst till:

* Parametrarna för konsoluppgiften via den `_parameters` fält.
* Jobb- och ID, via den `_jobId` och `_taskId` fält.
* Konfigurera aktivitet via den `_configuration` fält.

**Uppgiftsfel**

Du kan avsluta Run()-metoden genom att utlösa ett undantag vid fel, men det innebär den översta nivån Undantagshanteraren kontroll över sluttid för aktiviteten. Om du vill kontrollera slutkoden så att du kan skilja mellan olika typer av fel, till exempel i diagnostiskt syfte eller eftersom vissa feltillstånd ska avslutas jobbet och andra bör ändras bör du avsluta Run()-metoden genom att returnera en inte är noll slutkod. Detta blir sluttid för aktiviteten.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Slutkoder för aktiviteter och undantag i uppgiften Processor-mall
Slutkoder för aktiviteter och undantag tillhandahåller en mekanism för att fastställa resultatet av att köra ett program och de kan identifiera eventuella problem med körningen av programmet. Mallen uppgift Processor implementerar slutkoder för aktiviteter och undantag som beskrivs i det här avsnittet.

En uppgift processor-aktivitet som implementeras med mallen uppgift Processor kan returnera tre möjliga slutkoder för aktiviteter:

| Kod | Beskrivning |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Uppgiften processor körde slutförandet. Observera att detta inte innebär nödvändigtvis att det program som du startade lyckades – endast att processorn aktiviteten anropa den och utförs alla efterbearbeta utan undantag. Enligt slutkoden beror på programmet som anropas – vanligtvis slutkoden 0 betyder programmet lyckades och andra slutkod programmet misslyckades. |
| 1 |Uppgiftsprocessorn misslyckades med ett undantag i en '' del av programmet. Undantaget har översatts till en `TaskProcessorException` med diagnostisk information och, om möjligt, förslag för att åtgärda felet. |
| 2 |Uppgiftsprocessorn misslyckades med undantaget ”oväntat”. Undantaget loggades till standardutdata, men uppgiftsprocessorn kunde inte lägga till ytterligare diagnostik- eller reparation information. |

> [!NOTE]
> Om programmet som du anropar använder slutkoder för aktiviteter 1 och 2 för att ange specifika feltillstånd, är sedan med slutkoder för aktiviteter 1 och 2 för Uppgiftsfel för processor tvetydig. Du kan ändra dessa felkoder för uppgiften processor till särskilda slutkoder för aktiviteter genom att redigera undantag ärenden i filen Program.cs.
> 
> 

All information som returneras av undantag skrivs till stdout.txt och stderr.txt-filer. Mer information finns i felhantering, i Batch-dokumentationen.

### <a name="client-considerations"></a>Överväganden för klienten
**Storage-autentiseringsuppgifter**

Om uppgiften-processor använder Azure blob storage för att spara utdata med file conventions hjälpbibliotek, exempelvis så den behöver åtkomst till *antingen* autentiseringsuppgifterna för lagringskontot molnet *eller* en blob behållarens Webbadress som innehåller en signatur för delad åtkomst (SAS). Mallen har stöd för att ange autentiseringsuppgifter via vanliga miljövariabler. Klienten kan skicka autentiseringsuppgifter för lagringskontot på följande sätt:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Lagringskontot är sedan tillgängliga i klassen TaskProcessor via den `_configuration.StorageAccount` egenskapen.

Om du vill använda en URL för behållare med SAS kan du kan även skicka detta via en gemensam miljöinställning jobb, men uppgift processor mallen för närvarande innehåller inte inbyggt stöd för detta.

**Lagringsinställningar**

Du rekommenderas att aktiviteten klient eller job manager skapar alla behållare som krävs av uppgifter innan du lägger till uppgifter i jobbet. Detta är obligatoriskt om du använder en Webbadress för behållare med SAS, som till exempel en URL inte inkluderar behörighet att skapa behållaren. Vi rekommenderar även om du skickar autentiseringsuppgifter för lagringskonto, som sparas alla aktiviteter som behöva ringa CloudBlobContainer.CreateIfNotExistsAsync för behållaren.

## <a name="pass-parameters-and-environment-variables"></a>Skicka parametrar och miljövariabler
### <a name="pass-environment-settings"></a>Skicka miljöinställningar
En klient kan skicka information till job manager-aktiviteten i form av miljöinställningar. Den här informationen kan sedan användas av job manager-aktivitet när du genererar uppgift processor aktiviteterna som körs som en del av compute-jobb. Exempel på den information som du kan skicka som miljöinställningar är:

* Lagringskontots namn och nycklar
* Batch-kontots URL
* Batch-kontonyckel

Batch-tjänsten har en enkel metod för att skicka miljöinställningar till en job manager-aktivitet med hjälp av den `EnvironmentSettings` -egenskapen i [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Till exempel för att hämta den `BatchClient` instans för ett Batch-konto kan du skicka som miljövariabler från klienten code URL och autentiseringsuppgifter för delad nyckel för Batch-kontot. På samma sätt för att komma åt lagringskontot som är länkad till Batch-konto, kan du skicka namnet på lagringskontot och lagringskontonyckeln som miljövariabler.

### <a name="pass-parameters-to-the-job-manager-template"></a>Skicka parametrar till en Job Manager-mall
I många fall är det användbart att skicka jobbet parametrar till job manager-aktiviteten, antingen för att styra jobbet dela process eller för att konfigurera aktiviteterna för jobbet. Du kan göra detta genom att ladda upp en JSON-fil med namnet parameters.json som en resursfil för job manager-aktiviteten. Parametrarna kan sedan blir tillgängliga i den `JobSplitter._parameters` i Job Manager-mall.

> [!NOTE]
> Inbyggda parametern hanteraren stöder endast string-string ordlistor. Om du vill skicka komplexa JSON-värden som parametervärden du behöver skicka dessa som strängar och analysera dem i delningslist för jobbet eller ändra Ramverkets `Configuration.GetJobParameters` metod.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Skicka parametrar till mallen uppgift-Processor
Du kan även skicka parametrar till enskilda uppgifter som implementeras med hjälp av mallen uppgift Processor. Precis som med job manager-mall söker uppgift processor mallen efter en resursfil med namnet

parameters.JSON och om att hitta den läser in den som parameterns ordlista. Det finns ett par alternativ för hur du skickar parametrar till uppgiften processor uppgifter:

* Återanvända jobbparametrar JSON. Detta fungerar bra om de enda parametrarna är jobbomfattande som (till exempel en rendering höjd och bredd). Gör det, när du skapar en CloudTask i delningslist för jobbet, lägga till en referens till objektet parameters.json resurs fil från job manager aktiviteten ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) till den CloudTask ResourceFiles-samling.
* Generera och överför ett uppgiftsspecifika parameters.json dokument som en del av delningslist för jobbkörning och referera till den blobben i aktivitetens resurssamling filer. Detta är nödvändigt om olika aktiviteter har olika parametrar. Ett exempel kan vara ett 3D-rendering scenario där ramens indexet skickas till aktiviteten som en parameter.

> [!NOTE]
> Inbyggda parametern hanteraren stöder endast string-string ordlistor. Om du vill skicka komplexa JSON-värden som parametervärden du behöver skicka dessa som strängar och analysera dem i uppgift-processor eller ändra Ramverkets `Configuration.GetTaskParameters` metod.
> 
> 

## <a name="next-steps"></a>Nästa steg
### <a name="persist-job-and-task-output-to-azure-storage"></a>Bevara jobb- och utdata till Azure Storage
Ett annat bra verktyg i utvecklingen av Batch-lösning är [Azure Batch File Conventions][nuget_package]. Använd den här .NET-klassbiblioteket (för närvarande i förhandsversion) i Batch .NET-program för att enkelt lagra och hämta utdata för aktiviteten till och från Azure Storage. [Spara Azure Batch-jobb- och utdata](batch-task-output.md) innehåller en fullständig beskrivning av biblioteket och dess användning.


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
