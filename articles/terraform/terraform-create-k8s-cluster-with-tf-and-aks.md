---
title: Självstudiekurs - Skapa ett Kubernetes-kluster med Azure Kubernetes Service (AKS) med Terraform
description: I den här självstudien skapar du ett Kubernetes-kluster med Azure Kubernetes Service och Terraform
keywords: azurblå devops terraform aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 0a193c8da6441a04f742894797521fe92f26b2e1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945299"
---
# <a name="tutorial-create-a-kubernetes-cluster-with-azure-kubernetes-service-using-terraform"></a>Självstudiekurs: Skapa ett Kubernetes-kluster med Azure Kubernetes-tjänsten med Terraform

[Azure Kubernetes Service (AKS)](/azure/aks/) hanterar din värdbaserade Kubernetes-miljö. MED AKS kan du distribuera och hantera behållare utan expertis från behållarens orkestrering. MED AKS kan du också utföra många vanliga underhållsåtgärder utan att ta din app offline. Dessa åtgärder omfattar etablering, uppgradering och skalning av resurser på begäran.

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Använda HCL (HashiCorp Language) för att definiera ett Kubernetes-kluster
> * Använda Terraform och AKS för att skapa ett Kubernetes-kluster
> * Använda verktyget kubectl för att testa tillgängligheten för ett Kubernetes-kluster

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Konfigurera Terraform**: Följ anvisningarna i artikeln [Terraform and configure access to Azure](terraform-install-configure.md) (Terraform och konfigurera åtkomst till Azure)

- **Azure-tjänstens huvudnamn**: Följ anvisningarna i avsnittet **Skapa huvudnamn för tjänsten** i artikeln [Skapa Azure-tjänstens huvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Observera värdena för appId, displayName, password (lösenord) och tenant (klientorganisation).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Det första steget är att skapa katalogen som innehåller Terraform-konfigurationsfilerna för övningen.

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog som heter `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Deklarera Azure-providern

Skapa Terraform-konfigurationsfilen som deklarerar Azure-providern.

1. I Cloud Shell skapar du en fil som heter `main.tf`.

    ```bash
    code main.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    provider "azurerm" {
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="define-a-kubernetes-cluster"></a>Definiera ett Kubernetes-kluster

Skapa Terraform-konfigurationsfilen som deklarerar resurserna för Kubernetes-klustret.

1. I Cloud Shell skapar du en fil som heter `k8s.tf`.

    ```bash
    code k8s.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    resource "azurerm_resource_group" "k8s" {
        name     = var.resource_group_name
        location = var.location
    }
    
    resource "random_id" "log_analytics_workspace_name_suffix" {
        byte_length = 8
    }

    resource "azurerm_log_analytics_workspace" "test" {
        # The WorkSpace name has to be unique across the whole of azure, not just the current subscription/tenant.
        name                = "${var.log_analytics_workspace_name}-${random_id.log_analytics_workspace_name_suffix.dec}"
        location            = var.log_analytics_workspace_location
        resource_group_name = azurerm_resource_group.k8s.name
        sku                 = var.log_analytics_workspace_sku
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = azurerm_log_analytics_workspace.test.location
        resource_group_name   = azurerm_resource_group.k8s.name
        workspace_resource_id = azurerm_log_analytics_workspace.test.id
        workspace_name        = azurerm_log_analytics_workspace.test.name

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = var.cluster_name
        location            = azurerm_resource_group.k8s.location
        resource_group_name = azurerm_resource_group.k8s.name
        dns_prefix          = var.dns_prefix

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = file(var.ssh_public_key)
            }
        }

        default_node_pool {
            name            = "agentpool"
            node_count      = var.agent_count
            vm_size         = "Standard_DS1_v2"
        }

        service_principal {
            client_id     = var.client_id
            client_secret = var.client_secret
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = azurerm_log_analytics_workspace.test.id
            }
        }

        tags = {
            Environment = "Development"
        }
    }
    ```

    Den föregående koden anger namnet på klustret, platsen och resursgruppsnamnet. Prefixet för det fullständigt kvalificerade domännamnet (FQDN) är också inställt. FQDN används för att komma åt klustret.

    Posten `linux_profile` kan du konfigurera de inställningar som gör det möjligt att logga in på arbetsnoderna med hjälp av SSH.

    Med AKS betalar du bara för arbetarnoderna. Posten `default_node_pool` konfigurerar informationen för dessa arbetsnoder. Inkluderar `default_node_pool record` antalet arbetsnoder som ska skapas och typen av arbetsnoder. Om du behöver skala upp eller skala ned klustret i framtiden ändrar du värdet i den `count` här posten.

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="declare-the-variables"></a>Deklarera variablerna

1. I Cloud Shell skapar du en fil som heter `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="create-a-terraform-output-file"></a>Skapa en Terraform-utdatafil

Med [Terraform-utdata](https://www.terraform.io/docs/configuration/outputs.html) kan du definiera värden som markeras för användaren när en plan används i Terraform och kan hämtas med kommandot `terraform output`. I det här avsnittet skapar du en utdatafil som tillåter åtkomst till klustret med [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. I Cloud Shell skapar du en fil som heter `output.tf`.

    ```bash
    code output.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Konfigurera Azure Storage för att lagra Terraform-tillstånd

Terraform spårar tillstånd lokalt via filen `terraform.tfstate`. Det här mönstret fungerar bra i en miljö med en enda person. I en miljö med flera personer används [Azure-lagring](/azure/storage/) för att spåra tillstånd.

I det här avsnittet ser du hur du utför följande uppgifter:
- Hämta lagringskontoinformation (kontonamn och kontonyckel)
- Skapa en lagringsbehållare där terraformtillståndsinformation ska lagras.

1. I Azure-portalen väljer du **Alla tjänster** i vänstermenyn.

1. Välj **Lagringskonton**.

1. På fliken **Lagringskonton** väljer du namnet på det lagringskonto där Terraform ska lagra tillstånd. Du kan till exempel använda lagringskontot som skapades när du öppnade Cloud Shell första gången.  Lagringskontonamnet som skapades av Cloud Shell börjar vanligtvis med `cs` följt av en slumpmässig sträng med siffror och bokstäver. Ta del av det lagringskonto du väljer. Det här värdet behövs senare.

1. Välj **Åtkomstnycklar** på fliken för lagringskonto.

    ![Menyn Lagringskonto](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anteckna **nyckelvärdet** **key1.** (Om du väljer ikonen till höger om nyckeln kopieras värdet till Urklipp.)

    ![Lagringskontots åtkomstnycklar](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Skapa en behållare i ditt Azure-lagringskonto i Cloud Shell. Ersätt platshållarna med lämpliga värden för din miljö.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Skapa Kubernetes-klustret

I det här avsnittet ser du hur du använder kommandot `terraform init` för att skapa resurserna som definieras i konfigurationsfilerna som du skapade i föregående avsnitt.

1. I Cloud Shell, initiera Terraform. Ersätt platshållarna med lämpliga värden för din miljö.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Kommandot `terraform init` visar framgångsrikt initiera backend och provider plug-in:

    ![Exempel på resultat för "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportera autentiseringsuppgifterna för tjänstens huvudnamn. Ersätt platshållarna med lämpliga värden från tjänstens huvudnamn.

    ```bash
    export TF_VAR_client_id=<service-principal-appid>
    export TF_VAR_client_secret=<service-principal-password>
    ```

1. Kör kommandot `terraform plan` för att skapa Terraform-planen som definierar infrastrukturelementen. 

    ```bash
    terraform plan -out out.plan
    ```

    Kommandot `terraform plan` visar resurserna som kommer att skapas när du kör kommandot `terraform apply`:

    ![Exempel på resultat för "terraform plan"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Kör kommandot `terraform apply` för att tillämpa planen för att skapa Kubernetes-klustret. Processen för att skapa ett Kubernetes-kluster kan ta flera minuter, vilket resulterar i att Cloud Shell-sessionen tajmar ut. Om Cloud Shell-sessionen timeout kan du följa stegen i avsnittet "Återställ från en Cloud Shell-timeout" så att du kan slutföra självstudien.

    ```bash
    terraform apply out.plan
    ```

    Kommandot `terraform apply` visar resultatet av att skapa resurserna som definierats i konfigurationsfilerna:

    ![Exempel på resultat för "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. I Azure-portalen väljer du **Alla resurser** på den vänstra menyn för att se de resurser som skapats för ditt nya Kubernetes-kluster.

    ![Alla resurser i Azure-portalen](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Återhämtning från en Cloud Shell-timeout

Om Cloud Shell-sessionen tar time out kan du göra följande för att återställa:

1. Starta en Cloud Shell-session.

1. Ändra till katalogen som innehåller dina Terraform-konfigurationsfiler.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Kör följande kommando:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Testa Kubernetes-klustret

Kubernetes-verktygen kan användas för att verifiera det nyligen skapade klustret.

1. Hämta Kubernetes-konfigurationen från Terraform-tillståndet och lagra den i en fil som kubectl kan läsa.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Ange en miljövariabel så att kubectl hämtar korrekt konfiguration.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Kontrollera klustrets hälsotillstånd.

    ```bash
    kubectl get nodes
    ```

    Du bör se information för dina arbetarnoder och de bör ha statusen **Ready** enligt följande bild:

    ![Med verktyget kubectl kan du kontrollera ditt Kubernetes-klusters hälsotillstånd](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Övervaka hälsotillstånd och loggar

När AKS-klustret skapades aktiverades övervakning för att registrera hälsomått för både klusternoderna och poddarna. De här hälsomåtten är tillgängliga i Azure-portalen. Mer information om övervakning av hälsotillstånd för containrar finns i [Övervaka hälsotillstånd för Azure Kubernets Service](/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Läs mer om hur du använder Terraform i Azure](/azure/terraform)