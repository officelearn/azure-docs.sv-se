---
title: Använda plugin-programmet Azure Dev Spaces för Jenkins med Azure Kubernetes-tjänsten
description: Lär dig hur du använder plugin-programmet Azure Dev Spaces i en pipeline för kontinuerlig integrering.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037022"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Självstudiekurs: Använda plugin-programmet Azure Dev Spaces för Jenkins med Azure Kubernetes-tjänsten 

Med Azure Dev Spaces kan du testa och iterativt utveckla ditt mikrotjänstprogram som körs i Azure Kubernetes Service (AKS) utan att behöva replikera eller håna beroenden. Azure Dev Spaces-plugin-programmet för Jenkins hjälper dig att använda Dev Spaces i din pipeline för kontinuerlig integrering och leverans (CI/CD).

Den här självstudien använder även Azure Container Registry (ACR). ACR lagrar bilder och en ACR-uppgift bygger Docker- och Helm-artefakter. Om du använder ACR- och ACR-uppgift för artefaktgenerering tas behovet av att installera ytterligare programvara, till exempel Docker, på Jenkins-servern. 

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa ett AZURE Dev Spaces-aktiverat AKS-kluster
> * Distribuera ett program med flera tjänster till AKS
> * Förbered Jenkins-servern
> * Använd plugin-programmet Azure Dev Spaces i en Jenkins-pipeline för att förhandsgranska kodändringar innan de slås samman till projektet

Den här självstudien förutsätter mellanliggande kunskap om grundläggande Azure-tjänster, AKS, ACR, Azure Dev Spaces, [Jenkins-pipelines](https://jenkins.io/doc/book/pipeline/) och plugin-program och GitHub. Grundläggande förtrogenhet med stödverktyg som kubectl och Helm är till hjälp.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Ett GitHub-konto. Om du inte har ett GitHub-konto skapar du ett [kostnadsfritt konto](https://github.com/) innan du börjar.

* [Visual Studio-kod](https://code.visualstudio.com/download) med [Azure Dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installerat.

* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.43 eller senare.

* En Jenkins-huvudserver. Om du inte redan har en Jenkins-hanterare distribuerar du [Jenkins](https://aka.ms/jenkins-on-azure) på Azure genom att följa stegen i den här [snabbstarten](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Jenkins-servern måste ha både Helm och kubectl installerade och tillgängliga för Jenkins-kontot, vilket förklaras senare i den här självstudien.

* VS-kod, VS-kodterminalen eller WSL och Bash. 


## <a name="create-azure-resources"></a>Skapa Azure-resurser

I det här avsnittet skapar du Azure-resurser:

* En resursgrupp som innehåller alla Azure-resurser för den här självstudien.
* Ett [AKS-kluster (Azure Kubernetes Service).](https://docs.microsoft.com/azure/aks/)
* Ett [Azure-behållarregister](https://docs.microsoft.com/azure/container-registry/) (ACR) för att skapa (med ACR-uppgifter) och lagra Docker-avbildningar.

1. Skapa en resursgrupp.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Skapa ett AKS-kluster. Skapa AKS-klustret i en [region som stöder Dev Spaces](../dev-spaces/about.md#supported-regions-and-configurations).

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Konfigurera AKS så att de ska använda dev spaces.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Det här steget `azds` installerar CLI-tillägget.

4. Skapa ett behållarregister.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Distribuera exempelappar till AKS-klustret

I det här avsnittet konfigurerar du ett utvecklingsutrymme och distribuerar ett exempelprogram till AKS-klustret som du skapade i det sista avsnittet. Ansökan består av två delar, *webfrontend* och *mywebapi*. Båda komponenterna distribueras i ett utvecklingsutrymme. Senare i den här självstudien skickar du en pull-begäran mot mywebapi för att utlösa CI-pipelinen i Jenkins.

Mer information om hur du använder Azure Dev Spaces och utveckling av flera tjänster med Azure Dev Spaces finns i [Komma igång med Azure Dev Spaces med Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)och utveckling av flera tjänster med Azure Dev [Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Dessa tutorials ger ytterligare bakgrundsinformation som inte ingår här.

1. Ladda https://github.com/Azure/dev-spaces ner reporäntan från GitHub.

2. Öppna `samples/java/getting-started/webfrontend` mappen i VS-kod. (Du kan ignorera eventuella standarduppmaningar om att lägga till felsökningstillgångar eller återställa projektet.)

3. Uppdatera `/src/main/java/com/ms/sample/webfrontend/Application.java` så att den ser ut så här:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Klicka på **Visa** och sedan **Terminal** för att öppna den integrerade terminalen i VS-kod.

5. Kör `azds prep` kommandot för att förbereda programmet så att det körs i ett utvecklingsutrymme. Det här kommandot `dev-spaces/samples/java/getting-started/webfrontend` måste köras från för att förbereda programmet korrekt:

    ```bash
    azds prep --public
    ```

    Dev Spaces CLI:s `azds prep` kommando genererar Docker- och Kubernetes-tillgångar med standardinställningar. Dessa filer kvarstår under projektets livstid och de kan anpassas:

    * `./Dockerfile`och `./Dockerfile.develop` beskriv appens behållaravbildning och hur källkoden byggs och körs i behållaren.
    * Ett [Helm-diagram](https://helm.sh/docs/topics/charts/) under `./charts/webfrontend` beskriver hur du distribuerar containern till Kubernetes.
    * `./azds.yaml`är konfigurationsfilen för Azure Dev Spaces.

    Mer information finns i [Så här fungerar Azure Dev Spaces och är konfigurerat](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Skapa och kör programmet i `azds up` AKS med kommandot:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Sök igenom konsolutdata efter information om `up` url:en som skapades av kommandot. Den kommer att vara i formen:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Öppna webbadressen i ett webbläsarfönster och du bör se webbappen. När containern körs strömmas `stdout`- och `stderr`-utdata till terminalfönstret.

8. Konfigurera och distribuera *sedan mywebapi:*

    1. Ändra katalogen till`dev-spaces/samples/java/getting-started/mywebapi`

    2. Kör

        ```bash
        azds prep
        ```

    3. Kör

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet förbereder du Jenkins-servern för att köra exempelpipelinen CI.

* Installera plugin-program
* Installera Helm och Kubernetes CLI
* Lägga till autentiseringsuppgifter

### <a name="install-plug-ins"></a>Installera plugin-program

1. Logga in på Jenkins-servern. Välj **Hantera Jenkins > Hantera plugins**.
2. På fliken **Tillgänglig** väljer du följande plugin-program:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry Tasks](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Miljö Injektor](https://plugins.jenkins.io/envinject)
    * [GitHub-integrering](https://plugins.jenkins.io/github-pullrequest)

    Om dessa plugin-program inte visas i listan kontrollerar du fliken **Installerad** för att se om de redan är installerade.

3. Om du vill installera plugin-programmen väljer du **Hämta nu och installerar efter omstart**.

4. Starta om Jenkins-servern för att slutföra installationen.

### <a name="install-helm-and-kubectl"></a>Installera Helm och kubectl

Exempelpipelinen använder Helm och kubectl för att distribuera till utvecklingsutrymmet. När Jenkins installeras skapas ett administratörskonto med namnet *jenkins*. Både Helm och kubectl måste vara tillgängliga för jenkins-användaren.

1. Gör en SSH-anslutning till Jenkins-mästaren. 

2. Växla till `jenkins` användaren:
    ```bash
    sudo su jenkins
    ```

3. Installera Helm CLI. Mer information finns i [Installera Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Installera kubectl. Mer information finns i [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Lägga till autentiseringsuppgifter i Jenkins

1. Jenkins behöver ett Azure-tjänsthuvudnamn för att autentisera och komma åt Azure-resurser. Om du vill skapa tjänstens huvudnamn läser du avsnittet [Skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) i självstudien Distribuera till Azure App Service. Var noga med att spara `create-for-rbac` en kopia av utdata från eftersom du behöver den informationen för att slutföra nästa steg. Utdata ser ut ungefär så här:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Lägg till en autentiseringstyp för *Microsoft Azure Service Principal* i Jenkins med hjälp av tjänstens huvudinformation från föregående steg. Namnen i skärmdumpen nedan motsvarar `create-for-rbac`utdata från .

    **ID-fältet** är Jenkins-referensnamnet för tjänstens huvudnamn. I exemplet används `displayName` värdet (i `xxxxxxxjenkinssp`den här instansen), men du kan använda vilken text du vill. Det här autentiseringsnamnet är värdet för AZURE_CRED_ID miljövariabeln i nästa avsnitt.

    ![Lägga till autentiseringsuppgifter för tjänstens huvudnamn i Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Beskrivningen** är valfri. Mer detaljerade instruktioner finns i Avsnittet [Lägg till tjänstens huvudnamn i](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) jenkins-avsnittet i självstudien Distribuera till Azure App Service. 



3. Om du vill visa dina ACR-autentiseringsuppgifter kör du det här kommandot:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Gör en kopia av JSON-utgången, som ska se ut ungefär så här:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Lägg till ett användarnamn med lösenordsautentiseringstyp i Jenkins. *Username with password* **Användarnamnet** är användarnamnet från det senaste steget, i det här exemplet `acr01`. **Lösenordet** är värdet för det första lösenordet `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`i det här exemplet . **ID:et** för den här autentiseringsen är värdet för ACR_CRED_ID.

5. Konfigurera en AKS-autentiseringsuppgifter. Lägg till en *Kubernetes-konfiguration (kubeconfig)* autentiseringstyp i Jenkins (använd alternativet "Ange direkt"). Om du vill hämta åtkomstautentiseringsuppgifterna för AKS-klustret kör du följande kommando:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   ID-autentiseringsuppgifterna är värdet för KUBE_CONFIG_ID i nästa avsnitt. **ID**

## <a name="create-a-pipeline"></a>Skapa en pipeline

Det valda scenariot för exempelpipelinen baseras på ett verkligt mönster: En pull-begäran utlöser en CI-pipeline som bygger och distribuerar sedan de föreslagna ändringarna till ett Azure-dev-utrymme för testning och granskning. Beroende på resultatet av granskningen slås ändringarna antingen samman och distribueras till AKS eller ignoreras. Slutligen tas utvecklingsutrymmet bort.

Jenkins pipeline-konfiguration och Jenkinsfile definierar stegen i CI-pipelinen. Det här flödesschemat visar pipeline-stadier och beslutspunkter som definieras av Jenkinsfile:

![Jenkins rörledningsflöde](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Hämta en modifierad version av [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi) *mywebapi-projektet* från . Det här projektet innehåller flera filer som behövs för att skapa en pipeline, inklusive *Jenkinsfile,* *Dockerfiles*och Helm-diagrammet.

2. Logga in på Jenkins. Välj **Lägg till objekt**på menyn till vänster .

3. Välj **Pipeline**och ange sedan ett namn i rutan **Ange ett objektnamn.** Välj **OK**och sedan öppnas skärmen för pipelinekonfiguration automatiskt.

4. Markera Förbereda en **miljö för körningen**på fliken **Allmänt** . 

5. Kontrollera **Behåll Jenkins miljövariabler** och **behåll Jenkins byggvariabler**.

6. Ange följande miljövariabler i rutan **Egenskaperinnehåll:**

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Med hjälp av exempelvärdena som anges i föregående avsnitt bör listan över miljövariabler se ut ungefär så här:

    ![Jenkins pipeline-miljövariabler](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Välj **Pipeline-skript från SCM** i **Pipeline > Definition**.
8. I **SCM**väljer du **Git** och anger sedan webbadressen för repo.
9. Ange i Branch `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}` **Specifier**.
10. Fyll i SCM-repo-URL:en och skriptsökvägen "Jenkinsfile".
11. **Lätt kassan** bör kontrolleras.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Skapa en pull-begäran för att utlösa pipelinen

För att slutföra steg 3 i det här avsnittet måste du kommentera en del av Jenkinsfile, annars får du ett 404-fel när du försöker visa de nya och gamla versionerna sida vid sida. När du väljer att slå samman PR:en tas som standard den tidigare delade versionen av mywebapi bort och ersättas av den nya versionen. Gör följande ändring i Jenkinsfile innan du slutför steg 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Gör en `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`ändring på och skapa sedan en pull-begäran. Ett exempel:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Logga in på Jenkins och välj pipeline-namnet och välj sedan **Bygg nu**. 

    Du kan också ställa in en *webhook* för att automatiskt utlösa Jenkins-pipelinen. När en pull-begäran anges utfärdar GitHub ett POST till Jenkins, vilket utlöser pipelinen. Mer information om hur du konfigurerar en webhook finns i [Ansluta Jenkins till GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Jämför ändringar med den aktuella delade versionen:

    1. Öppna webbläsaren och navigera till `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`den delade versionen . TEST_ENDPOINT innehåller webbadressen.

    2. Öppna en annan flik och ange sedan URL:en för DEV-utrymme för PR. Det kommer att `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`likna . Länken i **Bygghistorik > <build#> > Console Output** för Jenkins-jobbet. Sök efter `aksapp`sidan eller bara om du vill `azdsprefix`se prefixet .

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Konstruera URL:en till det underordnade utvecklingsutrymmet

När du arkiverar en pull-begäran skapar Jenkins ett underordnat utvecklingsutrymme baserat på teamets delade utvecklingsutrymme och kör koden från din pull-begäran i det underordnade dev-utrymmet. URL:en till det underordnade `http://$env.azdsprefix.<test_endpoint>`utvecklingsutrymmet har formen . 

**$env.azdsprefix** anges under pipelinekörningen av azure dev spaces-plugin-programmet av **devSpacesCreate:**

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

Det `test_endpoint` är URL:en till den webfrontend-app som du tidigare distribuerat med `azds up`i Distribuera [exempelappar till AKS-klustret, steg 7](#test_endpoint). Värdet för `$env.TEST_ENDPOINT` anges i pipeline-konfigurationen. 

Följande kodavsnitt visar hur den underordnade url:en `smoketest` för dev-utrymme används i scenen. Koden kontrollerar om det underordnade TEST_ENDPOINT är tillgängligt och hämtas i så fall hälsningstexten till stdout:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med exempelprogrammet rensar du Azure-resurser genom att ta bort resursgruppen:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [CI/CD med Jenkins på Azure](jenkins-continuous-deployment.md)
