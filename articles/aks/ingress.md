---
title: Konfigurera ingång med Azure Kubernetes Service (AKS)-kluster
description: Installera och konfigurera en domänkontrollant för en NGINX-ingång i ett kluster med Azure Kubernetes Service (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fcf0b6f3b7f6d75006d8c10aab041c25fc0d8c39
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751293"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>HTTPS-ingång i Azure Kubernetes-tjänster (AKS)

En domänkontrollant för en ingång är en typ av programvara som tillhandahåller omvänd proxy, konfigurera trafikroutning och TLS upphörande för Kubernetes tjänster. Kubernetes ingång resurser används för att konfigurera regler för inkommande trafik och vägar för enskilda Kubernetes-tjänster. Med hjälp av en domänkontrollant för en inkommande trafik och regler för inkommande trafik, kan en extern adress användas att dirigera trafik till flera tjänster i ett Kubernetes kluster.

Det här dokumentet vägleder genom en exempeldistribution av den [NGINX ingång controller] [ nginx-ingress] i ett kluster med Azure Kubernetes Service (AKS). Dessutom kan den [certifikathanterare] [ cert-manager] för att automatiskt generera och konfigurera [vi kryptera] [ lets-encrypt] certifikat. Slutligen körs flera program i klustret AKS, som är tillgänglig via en adress.

## <a name="install-an-ingress-controller"></a>Installera ett meddelande om ingångs-domänkontrollant

Använd Helm för att installera NGINX ingång domänkontrollant. Finns NGINX ingång controller [dokumentationen] [ nginx-ingress] detaljerad distributionsinformation.

Det här exemplet installerar domänkontrollant i den `kube-system` namnområde, kan du ändra det till ett namnområde som du väljer. Lägg till om AKS klustret inte RBAC aktiverat `--set rbac.create=false` i kommandot. Mer information finns i [nginx-ingång diagram][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Under installationen skapas en Azure offentliga IP-adress för meddelande om ingångs-styrenhet. Använd kommandot kubectl get-tjänsten för att hämta den offentliga IP-adressen. Det kan ta lite tid innan IP-adress tilldelas till tjänsten.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Eftersom inga meddelanden om ingångs-regler har skapats om du bläddrar till den offentliga IP-adressen, dirigeras till NGINX ingång domänkontrollanter standard 404-sidan.

![Standard NGINX-serverdelen](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurera DNS-namn

Eftersom du använder HTTPS-certifikat, måste du konfigurera ett FQDN-namn för ingång domänkontrollanter IP-adress. I det här exemplet skapas en Azure-FQDN med Azure CLI. Uppdatera skriptet med IP-adressen för meddelanden om ingångs-domänkontrollant och det namn som du vill använda i FQDN.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Nu bör ingång controller tillgängligt via FQDN.

## <a name="install-cert-manager"></a>Installera certifikathanterare

NGINX ingång styrenhet stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS, det här dokumentet visas hur du använder [certifikathanterare][cert-manager], vilket ger automatisk [kan kryptera] [ lets-encrypt] certifikat generation och hanteringsfunktioner.

Använd kommandot installera Helm om du vill installera certifikathanterare styrenhet.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Om klustret inte är aktiverat RBAC kan du använda det här kommandot.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Mer information om certifikathanterare konfiguration finns i [-certifikathanterare projekt][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Skapa CA klustret utfärdare

Innan du kan utfärda certifikat certifikathanterare kräver en [utfärdaren] [ cert-manager-issuer] eller [ClusterIssuer] [ cert-manager-cluster-issuer] resurs. Resurserna är identiska i funktionen `Issuer` fungerar i en enda namnrymd där `ClusterIssuer` fungerar även över alla namnområden. Mer information finns i [certifikathanterare utfärdaren] [ cert-manager-issuer] dokumentation.

Skapa en utfärdare för klustret med hjälp av följande manifestet. Uppdatera e-postadressen med en giltig adress från din organisation.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Skapa certifikatobjekt

Därefter måste du skapa en resurs för certifikatet. Certifikat-resursen definierar önskade X.509-certifikat. Mer information finns i, [certifikathanterare certifikat][cert-manager-certificates].

Skapa resursen certifikat med följande manifestet.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Kör program

Ett meddelande om ingångs-styrenhet och en lösning för hantering av certifikat har nu konfigurerats. Nu köra några program i klustret AKS.

I det här exemplet används Helm för att köra flera instanser av en enkel hello world-programmet.

Lägg till Azure-exempel Helm databasen i utvecklingssystemet innan du kör programmet.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Kör AKS hello world diagram med följande kommando:

```bash
helm install azure-samples/aks-helloworld
```

Nu installera en andra instans av programmet hello world.

Ange ett nytt namn för den andra instansen så att de två programmen är visuellt distinkta. Du måste också ange ett unikt namn. De här konfigurationerna kan ses i följande kommando.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Skapa meddelande om ingångs-väg

Båda programmen är nu körs på klustret Kubernetes men har konfigurerats med en tjänst av typen `ClusterIP`. Därför är programmen inte tillgänglig från internet. Skapa en Kubernetes ingång resurs för att göra den tillgänglig. Resursen ingång konfigurerar regler som dirigerar trafik till en av de två programmen.

Skapa ett filnamn `hello-world-ingress.yaml` och kopiera följande YAML.

Notera som trafiken till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till den `ingress-demo` service.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
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

- [Helm CLI][helm-cli]
- [NGINX ingång domänkontrollant][nginx-ingress]
- [certifikathanterare][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
