---
title: Migrera en lokal Jupyter notebook till Azure-anteckningsböcker
description: Snabbt överföra en Jupyter-anteckningsbok till Azure-datorer från din lokala dator eller en URL och sedan dela den för samarbete.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2151d5b04b0fe5d47e8e2ddfb750ac279b6f74fa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970190"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Snabbstart: Migrera en lokal Jupyter Notebook

Jupyter-anteckningsböcker som du skapar lokalt på din egen dator är bara tillgängliga för dig. Du kan dela dina filer via en mängd olika sätt, men sedan mottagare har sina egna lokala kopia av anteckningsboken och det är svårt att inga ändringar som de kan göra. Du kan också lagra anteckningsböcker i en delad databas som är online, till exempel GitHub, men detta kräver fortfarande att varje medarbetare har sina egna lokal Jupyter-installation med samma konfiguration som din.

Genom att migrera dina lokala eller databasen-baserade anteckningsböcker till Azure-datorer kan lagra du dem i molnet från vilken du kan direkt dela dem med dina medarbetare. Dessa medarbetare behöver bara en webbläsare för att visa och köra din bärbara dator och om de [logga in](quickstart-sign-in-azure-notebooks.md) till Azure-datorer kan de också göra ändringar.

Den här snabbstarten visar hur du migrerar en bärbar dator från den lokala datorn eller en annan tillgänglig fil-URL. Information om hur du migrerar antecknings böcker från en GitHub-lagringsplats finns i [Quickstart: Klona en Notebook @ no__t-0.

## <a name="create-a-project-on-azure-notebooks"></a>Skapa ett projekt i Azure-anteckningsböcker

1. Gå till [Azure anteckningsböcker](https://notebooks.azure.com) och logga in. (Mer information finns i [Snabbstart – logga in på Azure-anteckningsböcker](quickstart-sign-in-azure-notebooks.md)).

1. Din offentliga profil-sida väljer du **Mina projekt** överst på sidan:

    ![Mina projekt länk överst i webbläsarfönstret](media/quickstarts/my-projects-link.png)

1. På den **Mina projekt** väljer **+ nytt projekt** (kortkommandot: n); knappen kan visas endast som **+** om webbläsaren är smal:

    ![Nytt projekt-kommando på sidan Mina projekt](media/quickstarts/new-project-command.png)

1. I den **Skapa nytt projekt** popup-fönstret som visas, ange lämpliga värden för den bärbara datorn som du migrerar i den **projektnamn** och **projekt-ID** fält, avmarkera de alternativ för **offentliga projekt** och **skapa en README.md**och välj sedan **skapa**.

## <a name="upload-the-local-notebook"></a>Ladda upp lokala anteckningsboken

1. På projektsidan väljer **överför** (som kan visas som ett dig pilen endast om ditt webbläsarfönster är liten) därefter 1. I popup-fönstret som visas väljer du **från datorn** om din bärbara dator finns på det lokala filsystemet eller **från URL: en** om din bärbara dator finns online:

    ![Kommandot för att överföra en bärbar dator från en URL eller den lokala datorn](media/quickstarts/upload-from-computer-url-command.png)

   (Om din bärbara dator finns i en GitHub-lagringsplats följer du stegen i [Quickstart: Klona en Notebook @ no__t-0 i stället.)

   - Om du använder **från datorn**, dra och släpp dina *.ipynb* filer i popup-fönstret eller välj **Välj filer**, bläddra till och välj de filer som du vill importera. Välj sedan **överför**. De överförda filerna får samma namn som de lokala filerna. (Du behöver inte ladda upp innehåll i *.ipynb_checkpoints* mappar.)

     ![Överför från datorn popup-fönstret](media/quickstarts/upload-from-computer-popup.png)

   - Om du använder **från URL: en**, ange källadressen i den **URL: en för filen** fältet och filnamnet för att tilldela till anteckningsboken i ditt projekt i den **filnamn** fältet. Välj sedan **överför**. Om du har flera filer med olika URL: er kan du använda den **+ Lägg till filen** kommando för att kontrollera den första Webbadress du angav, varefter popup-fönstret visar nya fält för en annan fil.

     ![Ladda upp från URL: en popup-fönstret](media/quickstarts/upload-from-url-popup.png)

1. Öppna och kör din nyligen uppladdade anteckningsboken för att verifiera dess innehåll och åtgärden. När du är klar väljer du **filen** > **stanna och Stäng** att Stäng anteckningsboken.

1. Högerklicka på filen i projektet och välj om du vill dela en länk till anteckningsboken överförda **Kopiera länk** (kortkommandot: y), klistra in länken i ett meddelande. Alternativt kan du dela projektet som en hela med den **dela** kontroll på projektsidan för.

1. Om du vill redigera filer än anteckningsböcker, högerklicka på filen i projektet och välj **Redigera fil** (kortkommandot: jag). Standardåtgärden **kör** (kortkommandot: r), bara visar filinnehållet och tillåter inte att redigera.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: skapa en kör en Jupyter-anteckningsbok för att göra linjär regression](tutorial-create-run-jupyter-notebook.md)
