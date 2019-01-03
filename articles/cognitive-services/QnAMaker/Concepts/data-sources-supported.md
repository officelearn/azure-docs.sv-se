---
title: Datakällor som stöds – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extraherar automatiskt fråga – svar par från halvstrukturerade innehåll, till exempel vanliga frågor och svar, produkthandböcker, riktlinjer, stöd för dokument och principer som lagras som webbsidor, PDF-filer eller filer som Microsoft Word-dokument. Innehållet kan också läggas till i knowledge base från strukturerade innehållsfiler för frågor och svar.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/27/2018
ms.author: tulasim
ms.openlocfilehash: f732391cc73f0a7cb417409cd6f6f2b3c54f93d6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994138"
---
# <a name="data-sources-for-qna-maker-content"></a>Datakällor för QnA Maker-innehåll

QnA Maker extraherar automatiskt fråga – svar par från halvstrukturerade innehåll, till exempel vanliga frågor och svar, produkthandböcker, riktlinjer, stöd för dokument och principer som lagras som webbsidor, PDF-filer eller filer som Microsoft Word-dokument. Innehållet kan också läggas till i knowledge base från strukturerade innehållsfiler för frågor och svar. 

Följande tabell sammanfattar typerna av innehåll och filformat som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Fast avsnitt eller med en ämnen startsida)<br>Stöd för sidor <br> (En sida instruktionsartiklar, felsökningsartiklar osv.)|[Vanlig vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/software-download/faq),<br> [Vanliga frågor och svar med ämnen startsida](https://support.microsoft.com/products/windows?os=windows-10)<br>[Support-artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOKUMENT|Vanliga frågor och svar,<br> Produkten manuellt<br> Broschyrer,<br> Dokumentet,<br> Flygblad princip<br> Supportguide,<br> Frågor och svar om strukturerade,<br> Osv.|[Strukturerad QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel på produkten Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel på halvstrukturerade structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel på vit paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Strukturerade QnA-fil<br> (inklusive RTF, HTML stöd)|[Exempel på frågor och svar om FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Strukturerade QnA-fil|[Exemplet chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Data-källplatser

Endast offentliga URL: er är giltiga för alla datakällor. Inte in datakällor som kräver autentisering. Du kan ladda ned filen från den autentiserade platsen och sedan använda alternativet ladda upp filer för att extrahera frågor och svar.

## <a name="faq-urls"></a>Vanliga frågor och svar-URL: er

QnA Maker kan stödja webbsidor för vanliga frågor och svar i 3 olika former: Vanlig vanliga frågor och svar-sidor, vanliga frågor och svar sidor med länkar, vanliga frågor och svar sidor med en ämnen startsida.

### <a name="plain-faq-pages"></a>Vanlig vanliga frågor och svar-sidor

Det här är den vanligaste typen av FAQ-sida, som svar direkt efter frågor på samma sida. 

Nedan visas ett exempel på en vanlig FAQ-sida:

![Vanlig vanliga frågor och svar-sida-exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Vanliga frågor och svar sidor med länkar 

I den här typen av FAQ-sida kan frågor räknas samman tillsammans och har kopplats till svar som finns i olika avsnitt i samma sida eller i olika sidor.

Nedan visas ett exempel på en FAQ-sida med länkar i avsnitten som finns på samma sida:

 ![Avsnittet vanliga frågor och svar länk sidan exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Vanliga frågor och svar sidor med en ämnen startsida

Den här typen av vanliga frågor och svar har en startsida med hjälp av där varje avsnitt är en länk till dess relevanta kunskapsbaser på en annan sida. QnA Maker crawlar här, alla länkade sidor för att extrahera motsvarande frågor och svar.

Nedan visas ett exempel på en FAQ-sida där en ämnen startsida har länkar till avsnitt i vanliga frågor och svar på olika sidor. 

 ![Vanliga frågor och svar för djuplänk sidan exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Stöd för URL: er

QnA Maker kan bearbeta halvstrukturerade stöd för webbsidor, t.ex web-artiklar som skulle beskriver hur du utför en viss uppgift att diagnostisera och lösa ett givet problem och vilka är de rekommenderade säkerhetsmetoderna för en viss process. Extrahering fungerar bäst för innehåll som har en tydlig struktur med hierarkisk rubriker.

> [!NOTE]
> Extrahering för artiklar är en ny funktion och är i början. Det fungerar bäst för enkla sidor, som även är strukturerade och inte innehåller komplexa sidhuvuden och sidfötter.

![QnA Maker stöder extrahering från halvstrukturerade webbsidor där en tydlig struktur visas hierarkisk rubriker](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / dokumentfiler

QnA Maker kan bearbeta delvis strukturerat innehåll i en PDF- eller DOC-fil och omvandla dem till kunskapsbaser. En bra fil som kan extraheras och är en där innehåll organiseras i någon strukturerade form och representeras i väldefinierade avsnitt. Avsnitten kan ytterligare delas i underavsnitt eller underavsnitt. Extrahering fungerar bäst på dokument som har en tydlig struktur med hierarkisk rubriker.

QnA Maker identifierar avsnitt och underavsnitt och relationer i filen baserat på visuella ledtrådar som teckenstorlek, stil numrering, färger, osv. Halvstrukturerade PDF- eller DOC-filer kan vara handböcker, vanliga frågor och svar, riktlinjer, principer, broschyrer, flygblad och många andra typer av filer. Nedan visas några exempel typer av dessa filer.

### <a name="product-manuals"></a>Produkthandböcker

En manuell är vanligtvis anvisningar som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell, extraheras rubriker och underrubriker som frågor och efterföljande innehållet som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan visas ett exempel på en manuell med en indexsida och hierarkisk innehåll

 ![Produkten manuell exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extrahering fungerar bäst på handböcker som har en tabell med innehåll och/eller en indexsida och en tydlig struktur med hierarkisk rubriker.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, riktlinjer, rapporter och andra filer

Många andra typer av dokument kan också bearbetas för att generera QA-par, förutsatt att de har en tydlig struktur och layout. Exempel på dessa är: Broschyrer, riktlinjer, rapporter, white paper-faktablad, vetenskapliga undersökningar, principer, böcker, osv. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Nedan visas ett exempel på ett halvstrukturerade dokument utan ett index:

 ![Azure Blob storage halvstrukturerade dokument](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturerat QnA-dokument

Formatet för strukturerade fråga – svar i dokumentfiler, är i form av alternerande frågor och svar per rad, en fråga per rad följt av dess svar i följande rad, enligt nedan: 

```text
Question1

Answer1

Question2

Answer2
```

Nedan visas ett exempel på ett strukturerade QnA word-dokument:

 ![Strukturerade frågor och svar om dokumentet exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturerade *TXT*, *TSV* och *XLS* filer

Kunskapsbaser i form av strukturerade *.txt*, *.tsv* eller *.xls* filer kan också överföras till QnA Maker att skapa eller utöka en kunskapsbas.  Dessa kan antingen vara oformaterad text eller kan ha innehåll i RTF- eller HTML. 

| Fråga  | Svar  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Alla övriga kolumner i källfilen ignoreras.

Nedan visas ett exempel på en strukturerade QnA *.xls* filen med HTML-innehåll:

 ![Strukturerade frågor och svar om excel-exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Format för strukturerade data via import

Importera en kunskapsbas ersätter innehållet i den befintliga kunskapsbasen. Importera kräver en strukturerade TSV-fil som innehåller information om datakällan. Den här informationen hjälper QnA Maker gruppera frågor svar-par och attributet dem till en viss datakälla.

| Fråga  | Svar  | Källa| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redigering|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redigeringsmässigt lägga till i kunskapsbas

Om du inte har redan befintliga innehåll för att fylla i knowledge base, kan du lägga till kunskapsbaser redigeringsmässigt i QnA Maker Knowledge base. Lär dig hur du uppdaterar din kunskapsbas [här](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Formatering överväganden

När en fil eller URL: en har den konverteras till Markdown och lagras i formatet. Om processen inte korrekt konverterar länkar i dina filer och URL: er, bör du redigera frågor och svar på den **redigera** sidan. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ställ in QnA Maker-tjänsten](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Se också 

[Översikt över QnA Maker](../Overview/overview.md)
