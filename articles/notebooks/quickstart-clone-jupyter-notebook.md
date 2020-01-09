---
title: Klona en Jupyter-anteckningsbok från GitHub med Azure Notebooks för hands version
description: Snabbt klona en Jupyter-anteckningsbok från en GitHub-lagringsplats och kör det i dina anteckningsböcker i Azure-konto.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 8aa88008ece170a5eed7ab491e3318aed5168923
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647109"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Snabb start: klona en bärbar dator i Azure Notebooks för hands version

Många dataanalytiker och utvecklare lagra sina bärbara datorer i [GitHub-lagringsplatser](https://github.com), en kostnadsfri tjänst som tillhandahåller lagring och versionskontroll för många olika projekttyper. GitHub används ofta för att samarbeta i Jupyter-anteckningsböcker som körs lokalt. I sådana fall kan varje medarbetare upprätthåller en lokal kopia av databasen och kör de bärbara datorerna från kopian.

Kloning av skapar en kopia av en GitHub-anteckningsbok i anteckningsböcker i Azure-konto i stället. Den här klonen frikopplat från den ursprungliga databasen; ändringarna lagras i ditt Azure-anteckningsböcker konto och påverkar inte ursprungligt. Eftersom din klon är i molnet, kan du dela projektet med andra medarbetare som inte behöver göra några lokala kopior eller även har Jupyter som är installerade på sina egna datorer. Du kan också klona en bärbar dator som en startpunkt för ett projekt på egen hand eller hämta datafiler.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-azure-cognitive-services-notebooks"></a>Klona Azure Cognitive Services-anteckningsböcker

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns i [Snabbstart – logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. Din offentliga profil-sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du pilknappen (kortkommando: U; knappen visas som **Ladda upp GitHub lagrings platsen** när webbläsarfönstret är tillräckligt bred):

    ![Ladda upp GitHub lagrings platsen-kommandot på sidan mina projekt](media/quickstarts/upload-github-repo-command.png)

1. I fönstret **överför GitHub-lagringsplats** som visas anger eller anger du följande information och väljer sedan **Importera**:

   - **GitHub-lagringsplats**: Microsoft/kognitivt-Services-Notebooks (det här namnet klonar Jupyter-anteckningsböcker för Azure Cognitive Services på [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klona rekursivt**: (avmarkerad)
   - **Projektnamnet**: Cognitive Services-klon
   - **Projektet ID**: cognitive services klon
   - **Offentliga**: (avmarkerad)

     ![Ladda upp GitHub lagrings platsen-popup för att samla in lagrings platsen-information](media/quickstarts/upload-github-repo-popup.png)

1. Ha tålamod medan processen körs; kloning av en databas kan ta några minuter.

1. När Kloningen har slutförts, går Azure-datorer du till det nya projektet där du kan se kopior av alla filer.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Dela en anteckningsbok

1. Om du vill dela din kopia av det klonade projektet använder den **dela** styra eller få en länk, få HTML eller Markdown-kod som innehåller en länk, eller skapa ett e-postmeddelande med en länk:

    ![Kommandot share för projektet](media/quickstarts/share-project-command.png)

1. Eftersom du avmarkerad den **offentliga** alternativet när du klonar projektet, klonen är privat. För att göra din kopia offentlig, Välj **Projektinställningar**, ange den **offentliga projekt** i popup-fönstret och välj sedan **spara**.

1. Välj en anteckningsbok i projektet för att köra den. Varje anteckningsbok i Azure Cognitive Services-databasen är till exempel en egen självständigt Snabbstart. På bilden nedan visas resultatet av att använda anteckningsboken BingImageSearchAPI efter att lägga till en Cognitive Services API-prenumerationsnyckel och ändra söktermen ”Valpar” till ”bunnies”:

    ![Kör Jupyter-anteckningsbok klonat från GitHub](media/quickstarts/clone-notebook-result.png)

1. När du är klar kör anteckningsboken, Välj **filen** > **Stäng och stoppa** att Stäng anteckningsboken och ett webbläsarfönster.

1. Om du vill dela en enskild anteckningsbok i projektet, högerklicka på den bärbara datorn och välj **Kopiera länk** (kortkommandot: y):

    ![Kontext menyn kommando för att kopiera en länk till en enskild notebook](media/quickstarts/copy-link-to-individual-notebook.png)

1. Om du vill redigera filer än anteckningsböcker, högerklicka på filen i projektet och välj **Redigera fil** (kortkommandot: jag). Standardåtgärden **kör** (kortkommandot: r), bara visar filinnehållet och tillåter inte att redigera.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
