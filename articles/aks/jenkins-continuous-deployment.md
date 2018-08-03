---
title: Kontinuerlig Jenkins-distribution med Kubernetes i Azure Kubernetes Service
description: Så här automatiserar du en process för kontinuerlig distribution med Jenkins för att distribuera och uppgradera en behållarbaserad app med Kubernetes i Azure Kubernetes Service
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a1a6799bc049fea829f8e32d12705e26e3a41dc0
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425766"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Kontinuerlig distribution med Jenkins och Azure Kubernetes Service

Det här dokumentet beskriver hur du ställer in en grundläggande kontinuerlig distribution arbetsflöde mellan Jenkins och ett kluster i Azure Kubernetes Service (AKS).

Exempelarbetsflödet innehåller följande steg:

> [!div class="checklist"]
> * Distribuera programmet Azure voting till ett Kubernetes-kluster.
> * Uppdatera programkod Azure vote och skicka till en GitHub-lagringsplats som startar kontinuerlig distributionsprocessen.
> * Jenkins klonar lagret och skapar en ny behållaravbildning med den uppdaterade koden.
> * Den här avbildningen skickas till Azure Container Registry (ACR).
> * Det program som körs i AKS-kluster uppdateras med den nya behållaravbildningen.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna slutföra stegen i den här artikeln.

- Grundläggande förståelse för Kubernetes, Git, CI/CD och Azure Container Registry (ACR).
- En [kluster Azure Kubernetes Service (AKS)] [ aks-quickstart] och [AKS-autentiseringsuppgifter som konfigurerats] [ aks-credentials] i utvecklingssystemet.
- En [Azure Container Registry (ACR) registret][acr-quickstart], namnet på ACR-inloggning, och [ACR autentiseringsuppgifter] [ acr-authentication] push och pull åtkomst.
- Azure CLI installerad i utvecklingssystemet.
- Docker installerad i utvecklingssystemet.
- GitHub-konto, [personlig åtkomsttoken för GitHub][git-access-token], och Git-klient installerad i utvecklingssystemet.

## <a name="prepare-application"></a>Förbereda ett program

Azure vote-programmet som används i det här dokumentet innehåller ett webbgränssnitt som finns i en eller flera poddar och en andra pod som är värd för Redis för tillfällig lagring.

Innan du skapar Jenkins / AKS-integrering, förbereda och distribuera programmet Azure voting till AKS-klustret. Tänk på det som versionen av programmet.

Gå till följande GitHub-databasen.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

När förgreningen har skapats kan du klona den utvecklingssystemet. Se till att du använder URL: en för din förgrening när Kloningen av den här lagringsplatsen.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Ändra katalogerna så att du arbetar från den klonade katalogen.

```bash
cd azure-voting-app-redis
```

Kör den `docker-compose.yaml` filen för att skapa den `azure-vote-front` behållaravbildning och starta programmet.

```bash
docker-compose up -d
```

När du är klar kan du använda den [dockeravbildning] [ docker-images] kommando för att se den skapa avbildningen.

Observera att tre avbildningar har laddats ned eller skapats. `azure-vote-front`-avbildningen innehåller programmet och använder `nginx-flask`-avbildningen som bas. `redis`-avbildningen används för att starta en Redis-instans.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Hämta ACR-inloggningsserver med den [az acr list] [ az-acr-list] kommando. Se till att uppdatera resursgruppens namn med resursgruppen som är värd för din ACR-registret.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd den [dockertagg] [ docker-tag] kommando för att tagga avbildningen med namnet på inloggningsservern och versionsnumret `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Uppdatera värdet för ACR-inloggning med namnet på inloggningsservern för ACR och push i `azure-vote-front` avbildningen till registret.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Distribuera program till Kubernetes

Ett Kubernetes-manifestfil kan vara hittades i roten för lagringsplatsen Azure vote och kan användas för att distribuera programmet till ditt Kubernetes-kluster.

Först måste uppdatera den **azure-vote-all-in-one-redis.yaml** manifestfilen med platsen för din ACR-registret. Öppna filen med valfritt redigeringsprogram och Ersätt `microsoft` med namnet på ACR-inloggningsservern. Du hittar värdet på rad **47** i manifestfilen.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Använd sedan den [kubectl gäller] [ kubectl-apply] kommando för att köra programmet. Det här kommandot parsar manifestfilen och skapar de definierade Kubernetes-objekten.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

En [Kubernetes-tjänst] [ kubernetes-service] skapas för att exponera till internet. Den här processen kan ta ett par minuter.

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

## <a name="deploy-jenkins-to-vm"></a>Distribuera Jenkins till virtuell dator

Ett skript har skapats i förväg att distribuera en virtuell dator, konfigurera åtkomst till nätverket och slutföra en grundläggande installation av Jenkins. Skriptet kopierar dessutom Kubernetes-konfigurationsfil från utvecklingssystemet till Jenkins-system. Den här filen används för autentisering mellan Jenkins och AKS-klustret.

Kör följande kommandon för att ladda ned och kör skriptet. Den under den URL som kan också användas för att granska innehållet i skriptet.

> [!WARNING]
> Det här exempelskriptet är för demo att snabbt etablera en Jenkins-miljö som körs på en virtuell Azure-dator. Azure det anpassade skripttillägget används för att konfigurera en virtuell dator och visar sedan autentiseringsuppgifterna som krävs. Din *~/.kube/config* kopieras till Jenkins VM.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

När skriptet har slutförts matar ut en adress för Jenkins-server som också en nyckel för att låsa upp Jenkins. Bläddra till URL: en, ange nyckeln och följa den anvisningarna på skärmen för att slutföra Jenkins-konfigurationen.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Jenkins-miljövariabler

En Jenkins-miljövariabeln är används för att lagra namnet på inloggningsservern för Azure Container Registry (ACR). Den här variabeln refereras till under kontinuerlig distribution Jenkins-jobbet.

Medan du är på administrationsportalen för Jenkins klickar du på **hantera Jenkins** > **konfigurera System**.

Under **globala egenskaper**väljer **miljövariabler**, och Lägg till en variabel med namnet `ACR_LOGINSERVER` och ett värde på din ACR-inloggningsserver.

![Jenkins-miljövariabler](media/aks-jenkins/env-variables.png)

När du är klar klickar du på **spara** på konfigurationssidan Jenkins.

## <a name="jenkins-credentials"></a>Jenkins-autentiseringsuppgifter

Lagra nu ACR-autentiseringsuppgifter i en Jenkins-objektet för autentiseringsuppgifter. Refererar till dessa autentiseringsuppgifter under Jenkins-byggjobb.

Tillbaka på administrationsportalen för Jenkins klickar du på **autentiseringsuppgifter** > **Jenkins** > **globala autentiseringsuppgifter (obegränsad)**  >   **Lägg till autentiseringsuppgifter**.

Se till att credential-typ är **användarnamn med lösenord** och ange följande:

- **Användarnamn** -ID för tjänstens huvudnamn användningen för autentisering med ACR-registret.
- **Lösenord** -klienthemlighet för tjänstens huvudnamn användningen för autentisering med ACR-registret.
- **ID** -autentiseringsuppgift identifierare som `acr-credentials`.

När du är klar bör formuläret autentiseringsuppgifter likna den här bilden:

![ACR-autentiseringsuppgifter](media/aks-jenkins/acr-credentials.png)

Klicka på **OK** och gå tillbaka till Jenkins-administratörsportalen.

## <a name="create-jenkins-project"></a>Skapa Jenkins-projektet

Från administratörsportalen Jenkins klickar du på **nytt objekt**.

Ge projektet ett namn, till exempel `azure-vote`väljer **Freestyle-projekt**, och klicka på **OK**.

![Jenkins-projektet](media/aks-jenkins/jenkins-project.png)

Under **Allmänt**väljer **GitHub-projektet** och ange Webbadressen till din förgrening av GitHub-projektet Azure voting.

![GitHub-projekt](media/aks-jenkins/github-project.png)

Under **Source Code Management**väljer **Git**, anger du Webbadressen till din förgrening av Azure Vote GitHub-lagringsplatsen.

Autentiseringsuppgifterna, klickar du på och **Lägg till** > **Jenkins**. Under **typ**väljer **hemliga text** och ange din [personlig åtkomsttoken för GitHub] [ git-access-token] som hemligheten.

Välj **Lägg till** när du är klar.

![Autentiseringsuppgifter för GitHub](media/aks-jenkins/github-creds.png)

Under **Build-utlösare**väljer **GitHub hook trigger för GITScm-avsökning**.

![Jenkins Skapa utlösare](media/aks-jenkins/build-triggers.png)

Under **kompileringsmiljö**väljer **använda hemliga texter eller filer**.

![Kompileringsmiljö för Jenkins](media/aks-jenkins/build-environment.png)

Under **bindningar**väljer **Lägg till** > **användarnamn och lösenord (avgränsade)**.

Ange `ACR_ID` för den **Användarnamnsvariabeln**, och `ACR_PASSWORD` för den **Lösenordsvariabel**.

![Jenkins-bindningar](media/aks-jenkins/bindings.png)

Lägg till en **skapa steg** av typen **köra shell** och Använd följande text. Det här skriptet skapar en ny behållaravbildning och skickas till ACR-registret.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Lägg till en annan **skapa steg** av typen **köra shell** och Använd följande text. Det här skriptet uppdaterar Kubernetes-distribution.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

När du har slutförts, klickar du på **spara**.

## <a name="test-the-jenkins-build"></a>Testa Jenkins build

Testa Jenkins build innan du fortsätter. Detta bekräftar att build-jobbet har konfigurerats korrekt, rätt autentiseringsfilen för Kubernetes är på plats och att rätt ACR-autentiseringsuppgifter har angetts.

Klicka på **skapa nu** i den vänstra menyn i projektet.

![Jenkins testa build](media/aks-jenkins/test-build.png)

Under den här processen klona GitHub-lagringsplatsen Jenkins build-servern. En ny behållaravbildning bygger och skickas till ACR-registret. Dessutom uppdateras Azure vote-programmet som körs på AKS-klustret för att använda den nya avbildningen. Eftersom inga ändringar har gjorts till programkoden, ändras inte programmet.

När processen är klar klickar du på **skapa #1** under versionshistorik och välj **Konsolutdata** att se alla utdata från skapandeprocessen. Den sista raden ska ange en version.

## <a name="create-github-webhook"></a>Skapa GitHub-webhook

Koppla sedan programmet lagringsplatsen till Jenkins build-servern så att på skrivning, utlöses en ny version.

1. Bläddra till den förgrenade GitHub-lagringsplatsen.
2. Välj **inställningar**och välj sedan **Webhooks** till vänster.
3. Välja att **Lägg till webhook**. För den *nyttolast-URL*, ange `http://<publicIp:8080>/github-webhook/` där `publicIp` är IP-adressen för Jenkins-servern. Se till att du inkluderar avslutande /. Lämna andra standardinställningar för innehållstyp och utlösare på *push* händelser.
4. Välj **Lägg till webhook**.

    ![GitHub-webhook](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testa CI/CD-processen från slutpunkt till slutpunkt

Öppna den klonade appen med en Kodredigerare på din utvecklingsdator.

Under den **/azure-vote/azure-vote** directory, hitta en fil med namnet **config_file.cfg**. Uppdatera röst värdena i den här filen till något annat än hundar och katter.

I följande exempel visar och uppdateras **config_file.cfg** fil.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

När du är klar spara filen, spara ändringarna och skicka dem till din förgrening av GitHub-lagringsplatsen... När överföringen är klar, utlöser GitHub-webhook en ny Jenkins build som uppdaterar behållaravbildningen och AKS-distributionen. Övervaka skapandeprocessen Jenkins-administratörskonsolen.

När bygget har slutförts bläddrar du igen till programslutpunkt att se att ändras.

![Azure vote uppdateras](media/aks-jenkins/azure-vote-updated-safari.png)

Nu har en enkel kontinuerlig process slutförts. Stegen och konfigurationer som visas i det här exemplet kan användas för att bygga ut ett mer stabilt och vara färdigt för produktionsanvändning kontinuerligt skapande automation.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli