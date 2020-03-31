---
title: Spara sökningar och fästa datatillgångar i Azure Data Catalog
description: How-to artikel belyser funktioner i Azure Data Catalog för att spara datakällor och datatillgångar för senare användning.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: a8545939fd027989519e0f7a161f750ec676749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976804"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Spara sökningar och fästa datatillgångar i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog innehåller funktioner för identifiering av datakällor. Du kan snabbt söka och filtrera katalogen för att hitta datakällor och förstå deras avsedda syfte, vilket gör det lättare att hitta rätt data för jobbet till hands.

Men vad händer om du behöver regelbundet arbeta med samma data? Och vad händer om du och andra användare regelbundet bidrar med dina kunskaper till samma datakällor i katalogen? I dessa situationer, att behöva upprepade gånger utfärda samma sökningar kan vara ineffektiva. Det är här sparade sök- och fästa datatillgångar kan hjälpa till.

## <a name="saved-searches"></a>Sparade sökningar
En sparad sökning i Data Catalog är en återanvändbar, per användare sökdefinition. Du kan definiera en sökning, inklusive söktermer, taggar och andra filter, och sedan spara den. Du kan köra den sparade sökdefinitionen igen senare för att returnera alla datatillgångar som matchar dess sökvillkor.

### <a name="create-a-saved-search"></a>Skapa en sparad sökning
Så här skapar du en sparad sökning:
1. Klicka på **Spara**i fönstret **Aktuell sökning** i Azure Data Catalog-portalen. 

    ![Länken Spara aktuella sökinställningar](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Ange de sökvillkor som du vill återanvända och klicka sedan på **Spara**.

    ![Sparat söknamn för aktuella sökinställningar](./media/data-catalog-how-to-save-pin/02-name.png)

3. När du uppmanas att ange ett namn för den sparade sökningen. Välj ett namn som är meningsfullt och som beskriver de datatillgångar som ska returneras av sökningen.

### <a name="manage-saved-searches"></a>Hantera sparade sökningar
När du har sparat en eller flera sökningar visas alternativet **Sparade sökningar** under rutan **Aktuell sökning.** När listan expanderas visas alla sparade sökningar.

 ![Datakatalog - Lista över sparade sökningar](./media/data-catalog-how-to-save-pin/03-list.png)

Gör något av följande:

* Om du vill köra en sökning väljer du en sparad sökning i listan.

* Om du vill visa en lista över hanteringsalternativ för en sparad sökning klickar du på nedpilen bredvid söknamnet.

    ![Alternativ för att hantera sparade sökningar](./media/data-catalog-how-to-save-pin/04-managing.png)

* Om du vill ange ett nytt namn för den sparade sökningen väljer du **Byt namn**. Sökdefinitionen ändras inte.

* Om du vill ta bort den sparade sökningen från listan väljer du **Ta bort**och bekräftar sedan borttagningen.

* Om du vill markera den sparade sökningen som standardsökning väljer du **Spara som standard**. Om du utför en "tom" sökning från startsidan för Azure Data Catalog körs standardsökningen. Dessutom visas sökningen som är markerad som standardsökning högst upp i listan **Sparade sökningar.**

### <a name="organizational-saved-searches"></a>Sparade sökningar i organisationen
Alla användare i organisationen kan spara sökningar för eget bruk. Datakatalogadministratörer kan också spara sökningar för alla användare inom organisationen. När administratörer sparar en sökning visas en **resurs inom företagsalternativet.** Om du väljer det här alternativet delar du den sparade sökningen efter alla användare i organisationen.

 ![Datakatalog - Sparade sökningar i Organisationen](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Fästa datatillgångar
Med sparade sökningar kan du spara och återanvända sökdefinitioner. De datatillgångar som returneras av sökningarna kan ändras med tiden när innehållet i katalogen ändras. När du fäster datatillgångar kan du uttryckligen identifiera specifika datatillgångar för att göra dem lättare att komma åt utan att behöva använda en sökning.

Det är enkelt att fästa en datatillgång. Om du vill lägga till datatillgången i listan fäst klickar du bara på **pin-ikonen.** Ikonen visas i hörnet av tillgångspanelen i panelvyn och i kolumnen längst till vänster i listvyn i Azure Data Catalog-portalen.

![Datakatalog - Ikonen för pin för datatillgång](./media/data-catalog-how-to-save-pin/05-pinning.png)

Det är lika enkelt att ta bort en datatillgång. Klicka bara på **ikonen ta bort för** att växla inställningen för den valda tillgången.

![Datakatalog - Ikonen för ta bort datatillgång](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Avsnittet Mina tillgångar
Startsidan för datakatalogportalen innehåller avsnittet **Mina tillgångar** som visar tillgångar av intresse för den aktuella användaren. Det här avsnittet innehåller både fästa tillgångar och sparade sökningar.

![Avsnittet Mina tillgångar på hemsidan](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Sammanfattning
Azure Data Catalog innehåller funktioner som gör det enklare att identifiera de datakällor du behöver, så att du och andra medlemmar i organisationen kan ägna mindre tid åt att leta efter data och mer tid att arbeta med dem. Sparade sökningar och fästa datatillgångar bygger på dessa kärnfunktioner så att användarna enkelt kan identifiera datakällor som de arbetar med upprepade gånger.
