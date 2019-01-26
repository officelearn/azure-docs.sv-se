---
title: Begränsningar och gränser – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker har meta-gränser för delar av knowledge base och tjänsten. Det är viktigt att hålla din kunskapsbas inom de här gränserna för att testa och publicera.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 51e494101b13b3020f79c9acfc4d853cee995d7b
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911430"
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
* Maximalt antal frågor svar-par: Beror på den [Azure Search-nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) valt 

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

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): när du behöver ha fler frågor och svar i kunskapsbasen, utanför din aktuella nivån uppgradera din prisnivå QnA Maker-tjänsten.
* [Sök](how-to/upgrade-qnamaker-service.md#upgrade-app-service) – när din kunskapsbas behöver hantera flera begäranden från din klientapp måste du uppgradera din app service-prisnivå.
* [Apptjänst](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): när du planerar att ha många kunskapsbaser uppgradera prisnivån Azure Search-tjänsten.