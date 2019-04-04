---
title: Använda Video Indexer-Redigeraren för att skapa projekt
titlesuffix: Azure Media Services
description: Det här avsnittet visar hur du använder Video Indexer-Redigeraren för att skapa projekt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896647"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Använda Video Indexer-Redigeraren för att skapa projekt

Video Indexer-webbplats, kan du använda dina videor djupa insikter till: hitta rätt medieinnehållet, leta upp de delar som du är intresserad av och använda resultatet för att skapa ett helt nytt projekt. När du skapat projektet kan återges och hämtas från Video Indexer och användas i din egen Redigera program eller underordnad arbetsflöden.

Vissa scenarier där du kan vara användbara den här funktionen är: 

* Skapa film visar för släpfordon.
* Med gamla klipp av videor i news-sändningar.
* Skapa kortare innehåll för sociala medier.

Den här artikeln visar hur du skapar ett projekt från grunden och hur du skapar ett projekt från en video i ditt konto.

## <a name="create-new-project-and-manage-videos"></a>Skapa nytt projekt och hantera videor

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Välj den **projekt** fliken. Om du har skapat projekt innan, visas alla dina andra projekt här.
1. Klicka på **Skapa nytt projekt**.  

    ![Nytt projekt](./media/video-indexer-view-edit/new-project.png)
1. Namnge ditt projekt genom att klicka på pennikonen. Ersätt texten där det står ”Namnlös project” med ditt projektnamn och klicka på kontrollen.

    ![Nytt projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Lägg till videor i projektet

> [!NOTE]
> Projekt kan för närvarande kan bara innehålla videor som indexeras i samma språk. När du har valt en video på ett språk du inte lägga till videor i ditt konto som är i ett annat språk.

1. Lägg till videor som du vill arbeta med i det här projektet genom att välja **lägga till videor**.

    Du kommer se alla videor i ditt konto och en sökruta där det står ”Sök efter text, nyckelord eller visuellt innehåll”. Att söka efter videor som har en angiven person, etikett, varumärke, nyckelord eller förekomsten i avskrift och OCR.
    
    I bilden nedan har söker vi till exempel efter videor som nämner ”GitHub”.
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Du kan filtrera resultatet ytterligare genom att välja **filtrera resultaten**. Du kan filtrera att visa videor som har en viss person i dem eller ange att du bara vill se videon resultat som är en i ett visst språk eller ha en specifik ägare. <br/> Du kan också ange omfattningen av din fråga. Till exempel om du vill söka efter ”GitHub” i OCR väljer **Visual Text**.

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    Du kan även lägga till flera filter i frågan. Använd den **+** / **-** knappar för att lägga till/ta bort filter. Använd **ta bort filter** att ta bort alla filter.
1. Om du vill lägga till videor, markerar du dem och välj sedan **Lägg till**.
1. Du kommer nu se alla videor som du har valt. Det här är de videor som du ska markera klipp för ditt projekt.

    Du kan ändra ordning på videor genom att dra och släppa eller genom att välja på menyknappen i listan och välja **Flytta ned** eller **Flytta upp**. På menyn lista kommer du även att kunna ta bort videon från det här projektet. 

    ![Ordna](./media/video-indexer-view-edit/rearrange.png)
    
    Har du möjlighet att lägga till fler videor i det här projektet när som helst genom att välja **lägga till videor**. Du kan också lägga till flera förekomster av samma videon i projektet. Du kanske vill göra detta om du vill visa ett klipp från en video och sedan ett klipp från en annan och sedan ett annat klipp från den första videon. 

### <a name="select-clips-to-use-in-your-project"></a>Välj klipp ska användas i ditt projekt

Om du klickar på pilen till höger i varje video nedåt öppnas dig insikter i videon baserat på tidsstämplar (klipp av videon). 

1. Välj **visa insikter** för att anpassa vilka insikter du vill se och som du inte vill se. 

    ![Visa insikter](./media/video-indexer-view-edit/insights.png)
1. Använd sökrutan som säger ”söka i avskriften, visual text, personer och etiketter” för att skapa frågor för specifika klipp.
1. Lägg till filter för att ange ytterligare information om på vilka scener som du letar efter genom att välja **filteralternativ**.

    ![Filtrera alternativ](./media/video-indexer-view-edit/filter-options.png)

    Du kan till exempel vill se klipp där GitHub nämns medan Donovan Brown som visas på skärmen. För det behöver du lägga till ett ”inkluderar”-filter som har ”användare” som typ av information. Sedan måste du ange ”Donovan Brown” i sökrutan för filtret.
    
    ![Ta med](./media/video-indexer-view-edit/include.png)
    
    Om du vill att klipp där GitHub nämns Donovan Brown är _inte_ på skärmen helt enkelt ändrar du filtret ”inkluderar” i ett ”exkluderingsfilter”, med hjälp av listrutan. 

1. Lägg till ett klipp i projektet genom att välja segment som du vill lägga till. Du kan avmarkera klippet genom att klicka på segmentet igen.
    
    Lägg till alla segment i en video genom att klicka på menyalternativet lista bredvid video och välja **välja alla segment**. 

    ![Lägg till alla](./media/video-indexer-view-edit/add-all.png)

    Du kan radera alla ditt val genom att välja Radera markering.

> [!TIP]
> När du att välja och ordning klippen, kan du förhandsgranska video i spelaren till höger på sidan. 

![Förhandsversion](./media/video-indexer-view-edit/preview.png)

Kom ihåg att spara projektet när du gör ändringar genom att välja **spara projektet**. 

### <a name="render-and-download-the-project"></a>Rendera och ladda ned projektet

> [!NOTE]
> För Video Indexer betald konton, har återgivning av projektet kodningskostnader. Video Indexer utvärderingskonton är begränsad till 5 timmar för rendering.

1. När du är klar kan du se till att projektet har sparats. Du kan nu rendera det här projektet. Välj **återges och ladda ned**. 

    ![Spara](./media/video-indexer-view-edit/save.png)

    Det är ett popup-fönster som talar om att Video indexer renderas en fil och sedan länken kommer att skickas till e-post. Välj Fortsätt. 
    
    Du kan även se ett meddelande som projektet återges på sidan. När det är klart att återges, visas ett nytt meddelande som projektet har gjorts. Klicka på meddelandet för att ladda ned projektet. Hämtar projektet i mp4-format.

    ![Rendering klar](./media/video-indexer-view-edit/rendering-done.png)

1. Du kan komma åt sparade projekt från den **projekt** fliken. 

    Om du väljer det här projektet kan se du alla insikter och tidslinje för det här projektet. Om du väljer **Videoredigerare**, du kan fortsätta att göra ändringar för det här projektet. Redigeringar omfattar att lägga till eller ta bort videor och klipp eller byta namn på projektet.

    ![Videoredigeraren](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Skapa ett projekt från din video

Du kan skapa ett nytt projekt direkt från en video i ditt konto. 

1. Gå till den **biblioteket** fliken på Video Indexer-webbplats.
1. Öppna videon som du vill använda för att skapa projektet. På sidan insikter och tidslinje, Välj den **Videoredigerare** knappen.

    Detta tar dig till samma sida som du använde för att skapa ett nytt projekt. Till skillnad från det nya projektet kan du se tidsstämplad insikter segmenten i videon som du hade började redigera tidigare.

## <a name="see-also"></a>Se också

[Video Indexer-översikt](video-indexer-overview.md)

