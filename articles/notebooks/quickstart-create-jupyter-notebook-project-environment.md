---
title: Skapa ett projekt med Azure-anteckningsböcker med anpassad miljö
description: Skapa ett nytt projekt i Azure-anteckningsböcker som har konfigurerats med en specifik uppsättning installerade paket och startskript.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 368bc0402e56479ad8696ca9ed0702f246a670bd
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262014"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Snabbstart: Skapa ett projekt med en anpassad miljö

Ett projekt i Azure-datorer är en samling av filer, till exempel bärbara datorer, filer, dokumentation, bilder och så vidare, tillsammans med en miljö som kan konfigureras med specifika installationsprogrammet kommandon. Vem som helst som klonar projektet till sina egna anteckningsböcker i Azure-konto har all information som de behöver för att återskapa den nödvändiga miljön genom att definiera miljön med projektet.

## <a name="create-a-project"></a>Skapa ett projekt

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns i [Snabbstart – logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. Din offentliga profil-sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På den **Mina projekt** väljer **+ nytt projekt** (kortkommandot: n); knappen kan visas endast som **+** om webbläsaren är smal:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I den **Skapa nytt projekt** popup-fönstret som visas, ange eller ange följande information och välj sedan **skapa**:

    - **Projektnamnet**: Projektet med anpassad miljö
    - **Projektet ID**: projekt-anpassad-miljö
    - **Offentliga projekt**: (avmarkerad)
    - **Skapa en README.md**: (avmarkerad)

1. Efter en liten stund navigerar du till det nya projektet i Azure-anteckningsböcker. Lägg till en anteckningsbok i projektet genom att välja den **+ ny** listrutan (som kan visas bara **+**), sedan välja **Notebook**.

1. Ge anteckningsboken ett namn som liknar *anpassade environment.ipynb*väljer **Python 3.6** för språk och väljer **New**.

## <a name="add-a-custom-setup-step"></a>Lägg till en anpassad installation steg

1. På projektsidan väljer **Projektinställningar**.

    ![Kommandot för projekt-settings](media/quickstarts/project-settings-command.png)

1. I den **Projektinställningar** popup-fönstret väljer den **miljö** fliken sedan under **steg för konfiguration av miljö**väljer **+ Lägg till**:

    ![Att lägga till en ny miljö installationssteget](media/quickstarts/environment-add-command.png)

1. Den **+ Lägg till** kommandot skapar ett steg som definieras av en åtgärd och en målfil som väljs från filerna i projektet. Följande åtgärder stöds:

    | Åtgärd | Beskrivning |
    | --- | --- |
    | Requirements.txt | Python-projekt definiera beroenden i en requirements.txt-fil. Markera rätt fil från listan över filer i projektets med det här alternativet, och även välja Python-version i ytterligare listrutan som visas. Om det behövs väljer **Avbryt** för att återgå till projektet, ladda upp eller skapa filen och sedan gå tillbaka till den **Projektinställningar** > **miljö** fliken och skapa en nytt steg. Det här steget på plats körs som kör en anteckningsbok i projektet automatiskt `pip install -r <file>` |
    | Shell-skript | Används för att indikera ett bash-kommandoskript (vanligtvis en fil med det *.sh* tillägget) som innehåller alla kommandon som du vill köra för att initiera miljön. |
    | Environment.yml | Ett Python-projekt som använder conda för hantering av en miljö använder en *environments.yml* fil att beskriva beroenden. Markera rätt fil från listan över filer i projektets med det här alternativet. |

1. Om du vill ta bort något steg i installationen, Välj den **X** till höger om steget.

1. När alla konfigurationssteg är på plats, Välj **spara**. (Välj **Avbryt** ignorera ändringar).

1. Om du vill testa din miljö, skapa och köra en ny anteckningsbok och sedan skapa en kodcell med instruktioner som är beroende av ett paket i miljön, till exempel med hjälp av ett Python `import` instruktionen. Om instruktionen lyckas, har sedan det nödvändiga paketet installerats i miljön.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera och konfigurera projekt i Azure-anteckningsböcker](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Självstudier: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
