---
title: Skapa en anpassad avbildning och köra i App Service från ett privat register
description: Så här använder du en anpassad Docker-avbildning för Web App for Containers.
keywords: azure app service, web app, linux, docker, container
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 07d5b718cb96a938cb6e796e1cf4864851433516
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070939"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Självstudier: Skapa en anpassad avbildning och köra i App Service från ett privat register

[App Service](app-service-linux-intro.md) tillhandahåller inbyggda Docker-avbildningar i Linux med stöd för vissa versioner, till exempel php 7,3 och Node. js 10,14. App Service använder Docker-behållar tekniken för att vara värd för både inbyggda avbildningar och anpassade avbildningar som en plattform som en tjänst. I den här självstudien får du lära dig hur du skapar en anpassad avbildning och kör den i App Service. Det här mönstret är användbart när de inbyggda avbildningarna inte inkluderar ditt språkval eller när ditt program kräver en specifik konfiguration som inte ingår i de inbyggda avbildningarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Distribuera en anpassad avbildning till ett privat behållar register
> * Kör den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om avbildningen
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Git](https://git-scm.com/downloads)
* [Docker](https://docs.docker.com/get-started/#setup)

## <a name="download-the-sample"></a>Hämta exemplet

Öppna terminalfönstret och kör följande kommando för att klona exempelappens lagringsplats till din lokala dator. Ändra sedan till katalogen som innehåller exempelkoden.

```bash
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## <a name="build-the-image-from-the-docker-file"></a>Skapa avbildningen från Docker-filen

På Git-lagringsplatsen tar du en titt på _Dockerfile_. Den här filen beskriver Python-miljön som krävs för att köra programmet. Dessutom konfigurerar avbildningen en [SSH](https://www.ssh.com/ssh/protocol/)-server för säker kommunikation mellan containern och värden.

```Dockerfile
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

Bygg Docker-avbildningen med `docker build` kommandot.

```bash
docker build --tag mydockerimage .
```

Testa att versionen fungerar genom att köra Docker-containern. Utfärda kommandot [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) och skicka avbildningens namn och tagg. Se till att ange porten som använder argumentet `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Verifiera att webbappen och containern fungerar som de ska genom att bläddra till `http://localhost:8000`.

![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

Om du vill skapa en app som använder avbildningen som du nyss har skapat kör du Azure CLI-kommandon som skapar en resurs grupp, push-överför avbildningen och skapar sedan App Service plan-webbappen för att köra den.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I Cloud Shell använder du kommandot [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) för att skapa ett Azure Container Registry.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Logga in på Azure Container Registry

Om du vill skicka en avbildning till registret måste du autentisera med det privata registret. I Cloud Shell använder [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) du kommandot för att hämta autentiseringsuppgifterna från registret som du skapade.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Utdata visar två lösen ord tillsammans med användar namnet.

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

Från det lokala terminalfönstret loggar du in på Azure Container Registry med hjälp `docker login` av kommandot, som du ser i följande exempel. *Ersätt\<Azure-Container-Registry-Name >* och  *\<register-username >* med värden för registret. När du uppmanas till det skriver du ett av lösen orden från föregående steg.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Bekräfta att inloggningen lyckades.

### <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Tagga den lokala avbildningen för Azure Container Registry. Exempel:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Push-överför avbildningen med kommandot `docker push`. Tagga avbildningen med namnet på registret följt av avbildningens namn och tagg.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Kontrol lera att push-åtgärden har slutförts i Cloud Shell.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Du bör få följande utdata.

```json
[
  "mydockerimage"
]
```

### <a name="create-app-service-plan"></a>Skapa apptjänstplan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Skapa webbapp

Skapa i Cloud Shell en [webbapp](app-service-linux-intro.md) i `myAppServicePlan` App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). _Ersätt\<App-Name->_ med ett unikt app-namn och  _\<Azure-Container – register namn >_ med ditt register namn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
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
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="configure-registry-credentials-in-web-app"></a>Konfigurera autentiseringsuppgifter för registret i en webbapp

För att App Service ska kunna hämta den privata avbildningen behöver den information om registret och avbildningen. I Cloud Shell anger du [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) dem med kommandot. *Ersätt\<App-Name >* ,  *\<Azure-Container-register-Name*  _\<_ >,  _\<registret-username >_ och Password >.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> När du använder ett annat register än Docker Hub `--docker-registry-server-url` måste det formateras `https://` som följt av registrets fullständigt kvalificerade domän namn.
>

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

De flesta Docker-avbildningar använder anpassade miljövariabler, till exempel en annan port än 80. Du anger App Service om porten som din avbildning använder med hjälp `WEBSITES_PORT` av appens inställningar. GitHub-sidan för [Python-exemplet i den här självstudien](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du behöver ställa in `WEBSITES_PORT` på _8000_.

Om du vill konfigurera appinställningar använder du kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Appinställningar är skifteslägeskänsliga och avgränsas med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testa webbappen

Verifiera att webbappen fungerar genom att bläddra till den (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Första gången du öppnar appen kan det ta lite tid eftersom App Service måste hämta hela avbildningen. Om tids gränsen för webbläsaren uppdateras, behöver du bara uppdatera sidan.

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

När du har ändrat Python-filen och sparat den måste du återskapa och push-överföra den nya Docker-avbildningen. Starta sedan om webbappen för att ändringarna ska börja gälla. Använd samma kommandon som du tidigare har använt i den här självstudien. Du kan referera till att [bygga avbildningen från Docker-filen](#build-the-image-from-the-docker-file) och push-avbildningen [till Azure Container Registry](#push-image-to-azure-container-registry). Testa webbappen genom att följa anvisningarna i [Testa webbappen](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Din anpassade avbildning måste vara konfigurerad för att du ska kunna aktivera SSH-anslutningen till din behållare. Låt oss ta en titt på den exempel lagrings plats som redan har den konfiguration som krävs.

* I [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)installerar följande kod SSH-servern och anger också inloggnings uppgifterna.

    ```Dockerfile
    ENV SSH_PASSWD "root:Docker!"
    RUN apt-get update \
            && apt-get install -y --no-install-recommends dialog \
            && apt-get update \
      && apt-get install -y --no-install-recommends openssh-server \
      && echo "$SSH_PASSWD" | chpasswd 
    ```

    > [!NOTE]
    > Den här konfigurationen tillåter inga externa anslutningar till containern. SSH är endast tillgängligt via webbplatsen för Kudu/SCM. Kudu/SCM-webbplatsen autentiseras med ditt Azure-konto.

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) kopierar [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) -filen i lagrings platsen till */etc/ssh/* -katalogen.

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) exponerar port 2222 i behållaren. Det är en intern port som endast är åtkomlig via containrar inom ett privat virtuellt nätverks nätverksbrygga. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* Inmatnings [skriptet](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) startar SSH-servern.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till container

SSH-anslutningen är bara tillgänglig via kudu-webbplatsen, som är tillgänglig `https://<app-name>.scm.azurewebsites.net`på.

Anslut genom att gå till `https://<app-name>.scm.azurewebsites.net/webssh/host` och logga in med ditt Azure-konto.

Du omdirigeras därefter till en sida som visar en interaktiv konsol.

Du kanske vill verifiera att vissa program körs i containern. Om du vill inspektera containern och verifiera körningsprocesserna utfärdar du kommandot `top` i kommandotolken.

```bash
top
```

Kommandot `top` tillgängliggör alla körningsprocesser i en container.

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

Grattis! Du har konfigurerat en anpassad Linux-behållare i App Service.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Distribuera en anpassad avbildning till ett privat behållar register
> * Kör den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om avbildningen
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

Gå vidare till nästa självstudie för att läsa hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Självstudier: Mappa ett anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera anpassad behållare](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Självstudier: WordPress-app med flera behållare](tutorial-multi-container-app.md)
