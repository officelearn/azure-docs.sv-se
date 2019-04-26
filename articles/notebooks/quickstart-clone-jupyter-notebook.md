---
title: Klona en Jupyter-anteckningsbok från GitHub med Azure-anteckningsböcker
description: Snabbt klona en Jupyter-anteckningsbok från en GitHub-lagringsplats och kör det i dina anteckningsböcker i Azure-konto.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: d7122b78-6daa-4bea-883b-ff832cfecef3
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 30625423553b71e848d27d047d4b7bc3add6eaff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60238731"
---
# <a name="quickstart-clone-a-notebook"></a>Snabbstart: Skapa en notebook-fil

Många dataanalytiker och utvecklare lagra sina bärbara datorer i [GitHub-lagringsplatser](https://github.com), en kostnadsfri tjänst som tillhandahåller lagring och versionskontroll för många olika projekttyper. GitHub används ofta för att samarbeta i Jupyter-anteckningsböcker som körs lokalt. I sådana fall kan varje medarbetare upprätthåller en lokal kopia av databasen och kör de bärbara datorerna från kopian.

Kloning av skapar en kopia av en GitHub-anteckningsbok i anteckningsböcker i Azure-konto i stället. Den här klonen frikopplat från den ursprungliga databasen; ändringarna lagras i ditt Azure-anteckningsböcker konto och påverkar inte ursprungligt. Eftersom din klon är i molnet, kan du dela projektet med andra medarbetare som inte behöver göra några lokala kopior eller även har Jupyter som är installerade på sina egna datorer. Du kan också klona en bärbar dator som en startpunkt för ett projekt på egen hand eller hämta datafiler.

## <a name="clone-azure-cognitive-services-notebooks"></a>Klona Azure Cognitive Services-anteckningsböcker

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns i [Snabbstart – logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. Din offentliga profil-sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På den **Mina projekt** väljer du uppåtpilen (kortkommandot: U; knappen visas som **överför GitHub-lagringsplatsen** när webbläsarfönstret är tillräckligt bred för):

    ![Ladda upp GitHub-lagringsplatsen kommandot på sidan Mina projekt](media/quickstarts/upload-github-repo-command.png)

1. I den **överför GitHub-lagringsplatsen** som visas, ange eller ange följande information och välj sedan **Import**:

   - **GitHub-lagringsplatsen**: Microsoft cognitive-tjänster-datorer (det här namnet klonar Jupyter-anteckningsböcker för Azure Cognitive Services på [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Klona rekursivt**: (avmarkerad)
   - **Projektnamnet**: Cognitive Services klon
   - **Projektet ID**: cognitive services klon
   - **Offentliga**: (avmarkerad)

     ![Ladda upp GitHub-lagringsplatsen popup-fönstret för att samla in information om lagringsplats](media/quickstarts/upload-github-repo-popup.png)

1. Ha tålamod medan processen körs; kloning av en databas kan ta några minuter.

1. När Kloningen har slutförts, går Azure-datorer du till det nya projektet där du kan se kopior av alla filer.

    [![](media/quickstarts/completed-clone.png "Vy över en slutförd klon")](media/quickstarts/completed-clone.png#lightbox)

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
