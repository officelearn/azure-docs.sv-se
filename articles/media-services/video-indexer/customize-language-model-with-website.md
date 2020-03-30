---
title: Anpassa språkmodell med videoindexer webbplats
titleSuffix: Azure Media Services
description: Läs om hur du anpassar en språkmodell med webbplatsen Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128087"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Anpassa en språkmodell med webbplatsen Video Indexer

Med Video Indexer kan du skapa anpassade språkmodeller för att anpassa taligenkänning genom att ladda upp anpassningstext, nämligen text från den domän vars ordförråd du vill att motorn ska anpassa sig till. När du tränar din modell kommer nya ord som visas i anpassningstexten att kännas igen.

En detaljerad översikt och metodtips för anpassade språkmodeller finns i [Anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

Du kan använda webbplatsen Video Indexer för att skapa och redigera anpassade språkmodeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda API:et enligt beskrivningen i [Anpassa språkmodellen med API:er](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språkmodell

1. Gå till [videoindexerarens](https://www.videoindexer.ai/) webbplats och logga in.
2. Om du vill anpassa en modell i ditt konto väljer du **anpassningsknappen Innehållsmodell** längst upp till höger på sidan.

   ![Anpassa innehållsmodellen i Video Indexer](./media/content-model-customization/content-model-customization.png)

3. Välj fliken **Språk.**

    Du ser en lista över språk som stöds.

    ![Lista över språkmodeller i Video Indexer](./media/customize-language-model/customize-language-model.png)

4. Under önskat språk väljer du **Lägg till modell**.
5. Skriv in namnet på språkmodellen och tryck enter.

    Det här steget skapar modellen och ger möjlighet att ladda upp textfiler till modellen.

6. Om du vill lägga till en textfil väljer du **Lägg till fil**. Utforskaren öppnas.

7. Navigera till och markera textfilen. Du kan lägga till flera textfiler i en språkmodell.

    Du kan också lägga till en textfil genom att markera **...** knappen till höger i språkmodellen och välja **Lägg till fil**.

8. När du har laddat upp textfilerna väljer du alternativet Grönt **Tåg.**

    ![Träna språkmodell i Video Indexer](./media/customize-language-model/train-model.png)

Träningsprocessen kan ta några minuter. När träningen är klar ser du **Tränad bredvid** modellen. Du kan förhandsgranska, hämta och ta bort filen från modellen.

![Tränad språkmodell i Video Indexer](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Använda en språkmodell på en ny video

Om du vill använda språkmodellen i en ny video gör du något av följande:

* Välj **knappen Ladda upp** högst upp på sidan.

    ![Knappen Ladda upp Video Indexer](./media/customize-language-model/upload.png)

* Släpp ljud- eller videofilen i cirkeln eller bläddra efter filen.

    ![Ladda upp mediefil Video Indexer](./media/customize-language-model/upload2.png)

Du får möjlighet att välja **videokällans språk**. Välj listrutan och välj en språkmodell som du har skapat i listan. Det bör stå språket i din språkmodell och det namn som du gav det inom parentes.

Välj alternativet **Ladda upp** längst ned på sidan så indexeras den nya videon med din språkmodell.

### <a name="using-a-language-model-to-reindex"></a>Använda en språkmodell för att indexera om

Om du vill använda språkmodellen för att indexera om en video i samlingen går du till dina **kontovideor** på startsidan för [Video indexeraren](https://www.videoindexer.ai/) och hovrar över namnet på videon som du vill indexera om.

Du ser alternativ för att redigera videon, ta bort video och indexera om videon. Välj alternativet om du vill indexera om videon.

![Indexera om med videoindexerare](./media/customize-language-model/reindex1.png)

Du får möjlighet att välja **videokällans språk** att indexera om videon med. Välj listrutan och välj en språkmodell som du har skapat i listan. Det bör stå språket i din språkmodell och det namn som du gav det inom parentes.

![Välj video källspråk – indexera om en video med Video Indexer](./media/customize-language-model/reindex.png)

Välj knappen **Indexera om** så indexerar du om videon med din språkmodell.

## <a name="edit-a-language-model"></a>Redigera en språkmodell

Du kan redigera en språkmodell genom att ändra dess namn, lägga till filer i den och ta bort filer från den.

Om du lägger till eller tar bort filer från språkmodellen måste du träna modellen igen genom att välja det gröna alternativet **Tåg.**

### <a name="rename-the-language-model"></a>Byta namn på språkmodellen

Du kan ändra namnet på språkmodellen genom att välja ellipsknappen (**...**) till höger i språkmodellen och välja **Byt namn**.

Skriv in det nya namnet och tryck enter.

### <a name="add-files"></a>Lägga till filer

Om du vill lägga till en textfil väljer du **Lägg till fil**. Utforskaren öppnas.

Navigera till och markera textfilen. Du kan lägga till flera textfiler i en språkmodell.

Du kan också lägga till en textfil genom att välja ellipsknappen (**...**) till höger i språkmodellen och välja **Lägg till fil**.

### <a name="delete-files"></a>Ta bort filer

Om du vill ta bort en fil från språkmodellen markerar du knappen ellips (**...**) till höger i textfilen och väljer **Ta bort**. Ett nytt fönster visas där det visas att borttagningen inte kan ångras. Välj alternativet **Ta bort** i det nya fönstret.

Den här åtgärden tar bort filen helt från språkmodellen.

## <a name="delete-a-language-model"></a>Ta bort en språkmodell

Om du vill ta bort en språkmodell från ditt konto väljer du ellipsknappen (**...**) till höger i språkmodellen och väljer **Ta bort**.

Ett nytt fönster visas där det visas att borttagningen inte kan ångras. Välj alternativet **Ta bort** i det nya fönstret.

Den här åtgärden tar bort språkmodellen helt från ditt konto. Alla videoklipp som använde den borttagna språkmodellen behåller samma index tills du indexerar om videon. Om du indexerar om videon kan du tilldela videon en ny språkmodell. Annars använder Video Indexer sin standardmodell för att indexera om videon.

## <a name="customize-language-models-by-correcting-transcripts"></a>Anpassa språkmodeller genom att korrigera transkriptioner

Video Indexer stöder automatisk anpassning av språkmodeller baserat på de faktiska korrigeringar användarna gör i transkriptionerna av sina videor.

1. Om du vill korrigera en utskrift öppnar du videon som du vill redigera från dina kontovideor. Välj fliken **Tidslinje.**

    ![Fliken Anpassa tidslinjen för språkmodell – Videoindexerare](./media/customize-language-model/timeline.png)

1. Välj pennikonen för att redigera transkriptionen.

    ![Anpassa språkmodellredigeringstranskriptionen – Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer fångar alla rader som korrigeras av dig i transkriptionen av din video och lägger till dem automatiskt i en textfil som heter "Från transkription redigeringar". Dessa redigeringar används för att omskola den specifika språkmodellen som användes för att indexera den här videon.
    
    Om du inte angav en språkmodell när du indexerade den här videon lagras alla ändringar för den här videon i en standardspråkmodell som kallas "Kontoanpassningar" på det identifierade språket i videon.
    
    Om flera redigeringar har gjorts på samma rad används endast den senaste versionen av den korrigerade raden för att uppdatera språkmodellen.  
    
    > [!NOTE]
    > Endast textkorrigeringar används för anpassningen. Korrigeringar som inte innehåller faktiska ord (till exempel skiljetecken eller blanksteg) ingår inte.
    
1. Du ser att transkriptionskorrigeringar visas på fliken Språk på anpassningssidan för innehållsmodell.

    ![Anpassa språkmodell – Video Indexer](./media/customize-language-model/customize.png)

   Om du vill titta på filen "Från avskriftsredigeringar" för var och en av dina språkmodeller väljer du den för att öppna den.

    ![Från transkriptredigeringar – Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Nästa steg

[Anpassa språkmodell med API:er](customize-language-model-with-api.md)
