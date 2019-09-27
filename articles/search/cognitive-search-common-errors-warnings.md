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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329388"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Vanliga fel och varningar i AI-pipeline för anrikning i Azure Search

Den här artikeln innehåller information och lösningar på vanliga fel och varningar som du kan stöta på under AI-anrikning i Azure Search.

## <a name="errors"></a>Fel
Indexeringen stoppas när antalet fel överstiger ["maxfaileditems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). I följande avsnitt får du hjälp att lösa fel som gör att indexeringen kan fortsätta.

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

##  <a name="warnings"></a>Varningar
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