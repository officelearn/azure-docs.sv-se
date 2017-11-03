---
title: "Spara sökningar och PIN-kod datatillgångar i Azure Data Catalog | Microsoft Docs"
description: "Artikel syntaxmarkering funktioner i Azure Data Catalog för att spara datakällor och datatillgångar för senare användning."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 8c319d0dcbe8b95af11b8be2368a9348b260446c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Spara sökningar och PIN-kod datatillgångar i Azure Data Catalog
## <a name="introduction"></a>Introduktion
Azure Data Catalog innehåller funktioner för upptäckt av datakälla. Du kan snabbt söka och filtrera katalogen för att identifiera datakällor och förstå deras syfte, vilket gör det lättare att hitta rätt data för jobbet till hands.

Men vad händer om du behöver arbeta regelbundet med samma data? Och vad händer om du och andra användare regelbundet bidrar med din kunskap till samma datakällor i katalogen? I sådana fall är kan att utfärda flera gånger i samma genomsöks vara ineffektiv. Detta är där med hjälp av sparad sökning och Fäst datatillgångar.

## <a name="saved-searches"></a>Sparade sökningar
En sparad sökning i Data Catalog är en återanvändbar per användare Sök definition. Du kan definiera en sökning, till exempel sökvillkor, taggar och andra filter och sparar den. Du kan köra sparade sökningen definition senare för att returnera alla datatillgångar som matchar sökvillkoren igen.

### <a name="create-a-saved-search"></a>Skapa en sparad sökning
Om du vill skapa en sparad sökning, gör du följande:
1. I Azure Data Catalog-portalen i den **aktuella sökningen** -fönstret klickar du på **spara**. 

    ![Aktuella Sök inställningar spara länken](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Ange sökvillkor som du vill återanvända **spara**.

    ![Aktuella sökinställningar sparade söknamn](./media/data-catalog-how-to-save-pin/02-name.png)

3. När du uppmanas ange ett namn för den sparade sökningen. Välj ett beskrivande namn och som beskriver datatillgångar som returneras av sökningen.

### <a name="manage-saved-searches"></a>Hantera sparade sökningar
När du har sparat en eller flera sökningar, en **sparade sökningar** alternativet visas under den **aktuella sökningen** rutan. När listan har expanderats visas alla sparade sökningar.

 ![Listan över sparade sökningar](./media/data-catalog-how-to-save-pin/03-list.png)

Gör något av följande:

* Om du vill köra en sökning, väljer du en sparad sökning i listan.

* Om du vill visa en lista med alternativ för en sparad sökning, klickar du på pilen bredvid namnet.

    ![Alternativ för att hantera sparade sökningar](./media/data-catalog-how-to-save-pin/04-managing.png)

* Om du vill ange ett nytt namn för den sparade sökningen väljer **Byt namn på**. Sök-definition ändras inte.

* Om du vill ta bort den sparade sökningen från listan, Välj **ta bort**, och sedan bekräfta borttagningen.

* Om du vill markera den sparade sökningen som standardsökning, Välj **sparar som standard**. Om du utför en ”tom” sökning på startsidan för Azure Data Catalog kan köra standardsökningen. Dessutom sökningen som är markerad som standardsökningen visas överst i den **sparade sökningar** lista.

### <a name="organizational-saved-searches"></a>Organisationens sparade sökningar
Alla användare i din organisation kan spara sökningar för eget bruk. Data Catalog administratörer kan också spara sökningar för alla användare i organisationen. När administratörer spara en sökning, de kan välja mellan en **resurs inom företaget** alternativet. Det här alternativet delar den sparade sökningen för alla användare i organisationen.

 ![Organisationens sparade sökningar](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Fäst datatillgångar
Du kan spara och återanvända Sök definitioner med sparade sökningar. Datatillgångar som returneras av sökningarna kan ändras när innehållet i katalogen ändringen. När du fäster datatillgångar identifierar du uttryckligen specifika datatillgångar så att de blir enklare att komma åt utan att behöva använda en sökning.

Det är enkelt att fästa en datatillgång. Om du vill lägga till data tillgången i listan Fäst klickar du på den **PIN-kod** ikon. Ikonen visas i hörnet på panelen tillgången i vyn sida vid sida och i den vänstra kolumnen i listvyn i Azure Data Catalog-portalen.

![Ikonen datatillgång PIN-kod](./media/data-catalog-how-to-save-pin/05-pinning.png)

Det är lika enkelt att ta bort en datatillgång. Klicka bara på den **ner** att växla inställningen för den markerade tillgången.

![Datatillgång ner ikon](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Avsnittet Mina tillgångar
Startsidan för Data Catalog innehåller en **Mina tillgångar** avsnitt som visar tillgångar av intresse för den aktuella användaren. Det här avsnittet innehåller både fasta tillgångar och sparade sökningar.

![Avsnittet Mina tillgångar på startsidan](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Sammanfattning
Azure Data Catalog innehåller funktioner som gör det lättare att identifiera de datakällor som du behöver, så att du och andra organisationsmedlemmar i kan ägna mindre tid som söker efter data och mer tid arbeta med den. Sparade sökningar och Fäst data tillgångar som bygger på dessa kärnfunktioner så att användarna enkelt kan identifiera datakällor som de arbetar med flera gånger.
