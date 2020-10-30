---
title: Använd Video Indexer redigeraren för att skapa projekt
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder Video Indexer redigeraren för att skapa projekt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 28186a7dcced47a42b3249f1f74b13e969b41978
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041362"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Använd Video Indexer redigeraren för att skapa projekt

Med Video Indexer webbplats kan du använda dina videor med djupgående insikter för att: hitta rätt medie innehåll, hitta de delar som du är intresse rad av och Använd resultatet för att skapa ett helt nytt projekt. När projektet har skapats kan projektet renderas och hämtas från Video Indexer och användas i dina egna redigerings program eller i efterföljande arbets flöden.

Vissa scenarier där den här funktionen kan vara användbar är: 

* Skapa film markeringar för släp vagnar.
* Använda gamla klipp av videor i nyhets brev.
* Skapa kortare innehåll för sociala medier.

Den här artikeln visar hur du skapar ett projekt från grunden och hur du skapar ett projekt från en video i ditt konto.

## <a name="create-new-project-and-manage-videos"></a>Skapa nytt projekt och hantera videor

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Välj fliken **projekt** . Om du har skapat projekt tidigare visas alla dina andra projekt här.
1. Klicka på **Skapa nytt projekt** .  

    ![Skärm bild som visar sidan "Video Indexer" med fliken "projekt" vald.](./media/video-indexer-view-edit/new-project.png)
1. Ge projektet ett namn genom att klicka på Penn ikonen. Ersätt texten som säger "namnlöst projekt" med ditt projekt namn och klicka på krysset.

    ![Nytt projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Lägga till videor i projektet

> [!NOTE]
> För närvarande kan projekt bara innehålla videor som indexerats på samma språk. När du har valt en video på ett språk kan du inte lägga till videor i ditt konto på ett annat språk.

1. Lägg till videor som du vill arbeta med i det här projektet genom att välja **Lägg till videor** .

    Du kommer att se alla videor i ditt konto och en sökruta med texten "söka efter text, nyckelord eller visuellt innehåll". Om du vill söka efter videor som har en angiven person, etikett, varumärke, nyckelord eller förekomst i avskriften och OCR.
    
    I bilden nedan söker vi till exempel efter videor som nämner "GitHub".
    
    ![Skärm bild som visar sökning efter videor som nämner GitHub med två resultat.](./media/video-indexer-view-edit/github.png)

    Du kan filtrera resultaten ytterligare genom att välja **filter resultat** . Du kan filtrera för att visa videor som har en viss person i dem eller för att ange att du bara vill visa video resultat som är ett visst språk eller som har en specifik ägare. <br/> Du kan också ange omfattningen för frågan. Om du till exempel vill söka efter "GitHub" i OCR väljer du **visuell text** .

    ![Filtrera](./media/video-indexer-view-edit/visual-text.png)

    Du kan skikta flera filter till frågan. Använd **+** / **-** knapparna för att lägga till/ta bort filter. Använd **Rensa filter** för att ta bort alla filter.
1. Om du vill lägga till videor markerar du dem och väljer sedan **Lägg till** .
1. Nu visas alla videor som du har valt. Det här är de videor från vilka du ska välja klipp för ditt projekt.

    Du kan ändra ordningen på videor genom att dra och släppa eller genom att välja menyn List meny och välja **Flytta ned** eller **Flytta upp** . Du kan också ta bort videon från det här projektet från menyn lista. 

    ![Skärm bild som visar Video Indexer med en snabb meny för en av videorna för att ta bort, rensa markeringen eller flytta ned.](./media/video-indexer-view-edit/rearrange.png)
    
    Du kan när som helst lägga till fler videor i projektet genom att välja **Lägg till videor** . Du kan också lägga till flera förekomster av samma video i projektet. Du kanske vill göra detta om du vill visa ett klipp från en video och sedan ett klipp från en annan och ett annat klipp från den första videon. 

### <a name="select-clips-to-use-in-your-project"></a>Välj klipp som ska användas i projektet

Om du klickar på nedåtpilen till höger om varje video kommer du att öppna insikterna i videon baserat på tidsstämplar (klipp i videon). 

1. Välj **Visa insikter** för att anpassa vilka insikter du vill se och vilka du inte vill se. 

    ![Visa insikter](./media/video-indexer-view-edit/insights.png)
1. Om du vill skapa frågor för vissa klipp använder du sökrutan med texten "Sök i avskrift, visuell text, personer och etiketter".
1. Lägg till filter för att ytterligare ange information om vilka scener du letar efter genom att välja **filter alternativ** .

    ![Filtrera alternativ](./media/video-indexer-view-edit/filter-options.png)

    Du kanske till exempel vill se klipp där GitHub anges medan Donovan Brown är på skärmen. För detta måste du lägga till ett "inkludera"-filter som har "personer" som typ av insikter. Sedan måste du skriva in "Donovan Brown" i Sök fältet för filtret.
    
    ![Skärm bild som visar Video Indexer med personer som är markerade för ett include-filter.](./media/video-indexer-view-edit/include.png)
    
    Om du vill att klipp där GitHub anges medan Donovan Brown _inte_ finns på skärmen, ändrar du bara filtret "ta med" till ett "exkludera"-filter med hjälp av list rutan. 

1. Lägg till ett klipp i projektet genom att välja det segment som du vill lägga till. Du kan avmarkera det här klippet genom att klicka på segmentet igen.
    
    Lägg till alla segment i en video genom att klicka på meny alternativet lista bredvid videon och markera **Markera alla segment** . 

    ![Lägg till alla](./media/video-indexer-view-edit/add-all.png)

    Du kan avmarkera alla alternativ genom att välja Rensa markering.

> [!TIP]
> När du väljer och sorterar dina klipp kan du förhandsgranska videon i spelaren på höger sida av sidan. 

![Skärm bild som visar Video Indexer med förhands granskningen av en video på höger sida av fönstret.](./media/video-indexer-view-edit/preview.png)

Kom ihåg att spara projektet när du gör ändringar genom att välja **Spara projekt** . 

### <a name="render-and-download-the-project"></a>Rendera och ladda ned projektet

> [!NOTE]
> För Video Indexer betalda konton har åter givningen av projektet kodnings kostnader. Video Indexer utvärderings konton är begränsade till 5 timmars åter givning.

1. När du är färdig kontrollerar du att projektet har sparats. Nu kan du återge det här projektet. Välj **rendera och hämta** . 

    ![Skärm bild som visar Video Indexer med alternativet att rendera och ladda ned ditt projekt.](./media/video-indexer-view-edit/save.png)

    Det kommer att finnas en popup-meny som visar att video Indexer kommer att återge en fil och sedan skickas länken till e-postmeddelandet. Välj Fortsätt. 
    
    Du ser också ett meddelande om att projektet återges ovanpå sidan. När den är klar visas ett nytt meddelande om att projektet har Render ATS. Klicka på meddelandet för att ladda ned projektet. Projektet laddas ned i MP4-format.

    ![Rendera gjorda](./media/video-indexer-view-edit/rendering-done.png)

1. Du kan komma åt sparade projekt från fliken **projekt** . 

    Om du väljer det här projektet visas alla insikter och tids linjen för projektet. Om du väljer **video redigerare** kan du fortsätta att göra ändringar i det här projektet. Redigeringarna inkluderar att lägga till eller ta bort videor och klipp eller att byta namn på projektet.

    ![Video redigerare](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Skapa ett projekt från videon

Du kan skapa ett nytt projekt direkt från en video i ditt konto. 

1. Gå till fliken **bibliotek** på video Indexer webbplats.
1. Öppna den video som du vill använda för att skapa projektet. På sidan insikter och tids linje väljer du knappen för **video redigeraren** .

    Detta tar dig till samma sida som du använde för att skapa ett nytt projekt. Till skillnad från det nya projektet ser du de tidsstämplade insikts segmenten i videon som du började redigera tidigare.

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md)

