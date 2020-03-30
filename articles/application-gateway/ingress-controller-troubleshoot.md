---
title: Felsökning av ingresskontroll för programgateway
description: Den här artikeln innehåller dokumentation om hur du felsöker vanliga frågor och/eller problem med ingress-styrenheten för programgateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795502"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Felsöka vanliga frågor eller problem med Ingress Controller

[Azure Cloud Shell](https://shell.azure.com/) är det bekvämaste sättet att felsöka eventuella problem med din AKS- och AGIC-installation. Starta skalet från [shell.azure.com](https://shell.azure.com/) eller genom att klicka på länken:

[![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testa med en enkel Kubernetes-app

Stegen nedan förutsätter:
  - Du har ett AKS-kluster med avancerat nätverk aktiverat
  - AGIC har installerats på AKS-klustret
  - Du har redan en programgateway på ett VNET som delas med AKS-klustret

Om du vill kontrollera att installationen av Application Gateway + AKS + AGIC är korrekt konfigurerad distribuerar du den enklaste möjliga appen:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
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
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Kopiera och klistra in alla rader samtidigt från skriptet ovan i ett [Azure Cloud Shell](https://shell.azure.com/). Kontrollera att hela kommandot kopieras `cat` - från `EOF`och med den sista .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Efter en lyckad distribution av appen ovanför aks-klustret kommer att ha en ny Pod, Service och en Ingress.

Få listan över poddar med `kubectl get pods -o wide` [Cloud Shell:](https://shell.azure.com/).
Vi förväntar oss att en pod med namnet "test-agic-app-pod" har skapats. Det kommer att ha en IP-adress. Den här adressen måste finnas inom det virtuella nätverket för Application Gateway, som används med AKS.

![Skida](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Få en lista `kubectl get services -o wide`över tjänster: . Vi förväntar oss att se en tjänst som heter "test-agic-app-service".

![Skida](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Få listan över ingresses: `kubectl get ingress`. Vi förväntar oss att en Ingress-resurs med namnet "test-agic-app-ingress" har skapats. Resursen kommer att ha ett värdnamn "test.agic.contoso.com".

![Skida](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

En av kapslarna kommer att vara AGIC. `kubectl get pods`kommer att visa en lista över poddar, varav en börjar med "ingress-azure". Få alla loggar av `kubectl logs <name-of-ingress-controller-pod>` pod med för att kontrollera att vi har haft en lyckad distribution. En lyckad distribution skulle ha lagt till följande rader i loggen:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternativt kan vi från [Cloud Shell](https://shell.azure.com/) endast hämta de `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`rader `<ingress-azure....>` som anger framgångsrik Application Gateway-konfiguration med , där bör det exakta namnet på AGIC-podden.

Application Gateway har följande konfiguration tillämpad:

- Lyssnare: ![lyssnare](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Routningsregel: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Backend Pool:
  - Det kommer att finnas en IP-adress i backend-adresspoolen och det `kubectl get pods -o wide` 
 ![kommer att matcha IP-adressen för pod vi observerade tidigare med backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Slutligen kan vi `cURL` använda kommandot inifrån [Cloud Shell](https://shell.azure.com/) för att upprätta en HTTP-anslutning till den nyligen distribuerade appen:

1. Används `kubectl get ingress` för att hämta den offentliga IP-adressen för Application Gateway
2. Använda `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Skida](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Ett resultat `HTTP/1.1 200 OK` av indikerar att Application Gateway + AKS + AGIC-systemet fungerar som förväntat.


## <a name="inspect-kubernetes-installation"></a>Inspektera Kubernetes-installation

### <a name="pods-services-ingress"></a>Poddar, tjänster, inträngning
AgIC (Application Gateway Ingress Controller) övervakar kontinuerligt följande Kubernetes-resurser: [Distribution](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) eller [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Tjänst](https://kubernetes.io/docs/concepts/services-networking/service/), [Inträngning](https://kubernetes.io/docs/concepts/services-networking/ingress/)


Följande måste finnas på plats för att AGIC ska fungera som förväntat:
  1. AKS måste ha en eller flera friska **poddar.**
     Verifiera detta från [Cloud Shell](https://shell.azure.com/) med `kubectl get pods -o wide --show-labels` Om `apsnetapp`du har en pod med en kan utdata se ut så här:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. En eller flera **tjänster**, refererar till `selector` poddar ovan via matchande etiketter.
     Verifiera detta från [Cloud Shell](https://shell.azure.com/) med`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. **Ingress**, kommenterad `kubernetes.io/ingress.class: azure/application-gateway`med , refererar till tjänsten ovan Verifiera detta från [Cloud Shell](https://shell.azure.com/) med`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Visa anteckningar i inträngningen `kubectl get ingress aspnetapp -o yaml` ovan: `aspnetapp` (ersätt med namnet på din inträngning)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Ingressresursen måste kommenteras `kubernetes.io/ingress.class: azure/application-gateway`med .
 

### <a name="verify-observed-namespace"></a>Verifiera observerat namnområde

* Hämta befintliga namnområden i Kubernetes-klustret. Vilket namnområde körs din app i? Tittar AGIC på det namnområdet? Se dokumentationen [för support för flera namnområden](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) om hur du korrekt konfigurerar observerade namnområden.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC-podden ska `default` finnas i namnområdet (se kolumn `NAMESPACE`). En hälsosam pod `Running` skulle `STATUS` ha i kolumnen. Det bör finnas minst en AGIC pod.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Om AGIC-podden inte`STATUS` är felfri (kolumnen `Running`från kommandot ovan är inte ):
  - få loggar för att förstå varför:`kubectl logs <pod-name>`
  - För den föregående instansen av pod:`kubectl logs <pod-name> --previous`
  - beskriv podden för att få mer sammanhang:`kubectl describe pod <pod-name>`


* Har du en Kubernetes [Service](https://kubernetes.io/docs/concepts/services-networking/service/) och [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) resurser?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Är din [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) kommenterad `kubernetes.io/ingress.class: azure/application-gateway`med: ? AGIC kommer bara att titta efter Kubernetes Ingress-resurser som har den här anteckningen.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC avger Kubernetes-händelser för vissa kritiska fel. Du kan visa följande:
  - i din terminal via`kubectl get events --sort-by=.metadata.creationTimestamp`
  - i webbläsaren med hjälp av [Kubernetes webbgränssnitt (Dashboard)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Loggningsnivåer

AGIC har 3 avverkningsnivåer. Nivå 1 är standard och visar minimalt antal logglinjer.
Nivå 5, å andra sidan, skulle visa alla loggar, inklusive sanerat innehåll av config tillämpas på ARM.

Kubernetes-communityn har etablerat 9 loggningsnivåer för [kubectl-verktyget.](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) I detta arkiv använder vi 3 av dessa, med liknande semantik:


| Utförlighet | Beskrivning |
|-----------|-------------|
|  1        | Standardloggnivå; visar startinformation, varningar och fel |
|  3        | Utökad information om händelser och ändringar. listor över skapade objekt |
|  5        | Loggar konverterade objekt; visar sanerad JSON-config appliceras på ARM |


Verbositetsnivåerna kan justeras `verbosityLevel` via variabeln i [helm-config.yaml-filen.](#sample-helm-config-file) Öka verbosity `5` nivå för att få JSON config skickas till [ARM:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)
  - lägga `verbosityLevel: 5` på en rad av sig själv i [helm-config.yaml](#sample-helm-config-file) och installera
  - få loggar med`kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Exempel på Helm-config-fil
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

