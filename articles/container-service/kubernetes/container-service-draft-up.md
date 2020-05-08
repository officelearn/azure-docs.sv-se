---
title: FÖRÅLDRAD Använd Draft med Azure Container Service och Azure Container Registry
description: Skapa ett ACS Kubernetes-kluster och ett Azure Container Registry för att skapa ditt första program i Azure med Draft.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 039879cb725c20af3c40698947ea9abe189d08ad
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731771"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>FÖRÅLDRAD Använd Draft med Azure Container Service och Azure Container Registry för att bygga och distribuera ett program till Kubernetes

> [!TIP]
> Den uppdaterade versionen av den här artikeln som använder Azure Kubernetes-tjänsten finns i [använda Draft med Azure Kubernetes service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) är ett nytt verktyg med öppen källkod som gör det enkelt att utveckla containerbaserade program och distribuera dem till Kubernetes-kluster utan ingående kunskaper om Docker och Kubernetes (t.o.m. utan att installera dem). Med verktyg som Draft kan du och dina team fokusera på att utveckla program med Kubernetes i stället för att lägga så mycket tid och kraft på infrastrukturen.

Du kan använda Draft med alla Docker-avbildningsregister och Kubernetes-kluster, även lokalt. I den här självstudien visas hur du använder ACS med Kubernetes och ACR för att skapa en Live-men säker utvecklare i Kubernetes med hjälp av Draft och hur du använder Azure DNS för att visa en pipeline för utvecklare som andra kan se på en domän.


## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry
Du kan enkelt [skapa ett nytt Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), men du måste utföra följande steg:

1. Skapa en Azure-resurs grupp för att hantera ACR-registret och Kubernetes-klustret i ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Skapa ett ACR-avbildnings register med [AZ ACR Create](/cli/azure/acr#az-acr-create) och se `--admin-enabled` till att alternativet är `true`inställt på.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Skapa en Azure Container Service med Kubernetes

Nu är du redo att använda [az acs create](/cli/azure/acs#az-acs-create) för att skapa ett ACS-kluster med Kubernetes som `--orchestrator-type`-värde.
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

Nu när du har ett kluster kan du importera autentiseringsuppgifterna med hjälp av kommandot [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Nu har du en lokal konfigurationsfil för klustret. Den behövs för att Helm och Draft ska kunna utföra sitt arbete.

## <a name="install-and-configure-draft"></a>Installera och konfigurera Draft


1. Hämta utkast för din miljö vid https://github.com/Azure/draft/releases och installera i sökvägen så att kommandot kan användas.
2. Ladda ned Helm för din miljö https://github.com/kubernetes/helm/releases på och [installera den i sökvägen så att kommandot kan användas](https://helm.sh/docs/intro/quickstart#install-helm).
3. Konfigurera Draft för att använda ditt register och skapa underdomäner för varje Helm-diagram som skapas. För att konfigurera Draft behöver du:
   - ditt Azure Container Registry-namn (`draftacsdemo` i det här exemplet)
   - din registernyckel, eller ditt lösenord, från `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`.

   Anropet `draft init` och konfigurations processen efterfrågar värden ovan. Observera att URL-formatet för registrets URL är register namnet (i det här exemplet `draftacsdemo`) plus. `.azurecr.io` Ditt användar namn är register namnet på egen hand. Processen ser ut ungefär så här första gången du kör den.
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

I Draft-databasen finns [sex enkla exempelprogram](https://github.com/Azure/draft/tree/master/examples). Klona lagrings platsen och låt oss använda [Java-exemplet](https://github.com/Azure/draft/tree/master/examples/example-java). Ändra till exemplen/Java-katalogen och skriv `draft create` för att bygga programmet. Det ska se ut som i följande exempel.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Utdata innehåller en Dockerfile och ett Helm-diagram. För att skapa och distribuera skriver du bara `draft up`. Utdata är omfattande, men bör likna följande exempel.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Visa ditt program på ett säkert sätt

Din behållare körs nu i ACS. Om du vill visa det använder `draft connect` du kommandot, som skapar en säker anslutning till klustrets IP-adress med en specifik port för ditt program så att du kan visa den lokalt. Om det lyckas, letar du efter URL: en för att ansluta till din app på den första raden efter **lyckad** indikator.

> [!NOTE]
> Om du får ett meddelande som säger att ingen poddar var klar väntar du en stund och försöker igen, eller så kan du se att poddar blir `kubectl get pods -w` redo med och sedan försöka igen när de gör det.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

I föregående exempel kan du skriva `curl -s http://localhost:46143` för att ta emot svaret. `Hello World, I'm Java!` När du CTRL + eller CMD + C (beroende på din operativ system miljö) är den säkra tunneln avbröts och du kan fortsätta att iterera.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Dela ditt program genom att konfigurera en distributions domän med Azure DNS

Du har redan utfört upprepnings slinga för utvecklare som utkastet skapar i föregående steg. Du kan dock dela ditt program via Internet genom att:
1. Installera ett ingress i ACS-klustret (för att tillhandahålla en offentlig IP-adress som appen ska visas på)
2. Att delegera din anpassade domän till Azure DNS och mappa din domän till IP-adressen ACS tilldelar din ingångs kontroll.

### <a name="use-helm-to-install-the-ingress-controller"></a>Använd Helm för att installera ingångs styrenheten.
Använd **Helm** för att söka efter och `stable/traefik`installera, en ingångs kontroll för att aktivera inkommande begär Anden för dina versioner.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Nu ställer du in en bevakning på `ingress`-kontrollanten för att fånga det externa IP-värdet när det distribueras. Det är den här IP-adressen som mappas till din distributionsdomän i nästa avsnitt.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

I det här fallet är den externa IP-adressen för distributionsdomänen `13.64.108.240`. Nu kan du mappa din domän till den här IP-adressen.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mappa den inkommande IP-adressen till en anpassad under domän

Draft skapar en version för varje Helm-diagram som skapas (varje program du arbetar med). Var och en får ett genererat namn som används av **Draft** som en under _domän_ ovanpå den rot _distributions domän_ som du styr. (I det här exemplet använder `squillace.io` vi som distributions domän.) Om du vill aktivera det här under domän beteendet måste du skapa en `'*.draft'` post för i dina DNS-poster för distributions domänen, så att varje genererad under domän dirigeras till Kubernetes-klustrets ingångs kontroll. 

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
   Använd kommandot [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) för att erhålla namnservrar för att delegera DNS-kontroll till Azure DNS för din domän.
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
3. Lägg till DNS-servrarna du får i domänleverantören för din distributionsdomän så att du kan använda Azure DNS för att peka om domänen som du vill. Hur du gör detta beror på vilken domän du har. [delegera din domän namnservrar till Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) innehåller en del av den information som du bör känna till. 
4. När din domän har delegerats till Azure DNS skapar du en post uppsättnings post för distributions domän mappningen till `ingress` IP-adressen från steg 2 i föregående avsnitt.
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

   1. Ta bort **utkast** från klustret genom att skriva `helm delete --purge draft`. 
   2. Installera om **utkastet** med samma `draft-init` kommando, men med `--ingress-enabled` alternativet:
      ```bash
      draft init --ingress-enabled
      ```
      Svara på frågorna som du gjorde första gången ovan. Men du har en fråga att svara på, med den fullständiga domän Sök vägen som du konfigurerade med Azure DNS.

6. Ange domänen på den översta nivån för ingress (t. ex. draft.example.com): draft.squillace.io
7. När du anropar `draft up` den här tiden kommer du att kunna se ditt program (eller `curl` det) i webb adressen till formuläret `<appname>.draft.<domain>.<top-level-domain>`. I det här exemplet är `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Nästa steg

Nu när du har ett Kubernetes ACS-kluster kan du undersöka med hjälp av [Azure Container Registry](../../container-registry/container-registry-intro.md) för att skapa flera olika distributioner för det här scenariot. Du kan till exempel skapa en DNS-postuppsättning för draft._basedomain.toplevel_ som styr saker från en djupare underdomän för specifika ACS-distributioner.






