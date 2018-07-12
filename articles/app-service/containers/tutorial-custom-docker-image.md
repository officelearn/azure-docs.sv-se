---
title: Använda en anpassad Docker-avbildning för Web App for Containers – Azure | Microsoft Docs
description: Så här använder du en anpassad Docker-avbildning för Web App for Containers.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: SyntaxC4
manager: SyntaxC4
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 887ed316605ab423159ef0d2e07f0960c702ed8b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38317965"
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Använda du en anpassad Docker-avbildning för Web App for Containers

[Web App for Containers](app-service-linux-intro.md) tillhandahåller fördefinierade Docker-avbildningar i Linux med stöd för specifika versioner, till exempel PHP 7.0 och Node.js 4.5. Web App for Containers använder teknik för Docker-behållare för att vara värd för inbyggda avbildningar och anpassade avbildningar som plattform som en tjänst. I den här självstudien lär du dig att skapa en anpassad Docker-avbildning och distribuera den till Web App for Containers. Det här mönstret är användbart när de inbyggda avbildningarna inte inkluderar ditt språkval eller när ditt program kräver en specifik konfiguration som inte ingår i de inbyggda avbildningarna.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Distribuera en anpassad Docker-avbildning till Azure
> * Konfigurera miljövariabler för att köra behållaren
> * Uppdatera Docker-avbildningen och distribuera om den
> * Anslut till behållaren med SSH
> * Distribuera en privat Docker-avbildning till Azure

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)
* Ett [Docker Hub-konto](https://docs.docker.com/docker-id/)

## <a name="download-the-sample"></a>Hämta exemplet

Öppna terminalfönstret och kör följande kommando för att klona exempelappens lagringsplats till din lokala dator. Ändra sedan till katalogen som innehåller exempelkoden.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Skapa avbildningen från Docker-filen

På Git-lagringsplatsen tar du en titt på _Dockerfile_. Den här filen beskriver Python-miljön som krävs för att köra programmet. Dessutom konfigurerar avbildningen en [SSH](https://www.ssh.com/ssh/protocol/)-server för säker kommunikation mellan behållaren och värden.

```docker
FROM python:3.4

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

För att skapa Docker-avbildningen kör du kommandot `docker build` och anger ett namn, _mydockerimage_, och en tagg, _v1.0.0_. Ersätt _\<docker-id>_ med ditt Docker Hub-konto-ID.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Kommandot producerar utdata som liknar följande:

```
# The output from the commands in this article has been shortened for brevity.

Sending build context to Docker daemon  5.558MB
Step 1/13 : FROM python:3.4
 ---> 9ff45ddb54e9
Step 2/13 : RUN mkdir /code
 ---> Using cache
 ---> f3f3ac01db0a
Step 3/13 : WORKDIR /code
 ---> Using cache
 ---> 38b32f15b442
.
.
.
Step 13/13 : ENTRYPOINT init.sh
 ---> Running in 5904e4c70043
 ---> e7cf08275692
Removing intermediate container 5904e4c70043
Successfully built e7cf08275692
Successfully tagged cephalin/mydockerimage:v1.0.0
```

Testa att versionen fungerar genom att köra Docker-behållaren. Utfärda kommandot [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) och skicka avbildningens namn och tagg. Se till att ange porten som använder argumentet `-p`.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Verifiera att webbappen och behållaren fungerar som de ska genom att bläddra till `http://localhost:2222`.

![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

> [!NOTE] 
> Du kan också ansluta till appbehållaren direkt från din lokala utvecklingsdator med SSH, SFTP eller Visual Studio Code (för livefelsökning av Node.js-appar). Mer information finns i [Remote debugging and SSH in App Service on Linux](https://aka.ms/linux-debug) (Fjärrfelsökning och SSH i App Service på Linux).
>

## <a name="push-the-docker-image-to-docker-hub"></a>Push-överför Docker-avbildningen till Docker Hub

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster för avbildningar och behållare. För att dela avbildningen måste du push-överföra den till ett register. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Push-överför du från ett privat Docker-register? Se de valfria instruktionerna för att [använda en Docker-avbildning från ett privat register](#use-a-docker-image-from-any-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker Hub är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna lagringsplatser, antingen offentliga eller privata. För att push-överföra en anpassad Docker-avbildning till den offentliga Docker Hub använder du kommandot [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) och anger ett fullständigt avbildningsnamn och tagg. Ett fullständigt avbildningsnamn och tagg ser ut som i följande exempel:

```
<docker-id>/image-name:tag
```

Innan du kan push-överföra en avbildning måste du logga in på Docker Hub med kommandot [`docker login`](https://docs.docker.com/engine/reference/commandline/login/). Ersätt _\<docker-id >_ med ditt kontonamn och ange ditt lösenord i konsolen i kommandotolken.

```bash
docker login --username <docker-id>
```

Ett meddelande om att ”inloggningen har slutförts” bekräftar att du är inloggad. När du är inloggad kan du push-överföra avbildningen till Docker Hub med kommandot [`docker push`](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Verifiera att push-överföringen lyckades genom att undersöka kommandots utdata.

```
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
c33197c3f6d4: Pushed
ccd2c850ee43: Pushed
02dff2853466: Pushed
6ce78153632a: Pushed
efef3f03cc58: Pushed
3439624d77fb: Pushed
3a07adfb35c5: Pushed
2fcec228e1b7: Mounted from library/python
97d2d3bae505: Mounted from library/python
95aadeabf504: Mounted from library/python
b456afdc9996: Mounted from library/python
d752a0310ee4: Mounted from library/python
db64edce4b5b: Mounted from library/python
d5d60fc34309: Mounted from library/python
c01c63c6823d: Mounted from library/python
v1.0.0: digest: sha256:21f2798b20555f4143f2ca0591a43b4f6c8138406041f2d32ec908974feced66 size: 3676
```

<!--
# [Private Registry](#tab/private-registry)

// Move Private Registry instructions here when Tabbed Conceptual bug is fixed

---
-->

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuera appen till Azure

Du kan vara värd för ursprungliga Linux-program i molnet med Azure Web Apps. Om du vill skapa en Web App for Containers måste du köra Azure CLI-kommandon som skapar en grupp, sedan en tjänstplan och slutligen själva webbappen. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa i Cloud Shell en [webbapp](app-service-linux-intro.md) i `myAppServicePlan` App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Glöm inte att ersätta _<appname>_ med ett unikt appnamn och _\<docker-ID>_ med ditt Docker-ID.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --deployment-container-image-name <docker-ID>/mydockerimage:v1.0.0
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

De flesta Docker-avbildningar har miljövariabler som behöver konfigureras. Om du använder en befintlig Docker-avbildning som har skapats av någon annan kanske avbildningen använder en annan port än 80. Du kan informera Azure om vilken port avbildningen använder med `WEBSITES_PORT`-appinställningen. GitHub-sidan för [Python-exemplet i den här självstudien](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du behöver ställa in `WEBSITES_PORT` på _8000_.

Om du vill konfigurera appinställningar använder du kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i Cloud Shell. Appinställningar är skifteslägeskänsliga och avgränsas med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Distribuerar du från et privat Docker-register? Se de valfria instruktionerna för att [använda en Docker-avbildning från ett privat register](#use-a-docker-image-from-any-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testa webbappen

Verifiera att webbappen fungerar genom att bläddra till den (`http://<app_name>azurewebsites.net`). 

![Testa webbappens portkonfiguration](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Ändra och distribuera om appen

I din lokala Git-lagringsplats öppnar du app/templates/app/index.html. Leta reda på det första HTML-elementet och ändra det.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

När du har ändrat Python-filen och sparat den måste du återskapa och push-överföra den nya Docker-avbildningen. Starta sedan om webbappen för att ändringarna ska börja gälla. Använd samma kommandon som du tidigare har använt i den här självstudien. Du kan läsa [Skapa avbildningen från Docker-filen](#build-the-image-from-the-docker-file) och [Push-överför Docker-avbildningen till Docker Hub](#push-the-docker-image-to-docker-hub). Testa webbappen genom att följa anvisningarna i [Testa webbappen](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Ansluta till Web App for Containers med SSH

SSH möjliggör säker kommunikation mellan en behållare och en klient. För att en anpassad Docker-avbildning ska stödja SSH måste du bygga den i en Dockerfile. Du aktiverar SSH i själva Docker-filen. SSH-instruktionerna har redan lagts till i exempel-dockerfilen, så du kan följa instruktionerna med din egen anpassade avbildning:

* En [RUN](https://docs.docker.com/engine/reference/builder/#run)-instruction som anropar `apt-get` och sedan ställer in lösenordet för rotkontot på `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Den här konfigurationen tillåter inga externa anslutningar till behållaren. SSH är endast tillgängligt via webbplatsen för Kudu/SCM. Kudu-/SCM-webbplatsen autentiseras med autentiseringsuppgifterna för publicering.

* En [COPY](https://docs.docker.com/engine/reference/builder/#copy)-instruktion som instruerar Docker-motorn att kopiera filen [sshd_config](http://man.openbsd.org/sshd_config) till katalogen */etc/ssh/*. Konfigurationsfilen ska baseras på filen [sshd_config](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Filen *sshd_config* måste innehålla följande objekt: 
    > * `Ciphers`måste innehålla minst ett objekt i listan: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs`måste innehålla minst ett objekt i listan: `hmac-sha1,hmac-sha1-96`.

* En [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)-instruktion som exponerar port 2222 i behållaren. Trots att rotlösenordet är känt går det inte att nå port 2222 från internet. Det är en intern port som endast är åtkomlig via behållare inom ett privat virtuellt nätverks nätverksbrygga. Efter det kopierar kommandona SSH-konfigurationsinformationen och startar `ssh`-tjänsten.

    ```docker
    EXPOSE 8000 2222
    ```

* Se till att [starta ssh-tjänsten](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) genom att använda ett kommandoskript i katalogen /bin.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till behållare

Web App for Containers tillåter inga externa anslutningar till behållaren. SSH är endast tillgängligt via Kudu-webbplatsen som är tillgänglig på `https://<app_name>.scm.azurewebsites.net`.

Anslut genom att gå till `https://<app_name>.scm.azurewebsites.net/webssh/host` och logga in med ditt Azure-konto.

Du omdirigeras därefter till en sida som visar en interaktiv konsol. 

Du kanske vill verifiera att vissa program körs i behållaren. Om du vill inspektera behållaren och verifiera körningsprocesserna utfärdar du kommandot `top` i kommandotolken.

```bash
top
```

Kommandot `top` tillgängliggör alla körningsprocesser i en behållare.

```
PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND
 1 root      20   0  945616  35372  15348 S  0.0  2.1   0:04.63 node
20 root      20   0   55180   2776   2516 S  0.0  0.2   0:00.00 sshd
42 root      20   0  944596  33340  15352 S  0.0  1.9   0:05.80 node /opt/s+
56 root      20   0   59812   5244   4512 S  0.0  0.3   0:00.93 sshd
58 root      20   0   20228   3128   2664 S  0.0  0.2   0:00.00 bash
62 root      20   0   21916   2272   1944 S  0.0  0.1   0:03.15 top
63 root      20   0   59812   5344   4612 S  0.0  0.3   0:00.03 sshd
65 root      20   0   20228   3140   2672 S  0.0  0.2   0:00.00 bash
71 root      20   0   59812   5380   4648 S  0.0  0.3   0:00.02 sshd
73 root      20   0   20228   3160   2696 S  0.0  0.2   0:00.00 bash
77 root      20   0   21920   2304   1972 R  0.0  0.1   0:00.00 top
```

Grattis! Du har konfigurerat en anpassad Docker-avbildning för Web App for Containers.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Använda en privat avbildning från Docker Hub (valfritt)

I [Skapa en webbapp](#create-a-web-app) angav du en avbildning på Docker Hub i kommandot `az webapp create`. Det är tillräckligt för en offentlig avbildning. Om du vill använda en privat avbildning behöver du konfigurera ditt Docker-konto-ID och -lösenord i din Azure-webbapp.

I Cloud Shell följer du kommandot `az webapp create` med [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Ersätt *\<app_name>*, samt _\<docker-id>_ och _\<password>_ med ditt ID och lösenord för Docker.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

Kommandot visar utdata som liknar följande JSON-sträng, som visar att konfigurationsändringen har genomförts:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "false"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<docker-id>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "DOCKER|<image-name-and-tag>"
  }
]
```

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Använda en Docker-avbildning från ett valfritt privat register (valfritt)

I det här avsnittet lär du dig att använda en Docker-avbildning från ett privat register i Web App for Containers, och i avsnittet används Azure Container Registry som exempel. Stegen för att använda övriga privata register är liknande. 

Azure Container Registry är en hanterad Docker-tjänst från Azure för att vara värd för privata avbildningar. Distributionerna kan vara av valfri typ, som [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/) och Web App for Containers. 

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I Cloud Shell använder du kommandot [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az_acr_create) för att skapa ett Azure Container Registry. Ange namn, resursgrupp och `Basic` för SKU:n. Tillgängliga SKU:er är `Classic`, `Basic`, `Standard` och `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

När en behållare skapas produceras följande utdata:

```
 - Finished ..
Create a new service principal and assign access:
  az ad sp create-for-rbac --scopes /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --password <password>

Use an existing service principal and assign access:
  az role assignment create --scope /subscriptions/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name> --role Owner --assignee <app-id>
{
  "adminUserEnabled": false,
  "creationDate": "2017-08-09T04:21:09.654153+00:00",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<azure-container-registry-name>",
  "location": "westeurope",
  "loginServer": "<azure-container-registry-name>.azurecr.io",
  "name": "<azure-container-registry-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myazurecontainerre042025"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="log-in-to-azure-container-registry"></a>Logga in på Azure Container Registry

För att push-överföra en avbildning till registret måste du ange autentiseringsuppgifter så att registret accepterar push-överföringen. Du kan hämta autentiseringsuppgifterna med kommandot [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az_acr_show) i Cloud Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Kommandot visar två lösenord som kan användas tillsammans med användarnamnet.

```json
<
  "passwords": [
    {
      "name": "password",
      "value": "{password}"
    },
    {
      "name": "password2",
      "value": "{password}"
    }
  ],
  "username": "<registry-username>"
}
```

Från ditt lokala terminalfönster loggar du in på Azure Container Registry med kommandot `docker login`. Servernamnet krävs för att logga in. Använd formatet `{azure-container-registry-name>.azurecr.io`. Skriv ditt lösenord i konsolen i kommandotolken.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Kontrollera att inloggningen lyckades. 

### <a name="push-an-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

> [!NOTE]
> Om du använder en egen avbildning ska du tagga bilden på följande sätt:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Push-överför avbildningen med kommandot `docker push`. Tagga avbildningen med namnet på registret följt av avbildningens namn och tagg.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Verifiera att push-överföringen har lagt till en behållare till registret genom att ange ACR-lagringsplatserna. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

När avbildningarna i registret listas bekräftas att `mydockerimage` är i registret.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Konfigurera webbappen så att den ska använda avbildningen från Azure Container Registry (eller alla privata register)

Du kan konfigurera Web App for Containers så att det kör en behållare som är lagrad i Azure Container Registry. Du använder Azure Container Registry precis som ett privat register, så om du måste använda ditt eget privata register är stegen för att slutföra en sådan uppgift ungefär desamma.

I Cloud Shell kör du [`az acr credential show`](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show) för att visa användarnamnet och lösenordet för Azure Container Registry. Kopiera användarnamnet och ett av lösenordet så att du kan använda det för att konfigurera webbappen i nästa steg.

```bash
az acr credential show --name <azure-container-registry-name>
```

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "password"
    },
    {
      "name": "password2",
      "value": "password2"
    }
  ],
  "username": "<registry-username>"
}
```

I Cloud Shell kör du kommandot [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) för att tilldela den anpassade Docker-avbildningen till webbappen. Ersätt *\<app_name>*, *\<docker-registry-server-url>*, _\<registry-username>_ och _\<password>_. För Azure Container Registry är *\<docker-registry-server-url>* i formatet `https://<azure-container-registry-name>.azurecr.io`. Om du använder något annat register än Docker Hub måste avbildningens namn börja med det helt kvalificerade domännamnet (FQDN) för registret. För Azure Container Registry ser de ut så här: `<azure-container-registry>.azurecr.io/mydockerimage`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://` krävs i *\<docker-registry-server-url>*.
>

Kommandot visar utdata som liknar följande JSON-sträng, som visar att konfigurationsändringen har genomförts:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "slotSetting": false,
    "value": "mydockerimage"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_URL",
    "slotSetting": false,
    "value": "<azure-container-registry-name>.azurecr.io"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
    "slotSetting": false,
    "value": "<registry-username>"
  },
  {
    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
    "slotSetting": false,
    "value": null
  }
]
```

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en Docker Python- och PostgreSQL-webbapp i Azure](tutorial-docker-python-postgresql-app.md)
