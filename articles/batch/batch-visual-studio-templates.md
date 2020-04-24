---
title: Bygg lösningar med Visual Studio-mallar – Azure Batch | Microsoft Docs
description: Lär dig hur Visual Studio-projektmallar kan hjälpa dig att implementera och köra beräknings intensiva arbets belastningar på Azure Batch.
ms.topic: article
ms.date: 02/27/2017
ms.custom: seodec18
ms.openlocfilehash: 8e8d5be4a9f0fb5482ba6c86a8766a25e5713c09
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117530"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Använd Visual Studio-projektmallar för att komma igång med batch-lösningar

Med **Visual Studio-mallarna** **Job Manager** och Task för batch får du en kod som hjälper dig att implementera och köra beräknings intensiva arbets belastningar på batch med minsta möjliga insats. Det här dokumentet beskriver de här mallarna och ger vägledning för hur du använder dem.

> [!IMPORTANT]
> I den här artikeln beskrivs bara information som gäller för dessa två mallar och förutsätter att du är bekant med batch-tjänsten och viktiga begrepp som är relaterade till den: pooler, Compute-noder, jobb och aktiviteter, jobb Manager-uppgifter, miljövariabler och annan relevant information. Du hittar mer information i [grunderna i Azure Batch](batch-technical-overview.md) och [Översikt över batch-funktioner för utvecklare](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Översikt på hög nivå
Du kan använda mallarna jobb hanterare och uppgifts processor för att skapa två användbara komponenter:

* En Job Manager-aktivitet som implementerar en jobb delning som kan bryta ned ett jobb till flera aktiviteter som kan köras separat, parallellt.
* En uppgifts processor som kan användas för att utföra bearbetning och bearbetning efter bearbetning runt en program kommando rad.

I ett scenario med film åter givning skulle exempelvis jobb delningen förvandla ett enskilt film jobb till hundratals eller tusentals olika uppgifter som bearbetar enskilda ramar separat. På motsvarande sätt anropar uppgifts processorn åter givnings programmet och alla beroende processer som behövs för att återge varje ram, samt utföra ytterligare åtgärder (till exempel kopiera den återgivna ramen till en lagrings plats).

> [!NOTE]
> Jobb Manager-och Task processor-mallarna är oberoende av varandra, så du kan välja att använda båda eller bara en av dem, beroende på kraven för ditt beräknings jobb och på dina inställningar.
> 
> 

Som du ser i diagrammet nedan, kommer ett beräknings jobb som använder dessa mallar att gå igenom tre steg:

1. Klient koden (t. ex. program, webb tjänst osv.) skickar ett jobb till batch-tjänsten på Azure, och anger som sin jobb Manager-aktivitet jobb Manager-programmet.
2. Batch-tjänsten kör uppgiften Job Manager på en Compute-nod och jobb delnings guiden startar det angivna antalet aktiviteter för aktivitets processor, på så många beräknings noder som krävs, baserat på parametrarna och specifikationerna i jobb delnings koden.
3. Aktivitets processor aktiviteter körs oberoende parallellt för att bearbeta indata och generera utdata.

![Diagram över hur klient kod interagerar med batch-tjänsten][diagram01]

## <a name="prerequisites"></a>Krav
Du behöver följande för att kunna använda batch-mallarna:

* En dator med Visual Studio 2015 installerad. Batch-mallar stöds för närvarande endast för Visual Studio 2015.
* Batch-mallarna som är tillgängliga från [Visual Studio-galleriet][vs_gallery] som Visual Studio-tillägg. Det finns två sätt att hämta mallarna:
  
  * Installera mallarna med hjälp av dialog rutan **tillägg och uppdateringar** i Visual Studio (mer information finns i [hitta och använda Visual Studio-tillägg][vs_find_use_ext]). I dialog rutan **tillägg och uppdateringar** söker du efter och laddar ned följande två tillägg:
    
    * Azure Batch jobb hanterare med jobb delning
    * Azure Batch uppgifts processor
  * Hämta mallarna från Onlinegalleri för Visual Studio: Microsoft Azure Batch- [projektmallar][vs_gallery_templates]
* Om du planerar att använda funktionen [programpaket](batch-application-packages.md) för att distribuera jobb hanteraren och uppgifts processorn till batch-datornoder måste du länka ett lagrings konto till ditt batch-konto.

## <a name="preparation"></a>Förberedelse
Vi rekommenderar att du skapar en lösning som kan innehålla din jobb hanterare såväl som uppgifts processor, eftersom detta kan göra det enklare att dela kod mellan jobb Manager och aktivitets processor program. Följ dessa steg om du vill skapa den här lösningen:

1. Öppna Visual Studio och välj **fil** > **nytt** > **projekt**.
2. Under **mallar**, expandera **andra projekt typer**, klicka på **Visual Studio-lösningar**och välj sedan **Tom lösning**.
3. Skriv ett namn som beskriver ditt program och syftet med den här lösningen (t. ex. "LitwareBatchTaskPrograms").
4. Klicka på **OK**för att skapa den nya lösningen.

## <a name="job-manager-template"></a>Job Manager-mall
Med Job Manager-mallen kan du implementera en Job Manager-aktivitet som kan utföra följande åtgärder:

* Dela ett jobb i flera aktiviteter.
* Skicka de aktiviteter som ska köras i batch.

> [!NOTE]
> Mer information om Job Manager-aktiviteter finns i [Översikt över batch-funktioner för utvecklare](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Skapa en jobb hanterare med mallen
Följ dessa steg om du vill lägga till en jobb hanterare i lösningen som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. Högerklicka på lösningen i Solution Explorer och klicka på **Lägg till** > **nytt projekt**.
3. Klicka på **moln**under **Visual C#** och klicka sedan på **Azure Batch Job Manager med jobb delning**.
4. Ange ett namn som beskriver ditt program och identifierar det här projektet som jobb hanterare (t. ex. "LitwareJobManager").
5. Klicka på **OK**för att skapa projektet.
6. Bygg slutligen projektet för att tvinga Visual Studio att läsa in alla refererade NuGet-paket och kontrol lera att projektet är giltigt innan du börjar ändra det.

### <a name="job-manager-template-files-and-their-purpose"></a>Mallfiler för jobb hanteraren och deras syfte
När du skapar ett projekt med hjälp av Job Manager-mallen genereras tre grupper med kod filer:

* Huvud program filen (Program.cs). Detta innehåller program start punkt och undantags hantering på den översta nivån. Du behöver normalt inte ändra detta.
* Framework-katalogen. Detta innehåller de filer som är ansvariga för det "standard"-arbete som utförs av Job Manager-programmet – packa upp parametrar, lägga till aktiviteter i batch-jobbet osv. Du behöver normalt inte ändra de här filerna.
* Jobb delnings filen (JobSplitter.cs). Det är här du ska använda din programspecifika logik för att dela upp ett jobb i aktiviteter.

Naturligtvis kan du lägga till ytterligare filer som krävs för att stödja din jobb delnings kod, beroende på komplexiteten för jobb delnings logiken.

Mallen genererar också standardfiler för .NET, till exempel en. CSPROJ-fil, app. config, packages. config osv.

Resten av det här avsnittet beskriver de olika filerna och deras kod struktur och förklarar vad varje klass gör.

![Visual Studio Solution Explorer visar lösningen för Job Manager-mall][solution_explorer01]

**Framework-filer**

* `Configuration.cs`: Kapslar in inläsningen av jobb konfigurations data, till exempel batch-kontoinformation, länkade lagrings konto uppgifter, jobb-och aktivitets information och jobb parametrar. Den ger också till gång till batch-definierade miljövariabler (se miljö inställningar för aktiviteter i batch-dokumentationen) via klassen Configuration. EnvironmentVariable.
* `IConfiguration.cs`: Sammanfattar implementeringen av konfigurations klassen, så att du kan testa din jobb delning med ett förfalskat eller ett modell objekt.
* `JobManager.cs`: Dirigerar komponenterna i Job Manager-programmet. Den är ansvarig för att initiera jobb delningen, anropa jobb delningen och skicka tillbaka de uppgifter som returneras av jobb delningen till uppgifts lämnaren.
* `JobManagerException.cs`: Representerar ett fel som kräver att jobb hanteraren avslutas. JobManagerException används för att omsluta "förväntade" fel där speciell diagnostikinformation kan tillhandahållas som del av uppsägningen.
* `TaskSubmitter.cs`: Den här klassen ansvarar för att lägga till aktiviteter som returneras av jobb delningen till batch-jobbet. JobManager-klassen sammanställer sekvensen med aktiviteter i batchar för effektiv men snabbare tillägg till jobbet och anropar sedan TaskSubmitter. SubmitTasks i en bakgrunds tråd för varje batch.

**Jobb delning**

`JobSplitter.cs`: Den här klassen innehåller programspecifik logik för att dela jobbet i aktiviteter. Ramverket anropar metoden JobSplitter. Split för att hämta en serie uppgifter som den lägger till i jobbet när metoden returnerar dem. Det här är den klass där du kommer att mata in logiken för ditt jobb. Implementera metoden Split för att returnera en sekvens av CloudTask-instanser som representerar de uppgifter som du vill partitionera jobbet i.

**Project-källfiler för standard .NET-kommando raden**

* `App.config`: Konfigurations fil för standard-.NET-program.
* `Packages.config`: Standard NuGet-paket beroende fil.
* `Program.cs`: Innehåller program start punkt och undantags hantering på den översta nivån.

### <a name="implementing-the-job-splitter"></a>Implementera jobb delning
När du öppnar projekt hanterarens projekt kommer projektet att ha JobSplitter.cs-filen öppen som standard. Du kan implementera den delade logiken för aktiviteterna i arbets belastningen med hjälp av metoden Split (), Visa nedan:

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
> Avsnittet kommenterat i- `Split()` metoden är det enda avsnittet i den mall-mallkod för jobb hanteraren som är avsett att ändra genom att lägga till logiken för att dela jobben i olika aktiviteter. Om du vill ändra ett annat avsnitt i mallen, se till att du är bekant med hur batch fungerar och prova några [batch-kod exempel][github_samples].
> 
> 

Din Split ()-implementering har åtkomst till:

* Jobb parametrarna via `_parameters` fältet.
* CloudJob-objektet som representerar jobbet via `_job` fältet.
* CloudTask-objektet som representerar Job Manager-aktiviteten via `_jobManagerTask` fältet.

Din `Split()` implementering behöver inte lägga till uppgifter till jobbet direkt. I stället bör din kod returnera en sekvens av CloudTask-objekt, och dessa läggs till automatiskt i jobbet av de Ramverks klasser som anropar jobb delnings listen. Det är vanligt att använda C#: s iterator`yield return`()-funktion för att implementera jobb delningar som detta gör att aktiviteterna kan startas så snart som möjligt, i stället för att vänta på att alla aktiviteter ska beräknas.

**Jobb delnings problem**

Om din jobb delning påträffar ett fel, bör det antingen:

* Avsluta sekvensen med C# `yield break` -instruktionen, i vilket fall jobb hanteraren kommer att behandlas som lyckad. eller
* Utlös ett undantag, i vilket fall jobb hanteraren kommer att behandlas som misslyckad och kan provas igen beroende på hur klienten har konfigurerat den.

I båda fallen är alla uppgifter som redan returnerade av jobb delningen och som läggs till i batch-jobbet tillgängliga för körning. Om du inte vill att det ska hända kan du:

* Avsluta jobbet innan du återgår från jobb delnings listen
* Formulera hela aktivitets samlingen innan du returnerar den (det vill säga returnera en `ICollection<CloudTask>` eller `IList<CloudTask>` i stället för att implementera jobb delningen med en C#-iterator)
* Använd aktivitets beroenden för att göra alla aktiviteter beroende av att jobb hanteraren har slutförts

**Återförsök för jobb hanteraren**

Om det uppstår ett fel i jobb hanteraren kan det göras ett nytt försök av batch-tjänsten beroende på inställningarna för klient återförsök. I allmänhet är detta säkert, eftersom när ramverket lägger till uppgifter i jobbet ignoreras alla uppgifter som redan finns. Men om beräknings aktiviteterna är kostsamma kanske du inte vill debiteras kostnaden för att beräkna om aktiviteter som redan har lagts till i jobbet. om omkörningen inte är garanterat att generera samma aktivitets-ID, går det inte att använda funktionen "ignorera dubbletter". I dessa fall bör du utforma din jobb delning för att identifiera det arbete som redan har utförts och inte upprepa det, till exempel genom att utföra en CloudJob. ListTasks innan du börjar ge uppgifter.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Avslutnings koder och undantag i Job Manager-mallen
Avslutnings koder och undantag ger en mekanism för att fastställa resultatet av att köra ett program, och de kan hjälpa till att identifiera eventuella problem med körningen av programmet. Mallen Job Manager implementerar de slut koder och undantag som beskrivs i det här avsnittet.

En Job Manager-aktivitet som implementeras med mallen för jobb hanteraren kan returnera tre möjliga slut koder:

| Kod | Beskrivning |
| --- | --- |
| 0 |Jobb hanteraren har slutförts. Jobb delnings koden kördes för att slutföras och alla aktiviteter har lagts till i jobbet. |
| 1 |Job Manager-aktiviteten misslyckades med ett undantag i en förväntad del av programmet. Undantaget översattes till en JobManagerException med diagnostikinformation och, om möjligt, förslag för att lösa problemet. |
| 2 |Job Manager-aktiviteten misslyckades med ett oväntat undantag. Undantaget loggades till standardutdata, men jobb hanteraren kunde inte lägga till ytterligare diagnostik-eller reparations information. |

Om det uppstår ett aktivitets fel i Job Manager kan vissa aktiviteter fortfarande ha lagts till i tjänsten innan felet uppstod. De här uppgifterna kommer att köras som vanligt. Se "jobb delnings problem" ovan för diskussion av den här kod Sök vägen.

All information som returneras av undantag skrivs till stdout. txt och stderr. txt-filer. Mer information finns i [fel hantering](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Klient överväganden
I det här avsnittet beskrivs vissa klient implementerings krav när du anropar en jobb hanterare baserat på den här mallen. Information om hur du skickar parametrar och miljö inställningar finns i [så här skickar du parametrar och miljövariabler från klient koden](#pass-environment-settings) .

**Obligatoriska autentiseringsuppgifter**

För att du ska kunna lägga till uppgifter i Azure Batch-jobbet kräver jobb Manager-aktiviteten din Azure Batchs konto-URL och nyckel. Du måste skicka dessa i miljövariabler med namnet YOUR_BATCH_URL och YOUR_BATCH_KEY. Du kan ställa in dessa i inställningar för aktivitets miljön i Job Manager. Till exempel i en C#-klient:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Autentiseringsuppgifter för lagring**

Normalt behöver inte klienten ange de länkade lagrings kontots autentiseringsuppgifter för Job Manager-aktiviteten eftersom (a) de flesta jobb ansvariga inte behöver komma åt det länkade lagrings kontot och (b) det länkade lagrings kontot är ofta tillgängligt för alla aktiviteter som en gemensam miljö inställning för jobbet. Om du inte tillhandahåller det länkade lagrings kontot via de gemensamma miljö inställningarna och jobb hanteraren kräver åtkomst till länkad lagring, ska du ange de länkade autentiseringsuppgifterna för lagring på följande sätt:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Aktivitets inställningar för jobb hanteraren**

Klienten ska ställa in jobb hanterarens *killJobOnCompletion* -flagga på **falskt**.

Det är vanligt vis säkert att klienten anger *runExclusive* till **false**.

Klienten bör använda *resourceFiles* -eller *applicationPackageReferences* -samlingen för att låta köras av jobb hanteraren (och de DLL-filer som krävs) distribueras till Compute-noden.

Som standard kommer jobb hanteraren inte att göras om den Miss lyckas. Beroende på din jobbs Manager-logik kanske klienten vill aktivera återförsök via *begränsningar*/*maxTaskRetryCount*.

**Jobbinställningar**

Om jobb delningen avger aktiviteter med beroenden måste klienten ange jobbets usesTaskDependencies till true.

I jobb delnings modellen är det ovanligt att klienter vill lägga till uppgifter till jobb över och ovanför vad jobb delningen skapar. Klienten bör därför normalt ange jobbets *onAllTasksComplete* till **terminatejob**.

## <a name="task-processor-template"></a>Uppgifts processor mall
Med en uppgifts processor mall kan du implementera en uppgifts processor som kan utföra följande åtgärder:

* Konfigurera den information som krävs för varje batch-uppgift som ska köras.
* Kör alla åtgärder som krävs av varje batch-aktivitet.
* Spara Uppgiftsutdata i beständig lagring.

Även om det inte krävs någon uppgifts processor för att köra aktiviteter i batch, är fördelen med att använda en uppgifts processor att det innehåller en omslutning för att implementera alla aktiviteter för körning på en plats. Om du till exempel behöver köra flera program i kontexten för varje aktivitet, eller om du behöver kopiera data till beständig lagring när du har slutfört varje aktivitet.

De åtgärder som utförs av uppgifts processorn kan vara enkla eller komplexa och så många eller så få, som krävs av din arbets belastning. Genom att implementera alla uppgifts åtgärder i en uppgifts processor kan du dessutom enkelt uppdatera eller lägga till åtgärder baserat på ändringar i program eller arbets belastnings krav. I vissa fall kanske en uppgifts processor inte är den optimala lösningen för din implementering eftersom den kan lägga till onödig komplexitet, till exempel när du kör jobb som snabbt kan startas från en enkel kommando rad.

### <a name="create-a-task-processor-using-the-template"></a>Skapa en uppgifts processor med mallen
Följ dessa steg om du vill lägga till en uppgifts processor till den lösning som du skapade tidigare:

1. Öppna din befintliga lösning i Visual Studio.
2. I Solution Explorer högerklickar du på lösningen, klickar på **Lägg till**och klickar sedan på **nytt projekt**.
3. Klicka på **moln**under **Visual C#** och klicka sedan på **Azure Batch aktivitets processor**.
4. Ange ett namn som beskriver ditt program och identifierar det här projektet som uppgifts processor (t. ex. "LitwareTaskProcessor").
5. Klicka på **OK**för att skapa projektet.
6. Bygg slutligen projektet för att tvinga Visual Studio att läsa in alla refererade NuGet-paket och kontrol lera att projektet är giltigt innan du börjar ändra det.

### <a name="task-processor-template-files-and-their-purpose"></a>Mallfiler för aktivitets processor och deras syfte
När du skapar ett projekt med hjälp av uppgifts processor mal len genereras tre grupper med kod filer:

* Huvud program filen (Program.cs). Detta innehåller program start punkt och undantags hantering på den översta nivån. Du behöver normalt inte ändra detta.
* Framework-katalogen. Detta innehåller de filer som är ansvariga för det "standard"-arbete som utförs av Job Manager-programmet – packa upp parametrar, lägga till aktiviteter i batch-jobbet osv. Du behöver normalt inte ändra de här filerna.
* Aktivitetens processor fil (TaskProcessor.cs). Det är här du ska använda din programspecifika logik för att köra en aktivitet (vanligt vis genom att anropa till en befintlig körbar fil). Kod för för-och efter bearbetning, t. ex. att hämta ytterligare data eller ladda upp resultat filer, visas också här.

Naturligtvis kan du lägga till ytterligare filer som krävs för att stödja din uppgifts processor kod, beroende på komplexiteten för jobb delnings logiken.

Mallen genererar också standardfiler för .NET, till exempel en. CSPROJ-fil, app. config, packages. config osv.

Resten av det här avsnittet beskriver de olika filerna och deras kod struktur och förklarar vad varje klass gör.

![Visual Studio Solution Explorer som visar lösningen för aktivitets processor mal len][solution_explorer02]

**Framework-filer**

* `Configuration.cs`: Kapslar in inläsningen av jobb konfigurations data, till exempel batch-kontoinformation, länkade lagrings konto uppgifter, jobb-och aktivitets information och jobb parametrar. Den ger också till gång till batch-definierade miljövariabler (se miljö inställningar för aktiviteter i batch-dokumentationen) via klassen Configuration. EnvironmentVariable.
* `IConfiguration.cs`: Sammanfattar implementeringen av konfigurations klassen, så att du kan testa din jobb delning med ett förfalskat eller ett modell objekt.
* `TaskProcessorException.cs`: Representerar ett fel som kräver att jobb hanteraren avslutas. TaskProcessorException används för att omsluta "förväntade" fel där speciell diagnostikinformation kan tillhandahållas som del av uppsägningen.

**Uppgifts processor**

* `TaskProcessor.cs`: Kör uppgiften. Ramverket anropar metoden TaskProcessor. Run. Detta är den klass där du kommer att mata in den programspecifika logiken för uppgiften. Implementera metoden Run för att:
  * Parsa och validera eventuella aktivitets parametrar
  * Skriv kommando raden för alla externa program som du vill anropa
  * Logga all diagnostikinformation som du kan behöva för fel sökning
  * Starta en process med den kommando raden
  * Vänta tills processen avslutas
  * Avbilda avslutnings koden för processen för att avgöra om den lyckades eller misslyckades
  * Spara alla utdatafiler som du vill behålla till beständig lagring

**Project-källfiler för standard .NET-kommando raden**

* `App.config`: Konfigurations fil för standard-.NET-program.
* `Packages.config`: Standard NuGet-paket beroende fil.
* `Program.cs`: Innehåller program start punkt och undantags hantering på den översta nivån.

## <a name="implementing-the-task-processor"></a>Implementera uppgifts processorn
När du öppnar projektet för uppgifts bearbetnings mal len kommer TaskProcessor.cs-filen vara öppen som standard. Du kan implementera körnings logiken för aktiviteterna i arbets belastningen med hjälp av metoden Run () som visas nedan:

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
> Avsnittet kommenterat i metoden Run () är det enda avsnittet i den mallkod för aktivitets processor som är avsett att ändra genom att lägga till körnings logiken för aktiviteterna i din arbets belastning. Om du vill ändra ett annat avsnitt i mallen måste du först bekanta dig med hur batch fungerar genom att granska batch-dokumentationen och prova några batch-kod exempel.
> 
> 

Metoden Run () ansvarar för att starta kommando raden, starta en eller flera processer, väntar på att processen ska slutföras, Spara resultaten och slutligen returnera med en slutkod. Metoden Run () är den plats där du implementerar bearbetnings logiken för dina aktiviteter. Aktivitets processor ramverket anropar metoden Run () för dig. du behöver inte anropa det själv.

Implementeringen () har åtkomst till:

* Aktivitets parametrarna via `_parameters` fältet.
* Jobb-och aktivitets-ID: n `_jobId` , `_taskId` via fälten och.
* Aktivitets konfigurationen via `_configuration` fältet.

**Uppgifts haverie**

Om det uppstår fel kan du avsluta metoden Run () genom att generera ett undantag, men detta gör att undantags hanteraren på den översta nivån styrs av slut koden för aktiviteten. Om du behöver kontrol lera slut koden så att du kan skilja olika typer av fel, till exempel för diagnostik eller eftersom vissa fellägen ska avsluta jobbet och andra inte ska, bör du avsluta körnings metoden genom att returnera en slutkod som inte är noll. Detta blir slut koden för uppgiften.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Avslutnings koder och undantag i uppgifts processor mal len
Avslutnings koder och undantag ger en mekanism för att fastställa resultatet av att köra ett program, och de kan hjälpa dig att identifiera eventuella problem med körningen av programmet. Uppgifts processor mal len implementerar de slut koder och undantag som beskrivs i det här avsnittet.

En uppgifts processor aktivitet som implementeras med uppgifts processor mal len kan returnera tre möjliga slut koder:

| Kod | Beskrivning |
| --- | --- |
| [Process. ExitCode][process_exitcode] |Aktivitets processorn kördes. Observera att detta inte innebär att det program du anropade lyckades – endast att aktivitets processorn anropade det och utförde efter bearbetning utan undantag. Innebörden av slut koden beror på vilket program som anropas – vanligt vis avslutar kod 0 betyder att programmet lyckades och andra slut koder innebär att programmet misslyckades. |
| 1 |Uppgifts processorn misslyckades med ett undantag i en förväntad del av programmet. Undantaget översattes till en `TaskProcessorException` med diagnostikinformation och, där det är möjligt, förslag på att lösa problemet. |
| 2 |Uppgifts processorn misslyckades med ett oväntat undantag. Undantaget loggades till standardutdata, men uppgifts processorn kunde inte lägga till ytterligare diagnostik-eller reparations information. |

> [!NOTE]
> Om programmet som du anropar använder slut koderna 1 och 2 för att ange vissa fel lägen, är det tvetydigt att använda slut koderna 1 och 2 för aktivitets processor fel. Du kan ändra aktivitets processorns felkoder till olika avsluts koder genom att redigera undantags fallen i filen Program.cs.
> 
> 

All information som returneras av undantag skrivs till stdout. txt och stderr. txt-filer. Mer information finns i fel hantering i batch-dokumentationen.

### <a name="client-considerations"></a>Klient överväganden
**Autentiseringsuppgifter för lagring**

Om din uppgifts processor använder Azure Blob Storage för att spara utdata, t. ex. genom att använda fil konventionernas bibliotek, behöver det *antingen* åtkomst till moln lagrings kontots autentiseringsuppgifter *eller* en BLOB container-URL som innehåller en signatur för delad åtkomst (SAS). Mallen innehåller stöd för att ange autentiseringsuppgifter via vanliga miljövariabler. Klienten kan överföra autentiseringsuppgifterna för lagring på följande sätt:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Lagrings kontot är sedan tillgängligt i TaskProcessor-klassen via `_configuration.StorageAccount` egenskapen.

Om du föredrar att använda en behållar-URL med SAS kan du också skicka detta via en arbets miljö inställning för jobb, men för närvarande inkluderar inte inbyggt stöd för det här.

**Lagrings konfiguration**

Vi rekommenderar att du skapar alla behållare som krävs av aktiviteter innan aktiviteterna läggs till i jobbet. Detta är obligatoriskt om du använder en behållar-URL med SAS, eftersom en sådan URL inte innehåller behörighet att skapa behållaren. Det rekommenderas även om du skickar autentiseringsuppgifter för lagrings kontot, eftersom det sparar varje aktivitet som måste anropa CloudBlobContainer. CreateIfNotExistsAsync på behållaren.

## <a name="pass-parameters-and-environment-variables"></a>Skicka parametrar och miljövariabler
### <a name="pass-environment-settings"></a>Pass miljö inställningar
En klient kan skicka information till Job Manager-aktiviteten i form av miljö inställningar. Den här informationen kan sedan användas av Job Manager-aktiviteten när du genererar uppgifter för uppgifts bearbetning som ska köras som en del av beräknings jobbet. Exempel på den information som du kan skicka som miljö inställningar är:

* Lagrings konto namn och konto nycklar
* Batch-kontots URL
* Batch-konto nyckel

Batch-tjänsten har en enkel mekanism för att överföra miljö inställningar till en Job Manager-aktivitet genom `EnvironmentSettings` att använda egenskapen i [Microsoft. Azure. batch. JobManagerTask][net_jobmanagertask].

Om du till exempel vill hämta `BatchClient` instansen för ett batch-konto kan du skicka som miljövariabler från klient koden till URL: en och autentiseringsuppgifterna för delad nyckel för batch-kontot. Om du vill komma åt det lagrings konto som är länkat till batch-kontot kan du också skicka lagrings kontots namn och lagrings konto nyckeln som miljövariabler.

### <a name="pass-parameters-to-the-job-manager-template"></a>Skicka parametrar till Job Manager-mallen
I många fall är det praktiskt att skicka parametrar för varje jobb till Job Manager-aktiviteten, antingen för att kontrol lera jobb delnings processen eller konfigurera aktiviteter för jobbet. Det kan du göra genom att ladda upp en JSON-fil med namnet Parameters. JSON som en resurs fil för Job Manager-aktiviteten. Parametrarna kan sedan bli tillgängliga i `JobSplitter._parameters` fältet i Job Manager-mallen.

> [!NOTE]
> Den inbyggda parameter hanteraren har endast stöd för sträng-till-sträng-ordlistor. Om du vill skicka komplexa JSON-värden som parameter värden måste du skicka dem som strängar och analysera dem i jobb delningen eller ändra ramverkets `Configuration.GetJobParameters` metod.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Skicka parametrar till uppgifts processor mal len
Du kan också skicka parametrar till enskilda uppgifter som implementeras med hjälp av uppgifts processor mal len. Precis som med Job Manager-mallen söker aktivitets processor mal len efter en resurs fil med namnet

Parameters. JSON, och om det hittas läses det in som parameter ord listan. Det finns ett par alternativ för att skicka parametrar till aktivitets processor aktiviteterna:

* Återanvänd JSON för jobb parametrar. Detta fungerar bra om de enda parametrarna är jobb breda (till exempel en åter givnings höjd och bredd). När du skapar en CloudTask i jobb delnings guiden lägger du till en referens till objektet Parameters. JSON-resurs från jobb hanterarens ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) till CloudTask ResourceFiles-samling.
* Generera och ladda upp ett verksamhetsspecifika Parameters. JSON-dokument som en del av jobb delnings körningen och referera till blobben i aktivitetens resurs fil samling. Detta är nödvändigt om olika aktiviteter har olika parametrar. Ett exempel kan vara ett 3D-rendering-scenario där ram indexet skickas till aktiviteten som en parameter.

> [!NOTE]
> Den inbyggda parameter hanteraren har endast stöd för sträng-till-sträng-ordlistor. Om du vill skicka komplexa JSON-värden som parameter värden måste du skicka dem som strängar och analysera dem i uppgifts processorn eller ändra ramverkets `Configuration.GetTaskParameters` metod.
> 
> 

## <a name="next-steps"></a>Nästa steg
### <a name="persist-job-and-task-output-to-azure-storage"></a>Spara jobb och Uppgiftsutdata i Azure Storage
Ett annat användbart verktyg för utveckling av batch-lösningar är [Azure batch fil konventioner][nuget_package]. Använd det här .NET-klass biblioteket (för närvarande i för hands version) i batch .NET-program för att enkelt lagra och hämta Uppgiftsutdata till och från Azure Storage. [Behåll Azure batch jobb-och Uppgiftsutdata](batch-task-output.md) innehåller en fullständig diskussion av biblioteket och dess användning.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://github.com/Azure/batch-extension-templates
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
