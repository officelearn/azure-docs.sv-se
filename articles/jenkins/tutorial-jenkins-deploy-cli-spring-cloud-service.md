---
title: Distribuera appar till Azure Spring Cloud med hjälp av Jenkins och Azure CLI
description: Lär dig hur du använder Azure CLI i en pipeline för kontinuerlig integrering och distribution för att distribuera mikrotjänster till Azure Spring Cloud-tjänsten
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: bc48eecc9e73b95fd9112d645135409c24369e10
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810216"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Självstudiekurs: Distribuera appar till Azure Spring Cloud med Jenkins och Azure CLI

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) är en fullständigt hanterad mikrotjänstutveckling med inbyggd tjänstidentifiering och konfigurationshantering. Tjänsten gör det enkelt att distribuera Spring Boot-baserade mikrotjänstprogram till Azure. Den här självstudien visar hur du kan använda Azure CLI i Jenkins för att automatisera kontinuerlig integrering och leverans (CI/CD) för Azure Spring Cloud.

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Etablera en tjänstinstans och starta ett Java Spring-program
> * Förbered Jenkins-servern
> * Använda Azure CLI i en Jenkins-pipeline för att skapa och distribuera mikrotjänstprogrammen 

Den här självstudien förutsätter mellanliggande kunskap om grundläggande Azure-tjänster, Azure Spring Cloud, [Jenkins-pipelines](https://jenkins.io/doc/book/pipeline/) och plugin-program och GitHub.

## <a name="prerequisites"></a>Krav

>[!Note]
> Azure Spring Cloud erbjuds för närvarande som en offentlig förhandsversion. Offentliga förhandsversioner gör det möjligt för kunder att experimentera med nya funktioner innan de släpps officiellt.  Offentliga förhandsversionsfunktioner och tjänster är inte avsedda för produktionsanvändning.  Mer information om support under förhandsversioner finns i våra [vanliga frågor](https://azure.microsoft.com/support/faq/) och svar eller en [supportförfrågan för](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) att få veta mer.

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

* Ett GitHub-konto. Om du inte har ett GitHub-konto skapar du ett [kostnadsfritt konto](https://github.com/) innan du börjar.

* En Jenkins-huvudserver. Om du inte redan har en Jenkins-hanterare distribuerar du [Jenkins](https://aka.ms/jenkins-on-azure) på Azure genom att följa stegen i den här [snabbstarten](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). Följande krävs på Jenkins-noden/agenten (till exempel. byggserver):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 eller högre](https://maven.apache.org/download.cgi)
    * [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.67 eller senare

    >[!TIP]
    > Verktyg som Git, JDK, Az CLI och Azure plug-ius ingår som standard i lösningsmallen För Azure Marketplace [Microsoft Jenkins.](https://aka.ms/jenkins-on-azure) 
    
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Etablera en tjänstinstans och starta ett Java Spring-program

Vi använder [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) som exempelprogram för Microsoft-tjänster och följer samma steg i [Snabbstart: Starta ett Java Spring-program med Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) för att etablera tjänstinstansen och konfigurera programmen. Om du redan har gått igenom samma process kan du hoppa till nästa avsnitt. Annars ingår azure CLI-kommandona i följande fall. Se [Snabbstart: Starta ett Java Spring-program med Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) för att hämta ytterligare bakgrundsinformation.

Din lokala dator måste uppfylla samma förutsättningar som Jenkins-byggservern. Kontrollera att följande är installerade för att skapa och distribuera mikrotjänstprogrammen:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 eller högre](https://maven.apache.org/download.cgi)
    * [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.67 eller senare

1. Installera Azure Spring Cloud-tillägget:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Skapa en resursgrupp som innehåller din Azure Spring Cloud-tjänst:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Etablera en instans av Azure Spring Cloud:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Gaffel [piggy metrics](https://github.com/Azure-Samples/piggymetrics) repo till ditt eget GitHub-konto. I din lokala dator, klona din `source-code`repo i en katalog som heter:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Konfigurera konfigurationsservern. Se till &lt;att du ersätter&gt; ditt GitHub-id med rätt värde.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Bygg projektet:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Skapa de tre mikrotjänsterna: **gateway,** **auth-service**och **kontotjänst:**

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Distribuera programmen: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Tilldela offentlig slutpunkt till gateway:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Fråga gateway-programmet för att hämta url:en så att du kan verifiera att programmet körs.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Navigera till url:en som angavs av föregående kommando för att köra PiggyMetrics-programmet. 

## <a name="prepare-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet förbereder du Jenkins-servern för att köra en version, vilket är bra för testning. På grund av säkerhetskonsekvenser bör du dock använda en [Azure VM-agent](https://plugins.jenkins.io/azure-vm-agents) eller [Azure Container-agent](https://plugins.jenkins.io/azure-container-agents) för att starta en agent i Azure för att köra dina versioner. Mer information finns i Jenkins-artikeln om [säkerhetsriskerna med att kompilera på ett original](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Installera plugin-program

1. Logga in på Jenkins-servern. Välj **Hantera Jenkins > Hantera plugins**.
2. På fliken **Tillgänglig** väljer du följande plugin-program:
    * [GitHub-integrering](https://plugins.jenkins.io/github-pullrequest)
    * [Azure-autentiseringsuppgifter](https://plugins.jenkins.io/azure-credentials)

    Om dessa plugin-program inte visas i listan kontrollerar du fliken **Installerad** för att se om de redan är installerade.

3. Om du vill installera plugin-programmen väljer du **Hämta nu och installerar efter omstart**.

4. Starta om Jenkins-servern för att slutföra installationen.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Lägga till din Azure Service Principal-autentiseringsautentiseringsuppgifter i Jenkins autentiseringsuppgifter

1. Du behöver en Azure-tjänstansvarig för att distribuera till Azure. Mer information finns i avsnittet [Skapa tjänstens huvudnamn](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) i självstudien Distribuera till Azure App Service. Utdata `az ad sp create-for-rbac` från ser ut ungefär så här:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. På Jenkins-instrumentpanelen väljer du **Autentiseringssystem** > **System**. Välj sedan **Globala autentiseringsuppgifter (obegränsade)**.

3. Välj **Lägg till autentiseringsuppgifter**. 

4. Välj **Microsoft Azure Service Principal** som slag.

5. Ange värden för: * Prenumerations-ID: använd ditt Azure-prenumerations-ID * Klient-ID: använd `appId` * Client Secret: använd `password` * Klient-ID: använd `tenant` * Azure Environment: välj ett förinställt värde. Använd till exempel **Azure** för Azure Global * ID: ange som **azure_service_principal**. Vi använder detta ID i ett senare steg i den här artikeln * Beskrivning: är ett valfritt fält. Vi rekommenderar att du ger ett meningsfullt värde här.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Installera förlängning av Maven och Az CLI-fjädermoln

Exempelpipelinen använder Maven för att bygga och Az CLI för att distribuera till tjänstinstansen. När Jenkins installeras skapas ett administratörskonto med namnet *jenkins*. Kontrollera att användaren *jenkins* har behörighet att köra fjädermoln-tillägget.

1. Anslut till Jenkins-mästaren via SSH. 

2. Installera Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Installera Azure CLI. Mer information finns [i Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI installeras som standard om du använder [Jenkins Master på Azure](https://aka.ms/jenkins-on-azure).

4. Växla till `jenkins` användaren:

    ```bash
        sudo su jenkins
    ```

5. Lägg till **fjädermoln-tillägget:**

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Skapa en Jenkinsfile
1. Skapa en **Jenkinsfile** i roten&gt;i din egen repo (;yourhttps://github.com/&ltGitHub id /piggymetrics).

2. Uppdatera filen enligt följande. Se till att du ersätter värdena för ** \<resursgruppsnamnet>** och ** \<tjänstnamnet>**. Ersätt **azure_service_principal** med rätt ID om du använder ett annat värde när du har lagt till autentiseringsuppgifterna i Jenkins. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Spara och genomför ändringen.

## <a name="create-the-job"></a>Skapa jobbet

1. Klicka på Nytt **objekt**på Jenkins-instrumentpanelen .

2. Ange ett namn, *Distribuera-PiggyMetrics* för jobbet och välj **Pipeline**. Klicka på OK.

3. Klicka på fliken **Pipeline**.

4. För **Definition** väljer du **Pipeline-skript från SCM**.

5. För **SCM** väljer du **Git**.

6. Ange GitHub-URL:en för din kluvna repo: ** https://github.com/&lt;ditt&gt;GitHub-id /piggymetrics.git**

7. Kontrollera att **Branch Specifier (svart för "any")** är ***/Azure**

8. Behåll **skriptsökväg** som **Jenkinsfile**

7. Klicka på **Spara**

## <a name="validate-and-run-the-job"></a>Validera och köra jobbet

Innan du kör jobbet ska vi uppdatera texten i inloggningsinmatningsrutan för att **ange inloggnings-ID**.

1. Öppna `index.html` i **/gateway/src/main/resources/static/**

2. Sök efter "ange din inloggning" och uppdatera till "ange inloggnings-ID"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Genomför ändringarna

4. Kör jobbet i Jenkins manuellt. Klicka på jobbet *Deploy-PiggyMetrics* på Jenkins-instrumentpanelen och sedan **skapa nu**.

När jobbet är klart navigerar du till den offentliga IP-adressen för **gateway-programmet** och kontrollerar att ditt program har uppdaterats. 

![Uppdaterade Piggy Metrics](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort de resurser som skapas i den här artikeln:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du använder Azure CLI i Jenkins för att automatisera kontinuerlig integrering och leverans (CI/CD) för Azure Spring Cloud.

Mer information om Azure Jenkins-provider finns på Jenkins på Azure-webbplatsen.

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/jenkins/)
