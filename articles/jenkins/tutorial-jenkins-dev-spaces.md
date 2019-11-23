---
title: Använda Azure dev Spaces-plugin-programmet för Jenkins med Azure Kubernetes-tjänsten
description: Lär dig hur du använder plugin-programmet för Azure dev Spaces i en pipeline för kontinuerlig integrering.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 9dba0307db8ebbf07422fd770ea336b2abc031bd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209670"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Självstudie: använda Azure dev Spaces-plugin-programmet för Jenkins med Azure Kubernetes-tjänsten 

Med Azure dev Spaces kan du testa och iterativt utveckla ditt mikroservice-program som körs i Azure Kubernetes service (AKS) utan att behöva replikera eller modellera beroenden. Med plugin-programmet för Azure dev Spaces för Jenkins kan du använda dev-utrymmen i pipeline för kontinuerlig integrering och leverans (CI/CD).

I den här självstudien används även Azure Container Registry (ACR). ACR lagrar bilder och en ACR-uppgift skapar Docker-och Helm-artefakter. Om du använder ACR-och ACR-aktiviteten för artefakt generering tar du bort behovet av att installera ytterligare program vara, till exempel Docker, på Jenkins-servern. 

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure dev Spaces-aktiverat AKS-kluster
> * Distribuera ett program med flera tjänster till AKS
> * Förbered din Jenkins-Server
> * Använd plugin-programmet för Azure dev Spaces i en Jenkins-pipeline för att förhandsgranska kod ändringar innan du sammanfogar dem i projektet

Den här självstudien förutsätter mellanliggande kunskaper om grundläggande Azure-tjänster, AKS, ACR, Azure dev Spaces, Jenkins [pipelines](https://jenkins.io/doc/book/pipeline/) och plugin-program och GitHub. Grundläggande kunskaper om support verktyg som kubectl och Helm är till hjälp.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Ett GitHub-konto. Om du inte har ett GitHub-konto kan du skapa ett [kostnads fritt konto](https://github.com/) innan du börjar.

* [Visual Studio Code](https://code.visualstudio.com/download) med tillägget [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installerat.

* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.43 eller högre.

* En Jenkins-huvudserver. Om du inte redan har en Jenkins-Master distribuerar du [Jenkins](https://aka.ms/jenkins-on-azure) på Azure genom att följa stegen i den här [snabb](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)starten. 

* Jenkins-servern måste ha både Helm och kubectl installerade och tillgängliga för Jenkins-kontot, vilket förklaras senare i den här självstudien.

* VS Code, VS Code terminal eller WSL och bash. 


## <a name="create-azure-resources"></a>Skapa Azure-resurser

I det här avsnittet skapar du Azure-resurser:

* En resurs grupp som innehåller alla Azure-resurser i den här självstudien.
* Ett [Azure Kubernetes service](https://docs.microsoft.com/azure/aks/) -kluster (AKS).
* Ett [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR) för att skapa (med ACR-aktiviteter) och lagra Docker-avbildningar.

1. Skapa en resursgrupp.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Skapa ett AKS-kluster. Skapa AKS-klustret i en [region som har stöd för dev Spaces](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Konfigurera AKS för att använda dev Spaces.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Det här steget installerar `azds` CLI-tillägget.

4. Skapa ett behållar register.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Distribuera exempel appar till AKS-klustret

I det här avsnittet skapar du ett dev-utrymme och distribuerar ett exempel program till det AKS-kluster som du skapade i det sista avsnittet. Programmet består av två delar, *webfrontend* och *mywebapi*. Båda komponenterna distribueras i ett dev-utrymme. Senare i den här självstudien skickar du en pull-begäran mot mywebapi för att utlösa CI-pipeline i Jenkins.

Mer information om hur du använder Azure dev Spaces och multi-service-utveckling med Azure dev Spaces finns i [komma igång med Azure dev Spaces med Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)och [utveckling av flera tjänster med Azure dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). De här självstudierna innehåller ytterligare bakgrunds information som inte ingår här.

1. Hämta https://github.com/Azure/dev-spaces lagrings platsen från GitHub.

2. Öppna mappen `samples/java/getting-started/webfrontend` i VS Code. (Du kan ignorera eventuella standarduppmaningar om att lägga till felsökningstillgångar eller återställa projektet.)

3. Uppdatera `/src/main/java/com/ms/sample/webfrontend/Application.java` för att se ut så här:

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

4. Klicka på **Visa** och sedan **Terminal** för att öppna den integrerade terminalen i vs Code.

5. Kör kommandot `azds prep` för att förbereda ditt program så att det körs i ett dev-utrymme. Det här kommandot måste köras från `dev-spaces/samples/java/getting-started/webfrontend` för att förbereda programmet korrekt:

    ```bash
    azds prep --public
    ```

    `azds prep` kommandot för dev Spaces CLI genererar Docker-och Kubernetes-tillgångar med standardinställningar. Dessa filer behålls under projektets livs längd och kan anpassas:

    * `./Dockerfile` och `./Dockerfile.develop` beskriver appens behållar avbildning och hur käll koden skapas och körs i behållaren.
    * Ett [Helm-diagram](https://helm.sh/docs/topics/charts/) under `./charts/webfrontend` beskriver hur du distribuerar containern till Kubernetes.
    * `./azds.yaml` är konfigurations filen för Azure dev Spaces.

    Mer information finns i [så här fungerar Azure dev Spaces och har kon figurer ATS](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Skapa och kör programmet i AKS med kommandot `azds up`:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Sök igenom konsolens utdata för information om den URL som skapades med kommandot `up`. Den kommer att vara i formen:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Öppna denna URL i ett webbläsarfönster och se webb programmet. När containern körs strömmas `stdout`- och `stderr`-utdata till terminalfönstret.

8. Sedan ställer du in och distribuerar *mywebapi*:

    1. Ändra katalog till `dev-spaces/samples/java/getting-started/mywebapi`

    2. Kör

        ```bash
        azds prep
        ```

    3. Kör

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet förbereder du Jenkins-servern så att den Kör exempel-CI-pipeline.

* Installera plugin-program
* Installera Helm och Kubernetes CLI
* Lägg till autentiseringsuppgifter

### <a name="install-plug-ins"></a>Installera plugin-program

1. Logga in på din Jenkins-Server. Välj **Hantera Jenkins > hantera plugin**-program.
2. På fliken **tillgänglig** väljer du följande plugin-program:
    * [Azure dev-utrymmen](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry uppgifter](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Miljö injektion](https://plugins.jenkins.io/envinject)
    * [GitHub-integrering](https://plugins.jenkins.io/github-pullrequest)

    Om dessa plugin-program inte visas i listan, kontrollerar du fliken **Installera** för att se om de redan är installerade.

3. Installera plugin-programmen genom att välja **Hämta nu och installera efter omstart**.

4. Starta om Jenkins-servern för att slutföra installationen.

### <a name="install-helm-and-kubectl"></a>Installera Helm och kubectl

Exempel pipelinen använder Helm och kubectl för att distribuera till dev-ytan. När Jenkins är installerat skapas ett administratörs konto med namnet *Jenkins*. Både Helm och kubectl måste vara tillgängliga för Jenkins-användaren.

1. Skapa en SSH-anslutning till Jenkins-huvudservern. 

2. Växla till `jenkins` användare:
    ```bash
    sudo su jenkins
    ```

3. Installera Helm CLI. Mer information finns i avsnittet om att [Installera Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Installera kubectl. Mer information finns i [**AZ ACS Kubernetes install-CLI**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Lägg till autentiseringsuppgifter i Jenkins

1. Jenkins behöver ett Azure-tjänstens huvud namn för att autentisera och komma åt Azure-resurser. Information om hur du skapar tjänstens huvud namn finns i avsnittet [skapa tjänstens huvud namn](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) i självstudien distribuera till Azure App Service. Se till att spara en kopia av utdata från `create-for-rbac` eftersom du behöver informationen för att slutföra nästa steg. Utdata kommer att se ut ungefär så här:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Lägg till en *Microsoft Azure tjänstens* typ av autentiseringsuppgifter i Jenkins med hjälp av tjänstens huvud namns information från föregående steg. Namnen i skärm bilden nedan motsvarar utdata från `create-for-rbac`.

    Fältet **ID** är namnet på Jenkins-autentiseringsuppgiften för tjänstens huvud namn. Exemplet använder värdet för `displayName` (i den här instansen `xxxxxxxjenkinssp`), men du kan använda vilken text du vill. Det här namnet på autentiseringsuppgifter är värdet för variabeln AZURE_CRED_ID miljö i nästa avsnitt.

    ![Lägg till autentiseringsuppgifter för tjänstens huvud namn i Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Beskrivningen** är valfri. Mer detaljerade instruktioner finns i avsnittet [lägga till tjänstens huvud namn i Jenkins](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) i självstudien distribuera till Azure App Service. 



3. Kör följande kommando för att visa dina ACR-autentiseringsuppgifter:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Gör en kopia av JSON-utdata som bör se ut ungefär så här:

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

4. Lägg till ett *användar namn med autentiseringstypen lösen ord* i Jenkins. **Användar namnet** är användar namnet från det sista steget, i det här exemplet `acr01`. **Lösen ordet** är värdet för det första lösen ordet, i det här exemplet `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. **ID: t** för den här autentiseringsuppgiften är värdet för ACR_CRED_ID.

5. Konfigurera en AKS-autentiseringsuppgift. Lägg till en *Kubernetes (kubeconfig)* -autentiseringstyp i Jenkins (Använd alternativet "Ange direkt"). Kör följande kommando för att få åtkomst behörighet för ditt AKS-kluster:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **ID** för den här autentiseringsuppgiften är värdet för KUBE_CONFIG_ID i nästa avsnitt.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Scenariot som valts för exempel pipelinen baseras på ett reellt mönster: en pull-begäran utlöser en CI-pipeline som bygger och distribuerar de föreslagna ändringarna i ett Azure dev-utrymme för testning och granskning. Beroende på resultatet av granskningen slås ändringarna samman och distribueras till AKS eller ignoreras. Slutligen tas dev-utrymmet bort.

Jenkins pipeline-konfiguration och Jenkinsfile definierar stegen i CI-pipeline. Det här flödesschemat visar pipeline-stegen och besluts punkterna som definieras av Jenkinsfile:

![Jenkins pipeline-flöde](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Hämta en ändrad version av *mywebapi* -projektet från https://github.com/azure-devops/mywebapi. Det här projektet innehåller flera filer som behövs för att skapa en pipeline, inklusive *Jenkinsfile*-, *Dockerfiles*-och Helm-diagrammet.

2. Logga in på Jenkins. Välj **Lägg till objekt**i menyn till vänster.

3. Välj **pipeline**och ange sedan ett namn i rutan **Ange ett objekt namn** . Välj **OK**så öppnas sidan pipeline-konfiguration automatiskt.

4. På fliken **Allmänt** kontrollerar du **förbereda en miljö för körningen**. 

5. Kontrol lera **Behåll Jenkins miljövariabler** och **Behåll Jenkins build-variabler**.

6. I rutan **Egenskaper innehåll** anger du följande miljövariabler:

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

    Med hjälp av de exempel värden som anges i föregående avsnitt bör listan över miljövariabler se ut ungefär så här:

    ![Jenkins för pipeline-miljövariabler](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Välj **pipeline-skript från SCM** i **pipeline > definition**.
8. I **SCM**väljer du **git** och anger sedan din lagrings platsen-URL.
9. I **gren specifikation**anger du `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Fyll i URL: en för SCM-lagrings platsen och skript Sök vägen "Jenkinsfile".
11. **Förenklad utcheckning** bör kontrol leras.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Skapa en pull-begäran för att utlösa pipelinen

För att slutföra steg 3 i det här avsnittet måste du kommentera en del av Jenkinsfile, annars får du ett 404-fel när du försöker visa nya och gamla versioner sida vid sida. Som standard kommer den tidigare delade versionen av mywebapi att tas bort och ersättas av den nya versionen när du väljer att slå samman PR. Gör följande ändring i Jenkinsfile innan du Slutför steg 1:

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

1. Gör en ändring i `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`och skapa sedan en pull-begäran. Exempel:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Logga in på Jenkins och välj pipelinens namn och välj sedan **Bygg nu**. 

    Du kan också skapa en *webhook* för att automatiskt utlösa Jenkins-pipeline. När en pull-begäran anges utfärdar GitHub ett inlägg till Jenkins, vilket utlöser pipelinen. Mer information om hur du konfigurerar en webhook finns i [ansluta Jenkins till GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Jämför ändringar i den aktuella delade versionen:

    1. Öppna webbläsaren och gå till den delade versionen `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT innehåller URL: en.

    2. Öppna en annan flik och ange sedan PR dev Space-URL: en. Det ser ut ungefär som `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Du hittar länken i **versions historik > < build # > > konsolens utdata** för Jenkins-jobbet. Sök efter `aksapp`på sidan, eller Visa endast prefixet genom att söka efter `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Skapa URL: en till det underordnade dev-utrymmet

När du arkiverar en pull-begäran skapar Jenkins ett underordnat dev-utrymme baserat på teamets delade dev-utrymme och kör koden från din pull-begäran i det underordnade dev-utrymmet. URL: en till det underordnade dev-området tar formuläret `http://$env.azdsprefix.<test_endpoint>`. 

**$ENV. azdsprefix** anges under pipeline-körningen av Azure dev Spaces-plugin-programmet med **devSpacesCreate**:

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

`test_endpoint` är URL: en till den webfrontend-app som du tidigare har distribuerat med `azds up`i [distribuera exempel program till AKS-klustret, steg 7](#test_endpoint). Värdet för `$env.TEST_ENDPOINT` anges i pipeline-konfigurationen. 

Följande kodfragment visar hur den underordnade dev Space-URL: en används i `smoketest`-fasen. Koden kontrollerar om det underordnade dev-utrymmet TEST_ENDPOINT är tillgängligt, och i så fall laddar ned hälsnings texten till stdout:

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

När du är klar med exempel programmet rensar du Azure-resurser genom att ta bort resurs gruppen:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder plugin-programmet för Azure dev Spaces för Jenkins och plugin-programmet Azure Container Registry för att bygga kod och distribuera till ett dev-utrymme.

I följande lista över resurser finns mer information om Azure dev Spaces, ACR tasks och CI/CD med Jenkins.

Azure dev-utrymmen:
* [Hur Azure Dev Spaces fungerar och är konfigurerade](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR-uppgifter:
* [Automatisera korrigering av operativsystem och ramverk med ACR Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatisk generering vid kod genomförande](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD med Jenkins på Azure:
* [Jenkins kontinuerlig distribution](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
