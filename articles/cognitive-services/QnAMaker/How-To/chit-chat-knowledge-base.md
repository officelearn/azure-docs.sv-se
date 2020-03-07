---
title: Att lägga till chit-chatt i en kunskapsbas med QnA Maker
titleSuffix: Azure Cognitive Services
description: Lägger till personliga chit-chatt till din robot gör det mer konversationsanpassade och engagerande när du skapar ett KB. QnA Maker kan du enkelt lägga till en i förväg uppsättning övre chit-chatt i din Kunskapsbas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a9a14056e6be62fc1c1b5e542c1a3acceb738eac
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389288"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Lägg till Chit-chatt i en kunskapsbas

Att lägga till chit-chatt i din robot gör det mer konversationsanpassade och engagerande. Funktionen chit-chatt i QnA maker kan du enkelt lägga till en i förväg uppsättning övre chit-chatt i kunskapsbasen (KB). Detta kan vara en startpunkt för din robot personlighet och kan du spara tid och pengar på att skriva dem från grunden.  

Den här data uppsättningen har cirka 100 scenarier med CHI2TEST i en röst av flera personer, som professionella, användarvänliga och witty. Välj den person som närmast liknar din robot röst. Får en användarfråga paras QnA Maker ihop med frågor och svar för närmaste kända chit-chatt.  

Några exempel på olika personliga uppgifter finns nedan. Du kan se alla personliga [data uppsättningar](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) tillsammans med information om dina personliga uppgifter.

För användar frågan för `When is your birthday?`har varje personlighet ett formaterat svar:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personlighet|Exempel|
|--|--|
|Professionell|Åldern gäller egentligen inte för mig.|
|Eget|Jag har verkligen ingen ålder.|
|Witty|Jag är ålders fri.|
|Caring|Jag har ingen ålder.|
|Entusiastisk|Jag är en bot, så jag har ingen ålder.|
||


## <a name="language-support"></a>Stöd för språk

Data uppsättningar för CHI2TEST-chatt stöds på följande språk:

|Språk|
|--|
|Kinesiska|
|Svenska|
|Franska|
|Tyskland|
|Italienska|
|Japanska|
|Koreanska|
|Portugisiska|
|Spanska|


## <a name="add-chit-chat-during-kb-creation"></a>Lägga till chit-chatt när KB skapas
Det finns ett alternativ för att lägga till chit-chatt under skapande av kunskapsbas, när du lägger till din käll-URL: er och filer. Välj den person som du vill som chit-chatt-bas. Om du inte vill lägga till en CHI2TEST, eller om du redan har stöd för CHI2TEST i dina data källor, väljer du **ingen**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Lägg till Chit-chatt till en befintlig KB
Välj din KB och gå till sidan **Inställningar** . Det finns en länk till alla data uppsättningar för CHI2TEST i lämpligt **. tsv** -format. Ladda ned den person som du vill och ladda upp dem som en källa. Se till att du inte redigera format eller metadata när du hämtar och ladda upp filen. 
  
![Lägg till chit-chatt till befintliga KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Redigera din chit-chatt frågor och svar
När du redigerar din Kunskapsbas visas en ny källa för chit-chatt, baserat på den person som du har valt. Du kan nu lägga till ändras frågor eller redigera svar, precis som med annan källa. 

![Redigera chit-chatt kunskapsbaser](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Om du vill visa metadata väljer du **visnings alternativ** i verktygsfältet och väljer sedan **Visa metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Lägg till ytterligare chit-chatt frågor och svar
Du kan lägga till nya chit-chatt frågor och svar som inte i den fördefinierade anges. Se till att du inte duplicerar ett QnA-par som redan omfattas av chit-chatt-uppsättningen. När du lägger till nya QnA i CHI2TEST läggs den till i din **redigerings** källa. För att se till att rangordningen förstår att detta är CHI2TEST, Lägg till nyckel/värde-paret "redaktionell: ChitChat", som visas i följande bild:
   
![! [Lägg till CHI2TEST kring] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Ta bort chit-chatt från en befintlig KB
Välj din KB och gå till sidan **Inställningar** . Specifika chit-chatt-källa har listats som en fil med namnet på valda personlighet. Du kan ta bort detta som en källfil.

![Ta bort chit-chatt från KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera en kunskaps bas](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Se även 

[Översikt över QnA Maker](../Overview/overview.md)
