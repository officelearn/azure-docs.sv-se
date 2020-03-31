---
title: Använda LetsEncrypt.org certifikat med Application Gateway
description: Den här artikeln innehåller information om hur du skaffar ett certifikat från LetsEncrypt.org och använder det på programgateway för AKS-kluster.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957971"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Använda certifikat med LetsEncrypt.org på Application Gateway för AKS-kluster

I det här avsnittet konfigureras DIN AKS för att utnyttja [LetsEncrypt.org](https://letsencrypt.org/) och automatiskt skaffa ett TLS/SSL-certifikat för din domän. Certifikatet installeras på Application Gateway, som kommer att utföra SSL/TLS-avslutning för AKS-klustret. Den inställning som beskrivs här använder [tillägget cert-manager](https://github.com/jetstack/cert-manager) Kubernetes, som automatiserar skapandet och hanteringen av certifikat.

Följ stegen nedan för att installera [cert-manager](https://docs.cert-manager.io) på ditt befintliga AKS-kluster.

1. Helm-sjökort

    Kör följande skript för `cert-manager` att installera helm-diagrammet. Detta kommer att:

    - skapa ett `cert-manager` nytt namnområde på din AKS
    - skapa följande CRD: Certifikat, Utmaning, ClusterIssuer, Issuer, Order
    - installera cert-manager-diagram (från [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Klusterissuerresurs

    Skapa `ClusterIssuer` en resurs. Det är `cert-manager` nödvändigt att `Lets Encrypt` företräda certifikatutfärdaren där de undertecknade certifikaten kommer att erhållas.

    Med hjälp av icke-namnområdesresursen `ClusterIssuer` utfärdar cert-manager certifikat som kan förbrukas från flera namnområden. `Let’s Encrypt`använder ACME-protokollet för att kontrollera att du styr ett visst domännamn och utfärda ett certifikat. Mer information om `ClusterIssuer` hur du konfigurerar egenskaper [här](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`instruerar `cert-manager` att utfärda certifikat `Lets Encrypt` med hjälp av mellanlagringsmiljön som används för testning (rotcertifikatet finns inte i webbläsarens/klientförtroendearkiven).

    Standardutmaningstypen i YAML `http01`nedan är . Andra utmaningar dokumenteras på [letsencrypt.org - Utmaningstyper](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Uppdatera `<YOUR.EMAIL@ADDRESS>` i YAML nedan

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Distribuera app

    Skapa en ingressresurs `guestbook` för att exponera programmet med hjälp av programgatewayen med Lets Encrypt-certifikatet.

    Se till att Application Gateway har en offentlig FRONTEND IP-konfiguration med ett DNS-namn (antingen med hjälp av standarddomänen `azure.com` eller etablera en `Azure DNS Zone` tjänst och tilldela din egen anpassade domän).
    Observera anteckningen `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`, som talar om för cert-manager att bearbeta taggade Ingress-resursen.

    > [!IMPORTANT] 
    > Uppdatera `<PLACEHOLDERS.COM>` i YAML nedan med din egen domän (eller Application Gateway en, till exempel "kh-aks-ingress.westeurope.cloudapp.azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Efter några sekunder kan du `guestbook` komma åt tjänsten via HTTPS-url:en för Application Gateway med hjälp av det automatiskt utfärdade **mellanlagringscertifikatet.** `Lets Encrypt`
    Din webbläsare kan varna dig för en ogiltig certifikatutfärdare. Mellanlagringscertifikatet utfärdas `CN=Fake LE Intermediate X1`av . Detta är en indikation på att systemet fungerade som förväntat och du är redo för ditt produktionscertifikat.

4. Produktionscertifikat

    När mellanlagringscertifikatet har konfigurerats kan du växla till en ACME-server för produktion:
    1. Ersätt mellanlagringsanteckningen på ingress-resursen med:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Ta bort den `ClusterIssuer` befintliga mellanlagringen som du skapade i föregående steg och skapa en ny genom att ersätta ACME-servern från ClusterIssuer YAML ovan med`https://acme-v02.api.letsencrypt.org/directory`

5. Certifikatets utgång och förnyelse

    Innan `Lets Encrypt` certifikatet upphör `cert-manager` att gälla uppdateras certifikatet automatiskt i Kubernetes hemliga arkiv. Då tillämpar Application Gateway Ingress Controller den uppdaterade hemligheten som refereras i de inkommande resurser som används för att konfigurera Application Gateway.
