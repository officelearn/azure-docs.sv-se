---
title: Använda Video Indexer-webbplats för att anpassa en Person modell – Azure
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en Person modell med Video Indexer-webbplatsen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: 8dd535d97e40fe1dd4358d782db60940af1dd95d
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892848"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Anpassa en Person modell med Video Indexer-webbplats

Video Indexer stöder kändisigenkänning för videoinnehåll. Funktionen för igenkänning av kändisar täcker ungefär en miljon ansikten baserat på vanliga datakälla, till exempel IMDB och Wikipedia övre LinkedIn Påverkare. En detaljerad översikt finns i [anpassa en Person-modell i Video Indexer](customize-person-model-overview.md).

Du kan använda Video Indexer-webbplatsen för att redigera ansikten som upptäcktes på en video, enligt beskrivningen i det här avsnittet. Du kan också använda API: et, enligt beskrivningen i [anpassa en Person modell med API: er](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Central hantering av Person modeller i ditt konto

1. Om du vill visa, redigera och ta bort Person-modeller i ditt konto, bläddra till Video Indexer-webbplatsen och logga in.
2. Klicka på knappen innehållsmodellen anpassning i det övre högra hörnet på sidan.

    ![Anpassning av innehållsmodellen](./media/customize-face-model/content-model-customization.png)
3. Välj fliken personer.

    Standard Person modellen visas i ditt konto. Den Person som standard-modellen innehåller alla ansikten du redigeras eller ändras i insikter om dina videor som du inte har angett en anpassad modell Person under indexering. 

    Om du har skapat andra Person modeller, visas de på den här sidan också. 

    ![Anpassning av innehållsmodellen](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Skapa en ny Person-modell

1. Klicka på den **+ Lägg till modell** knappen.

    ![Lägg till en ny person](./media/customize-face-model/add-new-person.png)
2. Ange namnet på modellen och klicka på knappen Kontrollera bredvid namnet.

    ![Lägg till en ny person](./media/customize-face-model/add-new-person2.png)

    Du har skapat en ny Person-modell. Du kan nu lägga till ansikten till den nya Person-modellen.
3. Klicka på menyknappen i listan och välj **+ Lägg till person**.

    ![Lägg till en ny person](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Lägg till en ny person till en Person-modell

> [!NOTE]
> Video Indexer kan du lägga till flera personer med samma namn i en Person-modell. Men rekommenderar vi att du ger unque namn till varje person i din modell för användbarhet och tydlighet.

1. Klicka på menyknappen lista bredvid den Person-modell som du vill lägga till de står inför att för att lägga till ett nytt ansikte till en Person-modell.
1. Klicka på **+ Lägg till person** på menyn.

    ![Lägg till ett nytt ansikte till personen](./media/customize-face-model/add-new-face.png)
 
    Ett popup-fönster uppmanas du att fylla i personens information. Ange namnet på personen och klicka på knappen Kontrollera.

    ![Lägg till ett nytt ansikte till personen](./media/customize-face-model/add-new-face2.png)
 
Du kan sedan välja i Utforskaren eller dra och släpp ansikte avbildningarna av de står inför. Video Indexer tar alla standardavbildning filtyper (t.ex.: JPG, PNG med mera).

Video Indexer ska kunna identifiera förekomster av den här personen i framtida videor som du indexerar och aktuella videor som du hade redan indexeras, med hjälp av Person-modellen där du har lagt till den här nya ansikte till. Igenkänning av personen i din aktuella videor kan ta lite tid att gälla, eftersom det här är en batchprocess.


## <a name="rename-a-person-model"></a>Byt namn på en Person-modell

Du kan byta namn på någon Person-modell i ditt konto, inklusive den Person som standard-modellen. Även om du byter namn på din standard Person modell kommer den fortfarande att fungera som person standardmodellen i ditt konto.

1. Klicka på menyknappen lista bredvid den Person-modell som du vill byta namn på.
2. Klicka på **Byt namn på** på menyn.

    ![Byt namn på en person](./media/customize-face-model/rename-person.png)
3. Klicka på det aktuella namnet på modellen och Skriv namnet på ditt nya.

    ![Byt namn på en person](./media/customize-face-model/rename-person2.png)
4. Klicka på knappen Kontrollera för din modell till att byta namn.

## <a name="delete-a-person-model"></a>Ta bort en Person-modell

Du kan ta bort alla Person-modell som du skapade i ditt konto. Men du kan inte ta bort din modell för standard-person.

1. Klicka på **ta bort** på menyn.

    ![Ta bort en person](./media/customize-face-model/delete-person.png)
    
    Ett popup-fönster ska visas och meddelar dig om att den här åtgärden tar bort Person modellen och alla personer och filer som ingår. Det går inte att ångra den här åtgärden. 

    ![Ta bort en person](./media/customize-face-model/delete-person2.png)
1. Klicka på Ta bort igen om du är säker.

> [!NOTE]
> Befintliga videor som har indexerats med hjälp av den här (borttagna) Person modellen stöder inte möjligheten att uppdatera namnen på ansikten som visas i videon. Du kommer att kunna redigera namnen på ansikten i följande videor förrän du indexera om dem med hjälp av en annan Person-modell. Om du indexera utan att ange en Person-modell, att standardmodellen användas. 

## <a name="manage-existing-people-in-a-person-model"></a>Hantera befintliga personer i en Person-modell

Om du vill titta på innehållet i någon av dina Person-modeller, klickar du på pilen bredvid namnet på den Person-modellen.
Listrutan visar alla personer i den specifika Person-modellen. Om du klickar på knappen lista menyn bredvid varje för personerna som du ser hantera, byta namn på och ta bort alternativ.  

![Lägg till ett nytt ansikte till personen](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Byt namn på en person

1. Om du vill byta namn på en person i din modell Person, klicka på menyknappen i listan och välj **Byt namn på** meny.
1. Klicka på det aktuella namnet på den person och skriver in namnet på din nya.
1. Klicka på knappen Kontrollera och personen som kommer att byta namn.

### <a name="delete-a-person"></a>Ta bort en person

1. Om du vill ta bort en person från din Person-modell, klicka på menyknappen i listan och välj **ta bort** från listmenyn.
1. Ett popup-fönster visar att den här åtgärden tar bort personen och att den här åtgärden inte kan ångras.
1. Klicka på Ta bort igen och personen som tas bort från modellen Person.

### <a name="manage-a-person"></a>Hantera en person

Om du klickar på **hantera**, visas alla ytor som den här personen som tränas från. Dessa ansikten komma från förekomster av den personen i videor som använder den här modellen för Person eller från avbildningar som du har laddat upp manuellt. 

Du kan lägga till flera ansikten personen genom att klicka på Lägg till bilder.

Du kan använda fönstret hantera att byta namn på personen och ta bort personen från Person-modellen.

## <a name="use-a-person-model-to-index-a-video"></a>Använd en modell för personen för att indexera en video

Du kan använda en Person-modell som indexerar videon nya genom att tilldela personen modellen vid uppladdning av videon.

Om du vill använda din Person modell på en ny video, gör du följande:

1. Klicka på den **överför** knappen överst på sidan.

    ![Ladda upp](./media/customize-face-model/upload.png)
1. Ta bort videofilen i cirkeln eller bläddra till din fil.
1. Klicka på pilen i avancerade alternativ.

    ![Ladda upp](./media/customize-face-model/upload2.png)
1. Klicka på listrutan och välja modellen med personen som du skapade.

    ![Ladda upp](./media/customize-face-model/upload3.png)
1. Klicka på alternativet ladda upp i längst ned på sidan och nya videon kommer att indexeras med hjälp av Person-modellen.

Om du inte anger en Person modell under överföringen indexerar Video Indexer videon med hjälp av standard Person-modellen i ditt konto.

## <a name="use-a-person-model-to-reindex-a-video"></a>Använd en modell för personen för att indexera en video 

Om du vill använda en Person-modell för att indexera en video i din samling, går du till ditt konto-videor på startsidan för Video Indexer och hovra över namnet på den video som du vill indexera.

Du kan se alternativ för att redigera, ta bort och indexera videon. 

1. Klicka på alternativet för att indexera videon.

    ![Reindex](./media/customize-face-model/reindex.png)

    Nu kan du välja modellen Person att indexera videon med.
1. Klicka på listrutan och välj den Person-modell som du vill använda. 

    ![Reindex](./media/customize-face-model/reindex2.png)

1. Klicka på den **indexera** knappen och videon ska indexeras med hjälp av Person-modellen.

Alla nya ändringar du gör ansikten har identifierats som dessutom utsetts i videon som du precis omindexerade sparas i modellen Person som du använde för att indexera videon.

## <a name="managing-people-in-your-videos"></a>Hantera personer i dina videor

Du kan hantera ansikten som identifieras och personer som har identifierats i videor du indexera genom att redigera och ta bort ansikten.

Tar bort ett ansikte, tar bort ett specifikt ansikte från insikter om videon.

Redigerar ett ansikte, byter namn på ett ansikte som har identifierats och eventuellt identifieras i videon. När du redigerar ett ansikte i videon sparas det namnet som en person post i modellen Person som har tilldelats till videon vid uppladdning och indexering.

Om du inte tilldelar en Person modell till videon under överföringen, sparas redigeringarna i ditt konto standardmodell person.

### <a name="edit-a-face"></a>Redigera ett ansikte


> [!NOTE]
> Om en Person modell har två eller flera olika personer med samma namn, kan du inte tagga det namnet i videor som använder den Person-modellen. Du kommer endast att kunna göra ändringar i personer som delar samma namn på fliken personer av sidan innehållsmodellen anpassning i Video Indexer. Därför rekommenderar vi att du ger unika namn till varje person i din modell Person.

1. Bläddra till Video Indexer-webbplatsen och logga in.
1. Sök efter en video som du vill visa och redigera i ditt konto.
1. Om du vill redigera ett ansikte i videon, gå till fliken insikter och klicka på pennikonen i det övre högra hörnet i fönstret.

    ![Redigera ett ansikte i videon](./media/customize-face-model/edit-face.png)
1. Klicka på någon av de identifierade ansiktena och ändra deras namn från ”okänt #X” (eller det namn som tidigare tilldelats ansiktet). 
1. Klicka på kryssikonen bredvid det nya namnet när du har skrivit i det nya namnet. Detta sparar det nya namnet och identifierar och alla förekomster av den här ansikte i andra aktuella videor och i framtiden videor som du överför-namn. Igenkänning av ansikte i dina aktuella videor kan ta lite tid att gälla, eftersom det här är en batchprocess.

Om du namnger ett ansikte med namnet på en befintlig person i modellen Person som videon använder sammanfogar detta identifierade ansikts-avbildningar från den här videon för den personen med som redan finns i modellen. Om du ger ett ansikte med ett helt nytt namn, skapar detta en ny Person-post i modellen Person som med hjälp av videon. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Ta bort ett ansikte

Gå till fönstret insikter och klicka på pennikonen i det övre högra hörnet i fönstret för att ta bort en identifierad ansikte i videon. Klicka på alternativet Ta bort under av de står inför. Detta tar bort som identifierats ansikte från videon. Personens ansikte identifieras i de andra videor som den visas fortfarande, men du kan ta bort ansiktet från dessa videor samt när de har indexerats. Personen fortsätter om den hade fått även att finnas i Person modellen som används för att indexera videon som du tog bort ansiktet såvida inte ta bort personen som specifikt från Person-modellen.

![Ta bort ett ansikte i videon](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Nästa steg

[Anpassa Person modell med API: er](customize-person-model-with-api.md)
