---
title: Skapa och dela en Jupyter-anteckningsbok i förhandsversionen av Azure Notebooks
description: Skapa och kör snabbt en Jupyter-anteckningsbok i förhandsversionen av Azure Notebooks och dela sedan anteckningsboken med andra.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: d3310444fa28240b8fb1344199514a9601a2c615
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064452"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Snabbstart: Skapa och dela en anteckningsbok i förhandsversionen av Azure Notebooks

I den här snabbstarten skapar och kör du en Jupyter-anteckningsbok på Azure Notebooks och delar sedan den anteckningsboken med andra. Med Jupyter kan du enkelt kombinera Markdown-text, körbar kod, beständiga data, grafik och visualiseringar på en delbar arbetsyta, anteckningsboken. Azure Notebooks är en kostnadsfri värdbaserad tjänst som används för att utveckla och köra Jupyter Notebooks i molnet utan installation.

## <a name="prerequisites"></a>Krav
Inga.

## <a name="create-a-new-project-and-notebook"></a>Skapa ett nytt projekt och en ny anteckningsbok

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Gå till [webbplatsen förhttps://notebooks.azure.com) Azure Notebooks (](https://notebooks.azure.com) och logga in. Mer information finns [i Snabbstart - Logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md).

1. På din offentliga profilsida väljer du **Mina projekt** högst upp på sidan:

    ![Länken Mina projekt visas högst upp i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du + **Nytt projekt** (kortkommando: n). Knappen kan bara **+** visas som om webbläsarfönstret är smalt:

    ![Nytt projektkommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. Ange eller ange följande information **i popup-fönstret Skapa nytt projekt** som visas och välj sedan **Skapa:**

   - **Projektnamn**: Hello World i Python
   - **Projekt-ID:** hello-world-python
   - **Offentligt projekt**: (rensat)
   - **Skapa en README.md:**(rensad)

     ![Nytt projekt popup med befolkade detaljer](media/quickstarts/new-project-popup.png)

1. Efter en stund navigerar Azure Notebooks dig till det nya projektet. Lägg till en anteckningsbok i projektet genom att välja listrutan **+**+ **Ny** (som bara kan visas) och sedan välja **Anteckningsbok:**

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. I **popup-fönstret Skapa ny anteckningsbok** som visas anger du ett filnamn för din bärbara dator, till exempel *HelloWorldInPython.ipynb* (*.ipynb* means IronPython (Jupyter) Notebook) och väljer **Python 3.6** för språket (kallas även *kärnan):*

    ![Popup-fönstret Skapa ny anteckningsbok](media/quickstarts/new-notebook-popup.png)

1. Välj **Ny** om du vill slutföra skapandet av anteckningsboken, som sedan visas i projektets fillista:

    ![Ny anteckningsbok som visas i projektets fillista](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Köra anteckningsboken

1. Välj den nya anteckningsboken för att köra den i redigeraren. kärnan du valde (Python 3.6 i det här exemplet) aktiveras automatiskt för den här anteckningsboken:

    ![Vy över en ny anteckningsbok i Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Som standard har anteckningsboken en tom kodcell. Om du vill ändra celltypen till **Markdown**använder du listrutan celltyp för att välja **Markdown:**

    ![Ändra celltypen i en ny anteckningsbok](media/quickstarts/create-notebook-cell-type.png)

1. Ange eller klistra in följande rubriktext i cellen:

    ```markdown
    # Hello World in Python
    ```

1. Eftersom du redigerar Markdown visas texten som en rubrik med "#". Om du vill rendera markeringen i HTML markerar du knappen **Kör.** Azure-anteckningsböcker skapar sedan automatiskt en ny kodcell efteråt:

    ![Körknappen för en cell och renderad Markdown](media/quickstarts/run-cell-markdown-render.png)

1. I kodcellen anger du följande Python-kod:

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Välj **Kör** (kortkommando: Skift+Retur) om du vill köra koden. Under cellen bör du se lyckade utdata som liknar följande text:

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Välj spara ikonen för att spara ditt arbete:

    ![Ikonen Spara i verktygsfältet Jupyters anteckningsbok](media/quickstarts/hello-results-save-icon.png)

1. Välj **menykommandot** > **Filsträn och Stoppa** om du vill stoppa servern och stänga webbläsarfönstret.

## <a name="share-the-notebook"></a>Dela anteckningsboken

Om du vill dela anteckningsboken växlar du tillbaka till projektsidan om det behövs, högerklickar på anteckningsboksfilen, väljer **Kopiera länk** (kortkommando: y) och klistrar in länken i ett lämpligt meddelande (e-post, snabbmeddelanden osv.).

På projektsidan kan du också använda **Menyn Dela** för att hämta en länk, skapa ett e-postmeddelande med länken eller hämta HTML- och Markdown-inbäddningskod:

![Kommandot Projektdelning](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa och kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
