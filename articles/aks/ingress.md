---
title: Konfigurera ingång med Azure Container Service (AKS)-kluster
description: Installera och konfigurera en domänkontrollant för en NGINX-ingång i ett kluster med Azure Container Service (AKS).
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1fe8a52a946b7e70a845e26b80dec94176c346f0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>HTTPS-ingång på Azure Container Service (AKS)

En domänkontrollant för en ingång är en typ av programvara som tillhandahåller omvänd proxy, konfigurera trafikroutning och TLS upphörande för Kubernetes tjänster. Kubernetes ingång resurser används för att konfigurera regler för inkommande trafik och vägar för enskilda Kubernetes-tjänster. Med hjälp av en domänkontrollant för en inkommande trafik och regler för inkommande trafik, kan en extern adress användas att dirigera trafik till flera tjänster i ett Kubernetes kluster.

Det här dokumentet vägleder genom en exempeldistribution av den [NGINX ingång controller] [ nginx-ingress] i ett kluster med Azure Container Service (AKS). Dessutom kan den [KUBE LEGO] [ kube-lego] för att automatiskt generera och konfigurera [vi kryptera] [ lets-encrypt] certifikat. Slutligen körs flera program i klustret AKS, som är tillgänglig via en adress.

## <a name="prerequisite"></a>Krav

Installera Helm CLI - finns Helm CLI [dokumentationen] [helm cli] för installationsanvisningar.

## <a name="install-an-ingress-controller"></a>Installera ett meddelande om ingångs-domänkontrollant

Använd Helm för att installera NGINX ingång domänkontrollant. Finns NGINX ingång controller [dokumentationen] [ nginx-ingress] detaljerad distributionsinformation. 

Uppdatera schema-databasen.

```console
helm repo update
```

Installera NGINX ingång domänkontrollant. Det här exemplet installerar domänkontrollant i den `kube-system` namnområde, kan du ändra det till ett namnområde som du väljer.

```
helm install stable/nginx-ingress --namespace kube-system
```

Under installationen skapas en Azure offentliga IP-adress för meddelande om ingångs-styrenhet. Använd kommandot kubectl get-tjänsten för att hämta den offentliga IP-adressen. Det kan ta lite tid innan IP-adress tilldelas till tjänsten.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   13.82.238.45   80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>         80/TCP                       20m
```

Eftersom inga meddelanden om ingångs-regler har skapats om du bläddrar till den offentliga IP-adressen, dirigeras till NGINX ingång domänkontrollanter standard 404-sidan.

![Standard NGINX-serverdelen](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurera DNS-namn

Eftersom du använder HTTPS-certifikat, måste du konfigurera ett FQDN-namn för ingång domänkontrollanter IP-adress. I det här exemplet skapas en Azure-FQDN med Azure CLI. Uppdatera skriptet med IP-adressen för meddelanden om ingångs-domänkontrollant och det namn som du vill använda i FQDN.

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

Om det behövs, kör du följande kommando för att hämta det fullständiga Domännamnet. Uppdatera värdet för IP-adress med det ingång-styrenhet.

```azurecli
az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '52.224.125.195')].[dnsSettings.fqdn]" --output tsv
```

Meddelanden om ingångs-domänkontrollant är nu tillgänglig via FQDN.

## <a name="install-kube-lego"></a>Installera KUBE LEGO

NGINX ingång styrenhet stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS, det här dokumentet visas hur du använder [KUBE LEGO][kube-lego], vilket ger automatisk [kan kryptera] [ lets-encrypt] certifikat generation och hanteringsfunktioner.

Använd kommandot installera Helm om du vill installera KUBE LEGO-styrenhet. Uppdatera e-postadressen med en från din organisation.

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

Mer information om KUBE LEGO konfiguration finns i [KUBE LEGO dokumentationen][kube-lego].

## <a name="run-application"></a>Kör program

Ett meddelande om ingångs-styrenhet och en lösning för hantering av certifikat har nu konfigurerats. Nu köra några program i klustret AKS.

I det här exemplet används Helm för att köra flera instanser av en enkel hello world-programmet.

Lägg till Azure-exempel Helm databasen i utvecklingssystemet innan du kör programmet.

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 Kör AKS hello world diagram med följande kommando:

```
helm install azure-samples/aks-helloworld
```

Nu installera en andra instans av programmet hello world.

Ange ett nytt namn för den andra instansen så att de två programmen är visuellt distinkta. Du måste också ange ett unikt namn. De här konfigurationerna kan ses i följande kommando.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Skapa meddelande om ingångs-väg

Båda programmen är nu körs på klustret Kubernetes men har konfigurerats med en tjänst av typen `ClusterIP`. Därför är programmen inte tillgänglig från internet. Skapa en Kubernetes ingång resurs för att göra den tillgänglig. Resursen ingång konfigurerar regler som dirigerar trafik till en av de två programmen.

Skapa ett filnamn `hello-world-ingress.yaml` och kopiera följande YAML.

Notera som trafiken till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till den `ingress-demo` service.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Skapa meddelande om ingångs-resursen med det `kubectl apply` kommando.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Testa konfigurationen ingång

Bläddra till det fullständiga Domännamnet för din Kubernetes ingång styrenhet, bör du se programmet hello world.

![Exempel på en](media/ingress/app-one.png)

Nu bläddra till FQDN för meddelanden om ingångs-styrenhet med den `/hello-world-two` sökväg, bör du se programmet hello world med anpassad rubrik.

![Exempel på två](media/ingress/app-two.png)

Observera också att anslutningen är krypterad och att ett certifikat utfärdat av vi kryptera används.

![Kan kryptera certifikat](media/ingress/certificate.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om programmet visas i det här dokumentet. 

- [NGINX ingång domänkontrollant][nginx-ingress]
- [KUBE LEGO][kube-lego]

<!-- LINKS - external -->
[helm-client]: https://docs.microsoft.com/en-us/azure/aks/kubernetes-helm#install-helm-cli
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
