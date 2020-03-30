---
title: Uppgradera ingress controller med Helm
description: Den här artikeln innehåller information om hur du uppgraderar en Program gateway ingress med Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795892"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Så här uppgraderar du en inkommande Application Gateway-styrenhet med hjälp av Helm 

Azure Application Gateway Ingress Controller for Kubernetes (AGIC) kan uppgraderas med hjälp av en Helm-databas som finns på Azure Storage.

Innan vi påbörjar uppgraderingsproceduren ska du se till att du har lagt till den nödvändiga databasen:

- Visa dina helm-databaser som för närvarande läggs till med:

    ```bash
    helm repo list
    ```

- Lägg till AGIC-repoen med:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Uppgradera

1. Uppdatera AGIC Helm-arkivet för att få den senaste versionen:

    ```bash
    helm repo update
    ```

1. Visa tillgängliga versioner `application-gateway-kubernetes-ingress` av diagrammet:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Exempelsvar:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Senaste tillgängliga versionen från listan ovan är:`0.7.0-rc1`

1. Visa Helm-diagrammen som är installerade:

    ```bash
    helm list
    ```

    Exempelsvar:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Helm-diagraminstallationen från exempelsvaret `odd-billygoat`ovan heter . Vi kommer att använda det här namnet för resten av kommandona. Ditt faktiska distributionsnamn kommer sannolikt att skilja sig åt.

1. Uppgradera Helm-distributionen till en ny version:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Rollback

Om Helm-distributionen misslyckas kan du återställa till en tidigare version.

1. Få det senast kända felfria versionsnumret:

    ```bash
    helm history odd-billygoat
    ```

    Exempel på utdata:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Från exempelutdata `helm history` av kommandot ser det ut `odd-billygoat` som den senaste lyckade distributionen av vår var revision`1`

1. Återställning till den senaste lyckade revideringen:

    ```bash
    helm rollback odd-billygoat 1
    ```
