---
title: Lägga till småprat i en QnA Maker-kunskapsbas
titleSuffix: Azure Cognitive Services
description: Genom att lägga till personlig småprat i din robot blir det mer konversations- och engagerande när du skapar en KB. Med QnA Maker kan du enkelt lägga till en förifylld uppsättning av den översta chit-chatten i din KB.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220714"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Lägg till Chit-chat i en kunskapsbas

Genom att lägga till småprat i din robot blir det mer konversera och engagerande. Chit-chat-funktionen i QnA maker gör att du enkelt kan lägga till en förifylld uppsättning av den översta chit-chat, i din kunskapsbas (KB). Detta kan vara en utgångspunkt för din bot personlighet, och det kommer att spara tid och kostnad för att skriva dem från grunden.  

Denna datauppsättning har cirka 100 scenarier för chit-chat i rösten av flera personas, som Professional, Friendly och Witty. Välj den persona som närmast liknar din bot röst. Med tanke på en användarfråga försöker QnA Maker matcha den med närmaste kända chit-chat QnA.  

Några exempel på de olika personligheterna finns nedan. Du kan se alla [personlighetsdatauppsättningar](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) tillsammans med detaljer om personligheterna.

För användarfrågan `When is your birthday?`för har varje personlighet ett formaterat svar:

<!-- added quotes so acrolinx doesn't score these sentences -->
|Personlighet|Exempel|
|--|--|
|Professionell|Ålder gäller inte mig.|
|Vänlig|Jag har ingen ålder.|
|Kvick|Jag är åldersfri.|
|Omtänksam|Jag har ingen ålder.|
|Entusiastiska|Jag är en bot, så jag har inte en ålder.|
||


## <a name="language-support"></a>Stöd för språk

Datauppsättningar för Chit-chat stöds på följande språk:

|Språk|
|--|
|Kinesiska|
|Svenska|
|Franska|
|Tyskland|
|Italienska|
|Japanska|
|Koreansk|
|Portugisiska|
|Spanska|


## <a name="add-chit-chat-during-kb-creation"></a>Lägg till små chatt under KB-skapande
När kunskapsbasen har skapats finns det ett alternativ för att lägga till småchatt efter att du har lagt till källadresser och filer. Välj den personlighet som du vill ha som din chit-chat bas. Om du inte vill lägga till småprat, eller om du redan har stöd för småchatt i dina datakällor, väljer du **Ingen**. 

## <a name="add-chit-chat-to-an-existing-kb"></a>Lägga till Chit-chat i en befintlig KB
Välj KB och navigera till sidan **Inställningar.** Det finns en länk till alla små chattdatauppsättningar i lämpligt **TSV-format.** Ladda ner den personlighet du vill ha och ladda sedan upp den som en filkälla. Se till att inte redigera formatet eller metadata när du hämtar och laddar upp filen. 
  
![Lägga till små chatt i befintlig KB](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Redigera dina frågor och svar om chit-chat
När du redigerar kb:n visas en ny källa för småprat, baserat på den personlighet du valt. Du kan nu lägga till ändrade frågor eller redigera svaren, precis som med alla andra källor. 

![Redigera chit-chat QnAs](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

Om du vill visa metadata väljer du **Visa alternativ** i verktygsfältet och väljer sedan **Visa metadata**.

## <a name="add-additional-chit-chat-questions-and-answers"></a>Lägg till ytterligare frågor och svar i små chatten
Du kan lägga till nya chit-chat QnA som inte finns i den fördefinierade uppsättningen. Se till att du inte duplicerar ett QnA-par som redan omfattas av chit-chat-uppsättningen. När du lägger till en ny chit-chat QnA läggs den till i din **redaktionella** källa. För att säkerställa att rankern förstår att detta är små chatt, lägg till metadata nyckel / värdepar "Editorial: småprat", som ses i följande bild:
   
![! [Lägg till chit-chat QnAs] (.. /media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png#lightbox)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Ta bort chit-chat från en befintlig KB
Välj KB och navigera till sidan **Inställningar.** Din specifika chit-chat-källa visas som en fil, med det valda personlighetsnamnet. Du kan ta bort detta som en källfil.

![Ta bort chit-chat från KB](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera en kunskapsbas](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>Se även 

[Översikt över QnA Maker](../Overview/overview.md)
