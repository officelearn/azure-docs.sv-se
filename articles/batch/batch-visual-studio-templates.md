---
title: Skapa lösningar med Visual Studio-mallar – Azure Batch | Microsoft-dokument
description: Lär dig mer om hur Visual Studio-projektmallar kan hjälpa dig att implementera och köra dina beräkningsintensiva arbetsbelastningar på Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a71dbd1b38ff58ccf1eb7a4d50daad5b24922e2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022757"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Använda Visual Studio-projektmallar för att starta Batch-lösningar

Mallarna **Job Manager** och **Uppgiftsprocessor Visual Studio** för Batch innehåller kod som hjälper dig att implementera och köra dina beräkningsintensiva arbetsbelastningar i Batch med minsta möjliga ansträngning. Det här dokumentet beskriver dessa mallar och ger vägledning för hur du använder dem.

> [!IMPORTANT]
> I den här artikeln beskrivs endast information som är tillämplig på dessa två mallar och förutsätter att du är bekant med batchtjänsten och nyckelbegrepp relaterade till den: pooler, beräkningsnoder, jobb och uppgifter, jobbhanterare, miljövariabler och andra relevanta Information. Du hittar mer information i grunderna för [Azure Batch](batch-technical-overview.md) och [Batch-funktionsöversikt för utvecklare](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Översikt på hög nivå
Mallarna Job Manager och Uppgiftsbehandlare kan användas för att skapa två användbara komponenter:

* En jobbhanterare som implementerar en jobbdelare som kan dela upp ett jobb i flera aktiviteter som kan köras oberoende av dem, parallellt.
* En uppgiftsprocessor som kan användas för att utföra förbearbetning och efterbearbetning runt en programkommandorad.

I ett filmåtergivningsscenario skulle jobbdelaren till exempel förvandla ett enda filmjobb till hundratals eller tusentals separata aktiviteter som bearbetar enskilda bildrutor separat. På motsvarande sätt skulle uppgiftsbehandlaren anropa renderingsprogrammet och alla beroende processer som behövs för att återge varje ram, samt utföra eventuella ytterligare åtgärder (till exempel kopiera den renderade ramen till en lagringsplats).

> [!NOTE]
> Mallarna Jobbhanteraren och Uppgiftsbehandlare är oberoende av varandra, så du kan välja att använda båda eller bara en av dem, beroende på kraven för beräkningsjobbet och dina inställningar.
> 
> 

Som visas i diagrammet nedan går ett beräkningsjobb som använder dessa mallar igenom tre steg:

1. Klientkoden (t.ex. program, webbtjänst osv.) skickar ett jobb till batch-tjänsten på Azure och anger som jobbhanterare uppgift jobbhanteraren programmet.
2. Batch-tjänsten kör jobbhanterarens uppgift på en beräkningsnod och jobbdelaren startar det angivna antalet aktivitetsprocessoruppgifter, på så många beräkningsnoder som krävs, baserat på parametrarna och specifikationerna i jobbdelningskoden.
3. Uppgiftsprocessoruppgifterna körs oberoende av samma sätt för att bearbeta indata och generera utdata.

![Diagram som visar hur klientkod interagerar med batchtjänsten][diagram01]

## <a name="prerequisites"></a>Krav
Om du vill använda batchmallarna behöver du följande:

* En dator med Visual Studio 2015 installerad. Batchmallar stöds för närvarande endast för Visual Studio 2015.
* Batchmallarna, som är tillgängliga från [Visual Studio Gallery][vs_gallery] som Visual Studio-tillägg. Det finns två sätt att hämta mallarna:
  
  * Installera mallarna med hjälp av dialogrutan **Tillägg och uppdateringar** i Visual Studio (mer information finns i Hitta och använda Visual [Studio-tillägg][vs_find_use_ext]). Sök och hämta följande två tillägg i dialogrutan **Tillägg och uppdateringar:**
    
    * Azure Batch Job Manager med jobbdelare
    * Azure batch-uppgiftsbehandlare
  * Ladda ned mallarna från onlinegalleriet för Visual Studio: [Microsoft Azure Batch Project-mallar][vs_gallery_templates]
* Om du planerar att använda funktionen [Programpaket](batch-application-packages.md) för att distribuera jobbhanteraren och uppgiftsbehandlaren till batchberäkningsnoderna måste du länka ett lagringskonto till ditt batchkonto.

## <a name="preparation"></a>Förberedelse
Vi rekommenderar att du skapar en lösning som kan innehålla både jobbhanteraren och uppgiftsbehandlare, eftersom det kan göra det enklare att dela kod mellan jobbhanteraren och uppgiftsprocessorprogrammen. Så här skapar du den här lösningen:

1. Öppna Visual Studio och välj **Arkiv** > **Nytt** > **Projekt**.
2. Expandera **Andra projekttyper**under **Mallar,** klicka på **Visual Studio Solutions**och välj sedan Tom **lösning**.
3. Skriv ett namn som beskriver ditt program och syftet med den här lösningen (t.ex.
4. Om du vill skapa den nya lösningen klickar du på **OK**.

## <a name="job-manager-template"></a>Mall för Jobbhanteraren
Mallen Job Manager hjälper dig att implementera en jobbhanterare som kan utföra följande åtgärder:

* Dela upp ett jobb i flera aktiviteter.
* Skicka dessa uppgifter som ska köras på Batch.

> [!NOTE]
> Mer information om jobbhanterare finns i [Översikt över batchfunktion för utvecklare](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Skapa en jobbhanterare med hjälp av mallen
Så här lägger du till en jobbhanterare i den lösning som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer och klicka på **Lägg till** > **nytt projekt**.
3. Klicka på **Cloud**under **Visuell C#** och klicka sedan på **Azure Batch Job Manager med Job Splitter**.
4. Skriv ett namn som beskriver ditt program och identifierar projektet som jobbhanterare (t.ex.
5. Om du vill skapa projektet klickar du på **OK**.
6. Slutligen, bygga projektet för att tvinga Visual Studio att ladda alla refererade NuGet paket och att kontrollera att projektet är giltigt innan du börjar ändra den.

### <a name="job-manager-template-files-and-their-purpose"></a>Mallfiler för Job Manager och deras syfte
När du skapar ett projekt med mallen Job Manager genereras tre grupper med kodfiler:

* Huvudprogramfilen (Program.cs). Detta innehåller programmets startpunkt och undantagshantering på den översta nivån. Du bör normalt inte behöva ändra detta.
* Katalogen Ram. Detta innehåller de filer som ansvarar för "standard" arbete som utförs av job manager-programmet - uppackning parametrar, lägga till uppgifter till Batch-jobbet, etc. Du bör normalt inte behöva ändra dessa filer.
* Jobbdelningsfilen (JobSplitter.cs). Här kommer du att placera din programspecifika logik för att dela upp ett jobb i aktiviteter.

Naturligtvis kan du lägga till ytterligare filer som krävs för att stödja ditt jobb splitter kod, beroende på komplexiteten i jobbet dela logik.

Mallen genererar också standard .NET-projektfiler som en CSPROJ-fil, app.config, packages.config, etc.

Resten av det här avsnittet beskriver de olika filerna och deras kodstruktur och förklarar vad varje klass gör.

![Visual Studio Solution Explorer som visar malllösningen Job Manager][solution_explorer01]

**Ram-filer**

* `Configuration.cs`: Sammanfattar inläsningen av jobbkonfigurationsdata som Batch-kontoinformation, länkade lagringskontoautentiseringsuppgifter, jobb- och uppgiftsinformation och jobbparametrar. Det ger också åtkomst till batchdefinierade miljövariabler (se Miljöinställningar för uppgifter, i batchdokumentationen) via klassen Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Sammanfattar implementeringen av klassen Configuration, så att du kan enhet testa din jobbdelare med hjälp av ett falskt eller utkastande konfigurationsobjekt.
* `JobManager.cs`: Dirigerar komponenterna i jobbhanteraren programmet. Den ansvarar för att initiera jobbdelaren, anropa jobbdelaren och skicka de aktiviteter som returneras av jobbdelaren till uppgiftslämnaren.
* `JobManagerException.cs`: Representerar ett fel som kräver att jobbhanteraren avslutas. JobManagerException används för att radbryta förväntade fel där specifik diagnostikinformation kan tillhandahållas som en del av uppsägningen.
* `TaskSubmitter.cs`: Den här klassen är ansvarig för att lägga till aktiviteter som returneras av jobbdelaren till batch-jobbet. Klassen JobManager sammanställer sekvensen av aktiviteter i batchar för effektiv men snabb addition av jobbet och anropar sedan TaskSubmitter.SubmitTasks på en bakgrundstråd för varje batch.

**Jobbdelare**

`JobSplitter.cs`: Den här klassen innehåller programspecifik logik för att dela upp jobbet i aktiviteter. Ramverket anropar jobsplitter.split-metoden för att hämta en sekvens av aktiviteter, som den lägger till i jobbet när metoden returnerar dem. Detta är den klass där du kommer att injicera logiken i ditt jobb. Implementera metoden Dela för att returnera en sekvens av CloudTask-instanser som representerar de uppgifter som du vill partitionera jobbet till.

**Projektfiler för standard .NET-kommandorad**

* `App.config`: Standard -NET-programkonfigurationsfil.
* `Packages.config`: Standard NuGet paketberoendefil.
* `Program.cs`: Innehåller programmets startpunkt och undantagshantering på den översta nivån.

### <a name="implementing-the-job-splitter"></a>Implementera jobbdelare
När du öppnar mallprojektet Job Manager öppnas filen JobSplitter.cs som standard. Du kan implementera delningslogiken för aktiviteterna i din arbetsbelastning med hjälp av metoden Split() nedan:

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
> Det kommenterade avsnittet i `Split()` metoden är det enda avsnittet i mallkoden för Jobbhanteraren som du vill ändra genom att lägga till logiken för att dela upp jobben i olika aktiviteter. Om du vill ändra en annan del av mallen, se till att du är bekant med hur Batch fungerar och prova några av [batchkodexempel][github_samples].
> 
> 

Implementeringen av Split() har tillgång till:

* Projektparametrarna, `_parameters` via fältet.
* CloudJob-objektet som representerar `_job` jobbet via fältet.
* CloudTask-objektet som representerar jobbhanterarens uppgift via fältet. `_jobManagerTask`

Implementeringen `Split()` behöver inte lägga till aktiviteter i jobbet direkt. I stället ska koden returnera en sekvens av CloudTask-objekt, och dessa läggs till i jobbet automatiskt av ramklasserna som anropar jobbdelaren. Det är vanligt att använda C#'s`yield return`iterator () funktion för att implementera jobbdelare eftersom det gör att aktiviteterna kan börja köras så snart som möjligt i stället för att vänta på att alla aktiviteter ska beräknas.

**Fel på jobbdelare**

Om ditt jobb splitter stöter på ett fel, bör det antingen:

* Avsluta sekvensen med `yield break` C#-satsen, i vilket fall jobbhanteraren kommer att behandlas som framgångsrik. Eller
* Kasta ett undantag, i vilket fall jobbhanteraren kommer att behandlas som misslyckades och kan göras om beroende på hur klienten har konfigurerat den).

I båda fallen kan alla aktiviteter som redan returnerats av jobbdelaren och läggs till i batch-jobbet köras. Om du inte vill att detta ska hända, då kan du:

* Avsluta jobbet innan du returnerar från jobbsplittringen
* Formulera hela uppgiftssamlingen innan du returnerar `ICollection<CloudTask>` den `IList<CloudTask>` (det vill än returnera en eller i stället för att implementera jobbdelaren med hjälp av en C# iterator)
* Använd aktivitetssamband för att göra alla aktiviteter beroende av att jobbhanteraren har slutförts

**Nya försök till jobbhanterare**

Om jobbhanteraren misslyckas kan den göras om av batch-tjänsten beroende på klientinställningarna för återförsök. I allmänhet är detta säkert, eftersom när ramverket lägger till aktiviteter i jobbet, ignorerar den alla uppgifter som redan finns. Men om det är dyrt att beräkna aktiviteter kanske du inte vill ådra dig kostnaden för att omberäkna aktiviteter som redan har lagts till i jobbet. Omvänt, om re-run inte garanteras att generera samma uppgift ID kommer då "ignorera dubbletter" beteende kommer inte att sparka i. I dessa fall bör du utforma din jobbdelare för att identifiera det arbete som redan har utförts och inte upprepa det, till exempel genom att utföra en CloudJob.ListTasks innan du börjar ge uppgifter.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Avsluta koder och undantag i mallen Jobbhanteraren
Avsluta koder och undantag ger en mekanism för att fastställa resultatet av att köra ett program, och de kan hjälpa till att identifiera eventuella problem med körningen av programmet. Mallen Jobbhanteraren implementerar de avslutningskoder och undantag som beskrivs i det här avsnittet.

En jobbhanterareuppgift som implementeras med mallen Jobbhanteraren kan returnera tre möjliga avslutningskoder:

| Kod | Beskrivning |
| --- | --- |
| 0 |Jobbhanteraren har slutförts. Jobbdelningskoden slutfördes och alla aktiviteter lades till i jobbet. |
| 1 |Jobbets chef-aktivitet misslyckades med ett undantag i en förväntad del av programmet. Undantaget översattes till en JobManagerException med diagnostikinformation och, om möjligt, förslag för att lösa felet. |
| 2 |Jobbets chef-uppgift misslyckades med ett "oväntat" undantag. Undantaget loggades till standardutdata, men jobbhanteraren kunde inte lägga till någon ytterligare diagnostik- eller reparationsinformation. |

Vid aktivitetsfel i jobbhanteraren kan vissa aktiviteter fortfarande ha lagts till i tjänsten innan felet uppstod. Dessa aktiviteter kommer att köras som vanligt. Se "Jobb splitterfel" ovan för diskussion om den här kodsökvägen.

All information som returneras av undantag skrivs in i stdout.txt- och stderr.txt-filer. Mer information finns i [Felhantering](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Kundöverväganden
I det här avsnittet beskrivs vissa krav på klientimplementering när du anropar en jobbhanterare baserat på den här mallen. Mer information om hur [du skickar parametrar och miljövariabler från klientkoden](#pass-environment-settings) finns information om att skicka parametrar och miljöinställningar.

**Obligatoriska autentiseringsuppgifter**

För att lägga till uppgifter i Azure Batch-jobbet kräver jobbhanterarens uppgift url och nyckel till Azure Batch-konto. Du måste skicka dessa i miljövariabler som heter YOUR_BATCH_URL och YOUR_BATCH_KEY. Du kan ange dessa i aktivitetsmiljöinställningarna i Jobbhanteraren. Till exempel i en C#-klient:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Autentiseringsuppgifter för lagring**

Klienten behöver vanligtvis inte ange de länkade lagringskontoautentiseringsuppgifterna till jobbhanteraruppgiften eftersom (a) de flesta jobbhanterare inte behöver uttryckligen komma åt det länkade lagringskontot och (b) det länkade lagringskontot ofta tillhandahålls till alla uppgifter som en gemensam miljöinställning för jobbet. Om du inte tillhandahåller det länkade lagringskontot via de gemensamma miljöinställningarna och jobbhanteraren kräver åtkomst till länkad lagring, bör du ange de länkade lagringsautentiseringsuppgifterna enligt följande:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Uppgiftsinställningar för Jobbhanterare**

Klienten ska ange att jobbhanteraren *dödar Flaggan killJobOnCompletion* till **false**.

Det är vanligtvis säkert för klienten att ställa *in runExclusive* till **false**.

Klienten ska använda *resursfilerna* eller *applicationPackageReferences-samlingen* för att få jobbhanteraren körbar (och dess obligatoriska DLL-filer) distribuerad till beräkningsnoden.

Som standard görs inte jobbhanteraren om det misslyckas. Beroende på logiken i jobbhanteraren kan klienten vilja aktivera återförsök via *begränsningar*/*maxTaskRetryCount*.

**Jobbinställningar**

Om jobbdelare avger aktiviteter med beroenden måste klienten ange jobbets usesTaskDependencies till true.

I jobbdelningsmodellen är det ovanligt att klienter vill lägga till uppgifter i jobb utöver vad jobbdelaren skapar. Klienten bör därför normalt ange att jobbets *onAllTasksComplete* ska **avslutas.**

## <a name="task-processor-template"></a>Mall för uppgiftsbehandlare
En uppgiftsprocessormall hjälper dig att implementera en uppgiftsbehandlare som kan utföra följande åtgärder:

* Ställ in den information som krävs för varje batch-aktivitet för att köras.
* Kör alla åtgärder som krävs för varje batch-aktivitet.
* Spara aktivitetsutdata i beständig lagring.

Även om en uppgiftsbehandlare inte krävs för att köra aktiviteter på Batch, är den viktigaste fördelen med att använda en uppgiftsbehandlare att den tillhandahåller ett omslag för att implementera alla åtgärder för att utföra uppgifter på en plats. Om du till exempel behöver köra flera program i samband med varje aktivitet, eller om du behöver kopiera data till beständig lagring efter att ha slutfört varje uppgift.

De åtgärder som utförs av uppgiftsbehandlaren kan vara så enkla eller komplexa, och så många eller så få, som krävs av din arbetsbelastning. Genom att implementera alla uppgiftsåtgärder i en uppgiftsbehandlare kan du dessutom enkelt uppdatera eller lägga till åtgärder baserat på ändringar i program eller arbetsbelastningskrav. I vissa fall kanske en uppgiftsbehandlare inte är den optimala lösningen för implementeringen, eftersom den kan lägga till onödig komplexitet, till exempel när du kör jobb som snabbt kan startas från en enkel kommandorad.

### <a name="create-a-task-processor-using-the-template"></a>Skapa en uppgiftsbehandlare med mallen
Så här lägger du till en uppgiftsbehandlare i den lösning som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer, klicka på **Lägg till**och klicka sedan på **Nytt projekt**.
3. Klicka på **Cloud**under **Visuell C#** och klicka sedan på **Azure Batch Task Processor**.
4. Skriv ett namn som beskriver ditt program och identifierar det här projektet som aktivitetsprocessor (t.ex.
5. Om du vill skapa projektet klickar du på **OK**.
6. Slutligen, bygga projektet för att tvinga Visual Studio att ladda alla refererade NuGet paket och att kontrollera att projektet är giltigt innan du börjar ändra den.

### <a name="task-processor-template-files-and-their-purpose"></a>Mallfiler för uppgiftsbehandlare och deras syfte
När du skapar ett projekt med uppgiftsbehandlarens mall genereras tre grupper av kodfiler:

* Huvudprogramfilen (Program.cs). Detta innehåller programmets startpunkt och undantagshantering på den översta nivån. Du bör normalt inte behöva ändra detta.
* Katalogen Ram. Detta innehåller de filer som ansvarar för "standard" arbete som utförs av job manager-programmet - uppackning parametrar, lägga till uppgifter till Batch-jobbet, etc. Du bör normalt inte behöva ändra dessa filer.
* Uppgiftsprocessorfilen (TaskProcessor.cs). Här kommer du att placera din programspecifika logik för att utföra en uppgift (vanligtvis genom att ropa till en befintlig körbar). För- och efterbehandling kod, såsom nedladdning av ytterligare data eller ladda upp resultatfiler, går också här.

Naturligtvis kan du lägga till ytterligare filer som krävs för att stödja din uppgift processorkod, beroende på komplexiteten i jobbet dela logik.

Mallen genererar också standard .NET-projektfiler som en CSPROJ-fil, app.config, packages.config, etc.

Resten av det här avsnittet beskriver de olika filerna och deras kodstruktur och förklarar vad varje klass gör.

![Utforskaren för Visual Studio-lösning som visar malllösningen För uppgiftsprocessor][solution_explorer02]

**Ram-filer**

* `Configuration.cs`: Sammanfattar inläsningen av jobbkonfigurationsdata som Batch-kontoinformation, länkade lagringskontoautentiseringsuppgifter, jobb- och uppgiftsinformation och jobbparametrar. Det ger också åtkomst till batchdefinierade miljövariabler (se Miljöinställningar för uppgifter, i batchdokumentationen) via klassen Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Sammanfattar implementeringen av klassen Configuration, så att du kan enhet testa din jobbdelare med hjälp av ett falskt eller utkastande konfigurationsobjekt.
* `TaskProcessorException.cs`: Representerar ett fel som kräver att jobbhanteraren avslutas. TaskProcessorException används för att radbryta förväntade fel där specifik diagnostikinformation kan tillhandahållas som en del av avslutningen.

**Uppgiftsbehandlare**

* `TaskProcessor.cs`: Kör uppgiften. Ramverket anropar metoden TaskProcessor.Run. Detta är den klass där du kommer att injicera den programspecifika logiken i din uppgift. Implementera metoden Kör för att:
  * Tolka och validera eventuella aktivitetsparametrar
  * Skriv kommandoraden för alla externa program som du vill starta
  * Logga all diagnostisk information som du kan behöva för felsökning
  * Starta en process med den kommandoraden
  * Vänta tills processen har avslutats
  * Samla in processens avslutningskod för att avgöra om den lyckades eller misslyckades
  * Spara alla utdatafiler som du vill behålla till beständig lagring

**Projektfiler för standard .NET-kommandorad**

* `App.config`: Standard -NET-programkonfigurationsfil.
* `Packages.config`: Standard NuGet paketberoendefil.
* `Program.cs`: Innehåller programmets startpunkt och undantagshantering på den översta nivån.

## <a name="implementing-the-task-processor"></a>Implementera uppgiftsbehandlaren
När du öppnar mallprojektet För uppgiftsbehandlare öppnas filen TaskProcessor.cs som standard. Du kan implementera körningslogiken för aktiviteterna i arbetsbelastningen med metoden Run() som visas nedan:

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
> Det kommenterade avsnittet i metoden Run() är det enda avsnittet i mallkoden för uppgiftsbehandlare som är avsedd att ändra genom att lägga till körningslogiken för aktiviteterna i arbetsbelastningen. Om du vill ändra en annan del av mallen bekantar du dig först med hur Batch fungerar genom att granska batchdokumentationen och prova några av batchkodexemplen.
> 
> 

Metoden Run() är ansvarig för att starta kommandoraden, starta en eller flera processer, vänta på att alla processer ska slutföras, spara resultaten och slutligen returnera med en avslutningskod. Metoden Run() är där du implementerar bearbetningslogiken för dina aktiviteter. Arbetsprocessorramen anropar metoden Run() åt dig. du behöver inte kalla det själv.

Implementeringen run() har åtkomst till:

* Aktivitetsparametrarna, via fältet `_parameters` .
* Jobb- och aktivitets-ID:na, via fälten `_jobId` och. `_taskId`
* Aktivitetskonfigurationen, `_configuration` via fältet.

**Fel på aktiviteten**

I händelse av fel kan du avsluta metoden Run() genom att utlösa ett undantag, men det lämnar den översta undantagshanteraren i kontroll över uppgiftsutträdeskoden. Om du behöver styra avslutningskoden så att du kan skilja mellan olika typer av fel, till exempel för diagnostiska ändamål eller för att vissa fellägen ska avsluta jobbet och andra inte bör avsluta metoden Run() genom att returnera en icke-noll utgångskoden. Detta blir uppgiftsutträdeskoden.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Avsluta koder och undantag i mallen Uppgiftsbehandlare
Avsluta koder och undantag ger en mekanism för att fastställa resultatet av att köra ett program, och de kan hjälpa till att identifiera eventuella problem med körningen av programmet. Mallen Uppgiftsprocessor implementerar de avslutningskoder och undantag som beskrivs i det här avsnittet.

En uppgiftsbehandlare som implementeras med mallen Uppgiftsprocessor kan returnera tre möjliga avslutningskoder:

| Kod | Beskrivning |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Uppgiftsbehandlaren slutfördes. Observera att detta inte innebär att programmet du anropade lyckades – bara att uppgiftsbehandlaren anropade den och utförde alla efterbearbetning utan undantag. Innebörden av avslutningskoden beror på det anropade programmet - vanligtvis avsluta kod 0 innebär att programmet lyckades och alla andra exit kod innebär att programmet misslyckades. |
| 1 |Uppgiftsbehandlaren misslyckades med ett undantag i en förväntad del av programmet. Undantaget översattes till `TaskProcessorException` en med diagnostikinformation och, om möjligt, förslag för att lösa felet. |
| 2 |Uppgiftsbehandlaren misslyckades med ett "oväntat" undantag. Undantaget loggades till standardutdata, men uppgiftsbehandlaren kunde inte lägga till ytterligare diagnostik- eller reparationsinformation. |

> [!NOTE]
> Om programmet du anropar använder utgångskoderna 1 och 2 för att ange specifika fellägen, är det tvetydigt att använda avslutningskoderna 1 och 2 för uppgiftsprocessorfel. Du kan ändra dessa felkoder för uppgiftsbehandlare till distinkta avslutningskoder genom att redigera undantagsfallen i Program.cs filen.
> 
> 

All information som returneras av undantag skrivs in i stdout.txt- och stderr.txt-filer. Mer information finns i Felhantering i batchdokumentationen.

### <a name="client-considerations"></a>Kundöverväganden
**Autentiseringsuppgifter för lagring**

Om uppgiftsbehandlaren använder Azure blob storage för att bevara utdata, till exempel med hjälp av hjälpbiblioteket för filkonventioner, behöver den åtkomst till *antingen* molnlagringskontouppgifterna *eller* en blob-behållar-URL som innehåller en SIGNATUR för delad åtkomst (SAS). Mallen innehåller stöd för att tillhandahålla autentiseringsuppgifter via vanliga miljövariabler. Klienten kan skicka lagringsuppgifterna på följande sätt:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Lagringskontot är sedan tillgängligt i klassen `_configuration.StorageAccount` TaskProcessor via egenskapen.

Om du föredrar att använda en behållar-URL med SAS kan du också skicka detta via en gemensam miljöinställning för jobbet, men uppgiftsprocessormallen innehåller för närvarande inte inbyggt stöd för detta.

**Installation av lagring**

Vi rekommenderar att aktiviteten klient eller jobbhanterare skapar alla behållare som krävs av aktiviteter innan aktiviteterna läggs till i jobbet. Detta är obligatoriskt om du använder en behållar-URL med SAS, eftersom en sådan URL inte innehåller behörighet att skapa behållaren. Vi rekommenderar även om du skickar autentiseringsuppgifter för lagringskonto, eftersom det sparar varje uppgift som måste anropa CloudBlobContainer.CreateIfNotExistsAsync på behållaren.

## <a name="pass-parameters-and-environment-variables"></a>Skicka parametrar och miljövariabler
### <a name="pass-environment-settings"></a>Inställningar för Godkänd miljö
En klient kan skicka information till jobbansvarige i form av miljöinställningar. Den här informationen kan sedan användas av jobbhanterarens uppgift när du genererar uppgiftsbehandlarens uppgifter som ska köras som en del av beräkningsjobbet. Exempel på den information som du kan skicka som miljöinställningar är:

* Namn och kontonycklar för lagringskonto
* URL för batchkonto
* Nyckeln för batchkonto

Batch-tjänsten har en enkel mekanism för att skicka miljöinställningar till en jobbhanterareuppgift med hjälp av egenskapen `EnvironmentSettings` i [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Om du till `BatchClient` exempel vill hämta instansen för ett batchkonto kan du skicka som miljövariabler från klientkoden URL:en och delade nyckeluppgifter för batch-kontot. Om du vill komma åt lagringskontot som är kopplat till batchkontot kan du skicka lagringskontonamnet och lagringskontonyckeln som miljövariabler.

### <a name="pass-parameters-to-the-job-manager-template"></a>Skicka parametrar till mallen Jobbhanteraren
I många fall är det användbart att skicka parametrar per jobb till jobbhanteraren, antingen för att styra jobbdelningsprocessen eller för att konfigurera aktiviteterna för jobbet. Du kan göra detta genom att ladda upp en JSON-fil med namnet parameters.json som en resursfil för jobbhanterarens uppgift. Parametrarna kan sedan bli `JobSplitter._parameters` tillgängliga i fältet i mallen Jobbansvarig.

> [!NOTE]
> Den inbyggda parameterhanteraren stöder endast ordlistor från sträng till sträng. Om du vill skicka komplexa JSON-värden som parametervärden måste du skicka dessa som strängar och tolka dem i `Configuration.GetJobParameters` jobbdelaren eller ändra ramverkets metod.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Skicka parametrar till uppgiftsprocessormallen
Du kan också skicka parametrar till enskilda uppgifter som implementerats med uppgiftsbehandlare mallen. Precis som med projekthanterarens mall söker uppgiftsbehandlaremallen efter en resursfil med namnet

parameters.json, och om den hittades läser den in den som parameterordlistan. Det finns ett par alternativ för hur du skickar parametrar till uppgiftsbehandlaren:

* Återanvända jobbparametrarna JSON. Detta fungerar bra om de enda parametrarna är hela jobbet (till exempel en renderingshöjd och bredd). Det gör du när du skapar en CloudTask i jobbdelaren, lägger du till en referens till parameterns.json-resursfilobjektet från jobbhanterarens aktivitet ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) i CloudTasks ResourceFiles-samling.
* Generera och ladda upp ett aktivitetsspecifikt parameters.json-dokument som en del av jobbdelningskörningen och referera till den blob i aktivitetens resursfilsamling. Detta är nödvändigt om olika uppgifter har olika parametrar. Ett exempel kan vara ett 3D-renderingsscenario där ramindex skickas till aktiviteten som en parameter.

> [!NOTE]
> Den inbyggda parameterhanteraren stöder endast ordlistor från sträng till sträng. Om du vill skicka komplexa JSON-värden som parametervärden måste du skicka dessa som strängar och tolka dem `Configuration.GetTaskParameters` i uppgiftsbehandlaren eller ändra ramverkets metod.
> 
> 

## <a name="next-steps"></a>Nästa steg
### <a name="persist-job-and-task-output-to-azure-storage"></a>Beständiga jobb- och uppgiftsutdata till Azure Storage
Ett annat användbart verktyg i batchlösningsutveckling är [Azure Batch File Conventions][nuget_package]. Använd det här .NET-klassbiblioteket (för närvarande i förhandsversion) i batch.NET-programmen för att enkelt lagra och hämta aktivitetsutdata till och från Azure Storage. [Persistens Azure Batch-jobb och uppgiftsutdata](batch-task-output.md) innehåller en fullständig diskussion om biblioteket och dess användning.


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
