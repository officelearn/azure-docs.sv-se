---
title: Importera från datakällor - QnA Maker
description: En QnA Maker-kunskapsbas består av en uppsättning QnA-uppsättningar (Question and Answer) och valfria metadata som är associerade med varje QnA-par.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7772b2c76dfb92ced4d2c0d4b8dae97a59d1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457829"
---
# <a name="importing-from-data-sources"></a>Importerar från datakällor

En kunskapsbas består av fråge- och svarsuppsättningar som tagits in av offentliga webbadresser och filer.

## <a name="data-source-locations"></a>Platser för datakälla

Innehållet förs in i en kunskapsbas från en datakälla. Datakällplatser är **offentliga url:er eller filer**som inte kräver autentisering.

[SharePoint-filer](../how-to/add-sharepoint-datasources.md), säkrade med autentisering, är undantaget. SharePoint-resurser måste vara filer, inte webbsidor. Om webbadressen slutar med ett webbtillägg, till exempel . ASPX importeras den inte till QnA Maker från SharePoint.

## <a name="chit-chat-content"></a>Chatta med Chit

Chit-chatten QnA-innehållsuppsättningen erbjuds som en komplett innehållsdatakälla på flera språk och konversationsstilar. Detta kan vara en utgångspunkt för din bot personlighet, och det kommer att spara tid och kostnad för att skriva dem från grunden. Läs om hur du [lägger till](../how-to/chit-chat-knowledge-base.md) den här innehållsuppsättningen i kunskapsbasen.

## <a name="structured-data-format-through-import"></a>Strukturerat dataformat genom import

Om du importerar en kunskapsbas ersätts innehållet i den befintliga kunskapsbasen. Import kräver `.tsv` en strukturerad fil som innehåller frågor och svar. Den här informationen hjälper QnA Maker att gruppera frågesvarsuppsättningarna och tillskriva dem en viss datakälla.

| Fråga  | Svar  | Källa| Metadata (1 nyckel: 1 värde) |
|-----------|---------|----|---------------------|
| Fråga1 | Svar1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Fråga2 | Svar2 | Redaktionella|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturerat flersnäckningsformat genom import

Du kan skapa konversationer med `.tsv` flera varv i ett filformat. Formatet ger dig möjlighet att skapa flersvängningskonversationer genom att analysera tidigare chattloggar (med `.tsv` andra processer, inte använda QnA Maker) och sedan skapa filen genom automatisering. Importera filen för att ersätta den befintliga kunskapsbasen.

> [!div class="mx-imgBorder"]
> ![Konceptuell modell av 3 nivåer av multi-turn fråga](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Kolumnen för en multi-turn `.tsv`, specifik för flera varv är **Uppmaningar**. Ett `.tsv`exempel som visas i Excel visar den information som ska inkluderas för att definiera underordnade med flera varv:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**Visningsordern** är numerisk och **displayText** är text som inte ska innehålla markering.

> [!div class="mx-imgBorder"]
> ![Exempel på fråga med flera svänger som visas i Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportera som exempel

Om du är osäker på hur du `.tsv` ska representera QnA-uppsättningen i filen:
* Använd det här [nedladdningsbara exemplet från GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Eller skapa uppsättningen i QnA Maker-portalen, spara och exportera sedan kunskapsbasen för ett exempel på hur du representerar uppsättningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utvecklingslivscykel för en kunskapsbas](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Se även

Använd QnA Maker [Markdown-referensen](../reference-markdown-format.md) för att formatera dina svar.

[Översikt över QnA Maker](../Overview/overview.md)

Skapa och redigera en kunskapsbas med:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generera ett svar med:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
