---
title: Azure Terraform VS Code-tillägg | Microsoft Docs
description: I den här artikeln lär du dig hur du installerar och använder Terraform-tillägg i Visual Studio Code.
keywords: terraform, devops, VM, azure
author: v-mavick
ms.author: v-mavick
ms.date: 08/14/2018
ms.topic: article
ms.prod: vs-code
ms.openlocfilehash: 0c88879faae100372055479ad4edb8c36d8f557d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190360"
---
# <a name="azure-terraform-vs-code-extension"></a>Azure Terraform VS Code-tillägg

Tillägget Microsoft Azure Terraform Visual Studio Code (VS Code) är utformad för att öka utvecklarnas produktivitet samtidigt som du skapar, testar och använder Terraform med Azure. Tillägget tillhandahåller Terraform kommandostöd, graph resursvisualisering och CloudShell integrering i VS Code.

## <a name="what-you-do"></a>Vad du gör

- Installera den körbara filen HashiCorp Terraform öppen källkod på din dator.
- Installera Terraform VS Code-tillägg för Azure på din lokala VS Code-installation.

## <a name="what-you-learn"></a>Detta får du får lära dig

I de här självstudierna lär du dig:

- Hur Terraform kan automatisera och förenkla etableringen av Azure-tjänster.
- Så här installerar och använder Microsoft Terraform VS Code-tillägg för Azure-tjänster.
- Hur du använder VS Code att skriva, planera och köra Terraform planer.

## <a name="what-you-need"></a>Vad du behöver

- En dator som kör Windows 10, Linux eller macOS 10.10 +.
- [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US).
- En aktiv Azure-prenumeration. [Aktivera en kostnadsfri 30-dagars utvärderingsversion Microsoft Azure-konto](https://azure.microsoft.com/free/).
- En installation av den [Terraform](https://www.terraform.io/) verktyg med öppen källkod på den lokala datorn.
  
## <a name="prepare-your-dev-environment"></a>Förbered din utvecklingsmiljö

### <a name="install-git"></a>Installera Git

För att slutföra övningarna i artikeln, måste du [installera Git](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Installera HashiCorp Terraform

Följ anvisningarna på Hashicorps [installera Terraform](https://www.terraform.io/intro/getting-started/install.html) webbsidan, som omfattar:

- Ladda ned Terraform
- Installera Terraform
- Verifiera Terraform är korrekt installerad

>[!Tip]
>Se till att följa instruktionerna om inställningen system PATH-variabeln.

### <a name="install-nodejs"></a>Installera Node.js

Terraform i Cloud Shell du vill använda, [installera Node.js](https://nodejs.org/) 6.0 +.

>[!NOTE]
>Kontrollera om Node.js är installerat, öppna ett terminalfönster och skriv: `node -v`

### <a name="install-graphviz"></a>Installera GraphViz

Du använder Terraform visualisera funktionen, måste du [installera GraphViz](http://graphviz.org/).

>[!NOTE]
>Kontrollera om GraphViz är installerat, öppna ett terminalfönster och skriv: `dot -V`

### <a name="install-the-azure-terraform-vs-code-extension"></a>Installera Azure Terraform VS Code-tillägg:

1. Starta VS Code.
2. Klicka på den **tillägg** ikon.

    ![Knappen tillägg](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

3. Använd den **Search tillägg i Marketplace** textrutan för att söka efter Azure Terraform-tillägg:

    ![Sök VS Code-tilläggen på Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

4. Klicka på **Installera**.

    >[!NOTE]
    >När du klickar på **installera** för Azure Terraform-tillägget installeras automatiskt VS Code tillägget Azure Account. Azure-konto är en beroende fil för Azure Terraform-tillägget som används för att utföra autentiseringar för Azure-prenumeration och Azure-relaterade code-tilläggen.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Kontrollera Terraform-tillägget installeras i Visual Studio Code

1. Klicka på ikonen för tillägg.
2. Typ `@installed` i sökrutan.

    ![Installerade tillägg](media/terraform-vscode-extension/tf-installed-extensions.png)

Azure Terraform-tillägget visas i listan över installerade tillägg.

![Installerade Terraform-tillägg](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Du kan nu köra alla kommandon som stöds Terraform i miljön Cloud Shell från VS Code.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Övning 1: Grundläggande Terraform kommandon hanteringspaketen

I den här övningen ska du skapa och köra en grundläggande Terraform-konfigurationsfil som etablerar en ny Azure resursgrupp.

### <a name="prepare-a-test-plan-file"></a>Förbereda en testfil i planen

1. I VS Code, väljer **fil > Ny fil** på menyraden.
2. Navigera till den [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#) och kopiera koden i den **exempel på användning** kodblock:
3. Klistra in den kopierade koden i den nya filen som du skapade i VS Code.

    ![Klistra in koden för exempel på användning](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Du kan ändra den **namn** värdet för resursgruppen, men det måste vara unik för din Azure-prenumeration.

4. Från menyraden väljer **fil > Spara som**.
5. I den **Spara som** dialogrutan navigerar du till en valfri plats och klicka sedan på **ny mapp**. (Ändra namnet på den nya mappen till något mer beskrivande än *ny mapp*.)

    >[!NOTE]
    >I det här exemplet heter mappen TERRAFORM TESTPLAN.

6. Kontrollera att den nya mappen markeras (valt) och klicka sedan på **öppna**.
7. I den **Spara som** dialogrutan ändra standardnamnet för fil som ska *main.tf*.

    ![Spara som main.tf](media/terraform-vscode-extension/tf-save-as-main.png)

8. Klicka på **Spara**.
- I menyraden väljer **fil > Öppna mapp**. Navigera till och markera den nya mappen som du skapade.

### <a name="run-terraform-init-command"></a>Kör Terraform *init* kommando

1. Starta Visual Studio Code.
2. Från menyraden för VS Code väljer **fil > Öppna mapp...**  och leta upp och välj din *main.tf* fil.

    ![main.tf fil](media/terraform-vscode-extension/tf-main-tf.png)

3. Från menyraden väljer **Visa > Kommandopalett... > Azure Terraform: Init**.
4. Efter en liten stund när du tillfrågas om *vill du öppna Cloud Shell?* Klicka på **OK**.

    ![Vill du öppna Cloud Shell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

5. Första gången du starta Cloud Shell från en ny mapp, blir du ombedd att konfigurera webbprogrammet. Klicka på **öppna**.

    ![Första start av Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

6. Sidan Välkommen till Azure Cloud Shell öppnas. Välj Bash eller PowerShell.

    ![Välkommen till Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >I det här exemplet valdes Bash (Linux).

7. Om du inte redan har konfigurerat ett Azure storage-konto visas följande skärmbild. Klicka på **skapa lagring**.

    ![Ingen lagring har monterats](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell öppnas i det gränssnitt som du tidigare valt och visar information om cloud enhet just skapats åt dig.

    ![Din enhet i molnet har skapats](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

9. Du kan nu stänga Cloud Shell
10. Från menyraden väljer **visa** > **Kommandopaletten** > **Azure Terraform: init**.

    ![Terraform har initierats](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualisera planen

Tidigare i den här självstudien har installerat du GraphViz. Terraform kan använda GraphViz för att generera en visuell representation av en konfiguration eller körning. Azure Terraform VS Code-tillägg implementerar den här funktionen via den *visualisera* kommando.

- Från den **menyraden**väljer**Visa > Kommandopaletten > Azure Terraform: visualisera**.

    ![Visualisera planen](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Kör Terraform *plan* kommando

Terraform *plan* används för att kontrollera om Körningsplan för en uppsättning ändringar kommer att göra som du tänkt.

>[!NOTE]
>Terraform *plan* inte göra några ändringar till riktig Azure-resurser. För att faktiskt göra ändringarna i din plan, använder vi Terraform *gäller* kommando.

- Från menyraden väljer **visa** > **Kommandopaletten** > **Azure Terraform: plan**.

    ![Terraform-plan](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Kör Terraform *gäller* kommando

När du är nöjd med resultaten av Terraform *plan*, kan du köra den *gäller* kommando.

1. Från menyraden väljer **visa** > **Kommandopaletten** > **Azure Terraform: tillämpa**.

    ![Använd Terraform](media/terraform-vscode-extension/tf-terraform-apply.png)

2. Typ *Ja*.

    ![Terraform gäller Ja](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Kontrollera din Terraform-plan har utförts

Visa om nya Azure-resursgruppen har skapats:

1. Öppna Azure Portal.
2. Välj **resursgrupper** i det vänstra navigeringsfönstret.

    ![Kontrollera din nya resurs](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Din nya resursgrupp bör visas i den **namn** kolumn.

>[!NOTE]
>Du kan lämna Azure Portal-fönstret öppet just nu. Vi kommer att använda det i nästa steg.

### <a name="run-terraform-destroy-command"></a>Kör Terraform *förstör* kommando

1. Från menyraden väljer **visa** > **Kommandopaletten** > **Azure Terraform: förstör**.

    ![Terraform förstör](media/terraform-vscode-extension/tf-terraform-destroy.png)

2. Typ *Ja*.

    ![Terraform förstör Ja](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Kontrollera att resursgruppen har raderats

Kontrollera att Terraform har raderats din nya resursgrupp:

1. Klicka på **uppdatera** på Azure portal *resursgrupper* sidan.
2. Resursgruppen visas inte längre.

    ![Kontrollera att resursgruppen har raderats](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Övning 2: Terraform *compute* modul

I den här övningen ska du lära dig hur du läser in Terraform *compute* modulen i VS Code-miljön.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klona terraform-azurerm-compute-modul

1. Använd [den här länken](https://github.com/Azure/terraform-azurerm-compute) att komma åt Terraform Azure Rm Compute-modulen på GitHub.
2. Klicka på **klona eller ladda ned**.

    ![Klona eller ladda ned](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >I det här exemplet vår mappen hette *terraform-azurerm-compute*.

### <a name="open-the-folder-in-vs-code"></a>Öppna mappen i VS Code

1. Starta Visual Studio Code.
2. Från den **menyraden**väljer **fil > Öppna mapp** och navigera till och markera den mapp du skapade i föregående steg.

    ![terraform-azurerm-compute-mapp](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Initiera Terraform

Innan du kan använda Terraform-kommandon från VS Code kan du hämta plugin-program för två Azure-providrar: slumpmässiga- och azurerm.

1. Skriv i rutan Terminal i VS Code-IDE: `terraform init`

    ![terraform init kommando](media/terraform-vscode-extension/tf-terraform-init-command.png)

2. Typ `az login` och följa den på skärmen instruktioner.

### <a name="module-test-lint"></a>Modulen test: *lint*

1. Från den **menyraden**väljer **Visa > Kommandopaletten > Azure Terraform: köra Test**.
2. Välj listan av alternativ för test **lint**.

    ![Välj typ av test](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

3. När du tillfrågas om *vill du öppna CloudShell?* klickar du på **OK** och följa den på skärmen instruktioner.

    ![Vill du öppna CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>När du kör antingen den **lint** eller **från slutpunkt till slutpunkt** test, Azure använder en behållartjänst för att etablera en testdator för att utföra det faktiska testet. Testresultaten därför kan vanligtvis ta flera minuter som ska returneras.

Efter en liten stund visas en lista i fönstret Terminal liknar det här exemplet:

![Testresultat för lint](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Modulen test: *slutpunkt till slutpunkt*

1. Från den **menyraden**väljer **Visa > Kommandopaletten > Azure Terraform: köra Test**.
2. Välj listan av alternativ för test **från slutpunkt till slutpunkt**.

    ![Välj typ av test](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

3. Om du blir tillfrågad *vill du öppna CloudShell?* klickar du på **OK** och följa den på skärmen instruktioner.

    ![Vill du öppna CloudShell?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>När du kör antingen den **lint** eller **från slutpunkt till slutpunkt** test, Azure använder en behållartjänst för att etablera en testdator för att utföra det faktiska testet. Testresultaten därför kan vanligtvis ta flera minuter som ska returneras.

Efter en liten stund visas en lista i fönstret Terminal liknar det här exemplet:

![Testresultat för slutpunkt till slutpunkt](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Nästa steg

Du har sett exempel på hur Terraform kan förenkla din etablering av Azure-tjänster från Visual Studio Code. Nu kan behöva du gå igenom några av de här resurserna:
- Den [Terraform-Modulregister](https://registry.terraform.io/) innehåller alla de Terraform-modulerna som är tillgängliga för Azure och andra providers som stöds.

För var och en av de här modulerna tillhandahåller följande information:

- En beskrivning av modulens allmänna funktioner och dess egenskaper
- Ett användningsexempel på
- Testa konfigurationer, som visar hur du skapar, köra och testa varje modul på din lokala utvecklingsdator
- En Dockerfile att skapa och köra en utvecklingsmiljö i modulen lokalt.
