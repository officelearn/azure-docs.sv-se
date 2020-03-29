---
title: Design kunskapsbas - QnA Maker
description: En QnA Maker-kunskapsbas består av en uppsättning QnA-uppsättningar (Question and Answer) och valfria metadata som är associerade med varje QnA-par.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844332"
---
# <a name="question-and-answer-set-concepts"></a>Begrepp för fråge- och svarsuppsättning

En kunskapsbas består av fråge- och svarsuppsättningar (QnA).  Varje uppsättning har ett svar och en uppsättning innehåller all information som är associerad med det _svaret_. Ett svar kan löst likna en databasrad eller en datastrukturförekomst.

## <a name="question-and-answer-sets"></a>Uppsättningar med frågor och svar

De inställningar **som krävs** i en QnA-uppsättning (Question-and-Answer) är:

* en **fråga** - text av användarfrågan, som används för att QnA Maker maskininlärning, att anpassa sig till texten i användarens fråga med olika ordalydelse men samma svar
* **svaret** - uppsättningens svar är det svar som returneras när en användarfråga matchas med den associerade frågan

Varje uppsättning representeras av ett **ID**.

De **valfria** inställningarna för en uppsättning är:

* **Alternativa former av frågan** - detta hjälper QnA Maker tillbaka rätt svar för ett bredare utbud av frågor fraser
* **Metadata**: Metadata är taggar som är associerade med ett QnA-par och representeras som nyckelvärdespar. Metadatataggar används för att filtrera QnA-par och begränsa den uppsättning över vilken frågematchning som utförs.
* **Flersvängningar**, används för att fortsätta en konversation med flera svängar

![QnA Maker kunskapsbaser](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Redaktionellt lägga till kunskapsbas

Om du inte har befintligt innehåll för att fylla i kunskapsbasen kan du lägga till QnA-uppsättningar redaktionellt i QnA Maker-portalen. Läs om hur du uppdaterar kunskapsbasen [här](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Redigera din kunskapsbas lokalt

När en kunskapsbas har skapats rekommenderar vi att du gör ändringar i kunskapsbastexten i [QnA Maker-portalen](https://qnamaker.ai)i stället för att exportera och importera igen via lokala filer. Det kan dock finnas tillfällen då du behöver redigera en kunskapsbas lokalt.

Exportera kunskapsbasen från sidan **Inställningar** och redigera sedan kunskapsbasen med Microsoft Excel. Om du väljer att använda ett annat program för att redigera den exporterade filen kan programmet införa syntaxfel eftersom det inte är helt TSV-kompatibelt. Microsoft Excels TSV-filer introducerar i allmänhet inga formateringsfel.

När du är klar med dina redigeringar importerar du TSV-filen från sidan **Inställningar.** Detta ersätter helt den aktuella kunskapsbasen med den importerade kunskapsbasen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kunskapsbaslivscykeln i QnA Maker](./development-lifecycle-knowledge-base.md)