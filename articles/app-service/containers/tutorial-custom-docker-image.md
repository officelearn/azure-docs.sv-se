---
title: 'Självstudiekurs: Skapa och kör en anpassad avbildning'
description: Lär dig hur du skapar en anpassad Linux-avbildning som kan köras på Azure App Service, distribuera den till Azure Container Register och köra den i App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 2609ff908b3c2f872cb63d3dcd7dcd481d316484
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085868"
---
# <a name="tutorial-build-a-custom-image-and-run-in-app-service-from-a-private-registry"></a>Självstudiekurs: Skapa en anpassad avbildning och kör i App Service från ett privat register

[App Service](app-service-linux-intro.md) ger inbyggda Docker-avbildningar på Linux med stöd för specifika versioner, till exempel PHP 7.3 och Node.js 10.14. App Service använder Docker-behållartekniken för att vara värd för både inbyggda avbildningar och anpassade avbildningar som en plattform som en tjänst. I den här självstudien får du lära dig hur du skapar en anpassad avbildning och kör den i App Service. Det här mönstret är användbart när de inbyggda avbildningarna inte inkluderar ditt språkval eller när ditt program kräver en specifik konfiguration som inte ingår i de inbyggda avbildningarna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Distribuera en anpassad avbildning till ett privat behållarregister
> * Köra den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om bilden
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

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

På Git-lagringsplatsen tar du en titt på _Dockerfile_. Den här filen beskriver Python-miljön som krävs för att köra programmet. Dessutom konfigurerar avbildningen en [SSH](https://www.ssh.com/ssh/protocol/)-server för säker kommunikation mellan containern och värden. Den sista raden i _Dockerfile_ `ENTRYPOINT ["init.sh"]` `init.sh` anropar , för att starta SSH-tjänsten och Python-servern.

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

#service SSH start
#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

Skapa Docker-avbildningen `docker build` med kommandot.

```bash
docker build --tag mydockerimage .
```

Testa att versionen fungerar genom att köra Docker-containern. Utfärda [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) kommandot och skicka namnet och taggen för bilden till det. Se till att ange porten som använder argumentet `-p`.

```bash
docker run -p 8000:8000 mydockerimage
```

Verifiera att webbappen och containern fungerar som de ska genom att bläddra till `http://localhost:8000`.

![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Distribuera app till Azure

Om du vill skapa en app som använder den avbildning som du just skapade kör du Azure CLI-kommandon som skapar en resursgrupp, skickar avbildningen och skapar sedan apptjänstens planwebbapp för att köra den.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I Cloud Shell använder [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) du kommandot för att skapa ett Azure-behållarregister.

```azurecli-interactive
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
```

### <a name="sign-in-to-azure-container-registry"></a>Logga in i Azure Container Registry

Om du vill skicka en avbildning till registret måste du autentisera med det privata registret. I Cloud Shell använder [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) du kommandot för att hämta autentiseringsuppgifterna från registret som du skapade.

```azurecli-interactive
az acr credential show --name <azure-container-registry-name>
```

Utdata visar två lösenord tillsammans med användarnamnet.

<pre>
{
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
  "username": "&lt;registry-username&gt;"
}
</pre>

Logga in i Azure Container-registret från det `docker login` lokala terminalfönstret med kommandot, som visas i följande exempel. Ersätt * \<azure-container-register-name>* och * \<register-användarnamn>* med värden för registret. Skriv in ett av lösenorden från föregående steg när du uppmanas att göra det.

```bash
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
```

Bekräfta att inloggningen lyckas.

### <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Tagga din lokala avbildning för Azure Container Registry. Ett exempel:
```bash
docker tag mydockerimage <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Push-överför avbildningen med kommandot `docker push`. Tagga avbildningen med namnet på registret följt av avbildningens namn och tagg.

```bash
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

Kontrollera att push-pushen lyckas i Cloud Shell.

```azurecli-interactive
az acr repository list -n <azure-container-registry-name>
```

Du bör få följande utgång.

<pre>
[
  "mydockerimage"
]
</pre>

### <a name="create-app-service-plan"></a>Skapa apptjänstplan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Skapa webbapp

Skapa i Cloud Shell en [webbapp](app-service-linux-intro.md) i `myAppServicePlan` App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Ersätt _ \<appnamn>_ med ett unikt appnamn och _ \<azure-container-registry-name>_ med ditt registernamn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-container-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

<pre>
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="configure-registry-credentials-in-web-app"></a>Konfigurera registerautentiseringsuppgifter i webbapp

För att App Service ska kunna hämta den privata avbildningen behöver den information om registret och avbildningen. I Cloud Shell, förse [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) dem med kommandot. Ersätt * \<>för>*, * \<azure-container-register-name>*, _ \<register-användarnamn>_ och _ \<lösenord>_.

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0 --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
```

> [!NOTE]
> När du använder ett annat register `--docker-registry-server-url` än Docker `https://` Hub måste det formateras enligt registrets fullständigt kvalificerade domännamn.
>

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

De flesta Docker-avbildningar använder anpassade miljövariabler, till exempel en annan port än 80. Du berättar för App Service om den `WEBSITES_PORT` port som bilden använder med hjälp av appinställningen. GitHub-sidan för [Python-exemplet i den här självstudien](https://github.com/Azure-Samples/docker-django-webapp-linux) visar att du behöver ställa in `WEBSITES_PORT` på _8000_.

Om du vill ange [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) appinställningar använder du kommandot i Cloud Shell. Appinställningar är skifteslägeskänsliga och avgränsas med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_PORT=8000
```

### <a name="test-the-web-app"></a>Testa webbappen

Verifiera att webbappen fungerar genom att bläddra till den (`http://<app-name>.azurewebsites.net`).

> [!NOTE]
> Första gången du öppnar appen kan det ta lite tid eftersom App Service måste hämta hela bilden. Om webbläsaren time out, bara uppdatera sidan.

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

När du har ändrat Python-filen och sparat den måste du återskapa och push-överföra den nya Docker-avbildningen. Starta sedan om webbappen för att ändringarna ska börja gälla. Använd samma kommandon som du tidigare har använt i den här självstudien. Du kan referera till [Skapa avbildningen från Docker-filen](#build-the-image-from-the-docker-file) och [Push-avbildningen till Azure Container Registry](#push-image-to-azure-container-registry). Testa webbappen genom att följa anvisningarna i [Testa webbappen](#test-the-web-app).

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="enable-ssh-connections"></a>Aktivera SSH-anslutningar

SSH möjliggör säker kommunikation mellan en container och en klient. Om du vill aktivera SSH-anslutning till behållaren måste den anpassade avbildningen konfigureras för den. Låt oss ta en titt på exempeldatabasen som redan har den nödvändiga konfigurationen.

* I [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile)installeras SSH-servern med följande kod och även inloggningsuppgifterna.

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

* [Dockerfile kopierar](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L18) [sshd_config](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/sshd_config) filen i databasen till katalogen */etc/ssh/.*

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

* [Dockerfile](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/Dockerfile#L22) exponerar port 2222 i behållaren. Det är en intern port som endast är åtkomlig via containrar inom ett privat virtuellt nätverks nätverksbrygga. 

    ```Dockerfile
    EXPOSE 8000 2222
    ```

* [Inmatningsskriptet](https://github.com/Azure-Samples/docker-django-webapp-linux/blob/master/init.sh#L5) startar SSH-servern.

    ```bash
    #!/bin/bash
    service ssh start
    ```

### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till container

SSH-anslutning är endast tillgänglig via Kudu-webbplatsen, som är tillgänglig på `https://<app-name>.scm.azurewebsites.net`.

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
> * Distribuera en anpassad avbildning till ett privat behållarregister
> * Köra den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om bilden
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"]
> [Självstudiekurs: Mappa anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla in andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera en anpassad container](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Självstudiekurs: WordPress-appen med flera behållare](tutorial-multi-container-app.md)
