---
title: Anpassa språk modeller med Video Indexer webbplats – Azure
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en språk modell med Video Indexer webbplats.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 329da39914ef957d3a5376ba59e0c7103ad6a5dd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513923"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Anpassa en språk modell med Video Indexer webbplats

Med Video Indexer kan du skapa anpassade språk modeller för att anpassa tal igenkänning genom att överföra anpassnings text, nämligen text från den domän vars vokabulär du vill att motorn ska anpassa sig till. När du tränar din modell kommer nya ord som visas i anpassnings texten att identifieras. 

En detaljerad översikt och bästa praxis för anpassade språk modeller finns i [Anpassa en språk modell med video Indexer](customize-language-model-overview.md).

Du kan använda Video Indexer webbplats för att skapa och redigera anpassade språk modeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda API: et enligt beskrivningen i [Anpassa språk modell med hjälp av API: er](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språk modell

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Om du vill anpassa en modell i ditt konto klickar du på knappen **anpassning av innehålls modell** i det övre högra hörnet på sidan.

   ![Anpassa innehålls modell](./media/content-model-customization/content-model-customization.png)

3. Välj fliken **språk** .

    Du ser en lista över språk som stöds. 

    ![Anpassa språk modell](./media/customize-language-model/customize-language-model.png)

4. Klicka på **Lägg till modell**under det språk som du vill använda.
5. Skriv in namnet på språk modellen och tryck på RETUR.

    Detta skapar modellen och ger alternativet att ladda upp textfiler till modellen.
6. Om du vill lägga till en textfil klickar du på **Lägg till fil**. Då öppnas Utforskaren.

7. Navigera till och välj text filen. Du kan lägga till flera textfiler i en språk modell.

    Du kan också lägga till en textfil genom att klicka på knappen **...** på höger sida av språk modellen och välja **Lägg till fil**.
8. När du är färdig med att ladda upp textfilerna klickar du på alternativet grönt **träna** .

    ![Träna språk modell](./media/customize-language-model/train-model.png)

Inlärnings processen kan ta några minuter. När utbildningen är färdig ser du **tränad** bredvid modellen. Du kan förhandsgranska, ladda ned och ta bort filen från modellen.

![Tränad språk modell](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Använda en språk modell i en ny video

Gör något av följande om du vill använda din språk modell på en ny video:

* Klicka på knappen **överför** överst på sidan 

    ![Ladda upp](./media/customize-language-model/upload.png)
* Ta bort ljud-eller video filen i cirkeln eller bläddra efter filen

    ![Ladda upp](./media/customize-language-model/upload2.png)

Detta ger dig möjlighet att välja **språk för video källan**. Klicka på list rutan och välj en språk modell som du skapade i listan. Det bör säga språket för din språk modell och det namn som du gav det inom parentes.

Klicka på alternativet **överför** längst ned på sidan så kommer din nya video att indexeras med din språk modell.

### <a name="using-a-language-model-to-reindex"></a>Använda en språk modell för att indexera om

Om du vill använda din språk modell för att indexera om en video i din samling, går du till dina **konto videor** på [video Indexer](https://www.videoindexer.ai/) start sida och hovrar över namnet på videon som du vill indexera om.

Du ser alternativ för att redigera videon, ta bort videon och indexera om videon. Klicka på alternativet för att indexera om videon.

![Indexera om](./media/customize-language-model/reindex1.png)

Det ger dig möjlighet att välja det **språk för video källa** som du vill indexera om videon med. Klicka på list rutan och välj en språk modell som du skapade i listan. Det bör säga språket för din språk modell och det namn som du gav det inom parentes.

![Indexera om](./media/customize-language-model/reindex.png)

Klicka på knappen för att indexera om och din video kommer att indexeras på **nytt** med hjälp av språk modellen.

## <a name="edit-a-language-model"></a>Redigera en språk modell

Du kan redigera en språk modell genom att ändra dess namn, lägga till filer i den och ta bort filer från den.

Om du lägger till eller tar bort filer från språk modellen måste du träna modellen igen genom att klicka på alternativet för grönt **träna** .

### <a name="rename-the-language-model"></a>Byt namn på språk modellen

Du kan ändra namnet på språk modellen genom att klicka på **...** på höger sida av språk modellen och välja **Byt namn**. 

Skriv det nya namnet och tryck på RETUR.

### <a name="add-files"></a>Lägg till filer

Om du vill lägga till en textfil klickar du på **Lägg till fil**. Då öppnas Utforskaren.

Navigera till och välj text filen. Du kan lägga till flera textfiler i en språk modell.

Du kan också lägga till en textfil genom att klicka på knappen **...** på höger sida av språk modellen och välja **Lägg till fil**.

### <a name="delete-files"></a>Ta bort filer

Om du vill ta bort en fil från språk modellen klickar du på knappen **...** på höger sida av text filen och väljer **ta bort**. Då visas ett nytt fönster som anger att borttagningen inte kan utföras. Klicka på alternativet **ta bort** i det nya fönstret.

Den här åtgärden tar bort filen helt från språk modellen.

## <a name="delete-a-language-model"></a>Ta bort en språk modell

Om du vill ta bort en språk modell från ditt konto klickar du på knappen **...** på höger sida av språk modellen och väljer **ta bort**.

Då visas ett nytt fönster som anger att borttagningen inte kan utföras. Klicka på alternativet **ta bort** i det nya fönstret.

Den här åtgärden tar bort språk modellen helt från ditt konto. Alla videoklipp som använder den borttagna språk modellen behåller samma index tills du Omindexerar videon igen. Om du indexerar om videon kan du tilldela en ny språk modell till videon. Annars kommer Video Indexer använda sin standard modell för att indexera om videon. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Anpassa språk modeller genom att korrigera avskrifter

Video Indexer stöder automatisk anpassning av språk modeller baserat på de faktiska korrigeringarna som användarna gör i avskrifterna av sina videor.

1. Om du vill göra ändringar i en avskrift öppnar du den video som du vill redigera från dina konto videor. Välj fliken **tids linje** .

    ![Anpassa språk modell](./media/customize-language-model/timeline.png)
1. Klicka på Penn ikonen för att redigera avskriften för avskriften. 

    ![Anpassa språk modell](./media/customize-language-model/edits.png)

    Video Indexer fångar alla rader som korrigeras av dig i avskriften av videon och lägger till dem automatiskt i en textfil med namnet "från avskrifts redigeringar". Dessa ändringar används för att träna om den specifika språk modell som användes för att indexera den här videon. 
    
    Om du inte angav någon språk modell när du indexerade videon, lagras alla redigeringar för videon i en standard språk modell som kallas konto anpassningar på det identifierade språket i videon. 
    
    Om flera ändringar har gjorts på samma rad används bara den senaste versionen av den korrigerade raden för att uppdatera språk modellen.  
    
    > [!NOTE]
    > Endast text korrigeringar används för anpassningen. Det innebär att korrigeringar som inte omfattar faktiska ord (till exempel skiljetecken eller blank steg) inte ingår. 
    
1. Avskrifts korrigeringar visas på fliken språk på sidan anpassning av innehålls modell.

    ![Anpassa språk modell](./media/customize-language-model/customize.png)

   Om du vill titta på filen "från avskrifts redigeringar" för var och en av dina språk modeller, klickar du på den för att öppna den. 

    ![Från avskrifts redigeringar](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Nästa steg

[Anpassa språk modellen med hjälp av API: er](customize-language-model-with-api.md)
