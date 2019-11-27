---
title: Klona en Jupyter-anteckningsbok från GitHub med Azure-anteckningsböcker
description: Snabbt klona en Jupyter-anteckningsbok från en GitHub-lagringsplats och kör det i dina anteckningsböcker i Azure-konto.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 500bd3f85409bb2f5e7b73be0478694695cc9c00
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277530"
---
# <a name="quickstart-clone-a-notebook"></a>Snabbstart: Klona en anteckningsbok

Många data experter och utvecklare lagrar sina antecknings böcker i [GitHub-databaser](https://github.com), en kostnads fri tjänst som tillhandahåller lagrings-och versions kontroll för många olika projekt typer. GitHub används ofta för att samarbeta i Jupyter-anteckningsböcker som körs lokalt. I sådana fall kan varje medarbetare upprätthåller en lokal kopia av databasen och kör de bärbara datorerna från kopian.

Kloning av skapar en kopia av en GitHub-anteckningsbok i anteckningsböcker i Azure-konto i stället. Den här klonen frikopplat från den ursprungliga databasen; ändringarna lagras i ditt Azure-anteckningsböcker konto och påverkar inte ursprungligt. Eftersom din klon är i molnet, kan du dela projektet med andra medarbetare som inte behöver göra några lokala kopior eller även har Jupyter som är installerade på sina egna datorer. Du kan också klona en bärbar dator som en startpunkt för ett projekt på egen hand eller hämta datafiler.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klona Azure Cognitive Services-anteckningsböcker

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. (Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du pilknappen (kortkommando: U; knappen visas som **Ladda upp GitHub lagrings platsen** när webbläsarfönstret är tillräckligt bred):

    ![Ladda upp GitHub lagrings platsen-kommandot på sidan mina projekt](media/quickstarts/upload-github-repo-command.png)

1. I fönstret **överför GitHub-lagringsplats** som visas anger eller anger du följande information och väljer sedan **Importera**:

   - **GitHub-lagringsplats**: Microsoft/kognitivt-Services-Notebooks (det här namnet klonar Jupyter-anteckningsböcker för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klona rekursivt**: (rensad)
   - **Projekt namn**: Cognitive Services klon
   - **Projekt-ID**: kognitiv-Services-Clone
   - **Offentlig**: (avmarkerad)

     ![Ladda upp GitHub lagrings platsen-popup för att samla in lagrings platsen-information](media/quickstarts/upload-github-repo-popup.png)

1. Ha tålamod medan processen körs; kloning av en databas kan ta några minuter.

1. När Kloningen har slutförts, går Azure-datorer du till det nya projektet där du kan se kopior av alla filer.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Dela en anteckningsbok

1. Om du vill dela din kopia av det klonade projektet använder du **delnings** kontrollen eller hämtar en länk, hämtar HTML-kod eller markdown-kod som innehåller länken eller skapar ett e-postmeddelande med länken:

    ![Kommandot share för projektet](media/quickstarts/share-project-command.png)

1. Eftersom du har rensat det **offentliga** alternativet när du klonar projektet, är klonen privat. Om du vill göra din kopia offentlig väljer du **projekt inställningar**, ange alternativet **offentligt projekt** i popup-fönstret och väljer sedan **Spara**.

1. Välj en anteckningsbok i projektet för att köra den. Varje anteckningsbok i Azure Cognitive Services-databasen är till exempel en egen självständigt Snabbstart. På bilden nedan visas resultatet av att använda anteckningsboken BingImageSearchAPI efter att lägga till en Cognitive Services API-prenumerationsnyckel och ändra söktermen ”Valpar” till ”bunnies”:

    ![Kör Jupyter-anteckningsbok klonat från GitHub](media/quickstarts/clone-notebook-result.png)

1. När du är klar med antecknings boken väljer du **fil** > **Stäng och stoppa** för att stänga antecknings boken och webbläsarfönstret.

1. Om du vill dela en enskild antecknings bok i projektet högerklickar du på antecknings boken och väljer **Kopiera länk** (kortkommando: y):

    ![Kontext menyn kommando för att kopiera en länk till en enskild notebook](media/quickstarts/copy-link-to-individual-notebook.png)

1. Om du vill redigera andra filer än antecknings böcker högerklickar du på filen i projektet och väljer **Redigera fil** (kortkommando: i). Standard åtgärden **Kör** (tangent bords gen väg: r), visar bara fil innehållet och tillåter inte redigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
