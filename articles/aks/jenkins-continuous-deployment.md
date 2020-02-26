---
title: Självstudie – distribuera från GitHub till Azure Kubernetes service (AKS) med Jenkins
description: Konfigurera Jenkins för kontinuerlig integrering (CI) från GitHub och kontinuerlig distribution (CD) till Azure Kubernetes service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: fd754b539a9b0ba3d47ddd2228365dbd09d6e6df
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595492"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Självstudie: Distribuera från GitHub till Azure Kubernetes service (AKS) med Jenkins kontinuerlig integrering och distribution

Den här självstudien distribuerar en exempel-app från GitHub till ett [Azure Kubernetes service-kluster (AKS)](/azure/aks/intro-kubernetes) genom att konfigurera kontinuerlig integrering (CI) och kontinuerlig distribution (CD) i Jenkins. När du uppdaterar din app genom att överföra incheckningar till GitHub kör Jenkins automatiskt en ny behållar version, push-överför behållar avbildningar till Azure Container Registry (ACR) och kör sedan appen i AKS. 

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Distribuera ett exempel på en Azure-röstning till ett AKS-kluster.
> * Skapa ett Basic Jenkins-projekt.
> * Konfigurera autentiseringsuppgifter för Jenkins att interagera med ACR.
> * Skapa ett Jenkins build-jobb och en GitHub-webhook för automatiska versioner.
> * Testa CI/CD-pipeline för att uppdatera ett program i AKS baserat på GitHub kod skrivningar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande objekt:

- Grundläggande förståelse för Kubernetes, git, CI/CD och behållar avbildningar

- Ett [AKS-kluster][aks-quickstart] och `kubectl` konfigurerat med [AKS-autentiseringsuppgifter för kluster][aks-credentials]

- Ett [Azure Container Registry-register (ACR)][acr-quickstart], ACR-inloggnings serverns namn och det AKS-kluster som har kon figurer ATS för att [autentisera med ACR-registret][acr-authentication]

- Azure CLI-versionen 2.0.46 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

- [Docker installerat][docker-install] i utvecklings systemet

- Ett GitHub-konto, [GitHub-personlig åtkomsttoken][git-access-token]och git-klient installerad i utvecklings systemet

- Om du anger din egen Jenkins-instans i stället för att använda det här exemplet för att distribuera Jenkins, behöver din Jenkins-instans [Docker installeras och konfigureras][docker-install] och [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Förbered din app

I den här artikeln använder du ett exempel på ett Azure-röstnings program som innehåller ett webb gränssnitt som finns i en eller flera poddar, och en andra Pod som är värd för Redis för tillfällig data lagring. Innan du integrerar Jenkins och AKS för automatiserade distributioner måste du först förbereda och distribuera Azures röst program manuellt till ditt AKS-kluster. Den här manuella distributionen är version ett av programmet och låter dig se hur programmet fungerar.

> [!NOTE]
> I Azures exempel Azure-röstning används en Linux-Pod som är schemalagd att köras på en Linux-nod. Det flöde som beskrivs i den här artikeln fungerar också för en Windows Server-Pod som är schemalagd på en Windows Server-nod.

Delar in följande GitHub-lagringsplats för exempel programmet – [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Om du vill förgrena lagringsplatsen till ditt eget GitHub-konto väljer du knappen **Fork** (Förgrening) i det övre högra hörnet.

Klona gaffel till utvecklings systemet. Se till att du använder URL: en för din förgrening vid kloning av denna lagrings platsen:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Ändra till katalogen för din klonade förgrening:

```console
cd azure-voting-app-redis
```

Om du vill skapa de behållar avbildningar som behövs för exempel programmet använder du *Docker-yaml-* filen med `docker-compose`:

```console
docker-compose up -d
```

De nödvändiga bas avbildningarna hämtas och program behållarna skapas. Sedan kan du använda kommandot [Docker images][docker-images] för att se den skapade avbildningen. Tre avbildningar har hämtats eller skapats. `azure-vote-front`-avbildningen innehåller programmet och använder `nginx-flask`-avbildningen som bas. `redis`-avbildningen används för att starta en Redis-instans:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Innan du kan push-överföra en *Azure-röst-front* behållar avbildning till ACR hämtar du ACR-inloggnings servern med kommandot [AZ ACR List][az-acr-list] . I följande exempel hämtas inloggnings Server adressen för ACR för ett register i resurs gruppen med namnet *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd kommandot [Docker tag][docker-tag] för att tagga avbildningen med ACR-inloggnings Server namnet och ett versions nummer för `v1`. Ange ett eget `<acrLoginServer>` namn som hämtades i föregående steg:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Skicka slutligen den *Azure-röstbaserade* avbildningen till ACR-registret. Ersätt `<acrLoginServer>` med inloggnings Server namnet för ditt eget ACR-register, till exempel `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Distribuera exempel programmet till AKS

Om du vill distribuera exempel programmet till ditt AKS-kluster kan du använda Kubernetes-manifest filen i roten i Azures lagrings platsen för Azure-röstning. Öppna manifest filen *Azure-rösta-all-in-One-Redis. yaml* med ett redigerings program, till exempel `vi`. Ersätt `microsoft` med namnet på din ACR-inloggningsserver. Det här värdet finns på rad **47** i manifest filen:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Använd sedan kommandot [kubectl Apply][kubectl-apply] för att distribuera programmet till ditt AKS-kluster:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

En Kubernetes för belastnings utjämning skapas för att exponera programmet på Internet. Den här processen kan ta ett par minuter. Använd kommandot [kubectl get service][kubectl-get] med argumentet `--watch` för att övervaka förloppet för distributionen av belastnings utjämning. När *EXTERNAL-IP*-adressen har ändrats från *pending* (väntar) till en *IP-adress* använder du `Control + C` för att stoppa kubectl-övervakningsprocessen.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Om du vill se hur programmet fungerar öppnar du en webbläsare till den externa IP-adressen för din tjänst. Röst programmet för Azure visas, som du ser i följande exempel:

![Azure exempel röstnings program som körs i AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Distribuera Jenkins till en virtuell Azure-dator

För att snabbt distribuera Jenkins för användning i den här artikeln kan du använda följande skript för att distribuera en virtuell Azure-dator, konfigurera nätverks åtkomst och slutföra en grundläggande installation av Jenkins. För autentisering mellan Jenkins och AKS-klustret kopierar skriptet Kubernetes-konfigurationsfilen från utvecklings systemet till Jenkins-systemet.

> [!WARNING]
> Det här exempel skriptet är i demonstrations syfte för att snabbt etablera en Jenkins-miljö som körs på en virtuell Azure-dator. Det använder Azures anpassade skript tillägg för att konfigurera en virtuell dator och sedan Visa de autentiseringsuppgifter som krävs. Din *~/.Kube/config* kopieras till den virtuella Jenkins-datorn.

Kör följande kommandon för att ladda ned och köra skriptet. Du bör granska innehållet i alla skript innan du kör det [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Det tar några minuter att skapa den virtuella datorn och distribuera de komponenter som krävs för Docker och Jenkins. När skriptet har slutförts, matar den ut en adress för Jenkins-servern och en nyckel för att låsa upp instrument panelen, som visas i följande exempel på utdata:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Öppna en webbläsare till den URL som visas och ange upplåsnings nyckeln. Slutför Jenkins-konfigurationen genom att följa anvisningarna på skärmen:

- Välj **Installera föreslagna plugin** -program
- Skapa den första administratörsanvändaren. Ange ett användar namn, till exempel *azureuser*, och ange sedan ditt eget säkra lösen ord. Skriv slutligen ett fullständigt namn och e-postadress.
- Välj **Spara och Slutför**
- När Jenkins är klar, väljer du **Börja använda Jenkins**
    - Om en tom sida visas i din webbläsare när du börjar använda Jenkins, starta om Jenkins-tjänsten. För att starta om tjänsten, SSH till den offentliga IP-adressen för din Jenkins-instans och typ `sudo service jenkins restart`. Uppdatera din webbläsare när tjänsten har startats om.
- Logga in på Jenkins med det användar namn och lösen ord som du skapade i installations processen.

## <a name="create-a-jenkins-environment-variable"></a>Skapa en Jenkins-miljö variabel

En Jenkins-miljövariabel används för att lagra ACR-inloggnings Server namnet. Den här variabeln refereras till i Jenkins build-jobbet. Utför följande steg för att skapa den här miljövariabeln:

- På vänster sida av Jenkins-portalen väljer du **Hantera Jenkins** > **Konfigurera system**
- Under **globala egenskaper**väljer du **miljövariabler**. Lägg till en variabel med namnet `ACR_LOGINSERVER` och värdet för din ACR-inloggnings Server.

    ![Jenkins-miljövariabler](media/aks-jenkins/env-variables.png)

- När du är klar klickar du på **Spara** längst ned på konfigurations sidan för Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Skapa en Jenkins-autentiseringsuppgift för ACR

För att Jenkins ska kunna bygga och sedan push-överföra uppdaterade behållar avbildningar till ACR måste du ange autentiseringsuppgifter för ACR. Den här autentiseringen kan använda Azure Active Directory tjänstens huvud namn. I för hands krav konfigurerade du tjänstens huvud namn för ditt AKS-kluster med *läsar* behörighet till ACR-registret. Med dessa behörigheter kan AKS-klustret *Hämta* avbildningar från ACR-registret. Under CI/CD-processen skapar Jenkins nya behållar avbildningar baserat på program uppdateringar och måste sedan *push* -överföra avbildningarna till ACR-registret. För att åtskilja roller och behörigheter konfigurerar du nu ett tjänst huvud namn för Jenkins med *deltagar* behörighet till ACR-registret.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Skapa ett huvud namn för tjänsten för Jenkins för att använda ACR

Börja med att skapa ett huvud namn för tjänsten med hjälp av kommandot [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

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

Anteckna *appId* och *lösen ordet* som visas i dina utdata. Dessa värden används i följande steg för att konfigurera resursen för autentiseringsuppgifter i Jenkins.

Hämta resurs-ID för ACR-registret med kommandot [AZ ACR show][az-acr-show] och lagra det som en variabel. Ange resurs gruppens namn och ACR namn:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Skapa nu en roll tilldelning för att tilldela tjänstens huvud *deltagar* rättigheter till ACR-registret. I följande exempel anger du ditt eget *appId* som visas i kommandot utdata ett tidigare för att skapa tjänstens huvud namn:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Skapa en resurs för autentiseringsuppgifter i Jenkins för ACR-tjänstens huvud namn

När roll tilldelningen har skapats i Azure lagrar nu dina ACR-autentiseringsuppgifter i ett Jenkins-objekt för autentiseringsuppgifter. Autentiseringsuppgifterna refereras till i Jenkins build-jobbet.

Tillbaka till vänster i Jenkins-portalen klickar du på **autentiseringsuppgifter** > **Jenkins** > **globala autentiseringsuppgifter (obegränsad)**  > **Lägg till autentiseringsuppgifter**

Se till att autentiseringstypen är ett **användar namn med lösen ordet** och ange följande objekt:

- **Användar namn** – *appId* för tjänstens huvud namn som skapats för autentisering med ACR-registret.
- **Password** – *lösen ordet* för tjänstens huvud namn som skapats för autentisering med ACR-registret.
- **ID – ID** för autentiseringsuppgift, till exempel *ACR-* credentials

När du är klar ser formuläret autentiseringsuppgifter ut som i följande exempel:

![Skapa ett Jenkins-objekt för autentiseringsuppgifter med tjänstens huvud namns information](media/aks-jenkins/acr-credentials.png)

Klicka på **OK** och gå tillbaka till Jenkins-portalen.

## <a name="create-a-jenkins-project"></a>Skapa ett Jenkins-projekt

På Start sidan i Jenkins-portalen väljer du **nytt objekt** på den vänstra sidan:

1. Ange *Azure-rösta* som jobbnamn. Välj **Freestyle-projekt**och välj sedan **OK**
1. Under avsnittet **Allmänt** väljer du **GitHub-projekt** och anger din förgrenade lagrings platsen-URL, t. ex. *https:\//GitHub.com/\<ditt-GitHub-Account\>/Azure-Voting-app-Redis*
1. Under avsnittet **käll kods hantering** väljer du **git**, anger din förgrenade lagrings platsen *. git* -URL, t. ex. *https:\//GitHub.com/\<ditt GitHub-konto\>/Azure-Voting-app-Redis.git*

1. Under avsnittet **build-utlösare** väljer du **GitHub Hook-utlösare för gitscm polling (-avsökning**
1. Under **build-miljö**väljer du **Använd hemliga texter eller filer**
1. Under **bindningar**väljer du **Lägg till** > **användar namn och lösen ord (separerade)**
   - Ange `ACR_ID` för **variabeln username**och `ACR_PASSWORD` för **variabeln password**

     ![Jenkins-bindningar](media/aks-jenkins/bindings.png)

1. Välj att lägga till ett **build-steg** av typen **execute Shell** och Använd följande text. Det här skriptet skapar en ny behållar avbildning och skickar den till ACR-registret.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Lägg till ett annat **build-steg** av typen **execute Shell** och Använd följande text. Det här skriptet uppdaterar program distributionen i AKS med den nya behållar avbildningen från ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. När du är klar klickar du på **Spara**.

## <a name="test-the-jenkins-build"></a>Testa Jenkins-versionen

Innan du automatiserar jobbet baserat på GitHub incheckningar ska du först testa Jenkins-versionen manuellt. Den här manuella versionen verifierar att jobbet har kon figurer ATS korrekt, att rätt Kubernetes-autentiseringsnyckel är på plats och att autentiseringen med ACR fungerar.

På den vänstra menyn i projektet väljer du **Bygg nu**.

![Jenkins test version](media/aks-jenkins/test-build.png)

Den första versionen tar en minut eller två eftersom Docker-avbildnings lagren hämtas till Jenkins-servern. Efterföljande versioner kan använda cachelagrade bild lager för att förbättra Bygg tiderna.

Under skapande processen klonas GitHub-lagringsplatsen till Jenkins-build-servern. En ny behållar avbildning skapas och skickas till ACR-registret. Slutligen uppdateras Azures röst program som körs på AKS-klustret för att använda den nya avbildningen. Eftersom inga ändringar har gjorts i program koden, ändras inte programmet om du visar exempel appen i en webbläsare.

När build-jobbet är klart klickar du på **build #1** under versions historik. Välj **konsol utdata** och visa utdata från bygg processen. Den sista raden ska visa en lyckad version.

## <a name="create-a-github-webhook"></a>Skapa en GitHub-webhook

När en lyckad manuell version är klar integrerar du GitHub i Jenkins-versionen. En webhook kan användas för att köra Jenkins build-jobbet varje gången ett kod genomförande görs i GitHub. Utför följande steg för att skapa GitHub-webhooken:

1. Bläddra till din förgrenade GitHub-lagringsplats i en webbläsare.
1. Välj **Inställningar** och sedan **Webhooks** på vänster sida.
1. Välj att **lägga till webhook**. För *nytto Last-URL: en*anger `http://<publicIp:8080>/github-webhook/`, där `<publicIp>` är IP-adressen för Jenkins-servern. Se till att ta med efterföljande/. Lämna de andra standardvärdena för innehålls typ och Utlös vid *push* -händelser.
1. Välj **Lägg till webhook**.

    ![Skapa en GitHub-webhook för Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testa hela CI/CD-pipeline

Nu kan du testa hela CI/CD-pipeline. När du push-överför en kod till GitHub sker följande steg:

1. GitHub-webbhooken når Jenkins.
1. Jenkins startar Bygg jobbet och hämtar den senaste koden som bekräftas från GitHub.
1. En Docker-version startas med den uppdaterade koden och den nya behållar avbildningen märks med det senaste versions numret.
1. Den här nya behållar avbildningen skickas till Azure Container Registry.
1. Ditt program distribueras till Azure Kubernetes service-uppdateringar med den senaste behållar avbildningen från Azure Container Registry-registret.

Öppna det klonade programmet med en kod redigerare på din utvecklings dator. Öppna filen med namnet **config_file. cfg**under katalogen */Azure-Vote/Azure-Vote* . Uppdatera röst värden i den här filen till något annat än katter och hundar, som du ser i följande exempel:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

När du har uppdaterat sparar du filen, genomför ändringarna och skickar dem till din förgrening av GitHub-lagringsplatsen. GitHub-webhook utlöser ett nytt build-jobb i Jenkins. I Jenkins-webbinstrumentpanelen övervakar du Bygg processen. Det tar några sekunder att hämta den senaste koden, skapa och pusha den uppdaterade avbildningen och distribuera det uppdaterade programmet i AKS.

När build-versionen har slutförts uppdaterar du webbläsaren för Azures exempel programmet Azure-röstning. Ändringarna visas, som du ser i följande exempel:

![Exempel på Azure-röstning i AKS som uppdaterats av Jenkins-build-jobbet](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Jenkins som en del av en CI/CD-lösning. AKS kan integreras med andra CI/CD-lösningar och automatiserings verktyg, till exempel [Azure DevOps-projektet][azure-devops] eller [skapa ett AKS-kluster med Ansible][aks-ansible].

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
[acr-authentication]: cluster-container-registry-integration.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
