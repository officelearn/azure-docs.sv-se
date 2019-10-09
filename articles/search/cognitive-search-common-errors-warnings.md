---
title: Vanliga fel och varningar – Azure Search
description: Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under AI-anrikning i Azure Search.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028316"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Vanliga fel och varningar i AI-pipeline för anrikning i Azure Search

Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under AI-anrikning i Azure Search.

## <a name="errors"></a>Fel
Indexeringen stoppas när antalet fel överstiger ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Om du vill att indexerarna ska ignorera dessa fel (och hoppa över "misslyckade dokument") bör du överväga att uppdatera `maxFailedItems` och `maxFailedItemsPerBatch` enligt beskrivningen [här](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Varje misslyckat dokument tillsammans med dess dokument nyckel (när det är tillgängligt) visas som ett fel i körnings statusen för indexeraren. Du kan använda [index-API: et](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att ladda upp dokumenten manuellt vid ett senare tillfälle om du har ställt in indexeraren för att tolerera felen.

I följande avsnitt får du hjälp att lösa fel som gör att indexeringen kan fortsätta.

### <a name="could-not-read-document"></a>Det gick inte att läsa dokumentet
Indexeraren kunde inte läsa dokumentet från data källan. Detta kan inträffa på grund av:

| Reason | Exempel | Action |
| --- | --- | --- |
| inkonsekventa fält typer i olika dokument | Värdets typ stämmer inte med kolumn typen. Det gick inte att lagra `'{47.6,-122.1}'` i Authors-kolumnen.  Förväntad typ är JArray. | Se till att typen för varje fält är samma för olika dokument. Om till exempel det första dokumentet `'startTime'`-fältet är ett DateTime-värde och i det andra dokumentet är det fel meddelandet. |
| fel från data källans underliggande tjänst | (från Cosmos DB) `{"Errors":["Request rate is large"]}` | Kontrol lera lagrings instansen för att säkerställa att den är felfri. Du kan behöva justera skalning/partitionering. |
| tillfälliga problem | Ett fel på transport nivå har uppstått när resultat togs emot från servern. CSP TCP-Provider, fel: 0-en befintlig anslutning tvingades stänga av den fjärranslutna värden | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |

### <a name="could-not-extract-document-content"></a>Det gick inte att extrahera dokument innehåll
Indexeraren med en BLOB-datakälla kunde inte extrahera innehållet från dokumentet (till exempel en PDF-fil). Detta kan inträffa på grund av:

| Reason | Exempel | Action |
| --- | --- | --- |
| blobben överskrider storleks gränsen | Dokumentet är `'150441598'` byte, vilket överskrider den maximala storleken `'134217728'` byte för dokument extrahering för den aktuella tjänst nivån. | [BLOB-indexerings fel](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB har en innehålls typ som inte stöds | Dokumentet innehåller en innehålls typ som inte stöds `'image/png'` | [BLOB-indexerings fel](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| bloben är krypterad | Det gick inte att bearbeta dokumentet – det kan vara krypterat eller lösenordsskyddat. | [BLOB-inställningar](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| tillfälliga problem | Fel vid bearbetning av BLOB: Begäran avbröts: Begäran avbröts. | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |

### <a name="could-not-parse-document"></a>Det gick inte att parsa dokumentet
Indexeraren läser dokumentet från data källan, men det uppstod ett problem med att konvertera dokument innehållet till det angivna fält mappnings schemat. Detta kan inträffa på grund av:

| Reason | Exempel | Action |
| --- | --- | --- |
| Dokument nyckeln saknas | Dokument nyckeln kan inte vara tom eller saknas | Se till att alla dokument har giltiga dokument nycklar |
| Dokument nyckeln är ogiltig | Dokument nyckeln får innehålla högst 1024 tecken | Ändra dokument nyckeln så att den uppfyller verifierings kraven. |
| Det gick inte att använda fält mappning i ett fält | Det gick inte att använda mappnings funktionen `'functionName'` till fält `'fieldName'`. Matris får inte vara null. Parameter namn: byte | Dubbelt kontrol lera de [fält mappningar](search-indexer-field-mappings.md) som definierats för indexeraren och jämför med informationen i det angivna fältet för det misslyckade dokumentet. Det kan vara nödvändigt att ändra fält mappningarna eller dokument data. |
| Det gick inte att läsa fältvärdet | Det gick inte att läsa värdet för kolumnen `'fieldName'` vid index `'fieldIndex'`. Ett fel på transport nivå har uppstått när resultat togs emot från servern. CSP TCP-Provider, fel: 0-en befintlig anslutning tvingades stänga av den fjärranslutna värden.) | Dessa fel beror vanligt vis på oväntade anslutnings problem med data källans underliggande tjänst. Försök att köra dokumentet via din indexerare igen senare. |

### <a name="could-not-index-document"></a>Det gick inte att indexera dokumentet
Dokumentet lästes och bearbetades, men indexeraren kunde inte lägga till det i Sök indexet. Detta kan inträffa på grund av:

| Reason | Exempel | Action |
| --- | --- | --- |
| Ett fält innehåller en term som är för stor | En term i dokumentet är större än [32 KB-gränsen](search-limits-quotas-capacity.md#api-request-limits) | Du kan undvika den här begränsningen genom att se till att fältet inte har kon figurer ATS som filtrerat, aspekt Bart eller sorterbart.
| Dokumentet är för stort för att indexeras | Ett dokument är större än [Max storleken för API-begäranden](search-limits-quotas-capacity.md#api-request-limits) | [Så här indexerar du stora data mängder](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Kompetens ingången ' languageCode ' har följande språk koder, "X, Y, Z", minst en som är ogiltig.
Ett eller flera av värdena som angavs i den valfria `languageCode`-indatamängden för en underordnad färdighet stöds inte. Detta kan inträffa om du skickar utdata från [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) till efterföljande kunskaper och utdata består av fler språk än vad som stöds i de efterföljande färdigheterna.

Om du vet att din data uppsättning är på ett språk bör du ta bort [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och kompetensen `languageCode` och använda färdighets parametern `defaultLanguageCode` för den kunskapen i stället, förutsatt att språket stöds för den kunskapen.

Om du vet att din data uppsättning innehåller flera språk och därför behöver [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) -och `languageCode`-indata, kan du överväga att lägga till en [ConditionalSkill](cognitive-search-skill-conditional.md) för att filtrera bort texten med språk som inte stöds innan du skickar in text till den efterföljande färdigheten.  Här är ett exempel på hur det kan se ut för EntityRecognitionSkill:

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

Här följer några referenser för de språk som stöds för närvarande för var och en av de kunskaper som kan skapa det här fel meddelandet:
* [Textanalys språk som stöds](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (för [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)och [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Språk som stöds av Translator](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (för [texten TranslationSkill](cognitive-search-skill-text-translation.md))
* [Text SplitSkill](cognitive-search-skill-textsplit.md) Språk som stöds: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>Kompetensen utfördes inte inom tids gränsen
Det finns två fall där du kan stöta på det här fel meddelandet, som var och en behandlas på olika sätt. Följ anvisningarna nedan, beroende på vilken kompetens som returnerade felet åt dig.

#### <a name="built-in-cognitive-service-skills"></a>Inbyggda kunskaper om kognitiva tjänster
Många av de inbyggda kognitiva färdigheterna, till exempel språk identifiering, entitets igenkänning eller OCR, backas upp av en kognitiv tjänst-API-slutpunkt. Ibland finns det tillfälliga problem med dessa slut punkter och tids gränsen för en begäran upphör. För tillfälliga problem finns det ingen åtgärd förutom att vänta och försöka igen. Som en minskning bör du överväga att ställa in indexeraren att [köras enligt ett schema](search-howto-schedule-indexers.md). Schemalagd indexering hämtar var den slutade. Förutsatt att de tillfälliga problemen är lösta, bör indexerings-och kognitiva kompetens bearbetningen fortsätta vid nästa schemalagda körning.

#### <a name="custom-skills"></a>Anpassade färdigheter
Om du stöter på ett tids gräns fel med en anpassad färdighet som du har skapat, finns det några saker du kan prova. Börja med att granska din anpassade färdighet och se till att den inte fastnar i en oändlig slinga och att den returnerar ett resultat konsekvent. När du har bekräftat att är fallet ska du ta reda på hur körnings tiden för din färdighet är. Om du inte uttryckligen angav ett `timeout`-värde i din anpassade kunskaps definition, är standard `timeout` 30 sekunder. Om 30 sekunder inte är tillräckligt lång för att din kunskap ska kunna köras, kan du ange ett högre `timeout`-värde i din anpassade kunskaps definition. Här är ett exempel på en anpassad kunskaps definition där tids gränsen har angetts till 90 sekunder:

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

Det maximala värdet som du kan ange för parametern `timeout` är 230 sekunder.  Om din anpassade färdighet inte kan köras konsekvent inom 230 sekunder kan du överväga att minska `batchSize` i din anpassade färdighet så att den har färre dokument för bearbetning i en enda körning.  Om du redan har angett din `batchSize` till 1 måste du skriva om färdigheten för att kunna köra under 230 sekunder eller dela upp den i flera anpassade kunskaper så att körnings tiden för en enskild anpassad färdighet är högst 230 sekunder. Läs den [anpassade kunskaps dokumentationen](cognitive-search-custom-skill-web-api.md) för mer information.

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Det gick inte att `MergeOrUpload` | `Delete`-dokument till Sök indexet

Dokumentet lästes och bearbetades, men indexeraren kunde inte lägga till det i Sök indexet. Detta kan inträffa på grund av:

| Reason | Exempel | Action |
| --- | --- | --- |
| En term i dokumentet är större än [32 KB-gränsen](search-limits-quotas-capacity.md#api-request-limits) | Ett fält innehåller en term som är för stor | Du kan undvika den här begränsningen genom att se till att fältet inte har kon figurer ATS som filtrerat, aspekt Bart eller sorterbart.
| Ett dokument är större än [Max storleken för API-begäranden](search-limits-quotas-capacity.md#api-request-limits) | Dokumentet är för stort för att indexeras | [Så här indexerar du stora data mängder](search-howto-large-index.md)
| Det går inte att ansluta till mål indexet (som kvarstår efter återförsök) eftersom tjänsten är under annan belastning, t. ex. frågor eller indexering. | Det gick inte att upprätta en anslutning till uppdaterings index. Sök tjänsten är hårt belastad. | [Skala upp Sök tjänsten](search-capacity-planning.md)
| Sök tjänsten korrigeras för tjänst uppdatering eller är i mitten av en omkonfiguration av topologin. | Det gick inte att upprätta en anslutning till uppdaterings index. Sök tjänsten är för närvarande nere/Search-tjänsten genomgår en över gång. | Konfigurera tjänsten med minst tre repliker för 99,9% tillgänglighet per [SLA-dokumentation](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Haveri i den underliggande beräknings-/nätverks resursen (sällsynt) | Det gick inte att upprätta en anslutning till uppdaterings index. Ett okänt fel uppstod. | Konfigurera indexerare så att de [körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett felaktigt tillstånd.

##  <a name="warnings"></a>Varna
Varningar slutar inte att indexera, men de anger villkor som kan leda till oväntade resultat. Oavsett om du vidtar åtgärder eller inte beror på data och ditt scenario.

### <a name="skill-input-was-truncated"></a>Kompetens ineffekten trunkerades
Kognitiva kunskaper har gränser för text längden som kan analyseras samtidigt. Om text ingången för dessa kunskaper överskrider den gränsen kommer vi att trunkera texten för att uppfylla gränsen och sedan utföra anrikningen på den trunkerade texten. Det innebär att kunskapen körs, men inte över alla dina data.

I exemplet nedan kan `'text'`-indatamängden utlösa den här varningen om den ligger över tecken gränsen. Du hittar de ingångs begränsningar du har i [kunskaps dokumentationen](cognitive-search-predefined-skills.md).

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

Om du vill se till att all text analyseras bör du överväga att använda [delade kunskaper](cognitive-search-skill-textsplit.md).