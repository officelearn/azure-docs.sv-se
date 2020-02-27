---
title: Innehålls typer-QnA Maker
description: Innehålls typer innehåller många standard strukturerade dokument som PDF, DOC och TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650205"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Innehålls typer av dokument som du kan lägga till i en kunskaps bas
Innehålls typer innehåller många standard strukturerade dokument som PDF, DOC och TXT.

## <a name="file-and-url-data-types"></a>Fil-och URL-datatyper

Följande tabell sammanfattar typerna av innehåll och filformat som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med hjälp av avsnitts start sida)<br>Support sidor <br> (Enkla sid artiklar, fel söknings artiklar osv.)|[Vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar om ämnes Sidan](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Support artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOKUMENT|Vanliga frågor<br> Produkt handbok,<br> Broschyrer,<br> Grön<br> Reklamblads princip,<br> Support guide,<br> Strukturerad QnA,<br> annat.|**Utan flera turn**<br>[Strukturerad QNA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exempel på produkt manual. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel på semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel White Paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Flera turn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-förmåner (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-förmåner (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|\* Excel|Strukturerade QnA-fil<br> (inklusive RTF, HTML-stöd)|[Exempel på QnA FAQ. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturerade QnA-fil|[Exempel på Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Om du behöver autentisering för din data källa kan du använda följande metoder för att hämta innehållet till QnA Maker:

* Ladda ned filen manuellt och importera till QnA Maker
* Lägg till fil från autentiserad [SharePoint-plats](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-innehåll

Två typer av dokument kan importeras via **URL** i QNA Maker:

* Vanliga frågor och svar-URL: er
* Support-URL: er

Varje typ indikerar ett förväntat format.

## <a name="file-based-content"></a>Filbaserat innehåll

Du kan lägga till filer i en kunskaps bas från en offentlig källa, eller det lokala fil systemet, i [QNA Maker portalen](https://www.qnamaker.ai).

## <a name="content-format-guidelines"></a>Rikt linjer för innehålls format

Läs mer om [format rikt linjerna](../reference-document-format-guidelines.md) för de olika filerna.

## <a name="next-steps"></a>Nästa steg

Förstå vilken information som lagras i en [fråga och ett svar (QNA)](question-answer-set.md).