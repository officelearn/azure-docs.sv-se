---
title: Klona en Jupyter-anteckningsbok från GitHub med förhandsversionen av Azure-anteckningsböcker
description: Klona snabbt en Jupyter-anteckningsbok från en GitHub-databas och kör den i ditt Azure Notebooks-konto.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d0f3a12ff04e115074c3821c5e29652484710bca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064605"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Snabbstart: Klona en anteckningsbok i förhandsversionen av Azure Notebooks

I den här snabbstarten kopierar du en Jupyter-anteckningsbok som lagras i GitHub till ett Azure Notebooks-konto. 

GitHub-databaser ger lagrings- och versionskontroll för Jupyter-anteckningsböcker. Medarbetare underhåller lokala kopior av databaserna och kör anteckningsböckerna från dessa kopior. Klona en Jupyter-anteckningsbok från GitHub till ditt Azure Notebooks-konto skapar en oberoende kopia av anteckningsboken. Ändringar lagras endast i ditt Azure Notebooks-konto och påverkar inte den ursprungliga GitHub-databasen. 

Eftersom klonen av Azure-anteckningsböcker finns i molnet kan du dela den med medarbetare som inte behöver göra några lokala kopior eller ha Jupyter installerat på sina datorer. Du kan också klona en anteckningsbok helt enkelt som en utgångspunkt för ett eget projekt eller för att hämta datafiler. 

## <a name="prerequisites"></a>Krav
Inga.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klona Azure Cognitive Services-anteckningsböcker

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Gå till [Azure-anteckningsböcker](https://notebooks.azure.com) och logga in. Mer information finns [i Snabbstart - Logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md).

1. På din offentliga profilsida väljer du **Mina projekt** högst upp på sidan:

    ![Länken Mina projekt visas högst upp i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du uppåtpilknappen (kortkommando: U; knappen visas som **Upload GitHub Repo** när webbläsarfönstret är tillräckligt brett):

    ![Ladda upp kommandot Upload GitHub Repo på sidan Mina projekt](media/quickstarts/upload-github-repo-command.png)

1. Ange eller ange följande information i **GitHub-databasen** för överföring av GitHub-lagringsplats och välj sedan **Importera:**

   - **GitHub-databas:** Microsoft/cognitive-services-notebooks (det här namnet klonar Jupyter-anteckningsböckerna för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klon rekursivt**: (rensas)
   - **Projektets namn**: Cognitive Services Clone
   - **Projekt-ID**: kognitiv-tjänster-klon
   - **Offentlig**: (rensad)

     ![Ladda upp GitHub Repo popup för att samla in repoinformation](media/quickstarts/upload-github-repo-popup.png)

1. Ha tålamod medan processen är klar; kloning av en slutförvar kan ta några minuter.

1. När kloningen är klar tar Azure Notebooks dig till det nya projektet där du kan se kopiorna av alla filer.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Dela en anteckningsbok

1. Om du vill dela ditt exemplar av det klonade projektet använder du kontrollen **Dela** eller hämtar en länk, hämtar HTML- eller Markdown-kod som innehåller länken eller skapar ett e-postmeddelande med länken:

    ![Kommandot Projektdelning](media/quickstarts/share-project-command.png)

1. Eftersom du har avmarkerat alternativet **Offentligt** när du klonar projektet är kloningen privat. Om du vill göra kopian offentlig väljer du **Projektinställningar,** anger alternativet **Offentligt projekt** i popup-fönstret och väljer sedan **Spara**.

1. Välj en anteckningsbok i projektet för att köra den. Varje anteckningsbok i Azure Cognitive Services-databasen är till exempel sin egen fristående snabbstart. Bilden nedan visar resultatet av att använda BingImageSearchAPI-anteckningsboken, efter att ha lagt till en Cognitive Services API-prenumerationsnyckel och ändrat sökordet "valpar" till "kaniner":

    ![Köra Jupyter-anteckningsbok klonad från GitHub](media/quickstarts/clone-notebook-result.png)

1. När du är klar med att köra anteckningsboken väljer du **Stäng fil** > **och stoppar** för att stänga anteckningsboken och dess webbläsarfönster.

1. Om du vill dela en enskild anteckningsbok i projektet högerklickar du på anteckningsboken och väljer **Kopiera länk** (kortkommando: y):

    ![Kommandot Snabbmeny för att kopiera en länk till en enskild anteckningsbok](media/quickstarts/copy-link-to-individual-notebook.png)

1. Om du vill redigera andra filer än anteckningsböcker högerklickar du på filen i projektet och väljer **Redigera fil** (kortkommando: i). Standardåtgärden **Run** (kortkommando: r) visar bara filinnehållet och tillåter inte redigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa och kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
