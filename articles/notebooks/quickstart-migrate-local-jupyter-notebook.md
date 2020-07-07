---
title: Migrera en lokal Jupyter-anteckningsbok till Azure Notebooks för hands version
description: Du kan snabbt överföra en Jupyter-anteckningsbok till Azure Notebooks för hands version från din lokala dator eller en webb-URL och sedan dela den för samarbete.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9c961ad67260cee2809e0bba1e79f2c709183dea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85832125"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Snabb start: Migrera en lokal Jupyter-anteckningsbok i Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

I den här snabb starten migrerar du en Jupyter-anteckningsbok från den lokala datorn eller en annan tillgänglig fil-URL till Azure Notebooks. 

Jupyter-anteckningsböcker på din dator är bara tillgängliga för dig. Du kan dela dina filer, men mottagarna har sina egna lokala kopior av antecknings boken och det är svårt att införliva sina ändringar. Även om du lagrar antecknings böcker i en delad onlinebutik, till exempel GitHub, måste varje medarbetare ha en lokal Jupyter-installation som har kon figurer ATS som din.

Genom att migrera dina lokala eller databasbaserade antecknings böcker till Azure Notebooks kan du genast dela dem med dina medarbetare, som bara behöver en webbläsare för att visa och köra dina antecknings böcker. Om de loggar in på Azure Notebooks kan de också göra ändringar.

## <a name="prerequisites"></a>Förutsättningar

- En [Jupyter Notebook](https://jupyter-notebook.readthedocs.io) på din lokala dator eller en annan tillgänglig fil-URL. 

## <a name="create-a-project-on-azure-notebooks"></a>Skapa ett projekt på Azure Notebooks

Den här snabb starten visar migrering av en bärbar dator från den lokala datorn eller en annan tillgänglig fil-URL. Om du vill migrera antecknings böcker från en GitHub-lagringsplats, se [snabb start: klona en bärbar dator](quickstart-clone-jupyter-notebook.md).

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. (Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Länken Mina projekt överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **nytt projekt** (kortkommando: n). Knappen får bara visas som **+** om webbläsarfönstret är smalare:

    ![Kommandot nytt projekt på sidan mina projekt](media/quickstarts/new-project-command.png)

1. I popup-fönstret **Skapa nytt projekt** som visas anger du lämpliga värden för den antecknings bok som du migrerar i fälten **projekt namn** och **projekt-ID** , tar bort alternativen för det **offentliga projektet** och **skapar en README.MD**och väljer sedan **skapa**.

## <a name="upload-the-local-notebook"></a>Ladda upp den lokala antecknings boken

1. På sidan projekt väljer du **överför** (som kan visas som en UPPIL endast om webbläsarfönstret är liten) och väljer sedan 1. I popup-fönstret som visas väljer du **från dator** om antecknings boken finns i det lokala fil systemet eller **från URL** : en om din bärbara dator finns online:

    ![Kommando för att överföra en antecknings bok från en URL eller den lokala datorn](media/quickstarts/upload-from-computer-url-command.png)

   Om din bärbara dator finns i en GitHub-lagringsplats följer du stegen i [snabb start: klona en antecknings bok](quickstart-clone-jupyter-notebook.md) i stället.

   - Om du använder **från dator**drar du och släpper dina *. ipynb* -filer till popup-fönstret, eller väljer **Välj filer**och bläddrar sedan till och väljer de filer som du vill importera. Välj sedan **Ladda upp**. De överförda filerna får samma namn som de lokala filerna. Du behöver inte ladda upp innehållet i några *. ipynb_checkpoints* mappar.

     ![Popup-fönstret Ladda upp från dator](media/quickstarts/upload-from-computer-popup.png)

   - Om du använder **från URL**anger du käll adressen i fältet **fil-URL** och fil namnet som ska tilldelas antecknings boken i ditt projekt i fältet **fil namn** . Välj sedan **Ladda upp**. Om du har flera filer med separata URL: er, använder du kommandot **Lägg till fil** för att kontrol lera den första URL som du angav, efter vilken popup-fönstret innehåller nya fält för en annan fil.

     ![Popup-menyn Ladda upp från URL](media/quickstarts/upload-from-url-popup.png)

1. Öppna och kör den nyligen uppladdade antecknings boken för att verifiera innehållet och åtgärden. När du är klar väljer du **fil**  >  **stanna och stänger** för att stänga antecknings boken.

1. Om du vill dela en länk till din uppladdade antecknings bok högerklickar du på filen i projektet och väljer **Kopiera länk** (kortkommando: y) och klistrar sedan in länken i lämpligt meddelande. Alternativt kan du dela projektet som helhet med hjälp av **resurs** kontrollen på projekt sidan.

1. Om du vill redigera andra filer än antecknings böcker högerklickar du på filen i projektet och väljer **Redigera fil** (kortkommando: i). Standard åtgärden **Kör** (tangent bords gen väg: r), visar bara fil innehållet och tillåter inte redigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa och kör en Jupyter-anteckningsbok för linjär regression](tutorial-create-run-jupyter-notebook.md)
