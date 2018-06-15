---
title: Frågor och svar om Maker gränser - kognitiva Azure-tjänster | Microsoft Docs
description: Frågor och svar om Maker gränser
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 4d2bafad08ffab76743cb802733a5d2f01a97f06
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "35356030"
---
# <a name="qna-maker-limits"></a>Frågor och svar om Maker gränser
Omfattande lista över gränserna mellan frågor och svar om Maker.

## <a name="knowledge-bases"></a>Knowledge baser

* Maximalt antal knowledge baser baserat på [gränser för Azure Search-nivå](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Azure Search-nivå** | **Ledigt** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximalt antal publicerade knowledge baser tillåten (maximalt antal index--1 (reserverad för test)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Extrahering-gränser
* Maximalt antal filer som kan extraheras och maximal filstorlek: se [QnAMaker priser](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* Maximalt antal djup länkar som kan crawlas för extrahering av QnAs från vanliga frågor och svar HTML-sidor: 20

## <a name="metadata-limits"></a>Metadata-gränser
* Antalet metadatafält per knowledge base-baserat på [gränser för Azure Search-nivå](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Azure Search-nivå** | **Ledigt** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal metadatafält per frågor och svar om Maker tjänst (över alla KBs)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Kunskapsbasen gränser
Övergripande gränser på innehållet i kunskapsbasen:
* Längden på svarstexten: 250000
* Längden på frågetexten: 1000
* Textlängden metadata nyckel/värde-: 100
* Tecken som stöds för metadata name: bokstäver, siffror och _  
* Tecken som stöds för metadatavärde: allt utom: och | 
* Längden på filnamn: 200
* Filformat som stöds: ”TSV”, ”PDF”, ”.txt”, ”.docx”, ”.xlsx”.
* Maximalt antal alternativa frågor: 100
* Maximalt antal frågor svar par: beror på den [Azure Search nivå](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) valt 

## <a name="create-knowledge-base-call-limits"></a>Skapa Knowledge base anropet begränsningar:
Dessa representerar gränser för varje skapa knowledge base-åtgärden. Det innebär att klicka på *skapa KB* eller anropar API: T för CreateKnowledgeBase.
* Maximalt antal alternativa frågor per svar: 100
* Maximalt antal URL: er: 10
* Maximalt antal filer: 10

## <a name="update-knowledge-base-call-limits"></a>Uppdatera Knowledge base anropet gränser
Dessa motsvarar gränser för varje uppdateringsåtgärd; Det innebär att klicka på *spara och träna* eller anropar API: T för UpdateKnowledgeBase.
* Längden på varje namn på datakälla: 300
* Maximalt antal frågor alternativa frågor läggs till eller tas bort: 100
* Antalet metadatafält läggs till eller tas bort: 10
* Maximalt antal URL: er som kan uppdateras: 5
