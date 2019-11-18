---
title: Index fel och varningar
titleSuffix: Azure Cognitive Search
description: Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under AI-anrikning i Azure Kognitiv sökning.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6b51581b5a8f94419dba60eee72669a3e1261b24
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151573"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Felsöka vanliga index fel och varningar i Azure Kognitiv sökning

Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under indexering och AI-anrikning i Azure Kognitiv sökning.

Indexeringen stoppas när antalet fel överstiger ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Om du vill att indexerarna ska ignorera dessa fel (och hoppa över "misslyckade dokument") bör du överväga att uppdatera `maxFailedItems` och `maxFailedItemsPerBatch` enligt beskrivningen [här](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Varje misslyckat dokument tillsammans med dess dokument nyckel (när det är tillgängligt) visas som ett fel i körnings statusen för indexeraren. Du kan använda [index-API: et](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) för att ladda upp dokumenten manuellt vid ett senare tillfälle om du har ställt in indexeraren för att tolerera felen.

Fel informationen i den här artikeln kan hjälpa dig att lösa fel som gör att indexeringen kan fortsätta.

Varningar slutar inte att indexera, men de anger villkor som kan leda till oväntade resultat. Oavsett om du vidtar åtgärder eller inte beror på data och ditt scenario.

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Fel: det gick inte att läsa dokumentet

Indexeraren kunde inte läsa dokumentet från data källan. Detta kan inträffa på grund av:

| Orsak | Information/exempel | Lösning |
| --- | --- | --- |
| inkonsekventa fält typer i olika dokument | Värdets typ stämmer inte med kolumn typen. Det gick inte att lagra `'{47.6,-122.1}'` i Authors-kolumnen.  Förväntad typ är JArray. | Se till att typen för varje fält är samma för olika dokument. Om t. ex. det första dokument `'startTime'`s fältet är ett datum/tid och i det andra dokumentet är det en sträng. detta fel uppstår. |
| fel från data källans underliggande tjänst | (från Cosmos DB) `{"Errors":["Request rate is large"]}` | Kontrol lera lagrings instansen för att säkerställa att den är felfri. Du kan behöva justera skalning/partitionering. |
| tillfälliga problem | Ett fel på transport nivå har uppstått när resultat togs emot från servern. (provider: TCP-Provider, fel: 0-en befintlig anslutning tvingades stänga av den fjärranslutna värden | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-document-content"></a>Fel: det gick inte att extrahera dokument innehåll
Indexeraren med en BLOB-datakälla kunde inte extrahera innehållet från dokumentet (till exempel en PDF-fil). Detta kan inträffa på grund av:

| Orsak | Information/exempel | Lösning |
| --- | --- | --- |
| blobben överskrider storleks gränsen | Dokumentet är `'150441598'` byte, vilket överskrider den maximala storleken `'134217728'` byte för dokument extrahering för den aktuella tjänst nivån. | [BLOB-indexerings fel](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| BLOB har en innehålls typ som inte stöds | Dokumentet innehåller en innehålls typ som inte stöds `'image/png'` | [BLOB-indexerings fel](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| bloben är krypterad | Det gick inte att bearbeta dokumentet – det kan vara krypterat eller lösenordsskyddat. | Du kan hoppa över blobben med [BLOB-inställningar](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| tillfälliga problem | Det gick inte att bearbeta blobben: begäran avbröts: begäran avbröts. | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Fel: det gick inte att parsa dokumentet
Indexeraren läser dokumentet från data källan, men det uppstod ett problem med att konvertera dokument innehållet till det angivna fält mappnings schemat. Detta kan inträffa på grund av:

| Orsak | Information/exempel | Lösning |
| --- | --- | --- |
| Dokument nyckeln saknas | Dokument nyckeln kan inte vara tom eller saknas | Se till att alla dokument har giltiga dokument nycklar |
| Dokument nyckeln är ogiltig | Dokument nyckeln får innehålla högst 1024 tecken | Ändra dokument nyckeln så att den uppfyller verifierings kraven. |
| Det gick inte att använda fält mappning i ett fält | Det gick inte att använda mappnings funktionen `'functionName'` till fält `'fieldName'`. Matris får inte vara null. Parameter namn: byte | Dubbelt kontrol lera de [fält mappningar](search-indexer-field-mappings.md) som definierats för indexeraren och jämför med informationen i det angivna fältet för det misslyckade dokumentet. Det kan vara nödvändigt att ändra fält mappningarna eller dokument data. |
| Det gick inte att läsa fältvärdet | Det gick inte att läsa värdet för kolumnen `'fieldName'` vid index `'fieldIndex'`. Ett fel på transport nivå har uppstått när resultat togs emot från servern. (provider: TCP-Provider, fel: 0-en befintlig anslutning tvingades stänga av den fjärranslutna värden.) | Dessa fel beror vanligt vis på oväntade anslutnings problem med data källans underliggande tjänst. Försök att köra dokumentet via din indexerare igen senare. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Fel: det gick inte att köra kompetensen
Indexeraren kunde inte köra en färdighet i färdigheter.

| Orsak | Information/exempel | Lösning |
| --- | --- | --- |
| Ett fält innehåller en term som är för stor | En term i dokumentet är större än [32 KB-gränsen](search-limits-quotas-capacity.md#api-request-limits) | Du kan undvika den här begränsningen genom att se till att fältet inte har kon figurer ATS som filtrerat, aspekt Bart eller sorterbart.
| Dokumentet är för stort för att indexeras | Ett dokument är större än [Max storleken för API-begäranden](search-limits-quotas-capacity.md#api-request-limits) | [Så här indexerar du stora data mängder](search-howto-large-index.md)
| Problem med tillfälliga anslutningar | Ett tillfälligt fel har uppstått. Försök igen senare. | Ibland finns det ibland oväntade anslutnings problem. Försök att köra dokumentet via din indexerare igen senare. |
| Potentiell produkt fel | Det uppstod ett oväntat fel. | Detta indikerar en okänd fel klass och det kan betyda att det finns en produkt bugg. Använd ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp. |
| Ett fel uppstod vid körningen av en färdighet | (Från slå samman färdighet) Ett eller flera förskjutnings värden var ogiltiga och kunde inte parsas. Objekt infogades i slutet av texten | Använd informationen i fel meddelandet för att åtgärda problemet. Den här typen av haveri kräver åtgärd för att lösa problemet. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Fel: det gick inte att köra kompetensen eftersom webb-API-begäran misslyckades
Det gick inte att köra kompetensen eftersom anropet till webb-API: et misslyckades. Den här klassen av fel inträffar vanligt vis när anpassade kunskaper används. i så fall måste du Felsöka den anpassade koden för att lösa problemet. Om felet i stället är från en inbyggd färdighet kan du läsa fel meddelandet för att få hjälp med att åtgärda problemet.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Fel: det gick inte att köra kompetensen eftersom kunskaps svaret för webb-API är ogiltigt
Det gick inte att köra kompetensen eftersom anropet till webb-API: et returnerade ett ogiltigt svar. Den här klassen av fel inträffar vanligt vis när anpassade kunskaper används. i så fall måste du Felsöka den anpassade koden för att lösa problemet. Om det i stället är från en inbyggd färdighet kan du skicka in ett [support ärende](https://ms.portal.azure.com/#create/Microsoft.Support) för att få hjälp.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Fel: det gick inte att köra kompetensen inom tids gränsen
Det finns två fall där du kan stöta på det här fel meddelandet, som var och en behandlas på olika sätt. Följ anvisningarna nedan, beroende på vilken kompetens som returnerade felet åt dig.

### <a name="built-in-cognitive-service-skills"></a>Inbyggda kunskaper om kognitiva tjänster
Många av de inbyggda kognitiva färdigheterna, till exempel språk identifiering, entitets igenkänning eller OCR, backas upp av en kognitiv tjänst-API-slutpunkt. Ibland finns det tillfälliga problem med dessa slut punkter och tids gränsen för en begäran upphör. För tillfälliga problem finns det ingen åtgärd förutom att vänta och försöka igen. Som en minskning bör du överväga att ställa in indexeraren att [köras enligt ett schema](search-howto-schedule-indexers.md). Schemalagd indexering hämtar var den slutade. Förutsatt att de tillfälliga problemen är lösta, bör indexerings-och kognitiva kompetens bearbetningen fortsätta vid nästa schemalagda körning.

### <a name="custom-skills"></a>Anpassade färdigheter
Om du stöter på ett tids gräns fel med en anpassad färdighet som du har skapat, finns det några saker du kan prova. Börja med att granska din anpassade färdighet och se till att den inte fastnar i en oändlig slinga och att den returnerar ett resultat konsekvent. När du har bekräftat att är fallet ska du ta reda på hur körnings tiden för din färdighet är. Om du inte uttryckligen angav ett `timeout`-värde i din anpassade kunskaps definition är standard `timeout` 30 sekunder. Om 30 sekunder inte är tillräckligt lång för att din kunskap ska kunna köras, kan du ange ett högre `timeout` värde i din anpassade kunskaps definition. Här är ett exempel på en anpassad kunskaps definition där tids gränsen har angetts till 90 sekunder:

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

Det maximala värde som du kan ange för parametern `timeout` är 230 sekunder.  Om din anpassade färdighet inte kan köras konsekvent inom 230 sekunder kan du överväga att minska `batchSize` av din anpassade färdighet så att den har färre dokument för bearbetning i en enda körning.  Om du redan har angett `batchSize` till 1 måste du skriva om färdigheten för att kunna köra under 230 sekunder eller dela upp den i flera anpassade kunskaper så att körnings tiden för en enskild anpassad färdighet är högst 230 sekunder. Läs den [anpassade kunskaps dokumentationen](cognitive-search-custom-skill-web-api.md) för mer information.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Fel: det gick inte att`MergeOrUpload`|`Delete`-dokument till Sök indexet

Dokumentet lästes och bearbetades, men indexeraren kunde inte lägga till det i Sök indexet. Detta kan inträffa på grund av:

| Orsak | Information/exempel | Lösning |
| --- | --- | --- |
| En term i dokumentet är större än [32 KB-gränsen](search-limits-quotas-capacity.md#api-request-limits) | Ett fält innehåller en term som är för stor | Du kan undvika den här begränsningen genom att se till att fältet inte har kon figurer ATS som filtrerat, aspekt Bart eller sorterbart.
| Ett dokument är större än [Max storleken för API-begäranden](search-limits-quotas-capacity.md#api-request-limits) | Dokumentet är för stort för att indexeras | [Så här indexerar du stora data mängder](search-howto-large-index.md)
| Dokumentet innehåller för många objekt i samlingen | En samling i dokumentet överskrider [Max antalet element i alla komplexa samlings gränser](search-limits-quotas-capacity.md#index-limits) | Vi rekommenderar att du minskar storleken på den komplexa samlingen i dokumentet till under gränsen och undviker hög lagrings belastning.
| Det går inte att ansluta till mål indexet (som kvarstår efter återförsök) eftersom tjänsten är under annan belastning, t. ex. frågor eller indexering. | Det gick inte att upprätta en anslutning till uppdaterings index. Sök tjänsten är hårt belastad. | [Skala upp Sök tjänsten](search-capacity-planning.md)
| Sök tjänsten korrigeras för tjänst uppdatering eller är i mitten av en omkonfiguration av topologin. | Det gick inte att upprätta en anslutning till uppdaterings index. Sök tjänsten är för närvarande nere/Search-tjänsten genomgår en över gång. | Konfigurera tjänsten med minst tre repliker för 99,9% tillgänglighet per [SLA-dokumentation](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Haveri i den underliggande beräknings-/nätverks resursen (sällsynt) | Det gick inte att upprätta en anslutning till uppdaterings index. Ett okänt fel uppstod. | Konfigurera indexerare så att de [körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett felaktigt tillstånd.
| En indexerings förfrågan som gjorts till mål indexet bekräftades inte inom en tids gräns på grund av nätverks problem. | Det gick inte att upprätta en anslutning till Sök indexet inom rimlig tid. | Konfigurera indexerare så att de [körs enligt ett schema](search-howto-schedule-indexers.md) för att hämta från ett felaktigt tillstånd. Dessutom kan du försöka med att sänka [batchstorleken](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) för indexeraren om detta fel tillstånd kvarstår.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Fel: det gick inte att indexera dokumentet eftersom index data till index var ogiltiga

Dokumentet lästes och bearbetades, men på grund av ett matchnings fel i konfigurationen av index fälten och typen av data som extraherats av indexeraren, gick det inte att lägga till i Sök indexet. Detta kan inträffa på grund av:

| Orsak | Information/exempel
| --- | ---
| Data typen för det eller de fält som extraherats av indexeraren är inte kompatibelt med data modellen för motsvarande mål index fält. | Data fältets_data_i dokumentet med nyckeln_data_har ett ogiltigt värde av typen EDM. String. Den förväntade typen var ' Collection (EDM. String) '. |
| Det gick inte att extrahera någon JSON-entitet från ett sträng värde. | Det gick inte att parsa värdet ' av typen ' EDM. String ' ' för fält_data_som ett JSON-objekt. Fel: efter parsning av ett värde påträffades ett oväntat Character:. Sökväg '_sökväg_', rad 1, position 3162. ' |
| Det gick inte att extrahera en samling JSON-entiteter från ett sträng värde.  | Det gick inte att parsa värdet ' av typen ' EDM. String ' ' för fält_data_som en JSON-matris. Fel: efter parsning av ett värde påträffades ett oväntat Character:. Sökväg [0], rad 1, position 27. |
| En okänd typ upptäcktes i käll dokumentet. | Okänd typ '_okänd_' kan inte indexeras |
| En inkompatibel notation för geografi punkter användes i käll dokumentet. | Sträng litteraler för well punkt stöds inte. Använd ingångar av polyjson-punkter i stället |

I alla dessa fall refererar du till [data typer som stöds](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) och [data typs mappning för indexerare](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) för att se till att du skapar index schemat korrekt och har konfigurerat lämpliga [fält mappningar för indexeraren](search-indexer-field-mappings.md). Fel meddelandet innehåller information som kan hjälpa till att spåra källan till matchnings felet.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Fel: det gick inte att bearbeta dokumentet inom indexeraren maximal kör tid

Det här felet uppstår när indexeraren inte kan slutföra bearbetningen av ett enstaka dokument från data källan inom den tillåtna körnings tiden. [Maximal kör tid](search-limits-quotas-capacity.md#indexer-limits) är kortare när färdighetsuppsättningar används. Om det här felet uppstår, om maxFailedItems har angetts till ett annat värde än 0, hoppar indexeraren över dokumentet vid framtida körningar så att indexeringen kan fortsätta. Om du inte kan välja att hoppa över några dokument, eller om du ser det här felet konsekvent, bör du överväga att dela upp dokument i mindre dokument så att delvis förloppet kan göras inom en enskild indexerare-körning.

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-could-not-execute-skill-because-a-skill-input-was-invalid"></a>Varning! det gick inte att köra kompetensen eftersom en kompetens information var ogiltig
Indexeraren kunde inte köra en färdighet i färdigheter eftersom det saknades en inmatare, fel typ eller på annat sätt ogiltig.

Kognitiva kunskaper har obligatoriska indata och valfria indata. Till exempel har den här [extraheringen av nyckel fraser](cognitive-search-skill-keyphrases.md) två obligatoriska indata `text`, `languageCode`och inga valfria indata. Om några obligatoriska indata är ogiltiga hoppas kunskapen över och genererar en varning. Överhoppade kunskaper genererar inga utdata, så om andra kunskaper använder utdata från den överhoppade kunskapen kan de generera ytterligare varningar.

Om du vill ange ett standardvärde i händelse av saknade indata kan du använda den [villkorliga kompetensen](cognitive-search-skill-conditional.md) för att generera ett standardvärde och sedan använda resultatet av den [villkorliga kompetensen](cognitive-search-skill-conditional.md) som färdighets indata.


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

| Orsak | Information/exempel | Lösning |
| --- | --- | --- |
| Kompetens ineffekten är av fel typ | Nödvändiga `X` för kompetens inflöden var inte av den förväntade typen `String`. Nödvändiga `X` för kompetens in hade inte det förväntade formatet. | Vissa kunskaper förväntar sig indata av särskilda typer, till exempel [sentiment-kompetens](cognitive-search-skill-sentiment.md) förväntar sig `text` att vara en sträng. Om indatan anger ett värde som inte är ett sträng värde, körs inte kompetensen och genererar inga utdata. Se till att data uppsättningen har inmatnings värden som är enhetliga i typ eller Använd en [anpassad webb-API-färdighet](cognitive-search-custom-skill-web-api.md) för att Förbearbeta indatan. Om du vill iterera över en matris kontrollerar du att kunskaps kontexten och indatamängden har `*` på rätt platser. Vanligt vis ska både kontexten och Indatakällan sluta med `*` för matriser. |
| Kompetens ineffekt saknas | Nödvändig `X` för kompetens inflöde saknas. | Om alla dokument får den här varningen, är det förmodligen ett stavfel i inmatnings Sök vägarna och du bör dubbelt kontrol lera egenskaps namnets Skift läge, extra eller saknas `*` i sökvägen, och dokument från data källan definierar de nödvändiga indatana. |
| Inmatade kunskaper om språk kod är ogiltiga | Kompetens ingångs `languageCode` har följande språk koder `X,Y,Z`, minst en som är ogiltig. | Se mer information [nedan](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Varning! kompetens ingången languageCode har följande språk koder, X, Y, Z, och minst en av dem är ogiltig.
Ett eller flera av de värden som angavs i den valfria `languageCode` indatamängden för en underordnad färdighet stöds inte. Detta kan inträffa om du skickar utdata från [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) till efterföljande kunskaper och utdata består av fler språk än vad som stöds i de efterföljande färdigheterna.

Om du vet att din data uppsättning är på ett språk, bör du ta bort [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och `languageCode` kompetens inmatningar och använda den `defaultLanguageCode` färdighets parameter för den kunskapen i stället, förutsatt att språket stöds för den aktuella kompetensen.

Om du vet att din data uppsättning innehåller flera språk och därför behöver [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) och `languageCode` indata, kan du överväga att lägga till en [ConditionalSkill](cognitive-search-skill-conditional.md) för att filtrera bort texten med språk som inte stöds innan du skickar in text till den efterföljande färdigheten.  Här är ett exempel på hur det kan se ut för EntityRecognitionSkill:

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

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Varning! kompetens ineffekten trunkerades
Kognitiva kunskaper har gränser för text längden som kan analyseras samtidigt. Om text ingången för dessa kunskaper överskrider den gränsen kommer vi att trunkera texten för att uppfylla gränsen och sedan utföra anrikningen på den trunkerade texten. Det innebär att kunskapen körs, men inte över alla dina data.

I exemplet nedan kan `'text'` indatafält utlösa den här varningen om den överskrider tecken gränsen. Du hittar de ingångs begränsningar du har i [kunskaps dokumentationen](cognitive-search-predefined-skills.md).

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

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Varning! kunskaps svar för webb-API innehåller varningar
Indexeraren kunde köra en färdighet i färdigheter, men svaret från webb-API-begäran angav att det fanns varningar under körningen. Granska varningarna för att förstå hur dina data påverkas och huruvida en åtgärd krävs eller inte.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Varning! den aktuella indexerare-konfigurationen stöder inte stegvist förlopp

Den här varningen inträffar bara för Cosmos DB data källor.

Stegvis förlopp under indexeringen säkerställer att om körningen av Indexer avbryts vid tillfälliga haverier eller körnings tids gräns, kan indexeraren Hämta var den slutade nästa gång den körs, i stället för att behöva indexera om hela samlingen från grunden. Detta är särskilt viktigt när du indexerar stora samlingar.

Möjligheten att återuppta ett index jobb som inte är klart är predikat på att dokumentet har beställts av `_ts` kolumnen. Indexeraren använder tidsstämpeln för att avgöra vilket dokument som ska plockas härnäst. Om kolumnen `_ts` saknas eller om indexeraren inte kan avgöra om en anpassad fråga beställs, börjar indexeraren i början och du ser den här varningen.

Det är möjligt att åsidosätta det här beteendet, aktivera stegvisa framsteg och ignorera den här varningen med hjälp av konfigurations egenskapen `assumeOrderByHighWatermarkColumn`.

Mer information finns i [stegvisa framsteg och anpassade frågor](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Varning: vissa data gick förlorade under projektionen. Raden "X" i tabellen "Y" har sträng egenskapen "Z", vilken var för lång.

Den [Table Storage tjänsten](https://azure.microsoft.com/services/storage/tables) har gränser för hur stora [enhets egenskaper](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) kan vara. Strängar kan innehålla högst 32 000 tecken. Om en rad med en sträng egenskap som är längre än 32 000 tecken projiceras, bevaras bara de första 32 000 tecknen. Undvik det här problemet genom att undvika att projicera rader med sträng egenskaper som är längre än 32 000 tecken.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Varning: trunkerad extraherad text till X tecken
Indexerare begränsar hur mycket text som kan extraheras från ett dokument. Den här gränsen är beroende av pris nivån: 32 000 tecken för kostnads fri nivå, 64 000 för Basic och 4 000 000 för standard-, standard S2-och standard S3-nivåer. Text som trunkerades kommer inte att indexeras. Undvik den här varningen genom att dela upp dokument med stora mängder text till flera mindre dokument. 

Mer information finns i [gränser för indexerare](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Varning! det gick inte att mappa utmatnings fältet "X" till Sök indexet
Mappningar av utdatakolumner som refererar till icke-existerande/null-data genererar varningar för varje dokument och resulterar i ett tomt index fält. Du kan lösa det här problemet genom att dubbelklicka på fältet utdata – mappa käll Sök vägar för möjliga skrivfel eller ange ett standardvärde med hjälp av den [villkorliga kompetensen](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Varning! principen för data ändrings identifiering har kon figurer ATS för att använda nyckel kolumnen "X"
[Principer för data ändrings identifiering](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) har särskilda krav för de kolumner som de använder för att identifiera ändringar. Ett av dessa krav är att den här kolumnen uppdateras varje gång käll posten ändras. Ett annat krav är att det nya värdet för den här kolumnen är större än det tidigare värdet. Nyckel kolumnerna uppfyller inte det här kravet eftersom de inte ändras vid varje uppdatering. Undvik det här problemet genom att välja en annan kolumn för principen för ändrings identifiering.