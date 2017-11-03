---
title: "Använd prestandaräknare i Azure Diagnostics | Microsoft Docs"
description: "Hitta flaskhalsar och justera prestanda med hjälp av prestandaräknare i Azure-molntjänster eller den virtuella datorn."
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: fc4c61e9-d49d-4ed9-a32c-b91cdf851909
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb
ms.openlocfilehash: 2cf765cb034725199127c547a9b8b997a4a6089c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Skapa och använda prestandaräknare i ett Azure-program
Den här artikeln beskriver fördelarna med och hur du placera prestandaräknare i Azure-program. Du kan använda dem för att samla in data, hitta flaskhalsar och finjustera system- och programprestanda.

Prestandaräknare som är tillgängliga för Windows Server, IIS och ASP.NET kan också samlas in och används för att fastställa hälsotillståndet för din Azure webbroller, arbetsroller och virtuella datorer. Du kan också skapa och använda anpassade prestandaräknare.  

Du kan undersöka prestandaräknardata

1. Direkt på programvärden med verktyget Prestandaövervakaren nås med hjälp av fjärrskrivbord
2. Med System Center Operations Manager med hjälp av Azure-hanteringspaket
3. Med andra övervakningsverktyg som kommer åt diagnostisk data överförs till Azure storage. Se [Store och visa diagnostiska Data i Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx) för mer information.  

Mer information om övervakning av prestanda i ditt program i den [Azure-portalen](http://portal.azure.com/), se [så övervakaren molntjänster](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Mer detaljerad information om hur du skapar en loggning och spårning strategi och använder diagnostik- och andra tekniker för att felsöka problem och optimera Azure-program finns [felsökning bästa praxis för att utveckla Azure Program](https://msdn.microsoft.com/library/azure/hh771389.aspx).

## <a name="enable-performance-counter-monitoring"></a>Aktivera övervakning av räknare
Prestandaräknare är inte aktiverade som standard. Ditt program eller en startåtgärd måste ändra standardkonfigurationen för diagnostik agent om du vill inkludera specifika prestandaräknare som du vill övervaka för varje rollinstans.

### <a name="performance-counters-available-for-microsoft-azure"></a>Prestandaräknare för Microsoft Azure
Azure tillhandahåller en delmängd av de tillgängliga prestandaräknarna för Windows Server, IIS och ASP.NET-stacken. I följande tabell visas några av prestandaräknarna särskilt intressanta för Azure-program.

| : Räknaren Kategoriobjektet (instans) | Räknarens namn | Referens |
| --- | --- | --- |
| .NET CLR-undantag (*globala*) |# Undantag som utlöses / sek |Prestandaräknare för undantag |
| .NET CLR-minne (*globala*) |Tid i GC i procent |Prestandaräknare för minnet |
| ASP.NET |Programmet startas om |Prestandaräknare för ASP.NET |
| ASP.NET |Körningstid för begäran |Prestandaräknare för ASP.NET |
| ASP.NET |Begäranden som kopplats från |Prestandaräknare för ASP.NET |
| ASP.NET |Omstart av arbetsprocess |Prestandaräknare för ASP.NET |
| ASP.NET-program (**totala**) |Totalt antal begäranden |Prestandaräknare för ASP.NET |
| ASP.NET-program (**totala**) |Begäranden per sekund |Prestandaräknare för ASP.NET |
| ASP.NET v4.0.30319 |Körningstid för begäran |Prestandaräknare för ASP.NET |
| ASP.NET v4.0.30319 |Väntetid för begäran |Prestandaräknare för ASP.NET |
| ASP.NET v4.0.30319 |Aktuella begäranden |Prestandaräknare för ASP.NET |
| ASP.NET v4.0.30319 |Begäranden i kö |Prestandaräknare för ASP.NET |
| ASP.NET v4.0.30319 |Begäranden som nekats |Prestandaräknare för ASP.NET |
| Minne |Tillgängliga megabyte |Prestandaräknare för minnet |
| Minne |Dedikerade byte |Prestandaräknare för minnet |
| Processor(_Total) |% Processortid |Prestandaräknare för ASP.NET |
| TCPv4 |Anslutningsfel |TCP-objekt |
| TCPv4 |Anslutningar |TCP-objekt |
| TCPv4 |Återställda anslutningar |TCP-objekt |
| TCPv4 |Segment/s |TCP-objekt |
| Nätverk Interface(*) |Mottagna byte/sek |Objektet för nätverksgränssnittet |
| Nätverk Interface(*) |Skickade byte/sek |Objektet för nätverksgränssnittet |
| Nätverksgränssnittet (Microsoft Virtual Machine Bus nätverk kort _2) |Mottagna byte/sek |Objektet för nätverksgränssnittet |
| Nätverksgränssnittet (Microsoft Virtual Machine Bus nätverk kort _2) |Skickade byte/sek |Objektet för nätverksgränssnittet |
| Nätverksgränssnittet (Microsoft Virtual Machine Bus nätverk kort _2) |Byte totalt/sek |Objektet för nätverksgränssnittet |

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Skapa och Lägg till anpassade prestandaräknare i ditt program
Azure har stöd för att skapa och ändra anpassade prestandaräknare för webbprogram och arbetsroller. Räknare kan användas för att spåra och övervaka programspecifika beteende. Du kan skapa och ta bort anpassade prestandaräknarkategorier och specificerare från en startaktivitet, webbroll eller arbetsroll med förhöjd behörighet.

> [!NOTE]
> Kod som gör ändringar i anpassade prestandaräknare har utökade behörigheter för att köra. Om koden är i en webbroll eller arbetsrollen, rollen måste innehålla taggen <Runtime executionContext="elevated" /> i filen ServiceDefinition.csdef för att rollen ska initieras korrekt.
>
>

Du kan skicka anpassade prestandaräknardata till Azure storage med hjälp av diagnostik-agenten.

Standard prestandaräknardata genereras av Azure processer. Anpassade prestandaräknardata måste ha skapats av rollen eller worker-rollen webbprogrammet. Se [prestandaräknaren typer](https://msdn.microsoft.com/library/z573042h.aspx) information om vilka typer av data som kan lagras i anpassade prestandaräknare. Se [PerformanceCounters exempel](http://code.msdn.microsoft.com/azure/) ett exempel som skapar och anger anpassade prestandaräknardata i en webbroll.

## <a name="store-and-view-performance-counter-data"></a>Lagra och visa information om prestandaräknare
Azure cachelagrar prestandaräknardata med annan diagnostisk information. Dessa data är tillgängliga för fjärråtkomst övervakning medan instansen körs använder fjärråtkomst till skrivbordet för att visa verktyg som Prestandaövervakaren. För att bevara data utanför rollinstansen överföra diagnostik agenten data till Azure-lagring. Storleksgränsen för cachelagrade prestandaräknardata kan konfigureras i diagnostik-agenten eller den kan konfigureras som en del av en delad gräns för alla diagnostikdata. Mer information om hur du anger buffertstorleken finns [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) och [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Se [Store och visa diagnostiska Data i Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx) en översikt över ställa in diagnostik-agenten för att överföra data till ett lagringskonto.

Varje konfigurerade prestandaräknarinstans registreras på en angiven samplingsfrekvensen och samplade data överförs till lagringskontot genom att en schemalagd begäran eller ett för uppringning på begäran. Automatiska överföringar kan schemaläggas så ofta som en gång per minut. Prestandaräknardata som överförs av diagnostik-agenten lagras i en tabell, WADPerformanceCountersTable, i lagringskontot. Den här tabellen kan nås och efterfrågas med Azure standardlagring API-metoder. Se [Microsoft Azure prestandaräknarna Sample](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) ett exempel på frågor och visa prestandaräknardata från tabellen WADPerformanceCountersTable.

> [!NOTE]
> Beroende på diagnostik agent överföring frekvens och kön svarstid kanske senaste prestandaräknardata i lagringskonto flera minuter för gammal.
>
>

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Aktivera prestandaräknare med diagnostik konfigurationsfilen
Använd följande procedur för att aktivera prestandaräknare i Azure-program.

## <a name="prerequisites"></a>Krav
Det här avsnittet förutsätter att du har importerat diagnostik övervakaren till ditt program och läggs diagnostik konfigurationsfilen till Visual Studio-lösning (diagnostics.wadcfg SDK 2.4 och lägre eller diagnostics.wadcfgx SDK 2.5 och senare). Se steg 1 och 2 i [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](cloud-services-dotnet-diagnostics.md)) mer information.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Steg 1: Samla in och lagra data från prestandaräknare
När du har lagt till diagnostik-filen i Visual Studio-lösning kan konfigurera du insamling och lagring av prestandaräknardata i ett Azure-program. Detta görs genom att lägga till prestandaräknare diagnostik-filen. Diagnostikdata, inklusive prestandaräknare, först samlas in på instansen. Data sparas sedan till tabellen WADPerformanceCountersTable i Azure Table-tjänsten, så du måste också ange storage-konto i ditt program. Om du testar programmet lokalt i Compute Emulator, kan du också lagra diagnostikdata lokalt i Storage-emulatorn. Innan du sparar diagnostikdata, måste du först gå till den [Azure-portalen](http://portal.azure.com/) och skapa ett klassiska storage-konto. Ett bra tips är att hitta ditt lagringskonto i samma geo-plats som din Azure-program. Genom att behålla den Azure-program och storage-konto finns i den samma geografiska platsen, undvika betalar extern bandbreddskostnaderna och minska svarstiden.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Lägg till prestandaräknare i filen diagnostik
Det finns många räknare som du kan använda. I följande exempel visas flera prestandaräknare som rekommenderas för webb- och arbetsprocesser för övervakning av rollen.

Öppna filen diagnostik (diagnostics.wadcfg SDK 2.4 och lägre eller diagnostics.wadcfgx i SDK 2.5 och senare) och Lägg till följande DiagnosticMonitorConfiguration-elementet:

```xml
<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
    <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
</PerformanceCounters>
```

BufferQuotaInMB-attribut som anger maximal mängd fillagring för system som är tillgängligt för samlingen datatyp (Azure loggar, IIS-loggar osv.). Standardvärdet är 0. När kvoten har uppnåtts, raderas de äldsta data som nya data läggs. Summan av alla bufferQuotaInMB-egenskaper måste vara större än värdet för attributet OverallQuotaInMB. Mer detaljerad information för att avgöra hur mycket lagringsutrymme måste finnas för insamling av diagnostikdata, se avsnittet installationsprogrammet BOMULLSTUSS i [felsökning bästa praxis för att utveckla program i Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

Attributet scheduledTransferPeriod, som anger intervallet mellan schemalagda dataöverföringar, avrundat till närmaste minut. I följande exempel anges till PT30M (30 minuter). Ange överföringsperioden till ett mindre värde, till exempel 1 minut, negativt påverkar programmets prestanda i produktionen men kan vara användbart för ser diagnostik arbetar snabbt när du testar. Schemalagda överföringsperioden bör vara tillräckligt små för att säkerställa att diagnostikdata inte skrivs på instansen, men tillräckligt stor för att det inte påverkar prestandan för ditt program.

Attributet counterSpecifier anger prestandaräknaren för att samla in. Attributet sampleRate anger den hastighet med vilken prestandaräknaren ska samlas in, i det här fallet 30 sekunder.

När du har lagt till de prestandaräknare som du vill samla in, kan du spara ändringarna i filen diagnostik. Därefter måste måste du ange storage-konto som ska vara beständig diagnostikdata.

### <a name="specify-the-storage-account"></a>Ange storage-konto
Du måste ange en anslutningssträng i din tjänstekonfigurationsfil (ServiceConfiguration.cscfg) för att bevara din diagnostikinformation till ditt Azure Storage-konto.

Lagringskontot kan anges i filen diagnostics.wadcfgx för Azure SDK 2.5.

> [!NOTE]
> Dessa anvisningar gäller endast till Azure SDK 2.4 och under. Lagringskontot kan anges i filen diagnostics.wadcfgx för Azure SDK 2.5.
>
>

Så här anslutningssträngar:

1. Öppna filen ServiceConfiguration.Cloud.cscfg i valfri textredigerare och ange anslutningssträngen för lagringen. Den *AccountName* och *AccountKey* värden finns i Azure-portalen i kontot instrumentpanelen för lagring, under åtkomstnycklar.

    ```xml
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Spara filen ServiceConfiguration.Cloud.cscfg.
3. Öppna filen ServiceConfiguration.Local.cscfg och kontrollera att UseDevelopmentStorage anges till true.

    ```xml
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   Nu när anslutningssträngar ställs behålls diagnostikdata till ditt lagringskonto i ditt program när programmet distribueras.
4. Spara och bygga projektet och sedan distribuera ditt program.

## <a name="step-2-optional-create-custom-performance-counters"></a>Steg 2: (Valfritt) skapa anpassade prestandaräknare
Du kan lägga till dina egna anpassade prestandaräknare för att övervaka webb-eller arbetsroller utöver de fördefinierade prestandaräknarna. Anpassade prestandaräknare kan användas för att spåra och övervaka programspecifika beteende och kan skapas eller tas bort i en startaktivitet, webbroll eller arbetsroll med förhöjd behörighet.

Azure diagnostics-agenten uppdaterar prestandaräknaren konfigurationen från filen .wadcfg minuts när du har startat.  Om du skapar anpassade prestandaräknare i OnStart-metoden och Start aktiviteterna ta längre tid än en minut att köra kommer dina anpassade prestandaräknare inte har skapats när Azure-diagnostik agenten försöker läsa in dem.  I det här scenariot ser du att Azure-diagnostik korrekt fångar upp alla diagnostikdata utom dina anpassade prestandaräknare.  Skapa prestandaräknarna i en startåtgärd eller flytta vissa av dina startaktivitet fungerar OnStart-metoden när du har skapat prestandaräknarna för att lösa problemet.

Utför följande steg för att skapa en enkel anpassade prestandaräknaren ”\MyCustomCounterCategory\MyButton1Counter”:

1. Öppna tjänstdefinitionsfilen (CSDEF) för ditt program.
2. Lägg till Runtime elementet så att WebRole eller WorkerRole elementet så att fjärrkörning med förhöjd behörighet:

    ```xml
    <runtime executioncontext="elevated"/>
    ```
3. Spara filen.
4. Öppna filen diagnostik (diagnostics.wadcfg SDK 2.4 och lägre eller diagnostics.wadcfgx i SDK 2.5 och senare) och Lägg till följande i DiagnosticMonitorConfiguration

    ```xml
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
      <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Spara filen.
6. Skapa anpassade prestandaräknarkategorin i metoden OnStart i din roll innan du anropar base. OnStart. Följande C#-exempel skapar en anpassad kategori om den inte redan finns:

    ```csharp
    public override bool OnStart()
    {
      if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
      {
         CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

         // add a counter tracking user button1 clicks
         CounterCreationData operationTotal1 = new CounterCreationData();
         operationTotal1.CounterName = "MyButton1Counter";
         operationTotal1.CounterHelp = "My Custom Counter for Button1";
         operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
         counterCollection.Add(operationTotal1);

         PerformanceCounterCategory.Create(
           "MyCustomCounterCategory",
           "My Custom Counter Category",
           PerformanceCounterCategoryType.SingleInstance, counterCollection);

         Trace.WriteLine("Custom counter category created.");
      }
      else {
        Trace.WriteLine("Custom counter category already exists.");
      }

    return base.OnStart();
    }
    ```
7. Uppdatera räknare i ditt program. I följande exempel uppdateringar för en anpassad prestandaräknare på Button1_Click händelser:

    ```csharp
    protected void Button1_Click(object sender, EventArgs e)
    {
      PerformanceCounter button1Counter = new PerformanceCounter(
        "MyCustomCounterCategory",
        "MyButton1Counter",
        string.Empty,
        false);
      button1Counter.Increment();
      this.Button1.Text = "Button 1 count: " +
        button1Counter.RawValue.ToString();
    }
    ```
8. Spara filen.  

Anpassade prestandaräknardata samlas nu av Azure-diagnostik övervakaren.

## <a name="step-3-query-performance-counter-data"></a>Steg 3: Fråga prestandaräknardata
När programmet har distribuerats och kör, diagnostik övervakaren kommer att börja samla in prestandaräknare och spara dessa data till Azure storage. Du kan använda verktyg som till exempel Server Explorer i Visual Studio [Azure Lagringsutforskaren](http://azurestorageexplorer.codeplex.com/), eller [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) av Cerebrata att visa data i WADPerformanceCountersTable för prestandaräknare tabell. Du kan också programmässigt fråga tabellen tjänsten använder [C#](../cosmos-db/table-storage-how-to-use-dotnet.md), [Java](../cosmos-db/table-storage-how-to-use-java.md), [Node.js](../cosmos-db/table-storage-how-to-use-nodejs.md), [Python](../cosmos-db/table-storage-how-to-use-python.md), [Ruby](../cosmos-db/table-storage-how-to-use-ruby.md), eller [PHP](../cosmos-db/table-storage-how-to-use-php.md).

Följande C#-exempel visar en enkel fråga mot tabellen WADPerformanceCountersTable och sparar diagnostikdata till en CSV-fil. När prestandaräknarna sparas till en CSV-fil, kan du använda graphing funktionerna i Microsoft Excel eller något annat verktyg för att visualisera data. Glöm inte att lägga till en referens till Microsoft.WindowsAzure.Storage.dll, som ingår i Azure SDK för .NET oktober 2012 och senare. Sammansättningen har installerats i katalogen % Program Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\.

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Table;
...

// Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
// you store your connection string using the Microsoft Azure service configuration
// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
// to retrieve your storage connection string.  If you're not using Cloud Services, it's
// recommended that you store the connection string in your web.config or app.config file.
// Use the ConfigurationManager type to retrieve your storage connection string.

string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

// Get a reference to the storage account using the connection string.  You can also use the development
// storage account (Storage Emulator) for local debugging.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
  .Where(
    TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
      TableOperators.And,
      TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
    )
  )
);

// Execute the table query.
IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

// Process the query results and build a CSV file.
StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

foreach (PerformanceCountersEntity entity in result)
{
  sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
    + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
}

StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
sw.Write(sb.ToString());
sw.Close();
```

Entiteter mappar till C#-objekt med hjälp av en anpassad klass som härleds från **TableEntity**. Följande kod definierar en entitetsklass som representerar en prestandaräknare i den **WADPerformanceCountersTable** tabell.

```csharp
public class PerformanceCountersEntity : TableEntity
{
  public long EventTickCount { get; set; }
  public string DeploymentId { get; set; }
  public string Role { get; set; }
  public string RoleInstance { get; set; }
  public string CounterName { get; set; }
  public double CounterValue { get; set; }
}
```

## <a name="next-steps"></a>Nästa steg
[Visa ytterligare artiklar på Azure-diagnostik](../azure-diagnostics.md)
