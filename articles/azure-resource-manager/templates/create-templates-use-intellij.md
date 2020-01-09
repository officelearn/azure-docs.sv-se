---
title: Distribuera mall – IntelliJ idé
description: Lär dig hur du skapar din första Azure Resource Manager-mall med hjälp av IntelliJ-idén och hur du distribuerar den.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: 1046c942e6bec4ded332027b7856110238b8f2d7
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680621"
---
# <a name="create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Skapa och Distribuera Azure Resource Manager-mallar med hjälp av IntelliJ-idén

Lär dig hur du distribuerar en Resource Manager-mall till Azure med IntelliJ-idén och hur du redigerar och uppdaterar mallen direkt från IDE. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. Information om de begrepp som är kopplade till att distribuera och hantera dina Azure-lösningar finns i [Översikt över mall-distribution](overview.md).

![Portal diagram för Resource Manager-mall](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

När du har slutfört självstudien kan du distribuera ett Azure Storage-konto. Samma process kan användas till att distribuera andra Azure-resurser.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* En [INTELLIJ idé](https://www.jetbrains.com/idea/download/) Ultimate-utgåva eller community-versionen installerad
* Den [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) installerad, kontrol lera [IntelliJ för plugin-program](https://www.jetbrains.com/help/idea/managing-plugins.html) för mer information
* [Logga in](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) på ditt Azure-konto för Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Distribuera en snabb starts mall

I stället för att skapa en mall från början öppnar du en mall från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/). Azure-snabbstartsmallar är lagringsplatser för Resource Manager-mallar. Mallen som används i den här artikeln kallas [skapa ett standard lagrings konto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Den definierar en Azure Storage konto resurs.

1. Högerklicka på och spara [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) och [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) till din lokala dator.

1. Om ditt Azure-verktyg är korrekt installerat och inloggat bör du se Azure Explorer i din IntelliJ-IDÉs marginal List. Högerklicka på **resurs hantering** och välj **skapa distribution**.

    ![Resource Manager-mall Högerklicka för att skapa distribution](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Konfigurera **distributions namn**, **prenumeration**, **resurs grupp**och **region**. Här distribuerar vi mallen till en ny resurs grupp `testRG`. Välj sedan sökväg för **resurs mal len** som `azuredeploy.json` och **resurs parametrar** som `azuredeploy.parameters.json` du har laddat ned.

    ![Resource Manager-mall Välj filer för att skapa distribution](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. När du har klickat på OK startas distributionen. Tills distributionen är klar kan du se förloppet från IntelliJ-IDÉts **Statusfält** längst ned.

    ![Distributions status för Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Bläddra i en befintlig distribution

1. När distributionen är färdig kan du se den nya resurs gruppen `testRG` och en ny distribution har skapats. Högerklicka på distributionen så kan du se en lista över möjliga åtgärder. Välj nu **Visa egenskaper**.

    ![Bläddra i Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. En flik öppnas för att visa några användbara egenskaper, t. ex. distributions status och mallens struktur.

    ![Resource Manager-mall Visa distributions egenskaper](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Redigera och uppdatera en befintlig distribution

1. Välj **Redigera distribution** från snabb meny eller Visa egenskapsvyn innan. En annan flik öppnas som visar mall-och parametervärdena för distributionen på Azure. Om du vill spara filerna på en lokal plats kan du klicka på **Exportera mallfil** eller **Exportera parameter**filen.

    ![Redigera distribution av Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Du kan redigera de två filerna på den här sidan och distribuera ändringarna till Azure. Här ändrar vi värdet för **storageAccountType** i parameter-filer, från `Standard_LRS` till `Standard_GRS`. Klicka sedan på **Uppdatera distribution** längst ned och bekräfta uppdateringen.

    ![Redigera distribution av Resource Manager-mall](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. När uppdaterings distributionen har slutförts kan du kontrol lera att det skapade lagrings kontot har ändrats till `Standard_GRS`på portalen.

## <a name="clean-up-resources"></a>Rensa resurser

1. När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Du kan göra det från Azure Portal eller Azure CLI. I Azure Explorer från IntelliJ idé högerklickar du på din skapade **resurs grupp** och väljer Ta bort.

    ![Ta bort resurs grupp i Azure Explorer från IntelliJ idé](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Observera att om du tar bort en distribution tas inte resurser som skapats i distributionen bort. Ta bort motsvarande resurs grupp eller vissa resurser om du inte längre behöver dem.

## <a name="next-steps"></a>Nästa steg

Huvud fokus i den här artikeln är att använda IntelliJ idé för att distribuera en befintlig mall från Azure snabb starts mallar. Du har också lärt dig hur du visar och uppdaterar en befintlig distribution på Azure. Snabbstartsmallarna i Azure kanske inte innehåller exakt det du behöver. Mer information om hur du utvecklar mallar finns i vår nya nybörjar-serien:

> [!div class="nextstepaction"]
> [Nybörjar självstudier](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Besök java på Azure dev Center](https://docs.microsoft.com/azure/java)
