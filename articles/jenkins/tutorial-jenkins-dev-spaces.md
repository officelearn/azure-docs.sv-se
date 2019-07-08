---
title: Med Azure Dev blanksteg-pluginprogrammet för Jenkins med Azure Kubenetes-tjänst
description: Lär dig hur du använder plugin-programmet Azure Dev blanksteg i en pipeline för kontinuerlig integrering.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: 8c47b8caf2d289ed17647b8003cc702156f3cddb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592081"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Självstudier: Med Azure Dev blanksteg-pluginprogrammet för Jenkins med Azure Kubenetes-tjänst 

Azure Dev blanksteg kan du testa och utveckla din mikrotjänst-program som körs i Azure Kubernetes Service (AKS) utan att behöva replikera eller fingera beroenden stegvis. Azure Dev blanksteg plugin-programmet för Jenkins hjälper dig att använda Dev blanksteg i kontinuerlig integrering och leverans (CI/CD) pipeline.

Den här självstudien använder även Azure Container Registry (ACR). ACR lagrar bilder och en ACR-uppgift skapar Docker och Helm artefakter. Med ACR och ACR-uppgift för artefakten generation eliminerar behovet av att installera ytterligare programvara, till exempel Docker på Jenkins-servern. 

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa ett Azure Dev blanksteg aktiverade AKS-kluster
> * Distribuera ett program som har flera tjänster till AKS
> * Förbereda din Jenkins-server
> * Använd plugin-programmet Azure Dev blanksteg i en Jenkins-pipeline för att förhandsgranska ändringar i koden innan du sammanfogar dem i projektet

Den här självstudiekursen förutsätter mellanliggande kunskaper om grundläggande Azure-tjänster, AKS, ACR, Azure Dev blanksteg, Jenkins [pipelines](https://jenkins.io/doc/book/pipeline/) och plugin-program och GitHub. Grundläggande kunskaper om stöd för verktyg som kubectl och Helm är bra.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Ett GitHub-konto. Om du inte har ett GitHub-konto kan du skapa en [kostnadsfritt konto](https://github.com/) innan du börjar.

* [Visual Studio Code](https://code.visualstudio.com/download) med den [Azure Dev blanksteg](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) tillägget installeras.

* [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.43 eller högre.

* En Jenkins-huvudserver. Om du inte redan har en Jenkins-huvudserver kan distribuera [Jenkins](https://aka.ms/jenkins-on-azure) på Azure genom att följa stegen i den här [snabbstarten](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Jenkins-servern måste ha både Helm och kubectl installerade och tillgängliga för Jenkins-konto som beskrivs senare i den här självstudien.

* VS Code, VS Code-terminalen eller WSL och Bash. 


## <a name="create-azure-resources"></a>Skapa Azure-resurser

I det här avsnittet skapar du Azure-resurser:

* En resursgrupp som innehåller alla Azure-resurser för den här självstudiekursen.
* En [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) kluster (AKS).
* En [Azure-behållarregister](https://docs.microsoft.com/azure/container-registry/) (ACR) och skapa (med ACR aktiviteter) som du kan lagra Docker-avbildningar.

1. Skapa en resursgrupp.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Skapa ett AKS-kluster. Skapa AKS-kluster i en [region som stöder utveckling blanksteg](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Konfigurera AKS om du vill använda Dev blanksteg.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Det här steget installerar den `azds` CLI-tillägg.

4. Skapa ett behållarregister.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Distribuera exempelappar till AKS-kluster

I det här avsnittet, Ställ in ett utrymme för utveckling och distribuerar ett exempelprogram till AKS-klustret du skapade i det sista avsnittet. Programmet består av två delar *webfrontend* och *mywebapi*. Båda komponenterna distribueras i en dev-utrymme. Senare i den här självstudien ska du skicka en pull-begäran mot mywebapi att utlösa pipelinen CI i Jenkins.

Läs mer om att använda Azure Dev blanksteg och flera tjänster utveckling med Azure Dev blanksteg, [komma igång med Azure Dev blankstegen med Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java), och [flera tjänster utveckling med Azure Dev blanksteg](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Dessa självstudier innehåller mer bakgrundsinformation som inte inkluderas här.

1. Ladda ned den https://github.com/Azure/dev-spaces lagringsplats från GitHub.

2. Öppna den `samples/java/getting-started/webfrontend` mapp i VS Code. (Du kan ignorera eventuella standarduppmaningar om att lägga till felsökningstillgångar eller återställa projektet.)

3. Uppdatera `/src/main/java/com/ms/sample/webfrontend/Application.java` ska se ut som följande:

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

4. Klicka på **visa** sedan **Terminal** att öppna den integrerade terminalen i VS Code.

5. Kör den `azds prep` kommando för att förbereda programmet att köras i en dev-utrymmet. Det här kommandot måste köras från `dev-spaces/samples/java/getting-started/webfrontend` att förbereda programmet korrekt:

    ```bash
    azds prep --public
    ```

    Dev blanksteg CLI `azds prep` kommandot genererar Docker och Kubernetes tillgångar med standardinställningar. De här filerna kvarstår för livslängden för projektet och de kan anpassas:

    * `./Dockerfile` och `./Dockerfile.develop` beskriva appens behållaravbildning och hur källkoden bygger och körs i behållaren.
    * Ett [Helm-diagram](https://helm.sh/docs/developing_charts/) under `./charts/webfrontend` beskriver hur du distribuerar containern till Kubernetes.
    * `./azds.yaml` är Azure Dev blanksteg konfigurationsfil.

    Mer information finns i [hur Azure Dev blanksteg fungerar och är konfigurerad](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Skapa och köra programmet i AKS med hjälp av den `azds up` kommando:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Skanna konsolens utdata för information om den URL som har skapats av den `up` kommando. Den kommer att vara i formen:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Öppna den här URL: en i ett webbläsarfönster och du bör se webbappen. När containern körs strömmas `stdout`- och `stderr`-utdata till terminalfönstret.

8. Konfigurera och distribuera sedan *mywebapi*:

    1. Gå till katalogen för `dev-spaces/samples/java/getting-started/mywebapi`

    2. Kör

        ```bash
        azds prep
        ```

    3. Kör

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet ska förbereda du Jenkins-servern och kör exempel CI-pipeline.

* Installera plugin-program
* Installera Helm och Kubernetes CLI
* Lägg till autentiseringsuppgifter

### <a name="install-plugins"></a>Installera plugin-program

1. Logga in på Jenkins-servern. Välj **hantera Jenkins > Hantera plugin-program**.
2. På den **tillgänglig** väljer du följande plugin-program:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry uppgifter](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Miljö Injector](https://plugins.jenkins.io/envinject)
    * [GitHub Integration](https://plugins.jenkins.io/github-pullrequest)

    Om dessa plugin-program inte visas i listan, kontrollerar du den **installerad** fliken för att se om de redan är installerat.

3. Välj för att installera den plugin-program, **ladda ned nu och installera efter omstart**.

4. Starta om Jenkins-servern för att slutföra installationen.

### <a name="install-helm-and-kubectl"></a>Installera Helm och kubectl

Exempel-pipeline använder Helm och kubectl för att distribuera till området utveckling. När Jenkins är installerad, skapas ett administratörskonto med namnet *jenkins*. Både Helm och kubectl måste vara tillgänglig för jenkins-användare.

1. Göra en SSH-anslutning i Jenkins-huvudserver. 

2. Växla till den `jenkins` användaren:
    ```bash
    sudo su jenkins
    ```

3. Installera Azure CLI. Mer information finns i [installera Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Installera kubectl. Mer information finns i [ **az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Lägg till autentiseringsuppgifter i Jenkins

1. Jenkins behöver ett huvudnamn för Azure-tjänst för autentisering och åtkomst till Azure-resurser. Om du vill skapa tjänstens huvudnamn, referera till den [skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) avsnittet i för att distribuera till Azure App Service-självstudie. Glöm inte att spara en kopia av utdata från `create-for-rbac` eftersom du behöver den informationen för att utföra nästa steg. Utdata ser ut ungefär så här:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Lägg till en *Microsoft Azure Service Principal* autentiseringstyp i Jenkins, med information om tjänstens huvudnamn i föregående steg. Namnen på skärmbilden nedan motsvarar utdata från `create-for-rbac`.

    Den **ID** fältet är namn på Jenkins-autentiseringsuppgift för tjänstens huvudnamn. I exemplet används värdet för `displayName` (i det här fallet `xxxxxxxjenkinssp`), men du kan använda valfri text som du vill. Den här namn på autentiseringsuppgifter är värdet för miljövariabeln AZURE_CRED_ID i nästa avsnitt.

    ![Lägg till autentiseringsuppgifter för tjänstens huvudnamn i Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    Den **beskrivning** är valfritt. Mer detaljerade instruktioner finns i [Jenkins-Lägg till tjänstens huvudnamn](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) avsnittet i för att distribuera till Azure App Service-självstudie. 



3. För att visa dina autentiseringsuppgifter för ACR, kör du följande kommando:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Skapa en kopia av JSON-utdata som bör se ut ungefär så här:

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

4. Lägg till en *användarnamn med lösenord* autentiseringstyp i Jenkins. Den **användarnamn** är användarnamnet från det sista steget i det här exemplet `acr01`. Den **lösenord** är värdet för det första lösenordet i det här exemplet `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. Den **ID** är värdet för ACR_CRED_ID av autentiseringsuppgiften.

5. Ställ in ett AKS-autentiseringsuppgifter. Lägg till en *Kubernetes-konfiguration (kubeconfig)* autentiseringstyp i Jenkins (Använd alternativet ”direkt RETUR”). Om du vill hämta autentiseringsuppgifterna för ditt AKS-kluster, kör du följande kommando:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   Den **ID** den här autentiseringsuppgiften är värdet för KUBE_CONFIG_ID i nästa avsnitt.

## <a name="create-a-pipeline"></a>Skapa en pipeline

Det scenario som valts för exempel-pipeline baseras på ett verkligt mönster: En pull-begäran som utlösare en CI-pipeline som skapar och distribuerar sedan de föreslagna ändringarna till ett Azure dev-utrymme för att testa och granska. Beroende på resultatet av granskningen är ändringarna antingen samman och distribueras till AKS eller tas bort. Slutligen bort dev.

Konfiguration för Jenkins-pipeline och Jenkinsfile definierar du stegen i CI-pipelinen. Det här flödesschemat visar pipeline-stadier och beslutspunkter som definieras av Jenkinsfile:

![Jenkins pipeline flöde](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Ladda ned en modifierad version av den *mywebapi* projektet från https://github.com/azure-devops/mywebapi. Det här projektet innehåller flera filer som behövs för att skapa en pipeline, inklusive den *Jenkinsfile*, *Dockerfiles*, och Helm-diagram.

2. Logga in på Jenkins. På menyn till vänster väljer **Lägg till objekt**.

3. Välj **Pipeline**, och ange sedan ett namn i den **ange ett namn för objektet** box. Välj **OK**, och sedan öppnas automatiskt konfigurationsskärmen pipeline.

4. På den **Allmänt** fliken Kontrollera **förbereda en miljö för körningen**. 

5. Kontrollera **Behåll Jenkins miljövariabler** och **Behåll Jenkins Build variabler**.

6. I den **egenskaper innehåll** , ange följande miljövariabler:

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

    Med hjälp av de exempelvärden som anges i föregående avsnitt, lista över miljövariabler bör se ut ungefär så här:

    ![Miljövariabler för Jenkins-pipeline](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Välj **Pipeline-skriptet från SCM** i **Pipeline > Definition**.
8. I **SCM**, Välj **Git** och sedan anger en URL för lagringsplatsen.
9. I **gren specificerare**, ange `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Fyll i SCM-repo-URL och skript sökvägen ”Jenkinsfile”.
11. **Förenklad checka ut** bör kontrolleras.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Skapa en pull-begäran för att utlösa pipelinen

Att slutföra steg 3 i det här avsnittet måste du kommentera en del av Jenkinsfile, annars får du ett 404-fel vid försök att visa de nya och gamla versionerna sida vid sida. Som standard när du väljer att slå samman din pull-begäran tas den tidigare dela versionen av mywebapi bort och ersättas med den nya versionen. Du kan göra följande ändring i Jenkinsfile innan du slutför steg 1:

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

1. Gör en ändring på `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`och sedan skapa en pull-begäran. Exempel:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Logga in på Jenkins och välj pipelinenamnet och välj sedan **skapa nu**. 

    Du kan också ställa in en *webhook* att automatiskt utlösa Jenkins-pipelinen. När en pull-begäran anges utfärdar GitHub ett INLÄGG till Jenkins, utlösa pipeline. Mer information om hur du konfigurerar en webhook finns i [ansluta Jenkins till GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Jämföra ändringar till den aktuella dela versionen:

    1. Öppna webbläsaren och navigera till den delade versionen `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT innehåller URL: en.

    2. Öppna en annan flik och ange URL: en PR dev utrymme. Det ska vara detsamma som `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Hittar du på länken i **skapa Historik >< build # >> Konsolutdata** för Jenkins-jobbet. Söksida för `aksapp`, eller om du vill se bara prefixet, Sök efter `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Konstruera URL: en till området underordnade utveckling

När du har registrerat en pull-begäran kan Jenkins skapar ett utrymme för utveckling av underordnade baserat på gruppens delade dev utrymme och kör koden från din pull-begäran i den underordnat dev utrymme. URL: en till området underordnade utveckling har formen `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** anges under pipelinekörning av Azure Dev blanksteg plugin-programmet genom att **devSpacesCreate**:

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

Den `test_endpoint` är Webbadressen till webfrontend-app som du tidigare har distribuerat med `azds up`i [distribuera exempelappar i AKS-klustret, steg 7](#test_endpoint). Värdet för `$env.TEST_ENDPOINT` anges i pipeline-konfigurationen. 

Följande kodfragment visar hur URL: en underordnad dev utrymme används i den `smoketest` steg. Kod som kontrollerar om det underordnade dev utrymmet TEST_ENDPOINT är tillgänglig och i så fall, hämtar du texten till stdout:

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

När du är klar med exempelprogrammet som kan rensa Azure-resurser genom att ta bort resursgruppen:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du använder Azure Dev blanksteg plugin-programmet för Jenkins och Azure Container Registry-plugin-programmet och skapa kod som du kan distribuera till en dev-utrymme.

Följande lista över resurser finns mer information om Azure Dev blanksteg, ACR-uppgifter och CI/CD med Jenkins.

Azure Dev blanksteg:
* [Hur Azure Dev Spaces fungerar och är konfigurerade](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR uppgifter:
* [Automatisera korrigering av operativsystem och ramverk med ACR Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatisk build på kodgenomförande](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD med Jenkins på Azure:
* [Kontinuerlig Jenkins-distribution](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
