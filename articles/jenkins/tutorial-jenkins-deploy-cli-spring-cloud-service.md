---
title: Distribuera appar till Azure våren Cloud med Jenkins och Azure CLI
description: Lär dig hur du använder Azure CLI i en kontinuerlig integrering och distributions pipeline för att distribuera mikrotjänster till Azure våren Cloud service
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75734980"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Självstudie: distribuera appar till Azure våren Cloud med Jenkins och Azure CLI

[Azure våren Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) är en helt hanterad mikroservice-utveckling med inbyggd tjänst identifiering och konfigurations hantering. Tjänsten gör det enkelt att distribuera våren Boot-baserade mikroservice-program till Azure. Den här självstudien visar hur du kan använda Azure CLI i Jenkins för att automatisera kontinuerlig integrering och leverans (CI/CD) för Azure våren Cloud.

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Etablera en tjänst instans och starta ett Java våren-program
> * Förbered din Jenkins-Server
> * Använd Azure CLI i en Jenkins-pipeline för att bygga och distribuera mikrotjänst program 

Den här självstudien förutsätter mellanliggande kunskaper om grundläggande Azure-tjänster, Azure våren-moln, Jenkins- [pipelines](https://jenkins.io/doc/book/pipeline/) och plugin-program och GitHub.

## <a name="prerequisites"></a>Krav

>[!Note]
> Azure våren Cloud erbjuds för närvarande som en offentlig för hands version. Med den offentliga för hands versionen kan kunder experimentera med nya funktioner före den officiella versionen.  Funktioner och tjänster för offentliga för hands versioner är inte avsedda för användning i produktion.  Om du vill ha mer information om support under för hands versionerna kan du läsa [vanliga frågor och svar](https://azure.microsoft.com/support/faq/) eller arkiv en [supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Ett GitHub-konto. Om du inte har ett GitHub-konto kan du skapa ett [kostnads fritt konto](https://github.com/) innan du börjar.

* En Jenkins-huvudserver. Om du inte redan har en Jenkins-Master distribuerar du [Jenkins](https://aka.ms/jenkins-on-azure) på Azure genom att följa stegen i den här [snabb](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)starten. Följande krävs för Jenkins-noden/agenten (till exempel. Build-Server):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 eller senare](https://maven.apache.org/download.cgi)
    * [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.67 eller högre

    >[!TIP]
    > Verktyg som git, JDK, AZ CLI och Azure-plugin-ius ingår som standard i Azure Marketplace [Microsoft Jenkins](https://aka.ms/jenkins-on-azure) Solution Template.
    
* [Registrera dig för en Azure-prenumeration](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Etablera en tjänst instans och starta ett Java våren-program

Vi använder [Piggy-mått](https://github.com/Azure-Samples/piggymetrics) som exempel på Microsoft-tjänstprogrammet och följer samma steg i [snabb start: starta ett Java våren-program med Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) för att etablera tjänst instansen och konfigurera programmen. Om du redan har gått igenom samma process kan du gå vidare till nästa avsnitt. Annars ingår Azure CLI-kommandona i följande avsnitt. Se [snabb start: starta ett Java våren-program med hjälp av Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) för att få mer bakgrunds information.

Den lokala datorn måste uppfylla samma krav som Jenkins build-servern. Kontrol lera att följande är installerat för att skapa och distribuera mikrotjänst program:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 eller senare](https://maven.apache.org/download.cgi)
    * [Azure CLI installerat](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.67 eller högre

1. Installera Azure våren Cloud-tillägget:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Skapa en resurs grupp som innehåller din Azure våren Cloud-tjänst:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Etablera en instans av Azure våren Cloud:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Förgrena [Piggy-måtten](https://github.com/Azure-Samples/piggymetrics) lagrings platsen till ditt eget GitHub-konto. Klona din lagrings platsen i en katalog med namnet `source-code`på den lokala datorn:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Konfigurera konfigurations servern. Se till att du ersätter &lt;ditt GitHub-ID&gt; med rätt värde.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Bygg projektet:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Skapa tre mikrotjänster: **Gateway**, **auth-service**och **Account-service**:

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

9. Tilldela en offentlig slut punkt till Gateway:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Fråga Gateway-appen för att hämta URL: en så att du kan kontrol lera att programmet körs.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Gå till URL: en som tillhandahålls av föregående kommando för att köra PiggyMetrics-programmet. 

## <a name="prepare-jenkins-server"></a>Förbereda Jenkins-servern

I det här avsnittet förbereder du Jenkins-servern för att köra en version, vilket är bra för testning. Men på grund av säkerhets indirekt bör du använda en [Azure VM-agent](https://plugins.jenkins.io/azure-vm-agents) eller [Azure Container agent](https://plugins.jenkins.io/azure-container-agents) för att skapa en agent i Azure för att köra dina build-versioner. Mer information finns i Jenkins-artikeln om [säkerhetsriskerna med att kompilera på ett original](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Installera plugin-program

1. Logga in på din Jenkins-Server. Välj **Hantera Jenkins > hantera plugin**-program.
2. På fliken **tillgänglig** väljer du följande plugin-program:
    * [GitHub-integrering](https://plugins.jenkins.io/github-pullrequest)
    * [Azure-autentiseringsuppgift](https://plugins.jenkins.io/azure-credentials)

    Om dessa plugin-program inte visas i listan, kontrollerar du fliken **Installera** för att se om de redan är installerade.

3. Installera plugin-programmen genom att välja **Hämta nu och installera efter omstart**.

4. Starta om Jenkins-servern för att slutföra installationen.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Lägg till dina autentiseringsuppgifter för Azure-tjänstens huvud namn i Jenkins Credential Store

1. Du behöver ett Azure-tjänstens huvud namn för att distribuera till Azure. Mer information finns i avsnittet [skapa tjänstens huvud namn](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) i självstudien distribuera till Azure App Service. Resultatet från `az ad sp create-for-rbac` ser ut ungefär så här:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. På Jenkins-instrumentpanelen väljer du **Autentiseringsuppgifter** > **System**. Välj sedan **Globala autentiseringsuppgifter (obegränsade)** .

3. Välj **Lägg till autentiseringsuppgifter**. 

4. Välj **Microsoft Azure tjänstens huvud namn** som typ.

5. Ange värden för: * prenumerations-ID: Använd ditt Azure-prenumerations-ID * klient-ID: Använd `appId` * klient hemlighet: Använd `password` * klient organisations-ID: Använd `tenant` * Azure-miljö: Välj ett värde för fördefinierat värde. Använd exempelvis **Azure** för Azure Global * ID: ange som **azure_service_principal**. Vi använder detta ID i ett senare steg i den här artikeln * Beskrivning: är ett valfritt fält. Vi rekommenderar att du anger ett meningsfullt värde här.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Installera Maven och AZ CLI våren-Cloud extension

Exempel pipelinen använder Maven för att bygga och AZ CLI för att distribuera till tjänst instansen. När Jenkins är installerat skapas ett administratörs konto med namnet *Jenkins*. Se till att användaren *Jenkins* har behörighet att köra våren-Cloud-tillägget.

1. Anslut till Jenkins-huvudservern via SSH. 

2. Installera maven

    ```bash
        sudo apt-get install maven 
    ```

3. Installera Azure CLI. Mer information finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI installeras som standard om du använder [Jenkins-Master på Azure](https://aka.ms/jenkins-on-azure).

4. Växla till `jenkins` användare:

    ```bash
        sudo su jenkins
    ```

5. Lägg till tillägget **fjäder moln** :

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Skapa en Jenkinsfile
1. I din egen lagrings platsen (https://github.com/&lt ; ditt GitHub-ID&gt; /piggymetrics) skapar du en **Jenkinsfile** i roten.

2. Uppdatera filen på följande sätt. Se till att du ersätter värdena för **\<resurs grupp namn >** och **\<tjänst namn >** . Ersätt **azure_service_principal** med rätt ID om du använder ett annat värde när du har lagt till autentiseringsuppgiften i Jenkins. 

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

1. Klicka på **nytt objekt**på Jenkins-instrumentpanelen.

2. Ange ett namn, *distribuera PiggyMetrics* för jobbet och välj **pipeline**. Klicka på OK.

3. Klicka på fliken **Pipeline**.

4. För **Definition** väljer du **Pipeline-skript från SCM**.

5. För **SCM** väljer du **Git**.

6. Ange GitHub-URL: en för din förgrenade lagrings platsen: **https://github.com/&lt ; ditt GitHub-id&gt; /piggymetrics.git**

7. Se till att **gren specifikation (svart för valfri)** är * **/Azure**

8. Behåll **skript Sök vägen** som **Jenkinsfile**

7. Klicka på **Spara**

## <a name="validate-and-run-the-job"></a>Verifiera och kör jobbet

Innan du kör jobbet uppdaterar vi texten i rutan logga in för att **ange inloggnings-ID**.

1. Öppna `index.html` i **/Gateway/src/main/Resources/static/** i din egen lagrings platsen

2. Sök efter "Ange din inloggning" och uppdatera till "ange inloggnings-ID"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Genomför ändringarna

4. Kör jobbet i Jenkins manuellt. På instrument panelen för Jenkins klickar du på jobbet *Deploy-PiggyMetrics* och **skapar sedan nu**.

När jobbet är klart navigerar du till den offentliga IP-adressen för **Gateway** -programmet och kontrollerar att programmet har uppdaterats. 

![Uppdaterade Piggy-mått](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder Azure CLI i Jenkins för att automatisera kontinuerlig integrering och leverans (CI/CD) för Azure våren Cloud.

Mer information om Azure Jenkins-providern finns på Jenkins på Azure-webbplatsen.

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/jenkins/)
