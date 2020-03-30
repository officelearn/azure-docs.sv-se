---
title: Aktivera stöd för flera namnområden för Program gateway-ingress-styrenhet
description: Den här artikeln innehåller information om hur du aktiverar stöd för flera namnområden i ett Kubernetes-kluster med en Program gateway-ingress-styrenhet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279928"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Aktivera stöd för flera namnområden i ett AKS-kluster med Application Gateway Ingress Controller

## <a name="motivation"></a>Motivation
Kubernetes [namnområden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) gör det möjligt att partitionera ett Kubernetes-kluster och allokeras till undergrupper i ett större team. Dessa undergrupper kan sedan distribuera och hantera infrastruktur med finare kontroller av resurser, säkerhet, konfiguration etc. Kubernetes gör det möjligt att definiera en eller flera ingressresurser oberoende av varandra inom varje namnområde.

Från och med version 0.7 [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) kan inta händelser från och observera flera namnområden. Om AKS-administratören bestämmer sig för att använda [App Gateway](https://azure.microsoft.com/services/application-gateway/) som en inträngning, kommer alla namnområden att använda samma instans av Application Gateway. En enda installation av Ingress Controller övervakar tillgängliga namnområden och konfigurerar den Application Gateway som den är associerad med.

Version 0.7 av AGIC fortsätter att `default` uteslutande observera namnområdet, såvida detta inte uttryckligen ändras till ett eller flera olika namnområden i Helm-konfigurationen (se avsnittet nedan).

## <a name="enable-multiple-namespace-support"></a>Aktivera stöd för flera namnrymder
Så här aktiverar du stöd för flera namnområden:
1. ändra [filen helm-config.yaml](#sample-helm-config-file) på något av följande sätt:
   - ta `watchNamespace` bort nyckeln helt från [helm-config.yaml](#sample-helm-config-file) - AGIC kommer att observera alla namnområden
   - inställd `watchNamespace` på en tom sträng - AGIC kommer att observera alla namnområden
   - lägga till flera namnområden åtskilda`watchNamespace: default,secondNamespace`av ett kommatecken ( ) - AGIC kommer att observera dessa namnområden uteslutande
2. tillämpa Helm-malländringar med:`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

När AGIC har distribuerats med möjlighet att observera flera namnområden kommer det att:
  - lista inträngningsresurser från alla tillgängliga namnområden
  - filtrera till ingående resurser som kommenterats med`kubernetes.io/ingress.class: azure/application-gateway`
  - komponera kombinerad [Application Gateway-config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - tillämpa konfigurationen på den tillhörande Application Gateway via [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="conflicting-configurations"></a>Konfigurationer som står i konflikt
Flera [namnrymdsingressresurser](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) kan instruera AGIC att skapa motstridiga konfigurationer för en enda Application Gateway. (Två ingresses som gör anspråk på samma domän till exempel.)

Högst upp i hierarkin - **lyssnare** (IP-adress, port och värd) och **routningsregler** (bindningslyssnare, serverdelspool och HTTP-inställningar) kan skapas och delas av flera namnområden/inträngningar.

Å andra sidan - sökvägar, servernamnspooler, HTTP-inställningar och TLS-certifikat kan bara skapas med ett namnområde och dubbletter tas bort.

Tänk dig till exempel följande duplicerade `staging` ingressresurser definierade namnområden och `production` för: `www.contoso.com`
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

Illviljan de två ingressresurserna som kräver, trafikerar för `www.contoso.com` att dras till de respektive Kubernetesen namespaces, only en backend kan serva trafikera. AGIC skulle skapa en konfiguration på "först till kvarn"-basis för en av resurserna. Om två ingresses-resurser skapas samtidigt har den tidigare i alfabetet företräde. Från exemplet ovan kommer vi bara att `production` kunna skapa inställningar för inträngningen. Application Gateway konfigureras med följande resurser:

  - Lyssnaren:`fl-www.contoso.com-80`
  - Routningsregel:`rr-www.contoso.com-80`
  - Backend Pool:`pool-production-contoso-web-service-80-bp-80`
  - HTTP-inställningar:`bp-production-contoso-web-service-80-80-websocket-ingress`
  - Hälsa Probe:`pb-production-contoso-web-service-80-websocket-ingress`

Observera att förutom *lyssnare* och *routningsregel*innehåller application gateway-resurser`production`som skapats namnet på det namnområde ( ) som de skapades för.

Om de två ingående resurserna introduceras i AKS-klustret vid olika tidpunkter, är det troligt att AGIC hamnar i ett `namespace-B` `namespace-A`scenario där det konfigurerar om Application Gateway och dirigerar om trafik från till .

Om du till `staging` exempel har lagt till först konfigurerar AGIC Application Gateway för att dirigera trafik till mellanlagringsserveringspoolen. I ett senare skede, införa `production` inträngning, kommer att orsaka AGIC att programmera `production` Application Gateway, som kommer att börja dirigera trafik till backend poolen.

## <a name="restrict-access-to-namespaces"></a>Begränsa åtkomst till namnområden
Som standard konfigurerar AGIC Application Gateway baserat på kommenterade inträngning inom valfritt namnområde. Om du vill begränsa det här beteendet har du följande alternativ:
  - begränsa namnområdena genom att uttryckligen definiera namnområden agic `watchNamespace` bör observera via YAML-tangenten i [helm-config.yaml](#sample-helm-config-file)
  - använda [Roll/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) för att begränsa AGIC till specifika namnområden

## <a name="sample-helm-config-file"></a>Exempel på Helm-config-fil
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

