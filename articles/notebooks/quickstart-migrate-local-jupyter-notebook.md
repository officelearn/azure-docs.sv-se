---
title: Migrera en lokal Jupyter-anteckningsbok till förhandsversionen av Azure-anteckningsböcker
description: Överför snabbt en Jupyter-anteckningsbok till förhandsversionen av Azure Notebooks från den lokala datorn eller en webb-URL och dela den sedan för samarbete.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064333"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Snabbstart: Migrera en lokal Jupyter-anteckningsbok i förhandsversionen av Azure Notebooks

I den här snabbstarten migrerar du en Jupyter-anteckningsbok från den lokala datorn eller en annan tillgänglig fil-URL till Azure Notebooks. 

Jupyter bärbara datorer på din egen dator är endast tillgängliga för dig. Du kan dela dina filer, men mottagarna har sedan sina egna lokala kopior av anteckningsboken och det är svårt att införliva ändringarna. Även om du lagrar anteckningsböcker i en delad onlinedatabas som GitHub måste varje medarbetare ha en lokal Jupyter-installation konfigurerad som din.

Genom att migrera dina lokala eller databasbaserade anteckningsböcker till Azure Notebooks kan du omedelbart dela dem med dina medarbetare, som bara behöver en webbläsare för att visa och köra dina anteckningsböcker. Om de loggar in på Azure Notebooks kan de också göra ändringar.

## <a name="prerequisites"></a>Krav

- En [Jupyter-anteckningsbok](https://jupyter-notebook.readthedocs.io) på den lokala datorn eller på en annan tillgänglig fil-URL. 

## <a name="create-a-project-on-azure-notebooks"></a>Skapa ett projekt på Azure Notebooks

Den här snabbstarten visar att du migrerar en anteckningsbok från den lokala datorn eller en annan tillgänglig fil-URL. Mer om du vill migrera anteckningsböcker från en GitHub-databas finns i [Snabbstart: Klona en anteckningsbok](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Gå till [Azure-anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns [i Snabbstart - Logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. På din offentliga profilsida väljer du **Mina projekt** högst upp på sidan:

    ![Länken Mina projekt visas högst upp i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **Nytt projekt** (kortkommando: n). Knappen kan bara **+** visas som om webbläsarfönstret är smalt:

    ![Nytt projektkommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I **popup-fönstret Skapa nytt projekt** som visas anger du lämpliga värden för den anteckningsbok som du migrerar i fälten **Projektnamn** och **Projekt-ID,** avmarkerar alternativen för **Offentligt projekt** och **Skapa en README.md**och väljer sedan **Skapa**.

## <a name="upload-the-local-notebook"></a>Ladda upp den lokala anteckningsboken

1. På projektsidan väljer du **Ladda upp** (som bara kan visas som en upppil om webbläsarfönstret är litet) och väljer sedan 1. I popup-fönstret som visas väljer du **Från dator** om anteckningsboken finns i det lokala filsystemet eller **Från URL:en** om anteckningsboken finns online:

    ![Kommando för att ladda upp en anteckningsbok från en URL eller den lokala datorn](media/quickstarts/upload-from-computer-url-command.png)

   Om anteckningsboken finns i en GitHub-databas följer du stegen i [Snabbstart: Klona en anteckningsbok](quickstart-clone-jupyter-notebook.md) i stället.

   - Om du använder **Från dator**drar och släpper du *IPynb-filerna* i popup-fönstret eller väljer **Välj filer**och bläddrar sedan till och markerar de filer du vill importera. Välj sedan **Ladda upp**. De uppladdade filerna får samma namn som de lokala filerna. Du behöver inte ladda upp innehållet i *några .ipynb_checkpoints* mappar.

     ![Ladda upp från datorns popup](media/quickstarts/upload-from-computer-popup.png)

   - Om du använder **Från URL**anger du källadressen i fältet **Fil-URL** och det filnamn som ska tilldelas anteckningsboken i projektet i fältet **Filnamn.** Välj sedan **Ladda upp**. Om du har flera filer med separata webbadresser använder du kommandot Lägg till **fil** för att kontrollera den första URL:en du angav, varefter popup-fönstret innehåller nya fält för en annan fil.

     ![Ladda upp från URL-popup](media/quickstarts/upload-from-url-popup.png)

1. Öppna och kör den nyligen uppladdade anteckningsboken för att verifiera dess innehåll och funktion. När du är klar väljer du **Filstopp** > **och stänger** den för att stänga anteckningsboken.

1. Om du vill dela en länk till den uppladdade anteckningsboken högerklickar du på filen i projektet och väljer **Kopiera länk** (kortkommando: y) och klistrar sedan in länken i rätt meddelande. Alternativt kan du dela projektet som helhet med hjälp av **kontrollen Dela** på projektsidan.

1. Om du vill redigera andra filer än anteckningsböcker högerklickar du på filen i projektet och väljer **Redigera fil** (kortkommando: i). Standardåtgärden **Run** (kortkommando: r) visar bara filinnehållet och tillåter inte redigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Skapa och kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
