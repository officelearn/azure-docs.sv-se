---
title: Spara sökningar och Fäst datatillgångar i Azure Data Catalog
description: Artikel markering funktioner i Azure Data Catalog för att spara datakällor och datatillgångar för senare användning.
services: data-catalog
author: steelanddata
ms.author: maroche
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 786d65eaf667ae8ae9dc2c91d3113f5057a98a27
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43053735"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Spara sökningar och Fäst datatillgångar i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog innehåller funktioner för identifiering av datakällor. Du kan snabbt söka och filtrera katalogen för att identifiera datakällor och förstå deras syfte, vilket gör det lättare att hitta rätt data för jobbet till hands.

Men vad händer om du behöver regelbundet arbeta med samma data? Och vad händer om du och andra användare regelbundet bidra med dina kunskaper till samma datakällor i katalogen? I sådana fall är kan att upprepade gånger utfärda samma sökningar vara ineffektiv. Det här är där sparad sökning och Fäst datatillgångar kan hjälpa.

## <a name="saved-searches"></a>Sparade sökningar
En sparad sökning i Data Catalog är en återanvändbar Sökdefinition per användare. Du kan definiera en sökning, inklusive söktermer, taggar och andra filter och spara den. Du kan köra sparade sökningen definitionen senare om du vill returnera alla datatillgångar som matchar sökvillkoren igen.

### <a name="create-a-saved-search"></a>Skapa en sparad sökning
Om du vill skapa en sparad sökning, gör du följande:
1. I Azure Data Catalog-portalen i den **aktuell sökning** fönstret klickar du på **spara**. 

    ![Aktuell sökning spara inställningslänken](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Anger sökvillkor som du vill återanvända och klicka sedan på **spara**.

    ![Aktuella sökinställningar som namn på sparad sökning](./media/data-catalog-how-to-save-pin/02-name.png)

3. När du uppmanas, anger du ett namn för den sparade sökningen. Välj ett beskrivande namn och som beskriver de dataresurser som returneras av sökningen.

### <a name="manage-saved-searches"></a>Hantera sparade sökningar
När du har sparat en eller flera sökningar, en **sparade sökningar** alternativet visas under den **aktuell sökning** box. När listan har expanderats visas alla sparade sökningar.

 ![Listan över sparade sökningar](./media/data-catalog-how-to-save-pin/03-list.png)

Gör något av följande:

* Välj en sparad sökning för att köra en sökning i listan.

* Om du vill visa en lista över alternativ för en sparad sökning, klickar du på nedpilen bredvid namnet.

    ![Alternativ för att hantera sparade sökningar](./media/data-catalog-how-to-save-pin/04-managing.png)

* Om du vill ange ett nytt namn på den sparade sökningen, Välj **Byt namn på**. Sök definitionen ändras inte.

* Om du vill ta bort den sparade sökningen från listan, Välj **ta bort**, och sedan bekräfta borttagningen.

* Om du vill markera den sparade sökningen som standardsökning, Välj **Spara som standard**. Om du gör en ”tom” sökning på startsidan för Azure Data Catalog, utförs standardsökningen. Dessutom sökning som har markerats som standardsökningen visas överst i den **sparade sökningar** lista.

### <a name="organizational-saved-searches"></a>Organisationens sparade sökningar
Alla användare i din organisation kan spara sökningar för eget bruk. Data Catalog-administratörer kan också spara sökningar för alla användare i organisationen. När administratörer spara en sökning, de visas med en **resurs inom företaget** alternativet. Det här alternativet delar den sparade sökningen för alla användare i organisationen.

 ![Organisationens sparade sökningar](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Fäst datatillgångar
Med sparade sökningar, kan du spara och återanvända search definitioner. Datatillgångar som returneras av sökningarna kan ändras med tiden som innehållet i katalogen ändringen. Du kan uttryckligen identifiera specifika datatillgångar så att de blir enklare att åtkomst utan att behöva söka på när du har fäst datatillgångar.

Det är enkelt att fästa en datatillgång. Om du vill lägga till datatillgången i listan fästa, klickar du på den **PIN-kod** ikon. Ikonen visas i hörnet av panelen tillgången i vyn sida vid sida och i kolumnen längst till vänster i listvyn i Azure Data Catalog-portalen.

![Ikonen datatillgång PIN-kod](./media/data-catalog-how-to-save-pin/05-pinning.png)

Det är lika enkelt att panelfönstret en datatillgång. Klicka bara på den **ta bort** ikon för att ändrar du inställningen för den markerade tillgången.

![Datatillgången-ta bort ikon](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Avsnittet Mina tillgångar
Startsidan för Data Catalog innehåller en **Mina tillgångar** avsnittet som visas resurser av intresse för den aktuella användaren. Det här avsnittet innehåller både fästa tillgångar och sparade sökningar.

![Avsnittet Mina tillgångar på startsidan](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Sammanfattning
Azure Data Catalog innehåller funktioner som gör det lättare att identifiera de datakällor som du behöver, så att du och andra organisationsmedlemmar kan ägna mindre tid på att söka efter data och mer tid arbeta med den. Sparade sökningar och Fäst data tillgångar som bygger på dessa kärnfunktioner så att användarna enkelt kan identifiera datakällor som de arbetar med flera gånger.
