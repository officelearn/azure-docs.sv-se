---
title: Så här skapar du ett projekt - Custom Translator
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du skapar och hanterar ett projekt i Azure Cognitive Services Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5b3a6f2e903d8178554b7a076ae1277633569353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836570"
---
# <a name="create-a-project"></a>Skapa ett projekt

Ett projekt är en behållare för modeller, dokument och tester. Varje projekt innehåller automatiskt alla dokument som överförs till arbetsytan som har rätt språkpar.

Att skapa projekt är det första steget mot att skapa en modell.

## <a name="create-a-project"></a>Skapa ett projekt:

1.  Klicka på Skapa projekt i portalen [Anpassad översättare.](https://portal.customtranslator.azure.ai)

    ![Skapa projekt](media/how-to/how-to-create-project.png)

2.  Ange följande information om projektet i dialogrutan:

    a.  Projektnamn (obligatoriskt): Ge projektet ett unikt och meningsfullt namn. Det är inte nödvändigt att nämna språken i titeln.

    b.  Beskrivning: En kort sammanfattning om projektet. Den här beskrivningen har inget inflytande över beteendet hos den anpassade översättaren eller det anpassade systemet, men kan hjälpa dig att skilja mellan olika projekt.

    c.  Språkpar (obligatoriskt): Välj det språk som du översätter från och till.

    d.  Kategori (obligatoriskt): Välj den kategori som är mest lämplig för projektet. Kategorin beskriver terminologin och stilen på de dokument som du tänker översätta.

    e.  Kategoribeskrivning: Använd det här fältet för att bättre beskriva det område eller den bransch där du arbetar. Till exempel, om din kategori är medicin, kan du lägga till ett visst dokument, en sådan operation, eller pediatrik. Beskrivningen har inget inflytande över beteendet hos den anpassade översättaren eller det anpassade systemet.

    f.  Projektetikett: [Projektetiketten](workspace-and-project.md#project-labels) skiljer mellan projekt med samma språkpar och kategori. Du kan *också* använda en etikett om du planerar att skapa flera projekt för samma språkpar och samma kategori och vill komma åt dessa projekt med ett annat KategoriID. Använd inte det här fältet om du bara bygger system för en kategori. En projektetikett krävs inte och kan inte skilja mellan språkpar. Du kan använda samma etikett för flera projekt.

    ![Dialogrutan Skapa projekt](media/how-to/how-to-create-project-dialog.png)

3.  Klicka på Skapa

## <a name="view-project-details"></a>Visa projektinformation

Målsidan för Custom Translator visar de första 10 projekten på arbetsytan. Den visar projektets namn, språkpar, kategori, status och BLEU-poäng.

När du har valt ett projekt visas följande på projektsidan:

- CategoryID: Ett CategoryID skapas genom att sammanfoga WorkspaceID- , projektetiketten och kategorikoden. Du använder CategoryID med API:et för textöversättare för att få anpassade översättningar.

- Tågknapp: Använd den här knappen för att starta en [utbildning en modell](how-to-train-model.md).

- Knappen Lägg till dokument: Använd den här knappen för att [ladda upp dokument](how-to-upload-document.md).

- Knappen Filterdokument: Använd den här knappen för att filtrera och söka efter specifika dokument.

    ![Visa projektinformation](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [söker, redigerar, tar bort projekt](how-to-search-edit-delete-projects.md).
- Läs om hur du [laddar upp dokument](how-to-upload-document.md) för att skapa översättningsmodeller.
