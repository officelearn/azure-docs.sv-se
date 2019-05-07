---
title: Självstudie – distribuera från GitHub till Azure Kubernetes Service (AKS) med Jenkins
description: Konfigurera Jenkins för kontinuerlig integrering (CI) från GitHub och distribution (CD) till Azure Kubernetes Service (AKS)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: 703aa081c8acf41f9206e2b0ccff45571367d2e8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073789"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Självstudier: Distribuera från GitHub till Azure Kubernetes Service (AKS) med Jenkins kontinuerlig integrering och distribution

Den här självstudien distribuerar en exempelapp från GitHub till en [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) kluster genom att konfigurera kontinuerlig integrering (CI) och kontinuerlig distribution (CD) i Jenkins. När du uppdaterar din app genom att skicka incheckningar till GitHub, Jenkins automatiskt körs en ny version av behållare, push-meddelanden behållaravbildningar till Azure Container Registry (ACR) och kör din app i AKS. 

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Distribuera en exempelapp för Azure voting till ett AKS-kluster.
> * Skapa en grundläggande Jenkins-projektet.
> * Ställ in autentiseringsuppgifter för Jenkins kan interagera med ACR.
> * Skapa en Jenkins-byggjobb och GitHub-webbhook för automatiserade versioner.
> * Testa CI/CD-pipeline för att uppdatera ett program i AKS baserat på GitHub-incheckningar kod.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du följande objekt:

- Grundläggande förståelse för Kubernetes, Git, CI/CD och container-avbildningar

- En [AKS-kluster] [ aks-quickstart] och `kubectl` konfigurerats med den [autentiseringsuppgifter för AKS-kluster][aks-credentials]

- En [Azure Container Registry (ACR) registret][acr-quickstart], namnet på ACR-inloggningsservern och AKS-kluster som har konfigurerats att [autentisera med ACR-registret][acr-authentication]

- Azure CLI version 2.0.46 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

- [Docker installerat] [ docker-install] i utvecklingssystemet

- En GitHub-konto, [personlig åtkomsttoken för GitHub][git-access-token], och Git-klient installerad i utvecklingssystemet

- Om du anger din egen Jenkins-instans i stället för att det här exemplet skriptade sätt att distribuera Jenkins kan Jenkins-instans behov [Docker installerat och konfigurerat] [ docker-install] och [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Förbereda din app

I den här artikeln använder du ett exempelprogram för Azure voting som innehåller ett webbgränssnitt som finns i en eller flera poddar och en andra pod som är värd för Redis för tillfällig lagring. Innan du integrerar Jenkins och AKS för automatisk distribution, först manuellt förbereda och distribuera programmet Azure voting till AKS-klustret. Den här manuell distribution är versionen av programmet och kan du se hur programmet fungerar i praktiken.

Förgrena följande GitHub-lagringsplatsen för exempelprogrammet - [ https://github.com/Azure-Samples/azure-voting-app-redis ](https://github.com/Azure-Samples/azure-voting-app-redis). Om du vill förgrena lagringsplatsen till ditt eget GitHub-konto väljer du knappen **Fork** (Förgrening) i det övre högra hörnet.

Klona förgreningen till utvecklingssystemet. Kontrollera att du använder URL: en för din förgrening när Kloningen av den här lagringsplatsen:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Ändra till katalogen för din klonade förgrening:

```console
cd azure-voting-app-redis
```

Du kan skapa behållaravbildningar som behövs för exempelprogrammet med den *docker compose.yaml* med `docker-compose`:

```console
docker-compose up -d
```

Nödvändiga Källavbildningen hämtas och programbehållare som byggts. Du kan sedan använda den [dockeravbildning] [ docker-images] kommando för att se den skapa avbildningen. Tre avbildningar har hämtats eller skapats. `azure-vote-front`-avbildningen innehåller programmet och använder `nginx-flask`-avbildningen som bas. Den `redis` avbildningen används för att starta en Redis-instans:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Innan du kan skicka den *azure-vote-front* behållaravbildning för ACR, hämta ACR-inloggningsserver med den [az acr list] [ az-acr-list] kommando. I följande exempel hämtas serveradress för ACR-inloggning för ett register i resursgruppen med namnet *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd den [dockertagg] [ docker-tag] kommando för att tagga avbildningen med namnet på ACR-inloggningsservern och versionsnumret `v1`. Ange din egen `<acrLoginServer>` namn som hämtades i föregående steg:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Slutligen kan skicka den *azure-vote-front* avbildningen till ACR-registret. Ersätter `<acrLoginServer>` med namnet på inloggningsservern för dina egna ACR-registret som `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Distribuera exempelprogrammet till AKS

Du kan använda Kubernetes-manifestfilen i roten av lagringsplatsen för Azure vote-databasen för att distribuera exempelprogrammet för att AKS-klustret. Öppna den *azure-vote-all-in-one-redis.yaml* manifestfilen med en redigerare som `vi`. Ersätt `microsoft` med namnet på din ACR-inloggningsserver. Det här värdet finns på rad **47** för manifestfilen:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Använd sedan den [kubectl gäller] [ kubectl-apply] kommando för att distribuera programmet till AKS-klustret:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

En Kubernetes load balancer-tjänsten har skapats för att exponera till internet. Den här processen kan ta ett par minuter. Du kan övervaka förloppet för load balancer distributionen genom att använda den [kubectl hämta tjänst] [ kubectl-get] med den `--watch` argumentet. När *EXTERNAL-IP*-adressen har ändrats från *pending* till en *IP-adress* använder du `Control + C` för att stoppa kubectl-övervakningsprocessen.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Öppna en webbläsare på den externa IP-adressen för din tjänst om du vill se hur programmet fungerar i praktiken. Programmet Azure voting visas som i följande exempel:

![Exempel på Azure vote-programmet som körs i AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Distribuera Jenkins till en Azure virtuell dator

För att snabbt distribuera Jenkins för användning i den här artikeln kan använda du följande skript för att distribuera en Azure virtuell dator, konfigurera åtkomst till nätverket och slutföra en grundläggande installation av Jenkins. För autentisering mellan Jenkins och AKS-kluster kopierar skriptet Kubernetes-konfigurationsfil från utvecklingssystemet till Jenkins-system.

> [!WARNING]
> Det här exempelskriptet är för demo att snabbt etablera en Jenkins-miljö som körs på en virtuell Azure-dator. Azure det anpassade skripttillägget används för att konfigurera en virtuell dator och visar sedan autentiseringsuppgifterna som krävs. Din *~/.kube/config* kopieras till Jenkins VM.

Kör följande kommandon för att ladda ned och kör skriptet. Du bör granska innehållet i alla skript innan du kör den- [ https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh ](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Det tar några minuter att skapa den virtuella datorn och distribuera de nödvändiga komponenterna för Docker och Jenkins. När skriptet har slutförts, visar en adress för Jenkins-servern och en nyckel för att låsa upp instrumentpanelen som visas i följande Exempelutdata:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Öppna en webbläsare på den URL som visas och ange Lås upplåsningsnyckeln. Följ den anvisningarna på skärmen för att slutföra Jenkins-konfigurationen:

- Välj **installera föreslagna plugin-program**
- Skapa den första administratörsanvändaren. Ange ett användarnamn som *azureuser*, ange ditt eget säkra lösenord. Skriv slutligen ett fullständigt namn och e-postadress.
- Välj **Spara och Slutför**
- När Jenkins är klar, väljer du **Börja använda Jenkins**
    - Om en tom sida visas i din webbläsare när du börjar använda Jenkins, starta om Jenkins-tjänsten. Starta om tjänsten, SSH till den offentliga IP-adressen av Jenkins-instansen och skriv `sudo service jenkins restart`. När tjänsten har startats om, uppdaterar du webbläsaren.
- Logga in på Jenkins med det användarnamn och lösenord som du skapade i installationen.

## <a name="create-a-jenkins-environment-variable"></a>Skapa en Jenkins-miljövariabel

En Jenkins-miljövariabeln är används för att lagra namnet på ACR-inloggningsservern. Den här variabeln refereras till under Jenkins-byggjobb. Om du vill skapa den här miljövariabeln, gör du följande:

- På vänster sida av Jenkins-portalen, väljer **hantera Jenkins** > **konfigurera System**
- Under **globala egenskaper**väljer **miljövariabler**. Lägg till en variabel med namnet `ACR_LOGINSERVER` och värdet för din ACR-inloggningsserver.

    ![Jenkins-miljövariabler](media/aks-jenkins/env-variables.png)

- När du är klar klickar du på **spara** längst ned på sidan för konfiguration av Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Skapa en Jenkins-autentiseringsuppgift för ACR

Om du vill tillåta Jenkins att skapa och skicka sedan uppdaterade behållaravbildningar till ACR, måste du ange autentiseringsuppgifter för ACR. Den här autentiseringen kan använda Azure Active Directory-tjänstobjekt. I de obligatoriska stegen du har konfigurerat tjänstens huvudnamn för din AKS-kluster med *läsare* behörigheter till ACR-registret. De här behörigheterna tillåter AKS-kluster till *pull* avbildningar från ACR-registret. Under CI/CD-processen Jenkins skapar nya behållaravbildningar baserat på programuppdateringar och måste sedan *push* dessa avbildningar till ACR-registret. Åtskillnad av roller och behörigheter, nu konfigurera ett huvudnamn för tjänsten för Jenkins med *deltagare* behörigheter till ACR-registret.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Skapa ett tjänstobjekt för Jenkins att använda ACR

Börja med att skapa ett tjänstobjekt med den [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] kommando:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anteckna den *appId* och *lösenord* visas i dina utdata. De här värdena används i följande steg för att konfigurera credential-resurs i Jenkins.

Hämta resurs-ID för din ACR-registret med den [az acr show] [ az-acr-show] kommandot och spara den som en variabel. Ange ditt resursgruppens namn och en ACR-namn:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Skapa nu en rolltilldelning för att tilldela tjänstobjektet *deltagare* rättigheter till ACR-registret. I följande exempel anger egna *appId* visas i utdata en föregående kommando för att skapa tjänstens huvudnamn:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Skapa en resurs för autentiseringsuppgifter i Jenkins för ACR-tjänsten huvudnamn

Med den rolltilldelning som skapats i Azure, nu lagra din ACR-autentiseringsuppgifter i ett Jenkins-autentiseringsuppgiftsobjekt. Refererar till dessa autentiseringsuppgifter under Jenkins-byggjobb.

Tillbaka på vänster sida av Jenkins-portalen klickar du på **autentiseringsuppgifter** > **Jenkins** > **globala autentiseringsuppgifter (obegränsad)**  >  **Lägg till autentiseringsuppgifter**

Se till att credential-typ är **användarnamn med lösenord** och ange följande:

- **Användarnamn** – *appId* för tjänstens huvudnamn som skapats för autentisering med ACR-registret.
- **Lösenordet** – *lösenord* för tjänstens huvudnamn som skapats för autentisering med ACR-registret.
- **ID** -autentiseringsuppgift identifierare som *acr-autentiseringsuppgifter*

När du är klar formuläret autentiseringsuppgifter ser ut som i följande exempel:

![Skapa ett Jenkins-autentiseringsuppgiftsobjekt med information om tjänstens huvudnamn](media/aks-jenkins/acr-credentials.png)

Klicka på **OK** och gå tillbaka till Jenkins-portalen.

## <a name="create-a-jenkins-project"></a>Skapa ett Jenkins-projekt

Startsidan för din Jenkins-portalen, väljer du **nytt objekt** på vänster sida:

1. Ange *azure-vote* som jobbnamn. Välj **Freestyle-projekt**och välj sedan **OK**
1. Under den **Allmänt** väljer **GitHub-projektet** och ange din förgrenade lagringsplats-URL, till exempel *https:\//github.com/\<your-github-konto\>/azure-voting-app-redis*
1. Under den **Källkodshantering** väljer **Git**, ange din förgrenade lagringsplats *.git* URL, till exempel *https:\//github.com/\<your-github-konto\>/azure-voting-app-redis.git*

1. Under den **Build-utlösare** väljer **GitHub hook trigger för GITscm-avsökning**
1. Under **kompileringsmiljö**väljer **använda hemliga texter eller filer**
1. Under **bindningar**väljer **Lägg till** > **användarnamn och lösenord (avgränsade)**
   - Ange `ACR_ID` för den **Användarnamnsvariabeln**, och `ACR_PASSWORD` för den **Lösenordsvariabel**

     ![Jenkins-bindningar](media/aks-jenkins/bindings.png)

1. Välj att lägga till en **skapa steg** av typen **köra shell** och Använd följande text. Det här skriptet skapar en ny behållaravbildning och skickas till ACR-registret.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Lägg till en annan **skapa steg** av typen **köra shell** och Använd följande text. Det här skriptet uppdaterar programdistribution i AKS med den nya behållaravbildningen från ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. När du har slutförts, klickar du på **spara**.

## <a name="test-the-jenkins-build"></a>Testa Jenkins build

Innan du automatisera jobb baserat på GitHub-incheckningar först manuellt testa Jenkins build. Den här manuell bygger verifierar att jobbet har konfigurerats korrekt, rätt autentiseringsfilen för Kubernetes är på plats och att autentiseringen med ACR fungerar.

På den vänstra menyn för projektet väljer **skapa nu**.

![Jenkins testa build](media/aks-jenkins/test-build.png)

Den första versionen tar en minut eller två som Docker-avbildningslager är hämtade Jenkins-servern. Efterföljande versioner kan använda cachelagrade avbildningslager för att förbättra build gånger.

När du skapar klona GitHub-lagringsplatsen Jenkins build-servern. En ny behållaravbildning bygger och skickas till ACR-registret. Dessutom uppdateras Azure vote-programmet som körs på AKS-klustret för att använda den nya avbildningen. Eftersom inga ändringar har gjorts till programkoden, ändras inte programmet om du visar exempelappen i en webbläsare.

När build-jobbet har slutförts, klickar du på **skapa #1** under versionshistorik. Välj **Konsolutdata** och visa utdata från skapandeprocessen. Den sista raden ska ange en version.

## <a name="create-a-github-webhook"></a>Skapa en GitHub-webhook

Med en manuell version fullständig, nu integrera GitHub i Jenkins build. En webhook kan användas för att köra varje gång en kodgenomförande görs i GitHub för Jenkins-byggjobb. För att skapa GitHub-webhook, gör du följande:

1. Bläddra till den förgrenade GitHub-databasen i en webbläsare.
1. Välj **Inställningar** och sedan **Webhooks** på vänster sida.
1. Välja att **Lägg till webhook**. För den *nyttolast-URL*, ange `http://<publicIp:8080>/github-webhook/`, där `<publicIp>` är IP-adressen för Jenkins-servern. Se till att du inkluderar avslutande /. Lämna andra standardinställningar för innehållstyp och utlösare på *push* händelser.
1. Välj **Lägg till webhook**.

    ![Skapa en webhook i GitHub för Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testa den fullständiga CI/CD-pipelinen

Nu kan du testa hela CI/CD-pipeline. När du skickar en kod till GitHub, händer följande:

1. GitHub-webhook når till Jenkins.
1. Jenkins startar build-jobbet och hämtar den senaste kodgenomförande från GitHub.
1. En Docker-version har startats med hjälp av den uppdaterade koden och den nya behållaravbildningen märks med senaste build-nummer.
1. Den här nya behållaravbildningen skickas till Azure Container Registry.
1. Programmet distribueras till Azure Kubernetes Service-uppdateringar med den senaste behållaravbildningen från Azure Container Registry-registret.

Öppna den klonade appen med en Kodredigerare på din utvecklingsdator. Under den */azure-vote/azure-vote* katalog, öppna filen med namnet **config_file.cfg**. Uppdatera röst värdena i den här filen till något än katter och hundar, som visas i följande exempel:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

När uppdaterades, spara filen, spara ändringarna och skicka dem till din förgrening av GitHub-lagringsplatsen. GitHub-webhook utlöser ett nytt build-jobb i Jenkins. Övervaka skapandeprocessen i web Jenkins-instrumentpanelen. Det tar några sekunder att hämta den senaste koden, skapa och skicka den uppdaterade avbildningen och distribuera det uppdaterade programmet i AKS.

Uppdatera din webbläsare i exempelprogrammet för Azure voting när versionen har slutförts. Ändringarna visas, enligt följande exempel:

![Exemplet Azure rösta i AKS uppdaterad av Jenkins-byggjobb](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att använda Jenkins som en del av en CI/CD-lösning. AKS kan integreras med andra CI/CD-lösningar och verktyg för automatisering som den [Azure DevOps-projekt] [ azure-devops] eller [skapar ett AKS-kluster med Ansible] [ aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
