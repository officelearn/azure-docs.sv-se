---
title: Använda videoindexeraren för att skapa projekt
titleSuffix: Azure Media Services
description: Det här avsnittet visar hur du använder videoindexeraren för att skapa projekt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839171"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Använda videoindexeraren för att skapa projekt

Video Indexer webbplats, kan du använda dina videor djupa insikter till: hitta rätt medieinnehåll, hitta de delar som du är intresserad av, och använda resultaten för att skapa ett helt nytt projekt. När projektet har skapats kan det återges och hämtas från Video Indexer och användas i dina egna redigeringsprogram eller arbetsflöden i nedströms.

Några scenarier där du kan ha den här funktionen användbar är: 

* Skapa filmhöjdpunkter för trailers.
* Använda gamla klipp av videor i nyhetssändningar.
* Skapa kortare innehåll för sociala medier.

Den här artikeln visar hur du skapar ett projekt från grunden och även hur du skapar ett projekt från en video i ditt konto.

## <a name="create-new-project-and-manage-videos"></a>Skapa nytt projekt och hantera videor

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Välj fliken **Projekt.** Om du har skapat projekt tidigare ser du alla dina andra projekt här.
1. Klicka på **Skapa nytt projekt**.  

    ![Nytt projekt](./media/video-indexer-view-edit/new-project.png)
1. Ge projektet ett namn genom att klicka på pennikonen. Ersätt texten med texten "Namnlöst projekt" med ditt projektnamn och klicka på checken.

    ![Nytt projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Lägga till videor i projektet

> [!NOTE]
> För närvarande kan projekt endast innehålla videor indexerade på samma språk. När du har valt en video på ett språk kan du inte lägga till videorna i ditt konto som är på ett annat språk.

1. Lägg till videoklipp som du vill arbeta med i det här projektet genom att välja **Lägg till videoklipp**.

    Du kommer att se alla videor i ditt konto och en sökruta med texten "Sök efter text, nyckelord eller visuellt innehåll". Så här söker du efter videor som har en angiven person, etikett, varumärke, nyckelord eller förekomst i avskriften och OCR.
    
    Till exempel, i bilden nedan, vi letar efter videor som nämner "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Du kan filtrera dina resultat ytterligare genom att välja **Filterresultat**. Du kan filtrera för att visa videor som har en viss person i sig eller för att ange att du bara vill se videoresultat som är ett på ett visst språk eller har en viss ägare. <br/> Du kan också ange frågans omfattning. Om du till exempel vill söka i "GitHub" i OCR väljer du **Visuell text**.

    ![Filter](./media/video-indexer-view-edit/visual-text.png)

    Du kan lagra flera filter i frågan. Använd **+** / **-** knapparna för att lägga till/ta bort filter. Använd **Rensa filter** för att ta bort alla filter.
1. Om du vill lägga till videor markerar du dem och väljer sedan **Lägg till**.
1. Nu kommer du att se alla videor du valde. Det här är videoklippen som du ska välja klipp från för projektet.

    Du kan ordna om ordningen på videorna genom att dra och släppa eller genom att välja listmenyknappen och välja **Flytta nedåt** eller **Flytta uppåt**. Från listmenyn kommer du också att kunna ta bort videon från det här projektet. 

    ![Ordna](./media/video-indexer-view-edit/rearrange.png)
    
    Du har möjlighet att lägga till fler videor till det här projektet när som helst genom att välja **Lägg till videor**. Du kan också lägga till flera förekomster av samma video i projektet. Du kanske vill göra detta om du vill visa ett klipp från en video och sedan ett klipp från en annan och sedan ett annat klipp från den första videon. 

### <a name="select-clips-to-use-in-your-project"></a>Markera klipp som ska användas i projektet

Om du klickar på nedåtpilen till höger i varje video öppnar du insikterna i videon baserat på tidsstämplar (klipp i videon). 

1. Välj **Visa statistik** om du vill anpassa vilka insikter du vill se och vilka du inte vill se. 

    ![Visa insikter](./media/video-indexer-view-edit/insights.png)
1. Om du vill skapa frågor för specifika klipp använder du sökrutan med texten "Sök i transkription, visuell text, personer och etiketter".
1. Lägg till filter för att ytterligare ange information om vilka scener du letar efter genom att välja **Filteralternativ**.

    ![Filtrera alternativ](./media/video-indexer-view-edit/filter-options.png)

    Du kanske till exempel vill se klipp där GitHub nämns medan Donovan Brown visas på skärmen. För detta måste du lägga till ett "include"-filter som har "Personer" som typ av insikt. Du måste sedan skriva in "Donovan Brown" i sökrutan för filtret.
    
    ![Inkludera](./media/video-indexer-view-edit/include.png)
    
    Om du vill ha klipp där GitHub nämns medan Donovan Brown _inte_ finns på skärmen, skulle du helt enkelt ändra "inkludera" filtret till ett "exkludera" filter med hjälp av rullgardinsmenyn. 

1. Lägg till ett klipp i projektet genom att välja det segment som du vill lägga till. Du kan avmarkera det här klippet genom att klicka på segmentet igen.
    
    Lägg till alla segment i en video genom att klicka på listmenyalternativet bredvid videon och välja **Markera alla segment**. 

    ![Lägg till alla](./media/video-indexer-view-edit/add-all.png)

    Du kan rensa hela markeringen genom att välja Rensa markering.

> [!TIP]
> När du väljer och beställer dina klipp kan du förhandsgranska videon i spelaren till höger på sidan. 

![Förhandsversion](./media/video-indexer-view-edit/preview.png)

Kom ihåg att spara projektet när du gör ändringar genom att välja **Spara projekt**. 

### <a name="render-and-download-the-project"></a>Rendera och ladda ned projektet

> [!NOTE]
> För betalkonton för videoindexerare har rendering av projektet kodningskostnader. Video Indexer testkonton är begränsade till 5 timmars rendering.

1. När du är klar kontrollerar du att projektet har sparats. Du kan nu återge det här projektet. Välj **Rendera och hämta**. 

    ![Spara](./media/video-indexer-view-edit/save.png)

    Det kommer att finnas en popup som talar om att Video indexer kommer att återge en fil och sedan nedladdningslänken kommer att skickas till din e-post. Välj Fortsätt. 
    
    Du ser också ett meddelande om att projektet återges överst på sidan. När det har gjorts kommer du att se ett nytt meddelande om att projektet har renderats. Klicka på meddelandet för att hämta projektet. Det kommer att ladda ner projektet i mp4-format.

    ![Rendering klar](./media/video-indexer-view-edit/rendering-done.png)

1. Du kan komma åt sparade projekt på fliken **Projekt.** 

    Om du väljer det här projektet visas alla insikter och tidslinjen för det här projektet. Om du väljer **Videoredigerare**kan du fortsätta att göra ändringar i det här projektet. Redigeringar inkluderar att lägga till eller ta bort videor och klipp eller byta namn på projektet.

    ![Videoredigerare](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Skapa ett projekt från videon

Du kan skapa ett nytt projekt direkt från en video i ditt konto. 

1. Gå till fliken **Bibliotek** på webbplatsen Video Indexer.
1. Öppna videon som du vill använda för att skapa projektet. På sidan insikter och tidslinje väljer du knappen **Videoredigerare.**

    Detta tar dig till samma sida som du använde för att skapa ett nytt projekt. Till skillnad från det nya projektet visas de tidsstämplade statistiksegmenten i videon, som du hade börjat redigera tidigare.

## <a name="see-also"></a>Se även

[Översikt över Video Indexer](video-indexer-overview.md)

