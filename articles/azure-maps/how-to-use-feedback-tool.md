---
title: Ge datafeedback till Azure Maps | Microsoft Azure Maps
description: Ge feedback på data med hjälp av feedbackverktyget för Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 92469370f1ea64f5ee1bc2a84e47cc8b294b5375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335353"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Ge datafeedback till Azure Maps

Azure Maps har varit tillgängligt sedan maj 2018. Azure Maps har tillhandahållit nya kartdata, lättanvända REST-API:er och kraftfulla SDK:er för att stödja våra företagskunder med olika typer av affärsanvändningsfall. Den verkliga världen förändras varje sekund, och det är viktigt för oss att ge en faktisk digital representation till våra kunder. Våra kunder som planerar att öppna eller stänga anläggningar behöver våra kartor för att uppdatera snabbt. Så, de kan effektivt planera för leverans, underhåll eller kundservice på rätt anläggningar. Vi har skapat webbplatsen för datafeedback i Azure Maps för att ge våra kunder möjlighet att ge direkt datafeedback. Kundernas datafeedback går direkt till våra dataleverantörer och deras kartredigerare. De kan snabbt utvärdera och införliva feedback i våra kartprodukter.  

[Azure Maps Data feedback webbplats](https://feedback.azuremaps.com) är ett enkelt sätt för våra kunder att ge karta data feedback, särskilt på affärspunkter av intresse och bostadsadresser. I den här artikeln får du hjälp med hur du ger olika typer av feedback med hjälp av feedbackwebbplatsen för Azure Maps.

## <a name="add-a-business-place-or-a-residential-address"></a>Lägga till en företagsplats eller en bostadsadress 

Du kanske vill ge feedback om en saknad intressepunkt eller en bostadsadress. Det finns två sätt att göra det på. Öppna webbplatsen för datafeedback i Azure Map, sök efter den saknade platsens koordinater och klicka sedan på "Lägg till en plats"

  ![sök saknad plats](./media/how-to-use-feedback-tool/search-poi.png)

Eller så kan du interagera med kartan. Klicka på platsen för att släppa en pin på koordinaten och klicka på "Lägg till en plats".

  ![lägga till stift](./media/how-to-use-feedback-tool/add-poi.png)

När du klickar dirigeras du till ett formulär för att ange motsvarande information för platsen.

  ![lägga till en plats](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Åtgärda en affärsplats eller en bostadsadress 

Feedbackwebbplatsen gör det också möjligt att söka och hitta en företagsplats eller en adress. Du kan ge feedback för att åtgärda adressen eller fästplatsen, om de inte är korrekta. Om du vill ge feedback för att åtgärda adressen använder du sökfältet för att söka efter en företagsadress eller bostadsadress. Klicka på platsen för ditt intresse från resultatlistan. Klicka på "Fixa det här stället".

  ![sökplats för att åtgärda](./media/how-to-use-feedback-tool/fix-place.png)

För att ge feedback för att åtgärda adressen, fyll i formuläret "Åtgärda en plats" och klicka sedan på "skicka"-knappen.

  ![åtgärda formulär](./media/how-to-use-feedback-tool/fix-form.png)

Om pin-platsen för platsen är fel kontrollerar du kryssrutan i formuläret "Åtgärda en plats" med det där ordet "Pin-platsen är felaktig". Flytta stiftet till rätt plats och klicka sedan på "skicka"-knappen.

  ![flytta stiftplats](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Lägg till en kommentar 

Förutom att du kan söka efter en plats kan du i feedbackverktyget också lägga till en textkommentar i fri form för information om platsen. Om du vill lägga till en kommentar söker du efter platsen eller klickar på platsen. Klicka på "Lägg till en kommentar", skriv en kommentar och klicka sedan på "Skicka".

  ![lägga till kommentar](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Spåra status 

Du kan också spåra status för din begäran genom att markera rutan "Jag vill spåra status" och tillhandahålla din e-post när du gör en begäran. Du får en spårningslänk i e-postmeddelandet som ger en aktuell status för din begäran. 

  ![feedback-status](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Nästa steg

Om du vill ställa in tekniska frågor som rör Azure Maps besöker du:

* [Överflödet av Azure Maps Stack](https://stackoverflow.com/questions/tagged/azure-maps)
* [Feedbackforum för Azure Maps](https://feedback.azure.com/forums/909172-azure-maps)
