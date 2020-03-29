---
title: Moderera bilder med anpassade listor och API-konsolen - Innehållsmoderator
titleSuffix: Azure Content Moderator
description: Du kan använda API:et för listhantering i Azure Content Moderator för att skapa anpassade listor med avbildningar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 27d9b12d9e1a0237050243c2b5f07edaa8d8857a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757192"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Moderera med anpassade bildlistor i API-konsolen

Du kan använda [API:et för listhantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) i Azure Content Moderator för att skapa anpassade listor med avbildningar. Använd de anpassade listorna med bilder med API:et för bildmoderering. Åtgärden för bildmoderering utvärderar avbildningen. Om du skapar anpassade listor jämför åtgärden den också med bilderna i dina anpassade listor. Du kan använda anpassade listor för att blockera eller tillåta bilden.

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

Du använder API:et för listhantering för att utföra följande uppgifter:

- Skapa en lista.
- Lägg till bilder i en lista.
- Skärmbilder mot bilderna i en lista.
- Ta bort bilder från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

## <a name="use-the-api-console"></a>Använda API-konsolen
Innan du kan provköra API:et i onlinekonsolen behöver du din prenumerationsnyckel. Detta finns på fliken **Inställningar** i rutan **Ocp-Apim-Subscription-Key.** Mer information finns i [Översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera sökindex

När du har gjort ändringar i en bildlista måste du uppdatera indexet för att ändringar ska inkluderas i framtida genomsökningar. Det här steget liknar hur en sökmotor på skrivbordet (om den är aktiverad) eller en sökmotor kontinuerligt uppdaterar sitt index för att inkludera nya filer eller sidor.

1. Välj **Bildlistor**på [API-referensen för bildlistehantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)och välj sedan **Uppdatera sökindex**.

   Sidan **Bildlistor - Uppdatera sökindex** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 
 
    ![Bildlistor – val av sökindex för sidområde](images/test-drive-region.png)

    API-konsolen **Image List - Refresh Search Index** öppnas.

3. Ange list-ID i rutan **ListId.** Ange din prenumerationsnyckel och välj sedan **Skicka**.

   ![Bildlistor – rutan Uppdatera sökindexkonsolens svarsinnehåll](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Skapa en bildlista

1. Gå till [API-referensen för api för bildlistehantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Sidan **Bildlistor - Skapa** öppnas. 

3. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats.

   ![Bildlistor - Val av sidregion](images/test-drive-region.png)

   **Bildlistorna - Skapa** API-konsolen öppnas.
 
4. Ange din prenumerationsnyckel i rutan **Ocp-Apim-Subscription-Key.**

5. I **rutan Begäran** anger du värden för **Namn** (till exempel MyList) och **Beskrivning**.

   ![Bildlistor - Skapa brödnamn och beskrivning av begäran om konsolbegäran](images/try-terms-list-create-1.png)

6. Använd platshållare för nyckelvärdespar för att tilldela listan mer beskrivande metadata.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
            "Category": "Competitors",
            "Type": "Exclude"
          }
       }

   Lägg till listmetadata som nyckelvärdespar och inte de faktiska bilderna.
 
7. Välj **Skicka**. Listan skapas. Observera **ID-värdet** som är associerat med den nya listan. Du behöver det här ID:t för andra funktioner för hantering av bildlistelista.

   ![Bildlistor - Innehållsrutan Skapa konsolsvar visar list-ID](images/try-terms-list-create-2.png)
 
8. Lägg sedan till bilder i MyList. Välj **Bild**på den vänstra menyn och välj sedan **Lägg till bild**.

   Sidan **Bild - Lägg till bild** öppnas. 

9. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats.

   ![Bild - Val av bildsida](images/test-drive-region.png)

   **Api-konsolen Image - Add Image** öppnas.
 
10. I rutan **ListId** anger du det list-ID som du har skapat och anger sedan url:en till den bild som du vill lägga till. Ange din prenumerationsnyckel och välj sedan **Skicka**.

11. Om du vill kontrollera att bilden har lagts till i listan väljer du **Bild**på den vänstra menyn och väljer sedan **Hämta alla bild-ID: n**.

    **Api-konsolen Image - Get All Image Ids** öppnas.
  
12. I rutan **ListId** anger du list-ID:t och anger sedan din prenumerationsnyckel. Välj **Skicka**.

    ![Bild - Hämta alla bild-ID-konsolSvarsinnehållsrutor visar de bilder som du har angett](images/try-image-list-create-11.png)
 
10. Lägg till några fler bilder. Nu när du har skapat en anpassad lista med bilder kan du prova [att utvärdera bilder](try-image-api.md) med hjälp av den anpassade avbildningslistan. 

## <a name="delete-images-and-lists"></a>Ta bort bilder och listor

Det är enkelt att ta bort en bild eller en lista. Du kan använda API:et för att utföra följande uppgifter:

- Ta bort en avbildning. (**Bild - Ta bort**)
- Ta bort alla bilder i en lista utan att ta bort listan. (Bild **- Ta bort alla bilder)**
- Ta bort en lista och allt dess innehåll. (**Bildlistor - Ta bort**)

I det här exemplet tas en enda bild bort:

1. Välj **Bild**i [API-referensen för bildlistehantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)och välj sedan **Ta bort**. 

   Sidan **Bild - Ta bort** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Bild - Ta bort markering av sidregion](images/test-drive-region.png)
 
   **Bild - Ta bort** API-konsolen öppnas.
 
3. I rutan **ListId** anger du ID:et i listan som en bild ska tas bort från.  Detta är numret som returneras i **konsolen Image - Get All Image Ids** för MyList. Ange sedan **bildens ImageId** för att ta bort. 

I vårt exempel är list-ID **58953**, värdet för **ContentSource**. Bild-ID är **59021**, värdet för **ContentIds**.

1. Ange din prenumerationsnyckel och välj sedan **Skicka**.

1. Om du vill kontrollera att bilden har tagits bort använder du konsolen **Bild - Få alla avbildnings-ID:n.**
 
## <a name="change-list-information"></a>Ändra listinformation

Du kan redigera en listas namn och beskrivning och lägga till metadataobjekt.

1. Välj **Bildlistor**på [API-referensen för bildlistehantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)och välj sedan Uppdatera information på den vänstra menyn och välj sedan **Uppdatera information**. 

   Sidan **Bildlistor - Uppdatera information** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats.  

    ![Bildlistor – val av uppdateringsinformationssida](images/test-drive-region.png)

    API-konsolen **Image List - Update Details** öppnas.
 
3. I rutan **ListId** anger du list-ID:t och anger sedan din prenumerationsnyckel.

4. Gör ändringarna i rutan **Begär** och välj sedan knappen **Skicka** på sidan.

   ![Bildlistor – Uppdatera information om att begäran om begäran om begäran om förfrågan](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller börja med [imagelistorna .NET-snabbstart](image-lists-quickstart-dotnet.md) för att integrera med ditt program.
