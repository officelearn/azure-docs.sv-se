---
title: 'Självstudie: skapa och köra en anpassad avbildning i Azure App Service'
description: En steg-för-steg-guide om hur du skapar en anpassad Linux-avbildning, push-överför avbildningen till Azure Container Registry och sedan distribuerar avbildningen till Azure App Service.
keywords: azure app service, web app, linux, docker, container
author: msangapu-msft
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
ms.custom: mvc, seodec18, tracking-python
ms.openlocfilehash: bfe1e9fd2532e308c474aee6983615e28a8081f5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506863"
---
# <a name="tutorial-run-a-custom-docker-image-in-app-service"></a>Självstudie: kör en anpassad Docker-avbildning i App Service

Azure App Service använder Docker-behållar tekniken för att vara värd för både inbyggda avbildningar och anpassade avbildningar. Om du vill se en lista över inbyggda avbildningar kör du Azure CLI-kommandot, ["AZ webapp List-Runtimes--Linux"](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes). Om dessa bilder inte uppfyller dina behov kan du bygga och distribuera en anpassad avbildning.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en anpassad avbildning om ingen inbyggd avbildning uppfyller dina behov
> * Skicka den anpassade avbildningen till ett privat behållar register på Azure
> * Kör den anpassade avbildningen i App Service
> * Konfigurera miljövariabler
> * Uppdatera och distribuera om avbildningen
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

Genom att slutföra den här självstudien får du en liten avgift på ditt Azure-konto för behållar registret och kan innebära ytterligare kostnader för att vara värd för behållaren i mer än en månad.

## <a name="set-up-your-initial-environment"></a>Konfigurera din inledande miljö

* Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Installera [Docker](https://docs.docker.com/get-started/#setup), som du använder för att bygga Docker-avbildningar. Om du installerar Docker kan det krävas en omstart av datorn.
* Installera <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 eller högre, med vilken du kör kommandon i alla gränssnitt för att etablera och konfigurera Azure-resurser.

När du har installerat Docker och Azure CLI öppnar du ett terminalfönster och kontrollerar att Docker är installerat:

```bash
docker --version
```

Kontrol lera också att din Azure CLI-version är 2.0.80 eller högre:

```azurecli
az --version
```

Logga sedan in på Azure via CLI:

```azurecli
az login
```

`az login`Kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter. När kommandot har slutförts visas JSON-utdata som innehåller information om dina prenumerationer.

När du har loggat in kan du köra Azure-kommandon med Azure CLI för att arbeta med resurser i din prenumeration.

## <a name="clone-or-download-the-sample-app"></a>Klona eller hämta exempel appen

Du kan hämta exemplet för den här självstudien via git-kloning eller hämta.

### <a name="clone-with-git"></a>Klona med git

Klona exempel lagrings platsen:

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Se till att inkludera `--config core.autocrlf=input` argumentet för att garantera korrekt rad brytningar i filer som används i Linux-behållaren:

Gå sedan till mappen:

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>Ladda ned från GitHub

I stället för att använda Git-kloning kan du besöka [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux) , välja **klona**och sedan välja **Hämta zip**. 

Packa upp ZIP-filen i en mapp med namnet *Docker-django-webapp-Linux*. 

Öppna sedan ett terminalfönster i den *Docker-django-webapp-Linux-* mappen.

## <a name="optional-examine-the-docker-file"></a>Valfritt Granska Docker-filen

Filen i exemplet med namnet _Dockerfile_ som beskriver Docker-avbildningen och innehåller konfigurations anvisningar:

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
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

* Den första gruppen med kommandon installerar appens krav i miljön.
* Den andra gruppen med kommandon skapar en [SSH](https://www.ssh.com/ssh/protocol/) -Server för säker kommunikation mellan behållaren och värden.
* Den sista raden, `ENTRYPOINT ["init.sh"]` , startar `init.sh` för att starta SSH-tjänsten och python-servern.

## <a name="build-and-test-the-image-locally"></a>Bygg och testa avbildningen lokalt

1. Kör följande kommando för att skapa avbildningen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Testa att bygget fungerar genom att köra Docker-behållaren lokalt:

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    Det här [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) kommandot anger porten med `-p` argumentet följt av namnet på avbildningen. 
    
    > [!TIP]
    > Om du kör på Windows och ser felet *standard_init_linux. go: 211: exec-användar processen orsakade "ingen sådan fil eller katalog"*, *init.sh* -filen innehåller CR-LF-raden i stället för förväntad LF-ändelse. Det här felet uppstår om du använde git för att klona exempel lagrings platsen men utelämnade `--config core.autocrlf=input` parametern. I det här fallet klonar du lagrings platsen igen med argumentet--config. Du kan också se felet om du har redigerat *init.sh* och sparat det med CRLF-slut. I det här fallet sparar du filen igen med endast LF-ändelsen.

1. Bläddra till `http://localhost:8000` för att kontrol lera att webbappen och behållaren fungerar som de ska.

    ![Testa webbappen lokalt](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I det här avsnittet och de som följer kan du etablera resurser i Azure som du push-överför avbildningen till och sedan distribuera en behållare till Azure App Service. Du börjar med att skapa en resurs grupp där du kan samla in alla dessa resurser.

Kör kommandot [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) för att skapa en resurs grupp:

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

Du kan ändra `--location` värdet för att ange en region nära dig.

## <a name="push-the-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

I det här avsnittet ska du push-överföra avbildningen till Azure Container Registry från vilken App Service kan distribuera den.

1. Kör [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) kommandot för att skapa en Azure Container Registry:

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    Ersätt `<registry-name>` med ett lämpligt namn för registret. Namnet får bara innehålla bokstäver och siffror och måste vara unikt i hela Azure.

1. Kör [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) kommandot för att hämta autentiseringsuppgifter för registret:

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    JSON-utdata för det här kommandot ger två lösen ord tillsammans med registrets användar namn.
    
1. Använd `docker login` kommandot för att logga in på behållar registret:

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    Ersätt `<registry-name>` och `<registry-username>` med värden från föregående steg. När du uppmanas till det skriver du ett av lösen orden från föregående steg.

    Du använder samma register namn i alla återstående steg i det här avsnittet.

1. När inloggningen lyckas, tagga din lokala Docker-avbildning för registret:

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. Använd `docker push` kommandot för att skicka avbildningen till registret:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Det kan ta några minuter att ladda upp avbildningen första gången eftersom den innehåller bas avbildningen. Efterföljande uppladdningar är vanligt vis snabbare.

    Medan du väntar kan du slutföra stegen i nästa avsnitt för att konfigurera App Service att distribuera från registret.

1. Använd `az acr repository list` kommandot för att kontrol lera att push lyckades:

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    Utdata ska visa namnet på din bild.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>Konfigurera App Service för att distribuera avbildningen från registret

Om du vill distribuera en behållare till Azure App Service skapar du först en webbapp på App Service och ansluter sedan webb programmet till behållar registret. När webbappen startar hämtar App Service automatiskt avbildningen från registret.

1. Skapa en App Service plan med [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) kommandot:

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    En App Service plan motsvarar den virtuella datorn som är värd för webbappen. Som standard använder föregående kommando en [pris nivå](https://azure.microsoft.com/pricing/details/app-service/linux/) som är kostnads fri för den första månaden. Du kan styra nivån med `--sku` parametern.

1. Skapa webbappen med [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) kommandot:

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    Ersätt `<app-name>` med ett namn för webbappen, som måste vara unikt i hela Azure. Ersätt även `<registry-name>` med namnet på ditt register från föregående avsnitt.

1. Använd [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) för att ställa in `WEBSITES_PORT` miljövariabeln som förväntat av appens kod: 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    Ersätt `<app-name>` med det namn som du använde i föregående steg.
    
    Mer information om den här miljövariabeln finns i [readme i exemplets GitHub-lagringsplats](https://github.com/Azure-Samples/docker-django-webapp-linux).

1. Aktivera [hanterad identitet](/azure/app-service/overview-managed-identity) för webbappen med hjälp av- [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) kommandot:

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    Ersätt `<app-name>` med det namn som du använde i föregående steg. Utdata från kommandot (filtreras efter `--query` `--output` argumenten) är tjänstens huvud namn för den tilldelade identiteten, som du använder strax.

    Med hanterad identitet kan du ge webbappen behörighet att få åtkomst till andra Azure-resurser utan att behöva några speciella autentiseringsuppgifter.

1. Hämta ditt prenumerations-ID med [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) kommandot, som du behöver i nästa steg:

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. Ge webb programmet behörighet att få åtkomst till behållar registret:

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    Ersätt följande värden:
    - `<principal-id>`med tjänstens huvud namn-ID från `az webapp identity assign` kommandot
    - `<registry-name>`med namnet på ditt behållar register
    - `<subscription-id>`med det prenumerations-ID som hämtats från `az account show` kommandot

Mer information om dessa behörigheter finns i [Vad är rollbaserad åtkomst kontroll i Azure](/azure/role-based-access-control/overview) och 

## <a name="deploy-the-image-and-test-the-app"></a>Distribuera avbildningen och testa appen

Du kan utföra de här stegen när avbildningen har överförts till behållar registret och App Service är helt etablerad.

1. Använd [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) kommandot för att ange behållar registret och avbildningen som ska distribueras för webb programmet:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    Ersätt `<app_name>` med namnet på din webbapp och Ersätt `<registry-name>` på två platser med namnet på ditt register. 

    - När du använder ett annat register än Docker-hubben (som det här exemplet visar) `--docker-registry-server-url` måste formateras som `https://` följt av registrets fullständigt kvalificerade domän namn.
    - Meddelandet "inga autentiseringsuppgifter angavs för att få åtkomst till Azure Container Registry. Försöker leta upp... " visar att Azure använder appens hanterade identitet för att autentisera med behållar registret i stället för att begära ett användar namn och lösen ord.
    - Om du får felet "AttributeError: NoneType"-objektet har inte attributet reserverat, kontrollerar du att det `<app-name>` är korrekt.

    > [!TIP]
    > Du kan hämta webbappens behållar inställningar när som helst med kommandot `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` . Avbildningen anges i egenskapen `DOCKER_CUSTOM_IMAGE_NAME` . När webbappen distribueras via Azure DevOps eller Azure Resource Manager mallar, kan avbildningen också visas i en egenskap med namnet `LinuxFxVersion` . Båda egenskaperna har samma syfte. Om båda finns i webbappens konfiguration, `LinuxFxVersion` prioriteras.

1. När `az webapp config container set` kommandot har slutförts ska webb programmet köras i behållaren på App Service.

    Testa appen genom att bläddra till `http://<app-name>.azurewebsites.net` och ersätta `<app-name>` med namnet på din webbapp. Vid första åtkomsten kan det ta lite tid innan appen svarar eftersom App Service måste hämta hela avbildningen från registret. Om tids gränsen för webbläsaren uppdateras, behöver du bara uppdatera sidan. När den första avbildningen hämtas körs efterföljande tester mycket snabbare.

    ![Lyckad test av webbappen på Azure](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>Ändra app-koden och distribuera om

I det här avsnittet ska du göra en ändring i webb program koden, återskapa behållaren och sedan skicka behållaren till registret. App Service hämtar sedan den uppdaterade avbildningen automatiskt från registret för att uppdatera den webbapp som körs.

1. I din lokala *Docker-django-webapp-Linux-* mapp öppnar du filen *app/templates/app/index.html*.

1. Ändra det första HTML-elementet så att det matchar följande kod.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. Spara ändringarna.

1. Ändra till mappen *Docker-django-webapp-Linux* och återskapa avbildningen:

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. Uppdatera versions numret i avbildningens tagg till v-1.0.1:

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    Byt ut `<registry-name>` mot namnet på ditt register.

1. Push-överför avbildningen till registret:

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. Starta om webb programmet:

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    Byt ut `<app_name>` mot namnet på din webbapp. Vid omstart hämtar App Service den uppdaterade avbildningen från behållar registret.

1. Kontrol lera att uppdateringen har distribuerats genom att bläddra till `http://<app-name>.azurewebsites.net` .

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

1. Aktivera behållar loggning:

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. Aktivera logg strömmen:

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

    Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

1. Om du vill stoppa logg strömningen när som helst, skriver du **CTRL** + **C**.

## <a name="connect-to-the-container-using-ssh"></a>Anslut till containern med SSH

SSH möjliggör säker kommunikation mellan en container och en klient. Din anpassade avbildning måste vara konfigurerad för att du ska kunna aktivera SSH-anslutningen till din behållare. När behållaren har körts kan du öppna en SSH-anslutning.

### <a name="configure-the-container-for-ssh"></a>Konfigurera behållaren för SSH

Exempel programmet som används i den här självstudien har redan den konfiguration som krävs i *Dockerfile*, som installerar SSH-servern och anger även inloggnings uppgifterna. Det här avsnittet är endast information. Om du vill ansluta till behållaren går du vidare till nästa avsnitt

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> Den här konfigurationen tillåter inte externa anslutningar till behållaren. SSH är endast tillgängligt via webbplatsen för Kudu/SCM. Kudu/SCM-webbplatsen autentiseras med ditt Azure-konto.

*Dockerfile* kopierar också *sshd_config* -filen till mappen */etc/ssh/* och exponerar port 2222 på behållaren:

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

Port 2222 är en intern port som endast är tillgänglig för behållare i ett privat virtuellt nätverks bro nätverk. 

Slutligen börjar Entry-skriptet, *init.sh*, SSH-servern.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>Öppna SSH-anslutning till container

1. Bläddra till `https://<app-name>.scm.azurewebsites.net/webssh/host` och logga in med ditt Azure-konto. Byt ut `<app-name>` mot namnet på din webbapp.

1. När du har loggat in omdirigeras du till en informations sida för webb programmet. Välj **SSH** överst på sidan för att öppna gränssnittet och använda kommandon.

    Du kan till exempel undersöka de processer som körs i den med hjälp av `top` kommandot.
    
## <a name="clean-up-resources"></a>Rensa resurser

De resurser som du skapade i den här artikeln kan medföra kontinuerliga kostnader. Om du vill rensa resurserna behöver du bara ta bort resurs gruppen som innehåller dem:

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Distribuera en anpassad avbildning till ett privat behållar register
> * Distribuera och den anpassade avbildningen i App Service
> * Uppdatera och distribuera om avbildningen
> * Få åtkomst till diagnostikloggar
> * Anslut till containern med SSH

I nästa självstudie får du lära dig hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera en anpassad container](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Självstudie: WordPress-app med flera behållare](tutorial-multi-container-app.md)
