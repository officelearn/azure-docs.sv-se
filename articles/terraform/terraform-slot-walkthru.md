---
title: Terraform med Azure provider distributionsplatsen
description: Terraform med vägledning fack Azure providern för distribution
keywords: terraform, devops, virtuell dator, Azure, distributionsplatser
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Använda Terraform för att etablera infrastruktur med Azure distributionsplatser

[Azure distributionsplatser](/azure/app-service/web-sites-staged-publishing) gör att du kan växla mellan olika versioner av ditt program – till exempel produktion och mellanlagrings - att minimera effekten av bruten distributioner. Den här artikeln visar du använder distributionsplatser genom att guida dig genom distributionen av två appar via GitHub och Azure. En app finns i ”produktionsplatsen”, medan andra appen finns i en ”” mellanlagringsplatsen. (Namn ”produktion” och ”mellanlagring” är valfri och kan vara vad du vill använda som representerar ditt scenario.) När dina distributionsplatser har konfigurerats, kan du sedan använda Terraform för att växla mellan de två platserna efter behov.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration** - om du inte har en Azure-prenumeration, skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **GitHub-konto** – en [GitHub](http://www.github.com) konto krävs för att duplicera och Använd GitHub-repo-test.

## <a name="create-and-apply-the-terraform-plan"></a>Skapa och använda Terraform planen

1. Bläddra till den [Azure-portalen](http://portal.azure.com)

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview), och om inte gjort tidigare - väljer **Bash** som din miljö.

    ![Kommandotolken i molnet-gränssnittet](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till den `clouddrive` directory.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog med namnet `deploy`.

    ```bash
    mkdir deploy
    ```

1. Skapa en katalog med namnet `swap`.

    ```bash
    mkdir swap
    ```

1. Kontrollera att båda katalogerna har skapats med hjälp av den `ls` bash kommando.

    ![Molnet shell när du har skapat kataloger](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Ändra sökvägen till den `deploy` directory.

    ```bash
    cd deploy
    ```

1. Med hjälp av den [vi editor](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), skapa en fil med namnet `deploy.tf`, som innehåller den [Terraform configuration](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Ange infogningsläge genom att trycka på bokstaven `i` nyckel.

1. Klistra in följande kod i Redigeraren för:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Tryck på den  **&lt;Esc >** för att avsluta infogningsläget.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando, följt av trycka på  **&lt;RETUR >**:

    ```bash
    :wq
    ```

1. När filen har skapats, kan du kontrollera dess innehåll.

    ```bash
    cat deploy.tf
    ```

1. Initiera Terraform.

    ```bash
    terraform init
    ```

1. Skapa Terraform plan.

    ```bash
    terraform plan
    ```

1. Tillhandahåller de resurser som definierats i den `deploy.tf` konfigurationsfilen. (Bekräfta åtgärden genom att ange `yes` i Kommandotolken.)

    ```bash
    terraform apply
    ```

1. Stäng fönstret molnet Shell.

1. Välj på Azure portal huvudmenyn **resursgrupper**.

    ![Azure-portalen resursgrupper](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. På den **resursgrupper** väljer **slotDemoResourceGroup**.

    ![Resursgruppen som skapats av Terraform](./media/terraform-slot-walkthru/resource-group.png)

När du är klar kan du se alla resurser som skapats av Terraform.

![Resurser som skapats av Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Duplicera testprojekt

Innan du kan testa att skapa och växla till och från respektive distributionsplatser, måste du duplicera test projektet från GitHub.

1. Bläddra till den [helt otroligt terraform lagringsplatsen på GitHub](https://github.com/Azure/awesome-terraform).

1. Förgrening den **helt otroligt terraform lagringsplatsen**.

    ![Duplicera helt otroligt terraform GitHub-repo](./media/terraform-slot-walkthru/fork-repo.png)

1. Följ alla frågar om du vill duplicera i din miljö.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Distribuera från GitHub till dina distributionsplatser

En gång förgrening test projektet lagringsplatsen, konfigurera distributionsplatser via följande steg:

1. Välj på Azure portal huvudmenyn **resursgrupper**.

1. Välj **slotDemoResourceGroup**.

1. Välj **slotAppService**.

1. Välj **distributionsalternativ**.

    ![Distributionsalternativ för en App Service-resurs](./media/terraform-slot-walkthru/deployment-options.png)

1. På den **distributionsalternativet** väljer **Välj källa**, och välj sedan **GitHub**.

    ![Välj distributionskälla](./media/terraform-slot-walkthru/select-source.png)

1. När Azure gör anslutningen och visar alla alternativ väljer **auktorisering**.

1. På den **auktorisering** väljer **auktorisera**, och ange de autentiseringsuppgifter som krävs för Azure för att komma åt ditt GitHub-konto. 

1. När Azure verifierar dina GitHub-autentiseringsuppgifter, visas ett meddelande som anger att auktoriseringen har slutförts. Välj **OK** att stänga den **auktorisering** fliken.

1. Välj **välja din organisation** och välj din organisation.

1. Välj **Välj projekt**.

1. På den **Välj projektet** väljer den **helt otroligt terraform** projekt.

    ![Välj helt otroligt terraform-projekt](./media/terraform-slot-walkthru/choose-project.png)

1. Välj **Välj gren**.

1. På den **Välj gren** väljer **master**.

    ![Välj mastergrenen](./media/terraform-slot-walkthru/choose-branch-master.png)

1. På den **distributionsalternativet** väljer **OK**.

Nu har du distribuerat produktionsplatsen. Om du vill distribuera mellanlagringsplatsen utför alla föregående steg i det här avsnittet med följande ändringar:

- I steg 3, **slotAppServiceSlotOne** resurs.

- Välj filialen som ”arbetar” i stället för mastergrenen i steg 13.

    ![Välj fungerar gren](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testa appdistributioner

I föregående avsnitt skapar du två fack - **slotAppService** och **slotAppServiceSlotOne** - om du vill distribuera från olika filialer i GitHub. Vi Förhandsgranska webbprogram för att verifiera att de har distribuerats.

Utför följande steg två gånger i steg 3 väljer du **slotAppService** första gången, och välj sedan **slotAppServiceSlotOne** den andra gången:

1. Välj på Azure portal huvudmenyn **resursgrupper**.

1. Välj **slotDemoResourceGroup**.

1. Välj antingen **slotAppService** eller **slotAppServiceSlotOne**.

1. Välj på översiktssidan **URL**.

    ![Välj URL på översiktsfliken för att återge appen](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Det kan ta flera minuter för Azure för att skapa och distribuera platsen från GitHub.
>
>

För den **slotAppService** webbapp som du ser en blå sida med en rubrik för **fack Demo App 1**. För den **slotAppServiceSlotOne** webbapp visas en grön sida med en rubrik för **fack Demo App 2**.

![Förhandsgranska appar för att testa att de har distribuerats korrekt](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Växla två distributionsplatser

Om du vill testa växling två distributionsplatser, utför du följande steg:
 
1. Gå till fliken webbläsaren körs **slotAppService** (app med blå sidan). 

1. Gå tillbaka till Azure-portalen på en separat flik.

1. Öppna moln-gränssnittet.

1. Ändra sökvägen till den **clouddrive/växlingen** directory.

    ```bash
    cd clouddrive/swap
    ```

1. Med redigeraren vi skapa en fil med namnet `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Ange infogningsläge genom att trycka på bokstaven `i` nyckel.

1. Klistra in följande kod i Redigeraren för:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Tryck på den  **&lt;Esc >** för att avsluta infogningsläget.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando, följt av trycka på  **&lt;RETUR >**:

    ```bash
    :wq
    ```

1. Initiera Terraform.

    ```bash
    terraform init
    ```

1. Skapa Terraform plan.

    ```bash
    terraform plan
    ```

1. Tillhandahåller de resurser som definierats i den `swap.tf` konfigurationsfilen. (Bekräfta åtgärden genom att ange `yes` i Kommandotolken.)

    ```bash
    terraform apply
    ```

1. När Terraform har slutfört byta platser, gå tillbaka till webbläsaren som återges av **slotAppService** web app och uppdatera sidan. 

Webbappen i din **slotAppServiceSlotOne** mellanlagringsplatsen har bytts med produktionsplatsen och nu återgivningar grönt. 

![Distributionsplatser har bytts](./media/terraform-slot-walkthru/slots-swapped.png)

Gå tillbaka till den ursprungliga produktionsversionen av appen genom att tillämpa Terraform planen som skapas från den `swap.tf` konfigurationsfilen.

```bash
terraform apply
```

När växlas, finns den ursprungliga konfigurationen.