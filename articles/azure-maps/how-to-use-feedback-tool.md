---
title: Lämna data feedback till Azure Maps | Microsoft Azure Maps
description: Ge feedback om data med hjälp av Microsoft Azure Maps-verktyget.
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 59670742586f596a817ad52383160a38358a6786
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911389"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Ge data feedback till Azure Maps

Azure Maps har vanligt vis varit tillgängligt sedan den 2018 maj, vilket ger nya kart data, lättanvända REST API: er och kraftfulla SDK: er som stöd för våra företags kunder på ett antal olika företags användnings fall. Den verkliga världen ändras varje sekund och det är viktigt att vi tillhandahåller en riktig digital representation av våra kunder. Våra kunder som planerar att öppna eller stänga anläggningar måste se till att våra kartor uppdateras så att de kan planera för leverans-, underhålls-eller kund tjänster på rätt sätt. Vi har skapat webbplatsen för Azure Maps data feedback för att hjälpa våra kunder att tillhandahålla direkt data feedback. Kundernas data feedback skickas direkt till våra data leverantörer och deras kart redigerare som snabbt kan utvärdera och införliva feedback i våra mappnings produkter.  

[Azure Maps data feedback-webbplatsen](https://feedback.azuremaps.com) är ett enkelt sätt för våra kunder att tillhandahålla feedback från kart data, särskilt i affärs punkter av intresse-och bostads adresser. Den här artikeln vägleder dig hur du ger olika typer av feedback på webbplatsen för Azure Maps feedback.

## <a name="add-a-business-place-or-a-residential-address"></a>Lägg till en företags plats eller en bostads adress 

Du kanske vill ge feedback för en saknad orienterings punkt eller bostads adress på kartan. Det finns två sätt att göra detta på. öppna webbplatsen för Azure Map-data feedback och Sök efter den saknade platsens koordinater och klicka på Lägg till en plats

  ![Sök efter saknad plats](./media/how-to-use-feedback-tool/search-poi.png)

Eller så kan du interagera med kartan och klicka på platsen för att släppa en PIN-kod på koordinaten och klicka på Lägg till en plats. 

  ![Lägg till PIN-kod](./media/how-to-use-feedback-tool/add-poi.png)

När du klickar på dirigeras du till ett formulär för att ange motsvarande information för platsen.

  ![Lägg till en plats](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Åtgärda en affärs plats eller en bostads adress 

På feedback-webbplatsen kan du också söka efter och hitta en affärs plats eller en adress och ge feedback för att åtgärda adressen eller PIN-koden, om den inte är korrekt. Om du vill ge feedback för att åtgärda adressen använder du Sök fältet för att söka efter en företags plats eller en lokal adress. Klicka på platsen för ditt intresse i listan resultat och klicka på "åtgärda det här stället".

  ![Sök plats för att åtgärda](./media/how-to-use-feedback-tool/fix-place.png)

Om du vill ge feedback om att åtgärda adressen, fyller du i formuläret "korrigera en plats" och klickar på knappen Skicka.

  ![korrigera formulär](./media/how-to-use-feedback-tool/fix-form.png)

Om PIN-koden för PIN-koden är fel markerar du kryss rutan på sidan "korrigera en plats" som säger "PIN-platsen är felaktig" och flyttar PIN-koden till rätt plats och klickar sedan på knappen Skicka.

  ![flytta PIN-plats](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Lägg till en kommentar 

Förutom att du kan söka efter en plats kan du med hjälp av feedback-verktyget lägga till en fritext kommentar för information som är relaterad till platsen. Om du vill lägga till en kommentars ökning för platsen eller klickar du på platsen och klickar på Lägg till en kommentar, skriver en kommentar och klickar sedan på Skicka. 

  ![lägg till kommentar](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Spåra status 

Du kan också spåra statusen för din begäran genom att markera rutan "Jag vill spåra status" och ange din e-postadress när du gör en begäran. Du får en uppföljnings länk i e-postmeddelandet som innehåller en uppdaterad status för din begäran. 

  ![feedback-status](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Nästa steg

Om du vill publicera tekniska frågor som rör Azure Maps går du till:

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Forum för Azure Maps feedback](https://feedback.azure.com/forums/909172-azure-maps)
