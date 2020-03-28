---
title: Självstudiekurs - Skapa en programgateway-ingress-styrenhet i Azure Kubernetes-tjänsten
description: I den här självstudien skapar du ett Kubernetes-kluster med Azure Kubernetes-tjänst med Application Gateway som ingress-styrenhet
keywords: azure devops terraform ansökan gateway ingress aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945320"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Självstudiekurs: Skapa en programgateway-ingress-styrenhet i Azure Kubernetes-tjänsten

[Azure Kubernetes Service (AKS)](/azure/aks/) hanterar din värdbaserade Kubernetes-miljö. AKS gör det snabbt och enkelt att distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. AKS eliminerar också bördan av att ta program offline för drift- och underhållsuppgifter. Med AKS kan dessa uppgifter - inklusive etablering, uppgradering och skalning av resurser - utföras på begäran.

En ingående styrenhet tillhandahåller olika funktioner för Kubernetes-tjänster. Dessa funktioner inkluderar omvänd proxy, konfigurerbar trafikroutning och TLS-avslutning. Kubernetes ingress-resurser används för att konfigurera ingressreglerna för enskilda Kubernetes-tjänster. Med hjälp av en ingående styrenhet och ingående regler kan en enda IP-adress dirigera trafik till flera tjänster i ett Kubernetes-kluster. Alla dessa funktioner tillhandahålls av Azure [Application Gateway](/azure/Application-Gateway/), vilket gör den till en idealisk Ingress-styrenhet för Kubernetes på Azure. 

I den här självstudien får du lära dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Skapa ett [Kubernetes-kluster](https://www.redhat.com/en/topics/containers/what-is-kubernetes) med AKS med Application Gateway som ingress controller.
> * Använd HCL (HashiCorp Language) för att definiera ett Kubernetes-kluster.
> * Använd Terraform för att skapa Application Gateway-resurs.
> * Använd Terraform och AKS för att skapa ett Kubernetes-kluster.
> * Använd kubectl-verktyget för att testa tillgängligheten för ett Kubernetes-kluster.

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

- **Konfigurera Terraform**: Följ anvisningarna i artikeln [Terraform and configure access to Azure](terraform-install-configure.md) (Terraform och konfigurera åtkomst till Azure)

- **Azure-resursgrupp**: Om du inte har en Azure-resursgrupp att använda för demon [skapar du en Azure-resursgrupp](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Notera resursgruppens namn och plats när dessa värden används i demon.

- **Azure-tjänstens huvudnamn**: Följ anvisningarna i avsnittet **Skapa huvudnamn för tjänsten** i artikeln [Skapa Azure-tjänstens huvudnamn med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Anteckna värdena för appId, displayName och password (lösenord).

- **Hämta tjänsthuvudhuvudhuvudobjekt-ID:** Kör följande kommando i Cloud Shell:`az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Det första steget är att skapa katalogen som innehåller Terraform-konfigurationsfilerna för övningen.

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Skapa en katalog som heter `terraform-aks-appgw-ingress`.

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

## <a name="define-input-variables"></a>Definiera indatavariabler

Skapa konfigurationsfilen för Terraform som visar alla variabler som krävs för den här distributionen.

1. I Cloud Shell skapar du en fil som heter `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
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

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="define-the-resources"></a>Definiera resurserna 
Skapa Terraform-konfigurationsfilen som skapar alla resurser. 

1. I Cloud Shell skapar du en fil som heter `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Klistra in följande kodblock om du vill skapa ett lokalt block för beräknade variabler som ska återanvändas:

    ```hcl
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

1. Klistra in följande kodblock för att skapa en datakälla för resursgruppen, ny användaridentitet:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Klistra in följande kodblock för att skapa basnätverksresurser:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Klistra in följande kodblock för att skapa Application Gateway-resurs:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Klistra in följande kodblock för att skapa rolltilldelningar:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Klistra in följande kodblock för att skapa Kubernetes-klustret:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

Koden som visas i det här avsnittet anger namnet på klustret, platsen och resource_group_name. Värdet `dns_prefix` - som ingår i det fullständigt kvalificerade domännamnet (FQDN) som används för att komma åt klustret - anges.

Posten `linux_profile` kan du konfigurera de inställningar som gör det möjligt att logga in på arbetsnoderna med hjälp av SSH.

Med AKS betalar du bara för arbetarnoderna. Posten `agent_pool_profile` konfigurerar informationen för dessa arbetsnoder. Inkluderar `agent_pool_profile record` antalet arbetsnoder som ska skapas och typen av arbetsnoder. Om du behöver skala upp eller skala ned klustret i framtiden ändrar du värdet i den `count` här posten.

## <a name="create-a-terraform-output-file"></a>Skapa en Terraform-utdatafil

[Med Terraform-utdata](https://www.terraform.io/docs/configuration/outputs.html) kan du definiera värden som markeras för användaren när Terraform `terraform output` tillämpar en plan och kan efterfrågas med kommandot. I det här avsnittet skapar du en utdatafil som tillåter åtkomst till klustret med [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

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

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Konfigurera Azure-lagring för att lagra Terraform-tillstånd

Terraform spårar tillstånd lokalt via filen `terraform.tfstate`. Det här mönstret fungerar bra i en miljö med en enda person. I en miljö med flera personer behöver du dock spåra tillstånd på servern med [Azure Storage](/azure/storage/). I det här avsnittet lär du dig att hämta nödvändig lagringskontoinformation och skapa en lagringsbehållare. Tillståndsinformationen för Terraform lagras sedan i den behållaren.

1. Välj **Lagringskonton**under **Azure-tjänster**i Azure-portalen . (Om alternativet **Lagringskonton** inte visas på huvudsidan väljer du **Fler tjänster** och letar sedan reda på och väljer det.)

1. På sidan **Lagringskonton** väljer du namnet på det lagringskonto som Terraform ska lagra tillstånd i. Du kan till exempel använda lagringskontot som skapades när du öppnade Cloud Shell första gången.  Lagringskontonamnet som skapades av Cloud Shell börjar vanligtvis med `cs` följt av en slumpmässig sträng med siffror och bokstäver. 

    Notera det lagringskonto du väljer, eftersom du behöver det senare.

1. Välj **Åtkomstnycklar** på lagringskontosidan.

    ![Menyn Lagringskonto](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Anteckna **nyckelvärdet** **key1.** (Om du väljer ikonen till höger om nyckeln kopieras värdet till Urklipp.)

    ![Lagringskontots åtkomstnycklar](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Skapa en behållare i ditt Azure-lagringskonto i Cloud Shell. Ersätt platshållarna med lämpliga värden för ditt Azure-lagringskonto.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Skapa Kubernetes-klustret
I det här avsnittet ser du hur du använder kommandot `terraform init` för att skapa resurserna som definieras i konfigurationsfilerna som du skapade i föregående avsnitt.

1. I Cloud Shell, initiera Terraform. Ersätt platshållarna med lämpliga värden för ditt Azure-lagringskonto.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    Kommandot `terraform init` visar framgångsrikt initiera backend och provider plug-in:

    ![Exempel på resultat för "terraform init"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Skapa en fil med `terraform.tfvars`namnet :

    ```bash
    code terraform.tfvars
    ```

1. Klistra in följande variabler som skapats tidigare i redigeraren. Om du vill hämta platsvärdet `az account list-locations`för din miljö använder du .

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Spara filen (**&lt;Ctrl>S**) och avsluta redigeraren (**&lt;Ctrl>Q**).

1. Kör kommandot `terraform plan` för att skapa Terraform-planen som definierar infrastrukturelementen. 

    ```bash
    terraform plan -out out.plan
    ```

    Kommandot `terraform plan` visar de resurser som skapas `terraform apply` när du kör kommandot:

    ![Exempel på resultat för "terraform plan"](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Kör kommandot `terraform apply` för att tillämpa planen för att skapa Kubernetes-klustret. Processen för att skapa ett Kubernetes-kluster kan ta flera minuter, vilket resulterar i att Cloud Shell-sessionen tajmar ut. Om Cloud Shell-sessionen timeout kan du följa stegen i avsnittet "Återställ från en Cloud Shell-timeout" så att du kan slutföra självstudien.

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

## <a name="install-azure-ad-pod-identity"></a>Installera Azure AD Pod-identitet

Azure Active Directory Pod Identity ger tokenbaserad åtkomst till [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

[Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) lägger till följande komponenter i Kubernetes-klustret:

  - Kubernetes [CRD:](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity` `AzureAssignedIdentity`, ,`AzureIdentityBinding`
  - [MIC-komponent (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - [NMI-komponent (Nod Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Om RBAC är **aktiverat**kör du följande kommando för att installera Azure AD Pod Identity i klustret:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Om RBAC är **inaktiverat**kör du följande kommando för att installera Azure AD Pod Identity i klustret:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Installera Helm

Koden i det här avsnittet använder [Helm](/azure/aks/kubernetes-helm) - Kubernetes pakethanterare - för att installera `application-gateway-kubernetes-ingress` paketet:

1. Om RBAC är **aktiverat**kör du följande uppsättning kommandon för att installera och konfigurera Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Om RBAC är **inaktiverat**kör du följande kommando för att installera och konfigurera Helm:

    ```bash
    helm init
    ```

1. Lägg till AGIC Helm-databasen:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Installera Helm-diagram för ingress controller

1. Hämta `helm-config.yaml` för att konfigurera AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Redigera `helm-config.yaml` och ange lämpliga `appgw` `armAuth` värden för och avsnitt.

    ```bash
    code helm-config.yaml
    ```

    Värdena beskrivs på följande sätt:

    - `verbosityLevel`: Anger verbositetsnivån för AGIC-loggningsinfrastrukturen. Se [Loggningsnivåer](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) för möjliga värden.
    - `appgw.subscriptionId`: Azure-prenumerations-ID för App Gateway. Exempel: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Namn på den Azure Resource Group där App Gateway skapades. 
    - `appgw.name`: Namnet på programgatewayen. Exempel: `applicationgateway1`.
    - `appgw.shared`: Den här booleska `false`flaggan ska vara standard. Ställ `true` in dig på om du behöver en [delad appgateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: Ange namnutrymmet som AGIC ska titta på. Namnområdet kan vara ett enda strängvärde eller en kommaavgränsad lista med namnområden. Om du lämnar den här variabeln som kommenteras ut eller ställer in den på tom eller tom sträng resulterar det i Att ingress controller observerar alla tillgängliga namnområden.
    - `armAuth.type`: Ett värde `aadPodIdentity` `servicePrincipal`på antingen eller .
    - `armAuth.identityResourceID`: Resurs-ID för den hanterade identiteten.
    - `armAuth.identityClientId`: Identitetens klient-ID.
    - `armAuth.secretJSON`: Behövs endast när Tjänsten Principal `armAuth.type` Secret typ `servicePrincipal`väljs (när har ställts in på ).

    Viktiga anmärkningar:
    - Värdet `identityResourceID` skapas i terraform skriptet och kan `echo "$(terraform output identity_resource_id)"`hittas genom att köra: .
    - Värdet `identityClientID` skapas i terraform skriptet och kan `echo "$(terraform output identity_client_id)"`hittas genom att köra: .
    - `<resource-group>` Värdet är resursgruppen för appgatewayen.
    - Värdet `<identity-name>` är namnet på den skapade identiteten.
    - Alla identiteter för en viss prenumeration `az identity list`kan visas med: .

1. Installera programgateway-ingressstyrenheten:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Installera en exempelapp

När du har installerat App Gateway, AKS och AGIC kan du installera en exempelapp via [Azure Cloud Shell:](https://shell.azure.com/)

1. Använd kommandot curl för att hämta YAML-filen:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Använda YAML-filen:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln.  

Ersätt platshållaren med rätt värde. Alla resurser inom den angivna resursgruppen tas bort.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"] 
> [Application Gateway, inkommande styrenhet](https://azure.github.io/application-gateway-kubernetes-ingress/)