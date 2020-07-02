---
title: Exportera ett Jupyter Notebook-projekt från Azure Notebooks för hands version
description: Exportera snabbt ett Jupyter Notebook-projekt.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: 31e32a6ebb39429078c7c6747bc6b10f177699ac
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802725"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Snabb start: exportera ett Jupyter Notebook-projekt i Azure Notebooks för hands version

I den här snabb starten ska du ladda ned ett Azure Notebooks-projekt för användning i andra Jupyter Notebook-lösningar. 

## <a name="prerequisites"></a>Krav
Ett befintligt Azure Notebooks-projekt.

## <a name="export-an-azure-notebooks-project"></a>Exportera ett Azure Notebooks-projekt

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Länken Mina projekt överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. Välj ett projekt.
1. Klicka på Ladda ned "Ladda ned" om du vill utlösa hämtningen av en zip-fil som innehåller alla projektfiler.
1. Du kan också klicka på knappen "Ladda ned projekt" på sidan för att ladda ned alla filer för ett specifikt projekt.

När du har hämtat dina projektfiler kan du använda dem med andra Jupyter Notebook-lösningar. Vissa alternativ som beskrivs i avsnitten nedan är: 
- [Visual Studio-koden](#use-notebooks-in-visual-studio-code)
- [Visual Studio-Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="download-the-requirements-file-used-by-azure-notebooks"></a>Hämta filen med krav som används av Azure Notebooks

Om du vill skapa en miljö som matchar Azure Notebooks för hands versionen kan du använda **AzureNotebooksRequirements.txt** filen som finns i GitHub.

1. Navigera till Azure Notebooks [GitHub-lagringsplatsen](https://github.com/microsoft/AzureNotebooks) eller så kan du [komma åt filen direkt](https://aka.ms/aznbrequirementstxt).
1. Ladda ned **AzureNotebooksRequirements.txt** -filen som innehåller paket beroenden för Azure Notebookss miljön. 
1. Från en kommando tolk navigerar du till den katalog som du vill använda för dina projekt och använder krav filen för att installera lämpliga paket.

    > [!Note]
    > Det är inte obligatoriskt, vanligt vis vill du skapa en ny virtuell miljö för paket installation enligt nedan.

    ```bash
    cd your-project-dir
    python -m venv .venv
    python -m pip install -r AzureNotebooksRequirements.txt
    ```

Mer information om filer för krav finns i [pip-dokumenten]( https://pip.pypa.io/en/stable/user_guide/#requirements-files).

## <a name="use-notebooks-in-visual-studio-code"></a>Använda antecknings böcker i Visual Studio Code

[Vs Code](https://code.visualstudio.com/) är en kostnads fri kod redigerare som du kan använda lokalt eller ansluten till fjärrdatorer. Tillsammans med python-tillägget erbjuder den en fullständig miljö för python-utveckling, inklusive en omfattande inbyggd upplevelse för att arbeta med Jupyter-anteckningsböcker. 

![VS Code Jupyter Notebook support](media/vs-code-jupyter-notebook.png)

När du har [laddat ned](#export-an-azure-notebooks-project) dina projektfiler kan du använda vs Code. Anvisningar om hur du använder VS Code med Jupyter Notebooks finns i avsnittet [arbeta med Jupyter-anteckningsböcker i Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) och [data science i självstudier för Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial) .

Du kan också använda [Azure Notebooks krav](#download-the-requirements-file-used-by-azure-notebooks) med Visual Studio Code för att skapa en miljö som matchar Azure Notebooks för hands versionen.

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Använda antecknings böcker i Visual Studio Codespaces

Visual Studio Codespaces innehåller miljöer där du kan redigera dina antecknings böcker med Visual Studio Code eller webbläsaren. Den ger samma fantastiska Jupyter-upplevelse som VS-kod, men utan att behöva installera något på din enhet. Om du inte vill konfigurera en lokal miljö och föredra en molnbaserad lösning, är det ett bra alternativ att skapa en codespace. Så här kommer du igång:

1. [Hämta](#export-an-azure-notebooks-project) dina projektfiler.
1. [Skapa en GitHub-lagringsplats](https://help.github.com/github/getting-started-with-github/create-a-repo) för att lagra dina antecknings böcker. 
1. [Lägg till dina filer](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) till lagrings platsen.
1. Konfigurera Visual Studio-Codespaces i [webbläsaren](https://docs.microsoft.com/visualstudio/online/how-to/browser), [Visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside)eller [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode).

## <a name="use-notebooks-with-azure-machine-learning"></a>Använda antecknings böcker med Azure Machine Learning

Azure Machine Learning tillhandahåller en maskin inlärnings plattform från slut punkt till slut punkt som gör det möjligt för användare att skapa och distribuera modeller snabbare på Azure. Med Azure ML kan du köra Jupyter-anteckningsböcker på en virtuell dator eller en delad kluster dator miljö. Om du behöver en molnbaserad lösning för din ML-arbets belastning med experiment spårning, data hantering med mera, rekommenderar vi Azure Machine Learning. Komma igång med Azure ML:

1. [Hämta](#export-an-azure-notebooks-project) dina projektfiler.
1. [Skapa en arbets yta](../machine-learning/how-to-manage-workspace.md) i Azure Portal.

   ![Skapar en arbetsyta](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Öppna [Azure Studio (för hands version)](https://ml.azure.com/).
1. Välj **antecknings böcker**i navigerings fältet på vänster sida.
1. Klicka på knappen **överför filer** och överför projektfilerna som du laddade ned från Azure Notebooks.

Om du vill ha mer information om Azure ML och köra Jupyter-anteckningsböcker kan du läsa [dokumentationen](../machine-learning/how-to-run-jupyter-notebooks.md) eller testa [introduktionen för att Machine Learning](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) modul på Microsoft Learn.


## <a name="use-azure-lab-services"></a>Använd Azure Lab Services

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) ge lärare möjlighet att enkelt konfigurera och tillhandahålla åtkomst på begäran till förkonfigurerade virtuella datorer för ett helt klass rum. Om du är ute efter ett sätt att arbeta med Jupyter-anteckningsböcker och moln beräkning i en skräddarsydd klass rums miljö är Lab-tjänster ett bra alternativ.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 När du har [hämtat](#export-an-azure-notebooks-project) dina projektfiler kan du använda dem med Azure Lab Services. Anvisningar om hur du konfigurerar ett labb finns i [Konfigurera ett labb för att lära data vetenskap med python-och Jupyter-anteckningsböcker](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Använd GitHub

GitHub tillhandahåller ett kostnads fritt, käll kontrolls sätt att lagra antecknings böcker (och andra filer), dela dina antecknings böcker med andra och samar beta. Om du letar efter ett sätt att dela dina projekt och samar beta med andra, är GitHub ett bra alternativ och kan kombineras med [Visual Studio-Codespaces](#use-notebooks-in-visual-studio-codespaces) för en bra utvecklings upplevelse. Komma igång med GitHub

1. [Hämta](#export-an-azure-notebooks-project) dina projektfiler.
1. [Skapa en GitHub-lagringsplats](https://help.github.com/github/getting-started-with-github/create-a-repo) för att lagra dina antecknings böcker. 
1. [Lägg till dina filer](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) till lagrings platsen.
