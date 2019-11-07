---
title: Data källor som stöds – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker extraherar automatiskt svars par som lagras som webb sidor, PDF-filer eller MS Word doc-filer eller strukturerade QnA-innehållsfiler.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: a1fcc1bec5db3ad64dc045cf5e1a449fce808132
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721220"
---
# <a name="data-sources-for-qna-maker-content"></a>Data källor för QnA Maker innehåll

QnA Maker extraherar automatiskt svars par från det delvis strukturerade innehållet, till exempel vanliga frågor, produkt handböcker, rikt linjer, support dokument och principer som lagras som webb sidor, PDF-filer eller MS Word doc-filer. Innehåll kan också läggas till i kunskaps basen från strukturerade QnA-innehållsfiler. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Fil-och URL-datatyper

Tabellen nedan sammanfattar de typer av innehåll och fil format som stöds av QnA Maker.

|Källtyp|Innehålls typ| Exempel|
|--|--|--|
|URL|Vanliga frågor och svar<br> (Platt, med avsnitt eller med hjälp av avsnitts start sida)<br>Support sidor <br> (Enkla sid artiklar, fel söknings artiklar osv.)|[Vanliga frågor och svar](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Vanliga frågor och svar med länkar](https://www.microsoft.com/en-us/software-download/faq),<br> [Vanliga frågor och svar om ämnes Sidan](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Support artikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOK|Vanliga frågor<br> Produkt handbok,<br> broschyrer,<br> Grön<br> Reklamblads princip,<br> Support guide,<br> Strukturerad QnA,<br> annat.|[Strukturerad QNA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel på produkt manual. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Exempel på semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Exempel White Paper. pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Exempel på multi-turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Strukturerad QnA-fil<br> (inklusive RTF, HTML-stöd)|[Exempel på QnA FAQ. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturerad QnA-fil|[Exempel på Chit-Chat. tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Importera och exportera kunskaps bas

**TSV-och XLS-filer**, från exporterade kunskaps baser, kan bara användas genom att importera filerna från sidan **Inställningar** på QNA Maker portalen. De kan inte användas som data källor när du skapar kunskaps basen eller från **+ Lägg till fil** eller **+ Lägg till URL** -funktionen på sidan **Inställningar** . 

## <a name="data-source-locations"></a>Platser för data källor

Platser för data källor är **offentliga URL: er eller filer**som inte kräver autentisering. 

Om du behöver autentisering för din data källa kan du använda följande metoder för att hämta dessa data till QnA Maker:

* [Ladda ned filen manuellt](#download-file-from-authenticated-data-source-location) och importera till QNA Maker
* Importera fil för autentiserad [SharePoint-plats](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Hämta filen från den autentiserade data käll platsen

Om du har en autentiserad fil (inte på en autentiserad SharePoint-plats) eller URL är ett alternativt alternativ att ladda ned filen från den autentiserade platsen till din lokala dator. Lägg sedan till filen från den lokala datorn i kunskaps basen.

### <a name="import-file-from-authenticated-sharepoint"></a>Importera filen från autentiserad SharePoint 

[Platser för SharePoint-datakällor](../How-to/add-sharepoint-datasources.md) får tillhandahålla autentiserade **filer**. SharePoint-resurser måste vara filer, inte webb sidor. Om URL: en slutar med ett webb tillägg, till exempel **. ASPX**importeras inte till QNA Maker från SharePoint.


## <a name="faq-urls"></a>URL: er för vanliga frågor

QnA Maker kan stödja vanliga frågor och svar webb sidor på tre olika sätt: vanliga frågor och svar sidor med länkar, vanliga frågor och svar sidor med avsnitts start sida.

### <a name="plain-faq-pages"></a>Vanliga FAQ-sidor

Detta är den vanligaste typen av vanliga frågor och svar, där svaren omedelbart följer frågorna på samma sida. 

Nedan visas ett exempel på en sida med vanliga frågor och svar:

![Exempel sidan Vanliga frågor och svar om en kunskaps bas](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Vanliga frågor och svar-sidor med länkar 

På den här typen av vanliga frågor och svar sammanställs frågor tillsammans och är länkade till svar som finns i olika delar av samma sida eller på olika sidor.

Nedan visas ett exempel på en sida med vanliga frågor och svar med länkar i avsnitt som finns på samma sida:

 ![Exempel på avsnittet Vanliga frågor och svar om avsnitts länk för en kunskaps bas](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Vanliga frågor och svar med en sida med ämnes sidor

Den här typen av vanliga frågor och svar har en start sida med ämnen där varje ämne är en länk till dess relevanta kring på en annan sida. Här kan QnA Maker crawla alla länkade sidor för att extrahera motsvarande frågor & svar.

Nedan visas ett exempel på en sida med vanliga frågor och svar där ett avsnitts start sida har länkar till vanliga frågor och svar på olika sidor. 

 ![Exempel på vanliga frågor om djupgående Länkar för en kunskaps bas](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Support-URL: er

QnA Maker kan bearbeta delvis strukturerade support webb sidor, till exempel webb artiklar som beskriver hur du utför en specifik uppgift, hur du diagnostiserar och löser ett specifikt problem och vilka metoder som är bästa praxis för en specifik process. Extraktion fungerar bäst på innehåll som har en tydlig struktur med hierarkiska rubriker.

> [!NOTE]
> Extrahering av support artiklar är en ny funktion och är i tidiga steg. Det fungerar bäst för enkla sidor, som är väl strukturerade och inte innehåller komplexa sidhuvuden och sid fötter.

![QnA Maker stöder extrahering från halv strukturerade webb sidor där en tydlig struktur visas med hierarkiska rubriker](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC-filer

QnA Maker kan bearbeta ett delvis strukturerat innehåll i en PDF-eller DOC-fil och konvertera det till kring. En bra fil som kan extraheras är en plats där innehållet organiseras i en strukturerad form och visas i väldefinierade avsnitt. Avsnitten kan delas upp ytterligare i underavsnitt eller delämnen. Extraktion fungerar bäst på dokument som har en tydlig struktur med hierarkiska rubriker.

QnA Maker identifierar avsnitt och underavsnitt och-relationer i filen baserat på visuella LED trådar som tecken storlek, stil, numrering, färger osv. Halv strukturerade PDF-eller DOC-filer kan vara handböcker, vanliga frågor, rikt linjer, principer, broschyrer, reklamblad och många andra typer av filer. Nedan visas några exempel typer av de här filerna.

### <a name="product-manuals"></a>Produkt handböcker

En manuell är vanligt vis ett hjälp material som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker bearbetar en manuell extraheras rubriker och under rubriker som frågor och efterföljande innehåll som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan visas ett exempel på en manuell med en index sida och hierarkiskt innehåll

 ![Produkt hand bok exempel för en kunskaps bas](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extraktion fungerar bäst på handböcker som har en innehålls förteckning och/eller en index sida och en tydlig struktur med hierarkiska rubriker.

### <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, rikt linjer, dokument och andra filer

Många andra typer av dokument kan också bearbetas för att generera frågor och svar, förutsatt att de har en tydlig struktur och layout. Dessa omfattar: broschyrer, rikt linjer, rapporter, fakta blad, vetenskapliga handlingar, principer, böcker osv. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Nedan visas ett exempel på ett delvis strukturerat dokument utan ett index:

 ![Halv strukturerat dokument i Azure Blob Storage](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturerat QnA-dokument

Formatet för strukturerad fråga – svar i DOC-filer, är i form av alternerande frågor och svar per rad, en fråga per rad följt av dess svar på följande rad, enligt nedan: 

```text
Question1

Answer1

Question2

Answer2
```

Nedan visas ett exempel på ett strukturerat QnA Word-dokument:

 ![Exempel på strukturerade QnA-dokument för en kunskaps bas](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturerade *txt*-, *TSV* -och *xls* -filer

Kring i form av Structured *. txt*-, *. tsv* -eller *. xls* -filer kan också överföras till QNA Maker för att skapa eller utöka en kunskaps bas.  Dessa kan antingen vara oformaterad text eller ha innehåll i RTF eller HTML. 

| Fråga  | Svar  | Metadata (1 nyckel: 1 värde) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Eventuella ytterligare kolumner i käll filen ignoreras.

### <a name="example-of-structured-excel-file"></a>Exempel på strukturerad Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med HTML-innehåll:

 ![Strukturerad QnA Excel-exempel för en kunskaps bas](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exempel på alternativa frågor för ett enskilt svar i Excel-fil

Nedan visas ett exempel på en strukturerad QnA *. xls* -fil med flera alternativa frågor för ett enda svar:

 ![Exempel på alternativa frågor för ett enskilt svar i Excel-fil](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

När filen har importer ATS visas fråga-och-svar-paret i kunskaps basen enligt nedan:

 ![Skärm bild av alternativa frågor för enskilda svar som importeras till kunskaps basen](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturerat data format via import

Om du importerar en kunskaps bas ersätts innehållet i den befintliga kunskaps basen. Importen kräver en strukturerad. TSV-fil som innehåller information om data källan. Den här informationen hjälper QnA Maker att gruppera frågans svars par och attributerar dem till en viss data källa.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redaktionellt|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Lägg till i kunskaps basen i redaktionellt

Om du inte har befintligt innehåll för att fylla i kunskaps basen kan du lägga till kring redaktionellt i QnA Maker Knowledge Base. Lär dig hur du uppdaterar din kunskaps bas [här](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Överväganden vid formatering

När du har importerat en fil eller URL, QnA Maker konvertera och lagra innehållet i [markdown-formatet](https://en.wikipedia.org/wiki/Markdown). Konverterings processen lägger till nya rader i texten, t. ex. `\n\n`. En kunskap om markdown-formatet hjälper dig att förstå det konverterade innehållet och hantera ditt kunskaps bas innehåll. 

Om du lägger till eller redigerar innehållet direkt i din kunskaps bas använder du **markdown-formatering** för att skapa RTF-innehåll eller ändra markdown format innehåll som redan finns i svaret. QnA Maker stöder mycket av markdown-formatet för att få RTF-funktioner till ditt innehåll. Men klient programmet, till exempel en chatt-robot, kanske inte stöder samma uppsättning markdown-format. Det är viktigt att testa klient programmets visning av svar. 

Följande är en lista med markdown-format som du kan använda i QnA Maker: 

|Syfte|Format|Exempel markdown|Rendering<br>som det visas i Chat-roboten|
|--|--|--|--|
En ny rad mellan 2 meningar.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formatera en ny rad mellan två meningar](../media/qnamaker-concepts-datasources/format-newline.png)|
|Sidhuvuden från H1 till H6, antalet `#` anger vilket sidhuvud. 1 `#` är H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formatera med markdown-rubriker](../media/qnamaker-concepts-datasources/format-headers.png)<br>![formatera med markdown-huvuden H1 till H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursiv stil |`*text*`|`How do I create a bot with *QnA Maker*?`|![formatera med kursiv stil](../media/qnamaker-concepts-datasources/format-italics.png)|
|Sträng (fet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formatera med stark markering för fetstil](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL för länk|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![format för URL (hyperlänk)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* URL för offentlig bild|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![format för offentlig bild-URL ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Genomstruken|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![format för genomstruken](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Fet och kursiv stil|`***text***`|`How can I create a ***QnA Maker*** bot?`|![format för fet och kursiv stil](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Fet URL för länk|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![format för fet URL](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL för kursiv stil för länk|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![format för kursiv stil-URL](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape-markdown symboler|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![format för kursiv stil-URL](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Sorterad lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>I föregående exempel används automatisk numrering som är inbyggd i markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>I föregående exempel används explicit numrering.|![format för ordnad lista](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Osorterad lista|`\n * item1 \n * item2`<br>eller<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![format för osorterad lista](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Kapslade listor|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Du kan kapsla in sorterade och osorterade listor tillsammans. Fliken `\t`anger den underordnade elementets indrags nivå.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![format för kapslad osorterad lista](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![format för kapslad ordnad lista](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker bearbetar inte bilden på något sätt. Det är klient programmets roll för att återge avbildningen. 

Om du vill lägga till innehåll med hjälp av uppdatera/Ersätt kunskaps-API: er och innehållet/filen innehåller HTML-taggar kan du bevara HTML-koden i filen genom att se till att öppning och stängning av taggarna konverteras i det kodade formatet.

| Bevara HTML  | Representation i API-begäran  | Representation i KB |
|-----------|---------|-------------------------|
| Ja | \&lt; br\&gt; | &lt;br&gt; |
| Ja | \&lt; H3\&gt; header\&lt;/H3\&gt; | &lt;H3&gt;-huvud&lt;/H3&gt; |

Dessutom konverteras CR LF (\r\n) till \n i KB. LF (\n) behålls i befintligt skick. Om du vill kringgå escape-sekvenser som a \t eller \n kan du använda omvänt snedstreck, till exempel: "\\\\r\\\\n" och "\\\\t"

## <a name="editing-your-knowledge-base-locally"></a>Redigera din kunskaps bas lokalt

När en kunskaps bas har skapats rekommenderar vi att du gör ändringar i kunskaps bas texten i [QNA Maker Portal](https://qnamaker.ai), i stället för att exportera och importera via lokala filer. Det kan dock finnas tillfällen då du behöver redigera en kunskaps bas lokalt. 

Exportera kunskaps basen från sidan **Inställningar** och redigera sedan kunskaps basen med Microsoft Excel. Om du väljer att använda ett annat program för att redigera den exporterade TSV-filen kan det leda till syntaxfel på grund av att det inte är fullständigt TSV-kompatibelt. Microsoft Excel-TSV-filer innehåller vanligt vis inte formateringsfel. 

När du är färdig med redigeringarna importerar du om TSV-filen från sidan **Inställningar** . Detta kommer att ersätta den aktuella kunskaps basen med den importerade kunskaps basen. 

## <a name="testing-your-markdown"></a>Testa din markdown

Använd **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -självstudien för att validera din markdown. I självstudien finns **en funktion för** att snabbt kopiera och klistra in. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>Versions kontroll för data i din kunskaps bas

Versions kontroll för data tillhandahålls via [import/export-funktionen](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) på sidan **Inställningar** . 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera en QnA Maker-tjänst](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Se även 

[Översikt över QnA Maker](../Overview/overview.md)
