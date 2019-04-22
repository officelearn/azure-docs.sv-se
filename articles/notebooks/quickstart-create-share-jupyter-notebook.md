---
title: Skapa och dela en Jupyter notebook på Azure
description: Snabbt skapa och köra en Jupyter notebook i Azure-anteckningsböcker och sedan dela den notebook med andra.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 5add60ad-0b4b-4fd5-adf5-eb50ce072d00
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b1618e2ac997445606ce98fc72a1ec35ca1280be
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273676"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Snabbstart: Skapa och dela en notebook-fil

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns i [Snabbstart – logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. Din offentliga profil-sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På den **Mina projekt** väljer **+ nytt projekt** (kortkommandot: n); knappen kan visas endast som **+** om webbläsaren är smal:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I den **Skapa nytt projekt** popup-fönstret som visas, ange eller ange följande information och välj sedan **skapa**:

   - **Projektnamnet**: Hello World i Python
   - **Projektet ID**: hello-world – python
   - **Offentliga projekt**: (avmarkerad)
   - **Skapa en README.md**: (avmarkerad)

     ![Nya projekt popup-fönster med ifyllt information](media/quickstarts/new-project-popup.png)

1. Efter en liten stund navigerar du till det nya projektet i Azure-anteckningsböcker. Lägg till en anteckningsbok i projektet genom att välja den **+ ny** listrutan (som kan visas bara **+**), sedan välja **Notebook**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "Ett nytt tomt projekt och lägga till notebook-kommando")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. I den **Skapa ny anteckningsbok** popup-fönstret som visas, ange ett filnamn för din bärbara dator, till exempel *HelloWorldInPython.ipynb* (*.ipynb* innebär v Ironpythonu (Jupyter)-anteckningsbok ), och välj **Python 3.6** för språket (kallas även den *kernel*):

    ![Skapa en ny anteckningsbok popup-fönstret](media/quickstarts/new-notebook-popup.png)

1. Välj **New** skapa anteckningsboken visas sedan i projektets fillistan:

    ![Ny anteckningsbok som visas i listan över filer i projektets](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Köra anteckningsboken

1. Välj ny anteckningsboken för att köra den i Redigeraren; kernel som du har valt (Python 3.6 i det här exemplet) aktiveras automatiskt för den här anteckningsboken:

    ![Vy av en ny anteckningsbok i Azure-anteckningsböcker](media/quickstarts/create-notebook-first-open.png)

1. Som standard har en tom kodcell i anteckningsboken. Ändra cell till **Markdown**, Använd den cell listrutan för att välja **Markdown**:

    ![Ändra typen cell i en ny notebook](media/quickstarts/create-notebook-cell-type.png)

1. Skriv eller klistra in följande rubriktext i cellen:

    ```markdown
    # Hello World in Python
    ```

1. Eftersom du redigerar Markdown kan visas texten som en rubrik med ”#”. För att rendera Markdown i HTML-format, Välj den **kör** knappen. Azure anteckningsböcker automatiskt skapar sedan en ny kodcell efteråt:

    ![Knappen Kör för en cell och renderade Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Ange följande kod för Python i kodcellen:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Välj **kör** (kortkommandot: SKIFT + RETUR) att köra koden. Under cellen bör du se lyckade utdata som liknar följande text:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Välj Spara ikon för att spara ditt arbete:

    ![Spara ikonen i verktygsfältet Jupyter notebook](media/quickstarts/hello-results-save-icon.png)

1. Välj den **filen** > **Stäng och stoppa** menykommandot att stoppa servern och stänga webbläsarfönstret.

## <a name="share-the-notebook"></a>Dela anteckningsboken

Om du vill dela din bärbara dator vid behov växla tillbaka till projektsidan, högerklicka på anteckningsboksfilen, Välj **Kopiera länk** (kortkommandot: y), och klistra in länken i ett meddelande (e-post, Snabbmeddelanden osv.).

På projektsidan kan du också använda den **resursen** inbäddningskod i menyn för att få en länk, skapa ett e-postmeddelande med en länk eller hämta HTML och Markdown:

![Kommandot share för projektet](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
