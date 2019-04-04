---
title: 'Utforma effektiv lista: frågor – Azure Batch | Microsoft Docs'
description: Öka prestandan genom att filtrera dina frågor när du begär information om Batch-resurser som pooler, jobb, uppgifter och beräkningsnoder.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
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
ms.openlocfilehash: ff3e95a603b8f9a188c7839578cd12287935de90
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918543"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Skapa frågor för att lista Batch-resurser effektivt

Här får du lära dig hur du ökar din Azure Batch-programmets prestanda genom att minska mängden data som returneras av tjänsten när du frågar jobb, uppgifter, compute-noder och andra resurser med den [Batch .NET] [ api_net] biblioteket.

Nästan alla Batch-program behöver utföra vissa typer av övervakning eller andra åtgärder som frågar Batch-tjänsten ofta med jämna mellanrum. Till exempel för att avgöra om det finns några köade aktiviteter som är kvar i ett jobb, måste du hämta data i varje aktivitet i jobbet. För att avgöra statusen för noderna i poolen, måste du hämta data på varje nod i poolen. Den här artikeln förklarar hur du kör sådana frågor i det mest effektiva sättet.

> [!NOTE]
> Batch-tjänsten innehåller särskilda API-stöd för vanliga scenarier för inventering aktiviteterna i ett jobb och räkna beräkningsnoder i Batch-pool. Istället för att använda en listfråga för dessa, kan du anropa den [hämta uppgift räknar] [ rest_get_task_counts] och [lista Pool noden räknar] [ rest_get_node_counts] åtgärder. Dessa åtgärder är mer effektiva än en listfråga, men returnerar begränsad mer information. Se [antal uppgifter och beräkningsnoder efter delstat](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Uppfyll DetailLevel
I en Batch-program numrera entiteter som jobb, uppgifter och beräkningsnoder i tusental. När du begär information om dessa resurser kan måste en potentiellt stora mängder data ”över kabeln” från Batch-tjänsten till ditt program på varje fråga. Genom att begränsa antalet objekt och typ av information som returneras av en fråga, kan du öka hastigheten på dina frågor och därför programmets prestanda.

Detta [Batch .NET] [ api_net] API kod kodfragment listor *varje* aktivitet som är associerad med ett jobb, tillsammans med *alla* av egenskaperna för varje aktivitet:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Du kan utföra en mycket effektivare lista-fråga, men genom att använda en ”detaljnivå” för frågan. Du gör detta genom att tillhandahålla en [ODATADetailLevel] [ odata] objekt till den [JobOperations.ListTasks] [ net_list_tasks] metod. Det här kodfragmentet returnerar endast ID, kommandoraden och beräkningsegenskaperna noden information för slutförda uppgifter:

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

I det här exemplet, om det finns tusentals aktiviteter i jobbet, resultaten från den andra frågan vanligtvis returneras mycket snabbare än först. Mer information om hur du använder ODATADetailLevel när du listar objekt med Batch .NET API: et ingår [nedan](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Vi rekommenderar starkt att som du *alltid* ange objektets ODATADetailLevel till din .NET API-anrop för listan att säkerställa maximal effektivitet och programmets prestanda. Genom att ange en detaljnivå kan hjälpa du att minska svarstiderna för Batch-tjänsten, förbättra belastningen på nätverket och minimera minnesanvändning av klientprogram.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrera, markera och utöka
Den [Batch .NET] [ api_net] och [Batch REST] [ api_rest] API: er ger möjlighet att minska både antalet objekt som returneras i en lista, samt mängden information som returneras för varje. Det gör du genom att ange **filter**, **Välj**, och **Expandera strängar** när du utför listfrågor.

### <a name="filter"></a>Filter
Filtersträngen är ett uttryck som minskar antalet objekt som returneras. Till exempel listan endast aktiviteterna för ett jobb som körs eller lista endast compute-noder som är redo att köra aktiviteter.

* Filtersträngen består av en eller flera uttryck med ett uttryck som består av ett egenskapsnamn, en operator och ett värde. De egenskaper som kan anges är specifika för varje enhetstyp av du fråga eftersom operatorer som stöds för varje egenskap.
* Flera uttryck kan kombineras med hjälp av de logiska operatorerna `and` och `or`.
* Det här exemplet filtrera strängar endast körning ”återge” uppgifter: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Välj
Välj strängen begränsar de värden som returneras för varje objekt. Du anger en lista över egenskapsnamn och endast de värdena som returneras för objekt i resultatet av frågan.

* Välj strängen består av en kommaavgränsad lista med egenskapsnamn. Du kan ange någon av egenskaperna för den enhetstyp som du frågar.
* Det här exemplet väljer sträng som anger att endast tre värden ska returneras för varje aktivitet: `id, state, stateTransitionTime`.

### <a name="expand"></a>Visa
Expandera strängen minskar antalet API-anrop som krävs för att erhålla viss information. När du använder en Expandera sträng, mer information om varje objekt hämtas med ett enda API-anrop. I stället för att först hämta listan över enheter som begär information för varje objekt i listan, använder du en Expandera sträng för att få samma information i ett enda API-anrop. Mindre API-anrop innebär bättre prestanda.

* Liknande till strängen som du väljer, expandera strängen styr om vissa data ska tas med i listan frågeresultat.
* Expandera strängen stöds endast när den används i lista jobb, scheman, uppgifter och pooler. Det stöder för närvarande endast statistikinformation.
* När alla egenskaper som krävs och ingen väljer sträng anges, expandera strängen *måste* användas för att få statistikinformation. Om en väljer sträng används för att hämta en delmängd av egenskaperna, sedan `stats` kan anges i väljer strängen och expandera strängen behöver inte anges.
* Det här exemplet Expandera sträng Anger att statistikinformation ska returneras för varje objekt i listan: `stats`.

> [!NOTE]
> När någon av tre fråga strängtyper (filtrera, markera och expandera), måste du se till att egenskapsnamn och fallet matchar det som deras motsvarigheter för REST API-element. Till exempel när du arbetar med .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask) klass, måste du ange **tillstånd** i stället för **tillstånd**, även om .NET-egenskapen är [ CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask.state). Finns i tabellen nedan för egenskapsmappningar mellan .NET och REST API: er.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regler för filter, markera och utöka strängar
* Egenskapsnamn i filtret, markera och utöka strängar som ska visas som i den [Batch REST] [ api_rest] API – även när du använder [Batch .NET] [ api_net]eller någon av de andra Batch SDK: er.
* Alla egenskapsnamn är skiftlägeskänsliga, men värden är skiftlägeskänsliga.
* Datum/tid strängar kan vara något av två format och måste föregås med `DateTime`.
  
  * W3C-DTF format-exempel: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 format-exempel: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Booleska strängar är antingen `true` eller `false`.
* Om en ogiltig egenskap eller operator har angetts en `400 (Bad Request)` fel.

## <a name="efficient-querying-in-batch-net"></a>Effektiva frågor i Batch .NET
I den [Batch .NET] [ api_net] API, den [ODATADetailLevel] [ odata] klassen används för att tillhandahålla filter, markera och utöka strängar att lista åtgärder. ODataDetailLevel klassen har tre offentliga strängegenskaper som kan anges i konstruktorn eller ange direkt i objektet. Du sedan skicka ODataDetailLevel-objektet som en parameter till olika åtgärder i listan som [ListPools][net_list_pools], [ListJobs][net_list_jobs], och [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: Begränsa antalet objekt som returneras.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Ange vilka egenskapsvärden som returneras med varje objekt.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: Hämta data för alla objekt i ett enda API-anrop i stället för separata anrop för varje objekt.

Följande kodavsnitt använder Batch .NET-API för att effektivt fråga Batch-tjänsten för statistik för en specifik uppsättning pooler. I det här scenariot har Batch-användaren både test- och pooler. Testa poolen ID: N har prefixet ”test” och produktionspoolen ID: N har prefixet ”prod”. I kodfragmentet *myBatchClient* är en korrekt initierad instans av den [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient#microsoft_azure_batch_batchclient) klass.

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
> En instans av [ODATADetailLevel] [ odata] som har konfigurerats med särskilda och expandera-satser kan även skickas till lämplig Get-metoder, till exempel [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), för att begränsa mängden data som returneras.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST i .NET API-mappningarna
Egenskapsnamn i filtret, markera och utöka strängar *måste* återspeglar motsvarigheterna REST API, både namn och en fallet. I tabellerna nedan innehåller mappningar mellan .NET och REST API-motsvarigheter.

### <a name="mappings-for-filter-strings"></a>Mappningar för filtersträngar
* **Metoder för .NET-listan**: Var och en av .NET API-metoder i den här kolumnen accepterar en [ODATADetailLevel] [ odata] objektet som en parameter.
* **REST-begäranden för listan**: Varje REST API-sida som är länkad till i den här kolumnen innehåller en tabell som anger egenskaper och åtgärder som är tillåtna i *filter* strängar. Du använder dessa egenskapsnamn och åtgärder när du skapar en [ODATADetailLevel.FilterClause] [ odata_filter] sträng.

| Metoder för .NET-lista | REST-begäranden för listan |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Lista över certifikat i ett konto][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Lista över de filer som är associerade med en aktivitet][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Lista statusen för jobbförberedelse- och jobbpubliceringsaktiviteter för ett jobb][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Lista jobb på ett konto][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Lista över filer i en nod][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Lista de uppgifter som är associerade med ett jobb][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Lista jobbscheman i ett konto][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Lista över jobb som är associerade med ett Jobbschema][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Lista över beräkningsnoderna i poolen][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Lista pooler i ett konto][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mappningar för väljer strängar
* **Batch .NET-typerna**: Batch .NET API-typer.
* **REST API-entiteter**: Varje sida i den här kolumnen innehåller en eller flera tabeller med REST API-egenskapsnamn för typen. Dessa egenskapsnamn som används när du skapar *Välj* strängar. Du kommer att använda dessa samma egenskapsnamn när du skapar en [ODATADetailLevel.SelectClause] [ odata_select] sträng.

| Batch .NET-typer | REST API-entiteter |
| --- | --- |
| [Certifikat][net_cert] |[Få information om ett certifikat][rest_get_cert] |
| [CloudJob][net_job] |[Få information om ett jobb][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Få information om ett Jobbschema][rest_get_schedule] |
| [ComputeNode][net_node] |[Få information om en nod][rest_get_node] |
| [CloudPool][net_pool] |[Få information om en pool][rest_get_pool] |
| [CloudTask][net_task] |[Hämta information om en aktivitet][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exempel: skapa en Filtersträng
När du skapar en Filtersträng för [ODATADetailLevel.FilterClause][odata_filter], med hjälp av tabellen ovan under ”mappningar för filtersträngar” att hitta REST API-dokumentationssida som motsvarar den liståtgärden som du vill utföra. Du hittar filtrerbara egenskaper och deras operatorer som stöds i tabellen för första försök för på sidan. Om du vill hämta alla uppgifter vars avslutskoden var inte är noll, till exempel detta rad på [lista de uppgifter som är associerade med ett jobb] [ rest_list_tasks] anger egenskapssträng och tillåten operatorer:

| Egenskap  | Tillåtna åtgärder | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Därför är Filtersträngen för att lista alla uppgifter med en slutkod:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exempel: skapa en väljer sträng
Att konstruera [ODATADetailLevel.SelectClause][odata_select], med hjälp av tabellen ovan under ”mappningar för väljer strängar” och gå till sidan REST API som motsvarar typ av enhet som du visar. Du hittar valbar egenskaperna och deras operatorer som stöds i tabellen för första försök för på sidan. Om du vill hämta endast ID och kommandoraden för varje aktivitet i en lista, till exempel du hittar dessa rader i tabellen gäller på [hämta information om en aktivitet][rest_get_task]:

| Egenskap  | Type | Anteckningar |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Välj strängen för att inkludera endast ID och kommandoraden med uppgifterna listan blir:

`id, commandLine`

## <a name="code-samples"></a>Kodexempel
### <a name="efficient-list-queries-code-sample"></a>Effektiv lista: frågor kodexempel
Kolla in den [EfficientListQueries] [ efficient_query_sample] exempelprojektet på GitHub för att se hur effektiva lista frågor kan påverka prestanda i ett program. Den här C#-konsolprogram som skapar och lägger till ett stort antal aktiviteter till ett jobb. Sedan det gör att flera anrop till den [JobOperations.ListTasks] [ net_list_tasks] metoden och pass [ODATADetailLevel] [ odata] objekt som är konfigurerad med olika egenskapsvärden för att variera mängden data som ska returneras. Det ger utdata som liknar följande:

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

Du kan avsevärt sänka frågesvarstiderna genom att begränsa egenskaperna och antalet objekt som returneras i förfluten tiderna visas. Du hittar det här och andra exempelprojekt i den [azure-batch-samples] [ github_samples] arkivet på GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics bibliotek och kod exemplet
Förutom den EfficientListQueries kodexemplet ovan, du kan hitta den [BatchMetrics] [ batch_metrics] projekt i den [azure-batch-samples] [ github_samples]GitHub-lagringsplatsen. Exempelprojektet BatchMetrics visar hur du effektivt kan övervaka förloppet för Azure Batch med hjälp av Batch-API.

Den [BatchMetrics] [ batch_metrics] exemplet innehåller en .NET-klassbiblioteksprojektet som du kan införliva i ditt eget projekt och en enkel kommandoradsprogram att utöva och demonstrerar användningen av biblioteket.

Exempelprogrammet i projektet visas följande åtgärder:

1. Att välja specifika attribut för att kunna hämta bara de egenskaper som du behöver
2. Filtrera efter tillstånd övergången gånger för att kunna hämta endast ändringar sedan den senaste frågan

Följande metod visas till exempel i BatchMetrics-biblioteket. Returnerar en ODATADetailLevel som anger att endast den `id` och `state` egenskaper ska hämtas för de entiteter som efterfrågas. Den anger också som endast de enheter vars tillstånd har ändrats sedan det angivna `DateTime` parametern ska returneras.

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
### <a name="parallel-node-tasks"></a>Parallell nodaktiviteter
[Maximera resursanvändningen för Azure Batch compute med samtidiga nodaktiviteter](batch-parallel-node-tasks.md) är en annan artikel som är relaterade till Batch-programmets prestanda. Vissa typer av arbetsbelastningar kan dra nytta av köra parallella uppgifter på större-- men färre--compute-noder. Kolla in den [Exempelscenario](batch-parallel-node-tasks.md#example-scenario) i artikeln för information om sådant scenario.


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
