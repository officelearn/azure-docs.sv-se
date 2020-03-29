---
title: Indexeringsfel och -varningar
titleSuffix: Azure Cognitive Search
description: Den här artikeln innehåller information och lösningar på vanliga fel och varningar som kan uppstå under AI-anrikning i Azure Cognitive Search.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72bf08dce36d857c1fe91bbe9806336dfa185f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671978"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Felsöka vanliga indexfel och varningar i Azure Cognitive Search

Den här artikeln innehåller information och lösningar på vanliga fel och varningar som kan uppstå under indexering och AI-anrikning i Azure Cognitive Search.

Indexeringen stoppas när antalet fel överskrider ["maxFailedItems".](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) 

Om du vill att indexerare ska ignorera dessa fel (och hoppa över "misslyckade dokument" kan du överväga att uppdatera `maxFailedItems` och `maxFailedItemsPerBatch` som beskrivs [här](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Varje misslyckat dokument tillsammans med dokumentnyckeln (när tillgängligt) visas som ett fel i indexeringskörningsstatusen. Du kan använda [index-API:et](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att manuellt ladda upp dokumenten vid ett senare tillfälle om du har ställt in indexeraren att tolerera fel.

Felinformationen i den här artikeln kan hjälpa dig att lösa fel, så att indexeringen kan fortsätta.

Varningar slutar inte indexera, men de indikerar förhållanden som kan resultera i oväntade resultat. Om du vidtar åtgärder eller inte beror på data och ditt scenario.

Från och `2019-05-06`med API-version, postnivå Indexer fel och varningar är strukturerade för att ge ökad klarhet kring orsaker och nästa steg. De innehåller följande egenskaper:

| Egenskap | Beskrivning | Exempel |
| --- | --- | --- |
| key | Dokumentets dokument-ID påverkas av felet eller varningen. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| namn | Åtgärdsnamnet som beskriver var felet eller varningen uppstod. Detta genereras av följande struktur: [kategori]. [underkategori]. [resourceType]. [resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| meddelande | En beskrivning på hög nivå av felet eller varningen. | Det gick inte att köra färdighet eftersom web api-begäran misslyckades. |
| Detaljer | Ytterligare information som kan vara till hjälp för att diagnostisera problemet, till exempel WebApi-svaret om det gick inte att köra en anpassad färdighet. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 källa,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... resten av stack spår ... |
| dokumentationLänk | En länk till relevant dokumentation med detaljerad information för att felsöka och lösa problemet. Denna länk pekar ofta på ett av nedanstående avsnitt på den här sidan. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Fel: Det gick inte att läsa dokumentet

Indexer kunde inte läsa dokumentet från datakällan. Detta kan inträffa på grund av:

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| Inkonsekventa fälttyper i olika dokument | "Typ av värde har en obalans med kolumntypen. Det gick `'{47.6,-122.1}'` inte att lagra i kolumnen författare.  Förväntad typ är JArray."  "Det gick inte att konvertera datatyp nvarchar till float."  "Konverteringen misslyckades när nvarchar-värdet "12 månader" konverteras till datatypsingt."  "Aritmetiska spillfelstänger som konverterar uttryck till datatypsingt." | Kontrollera att typen av varje fält är densamma i olika dokument. Om till exempel det `'startTime'` första dokumentfältet är en DateTime och i det andra dokumentet är det en sträng, kommer det här felet att slås. |
| fel från datakällans underliggande tjänst | (från Cosmos DB)`{"Errors":["Request rate is large"]}` | Kontrollera din lagringsinstans för att säkerställa att den är felfri. Du kan behöva justera skalning/partitionering. |
| tillfälliga frågor | Ett fel på transportnivå uppstod när resultaten tas emot från servern. (provider: TCP Provider, fel: 0 - En befintlig anslutning stängdes med våld av fjärrvärden | Ibland finns det oväntade anslutningsproblem. Försök att köra dokumentet via indexeraren igen senare. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Fel: Det gick inte att extrahera innehåll eller metadata från dokumentet
Indexeraren med en Blob-datakälla kunde inte extrahera innehållet eller metadata från dokumentet (till exempel en PDF-fil). Detta kan inträffa på grund av:

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| blob är över storleksgränsen | Dokumentet `'150441598'` är byte, vilket överskrider `'134217728'` den maximala storleken för dokumentextrahering för den aktuella tjänstnivån. | [blob indexeringsfel](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob har innehållstyp som inte stöds | Dokumentet har innehållstyp som inte stöds`'image/png'` | [blob indexeringsfel](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob är krypterad | Det gick inte att bearbeta dokumentet – det kan vara krypterat eller lösenordsskyddat. | Du kan hoppa över blob med [blob-inställningar](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| tillfälliga frågor | "Felbearbetning av blob: Begäran avbröts: Begäran avbröts." "Dokumentet har timeats ut under bearbetningen." | Ibland finns det oväntade anslutningsproblem. Försök att köra dokumentet via indexeraren igen senare. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Fel: Det gick inte att tolka dokumentet
Indexer läste dokumentet från datakällan, men det uppstod ett problem med att konvertera dokumentinnehållet till det angivna fältmappningsschemat. Detta kan inträffa på grund av:

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| Dokumentnyckeln saknas | Dokumentnyckeln kan inte saknas eller vara tom | Kontrollera att alla dokument har giltiga dokumentnycklar |
| Dokumentnyckeln är ogiltig | Dokumentnyckeln får inte vara längre än 1024 tecken | Ändra dokumentnyckeln för att uppfylla valideringskraven. |
| Det gick inte att använda fältmappning på ett fält | Det gick inte `'functionName'` att `'fieldName'`använda mappningsfunktionen i fältet . Matrisen kan inte vara null. Parameternamn: byte | Dubbelkolla [fältmappningarna](search-indexer-field-mappings.md) som definierats på indexeraren och jämför med data i det angivna fältet i det misslyckade dokumentet. Det kan vara nödvändigt att ändra fältmappningarna eller dokumentdata. |
| Det gick inte att läsa fältvärdet | Det gick inte att `'fieldName'` läsa `'fieldIndex'`värdet för kolumnen vid index . Ett fel på transportnivå uppstod när resultaten tas emot från servern. (provider: TCP Provider, fel: 0 - En befintlig anslutning stängdes med våld av fjärrvärden.) | Dessa fel beror vanligtvis på oväntade anslutningsproblem med datakällans underliggande tjänst. Försök att köra dokumentet via indexeraren igen senare. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Fel: Det gick inte att köra färdighet
Indexer kunde inte köra en färdighet i kompetensen.

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| Problem med tillfällig anslutning | Ett tillfälligt fel uppstod. Försök igen senare. | Ibland finns det oväntade anslutningsproblem. Försök att köra dokumentet via indexeraren igen senare. |
| Potentiell produktbugg | Det uppstod ett oväntat fel. | Detta indikerar en okänd felklass och kan innebära att det finns ett produktfel. Vänligen lämna in en [supportbiljett](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp. |
| En färdighet har stött på ett fel under körningen | (Från sammanslagningsfärdighet) Ett eller flera motprestationsvärden var ogiltiga och kunde inte tolkas. Objekt infogades i slutet av texten | Använd informationen i felmeddelandet för att åtgärda problemet. Den här typen av fel kräver åtgärder för att lösa. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Fel: Det gick inte att köra färdighet eftersom webb-API-begäran misslyckades
Körning av färdighet misslyckades eftersom anropet till webb-API:et misslyckades. Vanligtvis inträffar den här felklassen när anpassade kunskaper används, i vilket fall du måste felsöka din anpassade kod för att lösa problemet. Om felet i stället kommer från en inbyggd färdighet läser du felmeddelandet om hjälp med att åtgärda problemet.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Fel: Det gick inte att köra färdighet eftersom webb-API-skicklighetssvar är ogiltigt
Körning av färdighet misslyckades eftersom anropet till webb-API:et returnerade ett ogiltigt svar. Vanligtvis inträffar den här felklassen när anpassade kunskaper används, i vilket fall du måste felsöka din anpassade kod för att lösa problemet. Om felet i stället kommer från en inbyggd färdighet, vänligen lämna in en [supportbiljett](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Fel: Färdigheten har inte körning inom tidsfristen
Det finns två fall där du kan stöta på detta felmeddelande, som var och en bör behandlas annorlunda. Följ instruktionerna nedan beroende på vilken skicklighet som returnerats detta fel för dig.

### <a name="built-in-cognitive-service-skills"></a>Inbyggda kognitiva servicefärdigheter
Många av de inbyggda kognitiva färdigheterna, till exempel språkidentifiering, entitetsigenkänning eller OCR, backas upp av en Cognitive Service API-slutpunkt. Ibland finns det tillfälliga problem med dessa slutpunkter och en begäran kommer time out. För tillfälliga problem finns det ingen åtgärd förutom att vänta och försöka igen. Som en begränsning bör du ställa in indexeraren så att [den körs enligt ett schema](search-howto-schedule-indexers.md). Schemalagd indexering tar vid där den slutade. Förutsatt att tillfälliga problem är lösta bör indexering och kognitiv färdighetsbearbetning kunna fortsätta på nästa schemalagda körning.

Om du fortsätter att se det här felet i samma dokument för en inbyggd kognitiv färdighet, vänligen lämna in en [supportbiljett](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp, eftersom detta inte förväntas.

### <a name="custom-skills"></a>Anpassade färdigheter
Om du stöter på ett timeout-fel med en anpassad färdighet som du har skapat finns det ett par saker du kan prova. Granska först din anpassade färdighet och se till att den inte fastnar i en oändlig loop och att den returnerar ett resultat konsekvent. När du har bekräftat att så är fallet, bestämma vad körningstiden för din skicklighet är. Om du inte uttryckligen `timeout` angav ett värde på din `timeout` anpassade färdighetsdefinition är standardvärdet 30 sekunder. Om 30 sekunder inte är tillräckligt långa för att `timeout` din färdighet ska kunna köras kan du ange ett högre värde på din anpassade färdighetsdefinition. Här är ett exempel på en anpassad färdighetsdefinition där tidsgränsen är inställd på 90 sekunder:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

Det maximala värdet som du `timeout` kan ange för parametern är 230 sekunder.  Om din anpassade färdighet inte kan köras konsekvent inom 230 sekunder kan du överväga att minska din anpassade färdighet så att den `batchSize` har färre dokument att bearbeta inom en enda körning.  Om du redan `batchSize` har ställt in din till 1, måste du skriva om färdigheten för att kunna köra på under 230 sekunder eller på annat sätt dela upp den i flera anpassade färdigheter så att körningstiden för en enskild anpassad färdighet är högst 230 sekunder. Läs den [anpassade färdighetsdokumentationen](cognitive-search-custom-skill-web-api.md) för mer information.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Fel: Det`MergeOrUpload`gick inte ' ' | '`Delete`' dokument till sökindexet

Dokumentet lästes och bearbetades, men indexeraren kunde inte lägga till det i sökindexet. Detta kan inträffa på grund av:

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| Ett fält innehåller en term som är för stor | En term i dokumentet är större än [gränsen på 32 kB](search-limits-quotas-capacity.md#api-request-limits) | Du kan undvika den här begränsningen genom att se till att fältet inte är konfigurerat som filterbart, facetable eller sorterbart.
| Dokumentet är för stort för att indexeras | Ett dokument är större än den [maximala api-begärandens storlek](search-limits-quotas-capacity.md#api-request-limits) | [Så här indexerar du stora datauppsättningar](search-howto-large-index.md)
| Dokumentet innehåller för många objekt i samlingen | En samling i dokumentet överskrider den [maximala gränsen för element i alla komplexa samlingar](search-limits-quotas-capacity.md#index-limits) "Dokumentet med nyckeln `'1000052'` har `'4303'` objekt i samlingar (JSON-matriser). På `'3000'` de flesta objekt får vara i samlingar över hela dokumentet. Ta bort objekt från samlingar och försök indexera dokumentet igen." | Vi rekommenderar att du minskar storleken på den komplexa samlingen i dokumentet till under gränsen och undviker hög lagringsanvändning.
| Problem med att ansluta till målindexet (som kvarstår efter återförsök) eftersom tjänsten är under annan belastning, till exempel fråga eller indexering. | Det gick inte att upprätta anslutningen för att uppdatera index. Söktjänsten är under tung belastning. | [Skala upp söktjänsten](search-capacity-planning.md)
| Söktjänsten korrigeras för tjänstuppdatering eller är mitt i en topologi omkonfigurering. | Det gick inte att upprätta anslutningen för att uppdatera index. Söktjänsten är för närvarande nere/söktjänsten genomgår en övergång. | Konfigurera tjänsten med minst 3 repliker för 99,9 % tillgänglighet per [SLA-dokumentation](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Fel i den underliggande beräknings-/nätverksresursen (sällsynt) | Det gick inte att upprätta anslutningen för att uppdatera index. Ett okänt fel uppstod. | Konfigurera indexerare så att [de körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett misslyckat tillstånd.
| En indexeringsbegäran till målindexet bekräftades inte inom en tidsgränsen på grund av nätverksproblem. | Det gick inte att upprätta anslutningen till sökindexet i tid. | Konfigurera indexerare så att [de körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett misslyckat tillstånd. Försök dessutom att sänka [indexerarbatchstorleken](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) om feltillståndet kvarstår.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Fel: Det gick inte att indexera dokumentet eftersom vissa av dokumentets data inte var giltiga

Dokumentet lästes och bearbetades av indexeraren, men på grund av en obalans i konfigurationen av indexfälten och de data som extraherades och bearbetas av indexeraren kunde det inte läggas till i sökindexet. Detta kan inträffa på grund av:

| Orsak | Detaljer/exempel
| --- | ---
| Datatypen för de fält som extraheras av indexeraren är inkompatibel med datamodellen för motsvarande målindexfält. | Datafältet "_data_" i dokumentet med nyckeln "888" har ett ogiltigt värde av typen "Edm.String". Den förväntade typen var "Collection(Edm.String)". |
| Det gick inte att extrahera en JSON-entitet från ett strängvärde. | Det gick inte att tolka värdet av typen "Edm.String" i_fältdata_som ett JSON-objekt. Fel:'Efter tolkning av ett värde påträffades ett oväntat tecken: ''. Bana "_bana_", linje 1, position 3162." |
| Det gick inte att extrahera en samling JSON-entiteter från ett strängvärde.  | Det gick inte att tolka värdet av typen "Edm.String" i_fältdata_som en JSON-matris. Fel:'Efter tolkning av ett värde påträffades ett oväntat tecken: ''. Bana "[0]', linje 1, position 27." |
| En okänd typ upptäcktes i källdokumentet. | Okänd typ '_okänd_' kan inte indexeras |
| En inkompatibel notation för geografipunkter användes i källdokumentet. | WKT POINT-stränglitteraler stöds inte. Använd GeoJson punkt litteraler istället |

I alla dessa fall läser du [Datatyper](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) som stöds och [datatypsmappning för indexerare för](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) att se till att du skapar indexschemat korrekt och har ställt in lämpliga [indexerarfältmappningar](search-indexer-field-mappings.md). Felmeddelandet innehåller information som kan hjälpa till att spåra källan till obalansen.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Fel: Det går inte att använda en integrerad ändringsspårningsprincip eftersom tabellen har en sammansatt primärnyckel

Detta gäller sql-tabeller och inträffar vanligtvis när nyckeln antingen definieras som en sammansatt nyckel eller, när tabellen har definierat ett unikt klustrade index (som i ett SQL-index, inte ett Azure Search-index). Den främsta orsaken är att nyckelattributet ändras till en sammansatt primärnyckel när det gäller ett [unikt klustrade index](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). I så fall kontrollerar du att SQL-tabellen inte har ett unikt klustrade index eller att du mappar nyckelfältet till ett fält som garanterat inte har dubblettvärden.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Fel: Det gick inte att bearbeta dokumentet inom indexerarens maxkörningstid

Det här felet uppstår när indexeraren inte kan slutföra bearbetningen av ett enda dokument från datakällan inom den tillåtna körningstiden. [Maximal körtid](search-limits-quotas-capacity.md#indexer-limits) är kortare när skillsets används. När det här felet inträffar, om du har maxFailedItems inställt på ett annat värde än 0, kringgår indexeraren dokumentet på framtida körningar så att indexeringen kan fortskrida. Om du inte har råd att hoppa över något dokument, eller om du ser det här felet konsekvent, kan du överväga att dela upp dokument i mindre dokument så att partiella framsteg kan göras inom en enda indexeringskörning.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Fel: Det gick inte att projicera dokumentet

Det här felet uppstår när indexeraren försöker [projicera data i ett kunskapslager](knowledge-store-projection-overview.md) och det uppstod ett fel i vårt försök att göra det.  Det här felet kan vara konsekvent och fastställbart eller så kan det vara ett tillfälligt fel med projektionsutdatamottagaren som du kan behöva vänta och försöka igen för att lösa.  Här är en uppsättning kända feltillstånd och möjliga lösningar.

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| Det gick inte att `'blobUri'` uppdatera projektionsbloben i behållaren`'containerName'` |Den angivna behållaren finns inte. | Indexeraren kontrollerar om den angivna behållaren tidigare har skapats och skapar den om det behövs, men den utför bara den här kontrollen en gång per indexeringskörning. Det här felet innebär att något tog bort behållaren efter det här steget.  LÃ¶s problemet genom att fÃ¶1 fÃ¶1iga: lämna din lagringskontoinformation ensam, vänta tills indexeraren är klar och kör sedan indexeraren igen. |
| Det gick inte att `'blobUri'` uppdatera projektionsbloben i behållaren`'containerName'` |Det gick inte att skriva data till transportanslutningen: En befintlig anslutning stängdes med tvång av fjärrvärden. | Detta förväntas vara ett tillfälligt fel med Azure Storage och bör därför lösas genom att köra indexeraren igen. Om du stöter på det här felet konsekvent, vänligen lämna in en [supportbiljett](https://ms.portal.azure.com/#create/Microsoft.Support) så att den kan undersökas ytterligare.  |
| Det gick `'projectionRow'` inte att uppdatera raden i tabellen`'tableName'` | Servern är upptagen. | Detta förväntas vara ett tillfälligt fel med Azure Storage och bör därför lösas genom att köra indexeraren igen. Om du stöter på det här felet konsekvent, vänligen lämna in en [supportbiljett](https://ms.portal.azure.com/#create/Microsoft.Support) så att den kan undersökas ytterligare.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Varning: Färdighetsinmatningen var ogiltig
En indata till färdigheten saknades, fel typ eller på annat sätt ogiltig. Varningsmeddelandet visar effekten:
1) Det gick inte att utföra färdighet
2) Färdighetsutnredad men kan få oväntade resultat

Kognitiva färdigheter har krävt indata och valfria ingångar. [Nyckelfrasutextraheringsfärdigheten](cognitive-search-skill-keyphrases.md) `text`har `languageCode`till exempel två obligatoriska indata och inga valfria indata. Anpassade färdighetsindata betraktas alla som valfria indata.

Om nödvändiga indata saknas eller om någon indata inte är rätt typ, hoppas färdigheten över och genererar en varning. Överhoppade kunskaper genererar inga utdata, så om andra färdigheter använder utdata från den överhoppade färdigheten kan de generera ytterligare varningar.

Om en valfri indata saknas körs färdigheten fortfarande men kan ge oväntade utdata på grund av den saknade indata.

I båda fallen kan den här varningen förväntas på grund av formen på dina data. Om du till exempel har ett dokument som `firstName` `middleName`innehåller `lastName`information om personer med fälten kan `middleName`du ha vissa dokument som inte har någon post för . Om du `middleName` ska skicka som en indata till en färdighet i pipelinen, förväntas den här färdighetsinmatningen saknas en del av tiden. Du måste utvärdera dina data och scenario för att avgöra om någon åtgärd krävs som ett resultat av den här varningen.

Om du vill ange ett standardvärde vid utebliven inmatning kan du använda [den villkorliga färdigheten](cognitive-search-skill-conditional.md) för att generera ett standardvärde och sedan använda utdata för [den villkorliga färdigheten](cognitive-search-skill-conditional.md) som färdighetsinmatning.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Orsak | Detaljer/exempel | Lösning |
| --- | --- | --- |
| Färdighetsinmatning är fel typ | "Obligatorisk skicklighetsinmatning var inte `String`av den förväntade typen . Namn: `text`, `/document/merged_content`Källa: ."  "Obligatorisk skicklighetsinmatning var inte av det förväntade formatet. Namn: `text`, `/document/merged_content`Källa: ."  "Kan inte iterera `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`över icke-array."  "Det går `0` inte att `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`välja i icke-matris" | Vissa färdigheter förväntar sig indata av vissa `text` typer, till exempel Sentiment [skill](cognitive-search-skill-sentiment.md) förväntar sig att vara en sträng. Om indata anger ett icke-strängvärde körs inte färdigheten och genererar inga utdata. Kontrollera att datauppsättningen har indatavärden enhetliga i typ, eller använd en [anpassad webb-API-färdighet](cognitive-search-custom-skill-web-api.md) för att förbehandla indata. Om du itererar färdigheten över en matris kontrollerar `*` du färdighetskontexten och indata har i rätt positioner. Vanligtvis både kontext och indatakälla bör sluta med `*` för matriser. |
| Färdighetsinmatning saknas | "Krävs skicklighet input saknas. Namn: `text`, `/document/merged_content`Källa: " `/document/normalized_images/0/imageTags`"Saknat värde ."  "Det går `0` inte `/document/pages` att `0`välja i matris med längd." | Om alla dina dokument får den här varningen, troligen finns det ett stavfel i indatabanorna och du bör dubbelkolla egenskapsnamn hölje, extra eller saknas `*` i sökvägen, och se till att dokumenten från datakällan ger de nödvändiga ingångarna. |
| Indata för färdighetsspråkkod är ogiltig | Färdighetsinmatning `languageCode` har `X,Y,Z`följande språkkoder , varav minst en är ogiltig. | Se mer information [nedan](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Varning: Skill input 'languageCode' har följande språkkoder "X, Y,Z", varav minst en är ogiltig.
Ett eller flera av de `languageCode` värden som skickas till den valfria indata för en nedströms färdighet stöds inte. Detta kan inträffa om du skickar utdata från [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) till efterföljande färdigheter, och resultatet består av fler språk än vad som stöds i dessa nedströmsfärdigheter.

Om du vet att din datauppsättning är allt på ett språk, bör `languageCode` du ta `defaultLanguageCode` bort [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och skicklighetsinmatning och använda färdighetsparametern för den färdigheten i stället, förutsatt att språket stöds för den färdigheten.

Om du vet att din datauppsättning innehåller flera språk och därför `languageCode` behöver [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och inmatning, överväg att lägga till en [ConditionalSkill](cognitive-search-skill-conditional.md) för att filtrera bort texten med språk som inte stöds innan du skickar in texten till nedströms färdigheten.  Här är ett exempel på hur det kan se ut för EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Här är några referenser för de språk som för närvarande stöds för var och en av de kunskaper som kan ge det här felmeddelandet:
* [Språk som stöds av textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (för [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill,](cognitive-search-skill-entity-recognition.md) [SentimentSkill](cognitive-search-skill-sentiment.md)och [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Översättare stöds språk](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (för [textöversättningSkillen](cognitive-search-skill-text-translation.md))
* [Text SplitSkill](cognitive-search-skill-textsplit.md) Språk som stöds:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Varning: Färdighetsinmatningen trunkerades
Kognitiva färdigheter har gränser för längden på text som kan analyseras på en gång. Om textinmatningen för dessa kunskaper överskrider den gränsen trunkerar vi texten för att uppfylla gränsen och utför sedan anrikningen på den trunkerade texten. Detta innebär att färdigheten utförs, men inte över alla dina data.

I exemplet LanguageDetectionSkill nedan `'text'` kan indatafältet utlösa den här varningen om den överskrider teckengränsen. Du hittar kompetensinmatningsgränserna i [kompetensdokumentationen](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Om du vill vara säkra på att all text analyseras kan du överväga att använda [split-färdigheten](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Varning: Webb-API-kompetenssvar innehåller varningar
Indexer kunde köra en färdighet i kompetensen, men svaret från web API-begäran visade att det fanns varningar under körningen. Granska varningarna för att förstå hur dina data påverkas och om åtgärder krävs eller inte.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Varning: Den aktuella indexerarkonfigurationen stöder inte inkrementell förlopp

Den här varningen gäller endast för Cosmos DB-datakällor.

Inkrementell förlopp under indexeringen säkerställer att om indexeringskörningen avbryts av tillfälliga fel eller körningstidsgräns, kan indexeraren fortsätta där den slutade nästa gång den körs, i stället för att behöva indexera om hela samlingen från grunden. Detta är särskilt viktigt när stora samlingar indexeras.

Möjligheten att återuppta ett oavslutat indexeringsjobb `_ts` bygger på att dokument har beställts av kolumnen. Indexeraren använder tidsstämpeln för att avgöra vilket dokument som ska plockas upp härnäst. Om `_ts` kolumnen saknas eller om indexeraren inte kan avgöra om en anpassad fråga är ordnad av den, börjar indexeraren från början och du ser den här varningen.

Det är möjligt att åsidosätta det här beteendet, aktivera `assumeOrderByHighWatermarkColumn` inkrementella förlopp och undertrycka den här varningen med hjälp av konfigurationsegenskapen.

Mer information finns i [Inkrementella förlopp och anpassade frågor](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Varning: Vissa data gick förlorade under projektionen. Rad 'X' i tabellen 'Y' har strängegenskapen 'Z' som var för lång.

[Tjänsten Tabelllagring](https://azure.microsoft.com/services/storage/tables) har gränser för hur stora [entitetsegenskaper](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) kan vara. Strängar kan ha 32 000 tecken eller mindre. Om en rad med en strängegenskap som är längre än 32 000 tecken projiceras bevaras endast de första 32 000 tecknen. Undvik att projicera rader med strängegenskaper som är längre än 32 000 tecken för att undvika problemet.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Varning: Trunkerad extraherad text till X-tecken
Indexerare begränsar hur mycket text som kan extraheras från ett och samma dokument. Den här gränsen beror på prisnivån: 32 000 tecken för den kostnadsfria nivån, 64 000 för Basic, 4 miljoner standard, 8 miljoner standard S2 och 16 miljoner för Standard S3. Text som trunkerats indexeras inte. Undvik den här varningen genom att försöka dela upp dokument med stora mängder text i flera, mindre dokument. 

Mer information finns i [Indexergränser](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Varning: Det gick inte att mappa utdatafältet "X" till sökindex
Utdatafältmappningar som refererar till icke-existerande/null-data ger varningar för varje dokument och resulterar i ett tomt indexfält. Om du vill lösa problemet dubbelkollar du källsökvägarna för utdatafältmappning för möjliga stavfel eller anger ett standardvärde med hjälp av [villkorsfärdigheten](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Varning: Dataändringsidentifieringsprincipen är konfigurerad för att använda nyckelkolumnen "X"
Principer för [identifiering av dataändringar](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) har särskilda krav för de kolumner som de använder för att identifiera ändringar. Ett av dessa krav är att den här kolumnen uppdateras varje gång källobjektet ändras. Ett annat krav är att det nya värdet för den här kolumnen är större än det tidigare värdet. Viktiga kolumner uppfyller inte det här kravet eftersom de inte ändras på varje uppdatering. Om du vill lösa problemet väljer du en annan kolumn för ändringsidentifieringsprincipen.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Varning: Dokumenttexten verkar vara UTF-16 kodad, men saknar ett byteordermarkeringsmärke

[Indexeringsinställningarna](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) måste veta hur text kodas innan den tolkas. De två vanligaste sätten att koda text är UTF-16 och UTF-8. UTF-8 är en kodning med variabel längd där varje tecken är mellan 1 byte och 4 byte långt. UTF-16 är en fast längdkodning där varje tecken är 2 byte långt. UTF-16 har två olika varianter, "big endian" och "little endian". Textkodning bestäms av ett "byteordermärke", en serie byte före texten.

| Kodning | Byte order märke |
| --- | --- |
| UTF-16 Stora Endian | 0xFE 0xFF |
| UTF-16 Lilla Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Om det inte finns något byteordningsmärke antas texten vara kodad som UTF-8.

Om du vill undvika den här varningen bestämmer du vad textkodningen för den här bloben är och lägger till lämplig byteordermarkering.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Varning: Cosmos DB samling "X" har en Lazy indexering politik. Vissa data kan gå förlorade

Samlingar [Lazy](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) med lazy-indexeringsprinciper kan inte efterfrågas konsekvent, vilket resulterar i att indexeraren saknar data. Om du vill ta reda på den här varningen ändrar du indexeringsprincipen till Konsekvent.
