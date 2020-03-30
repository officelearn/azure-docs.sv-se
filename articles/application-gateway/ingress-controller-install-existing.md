---
title: Skapa en ingressstyrenhet med en befintlig Application Gateway
description: Den här artikeln innehåller information om hur du distribuerar en programgateway-ingress-styrenhet med en befintlig Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 048ab7249b27839890bab3e677154ca3c7a0cc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239422"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Installera en AGIC (Application Gateway Ingress Controller) med hjälp av en befintlig application gateway

Application Gateway Ingress Controller (AGIC) är en pod i kubernetes-klustret.
AGIC övervakar Kubernetes [Ingress-resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/) och skapar och tillämpar Application Gateway-konfiguration baserat på kubernetes-klustrets status.

## <a name="outline"></a>Disposition:
- [Krav](#prerequisites)
- [Autentisering av Azure Resource Manager (ARM)](#azure-resource-manager-authentication)
    - Alternativ 1: [Konfigurera aad-pod-identity](#set-up-aad-pod-identity) och skapa Azure Identity på ARMs
    - Alternativ 2: [Använda en tjänsthuvudnamn](#using-a-service-principal)
- [Installera ingress controller med Helm](#install-ingress-controller-as-a-helm-chart)
- [Multi-cluster/ Shared Application Gateway](#multi-cluster--shared-application-gateway): Installera AGIC i en miljö där Application Gateway delas mellan ett eller flera AKS-kluster och/eller andra Azure-komponenter.

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du redan har följande verktyg och infrastruktur installerade:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) med [avancerat nätverk](https://docs.microsoft.com/azure/aks/configure-azure-cni) aktiverat
- [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant) i samma virtuella nätverk som AKS
- [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) installerad på AKS-klustret
- [Cloud Shell](https://shell.azure.com/) är Azure-skalmiljön, `kubectl`som `helm` har `az` CLI, och installerat. Dessa verktyg krävs för kommandona nedan.

__Säkerhetskopiera konfigurationen av programgatewayen__ innan du installerar AGIC:
  1. använda [Azure-portalen](https://portal.azure.com/) navigera till din `Application Gateway` instans
  2. från `Export template` klicka`Download`

Zip-filen du hämtade kommer att ha JSON-mallar, bash och PowerShell skript som du kan använda för att återställa App Gateway om det skulle bli nödvändigt

## <a name="install-helm"></a>Installera Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) är paketansvarig för Kubernetes. Vi kommer att utnyttja `application-gateway-kubernetes-ingress` det för att installera paketet.
Använd [Cloud Shell](https://shell.azure.com/) för att installera Helm:

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

## <a name="azure-resource-manager-authentication"></a>Autentisering av Azure Resource Manager

AGIC kommunicerar med Kubernetes API-server och Azure Resource Manager. Det kräver en identitet för att komma åt dessa API:er.

## <a name="set-up-aad-pod-identity"></a>Konfigurera AAD Pod-identitet

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) är en controller, liknande AGIC, som också körs på din AKS. Den binder Azure Active Directory-identiteter till dina Kubernetes-poddar. Identitet krävs för att ett program i en Kubernetes-pod ska kunna kommunicera med andra Azure-komponenter. I det särskilda fallet här behöver vi tillstånd för AGIC pod att göra HTTP-förfrågningar till [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Följ [installationsinstruktionerna](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) för AAD Pod Identity för att lägga till den här komponenten i DIN AKS.

Nästa vi behöver för att skapa en Azure-identitet och ge den behörigheter ARM.
Använd [Cloud Shell](https://shell.azure.com/) för att köra alla följande kommandon och skapa en identitet:

1. Skapa en Azure-identitet **i samma resursgrupp som AKS-noderna**. Det är viktigt att välja rätt resursgrupp. Resursgruppen som krävs i kommandot nedan är *inte* den som refereras till i AKS-portalfönstret. Det här är resursgruppen för de virtuella datorerna. `aks-agentpool` Vanligtvis börjar resursgruppen `MC_` med och innehåller namnet på AKS. Till exempel:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. För rolltilldelningskommandona nedan `principalId` måste vi skaffa för den nyskapade identiteten:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Ge identitetsåtkomst `Contributor` till programgatewayen. För detta behöver du ID för Application Gateway, som kommer att se ut ungefär så här:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Få en lista över Application Gateway-ID:er i din prenumeration med:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Ge identitetsåtkomst `Reader` till resursgruppen Application Gateway. Resursgrupps-ID:et `/subscriptions/A/resourceGroups/B`skulle se ut som: . Du kan hämta alla resursgrupper med:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Använda en tjänsthuvudnamn
Det är också möjligt att ge AGIC tillgång till ARM via en Kubernetes hemlighet.

1. Skapa ett huvudnamn för Active Directory-tjänsten och koda med base64. Base64-kodningen krävs för att JSON-blobben ska sparas i Kubernetes.

```azurecli
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. Lägg till den base64-kodade JSON-bloben i `helm-config.yaml` filen. Mer information `helm-config.yaml` om finns i nästa avsnitt.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Installera ingress controller som ett helm-diagram
I de första stegen installerar vi Helm's Tiller i ditt Kubernetes-kluster. Använd [Cloud Shell](https://shell.azure.com/) för att installera AGIC Helm-paketet:

1. Lägg `application-gateway-kubernetes-ingress` till rodret repo och utföra en rodret uppdatering

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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

1. Redigera helm-config.yaml och fyll `appgw` i `armAuth`värdena för och .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > Egenskaperna `<identity-resource-id>` `<identity-client-id>` för Den Azure AD-identitet som du konfigurerar i föregående avsnitt. Du kan hämta den här informationen `az identity show -g <resourcegroup> -n <identity-name>`genom `<resourcegroup>` att köra följande kommando: , var är resursgruppen där aks-klusterobjekt på den översta nivån, Programgateway och Hanterad identifiering distribueras.

1. Installera `application-gateway-kubernetes-ingress` Helm-diagram `helm-config.yaml` med konfigurationen från föregående steg

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternativt kan du `helm-config.yaml` kombinera kommandot och Helm i ett steg:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Kontrollera loggen för den nyskapade pod för att kontrollera om den startade korrekt

Se [den här hjälpguiden](ingress-controller-expose-service-over-http-https.md) för att förstå hur du kan exponera en AKS-tjänst via HTTP eller HTTPS, till internet med hjälp av en Azure Application Gateway.



## <a name="multi-cluster--shared-application-gateway"></a>Multi-kluster / Delad programgateway
Som standard tar AGIC full äganderätt till den Application Gateway som den är länkad till. AGIC version 0.8.0 och senare kan dela en enda Application Gateway med andra Azure-komponenter. Vi kan till exempel använda samma Application Gateway för en app som finns på scale setet för virtuella datorer samt ett AKS-kluster.

__Säkerhetskopiera konfigurationen av programgatewayen__ innan du aktiverar den här inställningen:
  1. använda [Azure-portalen](https://portal.azure.com/) navigera till din `Application Gateway` instans
  2. från `Export template` klicka`Download`

Zip-filen du hämtade kommer att ha JSON-mallar, bash och PowerShell skript som du kan använda för att återställa Application Gateway

### <a name="example-scenario"></a>Exempel på ett scenario
Låt oss titta på en imaginär Application Gateway, som hanterar trafik för två webbplatser:
  - `dev.contoso.com`- värd på en ny AKS, med application gateway och AGIC
  - `prod.contoso.com`- finns på en [Azure Virtual Machine Scale Set](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Med standardinställningarna tar AGIC över 100 % ägarskap för den Application Gateway som den är pekad på. AGIC skriver över alla App Gateways konfiguration. Om vi manuellt skulle skapa en `prod.contoso.com` lyssnare för (på Application Gateway), utan att definiera den i `prod.contoso.com` Kubernetes Ingress, kommer AGIC att ta bort config inom några sekunder.

För att installera AGIC och även betjäna `prod.contoso.com` från våra Virtual Machine Scale `dev.contoso.com` Set-maskiner måste vi begränsa AGIC till att konfigurera endast. Detta underlättas genom att man instansierar följande [crd:](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Kommandot ovan skapar `AzureIngressProhibitedTarget` ett objekt. Detta gör AGIC (version 0.8.0 och senare) medveten om `prod.contoso.com` förekomsten av Application Gateway config för och uttryckligen instruerar den att undvika att ändra någon konfiguration som är relaterad till det värdnamnet.


### <a name="enable-with-new-agic-installation"></a>Aktivera med ny AGIC-installation
Om du vill begränsa AGIC (version 0.8.0 och senare) `helm-config.yaml` till en delmängd av konfigurationen för Application Gateway ändrar du mallen.
Lägg `appgw:` till `shared` nyckeln under avsnittet och `true`ställ in den på .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Tillämpa Helm-ändringarna:
  1. Se `AzureIngressProhibitedTarget` till att CRD är installerat med:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Uppdatera Helm:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Som ett resultat din AKS kommer `AzureIngressProhibitedTarget` `prohibit-all-targets`att ha en ny instans av kallas:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Objektet `prohibit-all-targets`, som namnet antyder, förbjuder AGIC från att ändra config för *någon* värd och sökväg.
Helm-installation `appgw.shared=true` med distribuerar AGIC, men kommer inte att göra några ändringar i Application Gateway.


### <a name="broaden-permissions"></a>Bredda behörigheter
Eftersom Helm `appgw.shared=true` med `prohibit-all-targets` och standard blockerar AGIC från att tillämpa någon config.

Bredda AGIC-behörigheter med:
1. Skapa en `AzureIngressProhibitedTarget` ny med din specifika inställning:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Först när du har skapat ett eget anpassat förbud kan du ta bort standardförbudet, som är för brett:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Aktivera för en befintlig AGIC-installation
Låt oss anta att vi redan har en fungerande AKS, Application Gateway och konfigurerad AGIC i vårt kluster. Vi har en `prod.contosor.com` Ingress för och framgångsrikt betjänar trafik för det från AKS. Vi vill `staging.contoso.com` lägga till vår befintliga Application Gateway, men måste vara värd för den på en [virtuell dator.](https://azure.microsoft.com/services/virtual-machines/) Vi kommer att återanvända den befintliga Application Gateway och manuellt konfigurera en `staging.contoso.com`lyssnare och serverda pooler för . Men manuellt tweaking Application Gateway config (via [portal,](https://portal.azure.com) [ARM API: er](https://docs.microsoft.com/rest/api/resources/) eller [Terraform)](https://www.terraform.io/)skulle stå i konflikt med AGIC antaganden om fullt ägande. Kort efter att vi har tillämpat ändringar kommer AGIC att skriva över eller ta bort dem.

Vi kan förbjuda AGIC från att göra ändringar i en delmängd av konfigurationen.

1. Skapa `AzureIngressProhibitedTarget` ett objekt:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Visa det nyligen skapade objektet:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Ändra Application Gateway config via portal - lägga till lyssnare, routningsregler, backends etc. Det nya objektet`manually-configured-staging-environment`som vi skapade ( ) kommer att `staging.contoso.com`förbjuda AGIC från att skriva över Application Gateway-konfiguration relaterad till .
