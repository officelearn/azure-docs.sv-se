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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: d1cb92e15109775fd120d82df6cfa94b7023d5b9
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Använda Draft med Azure Container Service och Azure Container Registry för att skapa ett program och distribuera det till Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Draft](https://aka.ms/draft) är ett nytt verktyg med öppen källkod som gör det enkelt att utveckla behållarbaserade program och distribuera dem till Kubernetes-kluster utan ingående kunskaper om Docker och Kubernetes (t.o.m. utan att installera dem). Med verktyg som Draft kan du och dina team fokusera på att utveckla program med Kubernetes i stället för att lägga så mycket tid och kraft på infrastrukturen.

Du kan använda Draft med alla Docker-avbildningsregister och Kubernetes-kluster, även lokalt. Den här kursen visar hur du använder ACS med Kubernetes och ACR för att skapa en levande men säker developer pipeline i Kubernetes med utkast och hur du använder Azure DNS gör att utvecklare pipeline att andra kan se på en annan domän.


## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Du kan enkelt [skapa ett nytt Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), men du måste utföra följande steg:

1. Skapa en Azure-resursgrupp för att hantera ditt ACR-register och Kubernetes-klustret i ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Skapa en ACR bild registret med hjälp av [az acr skapa](/cli/azure/acr#create) och kontrollera att den `--admin-enabled` alternativet är inställt på `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic --admin-enabled true 
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Skapa en Azure Container Service med Kubernetes

Nu är du redo att använda [az acs create](/cli/azure/acs#create) för att skapa ett ACS-kluster med Kubernetes som `--orchestrator-type`-värde.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
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

## <a name="install-and-configure-draft"></a>Installera och konfigurera Draft


1. Hämta utkast för din miljö på https://github.com/Azure/draft/releases och installera i din sökväg så att kommandot kan användas.
2. Hämta helm för din miljö på https://github.com/kubernetes/helm/releases och [installera den i din sökväg så att kommandot kan användas för](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Konfigurera Draft för att använda ditt register och skapa underdomäner för varje Helm-diagram som skapas. För att konfigurera Draft behöver du:
  - ditt Azure Container Registry-namn (`draftacsdemo` i det här exemplet)
  - din registernyckel, eller ditt lösenord, från `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

  Anropa `draft init` konfigurationsprocessen efterfrågar värdena ovan; Observera att URL-format för registret URL är namnet på registret (i det här exemplet `draftacsdemo`) plus `.azurecr.io`. Ditt användarnamn är registret namnet på en egen. Processen ser ut ungefär så här första gången du kör den.
 ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
```

Nu är du redo att distribuera ett program.


## <a name="build-and-deploy-an-application"></a>Skapa och distribuera ett program

I Draft-databasen finns [sex enkla exempelprogram](https://github.com/Azure/draft/tree/master/examples). Klona lagringsplatsen och vi använder den [Java-exemplet](https://github.com/Azure/draft/tree/master/examples/java). Ändra till exempel/java-katalogen och typen `draft create` att skapa programmet. Det ska se ut som i följande exempel.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Utdata innehåller en Dockerfile och ett Helm-diagram. För att skapa och distribuera skriver du bara `draft up`. Utdata är omfattande, men ska vara som i följande exempel.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>På ett säkert sätt visa ditt program

Din behållare körs nu i ACS. Du kan visa den i `draft connect` -kommandot, vilket skapar en säker anslutning till klustrets IP-adresser med en specifik port för ditt program så att du kan visa den lokalt. Om det lyckas, leta efter URL för att ansluta till din app på den första raden efter den **lyckade** indikatorn.

> [!NOTE]
> Om du får ett meddelande om att inga skida var klar vänta en stund och försök igen, eller kan du titta på skida bli klar med `kubectl get pods -w` och försök sedan igen när de gör.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

I föregående exempel kan du skriva `curl -s http://localhost:46143` ta emot svar, `Hello World, I'm Java!`. När du CTRL + eller CMD + C (beroende på miljön OS), säker tunnel är datakanalen och du kan fortsätta iteration.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Dela ditt program genom att konfigurera en distribution domän med Azure DNS

Utvecklare iteration loop som utkast skapar i föregående steg har utförts. Du kan dock dela ditt program via internet genom att:
1. Installera en ingång i ACS-kluster (för offentliga IP-adresser som du vill visa appen på)
2. Delegera en anpassad domän till Azure DNS och mappa domänen till IP tilldelar adress ACS ingång-styrenhet

### <a name="use-helm-to-install-the-ingress-controller"></a>Använd helm för att installera ingång-styrenhet.
Använd **helm** att söka efter och installera `stable/traefik`, en ingång-styrenhet, aktivera inkommande begäranden för din versioner.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Nu ställer du in en bevakning på `ingress`-kontrollanten för att fånga det externa IP-värdet när det distribueras. Det är den här IP-adressen som [mappas till din distributionsdomän](#wire-up-deployment-domain) i nästa avsnitt.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

I det här fallet är den externa IP-adressen för distributionsdomänen `13.64.108.240`. Nu kan du mappa din domän till den här IP-adressen.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mappa ingång IP-Adressen till en anpassad underdomän

Draft skapar en version för varje Helm-diagram som skapas (varje program du arbetar med). Var och en hämtar ett genererat namn som används av **utkast** som en _underdomän_ ovanpå roten _distribution domän_ som du bestämmer. (I det här exemplet använder vi `squillace.io` som distributionsdomän.) För att aktivera det här underdomänbeteendet måste du skapa en A-post för `'*.draft'` i dina DNS-poster för distributionsdomänen, så att varje genererad underdomän dirigeras till Kubernetes-klustrets ingress-kontrollant. 

Din domänleverantör har ett eget sätt att tilldela DNS-servrar. Gör så här om du vill [delegera dina DNS-servrar till Azure DNS](../../dns/dns-delegate-domain-azure-dns.md):

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
3. Lägg till DNS-servrarna du får i domänleverantören för din distributionsdomän så att du kan använda Azure DNS för att peka om domänen som du vill. Hur du gör detta varierar efter domän ange; [Delegera din domän nameservers till Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) innehåller vissa uppgifter som du bör känna till. 
4. När din domän har delegerats till Azure DNS, skapa en A-postuppsättningen post för din distribution domänmappning till den `ingress` IP från steg 2 i föregående avsnitt.
  ```azurecli
  az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
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
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
  }
  ```
5. Installera om **utkast**

   1. Ta bort **draftd** från klustret genom att skriva `helm delete --purge draft`. 
   2. Installera om **utkast** med hjälp av samma `draft-init` kommando, men med den `--ingress-enabled` alternativ:
    ```bash
    draft init --ingress-enabled
    ```
   Svara på frågorna som du gjorde första gången ovan. Du har dock en fler frågor att besvara, med den fullständiga domännamnet sökvägen som du har konfigurerat med Azure DNS.

6. Ange din toppnivådomänen för inkommande trafik (t.ex. draft.example.com): draft.squillace.io
7. När du anropar `draft up` nu, kommer du att kunna se ditt program (eller `curl` den) på URL i formatet `<appname>.draft.<domain>.<top-level-domain>`. I det här exemplet `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Nästa steg

Nu när du har ett Kubernetes ACS-kluster kan du undersöka med hjälp av [Azure Container Registry](../../container-registry/container-registry-intro.md) för att skapa flera olika distributioner för det här scenariot. Du kan till exempel skapa en DNS-postuppsättning för draft._basedomain.toplevel_ som styr saker från en djupare underdomän för specifika ACS-distributioner.






