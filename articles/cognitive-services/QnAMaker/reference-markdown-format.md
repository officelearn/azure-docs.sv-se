---
title: Markdown-format – QnA Maker
description: Nedan visas en lista med markdown-format som du kan använda i QnA Maker svars text.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: f3c51052abeb48584df8fa1e831dc4605d471741
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898568"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Markdown-format som stöds i QnA Maker svars text

QnA Maker lagrar svars text som markdown. Det finns många varianter av markdown. Använd denna referens för att se till att svars texten returneras och visas korrekt.

Använd **[CommonMark](https://commonmark.org/help/tutorial/index.html)** -självstudien för att validera din markdown. I självstudien finns **en funktion för** att snabbt kopiera och klistra in.

## <a name="supported-markdown-format"></a>Markdown-format som stöds

Nedan visas en lista med markdown-format som du kan använda i QnA Maker svars text.

|Syfte|Format|Markdown-exempel|Rendering<br>som det visas i Chat-roboten|
|--|--|--|--|
En ny rad mellan 2 meningar.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formatera en ny rad mellan två meningar](./media/qnamaker-concepts-datasources/format-newline.png)|
|Sidhuvuden från H1 till H6, antalet `#` anger vilket sidhuvud. 1 `#` är H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formatera med markdown-rubriker](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formatera med markdown-huvuden H1 till H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursiv stil |`*text*`|`How do I create a bot with *QnA Maker*?`|![formatera med kursiv stil](./media/qnamaker-concepts-datasources/format-italics.png)|
|Sträng (fet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formatera med stark markering för fetstil](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL för länk|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![format för URL (hyperlänk)](./media/qnamaker-concepts-datasources/format-url.png)|
|\* URL för offentlig bild|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![format för offentlig bild-URL ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Genomstruken|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![format för genomstruken](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Fet och kursiv stil|`***text***`|`How can I create a ***QnA Maker*** bot?`|![format för fet och kursiv stil](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Fet URL för länk|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![format för fet URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL för kursiv stil för länk|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![format för kursiv stil-URL](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape-markdown symboler|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![format för kursiv stil-URL](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Ordnad lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>I föregående exempel används automatisk numrering som är inbyggd i markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>I föregående exempel används explicit numrering.|![format för ordnad lista](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Osorterad lista|`\n * item1 \n * item2`<br>eller<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![format för osorterad lista](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Kapslade listor|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Du kan kapsla in sorterade och osorterade listor tillsammans. Fliken `\t`anger den underordnade elementets indrags nivå.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![format för kapslad osorterad lista](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![format för kapslad ordnad lista](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker bearbetar inte bilden på något sätt. Det är klient programmets roll för att återge avbildningen.

Om du vill lägga till innehåll med hjälp av Update/replace-API: er för kunskaps bas och innehållet/filen innehåller HTML-taggar kan du bevara HTML-koden i filen genom att se till att öppning och stängning av taggarna konverteras i kodat format.

| Bevara HTML  | Representation i API-begäran  | Representation i KB |
|-----------|---------|-------------------------|
| Ja | \&lt; br\&gt; | &lt;br&gt; |
| Ja | \&lt; H3\&gt; header\&lt;/H3\&gt; | &lt;H3&gt;-huvud&lt;/H3&gt; |

Dessutom konverteras CR LF (\r\n) till \n i KB. LF (\n) behålls i befintligt skick. Om du vill kringgå escape-sekvenser som a \t eller \n kan du använda omvänt snedstreck, till exempel: "\\\\r\\\\n" och "\\\\t"

## <a name="next-steps"></a>Nästa steg

Granska [fil format](reference-tsv-format-batch-testing.md)för batch-testning.