---
title: Distribuera mall - IntelliJ IDEA
description: Lär dig hur du skapar din första Azure Resource Manager-mall med IntelliJ IDEA och hur du distribuerar den.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: a5015a018f905b4353afd6bf25a48a2d942b3b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153360"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Skapa och distribuera ARM-mallar med hjälp av IntelliJ IDEA

Lär dig hur du distribuerar en ARM-mall (Azure Resource Manager) till Azure med IntelliJ IDEA och processen att redigera och uppdatera mallen direkt från IDE. ARM-mallar är JSON-filer som definierar de resurser du behöver distribuera för din lösning. Information om vilka begrepp som är associerade med distribution och hantering av dina Azure-lösningar finns i [översikt över malldistribution](overview.md).

![Mallportaldiagram för Resurshanteraren](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

När du har slutfört självstudien kan du distribuera ett Azure Storage-konto. Samma process kan användas till att distribuera andra Azure-resurser.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition eller Community Edition installerat
* [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) installerat, läs mer i [IntelliJ-guiden om hantering av insticksprogram](https://www.jetbrains.com/help/idea/managing-plugins.html)
* Vara [inloggad](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) på Azure-kontot för Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Distribuera en snabbstartsmall

I stället för att skapa en mall från början öppnar du en mall från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/). Azure Quickstart Templates är en lagringsplats för ARM-mallar. Mallen som används i den här artikeln kallas [Skapa ett standardlagringskonto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Den definierar en Azure Storage-kontoresurs.

1. Högerklicka och spara [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) och [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) till den lokala datorn.

1. Om din Azure Toolkit är korrekt installerade och inloggade bör du se Azure Explorer i IntelliJ IDEA:s sidofält. Högerklicka på **resurshanteringen** och välj **Skapa distribution**.

    ![Resource Manager-mall högerklickar för att skapa distribution](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Config ditt **distributionsnamn,** **prenumeration,** **resursgrupp**och **region**. Här distribuerar vi mallen `testRG`till en ny resursgrupp . Välj sedan sökväg för `azuredeploy.json` **Resursmall** `azuredeploy.parameters.json` som och **Resursparametrar** som du hämtade.

    ![Resource Manager-mall väljer filer för att skapa distribution](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. När du har klickat på OK startas distributionen. Tills distributionen är klar kan du hitta förloppet från IntelliJ IDEA:s **statusfält** längst ned.

    ![Distributionsstatus för Resurshanterarens mall](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Bläddra bland en befintlig distribution

1. När distributionen är klar kan du `testRG` se den nya resursgruppen och en ny distribution som skapats. Högerklicka på distributionen och du kan se en lista över möjliga åtgärder. Välj nu **Visa egenskaper**.

    ![Resurshanterarens mall bläddrar i distributionen](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. En flikvy är öppen för att visa några användbara egenskaper som distributionsstatus och mallstruktur.

    ![Resurshanterarens mall visar distributionsegenskaper](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Redigera och uppdatera en befintlig distribution

1. Välj **Redigera distribution** på högerklicksmenyn eller visa egenskaper innan. En annan flikvy öppnas och visar mall- och parameterfilerna för distributionen på Azure. Om du vill spara filerna på lokal plats kan du klicka på **Exportera mallfil** eller **exportera parameterfiler**.

    ![Redigera distribution av Resurshanteraren-mall](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Du kan redigera de två filerna på den här sidan och distribuera ändringarna till Azure. Här ändrar vi värdet för **storageAccountType** i parameterfiler, från `Standard_LRS` till `Standard_GRS`. Klicka sedan på **Uppdatera distribution** längst ned och bekräfta uppdateringen.

    ![Redigera distribution av Resurshanteraren-mall](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. När uppdateringsdistributionen har slutförts kan du kontrollera på `Standard_GRS`portalen att det skapade lagringskontot har ändrats till .

## <a name="clean-up-resources"></a>Rensa resurser

1. När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Du kan göra det från Azure Portal eller Azure CLI. Högerklicka på den skapade **resursgruppen** i Azure Explorer från IntelliJ IDEA och välj ta bort.

    ![Ta bort resursgrupp i Azure Explorer från IntelliJ IDEA](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Observera att ta bort en distribution kommer inte att ta bort resurser som skapats av distributionen. Ta bort motsvarande resursgrupp eller specifika resurser om du inte längre behöver dem.

## <a name="next-steps"></a>Nästa steg

Huvudfokus för den här artikeln är att använda IntelliJ IDEA för att distribuera en befintlig mall från Azure Quickstart-mallar. Du har också lärt dig hur du visar och uppdaterar en befintlig distribution på Azure. Snabbstartsmallarna i Azure kanske inte innehåller exakt det du behöver. Mer information om mallutveckling finns i vår nya videoprogramledningsserie:

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Besök Java på Azure Dev center](https://docs.microsoft.com/azure/java)
