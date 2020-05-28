---
title: Design kunskaps bas – QnA Maker
description: En QnA Maker kunskaps bas består av en uppsättning fråga-och-svar-par (QnA) och valfria metadata som är kopplade till varje QnA-par.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: e75a9313f02a6b5cc4235cdcccb381585d2192ae
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993005"
---
# <a name="question-and-answer-pair-concepts"></a>Begrepp för frågor och svars par

En kunskaps bas består av QnA-par (frågor och svar).  Varje par har ett svar och ett par innehåller all information som är kopplad till det _svaret_. Ett svar kan likna en databas rad eller en data struktur instans.

## <a name="question-and-answer-pairs"></a>Frågor och svar-par

De **obligatoriska** inställningarna i ett QNA-par (fråga-and-Answer) är:

* en **Frågetext** för användar frågan, som används för att QNA Maker maskin inlärning, för att justera med texten i användarens fråga med annan formulering men samma svar
* **svaret** – parets svar är svaret som returneras när en användar fråga matchas med den associerade frågan

Varje par representeras av ett **ID**.

De **valfria** inställningarna för ett par är:

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