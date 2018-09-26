---
title: Begränsningar och gränser – QnA Maker
titleSuffix: Azure Cognitive Services
description: Omfattande lista över gränserna över QnA Maker.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: efdcf57fb0f175be51e6b9f8e72e4a02fcbeb5f3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162329"
---
# <a name="qna-maker-limits"></a>QnA Maker gränser
Omfattande lista över gränserna över QnA Maker.

## <a name="knowledge-bases"></a>Kunskapsbaser

* Maximalt antal kunskapsbaser utifrån [gränserna för nivån av Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Azure Search-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Publicerade kunskapsbaser maxantalet (högsta antal index--1 (reserverade för test)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Extrahering av gränser
* Maximalt antal filer som kan extraheras och maximal filstorlek: se [QnAMaker priser](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* Maximalt antal djup-länkar som kan crawlas för extrahering av kunskapsbaser från vanliga frågor och svar HTML-sidor: 20

## <a name="metadata-limits"></a>Metadata-gränser
* Maxantalet metadatafält per kunskapsbas utifrån [gränserna för nivån av Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Azure Search-nivå** | **Kostnadsfri** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal metadatafält per QnA Maker-tjänsten (över alla KB-artiklar)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Gränser för kunskapsbas
Övergripande gränser för innehållet i kunskapsbasen:
* Längden på svarstexten: 250000
* Längden på frågetexten: 1000
* Textlängden metadata nyckel/värde: 100
* Tecken som stöds för Metadatanamn: alfabet, siffror och _  
* Tecken som stöds för metadatavärde: allt utom: och | 
* Längden på filnamnet: 200
* Filformat som stöds: ”TSV”, ”.pdf”, ”.txt”, ”.docx”, ”.xlsx”.
* Maximalt antal alternativa frågor: 100
* Maximalt antal frågor svar-par: beror på den [Azure Search-nivå](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) valt 

## <a name="create-knowledge-base-call-limits"></a>Skapa kunskapsbas anrop begränsningar:
Dessa representerar gränser för varje skapa kunskapsbas åtgärden. det vill säga att klicka på *skapa KB* eller anropa CreateKnowledgeBase API.
* Maximalt antal alternativa frågor per svar: 100
* Maximalt antal URL: er: 10
* Maximalt antal filer: 10

## <a name="update-knowledge-base-call-limits"></a>Uppdatera kunskapsbas anrop gränser
Dessa representerar gränser för varje uppdateringsåtgärd; det vill säga att klicka på *spara och träna* eller anropa UpdateKnowledgeBase API.
* Längden på varje källnamn: 300
* Maximalt antal alternativa frågor läggs till eller tas bort: 100
* Maximalt antal metadatafält läggs till eller tas bort: 10
* Maximalt antal URL: er som kan uppdateras: 5
