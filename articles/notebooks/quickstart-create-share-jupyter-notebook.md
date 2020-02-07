---
title: Skapa och dela en Jupyter-anteckningsbok för Azure Notebooks för hands version
description: Skapa snabbt och kör en Jupyter-anteckningsbok på Azure Notebooks för hands versionen och dela den sedan med andra.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064452"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Snabb start: skapa och dela en antecknings bok i Azure Notebooks för hands version

I den här snabb starten skapar du och kör en Jupyter Notebook på Azure Notebooks och delar sedan den bärbara datorn med andra. Med Jupyter kan du enkelt kombinera markdown text, körbar kod, beständiga data, grafik och visualiseringar på en enda delbar arbets yta, antecknings boken. Azure Notebooks är en kostnadsfri värdbaserad tjänst som används för att utveckla och köra Jupyter Notebooks i molnet utan installation.

## <a name="prerequisites"></a>Förutsättningar
Inga.

## <a name="create-a-new-project-and-notebook"></a>Skapa ett nytt projekt och en ny antecknings bok

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Gå till [Azure Notebooks-platsen (https://notebooks.azure.com)](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **+ nytt projekt** (kortkommando: n). Knappen får bara visas som **+** om webbläsarfönstret är smalare:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I popup-fönstret **Skapa nytt projekt** som visas anger eller anger du följande information och väljer sedan **skapa**:

   - **Projekt namn**: Hello World i python
   - **Projekt-ID**: Hello – världen – python
   - **Offentligt projekt**: (avmarkerat)
   - **Skapa en README.MD**: (avmarkerad)

     ![Nya projekt popup-fönster med ifyllt information](media/quickstarts/new-project-popup.png)

1. Efter en liten stund navigerar du till det nya projektet i Azure-anteckningsböcker. Lägg till en antecknings bok i projektet genom att markera List rutan **+ ny** (som kan visas som endast **+** ) och sedan välja **antecknings bok**:

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. I popup-fönstret **Skapa ny antecknings bok** visas anger du ett namn för din bärbara dator, till exempel *HelloWorldInPython. ipynb* ( *. ipynb* betyder ironpython (Jupyter) Notebook) och väljer **python 3,6** för språket (kallas även för *kernel*):

    ![Skapa en ny anteckningsbok popup-fönstret](media/quickstarts/new-notebook-popup.png)

1. Välj **ny** för att slutföra skapandet av antecknings boken, som sedan visas i projektets fil lista:

    ![Ny anteckningsbok som visas i listan över filer i projektets](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Köra anteckningsboken

1. Välj ny anteckningsboken för att köra den i Redigeraren; kernel som du har valt (Python 3.6 i det här exemplet) aktiveras automatiskt för den här anteckningsboken:

    ![Vy av en ny anteckningsbok i Azure-anteckningsböcker](media/quickstarts/create-notebook-first-open.png)

1. Som standard har en tom kodcell i anteckningsboken. Om du vill ändra cell typen till **markdown**, använder du List rutan cell typ för att välja **markdown**:

    ![Ändra typen cell i en ny notebook](media/quickstarts/create-notebook-cell-type.png)

1. Skriv eller klistra in följande rubriktext i cellen:

    ```markdown
    # Hello World in Python
    ```

1. Eftersom du redigerar Markdown kan visas texten som en rubrik med ”#”. Om du vill rendera markdown i HTML väljer du knappen **Kör** . Azure anteckningsböcker automatiskt skapar sedan en ny kodcell efteråt:

    ![Knappen Kör för en cell och renderade Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Ange följande kod för Python i kodcellen:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Kör koden genom att välja **Kör** (kortkommando: Shift + Retur). Under cellen bör du se lyckade utdata som liknar följande text:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Välj Spara ikon för att spara ditt arbete:

    ![Spara ikonen i verktygsfältet Jupyter notebook](media/quickstarts/hello-results-save-icon.png)

1. Välj **filen** > **Stäng och** stoppa meny kommandot för att stoppa servern och stänga webbläsarfönstret.

## <a name="share-the-notebook"></a>Dela anteckningsboken

Om du vill dela din bärbara dator går du tillbaka till projekt sidan om det behövs, högerklickar på anteckningsbok-filen, väljer **Kopiera länk** (kortkommando: y) och klistrar in länken i ett lämpligt meddelande (e-post, snabb meddelanden osv.).

På projekt sidan kan du också använda **delnings** menyn för att hämta en länk, skapa ett e-postmeddelande med länken eller hämta HTML-och markdown inbäddnings kod:

![Kommandot share för projektet](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa och kör en Jupyter-anteckningsbok för linjär regression](tutorial-create-run-jupyter-notebook.md)
