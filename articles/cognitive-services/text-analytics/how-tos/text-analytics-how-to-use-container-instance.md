---
title: Kör Kubernetes-tjänst
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Distribuera behållaren språk identifiering med en aktiv exemplet till Azure Kubernetes Service och testa den i en webbläsare.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 3541376331725fddcd58d94625f5d761ef159c97
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526512"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Distribuera språk identifiering av behållare till Azure Kubernetes Service

Lär dig hur du distribuerar du behållaren för identifiering av språk. Den här proceduren visar dig hur skapa lokala Docker-behållare, överför behållarna till ditt eget privata behållarregister, köra behållaren i ett Kubernetes-kluster och testa den i en webbläsare. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt. Använd inte Azure Cloud shell. 

* Använda en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Git](https://git-scm.com/downloads) för ditt operativsystem så kan du klona den [exempel](https://github.com/Azure-Samples/cognitive-services-containers-samples) används i den här proceduren. 
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
* [Docker-motorn](https://www.docker.com/products/docker-engine) och verifiera att Docker CLI fungerar i ett konsolfönster.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe). 
* En Azure-resurs med rätt prisnivån. Inte alla prisnivåer arbetar du med den här behållaren:
    * **Textanalys** resurs med F0 eller standardpriserna nivåerna endast.
    * **Cognitive Services** resurs med S0 prisnivå.

## <a name="running-the-sample"></a>Köra exemplet

Den här proceduren läser in och kör exemplet Cognitive Services-behållare för språkidentifiering. Exemplet har två behållare, en för klientprogrammet och en för Cognitive Services-behållare. Du måste skicka båda dessa avbildningar till din egen Azure Container Registry. Skapa ett Azure Kubernetes Service för att komma åt dessa avbildningar och köra behållarna när de är på din egen registret. När behållarna som körs, använda den **kubectl** CLI för att titta på hur behållare. Få åtkomst till klientprogrammet med en HTTP-begäran och se resultaten. 

![Konceptuell uppfattning om exemplet behållare som körs](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Exempelbehållare

Exemplet har två behållaravbildningar, en för frontend-webbplatsen. Den andra bilden är en behållare för identifiering av språk som returnerar det identifierade språket (kultur) texten. Behållare är tillgängliga från en extern IP-adress när du är klar. 

### <a name="the-language-frontend-container"></a>Språk-frontend-behållaren

Den här webbplatsen motsvarar ditt eget program för klientsidan som gör begäranden till slutpunkten för identifiering av språk. När proceduren har slutförts visas det identifierade språket av en sträng med tecken genom att gå till webbplatsen behållaren i en webbläsare med `http://<external-IP>/<text-to-analyze>`. Ett exempel på denna URL är `http://132.12.23.255/helloworld!`. Resultatet i webbläsaren är `English`.

### <a name="the-language-container"></a>Behållaren språk

Behållaren språk identifiering i det här specifika är tillgänglig för alla externa begäran. Behållaren har inte ändrats på något sätt så att den standard Cognitive Services container-specifika språkidentifiering API är tillgängligt. 

För den här behållaren är det API: et en POST-begäran för språkidentifiering. Som med alla Cognitive Services-behållare kan du läsa mer om behållaren från dess värdbaserade Swagger-information `http://<external-IP>:5000/swagger/index.html`. 

Port 5000 är standardporten som används med Cognitive Services-behållare. 

## <a name="create-azure-container-registry-service"></a>Skapa Azure Container Registry-tjänsten

Behållaravbildningarna måste vara tillgängliga för att distribuera behållaren till Azure Kubernetes Service. Skapa din egen Azure Container Registry-tjänst som värd för avbildningar. 

1. Logga in på Azure CLI 

    ```azurecli
    az login
    ```

1. Skapa en resursgrupp med namnet `cogserv-container-rg` att lagra alla resurser som skapats i den här proceduren.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Skapa din egen Azure Container Registry med formatet på namnet på din sedan `registry`, till exempel `pattyregistry`. Använd inte bindestreck eller underline tecken i namnet.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Spara resultatet att hämta den **loginServer** egenskapen. Det här är en del av värdbaserade behållarens-adress som används senare i den `language.yml` filen.

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. Logga in på ditt behållarregister. Du måste logga in innan du kan skicka avbildningar till registret.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Hämta Docker-avbildning för webbplats 

1. Exempelkoden som används i den här proceduren är i exempeldatabasen för Cognitive Services-behållare. Klona databasen om du vill ha en lokal kopia av exemplet.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    När databasen finns på den lokala datorn, hitta webbplatsen i den [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) directory. Den här webbplatsen fungerar som klientprogram som anropar språkidentifiering API som finns i behållaren för identifiering av språk.  

1. Skapa Docker-avbildningen för den här webbplatsen. Kontrollera att konsolen finns i den [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) katalogen där Dockerfile som finns när du kör följande kommando:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 . 
    ```

    Om du vill följa versionen på ditt behållarregister, lägger du till taggen med en versionsformat som `v1`. 

1. Överför avbildningen till behållarregistret. Det kan ta några minuter. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Om du får en `unauthorized: authentication required` felet, logga in med den `az acr login --name <your-container-registry-name>` kommando. 

    När processen är klar bör resultatet se ut:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Hämta språk identifiering av Docker-avbildning 

1. Hämta den senaste versionen av Docker-avbildningen till den lokala datorn. Det kan ta några minuter. Om det finns en nyare version av den här behållaren, ändrar du värdet från `1.1.006770001-amd64-preview` till en nyare version. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Tagga avbildningen med ditt behållarregister. Den senaste versionen och ersätta versionen `1.1.006770001-amd64-preview` om du har en senare version. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Överför avbildningen till behållarregistret. Det kan ta några minuter. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Hämta autentiseringsuppgifter för Container Registry

Följande steg krävs för att hämta nödvändig information för att ansluta ditt behållarregister med Azure Kubernetes Service du skapar senare i den här proceduren.

1. Skapa tjänstens huvudnamn.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Spara resultaten `appId` värde för parametern tilldelad person i steg 3, `<appId>`. Spara den `password` för nästa avsnitt klienthemlighet parametern `<client-secret>`.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Hämta ditt container registry-ID.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Spara utdata för scope-parametervärdet `<acrId>`, i nästa steg. Det ser ut som:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Spara hela värdet för steg 3 i det här avsnittet. 

1. Skapa en rolltilldelning för att ge rätt åtkomsten för AKS-klustret ska använda avbildningar som lagras i ditt behållarregister. Ersätt `<appId>` och `<acrId>` med de värden som samlats in i de föregående två stegen.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Skapa Azure Kubernetes Service

1. Skapa Kubernetes-klustret. Alla parametervärden är från föregående avsnitt utom name-parametern. Välj ett namn-värde som anger vem som skapade den och dess syfte, till exempel `patty-kube`. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Det här steget kan ta några minuter. Resultatet är: 

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
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

    Tjänsten har skapats men som har inte den webbplats eller behållare för identifiering av språk ännu.  

1. Hämta autentiseringsuppgifterna för Kubernetes-klustret. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Läsa in definitionen för dirigering till Kubernetes-tjänst

Det här avsnittet använder de **kubectl** CLI för att kommunicera med Azure Kubernetes Service. 

1. Innan du läser in orchestration-definition, kontrollera **kubectl** har åtkomst till noderna.

    ```console
    kubectl get nodes
    ```

    Svaret liknar:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Kopiera följande fil och ge den namnet `language.yml`. Filen har en `service` avsnittet och en `deployment` avsnittet var och en för två container-typer i `language-frontend` webbplats behållare och `language` identifiering av behållare. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Ändra språk-klientdel distribution linjer `language.yml` baserat på följande tabell för att lägga till egna behållaravbildningsnamn för registret, klienthemligheten och text analytics inställningar.

    Inställningar för språk-klientdel|Syfte|
    |--|--|
    |Rad 32<br> `image` Egenskapen|Platsen för den frontend-bilden på ditt Behållarregister<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Rad 44<br> `name` Egenskapen|Container Registry hemligheten för avbildningen, kallas `<client-secret>` i föregående avsnitt.|

1. Ändra språk distribution linjer `language.yml` baserat på följande tabell för att lägga till egna behållaravbildningsnamn för registret, klienthemligheten och text analytics inställningar.

    |Språkinställningarna för distribution|Syfte|
    |--|--|
    |Rad 78<br> `image` Egenskapen|Platsen för den språk-bilden på ditt Behållarregister<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Rad 95<br> `name` Egenskapen|Container Registry hemligheten för avbildningen, kallas `<client-secret>` i föregående avsnitt.|
    |Rad 91<br> `apiKey` Egenskapen|Resursnyckeln din text analytics|
    |Rad 92<br> `billing` Egenskapen|Fakturering för slutpunkten för din text analytics-resurs.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

    Eftersom den **apiKey** och **fakturering endpoint** ställs in som en del av definitionen för Kubernetes-orkestrering, behållaren webbplatsen behöver inte känna till dessa eller skicka dem som en del av begäran. Webbplats-behållaren som refererar till behållaren för identifiering av språk efter dess namn i orchestrator `language`. 

1. Läsa in orchestration-definitionsfilen för det här exemplet från mappen där du skapade och sparade i `language.yml`. 

    ```console
    kubectl apply -f language.yml
    ```

    Svaret är:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Hämta extern IP-adresser för behållare

Två behållare, kontrollera den `language-frontend` och `language` tjänster körs och hämta extern IP-adressen. 

```console
kubectl get all
```

```console
> kubectl get all
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

Om den `EXTERNAL-IP` för tjänsten visas som väntar, kör kommandot på nytt innan den IP-adressen visas innan du fortsätter till nästa steg. 

## <a name="test-the-language-detection-container"></a>Testa behållaren för identifiering av språk

Öppna en webbläsare och navigera till den externa IP-Adressen för den `language` behållare från föregående avsnitt: `http://<external-ip>:5000/swagger/index.html`. Du kan använda den `Try it` funktion i API för att testa slutpunkten för identifiering av språk. 

![Visa behållarens swagger-dokumentation](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testa klienten programbehållare

Ändra URL: en i webbläsaren till externa IP-Adressen för den `language-frontend` behållaren med hjälp av följande format: `http://<external-ip>/helloworld`. Engelska kultur texten i `helloworld` förväntas som `English`.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort Azure-resursgruppen när du är klar med klustret. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Relaterad information

* [kubectl för Docker-användare](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Nästa steg 

* Använder mer [Cognitive Services-behållare](../../cognitive-services-container-support.md)
* Använd Text Analytics Connected Service] (.. / vs-text-ansluten-service.md)


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->
