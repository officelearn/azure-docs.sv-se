---
title: Begränsningar och gränser – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker har meta-gränser för delar av knowledge base och tjänsten. Det är viktigt att hålla din kunskapsbas inom de här gränserna för att testa och publicera.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b90b4806e86ed0ba33500cf31a6ed892241ceabe
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423450"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kunskapsbas begränsningar och gränser

QnA Maker gränser som anges nedan är en kombination av [Azure Search pris nivå gränser](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) och [QNA Maker pris nivå gränser](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Du måste känna till båda uppsättningarna med gränser för att förstå hur många kunskaps baser du kan skapa per resurs och hur stor varje kunskaps bas kan växa.

## <a name="knowledge-bases"></a>Kunskaps banker

Det maximala antalet kunskaps baser baseras på [Azure Search nivå gränser](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Azure Search-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Högsta tillåtna antal publicerade kunskaps banker|2|14|49|199|199|2,999|

 Om din nivå till exempel har 15 tillåtna index, kan du publicera 14 kunskaps banker (1 index per publicerad kunskaps bas). Det femtonde indexet `testkb`används för alla kunskaps banker för redigering och testning. 

## <a name="extraction-limits"></a>Extrahering av gränser

### <a name="maximum-number-of-files"></a>Maximalt antal filer

Det maximala antalet filer som kan extraheras och maximal fil storlek baseras på **[QNA Maker pris nivå gränser](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Maximalt antal djup länkar från URL

Det maximala antalet djup länkar som kan crawlas för extrahering av kring från en URL-sida är **20**.

## <a name="metadata-limits"></a>Metadata-gränser

Det maximala antalet metadata-fält per kunskaps bas baseras på **[Azure Search nivå gränser](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Azure Search-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal metadatafält per QnA Maker-tjänsten (över alla KB-artiklar)|1,000|100 *|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>Gränser för kunskapsbas
Övergripande gränser för innehållet i kunskapsbasen:
* Svars textens längd: 25,000
* Längd på frågetext: 1,000
* Längd på nyckel/värde-text för metadata: 100
* Tecken som stöds för metadata: Alfabet, siffror och _  
* Tecken som stöds för metadata-värde: Alla utom: och | 
* Fil namnets längd: 200
* Filformat som stöds: ”TSV”, ”.pdf”, ”.txt”, ”.docx”, ”.xlsx”.
* Maximalt antal alternativa frågor: 300
* Maximalt antal svars par för frågor: Beror på vilken **[Azure Search nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** som valts. Ett fråge-och svars par mappar till ett dokument på Azure Search index. 
* URL/HTML-sida: 1 000 000 tecken

## <a name="create-knowledge-base-call-limits"></a>Skapa kunskapsbas anrop begränsningar:
Dessa representerar gränser för varje skapa kunskapsbas åtgärden. det vill säga att klicka på *skapa KB* eller anropa CreateKnowledgeBase API.
* Maximalt antal alternativa frågor per svar: 300
* Maximalt antal URL: er: 10
* Maximalt antal filer: 10

## <a name="update-knowledge-base-call-limits"></a>Uppdatera kunskapsbas anrop gränser
Dessa representerar gränser för varje uppdateringsåtgärd; det vill säga att klicka på *spara och träna* eller anropa UpdateKnowledgeBase API.
* Längd för varje käll namn: 300
* Maximalt antal alternativa frågor som lagts till eller tagits bort: 300
* Maximalt antal metadata-fält har lagts till eller tagits bort: 10
* Maximalt antal URL: er som kan uppdateras: 5

## <a name="next-steps"></a>Nästa steg

Lär dig när och hur du ändrar tjänst nivåer:

* [QNA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Om du behöver mer källfiler eller större dokument i din kunskaps bas, utöver din nuvarande nivå, uppgraderar du QnA Maker-tjänstens pris nivå.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): När din kunskaps bas behöver hantera fler förfrågningar från din klient app uppgraderar du din app service-pris nivå.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): När du planerar att ha många kunskaps baser uppgraderar du Azure Search-tjänstens pris nivå.
