---
title: Design effektiva List frågor – Azure Batch | Microsoft Docs
description: Öka prestanda genom att filtrera dina frågor när du begär information om batch-resurser som pooler, jobb, uppgifter och Compute-noder.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3bf9ba52bc4071755918b842da477384dcd38973
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323511"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Skapa frågor för att lista batch-resurser effektivt

Här lär du dig hur du ökar Azure Batch programmets prestanda genom att minska mängden data som returneras av tjänsten när du frågar jobb, aktiviteter, Compute-noder och andra resurser med [batch .net][api_net] -biblioteket.

Nästan alla batch-program behöver utföra någon typ av övervakning eller annan åtgärd som skickar frågor till batch-tjänsten, ofta med jämna mellanrum. Om du till exempel vill fastställa om det finns några köade uppgifter kvar i ett jobb måste du hämta data för alla aktiviteter i jobbet. Om du vill fastställa status för noder i poolen måste du hämta data på varje nod i poolen. Den här artikeln förklarar hur du kör sådana frågor på det mest effektiva sättet.

> [!NOTE]
> Batch-tjänsten tillhandahåller särskilt API-stöd för vanliga scenarier för att räkna aktiviteter i ett jobb, och beräkning av datornoder i batch-poolen. I stället för att använda en lista fråga för dessa kan du anropa åtgärderna [Hämta aktiviteter för antal][rest_get_task_counts] and [List Pool Node Counts][rest_get_node_counts] aktiviteter. Dessa åtgärder är mer effektiva än en List fråga, men returnerar mer begränsad information. Se [räkna aktiviteter och Compute-noder efter tillstånd](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Uppfylla DetailLevel
I ett batch-program för produktion kan entiteter som jobb, aktiviteter och Compute-noder numreras i tusental. När du begär information om de här resurserna måste en potentiellt stor mängd data "korsa kabeln" från batch-tjänsten till ditt program på varje fråga. Genom att begränsa antalet objekt och vilken typ av information som returneras av en fråga kan du öka hastigheten på dina frågor och därmed programmets prestanda.

Det här [batch .net][api_net] API-kodfragmentet listar *varje* aktivitet som är associerad med ett jobb, tillsammans med *alla* egenskaper för varje aktivitet:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Du kan använda en mycket mer effektiv List fråga, men genom att använda en "detalj nivå" i din fråga. Du gör detta genom att tillhandahålla en [ODATADetailLevel][odata] object to the [JobOperations.ListTasks][net_list_tasks] -metod. Det här kodfragmentet returnerar bara egenskaperna ID, kommando rad och Compute Node (information) för slutförda aktiviteter:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

I det här exempel scenariot, om det finns tusentals uppgifter i jobbet, kommer resultatet från den andra frågan vanligt vis att returnera mycket snabbare än den första. Mer information om hur du använder ODATADetailLevel när du listerar objekt med batch .NET-API: et finns [nedan](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Vi rekommenderar starkt att du *alltid* anger ett ODATADetailLevel-objekt till dina .NET API List-anrop för att säkerställa högsta effektivitet och prestanda för ditt program. Genom att ange en detalj nivå kan du hjälpa till att minska svars tiderna för batch-tjänsten, förbättra nätverks användningen och minimera minnes användningen av klient program.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrera, välja och expandera
[Batch .net][api_net] and [Batch REST][api_rest] -API: erna ger möjlighet att minska antalet objekt som returneras i en lista samt hur mycket information som returneras för var och en. Du gör detta genom att ange **filter**, **välja**och **expandera strängar** när du utför List frågor.

### <a name="filter"></a>Filter
Filter strängen är ett uttryck som minskar antalet objekt som returneras. Du kan till exempel endast lista de aktiviteter som körs för ett jobb, eller en lista med enbart datornoder som är redo att köra uppgifter.

* Filter strängen består av ett eller flera uttryck med ett uttryck som består av ett egenskaps namn, en operator och ett värde. De egenskaper som kan anges är specifika för varje entitetstyp som du frågar efter, som är de operatorer som stöds för varje egenskap.
* Flera uttryck kan kombineras med hjälp av logiska `and` operatorer `or`och.
* I den här exempel filter strängen visas bara de kör åter givnings aktiviteter `(state eq 'running') and startswith(id, 'renderTask')`som körs:.

### <a name="select"></a>Välj
Den valda strängen begränsar de egenskaps värden som returneras för varje objekt. Du kan ange en lista över egenskaps namn och bara de egenskaps värden som returneras för objekten i frågeresultatet.

* Select-strängen består av en kommaavgränsad lista med egenskaps namn. Du kan ange någon av egenskaperna för entitetstypen som du frågar.
* I det här exemplet väljer du sträng anger att endast tre egenskaps värden ska returneras för `id, state, stateTransitionTime`varje aktivitet:.

### <a name="expand"></a>Visa
Expand-strängen minskar antalet API-anrop som krävs för att hämta viss information. När du använder en Expand-sträng kan mer information om varje objekt hämtas med ett enda API-anrop. I stället för att först hämta listan över entiteter, och sedan begära information för varje objekt i listan, använder du en Expand sträng för att hämta samma information i ett enda API-anrop. Färre API-anrop innebär bättre prestanda.

* På samma sätt som med Select-strängen, kontrollerar Expand-strängen om vissa data ingår i lista frågeresultat.
* Expand strängen stöds bara när den används i ett List jobb, jobb scheman, uppgifter och pooler. För närvarande stöder den bara statistik information.
* När alla egenskaper krävs och ingen Select-sträng anges, *måste* expansions strängen användas för att hämta statistik information. Om en SELECT-sträng används för att hämta en delmängd av egenskaperna `stats` , kan anges i SELECT-strängen och den expanderade strängen behöver inte anges.
* I det här exemplet expanderas sträng anger du att statistik information ska returneras för varje objekt i `stats`listan:.

> [!NOTE]
> När du skapar någon av de tre typerna av frågesträngar (filter, Välj och expandera) måste du se till att egenskaps namnen och skiftet stämmer överens med de REST API elementens motsvarigheter. När du arbetar med .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) -klassen måste du till exempel ange **State** i stället för **State**, även om .net-egenskapen är [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Se tabellerna nedan för egenskaps mappningar mellan .NET-och REST-API: er.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regler för filter, Välj och expandera strängar
* Egenskaps namn i filter, Välj och expandera strängar bör visas i [batch-rest][api_rest] API--even when you use [Batch .NET][api_net] eller någon av de andra batch SDK: erna.
* Alla egenskaps namn är Skift läges känsliga, men egenskaps värden är Skift läges känsliga.
* Datum-och tids strängar kan vara ett av två format och måste föregås `DateTime`av.
  
  * Exempel på W3C-DTF format:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exempel på RFC 1123-format:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Booleska strängar är antingen `true` eller `false`.
* Om en ogiltig egenskap eller operator anges, uppstår ett `400 (Bad Request)` fel.

## <a name="efficient-querying-in-batch-net"></a>Effektiv fråga i batch .NET
I [batch .net][api_net] API, the [ODATADetailLevel][odata] -klassen används för att ange filter, välja och expandera strängar för att lista åtgärder. ODataDetailLevel-klassen har tre offentliga sträng egenskaper som kan anges i konstruktorn eller anges direkt för objektet. Sedan skickar du ODataDetailLevel-objektet som en parameter till de olika List åtgärder som [ListPools][net_list_pools], [ListJobs][net_list_jobs], and [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: Begränsa antalet objekt som returneras.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: Ange vilka egenskaps värden som ska returneras för varje objekt.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: Hämta data för alla objekt i ett enda API-anrop i stället för separata anrop för varje objekt.

Följande kodfragment använder batch .NET API för att effektivt fråga batch-tjänsten om statistik för en angiven uppsättning pooler. I det här scenariot har batch-användaren både test-och produktionspooler. TestPool-ID: n föregås av "test" och ID: n för produktionspooler har prefixet "Prod". I kodfragmentet är *myBatchClient* en korrekt initierad instans av [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient) -klassen.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> En instans av [ODATADetailLevel][odata] som kon figurer ATS med Select and Expand-satser kan också skickas till lämpliga get-metoder, till exempel [PoolOperations. GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), för att begränsa mängden data som returneras.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch-REST till .NET API-mappningar
Egenskaps namn i filter-, Select-och Expand-strängar *måste* reflektera sina REST API-motsvarigheter, både i namn och Skift läge. Tabellerna nedan innehåller mappningar mellan .NET och REST API motsvarigheter.

### <a name="mappings-for-filter-strings"></a>Mappningar för filter strängar
* **.Net-List metoder**: Varje .NET API-metod i den här kolumnen accepterar ett [ODATADetailLevel][odata] -objekt som en parameter.
* **Förfrågningar om rest-lista**: Varje REST API sida som är länkad till i den här kolumnen innehåller en tabell som anger de egenskaper och åtgärder som tillåts i *filter* strängar. Du kommer att använda dessa egenskaps namn och-åtgärder när du skapar en [ODATADetailLevel. FilterClause][odata_filter] -sträng.

| .NET-List metoder | REST List-begäranden |
| --- | --- |
| [Metoden certificateoperations. ListCertificates][net_list_certs] |[Lista certifikaten i ett konto][rest_list_certs] |
| [CloudTask. ListNodeFiles][net_list_task_files] |[Lista de filer som associeras med en aktivitet][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Lista status för jobb förberedelse-och jobb publicerings aktiviteter för ett jobb][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[Visa en lista över jobben i ett konto][rest_list_jobs] |
| [JobOperations. ListNodeFiles][net_list_nodefiles] |[Lista filerna på en nod][rest_list_nodefiles] |
| [JobOperations. ListTasks][net_list_tasks] |[Lista de uppgifter som är associerade med ett jobb][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Visa en lista med jobb scheman i ett konto][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Lista de jobb som är associerade med ett jobb schema][rest_list_schedule_jobs] |
| [PoolOperations. ListComputeNodes][net_list_compute_nodes] |[Visa en lista med datornoderna i en pool][rest_list_compute_nodes] |
| [PoolOperations. ListPools][net_list_pools] |[Lista pooler i ett konto][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mappningar för Select-strängar
* **Batch .net-typer**: Batch .NET API-typer.
* **REST API entiteter**: Varje sida i den här kolumnen innehåller en eller flera tabeller som visar REST API egenskaps namn för typen. Dessa egenskaps namn används när du skapar *Select* -strängar. Du kommer att använda samma egenskaps namn när du skapar en [ODATADetailLevel. SelectClause][odata_select] -sträng.

| Batch .NET-typer | REST API entiteter |
| --- | --- |
| [Certifikat][net_cert] |[Hämta information om ett certifikat][rest_get_cert] |
| [CloudJob][net_job] |[Hämta information om ett jobb][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Hämta information om ett jobb schema][rest_get_schedule] |
| [ComputeNode][net_node] |[Hämta information om en nod][rest_get_node] |
| [CloudPool][net_pool] |[Hämta information om en pool][rest_get_pool] |
| [CloudTask][net_task] |[Hämta information om en uppgift][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exempel: skapa en filter sträng
När du skapar en filter sträng för [ODATADetailLevel. FilterClause][odata_filter] , consult the table above under "Mappings for filter strings" to find the REST API documentation page that corresponds to the list operation that you wish to perform. You will find the filterable properties and their supported operators in the first multirow table on that page. If you wish to retrieve all tasks whose exit code was nonzero, for example, this row on [List the tasks associated with a job][rest_list_tasks] anger den tillämpliga egenskaps strängen och tillåtna operatorer:

| Egenskap | Tillåtna åtgärder | type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Därför blir filter strängen för att lista alla aktiviteter med en slutkod som inte är noll:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exempel: skapa en SELECT-sträng
Så här skapar du [ODATADetailLevel. SelectClause][odata_select], consult the table above under "Mappings for select strings" and navigate to the REST API page that corresponds to the type of entity that you are listing. You will find the selectable properties and their supported operators in the first multirow table on that page. If you wish to retrieve only the ID and command line for each task in a list, for example, you will find these rows in the applicable table on [Get information about a task][rest_get_task]:

| Egenskap | type | Anteckningar |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Den valda strängen för att inkludera endast ID och kommando rad med varje listad aktivitet skulle då vara:

`id, commandLine`

## <a name="code-samples"></a>Kodexempel
### <a name="efficient-list-queries-code-sample"></a>Effektiv lista frågor kod exempel
Kolla in metoden [EfficientListQueries][efficient_query_sample] sample project on GitHub to see how efficient list querying can affect performance in an application. This C# console application creates and adds a large number of tasks to a job. Then, it makes multiple calls to the [JobOperations.ListTasks][net_list_tasks] och överför [ODATADetailLevel][OData] -objekt som kon figurer ATS med olika egenskaps värden för att variera mängden data som ska returneras. Den ger utdata som liknar följande:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Som du ser i de förflutna tiderna kan du minska svars tiderna i frågan genom att begränsa egenskaperna och antalet objekt som returneras. Du kan hitta det här och andra exempel projekt i databasen [Azure-Batch-samples][github_samples] på GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics-bibliotek och kod exempel
Förutom EfficientListQueries-kod exemplet ovan kan du hitta [BatchMetrics][batch_metrics] project in the [azure-batch-samples][github_samples] GitHub-lagringsplatsen. BatchMetrics-exempelprojektet visar hur du effektivt övervakar Azure Batch jobb förlopp med batch-API: et.

[BatchMetrics][batch_metrics] -exemplet innehåller ett .NET-klass biblioteks projekt som du kan inkludera i dina egna projekt, och ett enkelt kommando rads program för att använda och demonstrera användningen av biblioteket.

Exempel programmet i projektet demonstrerar följande åtgärder:

1. Välja speciella attribut för att bara hämta de egenskaper du behöver
2. Filtrering av tillstånds över gångs tider för att bara hämta ändringar sedan den senaste frågan

Till exempel visas följande metod i BatchMetrics-biblioteket. Den returnerar en ODATADetailLevel som anger att endast `id` egenskaperna och `state` ska hämtas för de entiteter som efter frågas. Det anger också att endast entiteter vars tillstånd har ändrats sedan den `DateTime` angivna parametern ska returneras.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Nästa steg
### <a name="parallel-node-tasks"></a>Aktiviteter för parallell nod
[Maximera Azure Batch beräknings resursanvändningen med aktiviteter](batch-parallel-node-tasks.md) för samtidiga noder är en annan artikel som rör prestanda för batch-program. Vissa typer av arbets belastningar kan dra nytta av att köra parallella uppgifter på större, men färre-beräknings noder. Se [exempel scenariot](batch-parallel-node-tasks.md#example-scenario) i artikeln för information om ett sådant scenario.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
