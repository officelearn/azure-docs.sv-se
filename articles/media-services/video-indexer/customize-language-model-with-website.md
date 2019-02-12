---
title: Använda Video Indexer-webbplats för att anpassa en språkmodell - Azure
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en språkmodell med Video Indexer-webbplatsen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: be0b930c99bb14c34e395efce494e8d7372719b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998202"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Anpassa en språkmodell med Video Indexer-webbplats

Video Indexer kan du skapa anpassade språkmodeller om du vill anpassa taligenkänning genom att ladda upp anpassning text, nämligen text från domänen vars ordförråd som motorn efter behov. När du tränar modellen, identifieras nya ord visas i texten anpassning. 

En detaljerad översikt och bästa praxis för anpassade språkmodeller finns i [anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

Du kan använda Video Indexer-webbplatsen att skapa och redigera anpassade språkmodeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda API: et, enligt beskrivningen i [anpassa språkmodellen med API: er](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språkmodell

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Om du vill anpassa en modell i ditt konto, klickar du på den **innehåll modellanpassning** knappen i det övre högra hörnet på sidan.

   ![Anpassa innehållsmodellen](./media/content-model-customization/content-model-customization.png)

3. Välj den **språk** fliken.

    Du kan se en lista över språk som stöds. 

    ![Anpassa språkmodellen](./media/customize-language-model/customize-language-model.png)

4. Under det språk som du vill, klickar du på **Lägg till modell**.
5. Ange ett namn för språkmodell och tryck på RETUR.

    Detta skapar modellen och ger möjlighet att överföra textfiler till modellen.
6. Lägg till en textfil, klicka på **Lägg till filen**. Detta öppnar Utforskaren.

7. Navigera till och markera textfilen. Du kan lägga till flera textfiler en språkmodell.

    Du kan också lägga till en textfil genom att klicka på den **...**  knappen på höger sida av språkmodellen och välja **Lägg till filen**.
8. När du är klar överför textfiler, klicka på gröna **träna** alternativet.

    ![Träna språkmodell](./media/customize-language-model/train-model.png)

Utbildning-processen kan ta några minuter. När utbildningen är klar kan du se **Trained** bredvid modellen. Du kan förhandsgranska, hämta och ta bort filen från modellen.

![Tränade språkmodell](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Med hjälp av en språkmodell på en ny video

Om du vill använda din språkmodell på en ny video, gör du något av följande:

* Klicka på den **överför** knappen överst på sidan 

    ![Ladda upp](./media/customize-language-model/upload.png)
* Ta bort filen ljud- eller videoklipp i cirkeln eller bläddra efter din fil

    ![Ladda upp](./media/customize-language-model/upload2.png)

Detta ger dig möjlighet att välja den **Video källspråk**. Klicka på listrutan och välj en språkmodell som du skapade i listan. Det bör stå språket i din språkmodell och det namn som du gav den inom parentes.

Klicka på den **överför** alternativet längst ned på sidan och nya videon kommer att indexeras med hjälp av språkmodeller.

### <a name="using-a-language-model-to-reindex"></a>Med hjälp av en språkmodell att indexera

Om du vill använda din språkmodell för att indexera en video i din samling, gå till din **konto videor** på den [Video Indexer](https://www.videoindexer.ai/) startsida och hovra över namn på videon som du vill indexera.

Du kan se alternativ för att redigera din video, ta bort du video och indexera videon. Klicka på alternativet för att indexera videon.

![Reindex](./media/customize-language-model/reindex1.png)

Detta ger dig möjlighet att välja den **Video källspråk** att indexera videon med. Klicka på listrutan och välj en språkmodell som du skapade i listan. Det bör stå språket i din språkmodell och det namn som du gav den inom parentes.

![Reindex](./media/customize-language-model/reindex.png)

Klicka på den **indexera** knappen och videon ska indexeras med du språkmodellen.

## <a name="edit-a-language-model"></a>Redigera en språkmodell

Du kan redigera en språkmodell genom att ändra dess namn, lägga till filer och ta bort filer från den.

Om du lägger till eller ta bort filer från språkmodellen du behöver träna modellen igen genom att klicka i gröna **träna** alternativet.

### <a name="rename-the-language-model"></a>Byt namn på språkmodellen

Du kan ändra namnet på språkmodellen genom att klicka på **...**  på höger sida av språket modellera och välja **Byt namn på**. 

Typen i det nya namnet och därefter ange.

### <a name="add-files"></a>Lägg till filer

Lägg till en textfil, klicka på **Lägg till filen**. Detta öppnar Utforskaren.

Navigera till och markera textfilen. Du kan lägga till flera textfiler en språkmodell.

Du kan också lägga till en textfil genom att klicka på den **...**  knappen på höger sida av språkmodellen och välja **Lägg till filen**.

### <a name="delete-files"></a>Ta bort filer

Om du vill ta bort en fil från språkmodellen klickar du på den **...**  på höger sida av textfilen och välj **ta bort**. Detta öppnar ett nytt fönster som talar om att borttagningen inte kan ångras. Klicka på den **ta bort** alternativet i nytt fönster.

Den här åtgärden tar bort filen helt från språkmodellen.

## <a name="delete-a-language-model"></a>Ta bort en språkmodell

Ta bort en språkmodell från ditt konto genom att klicka på den **...**  på höger sida av språkmodellen och välj **ta bort**.

Detta öppnar ett nytt fönster som talar om att borttagningen inte kan ångras. Klicka på den **ta bort** alternativet i nytt fönster.

Den här åtgärden tar bort språkmodellen helt från ditt konto. En video som använde har tagits bort språkmodellen behåller samma index tills du indexera om videon. Om du indexera om videon, kan du tilldela en ny språkmodell till videon. I annat fall använder Video Indexer dess standardmodell för att indexera om videon. 

## <a name="next-steps"></a>Nästa steg

[Anpassa språkmodellen med API: er](customize-language-model-with-api.md)
