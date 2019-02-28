---
title: Begränsningar och gränser – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker har meta-gränser för delar av knowledge base och tjänsten. Det är viktigt att hålla din kunskapsbas inom de här gränserna för att testa och publicera.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: fe15cafceea8128735f7241fa5e4187d4d9c47a9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960062"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker kunskapsbas begränsningar och gränser
Omfattande lista över gränserna över QnA Maker.

## <a name="knowledge-bases"></a>Kunskapsbaser

* Maximalt antal kunskapsbaser utifrån [gränserna för nivån av Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Azure Search-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximalt antal publicerade kunskapsbaser tillåts|2|14|49|199|199|2,999|

 Om din nivå har 15 tillåtna index, kan du publicera 14 kunskapsbaser (1 index per publicerade knowledge base). Femtonde indexet `testkb`, används för alla kunskapsbaser för redigering och testning. 

## <a name="extraction-limits"></a>Extrahering av gränser
* Maximalt antal filer som kan extraheras och maximal filstorlek: Se [QnAMaker priser](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Maximalt antal djup länkar som kan crawlas för extrahering av kunskapsbaser från vanliga frågor och svar HTML-sidor: 20

## <a name="metadata-limits"></a>Metadata-gränser
* Maxantalet metadatafält per kunskapsbas utifrån [gränserna för nivån av Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Azure Search-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal metadatafält per QnA Maker-tjänsten (över alla KB-artiklar)|1,000|100 *|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>Gränser för kunskapsbas
Övergripande gränser för innehållet i kunskapsbasen:
* Längden på svarstexten: 25,000
* Längden på frågetext: 1,000
* Textlängden metadata nyckel/värde: 100
* Tecken som stöds för Metadatanamn: Bokstäver, siffror och _  
* Tecken som stöds för värde: Alla utom: och | 
* Längden på filnamn: 200
* Filformat som stöds: ”TSV”, ”.pdf”, ”.txt”, ”.docx”, ”.xlsx”.
* Maximalt antal alternativa frågor: 100
* Maximalt antal frågor svar-par: Beror på den [Azure Search-nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) valt. Ett par med frågor och svar mappas till ett dokument på Azure Search-index. 

## <a name="create-knowledge-base-call-limits"></a>Skapa kunskapsbas anrop begränsningar:
Dessa representerar gränser för varje skapa kunskapsbas åtgärden. det vill säga att klicka på *skapa KB* eller anropa CreateKnowledgeBase API.
* Maximalt antal alternativa frågor per svar: 100
* Maximalt antal URL: er: 10
* Maximalt antal filer: 10

## <a name="update-knowledge-base-call-limits"></a>Uppdatera kunskapsbas anrop gränser
Dessa representerar gränser för varje uppdateringsåtgärd; det vill säga att klicka på *spara och träna* eller anropa UpdateKnowledgeBase API.
* Längden på varje namn på datakälla: 300
* Maximalt antal alternativa frågor läggs till eller tas bort: 100
* Maximalt antal fält med metadata läggs till eller tas bort: 10
* Maximalt antal URL: er som kan uppdateras: 5

## <a name="next-steps"></a>Nästa steg

Se när och hur du ändrar tjänstnivåer:

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): När du behöver ha flera källfiler eller större dokument i kunskapsbasen, utanför din aktuella nivån, uppgradera din prisnivå QnA Maker-tjänsten.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): När din kunskapsbas behöver hantera flera begäranden från klientappen, uppgradera din app service-prisnivå.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): När du planerar att ha många kunskapsbaser uppgradera prisnivån Azure Search-tjänsten.
