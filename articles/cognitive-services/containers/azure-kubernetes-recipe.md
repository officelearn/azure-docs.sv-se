---
title: Köra behållare för språkidentifiering i Kubernetes-tjänsten
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Distribuera språkidentifieringsbehållaren, med ett exempel som körs, till Azure Kubernetes-tjänsten och testa den i en webbläsare.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399171"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Distribuera språkidentifieringsbehållaren för textanalys till Azure Kubernetes-tjänsten

Lär dig hur du distribuerar behållaren för språkidentifiering. Den här proceduren visar hur du skapar de lokala Docker-behållarna, skickar behållarna till ditt eget privata behållarregister, kör behållaren i ett Kubernetes-kluster och testar den i en webbläsare.

## <a name="prerequisites"></a>Krav

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud-skalet.

* Använd en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* [Git](https://git-scm.com/downloads) för ditt operativsystem så att du kan klona [exemplet](https://github.com/Azure-Samples/cognitive-services-containers-samples) som används i den här proceduren.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Docker-motorn](https://www.docker.com/products/docker-engine) och verifiera att Docker CLI fungerar i ett konsolfönster.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* En Azure-resurs med rätt prisnivå. Alla prisnivåer fungerar inte med den här behållaren:
  * **Textanalysresurs** med endast F0- eller Standardprisnivåer.
  * **Cognitive Services-resurs** med prisnivån S0.

## <a name="running-the-sample"></a>Köra exemplet

Den här proceduren läser in och kör exemplet Med Cognitive Services-behållaren för språkidentifiering. Exemplet innehåller två behållare, en för klientprogrammet och en för Cognitive Services-behållaren. Vi skickar båda dessa avbildningar till Azure Container Registry. När de är på ditt eget register, skapa en Azure Kubernetes-tjänst för att komma åt dessa avbildningar och köra behållarna. När behållarna är igång använder du **kubectl** CLI för att titta på behållarnas prestanda. Få tillgång till klientprogrammet med en HTTP-begäran och se resultatet.

![Konceptuell idé om att köra provbehållare](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Provbehållarna

Exemplet innehåller två behållarbilder, en för frontend-webbplatsen. Den andra bilden är språkidentifieringsbehållaren som returnerar det identifierade språket (kulturen) i texten. Båda behållarna är tillgängliga från en extern IP när du är klar.

### <a name="the-language-frontend-container"></a>Behållaren för språkfrontend

Den här webbplatsen motsvarar ditt eget program på klientsidan som gör begäranden om slutpunkten för språkidentifiering. När proceduren är klar får du det identifierade språket för en teckensträng `http://<external-IP>/<text-to-analyze>`genom att komma åt webbplatsbehållaren i en webbläsare med . Ett exempel på `http://132.12.23.255/helloworld!`den här webbadressen är . Resultatet i webbläsaren `English`är .

### <a name="the-language-container"></a>Språkbehållaren

Språkidentifieringsbehållaren, i den här specifika proceduren, är tillgänglig för alla externa begäranden. Behållaren har inte ändrats på något sätt så det standarda Cognitive Services-behållarspecifika språkidentifierings-API:et är tillgängligt.

För den här behållaren är det API:et en POST-begäran om språkidentifiering. Som med alla Cognitive Services-behållare kan du läsa mer om behållaren från dess värdbaserade Swagger-information. `http://<external-IP>:5000/swagger/index.html`

Port 5000 är standardporten som används med Cognitive Services-behållarna.

## <a name="create-azure-container-registry-service"></a>Skapa azure-behållarregistertjänst

Om du vill distribuera behållaren till Azure Kubernetes-tjänsten måste behållaravbildningarna vara tillgängliga. Skapa din egen Azure Container Registry-tjänst som värd för avbildningarna.

1. Logga in på Azure CLI

    ```azurecli-interactive
    az login
    ```

1. Skapa en resursgrupp med namnet `cogserv-container-rg` för att lagra alla resurser som skapas i den här proceduren.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Skapa ditt eget Azure Container-register med `registry`formatet på `pattyregistry`ditt namn och sedan , till exempel . Använd inte streck eller understrykningstecken i namnet.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Spara resultaten för **loginServer** att få inloggningServeregenskapen. Detta kommer att vara en del av den värdbaserade behållarens adress, som används senare i `language.yml` filen.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Logga in i behållarregistret. Du måste logga in innan du kan skicka bilder till ditt register.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Hämta bild av docker för webbplats

1. Exempelkoden som används i den här proceduren finns i databasen med exempeldatabasen för Cognitive Services-behållare. Klona databasen så att den har en lokal kopia av exemplet.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    När databasen finns på den lokala datorn hittar du webbplatsen i katalogen [\dotnet\Language\FrontendService.](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) Den här webbplatsen fungerar som klientprogrammet som anropar API:et för språkidentifiering som finns i språkidentifieringsbehållaren.  

1. Skapa Docker-avbildningen för den här webbplatsen. Kontrollera att konsolen finns i katalogen [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) där Dockerfile finns när du kör följande kommando:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Om du vill spåra versionen i behållarregistret lägger du `v1`till taggen med ett versionsformat, till exempel .

1. Skicka avbildningen till behållarregistret. Det kan ta några minuter.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Om du `unauthorized: authentication required` får ett felmeddelande, logga in med kommandot. `az acr login --name <your-container-registry-name>` 

    När processen är klar bör resultaten likna:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Hämta dockeravbildning för språkidentifiering

1. Dra den senaste versionen av Docker-avbildningen till den lokala datorn. Det kan ta några minuter. Om det finns en nyare version av `1.1.006770001-amd64-preview` den här behållaren ändrar du värdet från till den nyare versionen.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Taggavbildning med behållarregistret. Hitta den senaste versionen `1.1.006770001-amd64-preview` och ersätt versionen om du har en nyare version. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Skicka avbildningen till behållarregistret. Det kan ta några minuter.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Hämta autentiseringsuppgifter för behållarregister

Följande steg behövs för att få den information som krävs för att ansluta behållarregistret till Azure Kubernetes-tjänsten som du skapar senare i den här proceduren.

1. Skapa tjänstens huvudnamn.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Spara resultatvärdet `appId` för parametern för mottagare `<appId>`i steg 3. Spara `password` parametern för nästa avsnitts `<client-secret>`klienthemlighet .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Hämta ditt behållarregister-ID.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Spara utdata för scopeparametervärdet `<acrId>`i nästa steg. Det ser ut som:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Spara det fullständiga värdet för steg 3 i det här avsnittet.

1. Skapa en rolltilldelning om du vill bevilja rätt åtkomst för AKS-klustret för att använda avbildningar som lagras i behållarregistret. Ersätt `<appId>` `<acrId>` och med de värden som samlats in i de två föregående stegen.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Skapa Azure Kubernetes-tjänsten

1. Skapa Kubernetes-klustret. Alla parametervärden kommer från föregående avsnitt utom namnparametern. Välj ett namn som anger vem som skapade `patty-kube`det och dess syfte, till exempel .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Det här steget kan ta några minuter. Resultatet är:

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Tjänsten skapas men den har inte webbplatsbehållaren eller språkidentifieringsbehållaren ännu.  

1. Hämta autentiseringsuppgifter för Kubernetes-klustret.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Ladda orchestration-definitionen i din Kubernetes-tjänst

Det här avsnittet använder **kubectl** CLI för att prata med Azure Kubernetes Service.

1. Innan orchestration-definitionen läses in har **kubectl** åtkomst till noderna.

    ```console
    kubectl get nodes
    ```

    Svaret ser ut som:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Kopiera följande fil och `language.yml`namnge den . Filen har `service` ett avsnitt `deployment` och ett avsnitt vardera `language-frontend` för de `language` två behållartyperna, webbplatsbehållaren och identifieringsbehållaren.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Ändra distributionsraderna `language.yml` för språk frontend-distribution baserat på följande tabell för att lägga till egna inställningar för behållarregisteravbildning, klienthemlighet och textanalysinställningar.

    Inställningar för distribution av språk frontend|Syfte|
    |--|--|
    |Linje 32<br> `image`Egenskapen|Bildplats för frontend-avbildningen i behållarregistret<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Linje 44<br> `name`Egenskapen|Behållare registret hemlighet för avbildningen, `<client-secret>` kallad i ett tidigare avsnitt.|

1. Ändra språkdistributionsraderna `language.yml` för baserat på följande tabell för att lägga till egna inställningar för behållarregisteravbildningar, klienthemlighet och textanalysinställningar.

    |Inställningar för språkdistribution|Syfte|
    |--|--|
    |Linje 78<br> `image`Egenskapen|Bildplats för språkavbildningen i behållarregistret<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Linje 95<br> `name`Egenskapen|Behållare registret hemlighet för avbildningen, `<client-secret>` kallad i ett tidigare avsnitt.|
    |Linje 91<br> `apiKey`Egenskapen|Resursnyckeln för textanalys|
    |Linje 92<br> `billing`Egenskapen|Slutpunkten för fakturering för din textanalysresurs.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Eftersom **apiKey-** och **faktureringsslutpunkten** anges som en del av Kubernetes orchestration definition, behöver webbplatsbehållaren inte känna till dessa eller skicka dem som en del av begäran. Webbplatsbehållaren refererar till språkidentifieringsbehållaren med dess orchestrator-namn `language`.

1. Läs in orchestration definition-filen för det här exemplet `language.yml`från mappen där du skapade och sparade .

    ```console
    kubectl apply -f language.yml
    ```

    Svaret är:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Hämta externa IPs av behållare

För de två behållarna kontrollerar du `language-frontend` att tjänsterna och `language` tjänsterna körs och hämtar den externa IP-adressen.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Om `EXTERNAL-IP` tjänsten visas som väntande kör du kommandot igen tills IP-adressen visas innan du går vidare till nästa steg.

## <a name="test-the-language-detection-container"></a>Testa behållaren för språkidentifiering

Öppna en webbläsare och navigera till `language` behållarens externa `http://<external-ip>:5000/swagger/index.html`IP från föregående avsnitt: . Du kan `Try it` använda funktionen för API:et för att testa slutpunkten för språkidentifiering.

![Visa behållarens swagger-dokumentation](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testa klientprogrambehållaren

Ändra URL:en i webbläsaren till `language-frontend` behållarens externa IP `http://<external-ip>/helloworld`med följande format: . Den engelska kulturtexten `helloworld` av `English`förutsags som .

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med klustret tar du bort Azure-resursgruppen.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Relaterad information

* [kubectl för Docker-användare](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Behållare för kognitiva tjänster](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->