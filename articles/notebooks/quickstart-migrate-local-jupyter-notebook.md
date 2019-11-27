---
title: Migrera en lokal Jupyter notebook till Azure-anteckningsböcker
description: Snabbt överföra en Jupyter-anteckningsbok till Azure-datorer från din lokala dator eller en URL och sedan dela den för samarbete.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 3b7d5aab05923fb4356e0d45aaf21a77076a870a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277473"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Snabbstart: Migrera en lokal Jupyter-anteckningsbok

Jupyter-anteckningsböcker som du skapar lokalt på din egen dator är bara tillgängliga för dig. Du kan dela dina filer via en mängd olika sätt, men sedan mottagare har sina egna lokala kopia av anteckningsboken och det är svårt att inga ändringar som de kan göra. Du kan också lagra anteckningsböcker i en delad databas som är online, till exempel GitHub, men detta kräver fortfarande att varje medarbetare har sina egna lokal Jupyter-installation med samma konfiguration som din.

Genom att migrera dina lokala eller databasen-baserade anteckningsböcker till Azure-datorer kan lagra du dem i molnet från vilken du kan direkt dela dem med dina medarbetare. Dessa medarbetare behöver bara en webbläsare för att visa och köra din antecknings bok, och om de [loggar in](quickstart-sign-in-azure-notebooks.md) på Azure Notebooks kan de också göra ändringar.

Den här snabbstarten visar hur du migrerar en bärbar dator från den lokala datorn eller en annan tillgänglig fil-URL. Om du vill migrera antecknings böcker från en GitHub-lagringsplats, se [snabb start: klona en bärbar dator](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Skapa ett projekt i Azure-anteckningsböcker

1. Gå till [Azure Notebooks](https://notebooks.azure.com) och logga in. (Mer information finns i [snabb start – logga in till Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Från din offentliga profil sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På sidan **Mina projekt** väljer du **+ nytt projekt** (kortkommando: n); knappen får bara visas som **+** om webbläsarfönstret är smalare:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I popup-fönstret **Skapa nytt projekt** som visas anger du lämpliga värden för den antecknings bok som du migrerar i fälten **projekt namn** och **projekt-ID** , tar bort alternativen för det **offentliga projektet** och **skapar en README.MD**och väljer sedan **skapa**.

## <a name="upload-the-local-notebook"></a>Ladda upp lokala anteckningsboken

1. På sidan projekt väljer du **överför** (som kan visas som en UPPIL endast om webbläsarfönstret är liten) och väljer sedan 1. I popup-fönstret som visas väljer du **från dator** om antecknings boken finns i det lokala fil systemet eller **från URL** : en om din bärbara dator finns online:

    ![Kommandot för att överföra en bärbar dator från en URL eller den lokala datorn](media/quickstarts/upload-from-computer-url-command.png)

   (Om din bärbara dator finns i en GitHub-lagringsplats följer du stegen i [snabb start: klona en antecknings bok](quickstart-clone-jupyter-notebook.md) i stället.)

   - Om du använder **från dator**drar du och släpper dina *. ipynb* -filer till popup-fönstret, eller väljer **Välj filer**och bläddrar sedan till och väljer de filer som du vill importera. Välj sedan **Ladda upp**. De överförda filerna får samma namn som de lokala filerna. (Du behöver inte ladda upp innehållet i några *. ipynb_checkpoints* mappar.)

     ![Överför från datorn popup-fönstret](media/quickstarts/upload-from-computer-popup.png)

   - Om du använder **från URL**anger du käll adressen i fältet **fil-URL** och fil namnet som ska tilldelas antecknings boken i ditt projekt i fältet **fil namn** . Välj sedan **Ladda upp**. Om du har flera filer med separata URL: er, använder du kommandot **+ Lägg till fil** för att kontrol lera den första URL som du angav, efter vilken popup-fönstret innehåller nya fält för en annan fil.

     ![Ladda upp från URL: en popup-fönstret](media/quickstarts/upload-from-url-popup.png)

1. Öppna och kör din nyligen uppladdade anteckningsboken för att verifiera dess innehåll och åtgärden. När du är klar väljer du **fil** > **stanna och stänger** för att stänga antecknings boken.

1. Om du vill dela en länk till din uppladdade antecknings bok högerklickar du på filen i projektet och väljer **Kopiera länk** (kortkommando: y) och klistrar sedan in länken i lämpligt meddelande. Alternativt kan du dela projektet som helhet med hjälp av **resurs** kontrollen på projekt sidan.

1. Om du vill redigera andra filer än antecknings böcker högerklickar du på filen i projektet och väljer **Redigera fil** (kortkommando: i). Standard åtgärden **Kör** (tangent bords gen väg: r), visar bara fil innehållet och tillåter inte redigering.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
