---
title: Markdown-format - QnA Maker
description: Följande är listan över markdown-format som du kan använda i QnA Maker svarstext.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045399"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Markdown-format som stöds i QnA Maker-svarstext

QnA Maker lagrar svarstext som markdown. Det finns många smaker av markdown. Använd den här referensen för att se till att svarstexten returneras och visas korrekt.

Använd **[CommonMark](https://commonmark.org/help/tutorial/index.html)** den GemensammaMark-självstudien för att validera din Markdown. Självstudien har en **Prova den** funktion för snabbkopiera / klistra in validering.

## <a name="supported-markdown-format"></a>Markeringsformat som stöds

Följande är listan över markdown-format som du kan använda i QnA Maker svarstext.

|Syfte|Format|Markdown-exempel|Rendering<br>som visas i Chatt bot|
|--|--|--|--|
En ny linje mellan 2 meningar.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formatera en ny linje mellan två meningar](./media/qnamaker-concepts-datasources/format-newline.png)|
|Rubriker från h1 till h6, `#` antalet betecknar vilken rubrik. 1 `#` är h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formatera med markeringsrubriker](./media/qnamaker-concepts-datasources/format-headers.png)<br>![format med markeringsrubrikerna H1 till H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kursiv |`*text*`|`How do I create a bot with *QnA Maker*?`|![format med kursiv stil](./media/qnamaker-concepts-datasources/format-italics.png)|
|Stark (fet)|`**text**`|`How do I create a bot with **QnA Maker**?`|![format med stark markering för fetstil](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL för länk|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![format för URL (hyperlänk)](./media/qnamaker-concepts-datasources/format-url.png)|
|*URL för offentlig bild|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![format för offentlig bild-URL ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Genomstruken|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![format för genomstruken](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Fet och kursiv stil|`***text***`|`How can I create a ***QnA Maker*** bot?`|![format för fet och kursiv stil](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Fet URL för länk|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![format för fet webbse-adress](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Kursiv URL för länk|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![format för kursiv URL](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape-symboler för markering|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![format för kursiv URL](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Ordnad lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>I föregående exempel används automatisk numrering inbyggd i markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>I föregående exempel används explicit numrering.|![format för ordnad lista](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Osorterad lista|`\n * item1 \n * item2`<br>eller<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![format för oordnad lista](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Kapslade listor|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Du kan kapsla ihop ordnade och oordnade listor. Fliken `\t`, anger indragsnivån för det underordnade elementet.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![format för kapslad oordnad lista](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![format för kapslad ordnad lista](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker bearbetar inte bilden på något sätt. Det är klientprogrammets roll att återge avbildningen.

Om du vill lägga till innehåll med hjälp av api:er för uppdatering/ersättning och innehållet/filen innehåller html-taggar kan du bevara HTML-koden i filen genom att se till att öppnandet och stängningen av taggarna konverteras i det kodade formatet.

| Bevara HTML  | Representation i API-begäran  | Representation i KB |
|-----------|---------|-------------------------|
| Ja | \&Lt;br\&gt; | &lt;Br&gt; |
| Ja | \&Lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3-sidhuvud&gt;&lt;/h3&gt; |

Dessutom konverteras CR LF(\r\n) till \n i KB. LF(\n) hålls som den är. Om du vill fly från en escape-sekvens som en \t eller \n\\\\kan du\\\\använda omvänt snedstreck, till exempel : '\\\\r n' och ' t'

## <a name="next-steps"></a>Nästa steg

Granska filformat för [batchtestning](reference-tsv-format-batch-testing.md).
