---
title: Skapa en ingående styrenhet med en ny Application Gateway
description: Den här artikeln innehåller information om hur du distribuerar en programgateway-ingress-styrenhet med en ny Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a0bc6aef1becd53217be0eeb8c865b5c78a5d69f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239466"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Så här installerar du en AGIC -ingress-styrenhet (Application Gateway) med hjälp av en ny programgateway

Instruktionerna nedan förutsätter att AGIC (Application Gateway Ingress Controller) kommer att installeras i en miljö utan befintliga komponenter.

## <a name="required-command-line-tools"></a>Obligatoriska kommandoradsverktyg

Vi rekommenderar användning av [Azure Cloud Shell](https://shell.azure.com/) för alla kommandoradsåtgärder nedan. Starta skalet från shell.azure.com eller genom att klicka på länken:

[![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

Du kan också starta Cloud Shell från Azure-portalen med följande ikon:

![Portal lansering](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Ditt [Azure Cloud Shell](https://shell.azure.com/) har redan alla nödvändiga verktyg. Om du väljer att använda en annan miljö, se till att följande kommandoradsverktyg är installerade:

* `az`- Azure CLI: [installationsinstruktioner](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- Kubernetes kommandoradsverktyg: [installationsinstruktioner](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- Kubernetes pakethanterare: [installationsinstruktioner](https://github.com/helm/helm/releases/latest)
* `jq`- Kommandorad JSON-processor: [installationsinstruktioner](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Skapa en identitet

Följ stegen nedan för att skapa ett [huvudobjekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)för Azure Active Directory (AAD) .Follow the steps below to create an Azure Active Directory (AAD) service principal object . Vänligen spela `appId` `password`in `objectId` , och värden - dessa kommer att användas i följande steg.

1. Skapa AD-tjänstens huvudnamn ([Läs mer om RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    `appId` Värdena `password` och från JSON-utdata används i följande steg


1. Använd `appId` utdata från föregående kommando för `objectId` att hämta det nya tjänstens huvudnamn:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    Utdata för det `objectId`här kommandot är , som kommer att användas i Azure Resource Manager-mallen nedan

1. Skapa parameterfilen som ska användas i Azure Resource Manager-malldistributionen senare.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Om du vill distribuera ett **RBAC-aktiverat** kluster ställer du in `aksEnabledRBAC` fältet på`true`

## <a name="deploy-components"></a>Distribuera komponenter
I det här steget läggs följande komponenter till i din prenumeration:

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [Application Gateway](https://docs.microsoft.com/azure/application-gateway/overview) v2
- [Virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) med 2 [undernät](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [Hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), som kommer att användas av [AAD Pod Identity](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Hämta Azure Resource Manager-mallen och ändra mallen efter behov.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Distribuera Azure Resource Manager-mallen med . `az cli` Detta kan ta upp till 5 minuter.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. När distributionen är klar hämtar du `deployment-outputs.json`distributionsutdata till en fil med namnet .
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Konfigurera ingresskontroll för programgateway

Med instruktionerna i föregående avsnitt skapade och konfigurerade vi ett nytt AKS-kluster och en Application Gateway. Vi är nu redo att distribuera en exempelapp och en ingress-styrenhet till vår nya Kubernetes-infrastruktur.

### <a name="setup-kubernetes-credentials"></a>Autentiseringsuppgifter för Konfigurera Kubernetes
För följande steg behöver vi setup [kubectl](https://kubectl.docs.kubernetes.io/) kommando, som vi kommer att använda för att ansluta till vår nya Kubernetes kluster. [Cloud](https://shell.azure.com/) Shell `kubectl` har redan installerat. Vi kommer `az` att använda CLI för att få referenser för Kubernetes.

Hämta autentiseringsuppgifter för din nyligen distribuerade AKS[(läs mer):](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Installera AAD Pod-identitet
  Azure Active Directory Pod Identity ger tokenbaserad åtkomst till [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

  [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) lägger till följande komponenter i kubernetes-klustret:
   * Kubernetes [CRD:](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity` `AzureAssignedIdentity`, ,`AzureIdentityBinding`
   * [MIC-komponent (Managed Identity Controller)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [NMI-komponent (Nod Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Så här installerar du AAD Pod Identity i klustret:

   - *RBAC aktiverat* AKS-kluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC inaktiverad* AKS-kluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Installera Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) är paketansvarig för Kubernetes. Vi kommer att utnyttja `application-gateway-kubernetes-ingress` det för att installera paketet:

1. Installera [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) och kör `application-gateway-kubernetes-ingress` följande för att lägga till helm-paket:

    - *RBAC aktiverat* AKS-kluster

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC inaktiverad* AKS-kluster

        ```bash
        helm init
        ```

1. Lägg till AGIC Helm-databasen:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Installera Helm-diagram för ingress controller

1. Använd `deployment-outputs.json` filen som skapats ovan och skapa följande variabler.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Ladda ner helm-config.yaml, som kommer att konfigurera AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Eller kopiera YAML-filen nedan: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Redigera den nyligen nedladdade helm-config.yaml `appgw` och `armAuth`fyll i avsnitten och .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Värden:
     - `verbosityLevel`: Anger verbositetsnivån för AGIC-loggningsinfrastrukturen. Se [Loggningsnivåer](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) för möjliga värden.
     - `appgw.subscriptionId`: Azure-prenumerations-ID där Application Gateway finns. Exempel: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Namn på den Azure Resource Group där Application Gateway skapades. Exempel: `app-gw-resource-group`
     - `appgw.name`: Namnet på programgatewayen. Exempel: `applicationgatewayd0f0`
     - `appgw.shared`: Den här booleska `false`flaggan ska vara standard. Ställ `true` in på om du behöver en [delad programgateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: Ange namnutrymmet som AGIC ska titta på. Det kan vara ett enda strängvärde eller en kommaavgränsad lista med namnområden.
    - `armAuth.type`: kan `aadPodIdentity` vara eller`servicePrincipal`
    - `armAuth.identityResourceID`: Resurs-ID för den hanterade Azure-identiteten
    - `armAuth.identityClientId`: Identitetens klient-ID. Se nedan för mer information om Identitet
    - `armAuth.secretJSON`: Behövs endast när Tjänsten Principal `armAuth.type` Secret typ `servicePrincipal`väljs (när har ställts in på ) 


   > [!NOTE]
   > Värden `identityResourceID` `identityClientID` och är värden som skapades under stegen [Skapa en identitet](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity) och som kan erhållas igen med följande kommando:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`i kommandot ovan är resursgruppen för programgatewayen. `<identity-name>`är namnet på den skapade identiteten. Alla identiteter för en viss prenumeration kan visas med:`az identity list`


1. Installera programgateway-ingressstyrenheten:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Installera en exempelapp
Nu när vi har Installerat Application Gateway, AKS och AGIC kan vi installera en exempelapp via [Azure Cloud Shell:](https://shell.azure.com/)

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

Alternativt kan du:

* Ladda ner YAML-filen ovan:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Använda YAML-filen:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Andra exempel
Den här [programguiden](ingress-controller-expose-service-over-http-https.md) innehåller fler exempel på hur du exponerar en AKS-tjänst via HTTP eller HTTPS, till Internet med Application Gateway.
