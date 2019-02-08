---
title: Moderera text med anpassad termlistor - Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd listan Management-API för att skapa anpassade listor av termer som du använder med Moderering av Text-API.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 733aae0fe7bd11dfb5c41b7c3d15838a76ab5834
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870207"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Måttlig med anpassade termlistor i API-konsol

Den standardmässiga globala listan med termer i Azure Content Moderator räcker för de flesta modereringsbehov. Du kan dock behöva kontrollera termer som är specifika för din organisation. Till exempel vill du kanske tagga namn på konkurrenter för vidare granskning. 

Använd den [listan Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) att skapa anpassade listor av termer som du använder med Moderering av Text-API. Den **Text - skärmen** åtgärden söker igenom din text med avseende på svordomar och jämför också texten mot anpassade och delade svartlistor.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

Du kan använda listan Management-API för att utföra följande uppgifter:
- Skapa en lista.
- Lägga till termer i en lista.
- Kontrollera termer mot termer i en lista.
- Ta bort termer från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

## <a name="use-the-api-console"></a>Använd API-konsol

Innan du kan testa API: et i online-konsolen, måste din prenumerationsnyckel. Den här nyckeln finns på den **inställningar** fliken den **Ocp-Apim-Subscription-Key** box. Mer information finns i [Översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera search-index

När du gör ändringar i en termlista, måste du uppdatera dess index för ändringar som ska ingå i framtida skanningar. Det här steget liknar hur en sökmotor på skrivbordet (om aktiverat) eller en sökmotor för kontinuerligt uppdaterar dess index med nya filer eller sidor.

1.  I den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), i den vänstra menyn väljer du **termen visar en lista över**, och välj sedan **uppdatera sökindex**. 

  Den **termen - uppdatera Search Index över** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Termlistor - uppdatering sökindex sidan val](images/test-drive-region.png)

  Den **termen - uppdatera Search Index över** API-konsolen öppnas.

3.  I den **listId** anger list-ID. Ange din prenumerationsnyckel och välj sedan **skicka**.

  ![Termlistor API - uppdatering sökindex konsolen svar innehållsrutan](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Skapa en termlista
1.  Gå till den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  Den **termen visar - skapa** öppnas.

2.  För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Långsiktig listor – skapa val av region](images/test-drive-region.png)

  Den **termen visar - skapa** API-konsolen öppnas.
 
3.  I den **Ocp-Apim-Subscription-Key** anger din prenumerationsnyckel.

4.  I den **Begärandetext** anger värden för **namn** (till exempel MyList) och **beskrivning**.

  ![Långsiktig listor – skapa konsolen brödtext namn och beskrivning](images/try-terms-list-create-1.png)

5.  Använda nyckel / värde-par platshållare för att tilldela mer beskrivande metadata till din lista.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Lägg till metadata för lista som nyckel / värde-par och inte faktiska villkor.
 
6.  Välj **Skicka**. Listan skapas. Obs den **ID** värde som är associerat med den nya listan. Du behöver detta ID för andra hanteringsfunktioner för termen lista.

  ![Långsiktig listor – skapa konsolen svar innehåll rutan visar list-ID](images/try-terms-list-create-2.png)
 
7.  Lägg till villkor i MyList. I den vänstra menyn under **termen**väljer **Lägg till Term**. 

  Den **Term – Lägg till Term** öppnas. 

8.  För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Långsiktig – lägga till termen sidan val](images/test-drive-region.png)

  Den **Term – Lägg till Term** API-konsolen öppnas.
 
9.  I den **listId** , ange list-ID som du skapade och väljer ett värde för **språk**. Ange din prenumerationsnyckel och välj sedan **skicka**.

  ![Långsiktig – lägga till termen konsolen frågeparametrar](images/try-terms-list-create-3.png)
 
10. Om du vill kontrollera att termen har lagts till i listan, i den vänstra menyn, Välj **termen**, och välj sedan **hämta alla villkor**. 

  Den **termen - hämta alla villkor** API-konsolen öppnas.

11. I den **listId** , ange list-ID och sedan ange din prenumerationsnyckel. Välj **Skicka**.

12. I den **svarsinnehåll** kontrollerar du de villkor som du har angett.

  ![Termen - Get alla villkor konsolen svar innehållsrutan listor termer som du angav](images/try-terms-list-create-4.png)
 
13. Lägg till några flera villkor. Nu när du har skapat en anpassad lista med termer [genomsöka text](try-text-api.md) med hjälp av anpassade termen-listan. 

## <a name="delete-terms-and-lists"></a>Ta bort termer och listor

Det är enkelt att ta bort en term eller en lista. Du kan använda API: et för att utföra följande uppgifter:

- Ta bort en term. (**Term – ta bort**)
- Ta bort alla termer i en lista utan att ta bort listan. (**Term – ta bort alla villkor**)
- Ta bort en lista och allt dess innehåll. (**Termlistor - ta bort**)

Det här exemplet tar bort en enskild term.

1.  I den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), i den vänstra menyn väljer du **termen**, och välj sedan **ta bort**. 

  Den **Term – ta bort** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Termen - ta bort sidan val](images/test-drive-region.png)

  Den **Term – ta bort** API-konsolen öppnas.
  
3.  I den **listId** anger ID för listan som du vill ta bort en term från. Detta ID är antalet (i vårt exempel **122**) som returneras i de **termen visas – hämta information** konsolen för MyList. Ange perioden och välj ett språk.
 
  ![Termen - ta bort konsolen frågeparametrar](images/try-terms-list-delete-1.png)

4.  Ange din prenumerationsnyckel och välj sedan **skicka**.

5.  Kontrollera att termen har tagits bort genom att använda den **termen visar - hämta alla** konsolen.

  ![Långsiktig listor – hämta alla konsolen svar innehåll rutan visar att termen tas bort](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Ändra listinformation

Du kan redigera namn och beskrivning för en lista och lägga till för metadataobjekt.

1.  I den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), i den vänstra menyn väljer du **termen visar en lista över**, och välj sedan **Uppdateringsdetaljer**. 

  Den **termen visar - Uppdateringsdetaljer** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Termlistor - Uppdateringsdetaljer sidan val](images/test-drive-region.png)

  Den **termen visar - Uppdateringsdetaljer** API-konsolen öppnas.

3.  I den **listId** , ange list-ID och sedan ange din prenumerationsnyckel.

4.  I den **Begärandetext** , gör ändringarna och sedan väljer **skicka**.

  ![Termlistor - Uppdateringsdetaljer konsolen begäran brödtext redigeringar](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller börja med den [termen visar Snabbstart för .NET](term-lists-quickstart-dotnet.md) att integrera med ditt program.
