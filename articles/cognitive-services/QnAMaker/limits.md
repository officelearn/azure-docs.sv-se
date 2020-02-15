---
title: Begränsningar och gränser – QnA Maker
description: QnA Maker har meta-gränser för delar av knowledge base och tjänsten. Det är viktigt att hålla din kunskapsbas inom de här gränserna för att testa och publicera.
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 7fdf45a4a22f6d9ffe123f5998592739402be55f
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252016"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kunskapsbas begränsningar och gränser

QnA Maker gränser som anges nedan är en kombination av [gränserna för Azure kognitiv sökning-pris nivåer](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) och [QNA Maker pris nivå gränser](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Du måste känna till båda uppsättningarna med gränser för att förstå hur många kunskaps baser du kan skapa per resurs och hur stor varje kunskaps bas kan växa.

## <a name="knowledge-bases"></a>Kunskaps banker

Det maximala antalet kunskaps baser baseras på [gränserna för Azure kognitiv sökning nivån](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Kognitiv sökning-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Högsta tillåtna antal publicerade kunskaps banker|2|14|49|199|199|2,999|

 Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps banker (1 index per publicerad kunskaps bas). Det femtonde indexet `testkb`används för alla kunskaps banker för redigering och testning.

## <a name="extraction-limits"></a>Extrahering av gränser

### <a name="file-naming-constraints"></a>Begränsningar för fil namn givning

Fil namn får inte innehålla följande tecken:

|Använd inte Character|
|--|
|Enkelt citat `'`|
|Dubbel citat `"`|

### <a name="maximum-file-size"></a>Maximal filstorlek

|Format|Maximal fil storlek (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximalt antal filer

Det maximala antalet filer som kan extraheras och maximal fil storlek baseras på **[QNA Maker pris nivå gränser](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximalt antal djup länkar från URL

Det maximala antalet djup länkar som kan crawlas för extrahering av kring från en URL-sida är **20**.

## <a name="metadata-limits"></a>Metadata-gränser

Metadata lagras och jämförs i gemener.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Av Azure Kognitiv sökning pris nivå

Det maximala antalet metadata-fält per kunskaps bas baseras på **[Azure kognitiv sökning nivå gränser](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Azure Kognitiv sökning-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal metadatafält per QnA Maker-tjänsten (över alla KB-artiklar)|1,000|100 *|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Efter namn och värde

Längden på och godkända tecken för namn och värde för metadata visas i följande tabell.

|Objekt|Tillåtna tecken|Matchning av regex-mönster|Max tecken|
|--|--|--|--|
|Namn|Innebär<br>alfanumeriskt (bokstäver och siffror)<br>`_` (under streck)|`^[a-zA-Z0-9_]+$`|100|
|Värde|Tillåter allt utom<br>`:` (kolon)<br>`|` (lodrät pipe)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Gränser för kunskapsbas
Övergripande gränser för innehållet i kunskapsbasen:
* Längden på svarstexten: 25 000
* Längden på frågetexten: 1 000
* Textlängden metadata nyckel/värde: 100
* Tecken som stöds för namn på metadata: alfabet, siffror och `_`
* Tecken som stöds för metadata-värde: alla utom `:` och `|`
* Längden på filnamnet: 200
* Filformat som stöds: ”TSV”, ”.pdf”, ”.txt”, ”.docx”, ”.xlsx”.
* Maximalt antal alternativa frågor: 300
* Maximalt antal frågor/svar-par: beror på den valda **[Azure kognitiv sökning-nivån](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** . Ett fråge-och svars par mappar till ett dokument i Azure Kognitiv sökning index.
* URL/HTML-sida: 1 000 000 tecken

## <a name="create-knowledge-base-call-limits"></a>Skapa kunskapsbas anrop begränsningar:
Dessa motsvarar gränserna för varje åtgärd för att skapa kunskaps bas. Det gör du genom att klicka på *skapa KB* eller anropa CreateKnowledgeBase-API: et.
* Maximalt antal alternativa frågor per svar: 300
* Maximalt antal URL: er: 10
* Maximalt antal filer: 10

## <a name="update-knowledge-base-call-limits"></a>Uppdatera kunskapsbas anrop gränser
Dessa representerar gränserna för varje uppdaterings åtgärd. Det gör du genom att klicka på *Spara och träna* eller anropa UpdateKnowledgeBase-API: et.
* Längden på varje källnamn: 300
* Maximalt antal alternativa frågor som lagts till eller tagits bort: 300
* Maximalt antal metadatafält läggs till eller tas bort: 10
* Maximalt antal URL: er som kan uppdateras: 5

## <a name="next-steps"></a>Nästa steg

Lär dig när och hur du ändrar [pris nivåer för tjänster](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
