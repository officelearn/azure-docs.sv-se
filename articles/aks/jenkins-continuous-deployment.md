---
title: Jenkins kontinuerlig distribution med Kubernetes i Azure Container Service
description: "Automatisera en process för kontinuerlig distribution med Jenkins att distribuera och uppgradera en av app på Kubernetes i Azure Container Service"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1293fda45602203570a0f7f75481f67bdcb6edf3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-container-service"></a>Kontinuerlig distribution med Jenkins och Azure Container Service

Det här dokumentet visar hur du ställer in ett grundläggande kontinuerlig distribution arbetsflöde mellan Jenkins och ett Azure Container Service (AKS)-kluster. 

Arbetsflödesexemplet omfattar följande steg:

> [!div class="checklist"]
> * Distribuera programmet Azure rösta i Kubernetes-klustret.
> * Uppdatera Azure rösten programkod och push-installera en GitHub-lagringsplatsen, som startar processen kontinuerlig distribution.
> * Jenkins klonar databasen och skapar en ny avbildning för behållaren med den uppdaterade koden.
> * Den här avbildningen är pushas till Azure Container registret (ACR).
> * Det program som körs i klustret AKS uppdateras med den nya behållare avbildningen.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna slutföra stegen i den här artikeln.

- Grundläggande förståelse för Kubernetes, Git, CI/CD- och Azure Container registret (ACR).
- En [Azure Container Service (AKS) kluster] [ aks-quickstart] och [AKS autentiseringsuppgifter konfigurerat] [ aks-credentials] i utvecklingssystemet.
- En [Azure Container registret (ACR) registret][acr-quickstart], inloggningsnamnet server ACR och [ACR autentiseringsuppgifter] [ acr-authentication] push och pull-åtkomst.
- Azure CLI är installerad i utvecklingssystemet.
- Docker installerad i utvecklingssystemet.
- GitHub-konto [GitHub personliga åtkomsttoken][git-access-token], och Git-klienten installerad i utvecklingssystemet.

## <a name="prepare-application"></a>Förbereda program

Programmet Azure rösta i hela dokumentet innehåller ett webbgränssnitt som finns i en eller flera skida och en andra baljor värd Redis för lagring av tillfälliga data. 

Innan du skapar Jenkins / AKS integration, förbereda och distribuera programmet Azure rösta i AKS-klustret. Du kan se detta som versionen av programmet.

Duplicera följande GitHub-lagringsplats.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

När förgrening har skapats kan du klona den utvecklingssystemet. Se till att du använder URL-Adressen till din förgrening vid kloning av den här lagringsplatsen.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Ändra kataloger så att du arbetar från den klonade katalogen.

```bash
cd azure-voting-app-redis
```

Kör den `docker-compose.yaml` fil för att skapa den `azure-vote-front` behållare avbildningen och starta programmet.

```bash
docker-compose up -d
```

När du är klar, kan du använda den [docker bilder] [ docker-images] kommandot för att se skapade avbildningen.

Observera att tre bilder har hämtat eller skapat. Den `azure-vote-front` avbildningen som innehåller programmet och använder den `nginx-flask` avbildningen som bas. Den `redis` bilden används för att starta en Redis-instans.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Hämta ACR inloggningsserver med den [az acr lista] [ az-acr-list] kommando. Se till att uppdatera resursgruppens namn med resursgruppen som värd för din ACR-registret.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd den [docker-taggen] [ docker-tag] kommando för att tagga avbildningen med inloggningsnamnet för server och versionsnumret `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Uppdatera ACR inloggningen servervärdet med ACR inloggningsnamn för server och push i `azure-vote-front` avbildningen till registret. 

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Distribuera programmet till Kubernetes

En Kubernetes manifestfilen kan vara hittades i roten på Azure röst-databasen och kan användas för att distribuera programmet till Kubernetes klustret.

Uppdatera först den **azure-vote-all-in-one-redis.yaml** manifestfilen med platsen för din ACR-registret. Öppna filen med en textredigerare och Ersätt `microsoft` med ACR server inloggningsnamnet. Du hittar värdet på rad **47** i manifestfilen.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Använd sedan den [kubectl skapa] [ kubectl-create] kommando för att köra programmet. Det här kommandot parsar manifestfilen och skapar de definierade Kubernetes-objekten.

```bash
kubectl create -f azure-vote-all-in-one-redis.yaml
```

En [Kubernetes service] [ kubernetes-service] skapas för att exponera programmet till internet. Den här processen kan ta ett par minuter. 

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

Till en början visas *EXTERNAL-IP* för *azure-vote-front*-tjänsten som *pending* (väntande).
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

När *EXTERNAL-IP*-adressen har ändrats från *pending* till en *IP-adress* använder du `control+c` för att stoppa kubectl-övervakningsprocessen. 

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Bläddra till den externa IP-adressen för att visa programmet.

![Bild av Kubernetes-kluster i Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Distribuera Jenkins för VM

Ett skript har skapats i förväg att distribuera en virtuell dator, konfigurera åtkomst till nätverket och slutföra en grundläggande installation av Jenkins. Dessutom kopieras skriptet Kubernetes konfigurationsfilen från utvecklingssystemet till systemets Jenkins. Den här filen används för autentisering mellan Jenkins och AKS-klustret.

Kör följande kommandon för att hämta och köra skriptet. Den under den URL som kan också användas för att granska innehållet i skriptet.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

När skriptet har slutförts matar ut en adress för Jenkins-server som också en nyckel för att låsa upp Jenkins. Gå till URL, ange nyckeln och följa den anvisningarna för att slutföra konfigurationen Jenkins.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Jenkins miljövariabler

En Jenkins miljövariabel är används för att lagra servernamnet i registret för Azure-behållaren (ACR) inloggning. Den här variabeln refereras till under Jenkins kontinuerlig distributionsjobbet.

När du är på administrationsportalen Jenkins klickar du på **hantera Jenkins** > **konfigurera systemet**.

Under **globala egenskaper**väljer **miljövariabler**, och Lägg till en variabel med namnet `ACR_LOGINSERVER` och ett värde för din ACR inloggnings-server.

![Jenkins miljövariabler](media/aks-jenkins/env-variables.png)

När du är klar klickar du på **spara** på konfigurationssidan Jenkins.

## <a name="jenkins-credentials"></a>Jenkins autentiseringsuppgifter

Nu lagra autentiseringsuppgifterna ACR i ett autentiseringsuppgiftobjekt Jenkins. Dessa autentiseringsuppgifter refereras under Jenkins build-jobbet.

Klicka på tillbaka på administrationsportalen Jenkins **autentiseringsuppgifter** > **Jenkins** > **globala autentiseringsuppgifter (obegränsad)**  >   **Lägga till autentiseringsuppgifter**.

Se till att typ av autentiseringsuppgifter är **användarnamn med lösenord** och ange följande:

- **Användarnamnet** -ID för service principal användning av autentisering med ACR-registret.
- **Lösenord** -klienthemlighet för service principal användning av autentisering med ACR-registret.
- **ID** -autentiseringsuppgifter identifierare som `acr-credentials`.

När du är färdig bör formuläret autentiseringsuppgifter likna den här avbildningen:

![ACR-autentiseringsuppgifter](media/aks-jenkins/acr-credentials.png)

Klicka på **OK** och återgå till administrationsportalen Jenkins.

## <a name="create-jenkins-project"></a>Skapa Jenkins-projekt

Från administrationsportalen Jenkins klickar du på **nytt objekt**.

Namnge projektet, till exempel `azure-vote`väljer **Freestyle projektet**, och klicka på **OK**. 

![Jenkins projekt](media/aks-jenkins/jenkins-project.png)

Under **allmänna**väljer **GitHub projekt** och ange Webbadressen till din förgrening Azure röst GitHub-projektet.

![GitHub-projekt](media/aks-jenkins/github-project.png)

Under **källa kod Management**väljer **Git**, ange Webbadressen till din förgrening av Azure röst GitHub-lagringsplatsen. 

Autentiseringsuppgifter genom att klicka på och **Lägg till** > **Jenkins**. Under **typ**väljer **hemliga text** och ange din [GitHub personliga åtkomsttoken] [ git-access-token] som hemligheten. 

Välj **Lägg till** när du är klar.

![GitHub-autentiseringsuppgifter](media/aks-jenkins/github-creds.png)

Under **Skapa utlösare**väljer **GitHub hook utlösare för GITScm avsökning**.

![Jenkins Skapa utlösare](media/aks-jenkins/build-triggers.png)

Under **kompileringsmiljö**väljer **använda hemliga text eller filer**.

![Jenkins build-miljön](media/aks-jenkins/build-environment.png)

Under **bindningar**väljer **Lägg till** > **användarnamn och lösenord (avgränsade)**. 

Ange `ACR_ID` för den **variabelnamn**, och `ACR_PASSWORD` för den **Lösenordsvariabeln**.

![Jenkins bindningar](media/aks-jenkins/bindings.png)

Lägg till en **skapa steg** av typen **köra shell** och Använd följande text. Det här skriptet skapar en ny avbildning för behållaren och skickar den till din ACR-registret.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Lägga till en annan **skapa steg** av typen **köra shell** och Använd följande text. Det här skriptet uppdaterar Kubernetes distributionen.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

När du slutfört, klickar du på **spara**.

## <a name="test-the-jenkins-build"></a>Testa Jenkins build

Testa Jenkins versionen innan du fortsätter. Detta verifierar att bygga projektet har konfigurerats korrekt, rätt Kubernetes autentiseringsfilen är på plats och att rätt ACR autentiseringsuppgifter har angetts.

Klicka på **skapa nu** i den vänstra menyn i projektet. 

![Jenkins testa build](media/aks-jenkins/test-build.png)

Under den här processen klonas GitHub-lagringsplatsen till Jenkins build-servern. En ny behållare-avbildning har skapats och pushas till ACR-registret. Slutligen uppdateras Azure röst-program som körs på klustret AKS om du vill använda den nya avbildningen. Eftersom inga ändringar har gjorts till programkoden, ändras inte programmet.

När processen är klar, kan du klicka på **skapa #1** under Skapa historik och välj **konsolens utdata** att se alla utdata från processen för att bygga. Den sista raden bör ange en lyckad version. 

## <a name="create-github-webhook"></a>Skapa GitHub-webhook

Sedan koppla databasen som programmet ska Jenkins build-servern så att på alla commit utlöses en ny version.

1. Bläddra till andelen vridvuxna GitHub-lagret.
2. Välj **Inställningar** och sedan **Integreringar och tjänster** på vänster sida.
3. Välj **lägga till tjänsten**, ange `Jenkins (GitHub plugin)` i filterrutan och välj plugin-programmet.
4. För Jenkins koppla URL, ange `http://<publicIp:8080>/github-webhook/` där `publicIp` är IP-adressen för servern Jenkins. Se till att du inkluderar avslutande /.
5. Välj Lägg till tjänsten.
  
![GitHub-webhook](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testa processen CI/CD-slutpunkt till slutpunkt

På utvecklingsdatorn, öppna Klonat program med en redigerare. 

Under den **/azure-vote/azure-vote** directory, kan du hitta en fil med namnet **config_file.cfg**. Uppdatera rösten värdena i den här filen till något än katt och hund. 

I följande exempel visar och uppdatera **config_file.cfg** fil.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

När du är klar, spara filen och spara ändringarna push dem till din förgrening GitHub-Lagringsplatsens... När överföringen är klar, utlöser GitHub-webhook en ny version Jenkins som uppdaterar behållaren avbildningen och AKS distributionen. Övervaka build-processen på Jenkins administrationskonsolen. 

När versionen har slutförts, söka igen till slutpunkten för programmet att se att ändras.

![Azure rösten uppdateras](media/aks-jenkins/azure-vote-updated-safari.png)

Nu är har en process med enkel kontinuerlig distribution slutförts. Dessa steg och konfigurationer som visas i det här exemplet kan användas för att bygga ut en mer stabilt och klar för produktion kontinuerlig build-automatisering.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli