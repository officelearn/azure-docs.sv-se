---
title: Moderera text med anpassade termlistor - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd API:et för listhantering för att skapa anpassade listor med termer som ska användas med API:et för textmoderering.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 16cfb6c15a4d17ff3fb4f7f41f59f9f80af1e9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75382131"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Moderera med anpassade termlistor i API-konsolen

Den standardmässiga globala listan med termer i Azure Content Moderator räcker för de flesta modereringsbehov. Du kan dock behöva kontrollera termer som är specifika för din organisation. Till exempel vill du kanske tagga namn på konkurrenter för vidare granskning. 

Använd [API:et för listhantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) för att skapa anpassade listor med termer som ska användas med API:et för textmoderering. Åtgärden **Text - Skärm** söker igenom texten efter svordomar och jämför även text med anpassade och delade blockeringslistor.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

Du kan använda API:et för listhantering för att utföra följande uppgifter:
- Skapa en lista.
- Lägga till termer i en lista.
- Kontrollera termer mot termer i en lista.
- Ta bort termer från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

## <a name="use-the-api-console"></a>Använda API-konsolen

Innan du kan provköra API:et i onlinekonsolen behöver du din prenumerationsnyckel. Den här nyckeln finns på fliken **Inställningar** i rutan **Ocp-Apim-Subscription-Key.** Mer information finns i [Översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera sökindex

När du har gjort ändringar i en termlista måste du uppdatera indexet för att ändringar ska inkluderas i framtida genomsökningar. Det här steget liknar hur en sökmotor på skrivbordet (om den är aktiverad) eller en sökmotor kontinuerligt uppdaterar sitt index för att inkludera nya filer eller sidor.

1. I [API-referensen för termlisthantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)väljer du **Termlistor**på den vänstra menyn och väljer sedan **Uppdatera sökindex**. 

   Sidan **Termlistor - Uppdatera sökindex** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Termlistor – val av uppdateringsindex för sidområde](images/test-drive-region.png)

   API-konsolen **Termlistor - Uppdatera sökindex** öppnas.

3. Ange list-ID i rutan **ListId.** Ange din prenumerationsnyckel och välj sedan **Skicka**.

   ![API för termlistor – rutan Uppdatera svarsinnehåll för sökindexkonsolen](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Skapa en termlista
1. Gå till [API-referensen för termlisthantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Sidan **Termlistor - Skapa** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Termlistor - Skapa val av sidområde](images/test-drive-region.png)

   **Termlistorna - Skapa** API-konsolen öppnas.
 
3. Ange din prenumerationsnyckel i rutan **Ocp-Apim-Subscription-Key.**

4. I **rutan Begäran** anger du värden för **Namn** (till exempel MyList) och **Beskrivning**.

   ![Termlistor - Skapa brödnamn och beskrivning av begäran om konsolbegäran](images/try-terms-list-create-1.png)

5. Använd platshållare för nyckelvärdespar för att tilldela listan mer beskrivande metadata.

       {
          "Name": "MyExclusionList",
          "Description": "MyListDescription",
          "Metadata": 
          {
             "Category": "Competitors",
             "Type": "Exclude"
          }
       }

   Lägg till listmetadata som nyckelvärdespar och inte faktiska termer.
 
6. Välj **Skicka**. Listan skapas. Observera **ID-värdet** som är associerat med den nya listan. Du behöver det här ID:t för andra termlisthanteringsfunktioner.

   ![Termlistor - Innehållsrutan Skapa konsolsvar visar list-ID:t](images/try-terms-list-create-2.png)
 
7. Lägg till termer i MyList. Välj **Lägg till term**under **Term**på menyn till vänster . 

   Sidan **Term - Lägg till term** öppnas. 

8. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Term - Val av sidområde för term](images/test-drive-region.png)

   Term **- Lägg till term-API-konsolen** öppnas.
 
9. I rutan **listId** anger du det list-ID som du har skapat och väljer ett språkvärde **.** Ange din prenumerationsnyckel och välj sedan **Skicka**.

   ![Term - Lägga till frågeparametrar för termkonsol](images/try-terms-list-create-3.png)
 
10. Om du vill kontrollera att termen har lagts till i listan väljer du **Term**i den vänstra menyn och väljer sedan **Hämta alla villkor**. 

    **API-konsolen Term - Get All Terms** öppnas.

11. I rutan **ListId** anger du list-ID:t och anger sedan din prenumerationsnyckel. Välj **Skicka**.

12. Kontrollera de termer du angav i rutan **Svarsinnehåll.**

    ![Term - Hämta alla villkor konsol Svar innehåll rutan listar de termer som du angav](images/try-terms-list-create-4.png)
 
13. Lägg till några fler termer. Nu när du har skapat en anpassad lista med termer kan du prova [att skanna lite text](try-text-api.md) med hjälp av den anpassade termlistan. 

## <a name="delete-terms-and-lists"></a>Ta bort termer och listor

Det är enkelt att ta bort en term eller en lista. Du använder API:et för att utföra följande uppgifter:

- Ta bort en term. (**Term - Ta bort**)
- Ta bort alla termer i en lista utan att ta bort listan. (**Term - Ta bort alla villkor)**
- Ta bort en lista och allt dess innehåll. (**Term Listor - Ta bort**)

I det här exemplet tas en enda term bort.

1. I [API-referensen för termlisthantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)väljer du **Term**på den vänstra menyn och väljer sedan **Ta bort**. 

   **Termen - Ta bort** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Term - Ta bort markering av sidregion](images/test-drive-region.png)

   Term **- Delete** API-konsolen öppnas.
  
3. I rutan **ListId** anger du ID:t för listan som du vill ta bort en term från. Detta ID är det nummer (i vårt exempel **122**) som returneras i **termlistor - Få informationskonsol** för MyList. Ange termen och välj ett språk.
 
   ![Term - Ta bort konsolfrågeparametrar](images/try-terms-list-delete-1.png)

4. Ange din prenumerationsnyckel och välj sedan **Skicka**.

5. Om du vill kontrollera att termen har tagits bort använder du konsolen **Termlistor - Hämta alla.**

   ![Termlistor - Rutan Hämta allt konsolsvarsinnehåll visar att termen tas bort](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Ändra listinformation

Du kan redigera en listas namn och beskrivning och lägga till metadataobjekt.

1. I [API-referensen för termlisthantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)väljer du **Termlistor**på den vänstra menyn och väljer sedan **Uppdatera information**. 

   Sidan **Termlistor - Uppdatera information** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Termlistor - Val av informationsinformationssida](images/test-drive-region.png)

   API-konsolen **Term lists - Update Details** öppnas.

3. I rutan **ListId** anger du list-ID:t och anger sedan din prenumerationsnyckel.

4. Gör ändringarna i rutan **Begär** och välj sedan **Skicka**.

   ![Termlistor – Uppdatera information om redigeringar av begäranden i konsolen](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller börja med [termlistorna .NET-snabbstart](term-lists-quickstart-dotnet.md) för att integrera med ditt program.
