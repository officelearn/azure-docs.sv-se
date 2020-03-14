---
title: Importera från data källor – QnA Maker
description: En QnA Maker kunskaps bas består av en uppsättning fråge-och svars uppsättningar (QnA) och valfria metadata som är associerade med varje QnA-par.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219691"
---
# <a name="importing-from-data-sources"></a>Importerar från datakällor

En kunskaps bas består av frågor och svars uppsättningar från offentliga URL: er och filer.

## <a name="data-source-locations"></a>Data-källplatser

Innehållet samlas in i en kunskaps bas från en data källa. Platser för data källor är **offentliga URL: er eller filer**som inte kräver autentisering.

[SharePoint-filer](../how-to/add-sharepoint-datasources.md), säkra med autentisering, är undantaget. SharePoint-resurser måste vara filer, inte webb sidor. Om URL: en slutar med ett webb tillägg, till exempel. ASPX importeras inte till QnA Maker från SharePoint.

## <a name="chit-chat-content"></a>Innehåll för CHI2TEST-chatt

Innehålls uppsättningen för Chi2test av QnA erbjuds som en fullständig innehålls data källa på flera språk och i konversations format. Detta kan vara en startpunkt för din robot personlighet och kan du spara tid och pengar på att skriva dem från grunden. Lär dig [hur du lägger till](../how-to/chit-chat-knowledge-base.md) den här innehålls uppsättningen i din kunskaps bas.

## <a name="structured-data-format-through-import"></a>Format för strukturerade data via import

Importera en kunskapsbas ersätter innehållet i den befintliga kunskapsbasen. Importen kräver en strukturerad `.tsv`-fil som innehåller frågor och svar. Den här informationen hjälper QnA Maker att gruppera frågans svars uppsättningar och attributerar dem till en viss data källa.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redigering|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturerat multi-turn-format genom import

Du kan skapa flera-turn-konversationer i ett `.tsv` fil format. Formatet ger dig möjlighet att skapa fler tråds samtal genom att analysera tidigare chatt-loggar (med andra processer, inte använda QnA Maker) och sedan skapa `.tsv`-filen via Automation. Importera filen för att ersätta den befintliga kunskaps basen.

> [!div class="mx-imgBorder"]
> ![konceptuell modell av 3 nivåer av fråga i flera vända frågor](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Kolumnen för en `.tsv`med flera inaktivitet **, som är**speciell för flera-Turn, visas. Ett exempel `.tsv`, som visas i Excel, visar den information som ska inkluderas för att definiera underordnade noder i flera varv:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** är numeriskt och **visnings** texten är text som inte innehåller markdown.

> [!div class="mx-imgBorder"]
> ![flera frågor, exempel som visas i Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportera som exempel

Om du är osäker på hur du ska representera din QnA i `.tsv`-filen skapar du uppsättningen i QnA Maker-portalen, sparar och exporterar sedan kunskaps basen för ett exempel på hur du representerar uppsättningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utvecklings livs cykel för en kunskaps bas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se även

Använd QnA Maker [markdown-referens](../reference-markdown-format.md) som hjälp för att formatera dina svar.

[Översikt över QnA Maker](../Overview/overview.md)

Skapa och redigera en kunskaps bas med:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generera ett svar med:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
