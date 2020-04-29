---
title: Design kunskaps bas – QnA Maker
description: En QnA Maker kunskaps bas består av en uppsättning fråge-och svars uppsättningar (QnA) och valfria metadata som är associerade med varje QnA-par.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756782"
---
# <a name="question-and-answer-set-concepts"></a>Koncept för frågor och svars uppsättningar

En kunskaps bas består av fråge-och svars uppsättningar (QnA).  Varje uppsättning har ett svar och en uppsättning innehåller all information som är kopplad till det _svaret_. Ett svar kan likna en databas rad eller en data struktur instans.

## <a name="question-and-answer-sets"></a>Uppsättningar med frågor och svar

De **obligatoriska** inställningarna i en fråga-och-svar-uppsättning (QNA) är:

* en **Frågetext** för användar frågan, som används för att QNA Maker maskin inlärning, för att justera med texten i användarens fråga med annan formulering men samma svar
* svaret **–** uppsättningens svar är svaret som returneras när en användar fråga matchas med den associerade frågan

Varje uppsättning representeras av ett **ID**.

De **valfria** inställningarna för en uppsättning är:

* **Andra typer av frågor** – detta hjälper QNA Maker att returnera rätt svar för en större mängd olika frågor ordföljder
* **Metadata**: metadata är taggar som är associerade med ett QNA-par och som representeras som nyckel/värde-par. Metadata-Taggar används för att filtrera QnA-par och begränsa den mängd som frågan ska matchas över.
* Snabb **prompter**som används för att fortsätta en konversation med flera varv

![QnA Maker kunskaps baser](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Lägg till i kunskaps basen i redaktionellt

Om du inte har redan befintligt innehåll för att fylla i kunskaps basen kan du lägga till QnA-par i QnA Maker portalen. Lär dig hur du uppdaterar din kunskaps bas [här](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Redigera din kunskaps bas lokalt

När en kunskaps bas har skapats rekommenderar vi att du gör ändringar i kunskaps bas texten i [QNA Maker Portal](https://qnamaker.ai), i stället för att exportera och importera via lokala filer. Det kan dock finnas tillfällen då du behöver redigera en kunskaps bas lokalt.

Exportera kunskaps basen från sidan **Inställningar** och redigera sedan kunskaps basen med Microsoft Excel. Om du väljer att använda ett annat program för att redigera den exporterade filen kan det medföra syntaxfel på grund av att det inte är helt TSV. Microsoft Excel-TSV-filer innehåller vanligt vis inte formateringsfel.

När du är färdig med redigeringarna importerar du om TSV-filen från sidan **Inställningar** . Detta kommer att ersätta den aktuella kunskaps basen med den importerade kunskaps basen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kunskaps bas livs cykel i QnA Maker](./development-lifecycle-knowledge-base.md)