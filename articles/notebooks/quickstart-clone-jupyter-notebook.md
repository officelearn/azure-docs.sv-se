---
title: Klona en Jupyter-anteckningsbok från GitHub med Azure Notebooks för hands version
description: Klona snabbt en Jupyter-anteckningsbok från en GitHub-lagringsplats och kör den i ditt Azure Notebooks-konto.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 35352e74a8db071a2f85bd71857d47f3af1953e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85833094"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Snabb start: klona en bärbar dator i Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I den här snabb starten kopierar du en Jupyter-anteckningsbok som lagras i GitHub till ett Azure Notebooks-konto. 

GitHub-databaser tillhandahåller lagrings-och versions kontroll för Jupyter-anteckningsböcker. Medarbetare behåller lokala kopior av databaserna och kör antecknings böckerna från dessa kopior. Att klona en Jupyter-anteckningsbok från GitHub till ditt Azure Notebooks-konto skapar en oberoende kopia av antecknings boken. Ändringar lagras bara i ditt Azure Notebooks-konto och påverkar inte den ursprungliga GitHub-lagringsplatsen. 

Eftersom din Azure Notebooks klon är i molnet kan du dela den med medarbetare, som inte behöver göra några lokala kopior eller ha Jupyter installerade på sina datorer. Du kan också klona en antecknings bok som är en utgångs punkt för ett eget projekt eller hämta filer. 

## <a name="prerequisites"></a>Förutsättningar
Inga.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klona Azure Cognitive Services-anteckningsböcker

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Länken Mina projekt överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du pilknappen (kortkommando: U; knappen visas som **Ladda upp GitHub lagrings platsen** när webbläsarfönstret är tillräckligt bred):

    ![Ladda upp GitHub lagrings platsen-kommandot på sidan mina projekt](media/quickstarts/upload-github-repo-command.png)

1. I fönstret **överför GitHub-lagringsplats** som visas anger eller anger du följande information och väljer sedan **Importera**:

   - **GitHub-lagringsplats**: Microsoft/kognitivt-Services-Notebooks (det här namnet klonar Jupyter-anteckningsböcker för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) ).
   - **Klona rekursivt**: (rensad)
   - **Projekt namn**: Cognitive Services klon
   - **Projekt-ID**: kognitiv-Services-Clone
   - **Offentlig**: (avmarkerad)

     ![Ladda upp GitHub lagrings platsen-popup för att samla in lagrings platsen-information](media/quickstarts/upload-github-repo-popup.png)

1. Bli patient medan processen slutförs; Det kan ta några minuter att klona en lagrings plats.

1. När kloningen är färdig tar Azure Notebooks dig till det nya projektet där du kan se kopiorna av alla filer.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Dela en bärbar dator

1. Om du vill dela din kopia av det klonade projektet använder du **delnings** kontrollen eller hämtar en länk, hämtar HTML-kod eller markdown-kod som innehåller länken eller skapar ett e-postmeddelande med länken:

    ![Projekt resurs kommando](media/quickstarts/share-project-command.png)

1. Eftersom du har rensat det **offentliga** alternativet när du klonar projektet, är klonen privat. Om du vill göra din kopia offentlig väljer du **projekt inställningar**, ange alternativet **offentligt projekt** i popup-fönstret och väljer sedan **Spara**.

1. Välj en antecknings bok i projektet för att köra den. Varje antecknings bok i Azure Cognitive Services-lagringsplatsen är till exempel en egen självständiga snabb start. Bilden nedan visar resultatet av att använda BingImageSearchAPI Notebook, efter att du lagt till en prenumerations nyckel för Cognitive Services-API och ändrat Sök termen "puppies" till "Bunnies":

    ![Köra Jupyter Notebook som är klonad från GitHub](media/quickstarts/clone-notebook-result.png)

1. När du är klar med antecknings boken väljer du **Arkiv**  >  **Stäng och stoppa** för att stänga antecknings boken och webbläsarfönstret.

1. Om du vill dela en enskild antecknings bok i projektet högerklickar du på antecknings boken och väljer **Kopiera länk** (kortkommando: y):

    ![Snabb meny kommando för att kopiera en länk till en enskild antecknings bok](media/quickstarts/copy-link-to-individual-notebook.png)

1. Om du vill redigera andra filer än antecknings böcker högerklickar du på filen i projektet och väljer **Redigera fil** (kortkommando: i). Standard åtgärden **Kör** (tangent bords gen väg: r), visar bara fil innehållet och tillåter inte redigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa och kör en Jupyter-anteckningsbok för linjär regression](tutorial-create-run-jupyter-notebook.md)
