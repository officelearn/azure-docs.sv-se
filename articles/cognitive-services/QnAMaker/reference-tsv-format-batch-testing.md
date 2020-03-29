---
title: Batch test TSV-format - QnA Maker
titleSuffix: Azure Cognitive Services
description: Förstå TSV-formatet för batchtestning
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507829"
---
# <a name="batch-testing-tsv-format"></a>TSV-format för batchtestning

Batch testning är tillgänglig från [källkoden](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) eller som en [nedladdningsbar körbar zippade](https://aka.ms/qna_btzip). Formatet för kommandot för att köra batchtestet är:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Förväntat värde|
|--|--|
|1|namn på tsv-fil formaterad med [TSV-inmatningsfält](#tsv-input-fields)|
|2|URI för slutpunkt, med YOUR-HOST från sidan Publicera i QnA Maker-portalen.|
|3|SLUTPUNKT-NYCKEL, finns på Publicera sidan i QnA Maker portalen.|
|4|namnet på tsv-filen som skapats av batchtest för resultat.|

Använd följande information för att förstå och implementera TSV-formatet för batchtestning. 

## <a name="tsv-input-fields"></a>TSV-inmatningsfält

|TSV-inmatningsfilfält|Anteckningar|
|--|--|
|KBID|Kb-ID:n finns på sidan Publicera.|
|Fråga|Frågan en användare skulle ange.|
|Metadatataggar|valfri|
|Parametern Översta|valfri| 
|Förväntat svars-ID|valfri|

![Indataformat för TSV-fil för batchtestning.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV-utdatafält 

|TSV-utdatafilparametrar|Anteckningar|
|--|--|
|KBID|Kb-ID:n finns på sidan Publicera.|
|Fråga|Frågan som anges från indatafilen.|
|Svar|Bästa svar från din kunskapsbas.|
|Svars-ID|Svars-ID|
|Poäng|Förutsägelse poäng för svar. |
|Metadatataggar|associeras med returnerat svar|
|Förväntat svars-ID|valfritt (endast när förväntat svars-ID anges)|
|Dom etikett|valfria kan värden vara: korrekta eller felaktiga (endast när förväntat svar ges)|
