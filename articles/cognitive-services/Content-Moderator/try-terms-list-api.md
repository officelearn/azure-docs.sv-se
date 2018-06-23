---
title: Måttlig text med anpassade termen listor i Azure Content kontrollant | Microsoft Docs
description: Testkör anpassade termen listor i innehåll kontrollant API-konsolen.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 2542e4590781879408aafe8d072eceef157e02c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351816"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Måttlig med anpassade termen listor i API-konsolen

Globala listan över villkoren i Azure innehåll kontrollant är tillräcklig för de flesta innehåll avbrottsmoderering behov. Du kan dock behöva för termer som är specifika för din organisation. Du kanske exempelvis vill konkurrenter taggnamn för vidare undersökning. 

Använd den [listan Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) att skapa anpassade listor över de uttryck som ska användas med Text Avbrottsmoderering API. Den **Text - skärmen** åtgärden söker igenom texten bort olämpligt material och jämför också text mot anpassade och delade blacklists.

> [!NOTE]
> Det finns en övre gräns för **5 termen visar** med varje lista till **inte överstiga 10 000 villkoren**.
>

Du kan använda listan Management API för att göra följande:
- Skapa en lista.
- Lägga till villkor i en lista.
- Skärmen villkor mot villkoren i en lista.
- Ta bort villkor från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringarna i listan ingår i en ny genomsökning.

## <a name="use-the-api-console"></a>Använda API-konsolen

Innan du kan testa API i konsolen online måste din nyckel för prenumerationen. Den här nyckeln finns på den **inställningar** fliken den **Ocp-Apim-prenumeration-nyckeln** rutan. Mer information finns i [översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera sökindex

När du ändrar en term lista, måste du uppdatera dess index för att ändringarna ska ingå i framtida skanningar. Det här steget liknar hur en sökmotor på skrivbordet (om aktiverat) eller en sökmotor kontinuerligt uppdaterar dess index för att inkludera nya filer eller sidor.

1.  I den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), i den vänstra menyn, Välj **termen visar**, och välj sedan **uppdatera sökindex**. 

  Den **termen - uppdatera Search Index över** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Termen listor - uppdatering sökindex val av region](images/test-drive-region.png)

  Den **termen - uppdatera Search Index över** API-konsolen öppnas.

3.  I den **listId** ange list-ID. Ange nyckel för din prenumeration och välj sedan **skicka**.

  ![Termen listor API - konsolen uppdatera sökindex svar innehållsrutan](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Skapa en term-lista
1.  Gå till den [termen listan Management API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

  Den **termen visar - skapa** öppnas.

2.  För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Term listor – skapa val av region](images/test-drive-region.png)

  Den **termen visar - skapa** API-konsolen öppnas.
 
3.  I den **Ocp-Apim-prenumeration-nyckeln** ange din prenumeration nyckel.

4.  I den **Begärandetext** ange värden för **namn** (till exempel MyList) och **beskrivning**.

  ![Term listor – skapa konsolen begäran brödtext namn och beskrivning](images/try-terms-list-create-1.png)

5.  Använd nyckel / värde-par platshållare för att tilldela mer beskrivande metadata i listan.

        {
           "Name": "MyExclusionList",
           "Description": "MyListDescription",
           "Metadata": 
           {
              "Category": "Competitors",
              "Type": "Exclude"
           }
        }

  Lägg till listan metadata som nyckel / värde-par och inte faktiska villkor.
 
6.  Välj **Skicka**. Din lista skapas. Observera den **ID** värde som är associerat med den nya listan. Du behöver detta ID för andra termen listan hanteringsfunktioner.

  ![Term listor – skapa konsolen svar innehåll visar list-ID](images/try-terms-list-create-2.png)
 
7.  Lägg till villkor i MyList. I den vänstra menyn under **termen**väljer **Lägg till Term**. 

  Den **Term - Lägg till Term** öppnas. 

8.  För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Term – Lägg till termen val av region](images/test-drive-region.png)

  Den **Term - Lägg till Term** API-konsolen öppnas.
 
9.  I den **listId** , ange lista-ID som du skapade och välj ett värde för **språk**. Ange nyckel för din prenumeration och välj sedan **skicka**.

  ![Term – Lägg till termen konsolen frågeparametrar](images/try-terms-list-create-3.png)
 
10. Om du vill verifiera att termen har lagts till i listan i den vänstra menyn, Välj **termen**, och välj sedan **hämta alla villkor**. 

  Den **termen - hämta alla villkor** API-konsolen öppnas.

11. I den **listId** , ange list-ID och ange din prenumeration nyckel. Välj **Skicka**.

12. I den **svar innehåll** kontrollera de villkor du angett.

  ![Termen - Get alla villkor konsolen svar innehåll Listar de villkor som du har angett](images/try-terms-list-create-4.png)
 
13. Lägga till några fler termer. Nu när du har skapat en egen lista över villkor försök [genomsökning text](try-text-api.md) med hjälp av anpassade termen-listan. 

## <a name="delete-terms-and-lists"></a>Ta bort villkor och listor

Det är enkelt att ta bort en term eller en lista. Du kan använda API: et för att göra följande:

- Ta bort en term. (**Term – ta bort**)
- Ta bort alla villkor i en lista utan att ta bort i listan. (**Term - ta bort alla villkor**)
- Ta bort en lista och dess innehåll. (**Termen listor - ta bort**)

Det här exemplet tar bort en enskild term.

1.  I den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), i den vänstra menyn, Välj **termen**, och välj sedan **ta bort**. 

  Den **Term – ta bort** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Termen - ta bort sidan val](images/test-drive-region.png)

  Den **Term – ta bort** API-konsolen öppnas.
  
3.  I den **listId** Ange ID för listan som du vill ta bort en term från. Detta ID är antalet (i vårt exempel **122**) som returneras av **termen visas - hämta information** konsol för MyList. Ange villkoret och välj ett språk.
 
  ![Termen - ta bort konsolen frågeparametrar](images/try-terms-list-delete-1.png)

4.  Ange nyckel för din prenumeration och välj sedan **skicka**.

5.  Använd för att kontrollera att termen har tagits bort av **termen visar - hämta alla** konsolen.

  ![Term listor - hämta alla konsolen svar innehåll visar att termen har tagits bort](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Ändra listinformation

Du kan redigera namn och beskrivning för en lista och Lägg till för metadataobjekt.

1.  I den [termen listan Management API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f), i den vänstra menyn, Välj **termen visar**, och välj sedan **Uppdateringsdetaljer**. 

  Den **termen visar - Uppdateringsdetaljer** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Termen listor - information om val av region](images/test-drive-region.png)

  Den **termen visar - Uppdateringsdetaljer** API-konsolen öppnas.

3.  I den **listId** , ange list-ID och ange din prenumeration nyckel.

4.  I den **Begärandetext** , gör ändringarna och välj sedan **skicka**.

  ![Termen listor - information om konsolen begäran brödtext redigeringar](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller starta med den [termen visar .NET quickstart](term-lists-quickstart-dotnet.md) att integrera med ditt program.
