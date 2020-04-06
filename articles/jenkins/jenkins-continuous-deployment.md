---
title: Distribuera från GitHub till AKS med Jenkins
titleSuffix: Azure Kubernetes Service
description: Konfigurera Jenkins för kontinuerlig integrering (CI) från GitHub och kontinuerlig distribution (CD) till Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: a93cfc77178ff7638217663c2ceda500aae4cfd7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668639"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Självstudiekurs: Distribuera från GitHub till Azure Kubernetes Service (AKS) med Jenkins kontinuerlig integrering och distribution

Den här självstudien distribuerar en exempelapp från GitHub till ett [AKS-kluster (Azure Kubernetes Service)](/azure/aks/intro-kubernetes) genom att konfigurera kontinuerlig integrering (CI) och CD-program (Continuous Deployment) i Jenkins. På så sätt, när du uppdaterar din app genom att skicka commits till GitHub, Jenkins automatiskt kör en ny behållare bygga, skjuter behållaravbildningar till Azure Container Registry (ACR), och sedan kör din app i AKS. 

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Distribuera en exempelapp för Azure-omröstning till ett AKS-kluster.
> * Skapa ett grundläggande Jenkins-projekt.
> * Ställ in autentiseringsuppgifter för Jenkins att interagera med ACR.
> * Skapa ett Jenkins-byggjobb och GitHub webhook för automatiserade versioner.
> * Testa CI/CD-pipelinen för att uppdatera ett program i AKS baserat på GitHub-kod.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande objekt:

- Grundläggande förståelse av Kubernetes, Git, CI/CD och behållaravbildningar

- Ett [AKS-kluster](../aks/kubernetes-walkthrough.md) och `kubectl` konfigurerat med [AKS-klusterautentiseringsuppgifterna](/cli/azure/aks#az-aks-get-credentials).

- Ett [ACR-register (Azure Container Registry),](../container-registry/container-registry-get-started-azure-cli.md)ACR-inloggningsserverns namn och AKS-klustret som konfigurerats för att [autentisera med ACR-registret](../aks/cluster-container-registry-integration.md).

- Azure CLI version 2.0.46 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

- [Docker installerad](https://docs.docker.com/install/) på ditt utvecklingssystem

- Ett GitHub-konto, [GitHub-token för personlig åtkomst](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)och Git-klient installerad på ditt utvecklingssystem

- Om du anger en egen Jenkins-förekomst i stället för det här exempelskriptet för att distribuera Jenkins, behöver Jenkins-instansen [Docker installerad och konfigurerad](https://docs.docker.com/install/) och [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Förbereda din app

I den här artikeln använder du ett exempel på Azure-omröstningsprogram som innehåller ett webbgränssnitt som finns i en eller flera poddar och en andra pod som är värd för Redis för tillfällig datalagring. Innan du integrerar Jenkins och AKS för automatiserade distributioner ska du först förbereda och distribuera Azure-omröstningsprogrammet manuellt till aks-klustret. Den här manuella distributionen är version ett av programmet och låter dig se programmet i kraft.

> [!NOTE]
> Exempelet På Azure-omröstningsprogrammet används en Linux-pod som är schemalagd att köras på en Linux-nod. Det flöde som beskrivs i den här artikeln fungerar också för en Windows Server-pod som schemalagts på en Windows Server-nod.

Fork följande GitHub-databas för [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)exempelprogrammet - . Om du vill förgrena lagringsplatsen till ditt eget GitHub-konto väljer du knappen **Fork** (Förgrening) i det övre högra hörnet.

Klona gaffeln till ditt utvecklingssystem. Se till att du använder url:en till gaffeln när du klonar den här reporäntan:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Ändra till katalogen för din klonade gaffel:

```console
cd azure-voting-app-redis
```

Om du vill skapa behållaravbildningar som behövs för exempelprogrammet `docker-compose`använder du filen *docker-compose.yaml* med:

```console
docker-compose up -d
```

De nödvändiga basavbildningarna hämtas och applikationsbehållarna byggs. Du kan sedan använda kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/) för att se den skapade bilden. Tre avbildningar har hämtats eller skapats. `azure-vote-front`-avbildningen innehåller programmet och använder `nginx-flask`-avbildningen som bas. Bilden `redis` används för att starta en Redis-förekomst:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Innan du kan skicka *azure-vote-front-behållaravbildningen* till ACR, hämta din ACR-inloggningsserver med kommandot [az acr list.](/cli/azure/acr#az-acr-list) I följande exempel får ACR-inloggningsserveradressen för ett register i resursgruppen *myResourceGroup:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd [kommandot docker tag](https://docs.docker.com/engine/reference/commandline/tag/) för att tagga bilden med ACR-inloggningsserverns namn och ett versionsnummer av `v1`. Ange ditt `<acrLoginServer>` eget namn som erhållits i föregående steg:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Slutligen, skicka *azure-vote-front-avbildningen* till ditt ACR-register. Återigen, `<acrLoginServer>` ersätta med inloggningsservern namnet på ditt `myacrregistry.azurecr.io`eget ACR-register, till exempel:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Distribuera exempelprogrammet till AKS

Om du vill distribuera exempelprogrammet till AKS-klustret kan du använda kubernetes manifestfilen i roten till azure-röstdatabasens repo. Öppna manifestfilen *azure-vote-all-in-one-redis.yaml* med en `vi`redigerare som . Ersätt `microsoft` med namnet på din ACR-inloggningsserver. Det här värdet finns på rad **47** i manifestfilen:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Använd sedan kommandot [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) för att distribuera programmet till AKS-klustret:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

En Kubernetes belastningsutjämnad tjänst skapas för att exponera programmet för Internet. Den här processen kan ta ett par minuter. Om du vill övervaka förloppet för belastningsutjämnad `--watch` distributionen använder du kommandot [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) med argumentet. När *EXTERNAL-IP*-adressen har ändrats från *pending* till en *IP-adress* använder du `Control + C` för att stoppa kubectl-övervakningsprocessen.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Om du vill se programmet i praktiken öppnar du en webbläsare för tjänstens externa IP-adress. Azure-omröstningsprogrammet visas, vilket visas i följande exempel:

![Azure-exempel omröstningsprogram som körs i AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Distribuera Jenkins till en virtuell Azure-dator

Om du snabbt vill distribuera Jenkins för användning i den här artikeln kan du använda följande skript för att distribuera en virtuell Azure-dator, konfigurera nätverksåtkomst och slutföra en grundläggande installation av Jenkins. För autentisering mellan Jenkins och AKS-klustret kopierar skriptet konfigurationsfilen kubernetes från utvecklingssystemet till Jenkins-systemet.

> [!WARNING]
> Det här exempelskriptet är för demoändamål för att snabbt etablera en Jenkins-miljö som körs på en Virtuell Azure.This sample script is for demo purposes to quickly provision a Jenkins environment that runs on an Azure VM. Den använder Azure anpassade skripttillägg för att konfigurera en virtuell dator och sedan visa de nödvändiga autentiseringsuppgifterna. Din *~/.kube/config* kopieras till Jenkins VM.

Kör följande kommandon för att hämta och köra skriptet. Du bör granska innehållet i ett skript [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)innan du kör det - .

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Det tar några minuter att skapa den virtuella datorn och distribuera de nödvändiga komponenterna för Docker och Jenkins. När skriptet är klart matar det ut en adress för Jenkins-servern och en nyckel för att låsa upp instrumentpanelen, vilket visas i följande exempelutdata:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Öppna en webbläsare till webbadressen som visas och ange upplåsningsnyckeln. Följ anvisningarna på skärmen för att slutföra Jenkins-konfigurationen:

- Välj **Installera föreslagna plugin-program**
- Skapa den första administratörsanvändaren. Ange ett användarnamn, till exempel *azureuser,* och ange sedan ditt eget säkra lösenord. Skriv slutligen ett fullständigt namn och e-postadress.
- Välj **Spara och Slutför**
- När Jenkins är klar, väljer du **Börja använda Jenkins**
    - Om en tom sida visas i din webbläsare när du börjar använda Jenkins, starta om Jenkins-tjänsten. Om du vill starta om tjänsten går SSH till `sudo service jenkins restart`den offentliga IP-adressen för Jenkins-instansen och skriver . När tjänsten har startats om uppdaterar du webbläsaren.
- Logga in på Jenkins med det användarnamn och lösenord som du skapade i installationsprocessen.

## <a name="create-a-jenkins-environment-variable"></a>Skapa en Jenkins-miljövariabel

En Jenkins-miljövariabel används för att hålla acr-inloggningsserverns namn. Den här variabeln refereras under Jenkins-byggjobbet. Så här skapar du den här miljövariabeln:

- På jenkins-portalens vänstra sida väljer du **Hantera Jenkins** > **Konfigurera system**
- Välj **Miljövariabler**under **Globala egenskaper.** Lägg till en `ACR_LOGINSERVER` variabel med namnet och värdet på din ACR-inloggningsserver.

    ![Jenkins miljövariabler](media/jenkins-continuous-deployment/env-variables.png)

- När du är klar klickar du på **Spara** längst ned på Jenkins konfigurationssida.

## <a name="create-a-jenkins-credential-for-acr"></a>Skapa en Jenkins-autentiseringsuppgifter för ACR

Om du vill att Jenkins ska kunna skapa och sedan skicka uppdaterade behållaravbildningar till ACR måste du ange autentiseringsuppgifter för ACR. Den här autentiseringen kan använda Azure Active Directory-tjänsthuvudnamn. I förinställningarna har du konfigurerat tjänstens huvudnamn för *Reader* AKS-klustret med läsarbehörighet till ACR-registret. Dessa behörigheter gör det möjligt för AKS-klustret att *hämta* avbildningar från ACR-registret. Under CI/CD-processen skapar Jenkins nya behållaravbildningar baserat på programuppdateringar och måste sedan *skicka* dessa avbildningar till ACR-registret. För separation av roller och behörigheter konfigurerar du nu ett tjänsthuvudnamn för Jenkins med *deltagarbehörighet* till ACR-registret.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Skapa ett tjänsthuvudnamn för Jenkins att använda ACR

Skapa först ett tjänsthuvudnamn med kommandot [az ad sp create-for-rbac:](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)

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

Anteckna *appId* och *lösenord* som visas i utdata. Dessa värden används i följande steg för att konfigurera autentiseringsresurserna i Jenkins.

Hämta resurs-ID:n för ACR-registret med kommandot [az acr show](/cli/azure/acr#az-acr-show) och lagra det som en variabel. Ange resursgruppsnamnet och ACR-namnet:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Nu skapa en rolltilldelning för att tilldela tjänstens *huvuddeltagare* rättigheter till ACR-registret. I följande exempel anger du din egen *appId som* visas i utdata ett tidigare kommando för att skapa tjänstens huvudnamn:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Skapa en autentiseringsresurs i Jenkins för ACR-tjänstens huvudnamn

Med rolltilldelningen som skapats i Azure lagrar du nu dina ACR-autentiseringsuppgifter i ett Jenkins-autentiseringsobjekt. Dessa autentiseringsuppgifter refereras under Jenkins-byggjobbet.

Tillbaka till vänster på Jenkins-portalen klickar du på **Autentiseringsuppgifter** > **jenkins** > **globala autentiseringsuppgifter (obegränsade)** > **Lägg till autentiseringsuppgifter**

Kontrollera att autentiseringsuppgifterna är **användarnamn med lösenord** och ange följande objekt:

- **Användarnamn** - *AppId* för tjänstens huvudnamn som skapats för autentisering med acr-registret.
- **Lösenord** - *Lösenordet* för tjänstens huvudnamn som skapats för autentisering med ACR-registret.
- **ID** - Autentiseringsuppgifter identifierare såsom *acr-autentiseringsuppgifter*

När det är klart ser autentiseringsformuläret ut som följande exempel:

![Skapa ett Jenkins-autentiseringsobjekt med tjänstens huvudinformation](media/jenkins-continuous-deployment/acr-credentials.png)

Klicka på **OK** och gå tillbaka till Jenkins-portalen.

## <a name="create-a-jenkins-project"></a>Skapa ett Jenkins-projekt

På startsidan för Jenkins-portalen väljer du **Nytt objekt** till vänster:

1. Ange *azure-vote* som jobbnamn. Välj **Freestyle-projekt**och välj sedan **OK**
1. Under avsnittet **Allmänt** väljer du **GitHub-projektet** och anger url:en för tidig repo, till exempel *\/https: /github.com/\<your-github-account\>/azure-voting-app-redis*
1. Under avsnittet **Källkodshantering** väljer du **Git**, anger url:en för tidig *git-url för git,* till exempel *https:\//github.com/\<ditt-github-konto\>/azure-voting-app-redis.git*

1. Under avsnittet **Byggutlösare** väljer du **GitHub-krokutlösare för GITscm-avsökning**
1. Under **Skapa miljö**väljer du Använd **hemliga texter eller filer**
1. Under **Bindningar**väljer du **Lägg till** > **användarnamn och lösenord (avgränsat)**
   - Ange `ACR_ID` för **användarnamnsvariabeln**och `ACR_PASSWORD` för **lösenordsvariabeln**

     ![Jenkins bindningar](media/jenkins-continuous-deployment/bindings.png)

1. Välj att lägga till ett **byggsteg** av typen **Kör skal** och använd följande text. Det här skriptet skapar en ny behållaravbildning och skickar den till ACR-registret.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Lägg till ytterligare **ett byggsteg** av typen **Kör skal** och använd följande text. Det här skriptet uppdaterar programdistributionen i AKS med den nya behållaravbildningen från ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. När du är klar klickar du på **Spara**.

## <a name="test-the-jenkins-build"></a>Testa Jenkins-versionen

Innan du automatiserar jobbet baserat på GitHub-åtaganden testar du först Jenkins-versionen manuellt. Den här manuella versionen verifierar att jobbet har konfigurerats korrekt, att kubernetes-autentiseringsfilen är på plats och att autentiseringen med ACR fungerar.

Välj **Bygg nu**på menyn till vänster i projektet .

![Jenkins test bygga](media/jenkins-continuous-deployment/test-build.png)

Den första versionen tar en minut eller två som Docker bildlager dras ner till Jenkins servern. Efterföljande versioner kan använda de cachelagrade bildlagren för att förbättra byggtiderna.

Under byggprocessen klonas GitHub-databasen till Jenkins byggserver. En ny behållaravbildning byggs och skjuts till ACR-registret. Slutligen uppdateras Azure-omröstningsprogrammet som körs i AKS-klustret för att använda den nya avbildningen. Eftersom inga ändringar har gjorts i programkoden ändras inte programmet om du visar exempelappen i en webbläsare.

När byggjobbet är klart klickar du på **bygg #1** under bygghistorik. Välj **Konsolutdata** och visa utdata från byggprocessen. Den sista raden bör indikera en lyckad version.

## <a name="create-a-github-webhook"></a>Skapa en GitHub-webbkrok

Med en lyckad manuell build komplett, nu integrera GitHub i Jenkins bygga. En webhook kan användas för att köra Jenkins-byggjobbet varje gång en kod commit görs i GitHub. Så här skapar du GitHub-webbkroken:

1. Bläddra till din kluvna GitHub-databas i en webbläsare.
1. Välj **Inställningar** och sedan **Webhooks** på vänster sida.
1. Välj att **lägga till webhook**. För *nyttolast-URL:en*anger du `http://<publicIp:8080>/github-webhook/`, var `<publicIp>` är JENKINS-serverns IP-adress. Se till att inkludera den avslutande /. Lämna de andra standardvärdena för innehållstyp och utlösa *på push-händelser.*
1. Välj **Lägg till webhook**.

    ![Skapa en GitHub webhook för Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testa hela CI/CD-pipelinen

Nu kan du testa hela CI / CD pipeline. När du skickar en kod commit till GitHub, följande steg hända:

1. GitHub webhook når ut till Jenkins.
1. Jenkins startar byggjobbet och hämtar det senaste kodsantagandet från GitHub.
1. En Docker-version startas med den uppdaterade koden och den nya behållaravbildningen taggas med det senaste byggnumret.
1. Den här nya behållaravbildningen överförs till Azure Container Registry.
1. Ditt program som distribueras till Azure Kubernetes Service-uppdateringar med den senaste behållaravbildningen från Azure Container Registry-registret.

Öppna det klonade programmet med en kodredigerare på din utvecklingsmaskin. Öppna filen som heter **config_file.cfg**under katalogen */azure-vote/azure-vote* . Uppdatera röstvärdena i den här filen till något annat än katter och hundar, som visas i följande exempel:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

När du är uppdaterad sparar du filen, genomför ändringarna och skickar dessa till din gaffel i GitHub-databasen. GitHub webhook utlöser ett nytt byggjobb i Jenkins. Övervaka byggprocessen i Jenkins webbinstrumentpanel. Det tar några sekunder att hämta den senaste koden, skapa och skicka den uppdaterade avbildningen och distribuera det uppdaterade programmet i AKS.

När versionen är klar uppdaterar du webbläsaren i exempelprogrammet för Azure-omröstning. Dina ändringar visas, som visas i följande exempel:

![Exempel på Azure-omröstning i AKS som uppdateras av Jenkins-byggjobbet](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/jenkins)