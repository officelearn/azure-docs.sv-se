---
title: Utforma frågor för effektiva lista - Azure Batch | Microsoft Docs
description: Öka prestandan genom att filtrera dina frågor när du begär information om Batch-resurser som pooler, jobb, uppgifter och beräkningsnoder.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 08/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 330350d6ac6838ea5b09763fe1f73fab1934710c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Skapa frågor till listan Batch resurser effektivt

Här du lär dig hur du ökar Azure Batch-programmets prestanda genom att minska mängden data som returneras av tjänsten när du frågar projekt, uppgifter och beräkningsnoder med den [Batch .NET] [ api_net]bibliotek.

Nästan alla Batch-program behöver utföra vissa typer av övervakning eller andra åtgärder som frågar ofta Batch-tjänsten med jämna mellanrum. För att fastställa om det finns kvar i ett jobb köade aktiviteter, måste du hämta data för varje aktivitet i jobbet. För att avgöra status för noder i din pool, måste du hämta data på varje nod i poolen. Den här artikeln beskriver hur du kör dessa frågor i det mest effektiva sättet.

> [!NOTE]
> Batch-tjänsten innehåller särskilda API-stöd för ett vanligt scenario för inventering aktiviteter i ett projekt. Istället för att använda en fråga för dessa, kan du anropa den [hämta uppgift räknar] [ rest_get_task_counts] igen. Hämta uppgift antal anger hur många aktiviteter väntar, kör eller slutföra och hur många aktiviteter har lyckades eller misslyckades. Hämta uppgift räknar är effektivare än en fråga. Mer information finns i [antal uppgifter för ett jobb efter status (förhandsgranskning)](batch-get-task-counts.md). 
>
> Hämta uppgift räknar-åtgärden är inte tillgänglig i Batch-tjänsten-versioner tidigare än 2017-06-01.5.1. Om du använder en äldre version av tjänsten använder du en fråga för att räkna antalet aktiviteter i ett projekt i stället.
>
> 

## <a name="meet-the-detaillevel"></a>Uppfyller DetailLevel
I produktion Batch programmet numrera entiteter t.ex projekt, uppgifter och beräkningsnoder i tusentalsavgränsare. När du begär information om dessa resurser kan måste potentiellt stora mängder data ”över kabeln” från Batch-tjänsten till ditt program för varje fråga. Genom att begränsa antalet objekt och typ av information som returneras av en fråga, kan du öka hastigheten på dina frågor och därför prestanda för ditt program.

Detta [Batch .NET] [ api_net] API kodfragment visar *varje* aktivitet som är associerad med ett jobb, tillsammans med *alla* egenskaper för varje aktivitet:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Du kan utföra ett mycket effektivare listfrågan dock genom att använda ”detaljnivån” i frågan. Du kan göra detta genom att tillhandahålla en [ODATADetailLevel] [ odata] objekt till den [JobOperations.ListTasks] [ net_list_tasks] metod. Den här fragment returnerar endast ID, kommandoraden och noden egenskaper för beräkning av slutförda aktiviteter:

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

I det här exemplet, om det finns flera tusen uppgifter i jobbet, resultaten från den andra frågan vanligtvis returneras mycket snabbare än först. Mer information om hur du använder ODATADetailLevel när listobjekt med Batch .NET API ingår [under](#efficient-querying-in-batch-net).

> [!IMPORTANT]
> Vi rekommenderar starkt att du *alltid* ange ett ODATADetailLevel objekt till .NET-API lista-anrop för att säkerställa högsta effektivitet och prestanda för ditt program. Genom att ange en detaljnivå kan du sänka svarstider för Batch-tjänsten, förbättra belastningen på nätverket och minimera minnesanvändning av klientprogram.
> 
> 

## <a name="filter-select-and-expand"></a>Filtrera, markera och utöka
Den [Batch .NET] [ api_net] och [Batch REST] [ api_rest] API: er ger möjlighet att minska både antal objekt som returneras i en lista över samt mängden information som returneras för varje. Det gör du genom att ange **filter**, **Välj**, och **Expandera strängar** när du utför listan frågor.

### <a name="filter"></a>Filter
Filtersträngen är ett uttryck som minskar antalet objekt som returneras. Till exempel visa endast aktiviteterna för ett jobb som körs eller ange bara compute-noder som är redo att köra uppgifter.

* Filtersträngen består av en eller flera uttryck med ett uttryck som består av en egenskapsnamn, en operator och ett värde. De egenskaper som kan anges är specifika för varje enhetstyp av du fråga som är operatorer som stöds för varje egenskap.
* Du kan kombinera flera uttryck med logiska operatorer `and` och `or`.
* Det här exemplet filtrera strängar endast körning ”återge” uppgifter: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Välj
Välj strängen begränsar de värden som returneras för varje objekt. Du anger en lista över egenskapsnamn och endast de värdena som returneras för objekt i resultatet av frågan.

* Välj strängen består av en kommaavgränsad lista med egenskapsnamn. Du kan ange egenskaper för entitetstypen du frågar.
* Det här exemplet väljer sträng som anger att endast tre värden ska returneras för varje aktivitet: `id, state, stateTransitionTime`.

### <a name="expand"></a>Visa
Expandera strängen minskar antalet API-anrop som krävs för att hämta viss information. När du använder en sträng visa mer information om varje objekt kan erhållas med ett enda API-anrop. I stället för att erhålla listan med enheter som begär information för varje objekt i listan, använder du en Expandera sträng för att hämta samma information i ett enda API-anrop. Mindre API-anrop innebär bättre prestanda.

* Liknar väljer strängen, expandera strängen styr om vissa data ska tas med i listan frågeresultat.
* Expandera strängen stöds endast när den används i visar en lista över jobb, jobbscheman, uppgifter och pooler. Den stöder för närvarande bara statistik.
* När alla egenskaper som krävs och ingen väljer sträng anges Expandera strängen *måste* användas för att hämta statistikinformation om. Om en sträng som du väljer används för att skaffa en underuppsättning av egenskaper, sedan `stats` kan anges i väljer strängen och expandera strängen behöver inte anges.
* Det här exemplet Expandera sträng Anger att statistik ska returneras för varje objekt i listan: `stats`.

> [!NOTE]
> När någon av tre frågan strängtyper (filtrera, markera och expandera), måste du se till att egenskapsnamn och fallet matcha motsvarigheterna REST API-element. Till exempel när du arbetar med .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) klass, måste du ange **tillstånd** i stället för **tillstånd**, även om .NET-egenskapen är [ CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Finns i tabellerna nedan för egenskapsmappningar mellan .NET och REST API: er.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regler för filter, markera och utöka strängar
* Egenskaper för namnen i filtret, markera och utöka strängar som ska visas som i den [Batch REST] [ api_rest] API – även när du använder [Batch .NET] [ api_net]eller en annan Batch SDK: erna.
* Alla egenskapsnamn är skiftlägeskänsliga, men egenskapsvärden är skiftlägeskänsligt.
* Tidsvärdet strängar kan vara något av två format och måste föregås av `DateTime`.
  
  * W3C-DTF format exempel: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 format exempel: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Booleskt strängar är antingen `true` eller `false`.
* Om en ogiltig egenskap eller en operator har angetts en `400 (Bad Request)` fel returneras.

## <a name="efficient-querying-in-batch-net"></a>Effektiva frågor skickas i Batch .NET
I den [Batch .NET] [ api_net] API, den [ODATADetailLevel] [ odata] klassen används för att tillhandahålla filter och välj utöka strängar att lista åtgärder. Klassen ODataDetailLevel har tre offentliga strängegenskaper som kan anges i konstruktorn eller ange direkt i objektet. Du sedan skickar ODataDetailLevel-objektet som en parameter till olika Liståtgärder som [ListPools][net_list_pools], [ListJobs][net_list_jobs], och [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata].[ FilterClause][odata_filter]: begränsa antalet objekt som returneras.
* [ODATADetailLevel][odata].[ SelectClause][odata_select]: Ange vilka egenskapsvärden som returneras med varje objekt.
* [ODATADetailLevel][odata].[ ExpandClause][odata_expand]: hämta data för alla objekt i ett enda API-anrop i stället för anrop för varje objekt.

Följande kodavsnitt använder Batch .NET API för effektiva frågor Batch-tjänsten för statistiken för en specifik uppsättning pooler. I det här scenariot har Batch-användaren både test- och pooler. Poolen test ID föregås ”test” och ”produktprenumeration” föregås produktionspoolen-ID: N. I fragment, *myBatchClient* är en korrekt initierad instans av den [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) klass.

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
> En instans av [ODATADetailLevel] [ odata] som har konfigurerats med utvalda och expandera-satser kan också skickas till lämpliga Get-metoder som [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), du vill begränsa mängden data som returneras.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST till .NET-API-mappningar
Egenskapsnamn i filtret, markera och expandera strängar *måste* återspeglar motsvarigheterna REST API både namn och fallet. Tabellerna nedan innehåller mappningar mellan .NET och REST API-motsvarigheter.

### <a name="mappings-for-filter-strings"></a>Mappningar för filtersträngar
* **.NET-metoder för listan**: .NET API-metoderna i den här kolumnen accepterar ett [ODATADetailLevel] [ odata] objekt som parameter.
* **Begäranden om REST**: varje REST API-sidan kopplad till i den här kolumnen innehåller en tabell som anger egenskaperna och åtgärder som är tillåtna i *filter* strängar. Du använder dessa egenskapsnamn och åtgärder när du skapar en [ODATADetailLevel.FilterClause] [ odata_filter] sträng.

| Metoder för .NET-lista | Begäranden för REST-lista |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[Visa en lista över certifikat i ett konto][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[Visa en lista över filer som hör till en aktivitet][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[Visa status för jobbförberedelse och jobbet versionen uppgifter för ett jobb][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[Lista över jobb på ett konto][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[Lista över filer i en nod][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[Lista över aktiviteter som är kopplade till ett jobb][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[Visa en lista över jobbscheman på ett konto][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[Lista över jobb som är associerade med ett Jobbschema][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[Visa en lista med beräkningsnoder i poolen][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[Visa en lista med programpooler på ett konto][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Mappningar för väljer strängar
* **Batch-.NET-typer**: Batch .NET API-typer.
* **REST API-entiteter**: varje sida i den här kolumnen innehåller en eller flera tabeller som egenskapsnamn REST API för typen. Dessa egenskapsnamn som används när du skapar *Välj* strängar. Du ska använda samma egenskapsnamn när du skapar en [ODATADetailLevel.SelectClause] [ odata_select] sträng.

| Batch .NET-typer | REST API-enheter |
| --- | --- |
| [certifikat][net_cert] |[Hämta information om ett certifikat][rest_get_cert] |
| [CloudJob][net_job] |[Hämta information om ett jobb][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Hämta information om ett Jobbschema][rest_get_schedule] |
| [ComputeNode][net_node] |[Hämta information om en nod][rest_get_node] |
| [CloudPool][net_pool] |[Hämta information om en pool][rest_get_pool] |
| [CloudTask][net_task] |[Hämta information om en aktivitet][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Exempel: skapa en Filtersträng
När du skapar en Filtersträng för [ODATADetailLevel.FilterClause][odata_filter], med hjälp av tabellen ovan under ”mappningar för filtersträngar” till REST API-dokumentationen sidan som motsvarar den lista över åtgärden som du vill utföra. Du hittar filtrera egenskaperna och deras stöds operatorer i tabellen för första försök för på sidan. Om du vill hämta alla aktiviteter vars avslutskoden var inte är noll, till exempel detta rad på [lista över aktiviteter som är kopplade till ett jobb] [ rest_list_tasks] anger gäller egenskapssträng och tillåtna operatorer:

| Egenskap | Tillåtna åtgärder | Typ |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

Därför är Filtersträngen för att lista alla aktiviteter med slutkoden noll:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Exempel: skapa en väljer sträng
Att konstruera [ODATADetailLevel.SelectClause][odata_select], med hjälp av tabellen ovan under ”mappningar för väljer strängar” och gå till sidan REST-API som motsvarar typ av enhet som du visar. Du hittar valbar egenskaperna och deras stöds operatorer i tabellen för första försök för på sidan. Om du vill hämta endast ID och kommandoraden för varje aktivitet i en lista, t.ex, du hittar dessa rader i tabellen tillämplig på [hämta information om en aktivitet][rest_get_task]:

| Egenskap | Typ | Anteckningar |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

Välj strängen för inklusive ID och kommandoraden med uppgifterna listan blir:

`id, commandLine`

## <a name="code-samples"></a>Kodexempel
### <a name="efficient-list-queries-code-sample"></a>Kodexempel för effektiv listan frågor
Kolla in den [EfficientListQueries] [ efficient_query_sample] exempelprojektet på GitHub för att se hur effektiva listan frågor kan påverka prestanda i ett program. Den här C#-konsolprogram skapar och lägger till ett stort antal aktiviteter i ett jobb. Sedan gör det flera anrop till den [JobOperations.ListTasks] [ net_list_tasks] metoden och överför [ODATADetailLevel] [ odata] objekt som är konfigurerad med olika egenskapsvärden variera mängden data som ska returneras. Den genererar utdata som liknar följande:

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

Som visas i förfluten tid minskar du avsevärt svarstider för frågan genom att begränsa egenskaper och antalet objekt som returneras. Du hittar det här och andra exempelprojekt i den [azure-batch-samples] [ github_samples] databasen på GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics bibliotek och koden exempel
Förutom den EfficientListQueries kodexemplet ovan, du kan hitta den [BatchMetrics] [ batch_metrics] projektet i den [batch-azure-exempel] [ github_samples]GitHub-lagringsplatsen. Exempelprojektet BatchMetrics visar hur du effektivt övervaka Azure Batch-jobbet med Batch-API.

Den [BatchMetrics] [ batch_metrics] exempel innehåller en .NET-klassbiblioteksprojektet som du kan införliva i dina projekt och ett enkelt kommandoradsverktyg program att utöva och demonstrerar användningen av biblioteket.

Exempelprogrammet i projektet visas följande åtgärder:

1. Att markera specifika attribut för att kunna hämta bara de egenskaper som du behöver
2. Filtrering på tillstånd övergången gånger för att kunna hämta endast ändringar sedan den senaste frågan

Till exempel visas följande metod i BatchMetrics-biblioteket. Returnerar en ODATADetailLevel som anger att endast den `id` och `state` egenskaper som ska hämtas för de entiteter som efterfrågas. Det även anger att endast de enheter vars tillstånd har ändrats sedan det angivna `DateTime` parametern ska returneras.

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
### <a name="parallel-node-tasks"></a>Parallel-nod uppgifter
[Maximera Azure Batch beräkning Resursanvändning med samtidiga nod uppgifter](batch-parallel-node-tasks.md) en annan artikel rör Batch programprestanda. Vissa typer av arbetsbelastningar kan dra nytta av köra parallella aktiviteter på större-- men färre--compute-noder. Kolla in den [Exempelscenario](batch-parallel-node-tasks.md#example-scenario) i artikeln för information om sådant scenario.

### <a name="batch-forum"></a>Batch-Forum
Den [Azure Batch-Forum] [ forum] på MSDN är det bra att diskutera Batch och ställa frågor om tjänsten. HEAD på över för användbara ”Fäst” inlägg och publicera dina frågor när de uppstår när du skapar Batch-lösningar.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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

[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job