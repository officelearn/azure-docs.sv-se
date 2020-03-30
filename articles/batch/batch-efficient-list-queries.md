---
title: Designeffektiva listfrågor – Azure Batch | Microsoft-dokument
description: Öka prestanda genom att filtrera dina frågor när du begär information om batchresurser som pooler, jobb, aktiviteter och beräkningsnoder.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022247"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Skapa frågor för att lista batchresurser effektivt

Här får du lära dig hur du ökar prestanda för Azure Batch-programmet genom att minska mängden data som returneras av tjänsten när du frågar jobb, uppgifter, beräkningsnoder och andra resurser med [batch -NET-biblioteket.][api_net]

Nästan alla Batch-program måste utföra någon typ av övervakning eller annan åtgärd som frågar batch-tjänsten, ofta med jämna mellanrum. Om du till exempel vill avgöra om det finns några köade uppgifter kvar i ett jobb måste du hämta data om varje aktivitet i jobbet. Om du vill ta reda på status för noder i poolen måste du hämta data på varje nod i poolen. I den här artikeln beskrivs hur du kör sådana frågor på det mest effektiva sättet.

> [!NOTE]
> Batch-tjänsten ger särskilt API-stöd för de vanliga scenarierna för att räkna uppgifter i ett jobb och räkna beräkningsnoder i batch-pool. I stället för att använda en listfråga för dessa kan du anropa åtgärderna [Hämta aktivitetsantal][rest_get_task_counts] och [listpoolräkna.][rest_get_node_counts] Dessa åtgärder är effektivare än en listfråga, men returnerar mer begränsad information. Se [Antal uppgifter och beräkningsnoder efter tillstånd](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Möt detaljnivån
I ett produktionsbatchprogram kan entiteter som jobb, aktiviteter och beräkningsnoder numrera i tusental. När du begär information om dessa resurser måste en potentiellt stor mängd data "korsa kabeln" från batch-tjänsten till ditt program på varje fråga. Genom att begränsa antalet objekt och typ av information som returneras av en fråga kan du öka hastigheten på dina frågor och därmed programmets prestanda.

I det här [batch-KODAVSNITTET för NET][api_net] API-kod visas *varje* aktivitet som är associerad med ett jobb, tillsammans med *alla* egenskaper för varje aktivitet:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Du kan dock utföra en mycket effektivare listfråga genom att använda en "detaljnivå" på frågan. Du gör detta genom att ange ett [ODATADetailLevel-objekt][odata] till metoden [JobOperations.ListTasks.][net_list_tasks] Det här kodavsnittet returnerar endast egenskaperna för ID, kommandorad och beräkningsnodinformation för slutförda aktiviteter:

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

I det här exemplet, om det finns tusentals aktiviteter i jobbet, returneras resultaten från den andra frågan vanligtvis mycket snabbare än den första. Mer information om hur du använder ODATADetailLevel när du listar objekt med batch .NET-API:et finns [nedan](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Vi rekommenderar starkt att du *alltid* tillhandahåller ett ODATADetailLevel-objekt till dina .NET API-listanrop för att säkerställa maximal effektivitet och prestanda för ditt program. Genom att ange en detaljnivå kan du hjälpa till att sänka svarstiderna för batchtjänster, förbättra nätverksanvändningen och minimera minnesanvändningen för klientprogram.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrera, markera och expandera
[Batch .NET-][api_net] och [Batch-REST-API:erna][api_rest] ger möjlighet att minska både antalet artiklar som returneras i en lista och mängden information som returneras för var och en. Du gör det genom att ange **filter,** **markera**och **expandera strängar** när du utför listfrågor.

### <a name="filter"></a>Filter
Filtersträngen är ett uttryck som minskar antalet objekt som returneras. Lista till exempel endast de aktiviteter som körs för ett jobb, eller lista endast beräkningsnoder som är redo att köra aktiviteter.

* Filtersträngen består av ett eller flera uttryck, med ett uttryck som består av ett egenskapsnamn, en operator och ett värde. De egenskaper som kan anges är specifika för varje entitetstyp som du frågar, liksom de operatorer som stöds för varje egenskap.
* Flera uttryck kan kombineras med hjälp `and` av `or`logiska operatorer och .
* I det här exemplet visas filtersträngen `(state eq 'running') and startswith(id, 'renderTask')`endast de drivrutinsaktiviteter som körs: .

### <a name="select"></a>Välj
Urvalssträngen begränsar egenskapsvärdena som returneras för varje artikel. Du anger en lista med egenskapsnamn och endast dessa egenskapsvärden returneras för objekten i frågeresultatet.

* Urvalssträngen består av en kommaavgränsad lista med egenskapsnamn. Du kan ange någon av egenskaperna för den entitetstyp som du frågar efter.
* Den här exempelsträngen anger att endast tre egenskapsvärden `id, state, stateTransitionTime`ska returneras för varje aktivitet: .

### <a name="expand"></a>Visa
Expandera strängen minskar antalet API-anrop som krävs för att få viss information. När du använder en expanderande sträng kan mer information om varje objekt erhållas med ett enda API-anrop. I stället för att först hämta listan över entiteter och sedan begära information för varje objekt i listan använder du en expandersträng för att hämta samma information i ett enda API-anrop. Mindre API-anrop innebär bättre prestanda.

* I likhet med urvalssträngen styr expand strängen om vissa data ingår i listfrågeresultatet.
* Expand strängen stöds bara när den används i listning jobb, jobbscheman, aktiviteter och pooler. För närvarande stöder den bara statistikinformation.
* När alla egenskaper krävs och ingen urvalssträng har angetts *måste* expandersträngen användas för att hämta statistikinformation. Om en urvalssträng används för att hämta `stats` en delmängd av egenskaper kan du ange i urvalssträngen och expandersträngen behöver inte anges.
* I det här exemplet anger expanderingssträngen att statistikinformation `stats`ska returneras för varje objekt i listan: .

> [!NOTE]
> När du konstruerar någon av de tre frågesträngtyperna (filtrera, markerar och expanderar) måste du se till att egenskapsnamnen och skiftfallet matchar dem för deras REST API-elementmotsvarigheter. När du till exempel arbetar med klassen .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) måste du ange **tillstånd** i stället för **Tillstånd**, även om egenskapen .NET är [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Se tabellerna nedan för egenskapsmappningar mellan .NET- och REST-API:erna.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regler för filter, markering och expanderande strängar
* Egenskapsnamn i filter, markering och expanderande strängar ska visas på samma sätt som i [BATCH REST][api_rest] API – även när du använder [Batch .NET][api_net] eller någon av de andra batch-SDK:erna.
* Alla egenskapsnamn är skiftlägeskänsliga, men egenskapsvärden är skiftlägesokänsliga.
* Datum-/tidssträngar kan vara ett av två format `DateTime`och måste föregås av .
  
  * Exempel på W3C-DTF-format:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Exempel på RFC 1123-format:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Boolesksträngar `true` är `false`antingen eller .
* Om en ogiltig egenskap eller operatör anges uppstår ett `400 (Bad Request)` fel.

## <a name="efficient-querying-in-batch-net"></a>Effektiv fråga i Batch .NET
I [batch .NET][api_net] API används klassen [ODATADetailLevel][odata] för att leverera filter, välja och expandera strängar till liståtgärder. Klassen ODataDetailLevel har tre offentliga strängegenskaper som kan anges i konstruktorn eller ställas in direkt på objektet. Du skickar sedan ODataDetailLevel-objektet som en parameter till de olika liståtgärderna, till exempel [ListPools][net_list_pools], [ListJobs][net_list_jobs]och [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: Begränsa antalet artiklar som returneras.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Ange vilka egenskapsvärden som returneras med varje artikel.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: Hämta data för alla objekt i ett enda API-anrop i stället för separata anrop för varje objekt.

Följande kodavsnitt använder batch.NET-API:et för att effektivt fråga batchtjänsten efter statistik för en viss uppsättning pooler. I det här fallet har batch-användaren både test- och produktionspooler. Testpool-ID:erna föregås av "test", och produktionspool-ID:erna föregås av "prod". I utdraget är *myBatchClient* en korrekt initierad instans av klassen [BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

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
> En instans av [ODATADetailLevel][odata] som har konfigurerats med Select- och Expand-satser kan också skickas till lämpliga Get-metoder, till exempel [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), för att begränsa mängden data som returneras.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST till .NET API-mappningar
Egenskapsnamn i filter, markering och expanderande strängar *måste* återspegla sina REST API-motsvarigheter, både i namn och ärende. Tabellerna nedan innehåller mappningar mellan motsvarigheterna .NET och REST API.

### <a name="mappings-for-filter-strings"></a>Mappningar för filtersträngar
* **.NET-listmetoder:** Var och en av .NET API-metoderna i den här kolumnen accepterar ett [ODATADetailLevel-objekt][odata] som en parameter.
* **REST-listbegäranden:** Varje REST API-sida som är länkad till i den här kolumnen innehåller en tabell som anger de egenskaper och åtgärder som tillåts i *filtersträngar.* Du kommer att använda dessa egenskapsnamn och åtgärder när du skapar en [ODATADetailLevel.FilterClause-sträng.][odata_filter]

| .NET-listmetoder | Begäran om REST-lista |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Lista certifikaten i ett konto][rest_list_certs] |
| [CloudTask.ListNodeFiler][net_list_task_files] |[Lista de filer som är associerade med en uppgift][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Ange status för jobbförberedelser och jobbfrisättningsuppgifter för ett jobb][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Lista jobben i ett konto][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Lista filerna på en nod][rest_list_nodefiles] |
| [JobOperations.ListTaks][net_list_tasks] |[Lista de uppgifter som är associerade med ett jobb][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Lista jobbuppläggen i ett konto][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Lista de jobb som är associerade med ett jobbschema][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Lista beräkningsnoderna i en pool][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Lista poolerna i ett konto][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mappningar för utvalda strängar
* **Batch .NET-typer:** Batch .NET API-typer.
* **REST API-entiteter:** Varje sida i den här kolumnen innehåller en eller flera tabeller som listar egenskapsnamnen för REST API för typen. Dessa egenskapsnamn används när du konstruerar *välj* strängar. Du kommer att använda samma egenskapsnamn när du skapar en [ODATADetailLevel.SelectClause-sträng.][odata_select]

| Batch .NET-typer | REST API-entiteter |
| --- | --- |
| [Certifikat][net_cert] |[Få information om ett certifikat][rest_get_cert] |
| [CloudJob (Moln)][net_job] |[Få information om ett jobb][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Få information om ett jobbschema][rest_get_schedule] |
| [ComputeNode (Beräkningsnod)][net_node] |[Få information om en nod][rest_get_node] |
| [CloudPool (moln)][net_pool] |[Få information om en pool][rest_get_pool] |
| [CloudTask][net_task] |[Få information om en uppgift][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exempel: konstruera en filtersträng
När du skapar en filtersträng för [ODATADetailLevel.FilterClause][odata_filter]läser du tabellen ovan under "Mappningar för filtersträngar" för att hitta sidan REST API-dokumentation som motsvarar den liståtgärd som du vill utföra. Du hittar de filterbara egenskaperna och deras operatorer som stöds i den första flerväxningstabellen på den sidan. Om du vill hämta alla aktiviteter vars avslutningskod inte var noll, till exempel, anger den här raden i [Lista de aktiviteter som är associerade med ett jobb][rest_list_tasks] den tillämpliga egenskapssträngen och tillåtna operatorer:

| Egenskap | Tillåtna åtgärder | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Filtersträngen för att lista alla uppgifter med en icke-nollutträdeskod skulle alltså vara:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exempel: konstruera en urvalssträng
Om du vill konstruera [ODATADetailLevel.SelectClause][odata_select]läser du tabellen ovan under "Mappningar för utvalda strängar" och navigerar till SIDAN REST API som motsvarar den typ av entitet som du listar. Du hittar de valbara egenskaperna och deras operatorer som stöds i den första flerväxningstabellen på den sidan. Om du till exempel bara vill hämta ID och kommandorad för varje aktivitet i en lista hittar du dessa rader i den tillämpliga tabellen på [Hämta information om en uppgift:][rest_get_task]

| Egenskap | Typ | Anteckningar |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Den valda strängen för att bara inkludera ID och kommandoraden för varje listad uppgift blir då:

`id, commandLine`

## <a name="code-samples"></a>Kodexempel
### <a name="efficient-list-queries-code-sample"></a>Kodexempel för effektiva listfrågor
Kolla in [exempelprojektet EfficientListQueries][efficient_query_sample] på GitHub för att se hur effektiv listfrågor kan påverka prestanda i ett program. Det här C#-konsolprogrammet skapar och lägger till ett stort antal uppgifter i ett jobb. Sedan anropas flera anrop till metoden [JobOperations.ListTasks][net_list_tasks] och [ODATADetailLevel-objekt][odata] som är konfigurerade med olika egenskapsvärden varierar mängden data som ska returneras. Den producerar utdata som liknar följande:

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

Som visas i förflutna tider kan du avsevärt sänka frågesvarstiderna genom att begränsa egenskaperna och antalet objekt som returneras. Du hittar det här och andra exempelprojekt i databasen [för azure-batch-samples][github_samples] på GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>Exempel på BatchMetrics-bibliotek och kod
Förutom kodexemplet EfficientListQueries ovan kan du hitta [BatchMetrics-projektet][batch_metrics] i GitHub-arkivet för [azure-batch-exempel.][github_samples] Exempelprojektet BatchMetrics visar hur du effektivt övervakar Azure Batch-jobbframsteg med batch-API:et.

Exemplet [BatchMetrics][batch_metrics] innehåller ett .NET-klassbiblioteksprojekt som du kan införliva i dina egna projekt och ett enkelt kommandoradsprogram för att träna och demonstrera användningen av biblioteket.

Exempelprogrammet inom projektet visar följande åtgärder:

1. Välja specifika attribut för att bara hämta de egenskaper du behöver
2. Filtrering av tillståndsövergångstider för att bara hämta ändringar sedan den senaste frågan

Följande metod visas till exempel i batchmetrikbiblioteket. Den returnerar en ODATADetailLevel som `id` `state` anger att endast egenskaperna och ska erhållas för de entiteter som efterfrågas. Det anger också att endast enheter vars tillstånd `DateTime` har ändrats sedan den angivna parametern ska returneras.

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
### <a name="parallel-node-tasks"></a>Parallella nodaktiviteter
[Maximera Azure Batch-beräkningsresursanvändning med samtidiga noduppgifter](batch-parallel-node-tasks.md) är en annan artikel som är relaterad till batchprogramprestanda. Vissa typer av arbetsbelastningar kan dra nytta av att köra parallella uppgifter på större men färre beräkningsnoder. Kolla in [exempelscenariot](batch-parallel-node-tasks.md#example-scenario) i artikeln för information om ett sådant scenario.


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
