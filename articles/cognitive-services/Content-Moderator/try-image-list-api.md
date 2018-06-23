---
title: Måttlig bilder med hjälp av anpassade listor i Azure Content kontrollant | Microsoft Docs
description: Testkör anpassad avbildning listor i innehåll kontrollant API-konsolen.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2d714f017be16d978ffbb877a2b7e78e1caf9169
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351825"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Måttlig med anpassad avbildning listor i API-konsolen

Du använder den [listan Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) i Azure Content kontrollant att skapa anpassade listor med bilder. Använda anpassade listor med bilder med bild Avbrottsmoderering API. Bild avbrottsmoderering åtgärden utvärderar avbildningen. Om du skapar anpassade listor jämförs åtgärden även med bilder i din anpassade listor. Du kan använda anpassade listor för att blockera eller tillåta avbildningen.

> [!NOTE]
> Det finns en övre gräns för **5 bild visar** med varje lista till **inte överstiga 10 000 bilder**.
>

Du kan använda listan Management API för att göra följande:

- Skapa en lista.
- Lägga till bilder i en lista.
- Skärmbilder mot bilder i en lista.
- Ta bort bilder från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringarna i listan ingår i en ny genomsökning.

## <a name="use-the-api-console"></a>Använda API-konsolen
Innan du kan testa API i konsolen online måste din nyckel för prenumerationen. Den finns på den **inställningar** fliken den **Ocp-Apim-prenumeration-nyckeln** rutan. Mer information finns i [översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera sökindex

När du gör ändringar i en bildlista måste du uppdatera dess index för att ändringarna ska ingå i framtida skanningar. Det här steget liknar hur en sökmotor på skrivbordet (om aktiverat) eller en sökmotor kontinuerligt uppdaterar dess index för att inkludera nya filer eller sidor.

1.  I den [avbildningen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), i den vänstra menyn, Välj **bild som visar**, och välj sedan **uppdatera sökindex**.

  Den **bild som visar - uppdatera sökindex** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 
 
    ![Bilden visar - uppdatering sökindex val av region](images/test-drive-region.png)

    Den **bild som visar - uppdatera sökindex** API-konsolen öppnas.

3.  I den **listId** ange list-ID. Ange nyckel för din prenumeration och välj sedan **skicka**.

  ![Bilden visar - uppdatering sökindex konsolen svar innehållsrutan](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Skapa en bildlista

1.  Gå till den [avbildningen listan Management API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

  Den **bild som visar - skapa** öppnas. 

3.  För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.

    ![Bild visar – skapa val av region](images/test-drive-region.png)

    Den **bild som visar - skapa** API-konsolen öppnas.
 
4.  I den **Ocp-Apim-prenumeration-nyckeln** ange din prenumeration nyckel.

5.  I den **Begärandetext** ange värden för **namn** (till exempel MyList) och **beskrivning**.

  ![Bild visar – skapa konsolen begäran brödtext namn och beskrivning](images/try-terms-list-create-1.png)

6.  Använd nyckel / värde-par platshållare för att tilldela mer beskrivande metadata i listan.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
             "Category": "Competitors",
             "Type": "Exclude"
           }
        }

  Lägg till listan metadata som nyckel / värde-par och inte själva bilderna.
 
7.  Välj **Skicka**. Din lista skapas. Observera den **ID** värde som är associerat med den nya listan. Du behöver detta ID för andra hanteringsfunktioner för bild-listan.

  ![Bild listor – skapa konsolen svar innehåll visar list-ID](images/try-terms-list-create-2.png)
 
8.  Lägg sedan till bilder till MyList. Välj i den vänstra menyn **bild**, och välj sedan **lägga till bilden**.

  Den **Image - lägga till bilden** öppnas. 

9. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.

  ![Bild – Lägg till avbildning val av region](images/test-drive-region.png)

  Den **Image - lägga till bilden** API-konsolen öppnas.
 
10. I den **listId** , ange lista-ID som du skapade och ange URL till den bild som du vill lägga till. Ange nyckel för din prenumeration och välj sedan **skicka**.

11. Om du vill kontrollera att avbildningen har lagts till i listan i den vänstra menyn, Välj **bild**, och välj sedan **hämta alla Image-ID: N**.

  Den **Image - hämta alla Image-ID: N** API-konsolen öppnas.
  
12. I den **listId** , ange list-ID och ange din prenumeration nyckel. Välj **Skicka**.

  ![Bild - Get alla avbildningen ID: N konsolen svar innehåll i rutan visas de bilder som du har angett](images/try-image-list-create-11.png)
 
10. Lägga till några fler bilder. Nu när du har skapat en egen lista över bilder, försök [utvärderar bilder](try-image-api.md) med hjälp av listan anpassad avbildning. 

## <a name="delete-images-and-lists"></a>Ta bort bilder och listor

Det är enkelt att ta bort en bild eller en lista. Du kan använda API: et för att göra följande:

- Ta bort en avbildning. (**Bild – ta bort**)
- Ta bort alla filer i en lista utan att ta bort i listan. (**Bild – ta bort alla avbildningar**)
- Ta bort en lista och dess innehåll. (**Bilden visar - ta bort**)

Det här exemplet tar bort en bild:

1. I den [avbildningen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), i den vänstra menyn, Välj **bild**, och välj sedan **ta bort**. 

  Den **bild – ta bort** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Bild - ta bort sidan val](images/test-drive-region.png)
 
  Den **bild – ta bort** API-konsolen öppnas.
 
3.  I den **listId** Ange ID i listan om du vill ta bort en bild från.  Detta är antalet returneras i den **Image - hämta alla Image-ID: N** MyList-konsolen. Ange sedan den **ImageId** för avbildningen som ska ta bort. 

I vårt exempel list-ID är **58953**, värdet för **ContentSource**. Bild-ID är **59021**, värdet för **ContentIds**.

4.  Ange nyckel för din prenumeration och välj sedan **skicka**.

5.  Använd för att kontrollera att avbildningen har tagits bort av **Image - hämta alla Image-ID: N** konsolen.
 
## <a name="change-list-information"></a>Ändra listinformation

Du kan redigera namn och beskrivning för en lista och Lägg till för metadataobjekt.

1.  I den [avbildningen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672), i den vänstra menyn, Välj **bild som visar**, och välj sedan **Uppdateringsdetaljer**. 

  Den **bild som visar - Uppdateringsdetaljer** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.  

    ![Bilden visar - information om val av region](images/test-drive-region.png)

    Den **bild som visar - Uppdateringsdetaljer** API-konsolen öppnas.
 
3.  I den **listId** , ange list-ID och ange din prenumeration nyckel.

4.  I den **Begärandetext** , gör ändringarna och välj sedan den **skicka** på sidan.

  ![Bilden visar - information om konsolen begäran brödtext redigeringar](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller starta med den [bild som visar .NET quickstart](image-lists-quickstart-dotnet.md) att integrera med ditt program.
