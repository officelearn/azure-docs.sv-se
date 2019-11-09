---
title: Använd Video Indexer webbplats för att anpassa en person modell – Azure
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en person modell med Video Indexer webbplats.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838277"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Anpassa en person modell med Video Indexer webbplats

Video Indexer stöder kändis igenkänning för video innehåll. Kändis igenkännings funktionen täcker cirka 1 000 000 ansikten baserat på den begärda data källan, till exempel IMDB-, Wikipedia-och Top LinkedIn-påverkan. En detaljerad översikt finns [i anpassa en person modell i video Indexer](customize-person-model-overview.md).

Du kan använda Video Indexer webbplats för att redigera ansikten som upptäcktes i en video, enligt beskrivningen i det här avsnittet. Du kan också använda API: et enligt beskrivningen i [Anpassa en person modell med hjälp av API: er](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Central hantering av person modeller i ditt konto

1. Om du vill visa, redigera och ta bort person modellerna i ditt konto bläddrar du till Video Indexer webbplats och loggar in.
2. Klicka på anpassnings knappen för innehålls modell i det övre högra hörnet på sidan.

    ![Anpassning av innehålls modell](./media/customize-face-model/content-model-customization.png)
3. Välj fliken personer.

    Du kan se standard modellen för personer i ditt konto. Standard person modellen innehåller alla ansikten som du kan ha redigerat eller ändrat i insikterna för dina videor där du inte har angett en anpassad person modell under indexeringen. 

    Om du har skapat andra person modeller visas de även på den här sidan. 

    ![Anpassning av innehålls modell](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>Skapa en ny person modell

1. Klicka på knappen **+ Lägg till modell** .

    ![Lägg till en ny person](./media/customize-face-model/add-new-person.png)
2. Ange namnet på modellen och klicka på kryss knappen bredvid namnet.

    ![Lägg till en ny person](./media/customize-face-model/add-new-person2.png)

    Du har skapat en ny person modell. Nu kan du lägga till ansikten i den nya person modellen.
3. Klicka på Meny knappen lista och välj **+ Lägg till person**.

    ![Lägg till en ny person](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>Lägg till en ny person till en person modell

> [!NOTE]
> Med Video Indexer kan du lägga till flera personer med samma namn i en person modell. Vi rekommenderar dock att du ger unque namn till varje person i din modell för användbarhet och tydlighet.

1. Om du vill lägga till en ny ansikts till en person modell klickar du på list meny knappen bredvid den person modell som du vill lägga till ansikte till.
1. Klicka på **+ Lägg till person** på menyn.

    ![Lägg till en ny ansikte till personen](./media/customize-face-model/add-new-face.png)
 
    Ett popup-fönster gör att du kan fylla i personens information. Skriv namnet på personen och klicka på knappen kontrol lera.

    ![Lägg till en ny ansikte till personen](./media/customize-face-model/add-new-face2.png)
 
Du kan sedan välja från Utforskaren eller dra och släppa ansiktenas ansikts bilder. Video Indexer kommer att ha alla standard avbildnings fil typer (t. ex. JPG, PNG och mer).

Video Indexer ska kunna identifiera förekomster av den här personen i de kommande videor som du indexerar och de aktuella videor som du redan har indexerat med hjälp av den person modell som du har lagt till i den nya ytan. Det kan ta lite tid att känna till att personen i dina aktuella videor börjar gälla, eftersom det är en batch-process.


## <a name="rename-a-person-model"></a>Byta namn på en person modell

Du kan byta namn på alla person modeller i ditt konto, inklusive standard modellen för personer. Även om du byter namn på din standard modell för personer fungerar den fortfarande som standard modell för personer i ditt konto.

1. Klicka på list meny knappen bredvid den person modell som du vill byta namn på.
2. Klicka på **Byt namn** på menyn.

    ![Byta namn på en person](./media/customize-face-model/rename-person.png)
3. Klicka på modellens aktuella namn och skriv in det nya namnet.

    ![Byta namn på en person](./media/customize-face-model/rename-person2.png)
4. Klicka på knappen kontrol lera att din modell har bytt namn.

## <a name="delete-a-person-model"></a>Ta bort en person modell

Du kan ta bort alla person modeller som du har skapat i ditt konto. Du kan dock inte ta bort din standard modell för personer.

1. Klicka på **ta bort** på menyn.

    ![Ta bort en person](./media/customize-face-model/delete-person.png)
    
    Ett popup-fönster visas och du får ett meddelande om att den här åtgärden tar bort person modellen och alla personer och filer som den innehåller. Det går inte att utföra den här åtgärden. 

    ![Ta bort en person](./media/customize-face-model/delete-person2.png)
1. Om du är säker klickar du på ta bort igen.

> [!NOTE]
> Befintliga videor som indexerades med den här (nu borttagna) person modellen stöder inte möjligheten att uppdatera namnen på de ansikten som visas i videon. Du kommer bara att kunna redigera namnen på ansikten i dessa videor när du har indexerat dem igen med en annan person modell. Om du indexerar utan att ange en person modell kommer standard modellen att användas. 

## <a name="manage-existing-people-in-a-person-model"></a>Hantera befintliga personer i en person modell

Om du vill titta på innehållet i någon av dina person modeller klickar du på pilen bredvid namnet på person modellen.
I list rutan visas alla personer i den specifika person modellen. Om du klickar på list meny knappen bredvid var och en av personerna visas alternativ för hantera, Byt namn och ta bort.  

![Lägg till en ny ansikte till personen](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>Byta namn på en person

1. Om du vill byta namn på en person i din person modell klickar du på Meny knappen lista och väljer **Byt namn** på list menyn.
1. Klicka på personens aktuella namn och skriv det nya namnet.
1. Klicka på knappen kontrol lera och personen får ett nytt namn.

### <a name="delete-a-person"></a>Ta bort en person

1. Om du vill ta bort en person från din person modell klickar du på Meny knappen lista och väljer **ta bort** på menyn lista.
1. Ett popup-fönster visar att den här åtgärden tar bort personen och att den här åtgärden inte kan ångras.
1. Klicka på ta bort igen så tas personen bort från person modellen.

### <a name="manage-a-person"></a>Hantera en person

Om du klickar på **Hantera**visas alla ansikten som den här person modellen tränas från. Dessa ansikten kommer från förekomster av den personen i videor som använder den här person modellen eller från bilder som du har laddat upp manuellt. 

Du kan lägga till fler ansikten till personen genom att klicka på Lägg till bilder.

Du kan använda fönstret Hantera för att byta namn på personen och ta bort personen från person modellen.

## <a name="use-a-person-model-to-index-a-video"></a>Använda en person modell för att indexera en video

Du kan använda en person modell för att indexera din nya video genom att tilldela person modellen under uppladdningen av videon.

Gör så här om du vill använda din person modell på en ny video:

1. Klicka på knappen **överför** överst på sidan.

    ![Ladda upp](./media/customize-face-model/upload.png)
1. Släpp video filen i cirkeln eller bläddra efter filen.
1. Klicka på pilen avancerade alternativ.

    ![Ladda upp](./media/customize-face-model/upload2.png)
1. Klicka på list rutan och välj den person modell som du har skapat.

    ![Ladda upp](./media/customize-face-model/upload3.png)
1. Klicka på alternativet överför längst ned på sidan så kommer din nya video att indexeras med din person modell.

Om du inte anger någon person modell under uppladdningen kommer Video Indexer att indexera videon med standard modellen för personer i ditt konto.

## <a name="use-a-person-model-to-reindex-a-video"></a>Använda en person modell för att indexera om en video 

Om du vill använda en person modell för att indexera om en video i din samling går du till dina konto videor på Video Indexer start sida och hovrar över namnet på videon som du vill indexera om.

Du ser alternativ för att redigera, ta bort och indexera om videon. 

1. Klicka på alternativet för att indexera om videon.

    ![Indexera om](./media/customize-face-model/reindex.png)

    Nu kan du välja den person modell som du vill indexera om videon med.
1. Klicka på list rutan och välj den person modell som du vill använda. 

    ![Indexera om](./media/customize-face-model/reindex2.png)

1. Klicka på knappen för att **Indexera** om och din video kommer att indexeras igen med hjälp av din person modell.

Alla nya ändringar som du gör i ansikten som identifieras och identifieras i videon som du just har omindexerat sparas i den person modell som du använde för att indexera om videon.

## <a name="managing-people-in-your-videos"></a>Hantera personer i dina videor

Du kan hantera de ansikten som identifieras och personer som har identifierats i videor som du indexerar genom att redigera och ta bort ansikten.

Om du tar bort ett ansikte tas en speciell ansikte bort från videoklippets insikter.

Redigera ett ansikte, byter namn på en ansikte som identifieras och eventuellt identifieras i videon. När du redigerar en ansikte i videon sparas namnet som en person post i den person modell som har tilldelats videon under uppladdningen och indexeringen.

Om du inte tilldelar en person modell till videon under överföringen sparas din redigering i kontots standard modell för personer.

### <a name="edit-a-face"></a>Redigera ett ansikte


> [!NOTE]
> Om en person modell har två eller flera olika personer med samma namn kommer du inte att kunna tagga det namnet i videor som använder den person modellen. Du kommer bara att kunna göra ändringar i personer som delar namnet på fliken personer på sidan anpassning av innehålls modell i Video Indexer. Därför rekommenderar vi att du ger unika namn till varje person i din person modell.

1. Bläddra till Video Indexer webbplats och logga in.
1. Sök efter en video som du vill visa och redigera i ditt konto.
1. Om du vill redigera en ansikte i videon går du till fliken insikter och klickar på Penn ikonen i det övre högra hörnet i fönstret.

    ![Redigera en ansikte i videon](./media/customize-face-model/edit-face.png)
1. Klicka på någon av de identifierade ansikten och ändra deras namn från "okänd #X" (eller det namn som tidigare tilldelats till FACET). 
1. När du har skrivit det nya namnet klickar du på kryss ikonen bredvid det nya namnet. Detta sparar det nya namnet och identifierar och namnger alla förekomster av den här ytan i dina andra aktuella videor och i de kommande videor som du överför. Det kan ta lite tid att identifiera ansikte i dina andra aktuella videor, eftersom det är en batch-process.

Om du namnger en ansikte med namnet på en befintlig person i den person modell som videon använder, så sammanfogas identifierade ansikts bilder från den här videon med det som redan finns i modellen. Om du namnger en ansikte med ett helt nytt namn, skapas en ny person post i den person modell som videon använder. 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>Ta bort ett ansikte

Om du vill ta bort ett identifierat ansikte i videon går du till fönstret insikter och klickar på Penn ikonen i det övre högra hörnet i fönstret. Klicka på alternativet ta bort under namnet på FACET. Detta tar bort den identifierade ytan från videon. Personens ansikte kommer fortfarande att identifieras i de andra videor där den visas, men du kan ta bort ansikte från dessa videor även när de har indexerats. Personen, om den hade namngetts, fortsätter även att finnas i den person modell som användes för att indexera videon som du tog bort från, såvida du inte specifikt tar bort personen från person modellen.

![Ta bort en ansikte i videon](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>Nästa steg

[Anpassa person modellen med hjälp av API: er](customize-person-model-with-api.md)
