---
title: Måttlig bilder med anpassade listor och API-konsolen – Content Moderator
titlesuffix: Azure Content Moderator
description: Du kan använda listan Management API i Azure Content Moderator för att skapa anpassade listor över avbildningar.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7efa5114a903ba88010ec44f2f1038331df62948
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097988"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Måttlig med anpassade bildlistor i API-konsol

Du använder den [listan Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) i Azure Content Moderator för att skapa anpassade listor över avbildningar. Använda anpassade listor med avbildningar med Image Moderering API. Bildmoderering åtgärden utvärderar din avbildning. Om du skapar anpassade listor, jämför åtgärden även den till avbildningar i dina anpassade listor. Du kan använda anpassade listor för att blockera eller tillåta avbildningen.

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

Du kan använda listan Management-API för att utföra följande uppgifter:

- Skapa en lista.
- Lägga till bilder i en lista.
- Skärmbilder mot avbildningarna i en lista.
- Ta bort avbildningar från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

## <a name="use-the-api-console"></a>Använd API-konsol
Innan du kan testa API: et i online-konsolen, måste din prenumerationsnyckel. Den finns på den **inställningar** fliken den **Ocp-Apim-Subscription-Key** box. Mer information finns i [Översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera search-index

När du gör ändringar i en bildlista, måste du uppdatera dess index för ändringar som ska ingå i framtida skanningar. Det här steget liknar hur en sökmotor på skrivbordet (om aktiverat) eller en sökmotor för kontinuerligt uppdaterar dess index med nya filer eller sidor.

1. I den [bild listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), i den vänstra menyn väljer du **bild som visar**, och välj sedan **uppdatera sökindex**.

   Den **bild som visar - uppdatera sökindex** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 
 
    ![Bildlistor - uppdatering sökindex sidan val](images/test-drive-region.png)

    Den **bild som visar - uppdatera sökindex** API-konsolen öppnas.

3. I den **listId** anger list-ID. Ange din prenumerationsnyckel och välj sedan **skicka**.

   ![Bildlistor - uppdatering sökindex konsolen svar innehållsrutan](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Skapa en bildlista

1. Gå till den [bild listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Den **bild som visar - skapa** öppnas. 

3. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.

   ![Bild listor – skapa val av region](images/test-drive-region.png)

   Den **bild som visar - skapa** API-konsolen öppnas.
 
4. I den **Ocp-Apim-Subscription-Key** anger din prenumerationsnyckel.

5. I den **Begärandetext** anger värden för **namn** (till exempel MyList) och **beskrivning**.

   ![Bild listor – skapa konsolen brödtext namn och beskrivning](images/try-terms-list-create-1.png)

6. Använda nyckel / värde-par platshållare för att tilldela mer beskrivande metadata till din lista.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Lägg till metadata för lista som nyckel / värde-par och inte själva bilderna.
 
7. Välj **Skicka**. Listan skapas. Obs den **ID** värde som är associerat med den nya listan. Du behöver detta ID för andra hanteringsfunktioner för bild-listan.

   ![Bild listor – skapa konsolen svar innehåll rutan visar list-ID](images/try-terms-list-create-2.png)
 
8. Lägg sedan till bilder MyList. I den vänstra menyn väljer du **bild**, och välj sedan **Lägg till bild**.

   Den **bild – Lägg till bild** öppnas. 

9. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.

   ![Bild – Lägg till bild sidan val](images/test-drive-region.png)

   Den **bild – Lägg till bild** API-konsolen öppnas.
 
10. I den **listId** , ange list-ID som du skapade och sedan ange URL: en för den avbildning som du vill lägga till. Ange din prenumerationsnyckel och välj sedan **skicka**.

11. Om du vill kontrollera att avbildningen har lagts till i listan, i den vänstra menyn, Välj **bild**, och välj sedan **hämta alla bild ID: N**.

    Den **bild - hämta alla bild ID: N** API-konsolen öppnas.
  
12. I den **listId** , ange list-ID och sedan ange din prenumerationsnyckel. Välj **Skicka**.

    ![Bild - Get alla bild ID: N konsolen svar innehållsrutan visar en lista över avbildningar som du angav](images/try-image-list-create-11.png)
 
10. Lägg till några fler avbildningar. Nu när du har skapat en anpassad lista med avbildningar kan prova [utvärderar avbildningar](try-image-api.md) med hjälp av listan med anpassade avbildningar. 

## <a name="delete-images-and-lists"></a>Ta bort avbildningar och listor

Det är enkelt att ta bort en avbildning eller en lista. Du kan använda API: et för att utföra följande uppgifter:

- Ta bort en avbildning. (**Bild – ta bort**)
- Ta bort alla avbildningar i en lista utan att ta bort i listan. (**Bild – ta bort alla avbildningar**)
- Ta bort en lista och allt dess innehåll. (**Bildlistor - ta bort**)

Det här exemplet tar bort en enda avbildning:

1. I den [bild listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), i den vänstra menyn väljer du **bild**, och välj sedan **ta bort**. 

   Den **bild – ta bort** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

   ![Bild - ta bort sidan val](images/test-drive-region.png)
 
   Den **bild – ta bort** API-konsolen öppnas.
 
3. I den **listId** anger ID för listan om du vill ta bort en avbildning från.  Detta är antalet returneras i de **bild - hämta alla bild ID: N** konsolen för MyList. Ange sedan den **ImageId** för avbildningen som ska ta bort. 

I vårt exempel list-ID är **58953**, värdet för **ContentSource**. Bild-ID är **59021**, värdet för **ContentIds**.

1. Ange din prenumerationsnyckel och välj sedan **skicka**.

1. Kontrollera att avbildningen har tagits bort genom att använda den **bild - hämta alla bild ID: N** konsolen.
 
## <a name="change-list-information"></a>Ändra listinformation

Du kan redigera namn och beskrivning för en lista och lägga till för metadataobjekt.

1. I den [bild listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), i den vänstra menyn väljer du **bild som visar**, och välj sedan **Uppdateringsdetaljer**. 

   Den **bild som visar - Uppdateringsdetaljer** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.  

    ![Bildlistor - Uppdateringsdetaljer sidan val](images/test-drive-region.png)

    Den **bild som visar - Uppdateringsdetaljer** API-konsolen öppnas.
 
3. I den **listId** , ange list-ID och sedan ange din prenumerationsnyckel.

4. I den **Begärandetext** , gör ändringarna och väljer sedan den **skicka** på sidan.

   ![Bildlistor - Uppdateringsdetaljer konsolen begäran brödtext redigeringar](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller börja med den [bild visar en lista över Snabbstart för .NET](image-lists-quickstart-dotnet.md) att integrera med ditt program.
