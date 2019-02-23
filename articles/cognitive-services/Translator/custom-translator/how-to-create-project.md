---
title: Hur du skapar ett projekt? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: Hur du skapar ett projekt i anpassade Translator?
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: dde1bfbaf810d07a2b992258dd8674efec302dc6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737169"
---
# <a name="create-a-project"></a>Skapa ett projekt

Ett projekt är en behållare för en modeller, dokument, och testar. Alla projekt som innehåller den automatiskt alla dokument som överförs till den arbetsytan som har rätt språk-par.

Det är det första steget för att skapa en modell för att skapa projektet.

## <a name="create-a-project"></a>Skapa ett projekt:

1.  I den [anpassad Translator](https://portal.customtranslator.azure.ai) -portalen klickar du på Skapa projekt.

    ![Skapa projekt](media/how-to/how-to-create-project.png)

2.  Ange följande information om ditt projekt i dialogrutan:

    a.  Namn (krävs): Ge projektet ett unikt namn. Du behöver inte nämner språk i rubriken.

    b.  Beskrivning: En kort sammanfattning om projektet. Den här beskrivningen påverkas inte över beteendet för den anpassade Translator eller resulterande anpassade systemet, men kan hjälpa dig att skilja mellan olika projekt.

    c.  Språk par (krävs): Välj det språk som du översätta från och till.

    d.  Kategori (krävs): Välj den kategori som passar bäst för ditt projekt. Kategorin Beskriver terminologi och formatet för de dokument som du planerar att översätta.

    e.  Beskrivning: Använd det här fältet för att bättre beskriva programmatiskt eller bransch som du arbetar. Till exempel om en kategori som heter medicin, du kan lägga till ett visst dokument, sådana operation eller pediatrics. Beskrivningen påverkas inte över beteendet för den anpassade Translator eller resulterande anpassade systemet.

    f.  Etikett för projektet: Den [projekt etikett](workspace-and-project.md#project-labels) skiljer mellan projekt med samma språk-par och kategori. Ett bra tips är att använda en etikett *endast* om du planerar att skapa flera projekt för samma språk-par och samma kategori och vill komma åt dessa projekt med en annan CategoryID. Använd inte det här fältet om du bygger system för en kategori. En etikett för projektet är inte obligatoriskt och inte bra att skilja mellan språkpar. Du kan använda samma etikett för flera projekt.

    ![Skapa dialogrutan projekt](media/how-to/how-to-create-project-dialog.png)

3.  Klicka på Skapa

## <a name="view-project-details"></a>Visa projektinformation

Anpassad Translator denna sida visar de första 10 projekt på din arbetsyta. Den visar projektnamn, språk-par, kategori, status och BLEU poäng.

När du har valt ett projekt, ser du följande på projektsidan för:

- CategoryID: En CategoryID skapas genom att sammanfoga WorkspaceID, projekt etiketten och kod. Du kan använda CategoryID med Translator Text API för att hämta anpassade översättningar.

- Träna knappen: Använd den här knappen för att starta en [träna en modell](how-to-train-model.md).

- Lägg till dokument knappen: Använd den här knappen till [ladda upp dokument](how-to-upload-document.md).

- Filterknapp dokument: Använd den här knappen för att filtrera och söka efter specifika dokument.

    ![Visa projektinformation](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du söker, redigera, ta bort projektet](how-to-search-edit-delete-projects.md).
- Lär dig [hur du överför dokument](how-to-upload-document.md) att skapa översättningsmodeller.
