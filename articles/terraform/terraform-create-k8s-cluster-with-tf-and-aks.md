---
title: Skapa ett Kubernetes kluster med Azure Kubernetes Service (AKS) och Terraform
description: Kursen illustrerar hur du skapar ett Kubernetes kluster med Azure Kubernetes Service och Terraform
keywords: terraform, devops, virtuella, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/11/2018
ms.topic: article
ms.openlocfilehash: bd00a0cc8446802a03570edd58949a46c0769101
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35304203"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Skapa ett Kubernetes kluster med Azure Kubernetes Service och Terraform
[Azure Kubernetes Service (AKS)](/azure/aks/) hanterar din Kubernetes värdmiljö, vilket gör det snabbt och enkelt att distribuera och hantera behållare program utan behållaren orchestration kunskaper. Det eliminerar också problem med pågående åtgärder och underhåll genom etablering, uppgradering och skalning av resurser på begäran, utan att koppla från dina program.

I den här självstudiekursen beskrivs hur du utföra följande uppgifter för att skapa en [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) kluster med hjälp av [Terraform](http://terraform.io) och AKS:

> [!div class="checklist"]
> * Använd LISTAN (HashiCorp Language) för att definiera ett Kubernetes kluster
> * Använda Terraform och AKS för att skapa ett Kubernetes-kluster
> * Verktyget kubectl för att testa tillgängligheten för ett Kubernetes-kluster

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Konfigurera Terraform**: följer du anvisningarna i artikeln, [Terraform och konfigurera åtkomst till Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Azure-tjänstens huvudnamn**: följer du anvisningarna i avsnittet i den **skapa tjänstens huvudnamn** i artikeln, [skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Anteckna värdena för appId, displayName, lösenord och klientorganisations.

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen
Det första steget är att skapa den katalog som innehåller konfigurationsfilerna Terraform för den här övningen.

1. Bläddra till den [Azure-portalen](http://portal.azure.com).

1. Öppna [Azuremolnet Shell](/azure/cloud-shell/overview). Om du inte väljer en miljö tidigare väljer **Bash** som din miljö.

    ![Kommandotolken i molnet-gränssnittet](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till den `clouddrive` directory.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog med namnet `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Deklarera Azure-providern
Skapa konfigurationsfilen Terraform som deklarerar Azure-providern.

1. I molnet Shell, skapar du en fil med namnet `main.tf`.

    ```bash
    vi main.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i Redigeraren för:

    ```JSON
    provider "azurerm" {
        version = "=1.5.0"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Avsluta infogningsläge genom att välja den **Esc** nyckel.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Definiera ett Kubernetes kluster
Skapa konfigurationsfilen Terraform som deklarerar resurser för Kubernetes klustret.

1. I molnet Shell, skapar du en fil med namnet `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i Redigeraren för:

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
            vm_size         = "Standard_D2"
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

    Föregående kod anger namnet på klustret, plats och resource_group_name. Dessutom kan värdet den dns_prefix - som utgör en del av fullständigt kvalificerade domännamnet (FQDN) används för att få åtkomst till klustret.

    Den **linux_profile** posten kan du konfigurera inställningar som gör att du loggar in på arbetsnoderna via SSH.

    Med AKS betalar bara för arbetsnoderna. Den **agent_pool_profile** post konfigurerar information för dessa arbetarnoder. Den **agent_pool_profile post** innehåller antalet arbetarnoder att skapa och vilken typ av arbetsnoderna. Om du behöver skala upp eller ned klustret i framtiden kan du ändra den **antal** värde i den här posten.

1. Avsluta infogningsläge genom att välja den **Esc** nyckel.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Deklarera variablerna

1. I molnet Shell, skapar du en fil med namnet `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i Redigeraren för:

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

1. Avsluta infogningsläge genom att välja den **Esc** nyckel.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Skapa en Terraform utdatafilen
[Terraform matar ut](https://www.terraform.io/docs/configuration/outputs.html) kan du definiera värden som markeras för användaren när Terraform gäller en plan och kan efterfrågas med den `terraform output` kommando. I det här avsnittet skapar du en utdatafil som ger åtkomst till klustret med [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. I molnet Shell, skapar du en fil med namnet `output.tf`.

    ```bash
    vi output.tf
    ```

1. Ange infogningsläge genom att välja I nyckeln.

1. Klistra in följande kod i Redigeraren för:

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

1. Avsluta infogningsläge genom att välja den **Esc** nyckel.

1. Spara filen och avsluta redigeraren vi genom att ange följande kommando:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Konfigurera Azure storage för att lagra Terraform tillstånd
Terraform spårar lokalt tillstånd den `terraform.tfstate` filen. Det här mönstret fungerar bra i en enda person. Men i en mer praktiska miljö med flera personer som du behöver spåra tillstånd på den server som använder [Azure storage](/azure/storage/). I det här avsnittet hämta kontoinformationen nödvändiga lagring (namn och kontonyckel) och skapa en lagringsbehållare som tillståndsinformationen Terraform ska lagras.

1. Välj i Azure-portalen **alla tjänster** i den vänstra menyn.

1. Välj **lagringskonton**.

1. På den **lagringskonton** , Välj namnet på lagringskontot som Terraform är att lagra tillstånd. Du kan till exempel använda storage-konto som skapas när du har öppnat molnet Shell första gången.  Lagringskontonamnet som skapats av Cloud Shell vanligtvis börjar med `cs` följt av en slumpmässig sträng med siffror och bokstäver. **Kom ihåg namnet på lagringskontot som du väljer när det behövs senare.**

1. På fliken lagring konto väljer **åtkomstnycklar**.

    ![Storage-konto-menyn](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Anteckna den **key1** **nyckeln** värde. (Att välja ikonen till höger om nyckeln kopieras värdet till Urklipp.)

    ![Åtkomst för lagringskontonycklar](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. Skapa en behållare i Azure storage-konto i molnet Shell (Ersätt den &lt;YourAzureStorageAccountName > och &lt;YourAzureStorageAccountAccessKey >-platshållare med lämpliga värden för ditt Azure storage-konto ).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Skapa Kubernetes-kluster
I det här avsnittet visas hur du använder den `terraform init` kommando för att skapa resurser definierats konfigurationsfiler som du skapade i föregående avsnitt.

1. Initiera Terraform i molnet Shell (Ersätt den &lt;YourAzureStorageAccountName > och &lt;YourAzureStorageAccountAccessKey >-platshållare med lämpliga värden för ditt Azure storage-konto).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Den `terraform init` kommando visar att initiera serverdelen och providern plugin-programmet:

    ![Exempel på resultat för ”terraform init”](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Kör den `terraform plan` kommando för att skapa Terraform planen som definierar infrastrukturelement. Kommandot begär två värden: **var.client_id** och **var.client_secret**. För den **var.client_id** variabeln, anger du den **appId** värdet som associeras med tjänstens huvudnamn. För den **var.client_secret** variabeln, anger du den **lösenord** värdet som associeras med tjänstens huvudnamn.

    ```bash
    terraform plan -out out.plan
    ```

    Den `terraform plan` kommando visar de resurser som ska skapas när du kör den `terraform apply` kommando:

    ![Exempel på resultat för ”terraform plan”](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Kör den `terraform apply` kommando för att tillämpa planen för att skapa klustret Kubernetes. Processen för att skapa ett Kubernetes kluster kan ta flera minuter, vilket resulterar i molnet Shell sessionen avbryts. Om sessionen för molnet Shell du kan följa stegen i avsnittet [”återställa från en tidsgräns för molnet Shell”](#recover-from-a-dloud-shell-timeout) så att du kan slutföra kursen.

    ```bash
    terraform apply out.plan
    ```

    Den `terraform apply` kommando visar resultatet av att skapa resurser som definierats i konfigurationsfilerna:

    ![Exempel på ”terraform gäller” resultat](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Välj i Azure-portalen **alla tjänster** i den vänstra menyn om du vill visa de resurser som skapats för det nya klustret med Kubernetese.

    ![Kommandotolken i molnet-gränssnittet](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Återställa från ett moln Shell-timeout
Om sessionen för molnet Shell kan du utföra följande steg för att återställa:

1. Starta en session i molnet Shell.

1. Ändra till den katalog där konfigurationsfilerna Terraform.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Kör följande kommando:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Testa Kubernetes klustret
Kubernetes-verktyg kan användas för att verifiera det nya klustret.

1. Hämta Kubernetes konfigurationen från Terraform tillstånd och lagra den på en fil som kubectl kan läsa.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Ange en miljövariabel så att kubectl hämtar rätt konfig.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Kontrollera hälsotillståndet för klustret.

    ```bash
    kubectl get nodes
    ```

    Du bör se information om din arbetarnoder och de bör ha statusen **klar**, enligt följande bild:

    ![Kubectl-verktyget kan du kontrollera hälsotillståndet för Kubernetes klustret](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder Terraform och AKS för att skapa ett Kubernetes-kluster. Nedan följer några ytterligare resurser för att lära dig mer om Terraform i Azure: 

 [Terraform hubben i Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure provider-dokumentationen](http://aka.ms/terraform)  
 [Terraform Azure provider källa](http://aka.ms/tfgit)  
 [Terraform Azure-moduler](http://aka.ms/tfmodules)