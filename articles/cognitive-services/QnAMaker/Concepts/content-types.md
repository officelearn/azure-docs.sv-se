---
title: Innehållstyper - QnA Maker
description: Innehållstyper innehåller många vanliga strukturerade dokument som PDF, DOC och TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650205"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Innehållstyper av dokument som du kan lägga till i en kunskapsbas
Innehållstyper innehåller många vanliga strukturerade dokument som PDF, DOC och TXT.

## <a name="file-and-url-data-types"></a>Datatyper för fil och URL

I tabellen nedan sammanfattas de typer av innehåll och filformat som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med ett ämne hemsida)<br>Supportsidor <br> (Enkelsidiga artiklar, felsökningsartiklar etc.)|[Vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar med ämnen hemsida](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Artikeln Stöd](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Faqs<br> Produkthandbok,<br> Broschyrer<br> Papper<br> Flyer-policy,<br> Supportguide,<br> Strukturerad QnA,<br> osv.|**Utan multi-turn**<br>[Strukturerad QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exempel på produkthandbok.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel på semistrukturerad.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel på vitt papper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Multi-turn:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Fördelar (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Fördelar (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Strukturerad QnA-fil<br> (inklusive RTF, HTML-stöd)|[Exempel på vanliga frågor och svar på QnA.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Strukturerad QnA-fil|[Exempel på chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Om du behöver autentisering för datakällan bör du tänka på följande metoder för att få in innehållet i QnA Maker:

* Hämta filen manuellt och importera till QnA Maker
* Lägga till fil från autentiserat [Sharepoint-läge](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-innehåll

Två typer av dokument kan importeras via **URL** i QnA Maker:

* Vanliga frågor och svar webbadresser
* Stöd webbadresser

Varje typ anger ett förväntat format.

## <a name="file-based-content"></a>Filbaserat innehåll

Du kan lägga till filer i en kunskapsbas från en offentlig källa, eller ditt lokala filsystem, i [QnA Maker-portalen](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Riktlinjer för innehållsformat

Läs mer om [formatriktlinjerna](../reference-document-format-guidelines.md) för de olika filerna.

## <a name="next-steps"></a>Nästa steg

Förstå vilken information som lagras i en [fråga och svar (QnA) uppsättning](question-answer-set.md).