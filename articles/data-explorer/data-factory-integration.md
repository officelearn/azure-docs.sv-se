---
title: Azure Data Explorer-integrering med Azure Data Factory
description: I det här avsnittet kan du integrera Azure Data Explorer med Azure Data Factory för att använda kopierings-, uppslags- och kommandoaktiviteter
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293631"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrera Azure Data Explorer med Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) är en molnbaserad dataintegrationstjänst som gör att du kan integrera olika datalager och utföra aktiviteter på data. Med ADF kan du skapa datadrivna arbetsflöden för att dirigera och automatisera dataförflyttningar och dataomvandling. Azure Data Explorer är ett av de [datalager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) i Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory-aktiviteter för Azure Data Explorer

Olika integreringar med Azure Data Factory är tillgängliga för Azure Data Explorer-användare:

### <a name="copy-activity"></a>Kopieringsaktivitet
 
Azure Data Factory Copy-aktivitet används för att överföra data mellan datalager. Azure Data Explorer stöds som en källa, där data kopieras från Azure Data Explorer till alla datalager som stöds och en diskho, där data kopieras från alla datalager som stöds till Azure Data Explorer. Mer information finns i [kopiera data till eller från Azure Data Explorer med Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). och för en detaljerad genomgång se [läsa in data från Azure Data Factory till Azure Data Explorer](data-factory-load-data.md).
Azure Data Explorer stöds av Azure IR (Integration Runtime), som används när data kopieras i Azure och självvärd iR, som används vid kopiering av data från/till datalager som finns lokalt eller i ett nätverk med åtkomstkontroll, till exempel ett Virtuellt Azure-nätverk. Mer information finns i [vilken IR som ska användas](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> När du använder kopieringsaktiviteten och skapar en **länkad tjänst** eller en **datauppsättning**väljer du datalagret **Azure Data Explorer (Kusto)** och inte det gamla datalagret **Kusto**.  

### <a name="lookup-activity"></a>Uppslagsaktivitet
 
Uppslagsaktiviteten används för att köra frågor på Azure Data Explorer. Resultatet av frågan returneras som utdata för uppslagsaktiviteten och kan användas i nästa aktivitet i pipelinen enligt beskrivningen i [ADF-uppslagsdokumentationen](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Förutom svarsstorleksgränsen på 5 000 rader och 2 MB har aktiviteten också en tidsgräns för fråga på 1 timme.

### <a name="command-activity"></a>Kommandoaktivitet

Kommandoaktiviteten gör det möjligt att utföra Azure Data [Explorer-kontrollkommandon](/azure/kusto/concepts/#control-commands). Till skillnad från frågor kan kontrollkommandona potentiellt ändra data eller metadata. Vissa kontrollkommandon är inriktade på att använda data i Azure Data `.ingest` `.set-or-append`Explorer, med kommandon som eller ) eller kopiera `.export`data från Azure Data Explorer till externa datalager med kommandon som .
En detaljerad genomgång av kommandoaktiviteten finns i [använda kommandoaktiviteten Azure Data Factory för att köra Azure Data Explorer-kontrollkommandon](data-factory-command-activity.md).  Att använda ett kontrollkommando för att kopiera data kan ibland vara ett snabbare och billigare alternativ än kopieringsaktiviteten. Information om hur du ska använda kommandoaktiviteten jämfört med kopieringsaktiviteten finns [i välj mellan Kopiera och kommandoaktiviteter när du kopierar data](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Kopiera i grupp från en databasmall

[Kopian i massinlösen från en databas till Azure Data Explorer med hjälp av Azure Data Factory-mallen](data-factory-template.md) är en fördefinierad Azure Data Factory-pipeline. Mallen används för att skapa många pipelines per databas eller per tabell för snabbare datakopiering. 

### <a name="mapping-data-flows"></a>Mappa dataflöden 

[Azure Data Factory-mappningsdataflöden](/azure/data-factory/concepts-data-flow-overview) är visuellt utformade dataomvandlingar som gör det möjligt för datatekniker att utveckla grafisk dataomvandlingslogik utan att skriva kod. Om du vill skapa ett dataflöde och inta data till Azure Data Explorer använder du följande metod:

1. Skapa [mappningsdataflödet](/azure/data-factory/data-flow-create).
1. [Exportera data till Azure Blob](/azure/data-factory/data-flow-sink). 
1. Definiera [händelserutnät](/azure/data-explorer/ingest-data-event-grid) eller [ADF-kopieringsaktivitet](/azure/data-explorer/data-factory-load-data) för att förtära data till Azure Data Explorer.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Välj mellan kommandoaktiviteter för Kopiera och Azure Data Explorer när data kopieras 

Det här avsnittet hjälper dig att välja rätt aktivitet för dina datakopieringsbehov.

När du kopierar data från eller till Azure Data Explorer finns det två tillgängliga alternativ i Azure Data Factory:
* Kopiera aktivitet.
* Kommandoaktiviteten för Azure Data Explorer, som kör ett av de kontrollkommandon som överför data i Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Kopiera data från Azure Data Explorer
  
Du kan kopiera data från Azure Data [`.export`](/azure/kusto/management/data-export/) Explorer med hjälp av kopieringsaktiviteten eller kommandot. Kommandot `.export` kör en fråga och exporterar sedan resultatet av frågan. 

Se följande tabell för en jämförelse `.export` av kopieringsaktiviteten och kommandot för kopiering av data från Azure Data Explorer.

| | Kopieringsaktivitet | .export, kommando |
|---|---|---|
| **Beskrivning av flöde** | ADF kör en fråga på Kusto, bearbetar resultatet och skickar den till måldatalagret. <br>**(ADX > ADF > sink datalager**) | ADF skickar `.export` ett kontrollkommando till Azure Data Explorer, som kör kommandot, och skickar data direkt till måldatalagret. <br>**(ADX > sink datalager**) |
| **Måldatalager som stöds** | En mängd olika [datalager som stöds](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob, SQL-databas |
| **Prestanda** | Centraliserad | <ul><li>Distribuerad (standard), exportera data från flera noder samtidigt</li><li>Snabbare och COGS effektiv.</li></ul> |
| **Servergränser** | [Frågegränser kan](/azure/kusto/concepts/querylimits) utökas/inaktiveras. Som standard innehåller ADF-frågor: <ul><li>Storleksgräns på 500 000 poster eller 64 MB.</li><li>Tidsgränsen på 10 minuter.</li><li>`noTruncation`inställd på false.</li></ul> | Som standard utökar eller inaktiverar frågegränserna: <ul><li>Storleksgränserna är inaktiverade.</li><li>Tidsgränsen för servern utökas till 1 timme.</li><li>`MaxMemoryConsumptionPerIterator`och `MaxMemoryConsumptionPerQueryPerNode` utökas till max (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Om ditt kopieringsmål är ett av `.export` de datalager som stöds av kommandot, och om `.export` ingen av funktionerna för kopieringsaktivitet är avgörande för dina behov väljer du kommandot.

### <a name="copying-data-to-azure-data-explorer"></a>Kopiera data till Azure Data Explorer

Du kan kopiera data till Azure Data Explorer med hjälp av kopieringsaktiviteten`.set-or-append` `.set-or-replace`eller `.set` `.replace)`inmatningskommandona, till exempel [inmatning från fråga](/azure/kusto/management/data-ingestion/ingest-from-query) ( , , , och [inta från lagring](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Se följande tabell för en jämförelse av kopieringsaktiviteten och inmatningskommandon för kopiering av data till Azure Data Explorer.

| | Kopieringsaktivitet | Inta från fråga<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Inta från lagring <br> `.ingest` |
|---|---|---|---|
| **Beskrivning av flöde** | ADF hämtar data från källdatalagret, konverterar dem till ett tabellformat och gör de schemamappningsändringar som krävs. ADF överför sedan data till Azure-blobbar, delar upp dem i segment och hämtar sedan blobbar för att förtära dem i ADX-tabellen. <br> (**Källdatalager > ADF > Azure blobbar > ADX**) | Dessa kommandon kan köra en `.show` fråga eller ett kommando och inta resultatet av frågan i en tabell (**ADX > ADX**). | Det här kommandot intar data i en tabell genom att "dra" data från en eller flera molnlagringsartefakter. |
| **Källdatalager som stöds** |  [olika alternativ](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Azure Blob, SQL (med hjälp av sql_request plugin), Cosmos (med hjälp av cosmosdb_sql_request plugin) och alla andra datalager som tillhandahåller HTTP- eller Python-API:er. | Filsystem, Azure Blob Storage, ADLS Gen 1, ADLS Gen 2 |
| **Prestanda** | Intag ställs i kö och hanteras, vilket säkerställer små storleksutmatningar och säkerställer hög tillgänglighet genom att tillhandahålla belastningsutjämning, återförsök och felhantering. | <ul><li>Dessa kommandon har inte utformats för import av data med stora volymer.</li><li>Fungerar som förväntat och billigare. Men för produktionsscenarier och när trafikhastigheter och datastorlekar är stora använder du aktiviteten Kopiera.</li></ul> |
| **Servergränser** | <ul><li>Ingen storleksgräns.</li><li>Max tidsgräns: 1 timme per intas blob. |<ul><li>Det finns bara en storleksgräns för frågedelen, som `noTruncation=true`kan hoppas över genom att ange .</li><li>Max tidsgräns: 1 timme.</li></ul> | <ul><li>Ingen storleksgräns.</li><li>Max tidsgräns: 1 timme.</li></ul>|

> [!TIP]
> * När du kopierar data från ADF `ingest from query` till Azure Data Explorer använder du kommandona.  
> * För stora datauppsättningar (>1 GB) använder du kopieringsaktiviteten.  

## <a name="required-permissions"></a>Nödvändiga behörigheter

I följande tabell visas de behörigheter som krävs för olika steg i integreringen med Azure Data Factory.

| Steg | Åtgärd | Lägsta behörighetsnivå | Anteckningar |
|---|---|---|---|
| **Skapa en länkad tjänst** | Navigering i databasen | *databasvisare* <br>Den inloggade användaren som använder ADF bör ha behörighet att läsa databasmetadata. | Användaren kan ange databasnamnet manuellt. |
| | Testa anslutning | *databasövervakare* eller *tabell ingestor* <br>Tjänstens huvudnamn bör ha `.show` behörighet att köra kommandon på databasnivå eller inmatning på tabellnivå. | <ul><li>TestConnection verifierar anslutningen till klustret och inte till databasen. Det kan lyckas även om databasen inte finns.</li><li>Behörigheter för tabelladministratör räcker inte.</li></ul>|
| **Skapa en datauppsättning** | Navigering i tabell | *databasövervakare* <br>Den inloggade användaren med ADF måste ha behörighet `.show` att köra kommandon på databasnivå. | Användaren kan ange tabellnamn manuellt.|
| **Skapa en datauppsättning** eller **kopieringsaktivitet** | Förhandsgranska data | *databasvisare* <br>Tjänstens huvudnamn måste ha behörighet att läsa databasens metadata. | | 
|   | Importera schema | *databasvisare* <br>Tjänstens huvudnamn måste ha behörighet att läsa databasens metadata. | När ADX är källan till en tabell-till-tabellkopia importerar ADF schemat automatiskt, även om användaren inte har importerat schemat explicit. |
| **ADX som diskbänk** | Skapa en mappning av en kolumn med namn | *databasövervakare* <br>Tjänstens huvudnamn måste ha `.show` behörighet att köra kommandon på databasnivå. | <ul><li>Alla obligatoriska åtgärder kommer att arbeta med *tabell ingestor*.</li><li> Vissa valfria åtgärder kan misslyckas.</li></ul> |
|   | <ul><li>Skapa en CSV-mappning i tabellen</li><li>Släpp mappningen</li></ul>| *tabell ingestor* eller *databasadministratör* <br>Tjänstens huvudnamn måste ha behörighet att göra ändringar i en tabell. | |
|   | Mata in data | *tabell ingestor* eller *databasadministratör* <br>Tjänstens huvudnamn måste ha behörighet att göra ändringar i en tabell. | | 
| **ADX som källa** | Kör fråga | *databasvisare* <br>Tjänstens huvudnamn måste ha behörighet att läsa databasens metadata. | |
| **Kusto, kommando** | | Enligt behörighetsnivån för varje kommando. |

## <a name="performance"></a>Prestanda 

Om Azure Data Explorer är källan och du använder aktiviteten Uppslag, kopiera eller kommando som innehåller en fråga där, se [metodtips](/azure/kusto/query/best-practices) för prestandainformation och [ADF-dokumentation för kopieringsaktivitet](/azure/data-factory/copy-activity-performance).
  
Det här avsnittet behandlar användningen av kopieringsaktivitet där Azure Data Explorer är diskhon. Det uppskattade dataflödet för Azure Data Explorer-diskbänken är 11-13 MBps. I följande tabell beskrivs parametrarna som påverkar prestanda för Azure Data Explorer-diskhon.

| Parameter | Anteckningar |
|---|---|
| **Komponenter geografisk närhet** | Placera alla komponenter i samma region:<ul><li>och diskbänksdatalager.</li><li>ADF-integrationskörning.</li><li>Ditt ADX-kluster.</li></ul>Kontrollera att åtminstone din integrationskörning är i samma region som ADX-klustret. |
| **Antal dikuser** | 1 virtuell dator för varje 4 DIUs som används av ADF. <br>Om du ökar diu:erna kan det bara vara om källan är ett filbaserat arkiv med flera filer. Varje virtuell dator bearbetar sedan en annan fil parallellt. Därför kommer kopiering av en enda stor fil att ha en högre svarstid än att kopiera flera mindre filer.|
|**Belopp och SKU för ADX-klustret** | Ett stort antal ADX-noder ökar bearbetningstiden för intag.|
| **Parallellitet** | Om du vill kopiera en mycket stor mängd data från en databas partitionerar du dina data och använder sedan en ForEach-loop som kopierar varje partition parallellt eller använder [masskopian från databasen till Azure Data Explorer-mallen](data-factory-template.md). Inställningar**Graden av parallellitet** i kopieringsaktiviteten är inte relevant för ADX. **Settings** >  |
| **Komplexitet för databehandling** | Svarstiden varierar beroende på källfilformat, kolumnmappning och komprimering.|
| **Den virtuella datorn som kör din integrationskörning** | <ul><li>För Azure-kopia kan inte virtuella ADF-datorer och datorsku:er ändras.</li><li> För on-prem till Azure-kopia, fastställa att den virtuella datorn som är värd för din egenvärderade IR är tillräckligt stark.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Tips och vanliga fallgropar

### <a name="monitor-activity-progress"></a>Övervaka aktivitetens förlopp

* När aktivitetens förlopp övervakas kan egenskapen *Data skriven* vara mycket större än egenskapen *Data read* eftersom *dataläsning* beräknas enligt den binära filstorleken, medan *data som skrivs* beräknas enligt minnesstorleken, efter att data har de-serialiserats och expanderats.

* När du övervakar aktivitetsstatusen kan du se att data skrivs till Azure Data Explorer-diskbänken. När du frågar tabellen Azure Data Explorer ser du att data inte har kommit. Detta beror på att det finns två steg vid kopiering till Azure Data Explorer. 
    * Första steget läser källdata, delar upp den till 900 MB-segment och överför varje segment till en Azure Blob. Det första steget ses av ADF-aktivitetens framstegsvy. 
    * Den andra fasen börjar när alla data överförs till Azure Blobbar. Azure Data Explorer-motornoderna hämtar blobbar och intar data i sink-tabellen. Data visas sedan i tabellen Azure Data Explorer.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Underlåtenhet att inta CSV-filer på grund av felaktig utrymning

Azure Data Explorer förväntar sig att CSV-filer ska justeras med [RFC 4180](https://www.ietf.org/rfc/rfc4180.txt).
Den förväntar sig:
* Fält som innehåller tecken som kräver utrymning (till exempel " och nya rader) ska börja och sluta med ett **"** tecken, utan blanktecken. Alla **"** tecken *inuti* fältet förser med hjälp av ett dubbelt **"** tecken (**""**). Till exempel _"Hello, ""World"""_ är en giltig CSV-fil med en enda post med en enda kolumn eller ett fält med innehållet _Hello, "World"._
* Alla poster i filen måste ha samma antal kolumner och fält.

Med Azure Data Factory tillåts omvänt snedstreck (escape). Om du genererar en CSV-fil med ett omvänt snedstreck med Azure Data Factory misslyckas inmatningen av filen till Azure Data Explorer.

#### <a name="example"></a>Exempel

Följande textvärden: Hello, "World"<br/>
ABC DEF<br/>
"ABC\D"EF<br/>
"ABC DEF<br/>

Bör visas i en riktig CSV-fil enligt följande: "Hej, ""World"""<br/>
"ABC DEF"<br/>
""ABC DEF"<br/>
"""ABC\D""EF"<br/>

Genom att använda standard escape-tecknet (omvänt snedstreck) fungerar inte följande \"CSV med Azure Data Explorer: "Hello, World"\"<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC\D\"EF"<br/>

### <a name="nested-json-objects"></a>Kapslade JSON-objekt

När du kopierar en JSON-fil till Azure Data Explorer bör du tänka på följande:
* Matriser stöds inte.
* Om JSON-strukturen innehåller objektdatatyper förenklar Azure Data Factory objektets underordnade objekt och försöker mappa varje underordnat objekt till en annan kolumn i tabellen Azure Data Explorer. Om du vill att hela objektobjektet ska mappas till en enda kolumn i Azure Data Explorer:
    * Inta hela JSON-raden i en enda dynamisk kolumn i Azure Data Explorer.
    * Redigera pipelinedefinitionen manuellt med hjälp av Azure Data Factorys JSON-redigerare. I **mappningar**
       * Ta bort de flera mappningar som har skapats för varje underordnat objekt och lägg till en enda mappning som mappar objekttypen till tabellkolumnen.
       * Efter den avslutande hakparentesen lägger du till ett kommatecken följt av:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Ange ytterligare egenskaper vid kopiering till Azure Data Explorer

> [!NOTE]
> Den här funktionen är för närvarande tillgänglig genom att manuellt redigera JSON-nyttolasten. 

Lägg till en rad under avsnittet "sink" i kopieringsaktiviteten enligt följande:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

Att fly av värdet kan vara svårt. Använd följande kodavsnitt som referens:

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

* Lär dig hur du [kopierar data till Azure Data Explorer med hjälp av Azure Data Factory](data-factory-load-data.md).
* Lär dig mer om hur du använder [Azure Data Factory-mall för masskopia från databas till Azure Data Explorer](data-factory-template.md).
* Lär dig mer om hur du använder [kommandoaktiviteten i Azure Data Factory för att köra Azure Data Explorer-kontrollkommandon](data-factory-command-activity.md).
* Lär dig mer om [Azure Data Explorer-frågor](/azure/data-explorer/web-query-data) för datafrågor.



