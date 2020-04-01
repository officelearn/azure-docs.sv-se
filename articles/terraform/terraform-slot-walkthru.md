---
title: Självstudiekurs - Etablera infrastruktur med Azure-distributionsplatser med Terraform
description: I den här självstudien använder du Terraform med Distributionsplatser för Azure-provider
keywords: azure devops terraform distributionsplatser
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ddd4d84ee8bf4ab1e90dd68da185cdd9075fe1e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78943493"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Självstudiekurs: Etablera infrastruktur med Azure-distributionsplatser med Terraform

Du kan använda [distributionsfack för Terraform](/azure/app-service/deploy-staging-slots) för att växla mellan olika versioner av din app. Den möjligheten hjälper dig minimera effekten av brutna distributioner. 

I den här artikeln visas ett exempel på hur du kan använda distributionsfack genom att guida dig genom distributionen av två appar via GitHub och Azure. En app lagras på produktionsplatsen. Den andra appen lagras på en mellanlagringsplats. (Namnen "produktion" och "mellanlagring" är godtyckliga. De kan vara vad som är lämpligt för ditt scenario.) När du har konfigurerat distributionsplatserna använder du Terraform för att växla mellan de två platserna efter behov.

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **GitHub-konto**: Du behöver ett [GitHub-konto](https://www.github.com) för att förgrena och använda testlagringsplatsen på GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Skapa och tillämpa Terraform-planen

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog som heter `deploy`.

    ```bash
    mkdir deploy
    ```

1. Skapa en katalog som heter `swap`.

    ```bash
    mkdir swap
    ```

1. Använd bashkommandot `ls` för att kontrollera att du har skapat båda katalogerna.

    ![Cloud Shell när du har skapat kataloger](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Ändra sökvägen till katalogen `deploy`.

    ```bash
    cd deploy
    ```

1. I Cloud Shell skapar du en fil som heter `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

1. Nu när du har skapat filen kan du kontrollera dess innehåll.

    ```bash
    cat deploy.tf
    ```

1. Initiera Terraform.

    ```bash
    terraform init
    ```

1. Skapa Terraform-planen.

    ```bash
    terraform plan
    ```

1. Etablera resurserna som definieras i konfigurationsfilen `deploy.tf`. (Bekräfta åtgärden genom att ange `yes` i kommandotolken.)

    ```bash
    terraform apply
    ```

1. Stäng fönstret Cloud Shell.

1. Välj **Resursgrupper** i huvudmenyn på Azure Portal.

    !["Resursgrupper" väljs på portalen](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Välj **slotDemoResourceGroup** på fliken **Resursgrupper**.

    ![Resursgrupp som har skapats med Terraform](./media/terraform-slot-walkthru/resource-group.png)

Du kan nu se alla resurser som Terraform har skapat.

![Resurser som har skapats med Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Förgrena testprojektet

Innan du kan testa skapandet av och växlandet till och från distributionsfacken behöver du förgrena testprojektet från GitHub.

1. Bläddra till [lagringsplatsen awesome-terraform på GitHub](https://github.com/Azure/awesome-terraform).

1. Förgrena lagringsplatsen **awesome-terraform**.

    ![Förgrena GitHub-lagringsplatsen awesome-terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. Följ eventuella uppmaningar för att använda förgreningar i miljön.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Distribuera från GitHub till dina distributionsfack

När du har förgrenat testprojektets lagringsplats konfigurerar du distributionsfacken via följande steg:

1. Välj **Resursgrupper** i huvudmenyn på Azure Portal.

1. Välj **slotDemoResourceGroup**.

1. Välj **slotAppService**.

1. Välj **Distributionsalternativ**.

    ![Distributionsalternativ för en App Service-resurs](./media/terraform-slot-walkthru/deployment-options.png)

1. På fliken **Distributionsalternativ** väljer du **Välj källa** och väljer sedan **GitHub**.

    ![Välj distributionskälla](./media/terraform-slot-walkthru/select-source.png)

1. När anslutningen har skapats i Azure och alla alternativ visas väljer du **Auktorisering**.

1. På fliken **Auktorisering** väljer du **Auktorisera** och anger de autentiseringsuppgifter som Azure behöver för att få åtkomst till ditt GitHub-konto. 

1. När Azure har verifierat dina autentiseringsuppgifter för GitHub visas ett meddelande om att auktoriseringsprocessen är klar. Välj **OK** för att stänga fliken **Auktorisering**.

1. Välj **Välj din organisation** och välj din organisation.

1. Välj **Välj projekt**.

1. På fliken **Välj projekt** väljer du projektet **awesome-terraform**.

    ![Välj projektet awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Välj **Välj gren**.

1. På fliken **Välj gren** väljer du **master**.

    ![Välj huvudgrenen](./media/terraform-slot-walkthru/choose-branch-master.png)

1. På fliken **Distributionsalternativet** väljer du **OK**.

Nu har du distribuerat produktionsplatsen. Så här distribuerar du mellanlagringsplatsen med följande ändringar:

- I steg 3 väljer du resursen **slotAppServiceSlotOne**.

- I steg 13 väljer du arbetsgrenen i stället för huvudgrenen.

    ![Välj arbetsgrenen](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testa appdistributionerna

I föregående avsnitt konfigurerade du två platser – **slotAppService** och **slotAppServiceSlotOne** – som ska distribueras från olika grenar i GitHub. Nu ska vi förhandsgranska webbapparna för att verifiera att de har distribuerats.

1. Välj **Resursgrupper** i huvudmenyn på Azure Portal.

1. Välj **slotDemoResourceGroup**.

1. Välj antingen **slotAppService** eller **slotAppServiceSlotOne**.

1. På översiktssidan väljer du **URL**.

    ![Välj webbadressen på översiktsfliken för att återge appen](./media/terraform-slot-walkthru/resource-url.png)

1. Beroende på vilken app du har valt visas följande resultat:
    - **slotAppService** webbapp - Blå sida med en sidrubrik **slot Demo App 1**. 
    - **slotAppServiceSlotOne** webbapp - Grön sida med en sidrubrik **på Slot Demo App 2**.

    ![Förhandsgranska apparna för att testa att de har distribuerats korrekt](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Växla mellan de två distributionsfacken

Så här testar du att byta ut de två distributionsplatserna:
 
1. Växla till fliken i webbläsaren med **slotAppService** (appen med den blå sidan). 

1. Gå tillbaka till Azure-portalen på en separat flik.

1. Öppna Cloud Shell.

1. Ändra katalog till **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. I Cloud Shell skapar du en fil som heter `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

1. Initiera Terraform.

    ```bash
    terraform init
    ```

1. Skapa Terraform-planen.

    ```bash
    terraform plan
    ```

1. Etablera resurserna som definieras i konfigurationsfilen `swap.tf`. (Bekräfta åtgärden genom att ange `yes` i kommandotolken.)

    ```bash
    terraform apply
    ```

1. När Terraform har bytt ut kortplatserna går du tillbaka till webbläsaren. Uppdatera sidan. 

Webbappen på mellanlagringsplatsen **slotAppServiceSlotOne** har växlat med produktionsplatsen och återges nu i grönt. 

![Distributionsplatserna har växlat](./media/terraform-slot-walkthru/slots-swapped.png)

Återgå till den ursprungliga produktionsversionen av appen genom att tillämpa Terraform-planen som du skapade från konfigurationsfilen `swap.tf` igen.

```bash
terraform apply
```

När appen har växlats visas den ursprungliga konfigurationen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)