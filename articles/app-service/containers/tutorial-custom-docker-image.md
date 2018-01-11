---
title: "Använda en anpassad Docker-avbildning för webbprogrammet för behållare - Azure | Microsoft Docs"
description: "Hur du använder en anpassad Docker-avbildning för webbprogrammet för behållare."
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: 
author: SyntaxC4
manager: SyntaxC4
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 2580c2109ce33b1ce99aa491f7d0002edf060693
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="use-a-custom-docker-image-for-web-app-for-containers"></a>Använda en anpassad Docker-avbildning för webbprogrammet för behållare

[Web App för behållare](app-service-linux-intro.md) innehåller inbyggda Docker-avbildningar på Linux med stöd för specifika versioner, till exempel PHP 7.0 och Node.js 4.5. Web App för behållare använder Docker-behållare som värd för både inbyggda avbildningar och anpassade avbildningar som en plattform som en tjänst. I kursen får du lära dig hur du skapar en anpassad Docker-avbildning och distribuera den till webbprogrammet för behållare. Det här mönstret är användbar när inbyggda bilder inte med din språk eller när programmet kräver en viss konfiguration som inte tillhandahålls inom de inbyggda bilderna.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Git](https://git-scm.com/downloads)
* En aktiv [Azure-prenumeration](https://azure.microsoft.com/pricing/free-trial/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Docker](https://docs.docker.com/get-started/#setup)
* En [Docker-hubb-konto](https://docs.docker.com/docker-id/)

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando för att klona exempel app lagringsplatsen till den lokala datorn och sedan ändra till katalogen som innehåller exempelkoden i ett terminalfönster.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Skapa bilden från filen Docker

Ta en titt på Git-lagringsplatsen, _Dockerfile_. Den här filen beskriver Python-miljö som krävs för att köra programmet. Dessutom avbildningen ställer in en [SSH](https://www.ssh.com/ssh/protocol/) server för säker kommunikation mellan behållaren och värden.

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

För att skapa filen Docker kör den `docker build` kommando och ange ett namn, `mydockerimage`, och tagg, `v1.0.0`. Ersätt `<docker-id>` konto med hubben Docker ID.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Kommandot resulterar utdata som liknar följande:

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

Kontrollera att bygga fungerar genom att köra Docker-behållaren. Problem i [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando och ange namn och taggen för avbildningen till den. Se till att ange den port som använder den `-p` argumentet.

```bash
docker run -p 2222:8000 <docker-ID>/mydockerimage:v1.0.0
```

Kontrollera webbapp och behållare fungerar som de ska genom att bläddra till `http://localhost:2222`.

![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

## <a name="push-the-docker-image-to-docker-hub"></a>Push-Docker-avbildningen till Docker-hubb

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster avbildningen och behållare. För att dela din bild skicka du den till ett register. 

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry such as Azure Container Registry. Select the appropriate tab for your scenario below (your selection will switch multiple tabs on this page). -->

> [!NOTE]
> Push-installation till ett privat Docker-register? Valfria instruktionerna för att se [Push en Docker-avbildning till privata registret](#push-a-docker-image-to-private-registry-optional).

<!--## [Docker Hub](#tab/docker-hub)-->

Docker-hubben är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna databaser, offentlig eller privat. För att vidarebefordra en anpassad Docker-avbildning till offentliga Docker-hubben, Använd den [docker push](https://docs.docker.com/engine/reference/commandline/push/) kommando och ange ett fullständigt namn och en tagg. En fullständig avbildningsnamn och tagg ser ut som i följande exempel:

```
<docker-id>/image-name:tag
```

Om du inte har loggat in till Docker-hubben kan göra det med den [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot innan du försöker att skicka en bild.

```bash
docker login --username <docker-id> --password <docker-hub-password>
```

Meddelandet ”inloggningen har slutförts” bekräftar att du är inloggad. Efter loggat in kan du skicka bilden till Docker-hubb med hjälp av den [docker push](https://docs.docker.com/engine/reference/commandline/push/) kommando.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Kontrollera att push lyckades genom att undersöka kommandot utdata.

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

Du kan vara värd för interna Linux-program i molnet med hjälp av Azure Web Apps. Om du vill skapa en Webbapp för behållare, måste du köra Azure CLI-kommandona som skapar en grupp och sedan en serviceplan och slutligen webbprogrammet sig själv. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)] 

### <a name="create-a-web-app"></a>Skapa en webbapp

I Cloud Shell skapar du en [webbapp](app-service-linux-intro.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Glöm inte att ersätta `<app_name>` med ett unikt appnamn och < docker-ID > med Docker-ID.

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

De flesta Docker-bilder har miljövariabler som måste konfigureras. Om du använder en befintlig Docker-avbildning som skapats av någon annan kan Avbildningen använda en annan port än 80. Azure berätta den port som används av avbildningen med hjälp av den `WEBSITES_PORT` appinställningen. GitHub-sidan för den [Python-exempel i den här självstudiekursen](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du måste ange `WEBSITES_PORT` till _8000_.

Ange app-inställningar i [az webapp konfigurationsuppsättning appsettings](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) i molnet Shell. Appinställningar är skiftlägeskänsliga och avgränsade med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_PORT=8000
```

<!-- Depending on your requirements, you may have your docker images in a Public Docker Registry, such as Docker Hub, or a Private Docker Registry, such as Azure Container Registry. Select the appropriate tab for your scenario below: -->

> [!NOTE]
> Distribuera från ett privat Docker-register? Valfria instruktionerna för att se [konfigurerar webbprogram att använda Docker-behållare från ett privat register](#configure-web-app-to-use-docker-container-from-a-private-registry-optional).

<!-- # [Docker Hub](#tab/docker-hub)-->

<!-- # [Private Registry](#tab/private-registry)

// Place Private Registry text back here once Tabbed Conceptual bug is fixed

---
-->

### <a name="test-the-web-app"></a>Testa webbappen

Kontrollera att webbprogrammet fungerar genom att bläddra till den (`http://<app_name>azurewebsites.net`). 

![Port testkonfiguration web app](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="change-web-app-and-redeploy"></a>Ändra webbapp och distribuera om

Öppna app/templates/app/index.html i din lokala Git-lagringsplats. Leta upp det första elementet i HTML- och ändra den till.

```python
<nav class="navbar navbar-inverse navbar-fixed-top">
    <div class="container">
      <div class="navbar-header">         
        <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>       
      </div>            
    </div>
  </nav> 
```

När du har ändrat Python-fil och sparat den, måste du återskapa och push den nya Docker-avbildningen. Starta sedan om webbappen för att ändringarna ska börja gälla. Använd samma kommandon som du tidigare har använt i den här självstudiekursen. Du kan referera till [skapa bilden från filen Docker](#build-the-image-from-the-docker-file) och [Push Docker-avbildningen till Docker-hubben](#push-the-docker-image-to-docker-hub). Testa webbappen genom att följa anvisningarna i [testa webbprogrammet](#test-the-web-app).

## <a name="connect-to-web-app-for-containers-using-ssh"></a>Ansluta till webbprogrammet för behållare via SSH

SSH kan säker kommunikation mellan en behållare och en klient. För en anpassad Docker-avbildning som stöd för SSH måste bygga i en Dockerfile. Du kan aktivera SSH i filen Docker. SSH-instruktioner har redan lagts till exempel-dockerfile så att du kan följa dessa anvisningar med en egen anpassad avbildning:

* En [kör](https://docs.docker.com/engine/reference/builder/#run) instruktion som anropar `apt-get`, anger lösenordet för rotkontot till `"Docker!"`.

    ```docker
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Den här konfigurationen tillåter inte externa anslutningar till behållaren. SSH är endast tillgängliga från Kudu/SCM-platsen. Kudu/SCM platsen autentiseras med publishing autentiseringsuppgifter.

* En [kopiera](https://docs.docker.com/engine/reference/builder/#copy) instruktion som instruerar Docker-motorn att kopiera den [sshd_config](http://man.openbsd.org/sshd_config) filen till den */etc/ssh/* directory. Konfigurationsfilen ska baseras på [sshd_config filen](https://github.com/Azure-App-Service/node/blob/master/6.11.1/sshd_config).

    ```docker
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Den *sshd_config* filen måste innehålla följande: 
    > * `Ciphers`måste innehålla minst ett objekt i listan: `aes128-cbc,3des-cbc,aes256-cbc`.
    > * `MACs`måste innehålla minst ett objekt i listan: `hmac-sha1,hmac-sha1-96`.

* En [EXPONERA](https://docs.docker.com/engine/reference/builder/#expose) instruktion att visar port 2222 i behållaren. Även om rotlösenordet är känd kan port 2222 inte nås från internet. Det är en intern port tillgänglig endast av behållare i bridge nätverk i ett privat virtuellt nätverk. Sedan kan kommandon kopiera SSH konfigurationsinformation och starta den `ssh` service.

    ```docker
    EXPOSE 8000 2222
    ```

* Se till att [starta den ssh-tjänsten](https://github.com/Azure-App-Service/node/blob/master/6.9.3/startup/init_container.sh) genom att använda ett kommandoskript i katalogen/BIN.
 
    ```bash
    #!/bin/bash
    service ssh start
    ```
     
### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till behållaren

Web App för behållare tillåter inte externa anslutningar till behållaren. SSH är endast tillgängliga från Kudu-plats som är tillgängliga i `https://<app_name>.scm.azurewebsites.net`.

För att ansluta, bläddra till `https://<app_name>.scm.azurewebsites.net/webssh/host` och logga in med ditt Azure-konto.

Du omdirigeras sedan till en sida där en interaktiv konsol. 

Du kanske vill kontrollera att vissa program körs i behållaren. Om du vill inspektera behållaren och verifiera processer som körs, utfärda den `top` kommando i Kommandotolken.

```bash
top
```

Den `top` kommando visar alla processer som körs i en behållare.

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

Grattis! Du har konfigurerat en anpassad Docker-avbildning för en Webbapp för behållare.

## <a name="use-a-private-image-from-docker-hub-optional"></a>Använd en privat avbildning från Docker-hubben (valfritt)

I [skapa en webbapp](#create-a-web-app), du har angett en bild på Docker-hubben i den `az webapp create` kommando. Det här är bra för en offentlig avbildning. Om du vill använda en privat avbildning måste du konfigurera ditt Docker konto-ID och lösenord i Azure-webbapp.

Molnet Shell, så den `az webapp create` med [az webapp konfigurationsuppsättning behållaren](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). Ersätt  *\<appnamn >*, och även _< docker-id >_ och  _<password>_  med Docker-ID och lösenord.

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-registry-server-user <docker-id> --docker-registry-server-password <password>
```

Kommandot visar utdata som liknar den följande JSON-strängen, som visar att konfigurationsändringen har slutförts:

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

## <a name="use-a-docker-image-from-any-private-registry-optional"></a>Använd en Docker-avbildning från alla privata registret (valfritt)

Du lär dig hur du använder en Docker-avbildning från en privat registret i webbprogram för behållare i det här avsnittet och Azure Container registret används som ett exempel. Steg för att använda andra privata register är liknande. 

Azure-behållaren registret är en hanterad Docker-tjänst från Azure som värd för privata bilder. Distributioner kan ha olika typer, inklusive [Docker Swarm](https://docs.docker.com/engine/swarm/), [Kubernetes](https://kubernetes.io/), och Web App för behållare. 

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I gränssnittet molnet använder den [az acr skapa](/cli/azure/acr?view=azure-cli-latest#az_acr_create) kommando för att skapa ett Azure Container registret. Ange namn, resursgruppens namn och `Basic` för SKU: N. Tillgängliga SKU: er är `Classic`, `Basic`, `Standard`, och `Premium`.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

Skapa en behållare resulterar i följande utdata:

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

### <a name="log-in-to-azure-container-registry"></a>Logga in på Azure-behållaren registret

För att kunna trycka på en bild i registret, måste du ange autentiseringsuppgifter så registret godkänner push-meddelandet. Du kan hämta dessa autentiseringsuppgifter genom att använda den [az acr visa](/cli/azure/acr?view=azure-cli-latest#az_acr_show) i molnet Shell. 

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Kommandot visar två lösenord som kan användas med användarnamnet.

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

Logga in från din lokala terminalfönster till registret för Azure-behållaren med den `docker login` kommando. Servernamnet krävs för att logga in. Använd formatet `{azure-container-registry-name>.azurecr.io`.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username> --password <password> 
```

Kontrollera att inloggningen lyckades. 

### <a name="push-an-image-to-azure-container-registry"></a>Push-en bild till registret för Azure-behållare

> [!NOTE]
> Om du använder en egen avbildning tagga bilden på följande sätt:
> ```bash
> docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
> ```

Push-avbildningen med hjälp av den `docker push` kommando. Taggen bild med namnet på registret, följt av ditt namn och en tagg.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Kontrollera att push-meddelandet har lagts till en behållare i registret genom att ange ACR-databaser. 

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Visar en lista över bilder i registret bekräftar att `mydockerimage` i registret.

```json
[
  "mydockerimage"
]
```

### <a name="configure-web-app-to-use-the-image-from-azure-container-registry-or-any-private-registry"></a>Konfigurera Webbappen att använda bilden från registret för Azure-behållaren (eller alla privata registret)

Du kan konfigurera webbprogrammet för behållare så att det körs en behållare som lagras i registret för Azure-behållare. Använda registret för Azure-behållare är precis som med alla privata registret, så om du behöver använda egna privata registret, steg för att slutföra den här uppgiften är liknande.

Molnet Shell, kör [az acr autentiseringsuppgifter visa](/cli/azure/acr/credential?view=azure-cli-latest#az_acr_credential_show) att visa användarnamn och lösenord för Azure-behållare registret. Kopiera användarnamn och ett lösenord så att du kan använda för att konfigurera webbprogrammet i nästa steg.

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

Molnet Shell och kör den [az webapp konfigurationsuppsättning behållaren](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) kommando för att tilldela den anpassade Docker-avbildningen till webbappen. Ersätt  *\<appnamn >*,  *\<docker-registret-server-url >*,  _\<register-username >_, och  _\<lösenord >_. För Azure-behållare registernyckeln  *\<docker-registret-server-url >* är i formatet `https://<azure-container-registry-name>.azurecr.io`. 

```azurecli-interactive
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> `https://`krävs i  *\<docker-registret-server-url >*.
>

Kommandot visar utdata som liknar den följande JSON-strängen, som visar att konfigurationsändringen har slutförts:

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
> [Skapa en Docker Python och PostgreSQL-webbapp i Azure](tutorial-docker-python-postgresql-app.md)
