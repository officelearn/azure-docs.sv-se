---
title: "Använda Draft med Azure Container Service och Azure Container Registry | Microsoft Docs"
description: "Skapa ett ACS Kubernetes-kluster och ett Azure Container Registry för att skapa ditt första program i Azure med Draft."
services: container-service
documentationcenter: 
author: squillace
manager: gamonroy
editor: 
tags: draft, helm, acs, azure-container-service
keywords: Docker, Containers, microservices, Kubernetes, Draft, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: rasquill
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dc3ae52b1ec6717c7e19a160e3e7ea5d211f1f5f
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017



---

<a id="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes" class="xliff"></a>

# Använda Draft med Azure Container Service och Azure Container Registry för att skapa ett program och distribuera det till Kubernetes

[Draft](https://aka.ms/draft) är ett nytt verktyg med öppen källkod som gör det enkelt att utveckla behållarbaserade program och distribuera dem till Kubernetes-kluster utan ingående kunskaper om Docker och Kubernetes (t.o.m. utan att installera dem). Med verktyg som Draft kan du och dina team fokusera på att utveckla program med Kubernetes i stället för att lägga så mycket tid och kraft på infrastrukturen.

Du kan använda Draft med alla Docker-avbildningsregister och Kubernetes-kluster, även lokalt. Den här kursen visar hur du använder ACS med Kubernetes, ACR och Azure DNS för att skapa en CI/CD-utvecklarpipeline med Draft.


<a id="create-an-azure-container-registry" class="xliff"></a>

## Skapa ett Azure Container Registry
Du kan enkelt [skapa ett nytt Azure Container Registry](../container-registry/container-registry-get-started-azure-cli.md), men du måste utföra följande steg:

1. Skapa en Azure-resursgrupp för att hantera ditt ACR-register och Kubernetes-klustret i ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Skapa ett ACR-avbildningsregister med hjälp av [az acr create](/cli/azure/acr#create)
      ```azurecli
      az acr create -g draft -n draftacs --sku Basic --admin-enabled true -l eastus
      ```


<a id="create-an-azure-container-service-with-kubernetes" class="xliff"></a>

## Skapa en Azure Container Service med Kubernetes

Nu är du redo att använda [az acs create](/cli/azure/acs#create) för att skapa ett ACS-kluster med Kubernetes som `--orchestrator-type`-värde.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes
```

> [!NOTE]
> Eftersom Kubernetes inte är standardorchestratortypen måste du använda växeln `--orchestrator-type kubernetes`.

Utdata ser ut ungefär så här vid ett lyckat resultat.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Nu när du har ett kluster kan du importera autentiseringsuppgifterna med hjälp av kommandot [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Nu har du en lokal konfigurationsfil för klustret. Den behövs för att Helm och Draft ska kunna utföra sitt arbete.

<a id="install-and-configure-draft" class="xliff"></a>

## Installera och konfigurera Draft
Installationsinstruktioner för Draft finns i [Draft-lagringsplatsen](https://github.com/Azure/draft/blob/master/docs/install.md). Instruktionerna är relativt enkla, men du måste göra vissa inställningar eftersom verktyget är beroende av [Helm](https://aka.ms/helm) för att skapa och distribuera ett Helm-diagram till Kubernetes-klustret.

1. [Ladda ned och installera Helm](https://aka.ms/helm#install).
2. Använd Helm för att söka efter och installera `stable/traefik`, och ingress-kontrollanten för att aktivera inkommande begäranden för dina versioner.
    ```bash
    $ helm search traefik
    NAME            VERSION DESCRIPTION
    stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

    $ helm install stable/traefik --name ingress
    ```
    Nu ställer du in en bevakning på `ingress`-kontrollanten för att fånga det externa IP-värdet när det distribueras. Det är den här IP-adressen som [mappas till din distributionsdomän](#wire-up-deployment-domain) i nästa avsnitt.

    ```bash
    kubectl get svc -w
    NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
    ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
    kubernetes                    10.0.0.1       <none>          443/TCP                      7h
    ```

    I det här fallet är den externa IP-adressen för distributionsdomänen `13.64.108.240`. Nu kan du mappa din domän till den här IP-adressen.

<a id="wire-up-deployment-domain" class="xliff"></a>

## Ställa in distributionsdomän

Draft skapar en version för varje Helm-diagram som skapas (varje program du arbetar med). Var och en får ett genererat namn som används av Draft som en _underdomän_ ovanför _distributionsrotdomänen_ som du kontrollerar. (I det här exemplet använder vi `squillace.io` som distributionsdomän.) För att aktivera det här underdomänbeteendet måste du skapa en A-post för `'*'` i dina DNS-poster för distributionsdomänen, så att varje genererad underdomän dirigeras till Kubernetes-klustrets ingress-kontrollant.

Din domänleverantör har ett eget sätt att tilldela DNS-servrar. Gör så här om du vill [delegera dina DNS-servrar till Azure DNS](../dns/dns-delegate-domain-azure-dns.md):

1. Skapa en resursgrupp för din zon.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Skapa en DNS-zon för din domän.
Använd kommandot [az network dns zone create](/cli/azure/network/dns/zone#create) för att erhålla namnservrar för att delegera DNS-kontroll till Azure DNS för din domän.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Lägg till DNS-servrarna du får i domänleverantören för din distributionsdomän så att du kan använda Azure DNS för att peka om domänen som du vill.
4. Skapa en post för A-postuppsättningen för mappningen av distributionsdomänen till IP-adressen för `ingress` från steg 2 i föregående avsnitt.
    ```azurecli
    az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*' -g squillace.io -z squillace.io
    ```
Utdata ser ut ungefär så här:
    ```json
    {
      "arecords": [
        {
          "ipv4Address": "13.64.108.240"
        }
      ],
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
      "metadata": null,
      "name": "*",
      "resourceGroup": "squillace.io",
      "ttl": 3600,
      "type": "Microsoft.Network/dnszones/A"
    }
    ```

5. Konfigurera Draft för att använda ditt register och skapa underdomäner för varje Helm-diagram som skapas. För att konfigurera Draft behöver du:
  - ditt Azure Container Registry-namn (`draft` i det här exemplet)
  - din registernyckel, eller ditt lösenord, från `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.
  - rotdistributionsdomänen som du har konfigurerat för att mappa till den externa Kubernetes ingress-IP-adressen (`squillace.io` i det här fallet)

  Anropa `draft init` så efterfrågas värdena ovan i konfigurationsprocessen. Processen ser ut ungefär så här första gången du kör den.
    ```
    draft init
    Creating pack ruby...
    Creating pack node...
    Creating pack gradle...
    Creating pack maven...
    Creating pack php...
    Creating pack python...
    Creating pack dotnetcore...
    Creating pack golang...
    $DRAFT_HOME has been configured at /Users/ralphsquillace/.draft.

    In order to install Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io, quay.io, myregistry.azurecr.io): draft.azurecr.io
    2. Enter your username: draft
    3. Enter your password:
    4. Enter your org where Draft will push images [draft]: draft
    5. Enter your top-level domain for ingress (e.g. draft.example.com): squillace.io
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
    ```

Nu är du redo att distribuera ett program.


<a id="build-and-deploy-an-application" class="xliff"></a>

## Skapa och distribuera ett program

I Draft-databasen finns [sex enkla exempelprogram](https://github.com/Azure/draft/tree/master/examples). Klona databasen och använd [Python-exemplet](https://github.com/Azure/draft/tree/master/examples/python). Ändra till katalogen examples/Python och skriv `draft create` för att skapa programmet. Det ska se ut som i följande exempel.
```bash
$ draft create
--> Python app detected
--> Ready to sail
```

Utdata innehåller en Dockerfile och ett Helm-diagram. För att skapa och distribuera skriver du bara `draft up`. Utdata är omfattande, men inleds som i följande exempel.
```bash
$ draft up
--> Building Dockerfile
Step 1 : FROM python:onbuild
onbuild: Pulling from library/python
10a267c67f42: Pulling fs layer
fb5937da9414: Pulling fs layer
9021b2326a1e: Pulling fs layer
dbed9b09434e: Pulling fs layer
ea8a37f15161: Pulling fs layer
<snip>
```

Och om körningen lyckas blir resultatet ungefär som i följande exempel.
```bash
ab68189731eb: Pushed
53c0ab0341bee12d01be3d3c192fbd63562af7f1: digest: sha256:bb0450ec37acf67ed461c1512ef21f58a500ff9326ce3ec623ce1e4427df9765 size: 2841
--> Deploying to Kubernetes
--> Status: DEPLOYED
--> Notes:

  http://gangly-bronco.squillace.io to access your application

Watching local files for changes...
```

Oavsett vilket namn diagrammet har kan du nu ange `curl http://gangly-bronco.squillace.io` för att få svaret `Hello World!`.

<a id="next-steps" class="xliff"></a>

## Nästa steg

Nu när du har ett Kubernetes ACS-kluster kan du undersöka med hjälp av [Azure Container Registry](../container-registry/container-registry-intro.md) för att skapa flera olika distributioner för det här scenariot. Du kan till exempel skapa en DNS-postuppsättning för draft._basedomain.toplevel_ som styr saker från en djupare underdomän för specifika ACS-distributioner.







