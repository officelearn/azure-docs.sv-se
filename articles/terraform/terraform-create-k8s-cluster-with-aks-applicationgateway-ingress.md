---
title: Skapa ett Kubernetes-kluster med Application Gateway som ingress-kontrollant med AKS (Azure Kubernetes Service)
description: Självstudie som visar hur du skapar ett Kubernetes-kluster med Azure Kubernetes Service med Application Gateway som ingress-kontrollant
services: terraform
ms.service: terraform
keywords: terraform, devops, virtual machine, azure, kubernetes, ingress, application gateway
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 1/10/2019
ms.openlocfilehash: 2235c281b5b25390838a8f201481cfbdc9e5c223
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478973"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Skapa ett Kubernetes-kluster med Application Gateway som ingress-kontrollant med Azure Kubernetes Service och Terraform
[Azure Kubernetes Service (AKS)](/azure/aks/) hanterar din värdbaserade Kubernetes-miljö. AKS gör det snabbt och enkelt att distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. Det eliminerar också problem med pågående åtgärder och underhåll genom etablering, uppgradering och skalning av resurser på begäran, utan att koppla från dina program.

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster. Alla ovanstående funktioner tillhandahålls av Azure [Application Gateway](/azure/Application-Gateway/), vilket gör det till en perfekt ingress-kontrollant för Kubernetes på Azure. 

I den här självstudien lär du dig hur du utför följande uppgifter för att skapa ett [Kubernetes-kluster](https://www.redhat.com/en/topics/containers/what-is-kubernetes) med AKS, med App Gateway som ingress-kontrollant:

> [!div class="checklist"]
> * Använda HCL (HashiCorp Language) för att definiera ett Kubernetes-kluster
> * Skapa en resurs för en programgateway med hjälp av Terraform
> * Använda Terraform och AKS för att skapa ett Kubernetes-kluster
> * Använda verktyget kubectl för att testa tillgängligheten för ett Kubernetes-kluster

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure-prenumeration**: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Konfigurera Terraform**: Följ anvisningarna i artikeln [Terraform and configure access to Azure](/azure/virtual-machines/linux/terraform-install-configure) (Terraform och konfigurera åtkomst till Azure)

- **Azure-tjänstens huvudnamn**: Följ anvisningarna i avsnittet **Skapa huvudnamn för tjänsten** i artikeln [Skapa Azure-tjänstens huvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Anteckna värdena för appId, displayName och password (lösenord).
    - Anteckna objekt-ID för tjänstens huvudnamn genom att köra följande kommando

    ```bash
     az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen
Det första steget är att skapa katalogen som innehåller Terraform-konfigurationsfilerna för övningen.

1. Bläddra till [Azure-portalen](http://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog som heter `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd terraform-aks-appgw-ingress
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
        version = "~>1.18"
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
## <a name="define-input-variables"></a>Definiera indatavariabler
Skapa Terraform-konfigurationsfilen som visar en lista över alla variabler som krävs för distributionen
1. I Cloud Shell skapar du en fil som heter `variables.tf`
    ```bash
    vi variables.tf
    ```
1. Starta infogningsläget genom att trycka på tangenten I.

2. Klistra in följande kod i redigeringsprogrammet:
    
    ```JSON
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>Definiera resurserna 
Skapa Terraform-konfigurationsfilen som skapar alla resurser. 

1. I Cloud Shell skapar du en fil som heter `resources.tf`.

    ```bash
    vi resources.tf
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande kodblock i redigeringsprogrammet:

    a. Skapa ett block för lokala variabler som kan återanvändas av beräknade variabler

    ```JSON
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```
    b. Skapa en datakälla för resursgruppen, ny användaridentitet
    ```JSON
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```
    c. Skapa basnätverksresurser
   ```JSON
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```
    d. Skapa en Application Gateway-resurs
    ```JSON
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```
    e. Skapa rolltilldelningar
    ```JSON
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```
    f. Skapa Kubernetes-klustret
    ```JSON
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
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

1. På fliken **Lagringskonton** väljer du namnet på det lagringskonto där Terraform ska lagra tillstånd. Du kan till exempel använda lagringskontot som skapades när du öppnade Cloud Shell första gången.  Lagringskontonamnet som skapades av Cloud Shell börjar vanligtvis med `cs` följt av en slumpmässig sträng med siffror och bokstäver. **Anteckna namnet på lagringskontot som du väljer – vi behöver det senare.**

1. Välj **Åtkomstnycklar** på fliken för lagringskonto.

    ![Menyn Lagringskonto](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Anteckna **nyckelvärdet** för **key1**. (Om du väljer ikonen till höger om nyckeln kopieras värdet till Urklipp.)

    ![Lagringskontots åtkomstnycklar](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

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

    ![Exempel på resultat för "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Skapa en variabelfil för att ange indatavärden I Cloud Shell skapar du en fil med namnet `main.tf`.

    ```bash
    vi terraform.tfvars
    ```

1. Starta infogningsläget genom att trycka på tangenten I.

1. Klistra in följande variabler som skapats tidigare i redigeraren:

    ```JSON
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Avsluta infogningsläget genom att trycka på tangenten **Esc**.

1. Spara filen och avsluta VI-redigeringsprogrammet genom att ange följande kommando:

    ```bash
    :wq
    ```

1. Kör kommandot `terraform plan` för att skapa Terraform-planen som definierar infrastrukturelementen. 

    ```bash
    terraform plan -out out.plan
    ```

    Kommandot `terraform plan` visar resurserna som kommer att skapas när du kör kommandot `terraform apply`:

    ![Exempel på resultat för "terraform plan"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Kör kommandot `terraform apply` för att tillämpa planen för att skapa Kubernetes-klustret. Processen för att skapa ett Kubernetes-kluster kan ta flera minuter, vilket resulterar i att tidsgränsen för Cloud Shell-sessionen nås. Om tidsgränsen för Cloud Shell-sessionen nås kan du följa stegen i avsnittet [Återhämtning från en Cloud Shell-timeout](#recover-from-a-dloud-shell-timeout) för att slutföra självstudien.

    ```bash
    terraform apply out.plan
    ```

    Kommandot `terraform apply` visar resultatet av att skapa resurserna som definierats i konfigurationsfilerna:

    ![Exempel på resultat för "terraform apply"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. I Azure-portalen väljer du **Resursgrupper** i den vänstra menyn för att se resurserna som skapades för ditt nya Kubernetes-kluster i den valda resursgruppen.

    ![Cloud Shell-kommandotolk](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Återhämtning från en Cloud Shell-timeout
Om tidsgränsen för Cloud Shell-sessionen nås kan du använda följande steg för att återhämta:

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

    ![Med verktyget kubectl kan du kontrollera ditt Kubernetes-klusters hälsotillstånd](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig hur du använder Terraform och AKS för att skapa ett Kubernetes-kluster. Här är några ytterligare resurser som du kan använda om du vill lära dig mer om Terraform på Azure.
 
 > [!div class="nextstepaction"] 
 > [Terraform Hub på Microsoft.com](https://docs.microsoft.com/azure/terraform/)
 