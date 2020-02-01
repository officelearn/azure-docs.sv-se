---
title: Innehålls typer-QnA Maker
description: Innehålls typer innehåller många standard strukturerade dokument som PDF, DOC och TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 3020c0623cff73e5a907f4d63f08f5866afd52dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902085"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Innehålls typer av dokument som du kan lägga till i en kunskaps bas
Innehålls typer innehåller många standard strukturerade dokument som PDF, DOC och TXT.

## <a name="file-and-url-data-types"></a>Fil-och URL-datatyper

Följande tabell sammanfattar typerna av innehåll och filformat som stöds av QnA Maker.

|Källtyp|Innehållstyp| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med hjälp av avsnitts start sida)<br>Support sidor <br> (Enkla sid artiklar, fel söknings artiklar osv.)|[Vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar om ämnes Sidan](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Support artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOKUMENT|Vanliga frågor<br> Produkt handbok,<br> Broschyrer,<br> Grön<br> Reklamblads princip,<br> Support guide,<br> Strukturerad QnA,<br> annat.|**Utan flera turn**<br>[Strukturerad QNA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Exempel på produkt manual. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel på semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel White Paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Flera turn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-förmåner (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-förmåner (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|\* Excel|Strukturerade QnA-fil<br> (inklusive RTF, HTML-stöd)|[Exempel på frågor och svar om FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturerade QnA-fil|[Exemplet chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

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

QnA Maker lägger till fil-och URL-innehåll, konverterar innehållet till QnA-uppsättningar, lagras som markdown (`.md`). Innehållet passar för en kunskaps bas där innehållet är ordnat i en strukturerad form och visas i väldefinierade avsnitt. Avsnitten kan delas upp ytterligare i underavsnitt eller delämnen. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

QnA Maker identifierar avsnitt och underavsnitt och relationer i innehållet baserat på visuella LED trådar som tecken storlek, stil, numrering, färger osv. Halv strukturerat innehåll innehåller handböcker, vanliga frågor och rikt linjer, principer, broschyrer, reklamblad och många andra typer av filer.

## <a name="faq-urls"></a>Vanliga frågor och svar-URL: er

QnA Maker kan stödja webbsidor för vanliga frågor och svar i 3 olika former: vanlig vanliga frågor och svar-sidor, vanliga frågor och svar sidor med länkar, vanliga frågor och svar sidor med en ämnen startsida.


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


### <a name="support-urls"></a>Support-URL: er

QnA Maker kan bearbeta delvis strukturerade support webb sidor, till exempel webb artiklar som beskriver hur du utför en specifik uppgift, hur du diagnostiserar och löser ett specifikt problem och vilka metoder som är bästa praxis för en specifik process. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extrahering av support artiklar är en ny funktion och är i tidiga steg. Det fungerar bäst för enkla sidor, som är väl strukturerade och inte innehåller komplexa sidhuvuden och sid fötter.

![QnA Maker stöder extrahering från halv strukturerade webb sidor där en tydlig struktur visas med hierarkiska rubriker](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF / dokumentfiler

QnA Maker kan bearbeta delvis strukturerat innehåll i en PDF- eller DOC-fil och omvandla dem till kunskapsbaser. En bra fil som kan extraheras och är en där innehåll organiseras i någon strukturerade form och representeras i väldefinierade avsnitt. Avsnitten kan ytterligare delas i underavsnitt eller underavsnitt. Extrahering fungerar bäst på dokument som har en tydlig struktur med hierarkisk rubriker.

QnA Maker identifierar avsnitt och underavsnitt och-relationer i filen baserat på visuella LED trådar som tecken storlek, stil, numrering, färger osv. Halv strukturerade PDF-eller DOC-filer kan vara handböcker, vanliga frågor, rikt linjer, principer, broschyrer, reklamblad och många andra typer av filer. Nedan visas några exempel typer av dessa filer.

### <a name="product-manuals"></a>Produkthandböcker

En manuell är vanligtvis anvisningar som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell, extraheras rubriker och underrubriker som frågor och efterföljande innehållet som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan visas ett exempel på en manuell med en indexsida och hierarkisk innehåll

 ![Produkten manuell exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extrahering fungerar bäst på handböcker som har en tabell med innehåll och/eller en indexsida och en tydlig struktur med hierarkisk rubriker.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, riktlinjer, rapporter och andra filer

Många andra typer av dokument kan också bearbetas för att generera QA-par, förutsatt att de har en tydlig struktur och layout. Dessa omfattar: broschyrer, rikt linjer, rapporter, fakta blad, vetenskapliga handlingar, principer, böcker osv. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

| Fråga  | Svar  | Metadata (1 nyckel: 1 värde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Alla övriga kolumner i källfilen ignoreras.

### <a name="example-of-structured-excel-file"></a>Exempel på strukturerad Excel-fil

Nedan visas ett exempel på en strukturerade QnA *.xls* filen med HTML-innehåll:

 ![Strukturerade frågor och svar om excel-exempel för en kunskapsbas](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exempel på alternativa frågor för ett enskilt svar i Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med flera alternativa frågor för ett enda svar:

 ![Exempel på alternativa frågor för ett enskilt svar i Excel-fil](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

När filen har importer ATS visas fråga-och-svar-paret i kunskaps basen enligt nedan:

 ![Skärm bild av alternativa frågor för enskilda svar som importeras till kunskaps basen](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>Formatering överväganden

När du har importerat en fil eller URL, QnA Maker konvertera och lagra innehållet i [markdown-formatet](https://en.wikipedia.org/wiki/Markdown). Konverterings processen lägger till nya rader i texten, t. ex. `\n\n`. En kunskap om markdown-formatet hjälper dig att förstå det konverterade innehållet och hantera ditt kunskaps bas innehåll.

Om du lägger till eller redigerar innehållet direkt i din kunskaps bas använder du **markdown-formatering** för att skapa RTF-innehåll eller ändra markdown format innehåll som redan finns i svaret. QnA Maker stöder mycket av markdown-formatet för att få RTF-funktioner till ditt innehåll. Men klient programmet, till exempel en chatt-robot, kanske inte stöder samma uppsättning markdown-format. Det är viktigt att testa klient programmets visning av svar.

Läs mer i [referens dokumentationen för QNA Maker markdown](../reference-markdown-format.md).

## <a name="testing-your-markdown"></a>Testa din markdown

Använd **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -självstudien för att validera din markdown. I självstudien finns **en funktion för** att snabbt kopiera och klistra in.

## <a name="next-steps"></a>Nästa steg

* Förstå hur du utformar och hanterar [QNA-uppsättningar (frågor och svar)](question-answer-set.md)