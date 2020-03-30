---
title: Skapa ett azure notebooks preview-projekt med en anpassad miljö
description: Skapa ett nytt projekt i förhandsversionen av Azure Notebooks som är konfigurerat med en specifik uppsättning installerade paket och startskript.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196849"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Snabbstart: Skapa ett projekt med en anpassad miljö i förhandsversionen av Azure Notebooks

Ett projekt i Azure Notebooks är en samling filer, till exempel anteckningsböcker, datafiler, dokumentation, avbildningar och så vidare, tillsammans med en miljö som kan konfigureras med specifika inställningskommandon. Genom att definiera miljön med projektet har alla som klonar projektet till sitt eget Azure Notebooks-konto all information de behöver för att återskapa den nödvändiga miljön.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Skapa ett projekt

1. Gå till [Azure-anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns [i Snabbstart - Logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. På din offentliga profilsida väljer du **Mina projekt** högst upp på sidan:

    ![Länken Mina projekt visas högst upp i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du + **Nytt projekt** (kortkommando: n); knappen får bara **+** visas som om webbläsarfönstret är smalt:

    ![Nytt projektkommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. Ange eller ange följande information **i popup-fönstret Skapa nytt projekt** som visas och välj sedan **Skapa:**

    - **Projektnamn**: Projekt med en anpassad miljö
    - **Projekt-ID:** projektanpassad miljö
    - **Offentligt projekt**: (rensat)
    - **Skapa en README.md:**(rensad)

1. Efter en stund navigerar Azure Notebooks dig till det nya projektet. Lägg till en anteckningsbok i projektet genom att välja listrutan **+**+ **Ny** (som bara kan visas) och sedan välja **Anteckningsbok**.

1. Ge anteckningsboken ett namn som *Anpassad environment.ipynb*, välj **Python 3.6** för språket och välj **Nytt**.

## <a name="add-a-custom-setup-step"></a>Lägga till ett anpassat installationssteg

1. Välj **Projektinställningar**på projektsidan .

    ![Kommandot Projektinställningar](media/quickstarts/project-settings-command.png)

1. I popup-fönstret **Projektinställningar** väljer du fliken **Miljö** och väljer sedan **+ Lägg till**under Steg för **miljöinställningar:**

    ![Kommando för att lägga till ett nytt miljöinställningssteg](media/quickstarts/environment-add-command.png)

1. Kommandot **+ Lägg till** skapar ett steg som definieras av en åtgärd och en målfil som har valts från filerna i projektet. Följande åtgärder stöds:

   | Åtgärd | Beskrivning |
   | --- | --- |
   | Krav.txt | Python-projekt definierar sina beroenden i en requirements.txt-fil. Med det här alternativet väljer du lämplig fil i projektets fillista och väljer även Python-versionen i den ytterligare listrutan som visas. Om det behövs väljer du **Avbryt** om du vill återgå till projektet, ladda upp eller skapa filen, gå sedan tillbaka till fliken**Miljö** **i Project Settings** > och skapa ett nytt steg. Med det här steget körs en anteckningsbok i projektet automatiskt`pip install -r <file>` |
   | Shell-skript | Används för att ange ett bash shell-skript (vanligtvis en fil med *tillägget .sh)* som innehåller alla kommandon som du vill köra för att initiera miljön. |
   | Miljö.yml | Ett Python-projekt som använder conda för att hantera en miljö använder en *environments.yml-fil* för att beskriva beroenden. Med det här alternativet väljer du lämplig fil i projektets fillista. |

   > [!WARNING]
   > Eftersom det här är en förhandsversionstjänst under `Environment.yml` utveckling finns det för närvarande ett känt problem där inställningen inte tillämpas på projektet som förväntat. Projektet och Jupyter-anteckningsböckerna inom läser för närvarande inte in den angivna miljöfilen.

1. Om du vill ta bort alla inställningssteg markerar du **X** till höger om steget.

1. När alla installationssteg är på plats väljer du **Spara**. (Välj **Avbryt** om du vill ignorera ändringar).

1. Om du vill testa din miljö skapar och kör du en ny anteckningsbok och skapar sedan en `import` kodcell med satser som är beroende av ett paket i miljön, till exempel med hjälp av en Python-sats. Om satsen lyckas installerades det nödvändiga paketet i miljön.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera och konfigurera projekt i Azure Notebooks](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Självstudiekurs: skapa en kör en Jupyter anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
