---
title: Skapa ett Azure Notebooks för hands versions projekt med en anpassad miljö
description: Skapa ett nytt projekt i Azure Notebooks för hands versionen som är konfigurerad med en speciell uppsättning installerade paket och start skript.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196849"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Snabb start: skapa ett projekt med en anpassad miljö i Azure Notebooks för hands version

Ett projekt i Azure-datorer är en samling av filer, till exempel bärbara datorer, filer, dokumentation, bilder och så vidare, tillsammans med en miljö som kan konfigureras med specifika installationsprogrammet kommandon. Vem som helst som klonar projektet till sina egna anteckningsböcker i Azure-konto har all information som de behöver för att återskapa den nödvändiga miljön genom att definiera miljön med projektet.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Skapa ett projekt

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. (Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **+ nytt projekt** (kortkommando: n); knappen får bara visas som **+** om webbläsarfönstret är smalare:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I popup-fönstret **Skapa nytt projekt** som visas anger eller anger du följande information och väljer sedan **skapa**:

    - **Projekt namn**: projekt med en anpassad miljö
    - **Projekt-ID**: projekt-anpassad – miljö
    - **Offentligt projekt**: (avmarkerat)
    - **Skapa en README.MD**: (avmarkerad)

1. Efter en liten stund navigerar du till det nya projektet i Azure-anteckningsböcker. Lägg till en antecknings bok i projektet genom att markera List rutan **+ ny** (som kan visas som endast **+** ) och sedan välja **antecknings bok**.

1. Ge antecknings boken ett namn som *anpassad miljö. ipynb*, välj **python 3,6** för språket och välj **ny**.

## <a name="add-a-custom-setup-step"></a>Lägg till en anpassad installation steg

1. På sidan projekt väljer du **projekt inställningar**.

    ![Kommandot för projekt-settings](media/quickstarts/project-settings-command.png)

1. I popup-fönstret **projekt inställningar** väljer du fliken **miljö** . under **miljö konfigurations steg**väljer du **+ Lägg till**:

    ![Att lägga till en ny miljö installationssteget](media/quickstarts/environment-add-command.png)

1. Kommandot **+ Add** skapar ett steg som definieras av en åtgärd och en målfil som väljs från filerna i projektet. Följande åtgärder stöds:

   | Åtgärd | Beskrivning |
   | --- | --- |
   | Requirements.txt | Python-projekt definiera beroenden i en requirements.txt-fil. Markera rätt fil från listan över filer i projektets med det här alternativet, och även välja Python-version i ytterligare listrutan som visas. Om det behövs väljer du **Avbryt** för att återgå till projektet, laddar upp eller skapar filen och återgår sedan till fliken **projekt inställningar** > - **miljö** och skapar ett nytt steg. Med det här steget på plats körs en antecknings bok i projektet automatiskt `pip install -r <file>` |
   | Shell-skript | Används för att ange ett bash-gränssnitts skript (vanligt vis en fil med fil namns tillägget *. sh* ) som innehåller alla kommandon som du vill köra för att initiera miljön. |
   | Environment.yml | Ett python-projekt som använder Conda för att hantera en miljö använder en miljö *. yml* -fil för att beskriva beroenden. Markera rätt fil från listan över filer i projektets med det här alternativet. |

   > [!WARNING]
   > Eftersom det här är en för hands versions tjänst under utveckling finns det ett känt problem där `Environment.yml` inställningen inte tillämpas på projektet som förväntat. Projektet och Jupyter-anteckningsbokarna i läser inte in den angivna miljö filen för närvarande.

1. Om du vill ta bort ett installations steg väljer du **X** till höger om steget.

1. När alla installations steg är på plats väljer du **Spara**. (Välj **Avbryt** om du vill ignorera ändringarna).

1. Testa din miljö genom att skapa och köra en ny antecknings bok och sedan skapa en Code-cell med instruktioner som är beroende av ett paket i miljön, till exempel att använda en python `import`-instruktion. Om instruktionen lyckas, har sedan det nödvändiga paketet installerats i miljön.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera och konfigurera projekt i Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Självstudie: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
