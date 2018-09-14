---
title: Skapa ett Kubernetes-kluster med Azure Kubernetes Service (AKS) och Terraform
description: Självstudie som visar hur du skapar ett Kubernetes-kluster med Azure Kubernetes Service och Terraform
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuell dator, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/06/2018
ms.openlocfilehash: cd1219fda7821fdc99e334de58826317113415d4
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053649"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Skapa ett Kubernetes-kluster med Azure Kubernetes Service och Terraform
[Azure Kubernetes Service (AKS)](/azure/aks/) hanterar din värdmiljö för Kubernetes, vilket gör det enkelt att snabbt distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. Det eliminerar också problem med pågående åtgärder och underhåll genom etablering, uppgradering och skalning av resurser på begäran, utan att koppla från dina program.

I den här självstudien lär du dig hur du utför följande uppgifter för att skapa ett [Kubernetes-kluster](https://www.redhat.com/en/topics/containers/what-is-kubernetes) med [Terraform](http://terraform.io) och AKS:

> [!div class="checklist"]
> * Använda HCL (HashiCorp Language) för att definiera ett Kubernetes-kluster
> * Använda Terraform och AKS för att skapa ett Kubernetes-kluster
> * Använda verktyget kubectl för att testa tillgängligheten för ett Kubernetes-kluster

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Konfigurera Terraform**: Följ anvisningarna i artikeln [Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Terraform och konfigurera åtkomst till Azure)

- **Azure-tjänstens huvudnamn**: Följ anvisningarna i avsnittet i avsnittet **Skapa huvudnamn för tjänsten** i artikeln [Skapa Azure-tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Observera värdena för appId, displayName, password (lösenord) och tenant (klientorganisation).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen
Det första steget är att skapa katalogen som innehåller Terraform-konfigurationsfilerna för övningen.

1. Bläddra till [Azure-portalen](http://portal.azure.com).

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
    vi main.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet:

    ```JSON
    provider "azurerm" {
        version = "~>1.5"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Definiera ett Kubernetes-kluster
Skapa Terraform-konfigurationsfilen som deklarerar resurserna för Kubernetes-klustret.

1. I Cloud Shell skapar du en fil som heter `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
            key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS2_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    Föregående kod anger namn för klustret, platsen och resource_group_name. Dessutom anges värdet dns_prefix som utgör en del av det fullständigt kvalificerade domännamnet (FQDN) som används för att få åtkomst till klustret.

    Med posten **linux_profile** kan du konfigurera inställningarna som aktiverar inloggning med SSH på arbetarnoderna.

    Med AKS betalar du bara för arbetarnoderna. I posten **agent_pool_profile** konfigureras information för de här arbetarnoderna. Posten **agent_pool_profile** innehåller antalet arbetarnoder som ska skapas och typen av arbetarnoder. Om du behöver skala upp eller ned klustret i framtiden ändrar du värdet **count** i den här posten.

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Deklarera variablerna

1. I Cloud Shell skapar du en fil som heter `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet:

    ```JSON
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
    ```

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Skapa en Terraform-utdatafil
Med [Terraform-utdata](https://www.terraform.io/docs/configuration/outputs.html) kan du definiera värden som markeras för användaren när en plan används i Terraform och kan hämtas med kommandot `terraform output`. I det här avsnittet skapar du en utdatafil som tillåter åtkomst till klustret med [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. I Cloud Shell skapar du en fil som heter `output.tf`.

    ```bash
    vi output.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kod i redigeringsprogrammet:

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Konfigurera Azure Storage för att lagra Terraform-tillstånd
Terraform spårar tillstånd lokalt via filen `terraform.tfstate`. Det här mönstret fungerar bra i en miljö med en enda person. I en miljö med flera personer behöver du dock spåra tillstånd på servern med [Azure Storage](/azure/storage/). I det här avsnittet hämtar du den nödvändiga lagringskontoinformationen (kontonamn och kontonyckel) och skapar en lagringsbehållare som Terraform-tillståndsinformationen kommer att lagras i.

1. I Azure-portalen väljer du **Alla tjänster** i vänstermenyn.

1. Välj **Lagringskonton**.

1. På fliken **Lagringskonton** väljer du namnet på det lagringskonto där Terraform ska lagra tillstånd. Du kan till exempel använda lagringskontot som skapades när du öppnade Cloud Shell första gången.  Lagringskontonamnet som skapades av Cloud Shell börjar vanligtvis med `cs` följt av en slumpmässig sträng med siffror och bokstäver. **Kom ihåg namnet på lagringskontot som du väljer – du behöver det senare.**

1. Välj **Åtkomstnycklar** på fliken för lagringskonto.

    ![Menyn Lagringskonto](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anteckna **nyckelvärdet** för **key1**. (Om du väljer ikonen till höger om nyckeln kopieras värdet till Urklipp.)

    ![Lagringskontots åtkomstnycklar](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. I Cloud Shell skapar du en behållare på ditt Azure Storage-konto (ersätt platshållarna &lt;YourAzureStorageAccountName> och &lt;YourAzureStorageAccountAccessKey> med lämpliga värden för ditt Azure Storage-konto).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Skapa Kubernetes-klustret
I det här avsnittet ser du hur du använder kommandot `terraform init` för att skapa resurserna som definieras i konfigurationsfilerna som du skapade i föregående avsnitt.

1. I Cloud Shell initierar du Terraform (ersätt platshållarna &lt;YourAzureStorageAccountName> och &lt;YourAzureStorageAccountAccessKey> med lämpliga värden för ditt Azure Storage-konto).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Kommandot `terraform init` visar hur det går att initiera pluginprogrammet för serverdelen och providern:

    ![Exempel på resultat för "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Kör kommandot `terraform plan` för att skapa Terraform-planen som definierar infrastrukturelementen. Kommandot begär två värden: **var.client_id** och **var.client_secret**. För variabeln **var.client_id** anger du värdet **appId** som kopplas till tjänstens huvudnamn. För variabeln **var.client_secret** anger du värdet **password** som kopplas till tjänstens huvudnamn.

    ```bash
    terraform plan -out out.plan
    ```

    Kommandot `terraform plan` visar resurserna som kommer att skapas när du kör kommandot `terraform apply`:

    ![Exempel på resultat för "terraform plan"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Kör kommandot `terraform apply` för att tillämpa planen för att skapa Kubernetes-klustret. Processen för att skapa ett Kubernetes-kluster kan ta flera minuter, vilket resulterar i att tidsgränsen för Cloud Shell-sessionen nås. Om tidsgränsen för Cloud Shell-sessionen nås kan du följa stegen i avsnittet [Återhämtning från en Cloud Shell-timeout](#recover-from-a-dloud-shell-timeout) för att slutföra självstudien.

    ```bash
    terraform apply out.plan
    ```

    Kommandot `terraform apply` visar resultatet av att skapa resurserna som definierats i konfigurationsfilerna:

    ![Exempel på resultat för "terraform apply"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. I Azure-portalen väljer du **Alla tjänster** i den vänstra menyn för att se resurserna som skapades för ditt nya Kubernetese-kluster.

    ![Cloud Shell-kommandotolk](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Återhämtning från en Cloud Shell-timeout
Om tidsgränsen för Cloud Shell-sessionen nås kan du utföra följande steg för att återhämta:

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

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du använder Terraform och AKS för att skapa ett Kubernetes-kluster. Här är några ytterligare resurser som du kan använda om du vill lära dig mer om Terraform på Azure: 

 [Terraform Hub på Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform-dokumentation för Azure-providrar](http://aka.ms/terraform)  
 [Terraform-källa för Azure-providrar](http://aka.ms/tfgit)  
 [Terraform-moduler för Azure](http://aka.ms/tfmodules)