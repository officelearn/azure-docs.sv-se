---
title: Azure Datautforskaren-integrering med Azure Data Factory
description: I det här avsnittet integrerar du Azure Datautforskaren med Azure Data Factory för att använda åtgärderna Copy, lookup och Command
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 51683e529f832e06efbe8eb71466f3b27d95fcb1
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819135"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrera Azure-Datautforskaren med Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) är en molnbaserad data integrerings tjänst som gör att du kan integrera olika data lager och utföra aktiviteter på data. Med ADF kan du skapa data drivna arbets flöden för att dirigera och automatisera data förflyttning och data omvandling. Azure Datautforskaren är ett av de [data lager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) i Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory aktiviteter för Azure Datautforskaren

Olika integreringar med Azure Data Factory är tillgängliga för Azure Datautforskaren-användare:

### <a name="copy-activity"></a>Kopieringsaktivitet
 
Azure Data Factory kopierings aktivitet används för att överföra data mellan data lager. Azure Datautforskaren stöds som en källa, där data kopieras från Azure Datautforskaren till alla data lager som stöds och en mottagare, där data kopieras från ett data lager som stöds till Azure Datautforskaren. Mer information finns i [Kopiera data till eller från Azure datautforskaren med Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). en detaljerad genom gång finns i [läsa in data från Azure Data Factory till Azure datautforskaren](data-factory-load-data.md).
Azure Datautforskaren stöds av Azure IR (Integration Runtime) som används när data kopieras i Azure och lokal IR-anslutning som används vid kopiering av data från/till data lager som finns lokalt eller i ett nätverk med åtkomst kontroll, till exempel en Azure-Virtual Network. Mer information finns i [vilken IR som ska användas](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> När du använder kopierings aktiviteten och skapar en **länkad tjänst** eller en **data uppsättning**väljer du data lagret **Azure datautforskaren (Kusto)** och inte det gamla **Kusto**för data lager.  

### <a name="lookup-activity"></a>Söknings aktivitet
 
Söknings aktiviteten används för att köra frågor på Azure Datautforskaren. Resultatet av frågan returneras som resultatet av uppslags aktiviteten och kan användas i nästa aktivitet i pipelinen enligt beskrivningen i [dokumentationen för ADF-sökning](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Förutom svars storleks gränsen på 5 000 rader och 2 MB har aktiviteten också en tids gräns på 1 timme.

### <a name="command-activity"></a>Kommando aktivitet

Med kommando aktiviteten kan du köra Azure Datautforskaren [Control-kommandon](/azure/kusto/concepts/#control-commands). Till skillnad från frågor kan kontroll kommandon eventuellt ändra data eller metadata. Några av kontroll kommandona är riktade till att mata in data i Azure Datautforskaren, med kommandon som `.ingest`eller `.set-or-append`) eller kopiera data från Azure Datautforskaren till externa data lager med hjälp av kommandon som `.export`.
En detaljerad genom gång av kommando aktiviteten finns i [använda Azure Data Factory kommando aktivitet för att köra Azure datautforskaren Control-kommandon](data-factory-command-activity.md).  Om du använder ett kontroll kommando för att kopiera data kan du, när som helst, vara ett snabbare och billigare alternativ än kopierings aktiviteten. För att avgöra när du ska använda kommando aktiviteten jämfört med kopierings aktiviteten, se [Välj mellan kopierings-och kommando aktiviteter när du kopierar data](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Kopiera i bulk från en databas mall

[Kopian i bulk från en databas till Azure datautforskaren med hjälp av Azure Data Factory mal len](data-factory-template.md) är en fördefinierad Azure Data Factory pipeline. Mallen används för att skapa många pipeliner per databas eller per tabell för snabbare data kopiering. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Välj mellan kopierings-och Azure Datautforskaren kommando aktiviteter vid kopiering av data 

Det här avsnittet hjälper dig att välja rätt aktivitet för dina data kopierings behov.

När du kopierar data från eller till Azure Datautforskaren finns det två tillgängliga alternativ i Azure Data Factory:
* Kopierings aktivitet.
* Azure Datautforskaren kommando aktivitet, som kör ett av kontroll kommandona som överför data i Azure Datautforskaren. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopiera data från Azure Datautforskaren
  
Du kan kopiera data från Azure Datautforskaren med hjälp av kopierings aktiviteten eller [`.export`](/azure/kusto/management/data-export/) kommandot. Kommandot `.export` kör en fråga och exporterar sedan resultatet från frågan. 

I följande tabell visas en jämförelse av kommandot Kopiera aktivitet och `.export` för att kopiera data från Azure Datautforskaren.

| | Kopieringsaktivitet | . export-kommandot |
|---|---|---|
| **Flödes Beskrivning** | ADF kör en fråga på Kusto, bearbetar resultatet och skickar det till mål data lagret. <br>(**ADX > ADF > mottagare av data lager**) | ADF skickar ett `.export` kontroll kommando till Azure Datautforskaren, som kör kommandot och skickar data direkt till mål data lagret. <br>(**ADX > Sink data Store**) |
| **Mål data lager som stöds** | En mängd olika [data lager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure-Blob, SQL Database |
| **Prestanda** | Centraliserad | <ul><li>Distribuerat (standard), exportera data från flera noder samtidigt</li><li>Snabbare och KSV-effektivt.</li></ul> |
| **Server begränsningar** | [Frågans gränser](/azure/kusto/concepts/querylimits) kan utökas/inaktive ras. Som standard innehåller ADF-frågor: <ul><li>Storleks gräns på 500 000 poster eller 64 MB.</li><li>Tids gräns på 10 minuter.</li><li>`noTruncation` angetts till false.</li></ul> | Som standard utökar eller inaktiverar du begränsningarna för frågan: <ul><li>Storleks gränserna har inaktiverats.</li><li>Serverns tids gräns är utökad till 1 timme.</li><li>`MaxMemoryConsumptionPerIterator` och `MaxMemoryConsumptionPerQueryPerNode` utökas till max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Om ditt kopierings mål är ett av de data lager som stöds av `.export` kommandot och om ingen av funktionerna kopiera aktivitet är avgörande för dina behov, väljer du kommandot `.export`.

### <a name="copying-data-to-azure-data-explorer"></a>Kopiera data till Azure Datautforskaren

Du kan kopiera data till Azure Datautforskaren med hjälp av kommandona Kopiera aktivitet eller inmatning, t. ex. inmatning [från fråga](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`och mata in [från Storage](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

I följande tabell finns en jämförelse av kopierings aktiviteten och inmatnings kommandon för att kopiera data till Azure Datautforskaren.

| | Kopieringsaktivitet | Mata in från fråga<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Mata in från lagring <br> `.ingest` |
|---|---|---|---|
| **Flödes Beskrivning** | ADF hämtar data från käll data lagret, konverterar det till ett tabell format och gör nödvändiga ändringar i schema mappningen. ADF överför sedan data till Azure-blobbar, delar upp dem i segment och laddar sedan ned Blobbarna för att mata in dem i ADX-tabellen. <br> (**Käll data lager > ADF > Azure blobs > ADX**) | Dessa kommandon kan köra en fråga eller ett `.show` kommando och mata in resultatet av frågan i en tabell (**ADX > ADX**). | Det här kommandot matar in data i en tabell genom att "Hämta" data från en eller flera moln lagrings artefakter. |
| **Käll data lager som stöds** |  [olika alternativ](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS gen 2, Azure Blob, SQL (med sql_request-plugin-programmet), Cosmos (med hjälp av cosmosdb_sql_request-plugin-programmet) och andra data lager som tillhandahåller HTTP-eller python-API: er. | Fil system, Azure Blob Storage, ADLS gen 1, ADLS gen 2 |
| **Prestanda** | Inmatningarna köas och hanteras, vilket säkerställer att det går att använda små mängder och säkerställer hög tillgänglighet genom att tillhandahålla belastnings utjämning, nya försök och fel hantering. | <ul><li>Dessa kommandon har inte utformats för att importera data med hög volym.</li><li>Fungerar som förväntat och billigare. Men för produktions scenarier och när trafik hastigheter och data storlekar är stora, använder du kopierings aktiviteten.</li></ul> |
| **Server begränsningar** | <ul><li>Ingen storleks gräns.</li><li>Max. gräns för tids gräns: 1 timme per inmatad blob. |<ul><li>Det finns bara en storleks gräns på fråge delen, som kan hoppas över genom att ange `noTruncation=true`.</li><li>Max. tids gräns: 1 timme.</li></ul> | <ul><li>Ingen storleks gräns.</li><li>Max. tids gräns: 1 timme.</li></ul>|

> [!TIP]
> * När du kopierar data från ADF till Azure Datautforskaren använda `ingest from query`-kommandon.  
> * För stora data mängder (> 1 GB) använder du kopierings aktiviteten.  

## <a name="required-permissions"></a>Nödvändiga behörigheter

I följande tabell visas de behörigheter som krävs för olika steg i integrationen med Azure Data Factory.

| Steg | Åtgärd | Lägsta behörighets nivå | Anteckningar |
|---|---|---|---|
| **Skapa en länkad tjänst** | Databas navigering | *databas visare* <br>Den inloggade användaren som använder ADF bör ha behörighet att läsa metadata för databasen. | Användaren kan ange databas namnet manuellt. |
| | Testa anslutning | *databas övervakare* eller *tabell* inmatning <br>Tjänstens huvud namn bör ha behörighet att köra `.show`-kommandon eller tabell nivå inmatning på databas nivå. | <ul><li>TestConnection verifierar anslutningen till klustret och inte till-databasen. Det kan lyckas även om databasen inte finns.</li><li>Tabell administratörens behörigheter räcker inte.</li></ul>|
| **Skapa en data uppsättning** | Tabell navigering | *databas övervakare* <br>Den inloggade användaren med ADF måste ha behörighet att köra kommandon på databas nivå `.show`. | Användaren kan ange tabell namn manuellt.|
| **Skapa en data uppsättning** eller **kopierings aktivitet** | Förhandsgranska data | *databas visare* <br>Tjänstens huvud namn måste ha behörighet att läsa metadata för databasen. | | 
|   | Importera schema | *databas visare* <br>Tjänstens huvud namn måste ha behörighet att läsa metadata för databasen. | När ADX är källan till en tabell kopia, kommer ADF att importera schemat automatiskt, även om användaren inte har importerat schemat explicit. |
| **ADX som mottagare** | Skapa en kolumn mappning per namn | *databas övervakare* <br>Tjänstens huvud namn måste ha behörighet att köra `.show`-kommandon på databas nivå. | <ul><li>Alla obligatoriska åtgärder fungerar med *Table*-inmatnings plats.</li><li> Vissa valfria åtgärder kan inte utföras.</li></ul> |
|   | <ul><li>Skapa en CSV-mappning i tabellen</li><li>Ta bort mappningen</li></ul>| *tabell* inmatnings-eller *databas administratör* <br>Tjänstens huvud namn måste ha behörighet att göra ändringar i en tabell. | |
|   | För in data | *tabell* inmatnings-eller *databas administratör* <br>Tjänstens huvud namn måste ha behörighet att göra ändringar i en tabell. | | 
| **ADX som källa** | Kör fråga | *databas visare* <br>Tjänstens huvud namn måste ha behörighet att läsa metadata för databasen. | |
| **Kusto-kommando** | | Enligt behörighets nivån för varje kommando. |

## <a name="performance"></a>Prestanda 

Om Azure Datautforskaren är källan och du använder en söknings-, kopierings-eller kommando aktivitet som innehåller en fråga där, se [metod tips](/azure/kusto/query/best-practices) för att få information om prestanda information och [ADF-dokumentation för kopierings aktiviteten](/azure/data-factory/copy-activity-performance).
  
Det här avsnittet behandlar användningen av kopierings aktivitet där Azure Datautforskaren är mottagaren. Det beräknade data flödet för Azure Datautforskaren Sink är 11-13 Mbit/s. I följande tabell beskrivs de parametrar som påverkar prestandan för Azure Datautforskaren-mottagare.

| Parameter | Anteckningar |
|---|---|
| **Komponenter geografisk närhet** | Placera alla komponenter i samma region:<ul><li>käll-och mottagar data lager.</li><li>ADF integration Runtime.</li><li>Ditt ADX-kluster.</li></ul>Se till att minst din integration runtime finns i samma region som ditt ADX-kluster. |
| **Antal DIUs** | 1 virtuell dator för varje 4-DIUs som används av ADF. <br>Om du ökar DIUs får du bara hjälp om din källa är en filbaserad lagring med flera filer. Varje virtuell dator kommer sedan att bearbeta en annan fil parallellt. Därför har kopiering av en enda stor fil en högre latens än att kopiera flera mindre filer.|
|**Mängden och SKU: n för ditt ADX-kluster** | Ett stort antal ADX-noder ökar bearbetnings tiden för inläsning.|
| **Parallellitet** | Om du vill kopiera en mycket stor mängd data från en databas, partitionerar du dina data och använder sedan en Visningsmall som kopierar varje partition parallellt eller använder [Mass kopiering från databas till Azure datautforskaren-mall](data-factory-template.md). Obs! **inställningar** > **graden av parallellitet** i kopierings aktiviteten är inte relevant för ADX. |
| **Data bearbetnings komplexitet** | Svars tiden varierar beroende på käll fils format, kolumn mappning och komprimering.|
| **Den virtuella datorn som kör integrerings körningen** | <ul><li>För Azure Copy går det inte att ändra ADF VM och Machine SKU: er.</li><li> För lokal till Azure Copy kontrollerar du att den virtuella datorn som är värd för din egen IR-överföring är tillräckligt stark.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tips och vanliga fall GRO par

### <a name="monitor-activity-progress"></a>Övervaka aktivitets förlopp

* När du övervakar aktivitets förloppet kan den *data skrivna* egenskapen vara mycket större än egenskapen *läsa data* eftersom *data som läses* beräknas enligt den binära fil storleken, medan data som *skrivs* beräknas enligt minnes storleken i minnet efter att data har deserialiserats och expanderats.

* När du övervakar aktivitets förloppet kan du se att data skrivs till Azure Datautforskaren-mottagaren. När du frågar Azure Datautforskaren-tabellen ser du att data inte har anlänt. Det beror på att det finns två steg när du kopierar till Azure Datautforskaren. 
    * Första steget läser källdata, delar upp dem till 900 MB-segment och överför varje segment till en Azure-blob. Det första steget visas i vyn ADF-aktivitets förlopp. 
    * Det andra steget börjar när alla data har laddats upp till Azure-blobar. Azure Datautforskaren Engine-noderna laddar ned blobarna och matar in data i tabellen mottagare. Data visas sedan i Azure Datautforskaren-tabellen.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Det gick inte att mata in CSV-filer på grund av felaktiga undantag

Azure-Datautforskaren förväntar CSV-filer som överensstämmer med [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Den förväntar sig:
* Fält som innehåller tecken som kräver undantag (till exempel "och nya rader) måste börja och sluta med ett **"** -tecken, utan blank steg. Alla **"** tecken *inuti* fältet är undantagna genom att använda ett dubbelt **"** tecken ( **""** ). Till exempel _"Hello," "World" "_ är en giltig CSV-fil med en enda post med en kolumn eller ett fält med innehållet _Hej," världen "_ .
* Alla poster i filen måste ha samma antal kolumner och fält.

Azure Data Factory tillåter ett omvänt snedstreck (Escape). Om du genererar en CSV-fil med ett omvänt snedstreck med hjälp av Azure Data Factory, kommer det inte att gå att mata in filen till Azure Datautforskaren.

#### <a name="example"></a>Exempel

Följande text värden: Hej, "världen"<br/>
ABC-DEF<br/>
"ABC\D" EF<br/>
"ABC DEF<br/>

Ska visas i en korrekt CSV-fil på följande sätt: "Hello," "World" "<br/>
"ABC DEF"<br/>
"" "ABC DEF"<br/>
"" "ABC\D" "EF"<br/>

Genom att använda standard escape-tecken (omvänt snedstreck) fungerar inte följande CSV med Azure Datautforskaren: "Hello, \"World\""<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Kapslade JSON-objekt

Tänk på följande när du kopierar en JSON-fil till Azure Datautforskaren:
* Matriser stöds inte.
* Om JSON-strukturen innehåller objekt data typer, kommer Azure Data Factory att förenkla objektets underordnade objekt och försöka mappa varje underordnat objekt till en annan kolumn i Azure Datautforskaren-tabellen. Om du vill att hela objekt objekt ska mappas till en enda kolumn i Azure Datautforskaren:
    * Mata in hela JSON-raden i en enda dynamisk kolumn i Azure Datautforskaren.
    * Redigera pipeline-definitionen manuellt genom att använda Azure Data Factorys JSON-redigerare. I **mappningar**
       * Ta bort de flera mappningarna som har skapats för varje underordnat objekt och Lägg till en enda mappning som mappar din objekt typ till din tabell kolumn.
       * Efter den avslutande hakparentesen lägger du till ett kommatecken följt av:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Ange AdditionalProperties när du kopierar till Azure Datautforskaren

> [!NOTE]
> Den här funktionen är för närvarande tillgänglig genom att redigera JSON-nyttolasten manuellt. 

Lägg till en enskild rad under avsnittet "Sink" i kopierings aktiviteten enligt följande:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Det kan vara knepigt att begränsa antalet undantag i värdet. Använd följande kodfragment som referens:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

Det utskrivna värdet:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [kopierar data till Azure datautforskaren med hjälp av Azure Data Factory](data-factory-load-data.md).
* Lär dig mer om [att använda Azure Data Factory mall för Mass kopiering från databas till Azure datautforskaren](data-factory-template.md).
* Lär dig mer om [att använda Azure Data Factory kommando aktivitet för att köra Azure datautforskaren Control-kommandon](data-factory-command-activity.md).
* Lär dig mer om [Azure datautforskaren frågor](/azure/data-explorer/web-query-data) för data frågor.



