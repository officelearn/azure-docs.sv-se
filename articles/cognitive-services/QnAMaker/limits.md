---
title: Gränser och gränser - QnA Maker
description: QnA Maker har metagränser för delar av kunskapsbasen och tjänsten. Det är viktigt att hålla din kunskapsbas inom dessa gränser för att testa och publicera.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273343"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kunskapsbas gränser och gränser

QnA Maker-gränser som anges nedan är en kombination av [azure Cognitive Search-prisnivågränserna](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) och [QnA Maker-prisnivågränserna](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Du måste känna till båda uppsättningarna gränser för att förstå hur många kunskapsbaser du kan skapa per resurs och hur stor varje kunskapsbas kan växa.

## <a name="knowledge-bases"></a>Kunskapsbaser

Det maximala antalet kunskapsbaser baseras på [Azure Cognitive Search-nivågränser](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Cognitive Search-nivå** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximalt antal tillåtna publicerade kunskapsbaser|2|14|49|199|199|2,999|

 Om din nivå till exempel har 15 tillåtna index kan du publicera 14 kunskapsbaser (1 index per publicerad kunskapsbas). Det femtonde `testkb`indexet, används för alla kunskapsbaser för redigering och testning.

## <a name="extraction-limits"></a>Extraheringsgränser

### <a name="file-naming-constraints"></a>Villkor för filnamngivning

Filnamnen får inte innehålla följande tecken:

|Använd inte tecken|
|--|
|Ett citat`'`|
|Dubbel offert`"`|

### <a name="maximum-file-size"></a>Maximal filstorlek

|Format|Max filstorlek (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximalt antal filer

Det maximala antalet filer som kan extraheras och maximal filstorlek baseras på **[qnA Maker-prisnivågränserna](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximalt antal djuplänkar från URL

Det maximala antalet djuplänkar som kan genomsökas för extrahering av QnAs från en URL-sida är **20**.

## <a name="metadata-limits"></a>Begränsningar för metadata

Metadata visas som en textbaserad nyckel:värdepar, till exempel `product:windows 10`. Den lagras och jämförs i gemener.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Efter Azure Cognitive Search-prisnivå

Maximalt antal metadatafält per kunskapsbas baseras på begränsningarna för **[azure cognitive search-nivån](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)**.

|**Azure Cognitive Search-nivå** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximala metadatafält per QnA Maker-tjänst (i alla KBs)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Efter namn och värde

Längden och godtagbara tecken för metadatanamn och metadatavärde visas i följande tabell.

|Objekt|Tillåtna tecken|Regex mönster matchning|Max tecken|
|--|--|--|--|
|Namn (nyckel)|Tillåter<br>alfanumeriskt (bokstäver och siffror)<br>`_`(understreck)<br> Får inte innehålla blanksteg.|`^[a-zA-Z0-9_]+$`|100|
|Värde|Tillåter allt utom<br>`:`-Jag är inte så bra som jag vet.<br>`|`(vertikalt rör)<br>Endast ett värde tillåts.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Innehållsbegränsningar för kunskapsbasen
Övergripande gränser för innehållet i kunskapsbasen:
* Svarstextens längd: 25 000
* Längden på frågetexten: 1 000
* Metadatanyckelns/värdetextens längd: 100
* Tecken som stöds för metadatanamn: Alfabet, siffror och`_`
* Tecken som stöds för `:` metadatavärde: Alla utom och`|`
* Filnamnslängd: 200
* Filformat som stöds: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Maximalt antal alternativa frågor: 300
* Maximalt antal frågesvarspar: Beror på den **[Azure Cognitive Search-nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** som valts. En fråga och svar par kartor till ett dokument på Azure Cognitive Search index.
* URL/HTML-sida: 1 miljon tecken

## <a name="create-knowledge-base-call-limits"></a>Skapa samtalsgränser för kunskapsbas:
Dessa representerar gränserna för varje skapa kunskapsbasåtgärd. det vill exempel på att klicka på *Skapa KB* eller anropa Api:et för CreateKnowledgeBase.
* Maximalt antal alternativa frågor per svar: 300
* Maximalt antal webbadresser: 10
* Maximalt antal filer: 10

## <a name="update-knowledge-base-call-limits"></a>Uppdatera samtalsgränser för kunskapsbasen
Dessa representerar gränserna för varje uppdateringsåtgärd. det vill exempel på genom att klicka på *Spara och träna* eller anropa Api:et för UpdateKnowledgeBase.
* Längd på varje källnamn: 300
* Maximalt antal alternativa frågor som lagts till eller tagits bort: 300
* Maximalt antal metadatafält som har lagts till eller tagits bort: 10
* Maximalt antal webbadresser som kan uppdateras: 5

## <a name="next-steps"></a>Nästa steg

Lär dig när och hur du ändrar [tjänstprisnivåer](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
