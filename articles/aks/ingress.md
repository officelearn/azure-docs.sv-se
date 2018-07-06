---
title: Konfigurera inkommande med kluster Azure Kubernetes Service (AKS)
description: Installera och konfigurera en ingress-kontrollanten för NGINX i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bd223e9eebac495d7336c618b831528505c30959
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857403"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>HTTPS-Ingress på Azure Kubernetes Service (AKS)

Ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbara trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera de inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollanten och ingående regler, kan en extern adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Det här dokumentet beskriver en exempeldistribution av den [ingress-kontrollanten för NGINX] [ nginx-ingress] i ett kluster i Azure Kubernetes Service (AKS). Dessutom kan den [certifikathanterare] [ cert-manager] projektet används för att automatiskt generera och konfigurera [vi kryptera] [ lets-encrypt] certifikat. Slutligen kan körs flera program i AKS-kluster som är tillgänglig via en adress.

## <a name="install-an-ingress-controller"></a>Installera en ingress-kontrollant

Använd Helm för att installera NGINX ingress-kontrollant. Se ingress-kontrollanten för NGINX [dokumentation] [ nginx-ingress] detaljerad distributionsinformation.

Det här exemplet installerar controller i den `kube-system` namnområdet, kan du ändra det till ett namnområde av ditt val. Om AKS-klustret inte RBAC aktiverat lägger du till `--set rbac.create=false` i kommandot. Mer information finns i den [nginx-ingress diagrammet][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Under installationen skapas en Azure offentlig IP-adress för ingress-kontrollant. Använd kommandot kubectl get service för att hämta den offentliga IP-adressen. Det kan ta lite tid innan IP-adress tilldelas till tjänsten.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Eftersom inga inkommande regler har skapats, om du bläddrar till offentliga IP-adress, dirigeras till NGINX ingress-styrenheter 404-webbsidan.

![Standard NGINX-serverdel](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurera DNS-namn

Eftersom du använder HTTPS-certifikat, måste du konfigurera ett FQDN-namn för ingress-styrenheter IP-adress. I det här exemplet skapas en Azure-FQDN med Azure CLI. Uppdatera skriptet med IP-adressen för ingress-kontrollanten och det namn som du vill använda i det fullständiga Domännamnet.

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

Ingress-kontrollanten ska nu vara tillgänglig via det fullständiga Domännamnet.

## <a name="install-cert-manager"></a>Installera certifikathanterare

Ingress-kontrollanten för NGINX stöder TLS-avslutning. Det finns flera sätt att hämta och konfigurera certifikat för HTTPS, det här dokumentet visar hur du använder [certifikathanterare][cert-manager], som tillhandahåller automatiska [kan kryptera] [ lets-encrypt] certifikat generation och hanteringsfunktioner.

Använd kommandot installera Helm för att installera den certifikathanterare styrenheten.

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

Mer information om certifikathanterare konfigurationen finns i den [certifikathanterare projektet][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Skapa utfärdare för CA-kluster

Innan certifikat kan utfärdas, certifikathanterare kräver en [utfärdare] [ cert-manager-issuer] eller [ClusterIssuer] [ cert-manager-cluster-issuer] resurs. Resurserna är identiska i funktionen `Issuer` fungerar i ett enda namnområde där `ClusterIssuer` fungerar över alla namnområden. Mer information finns i den [certifikathanterare utfärdare] [ cert-manager-issuer] dokumentation.

Skapa en utfärdare för kluster med hjälp av följande manifestet. Uppdatera den e-postadressen med en giltig adress från din organisation.

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

Därefter måste du skapa en resurs för certifikatet. Certifikatresursen definierar det önskade X.509-certifikatet. Mer information finns i, [certifikathanterare certifikat][cert-manager-certificates].

Skapa certifikat-resurs med följande manifestet.

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

Ingress-kontrollanten och en lösning för hantering av certifikat har nu konfigurerats. Kör nu några program för AKS-klustret.

I det här exemplet används Helm för att köra flera instanser av ett enkelt hello world-program.

Lägg till Helm-lagringsplatsen Azure-exempel i utvecklingssystemet innan du kör programmet.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Kör AKS hello world diagram med följande kommando:

```bash
helm install azure-samples/aks-helloworld
```

Nu installera en andra instans av hello world-program.

Ange ett nytt namn för den andra instansen så att de två programmen är visuellt åtskilda. Du måste också ange ett unikt namn. De här konfigurationerna kan ses i följande kommando.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Skapa inkommande väg

Båda programmen finns nu som körs på ett Kubernetes-kluster, men har konfigurerats med en tjänst av typen `ClusterIP`. Därför måste är programmen inte tillgänglig från internet. Skapa en Kubernetes ingress-resurs för att göra dem tillgängliga. Ingress-resursen konfigurerar regler som vidarebefordrar trafik till en av de två programmen.

Skapa ett filnamn `hello-world-ingress.yaml` och kopiera följande YAML.

Anteckna som trafiken till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/` dirigeras till tjänsten med namnet `aks-helloworld`. Trafik till adressen `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` dirigeras till den `ingress-demo` service.

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

Skapa ingående resursen med den `kubectl apply` kommando.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Testa ingress-konfiguration

Bläddra till det fullständiga Domännamnet för din Kubernetes ingress-kontrollant, bör du se hello world-program.

![Exempel på en](media/ingress/app-one.png)

Gå till FQDN för ingress-kontrollanten med den `/hello-world-two` sökväg, bör du se hello world-program med anpassade rubriken.

![Exempel på två](media/ingress/app-two.png)

Observera också att anslutningen är krypterad och att ett certifikat utfärdat av vi kryptera används.

![Gör att kryptera certifikat](media/ingress/certificate.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om den programvara som visas i det här dokumentet.

- [Azure CLI][helm-cli]
- [Ingress-kontrollanten för NGINX][nginx-ingress]
- [certifikathanterare][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
