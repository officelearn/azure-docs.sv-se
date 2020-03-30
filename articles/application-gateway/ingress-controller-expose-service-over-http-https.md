---
title: Exponera en AKS-tjänst via HTTP eller HTTPS med Application Gateway
description: Den här artikeln innehåller information om hur du exponerar en AKS-tjänst via HTTP eller HTTPS med Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795575"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Exponera en AKS-tjänst via HTTP eller HTTPS med Application Gateway 

Dessa självstudier hjälper till att illustrera användningen av [Kubernetes ingress-resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/) för att exponera en exempelkubernetes-tjänst via [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) via HTTP eller HTTPS.

## <a name="prerequisites"></a>Krav

- Installerat `ingress-azure` rorstand.
  - [**Greenfield Deployment**](ingress-controller-install-new.md): Om du börjar från början läser du de här installationsanvisningarna, som beskriver steg för att distribuera ett AKS-kluster med Application Gateway och installera ingress-styrenheten för programgateway i AKS-klustret.
  - [**Brownfield Deployment**](ingress-controller-install-existing.md): Om du har ett befintligt AKS-kluster och Application Gateway läser du dessa instruktioner för att installera ingress-styrenheten för programgateway i AKS-klustret.
- Om du vill använda HTTPS i det här programmet behöver du ett x509-certifikat och dess privata nyckel.

## <a name="deploy-guestbook-application"></a>Distribuera `guestbook` program

Gästboksprogrammet är ett kanoniskt Kubernetes-program som består av en webbgränssnittsfrontend, en backend och en Redis-databas. Som standard `guestbook` exponeras programmet via en `frontend` tjänst `80`med namn på porten . Utan en Kubernetes Ingress-resurs är tjänsten inte tillgänglig utanför AKS-klustret. Vi kommer att använda programmet och ställa in Ingress-resurser för att komma åt programmet via HTTP och HTTPS.

Följ instruktionerna nedan för att distribuera gästboksprogrammet.

1. Ladda `guestbook-all-in-one.yaml` ner [härifrån](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Distribuera `guestbook-all-in-one.yaml` till AKS-klustret genom att köra

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Nu har `guestbook` programmet distribuerats.

## <a name="expose-services-over-http"></a>Exponera tjänster via HTTP

För att exponera gästboksprogrammet använder vi följande ingressresurs:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Den här ingressen `frontend` exponerar tjänsten för distributionen `guestbook-all-in-one` som en standardservering för Programgatewayen.

Spara ovanstående ingående `ing-guestbook.yaml`resurs som .

1. Distribuera `ing-guestbook.yaml` genom att köra:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Kontrollera loggen för ingående styrenheten för distributionsstatus.

Nu `guestbook` ska programmet vara tillgängligt. Du kan kontrollera detta genom att besöka den offentliga adressen till Application Gateway.

## <a name="expose-services-over-https"></a>Exponera tjänster via HTTPS

### <a name="without-specified-hostname"></a>Utan angivet värdnamn

Utan att ange värdnamn är gästbokstjänsten tillgänglig på alla värdnamn som pekar på programgatewayen.

1. Innan du distribuerar ingående måste du skapa en kubernetes-hemlighet som värd för certifikatet och den privata nyckeln. Du kan skapa en kubernetes hemlighet genom att köra

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Definiera följande inträngning. Ange namnet på hemligheten i avsnittet `secretName` i inträngningen.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Ersätt `<guestbook-secret-name>` i ovanstående Ingress Resource med namnet på din hemlighet. Lagra ovanstående ingående resurs i `ing-guestbook-tls.yaml`ett filnamn .

1. Distribuera ing-guestbook-tls.yaml genom att köra

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Kontrollera loggen för ingående styrenheten för distributionsstatus.

Nu `guestbook` kommer programmet att vara tillgängligt på både HTTP och HTTPS.

### <a name="with-specified-hostname"></a>Med angivet värdnamn

Du kan också ange värdnamnet på ingående för att multiplex TLS-konfigurationer och tjänster.
Genom att ange värdnamn är gästbokstjänsten endast tillgänglig på den angivna värden.

1. Definiera följande inträngning.
    I inträngningen anger du namnet på `secretName` hemligheten i avsnittet `hosts` och ersätter värdnamnet i avsnittet därefter.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Distribuera `ing-guestbook-tls-sni.yaml` genom att köra

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Kontrollera loggen för ingående styrenheten för distributionsstatus.

`guestbook` Nu programmet kommer att vara tillgänglig på både HTTP`<guestbook.contoso.com>` och HTTPS endast på den angivna värden (i det här exemplet).

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Med följande inträngning kan du lägga till ytterligare sökvägar i den här inträngningen och omdirigera dessa sökvägar till andra tjänster:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
