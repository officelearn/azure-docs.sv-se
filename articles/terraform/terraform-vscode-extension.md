---
title: Installera och använda tillägget Azure Terraform i Visual Studio Code
description: Lär dig hur du installerar och använder tillägget Azure Terraform i Visual Studio Code.
services: terraform
ms.service: azure
keywords: terraform, azure, devops, visual studio code, extension
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 08/31/2018
ms.openlocfilehash: b1102649e48af8cb36a64f1142c078bf9ebc0d99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888282"
---
# <a name="install-and-use-the-azure-terraform-visual-studio-code-extension"></a>Installera och använda tillägget Azure Terraform i Visual Studio Code

Tillägget Microsoft Azure Terraform i Visual Studio Code hjälper utvecklare att skapa, testa och använda Terraform med Azure. Tillägget tillhandahåller stöd för Terraform-kommandon, visualisering av resursdiagram och CloudShell-integrering med Visual Studio Code.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * använder Terraform för att automatisera och förenkla etableringen av Azure-tjänster
> * installerar och använder tillägget Microsoft Terraform i Visual Studio Code för Azure-tjänster
> * använder Visual Studio Code för att skriva, planera och köra Terraform-planer.

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration**: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Terraform**: [Installera och konfigurera Terraform](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: Installera den version av [Visual Studio Code](https://code.visualstudio.com/download) som passar din miljö.

## <a name="prepare-your-dev-environment"></a>Förbereda utvecklingsmiljön

### <a name="install-git"></a>Installera Git

För att slutföra övningarna i artikeln måste du [installera Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Installera HashiCorp Terraform

Följ anvisningarna på Hashicorps webbsida [Installera Terraform](https://www.terraform.io/intro/getting-started/install.html), som beskriver hur du:

- Laddar ned Terraform
- Installerar Terraform
- Bekräftar att Terraform är korrekt installerat

>[!Tip]
>Följ instruktionerna för att ange din PATH-systemvariabel.

### <a name="install-nodejs"></a>Installera Node.js

För att använda Terraform i Cloud Shell måste du [installera Node.js](https://nodejs.org/) 6.0+.

>[!NOTE]
>Kontrollera om Node.js är installerat genom att öppna ett terminalfönster och ange `node -v`.

### <a name="install-graphviz"></a>Installera GraphViz

För att använda Terraform-visualiseringsfunktionen måste du [installera GraphViz](https://graphviz.org/).

>[!NOTE]
>Kontrollera om GraphViz är installerat genom att öppna ett terminalfönster och ange `dot -V`.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Installera tillägget Azure Terraform i Visual Studio Code

1. Starta Visual Studio Code.

1. Välj **Tillägg**.

    ![Knappen Tillägg](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Använd textrutan **Search Extensions i Marketplace** (Sök efter tillägg på Marketplace) för att söka efter Azure Terraform-tillägget:

    ![Sök efter Visual Studio Code-tillägg på Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Välj **Installera**.

    >[!NOTE]
    >När du väljer **Installera** för Azure Terraform-tillägget installerar Visual Studio Code automatiskt Azure-kontotillägget. Azure-kontot är en beroende fil för Azure Terraform-tillägget, som används för att utföra autentiseringar av Azure-prenumerationer och Azure-relaterade kodtillägg.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Kontrollera att Terraform-tillägget är installerat i Visual Studio Code

1. Välj **Tillägg**.

1. Ange `@installed` i sökrutan.

    ![Installerade tillägg](media/terraform-vscode-extension/tf-installed-extensions.png)

Azure Terraform-tillägget visas i listan över installerade tillägg.

![Installerade Terraform-tillägg](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Nu kan du köra alla Terraform-kommandon som stöds i Cloud Shell-miljön inifrån Visual Studio Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Övning 1: Genomgång av grundläggande Terraform-kommandon

I den här övningen ska du skapa och köra en grundläggande Terraform-konfigurationsfil som etablerar en ny Azure-resursgrupp.

### <a name="prepare-a-test-plan-file"></a>Förbereda filen för en testplan

1. I Visual Studio Code väljer du **Fil > Ny fil** på menyraden.

1. Gå till [sidan Terraform azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) i webbläsaren och kopiera koden i kodblocket för **exempelanvändning**:

    ![Exempelanvändning](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Klistra in den kopierade koden i den nya filen som du skapade i Visual Studio Code.

    ![Klistra in exempelanvändningskoden](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Du kan ändra **namnvärdet** för resursgruppen, men det måste vara unikt för din Azure-prenumeration.

1. Välj **Fil > Spara som** på menyraden.

1. I dialogrutan **Spara som** navigerar du till valfri plats och väljer sedan **Ny mapp**. (Ändra namnet på den nya mappen till något mer beskrivande än *Ny mapp*.)

    >[!NOTE]
    >I det här exemplet heter mappen TERRAFORM-TEST-PLAN.

1. Kontrollera att den nya mappen är markerad (vald) och välj sedan **Öppna**.

1. I dialogrutan **Spara som** ändrar du standardnamnet för filen till *main.tf*.

    ![Spara som main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

1. Välj **Spara**.
1. På menyraden väljer du **Fil > Öppna mapp**. Navigera till och välj den nya mappen som du skapade.

### <a name="run-terraform-init-command"></a>Kör Terraform-kommandot *init*

1. Starta Visual Studio Code.

1. Från Visual Studio Code-menyraden väljer du **Fil > Öppna mapp** och letar upp och väljer filen *main.tf*.

    ![Filen main.tf](media/terraform-vscode-extension/tf-main-tf.png)

1. Från menyraden väljer du **Visa > Kommandopalett... > Azure Terraform: Init**.

1. När bekräftelsen visas väljer du **OK**.

    ![Vill du öppna Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Första gången du startar Cloud Shell från en ny mapp blir du ombedd att konfigurera webbprogrammet. Välj **Öppna**.

    ![Första starten av Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Sidan Välkommen till Azure Cloud Shell öppnas. Välj Bash eller PowerShell.

    ![Välkommen till Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >I det här exemplet valdes Bash (Linux).

1. Om du inte redan har konfigurerat ett Azure Storage-konto visas följande skärm. Välj **Skapa lagring**.

    ![Ingen lagring har monterats](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell startar i det gränssnitt som du valde tidigare och visar information om molnenheten som precis skapats åt dig.

    ![Din molnenhet har skapats](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Du kan stänga Cloud Shell

1. Från menyraden väljer du **Visa** > **Kommandopalett** > **Azure Terraform: init**.

    ![Terraform har initierats](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualisera planen

Tidigare i den här självstudien installerade du GraphViz. Terraform kan använda GraphViz för att generera en visuell representation av en konfiguration eller körning. Azure Terraform-tillägget i Visual Studio Code implementerar den här funktionen via kommandot *visualize*.

- Från menyraden väljer du **Visa > Kommandopalett > Azure Terraform: Visualisera**.

    ![Visualisera planen](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Kör Terraform-kommandot *plan*

Terraform-kommandot *plan* används för att kontrollera om körningsplanen för en uppsättning ändringar kommer att göra det du tänkt dig.

>[!NOTE]
>Terraform-kommandot *plan* gör inte några ändringar i de verkliga Azure-resurserna. För att faktiskt göra ändringarna i din plan använder vi Terraform-kommandot *apply*.

- Från menyraden väljer du **Visa** > **Kommandopalett** > **Azure Terraform: plan**.

    ![Terraform-plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Kör Terraform-kommandot *apply*

När du är nöjd med resultatet från Terraform-kommandot *plan* kan du köra kommandot *apply*.

1. Från menyraden väljer du **Visa** > **Kommandopalett** > **Azure Terraform: apply**.

    ![Terraform-kommandot apply](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Ange `yes`.

    ![Terraform apply yes](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Kontrollera att Terraform-planen har utförts

Se om den nya Azure-resursgruppen har skapats genom att göra följande:

1. Öppna Azure Portal.

1. Välj **Resursgrupper** i det vänstra navigeringsfönstret.

    ![Kontrollera din nya resurs](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Din nya resursgrupp bör visas i kolumnen **NAMN**.

>[!NOTE]
>Du kan lämna Azure Portal-fönstret öppet. Vi ska använda det i nästa steg.

### <a name="run-terraform-destroy-command"></a>Kör Terraform-kommandot *destroy*

1. Från menyraden väljer du **Visa** > **Kommandopalett** > **Azure Terraform: destroy**.

    ![Terraform-kommandot destroy](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Ange *yes*.

    ![Terraform destroy yes](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Kontrollera att resursgruppen har raderats

Kontrollera att Terraform har raderat din nya resursgrupp:

1. Välj **Uppdatera** på sidan **Resursgrupper** på Azure Portal.

1. Resursgruppen visas inte längre.

    ![Kontrollera att resursgruppen har raderats](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Övning 2: Terraform-modulen *compute*

I den här övningen lär du dig hur du läser in Terraform-modulen *compute* i Visual Studio Code-miljön.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klona modulen terraform-azurerm-compute

1. Använd [den här länken](https://github.com/Azure/terraform-azurerm-compute) för att komma åt modulen Terraform Azure Rm Compute i GitHub.

1. Välj **Klona eller ladda ned**.

    ![Klona eller ladda ned](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >I det här exemplet heter vår mapp *terraform-azurerm-compute*.

### <a name="open-the-folder-in-visual-studio-code"></a>Öppna mappen i Visual Studio Code

1. Starta Visual Studio Code.

1. Från menyraden väljer du **Fil > Öppna mapp** och navigerar sedan till och väljer mappen som du skapade i föregående steg.

    ![Mappen terraform-azurerm-compute](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Initiera Terraform

Innan du kan börja använda Terraform-kommandona inifrån Visual Studio Code måste du hämta plugin-programmen för två Azure-providers: random och azurerm.

1. I rutan Terminal i Visual Studio Code IDE anger du `terraform init`.

    ![Terraform-kommandot init](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Ange `az login`, tryck på **Retur** och följ anvisningarna på skärmen.

### <a name="module-test-lint"></a>Modultest: *lint*

1. Från menyraden väljer du **Visa > Kommandopalett > Azure Terraform: Kör test**.

1. Från listan med alternativ för testtyp väljer du **lint**.

    ![Välj typ av test](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. När bekräftelsen visas väljer du **OK** och följer anvisningarna på skärmen.

    ![Vill du öppna CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>När du kör testet **lint** eller testet **end to end** använder Azure en containertjänst för att etablera en testdator där själva testet utförs. Därför kan det ta flera minuter innan testresultatet returneras.

Efter en liten stund visas en lista i terminalfönstret liknande den i det här exemplet:

![Lint-testresultat](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Modultest: *end-to-end*

1. Från menyraden väljer du **Visa > Kommandopalett > Azure Terraform: Kör test**.

1. Från listan med alternativ för testtyp väljer du **end to end**.

    ![Välj typ av test](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. När bekräftelsen visas väljer du **OK** och följer anvisningarna på skärmen.

    ![Vill du öppna CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>När du kör testet **lint** eller testet **end to end** använder Azure en containertjänst för att etablera en testdator där själva testet utförs. Därför kan det ta flera minuter innan testresultatet returneras.

Efter en liten stund visas en lista i terminalfönstret liknande den i det här exemplet:

![End to end-testresultat](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Lista över tillgängliga Terraform-moduler för Azure (och andra providers som stöds)](https://registry.terraform.io/)