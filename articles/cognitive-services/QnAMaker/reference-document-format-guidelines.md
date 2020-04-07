---
title: Riktlinjer för importdokumentformat - QnA Maker
description: Förstå hur typerna av webbadresser används för att importera och skapa QnA-uppsättningar.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 1e006520ce5c61f2da3f425af2332b0d41cc3881
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754902"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Formatera riktlinjer för importerade dokument och webbadresser

Läs igenom de här formateringsriktlinjerna för att få bästa resultat för ditt innehåll.

## <a name="formatting-considerations"></a>Formateringsöverväganden

När du har importerat en fil eller URL konverteras och lagras innehållet i [markeringsformatet.](https://en.wikipedia.org/wiki/Markdown) Konverteringsprocessen lägger till nya rader `\n\n`i texten, till exempel . En kunskap om markdown-formatet hjälper dig att förstå det konverterade innehållet och hantera ditt kunskapsbasinnehåll.

Om du lägger till eller redigerar innehållet direkt i kunskapsbasen använder du **markeringsformatering** för att skapa RTF-innehåll eller ändra innehållet i markdown-formatet som redan finns i svaret. QnA Maker stöder mycket av markdown-formatet för att få rtf-funktionerna till ditt innehåll. Klientprogrammet, till exempel en chattrobot, kanske inte stöder samma uppsättning markdown-format. Det är viktigt att testa klientprogrammets visning av svar.

Se en fullständig lista över [innehållstyper och exempel](./Concepts/content-types.md#file-and-url-data-types).

## <a name="basic-document-formatting"></a>Grundläggande dokumentformatering

QnA Maker identifierar avsnitt och underavsnitt och relationer i filen baserat på visuella ledtrådar som:

* teckenstorlek
* Teckenstil
* Numrering
* färger

## <a name="product-manuals"></a>Produktmanualer

En handbok är vanligtvis vägledning material som medföljer en produkt. Det hjälper användaren att konfigurera, använda, underhålla och felsöka produkten. När QnA Maker behandlar en handbok extraheras rubrikerna och underrubrikerna som frågor och det efterföljande innehållet som svar. Se ett exempel [här](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Nedan följer ett exempel på en handbok med en indexsida och hierarkiskt innehåll

 ![Produkthandbok exempel för en kunskapsbas](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extrahering fungerar bäst på handböcker som har en innehållsförteckning och/eller en indexsida, och en tydlig struktur med hierarkiska rubriker.

## <a name="brochures-guidelines-papers-and-other-files"></a>Broschyrer, riktlinjer, papper och andra filer

Många andra typer av dokument kan också bearbetas för att generera QA-par, förutsatt att de har en tydlig struktur och layout. Dessa inkluderar: Broschyrer, riktlinjer, rapporter, vitböcker, vetenskapliga artiklar, policyer, böcker, etc. Se ett exempel [här](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Nedan visas ett exempel på ett halvstrukturerat dokument utan index:

 ![Halvstrukturerad doc för Azure Blob-lagring](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturerat QnA-dokument

Formatet för strukturerade frågestunder i DOC-filer, är i form av alternerande frågor och svar per rad, en fråga per rad följt av dess svar på följande rad, som visas nedan:

```text
Question1

Answer1

Question2

Answer2
```

Nedan följer ett exempel på ett strukturerat QnA-orddokument:

 ![Strukturerat QnA-dokumentexempel för en kunskapsbas](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturerade *TXT-,* *TSV-* och *XLS-filer*

QnAs i form av strukturerade *.txt,* *.tsv* eller *.xls* filer kan också laddas upp till QnA Maker för att skapa eller utöka en kunskapsbas.  Dessa kan antingen vara oformaterad text eller ha innehåll i RTF eller HTML.

| Fråga  | Svar  | Metadata (1 nyckel: 1 värde) |
|-----------|---------|-------------------------|
| Fråga1 | Svar1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Fråga2 | Svar2 |      `Key:Value`           |

Ytterligare kolumner i källfilen ignoreras.

### <a name="example-of-structured-excel-file"></a>Exempel på strukturerad Excel-fil

Nedan är ett exempel på en strukturerad QnA *.xls-fil* med HTML-innehåll:

 ![Strukturerad QnA utmärker sig exempel för en kunskapsbas](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Exempel på alternativa frågor för ett enda svar i Excel-filen

Nedan är ett exempel på en strukturerad QnA *.xls-fil,* med flera alternativa frågor för ett enda svar:

 ![Exempel på alternativa frågor för ett enda svar i Excel-filen](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

När filen har importerats finns fråge- och svarsparet i kunskapsbasen enligt nedan:

 ![Skärmbild av alternativa frågor för ett enda svar som importeras till kunskapsbas](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturerat dataformat genom import

Om du importerar en kunskapsbas ersätts innehållet i den befintliga kunskapsbasen. Import kräver en strukturerad TSV-fil som innehåller information om datakällan. Den här informationen hjälper QnA Maker att gruppera frågesvarsparen och tillskriva dem en viss datakälla.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |
|-----------|---------|----|---------------------|
| Fråga1 | Svar1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Fråga2 | Svar2 | Redaktionella|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Dokumentformatering med flera varv

* Använd rubriker och underrubriker för att beteckna hierarki. Du kan till exempel H1 ange den överordnade QnA och h2 för att beteckna QnA som ska tas som prompt. Använd liten rubrikstorlek för att ange efterföljande hierarki. Använd inte stil, färg eller någon annan mekanism för att antyda struktur i dokumentet, QnA Maker kommer inte att extrahera flera varvsansningar.
* Det första tecknet i rubriken måste aktiveras.
* Avsluta inte en rubrik med `?`ett frågetecken.

**Exempeldokument:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Fördelar (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso Fördelar (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Nästa steg

Se en fullständig lista över [innehållstyper och exempel](./Concepts/content-types.md#file-and-url-data-types)