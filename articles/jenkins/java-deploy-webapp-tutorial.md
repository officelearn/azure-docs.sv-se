---
title: Använda Jenkins för att distribuera dina webbprogram till Azure
description: Konfigurera kontinuerlig integrering från GitHub till Azure App Service för Java-webbappar med hjälp av Jenkins och Docker.
ms.topic: tutorial
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: e880d84c3ae0fd23c11bb9b30733544bd5f28872
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389950"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Konfigurera kontinuerlig integrering och distribution till Azure App Service med Jenkins

Den här självstudien konfigurerar kontinuerlig integrering och distribution (CI/CD) av en Java-exempelwebbapp som utvecklats med ramverket [Spring Boot](http://projects.spring.io/spring-boot/) till [Azure App Service-webbapp på Linux](/azure/app-service/containers/app-service-linux-intro) med hjälp av Jenkins.

I den här självstudien går du igenom följande uppgifter:

> [!div class="checklist"]
> * Installera det Jenkins-plugin-program som krävs för att distribuera till Azure App Service.
> * Definiera ett Jenkins-jobb för att skapa Docker-avbildningar från en GitHub-lagringsplats när en ny incheckning skickas.
> * Definiera en ny Azure-webbapp för Linux och konfigurera den för att distribuera Docker-avbildningar som överförs till Azure Container-registret.
> * Konfigurera Jenkins plugin-programmet för Azure App Service.
> * Distribuera exempelappen till Azure App Service med en manuell version.
> * Utlös en Jenkins-version och uppdatera webbappen genom att skicka ändringar till GitHub.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här kursen behöver du:

* [Jenkins](https://jenkins.io/) med JDK och Maven-verktyg som har konfigurerats. Om du inte har ett Jenkins-system kan du skapa ett nu i Azure från [Jenkins-lösningsmallen](/azure/jenkins/install-jenkins-solution-template).
* Ett [GitHub](https://github.com)-konto.
* [Azure CLI 2.0](/cli/azure), antingen från din lokala kommandoraden eller i [Azure Cloud Shell](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Installera Jenkins-plugin-program

1. Öppna en webbläsare till din Jenkins-webbkonsol och välj **Hantera Jenkins** i den vänstra menyn, och välj sedan **Hantera plugin-program**.
2. Välj fliken **Tillgängliga**.
3. Sök efter och markera kryssrutan intill följande plugin-program:   

    - [Azure App Service Plug-in](https://plugins.jenkins.io/azure-app-service)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source)

    Om plugin-programmen inte visas kontrollerar du att de inte redan har installerats genom att titta på fliken **Installerade**.

1. Välj **Ladda ned nu och installera efter omstart** för att aktivera plugin-programmen i din Jenkins-konfiguration.

## <a name="configure-github-and-jenkins"></a>Konfigurera GitHub och Jenkins

Konfigurera Jenkins för att ta emot [GitHub-webhooks](https://developer.github.com/webhooks/) när nya incheckningar skickas till en lagringsplats i ditt konto.

1. Välj **Hantera Jenkins** och sedan **Konfigurera system**. I avsnittet **GitHub** kontrollerar du att **Manage hooks** (Hantera hookar) är markerat och väljer sedan **Manage additional GitHub actions** (Hantera ytterligare GitHub-åtgärder) och väljer **Convert login and password to token** (Konvertera inloggning och lösenord till token).
2. Välj alternativknappen **From login and password** (Från användarnamn och lösenord) och ange ditt GitHub-användarnamn och -lösenord. Välj **Create token credentials** (Skapa tokenautentiseringsuppgifter) för att skapa en ny [GitHub Personal Access Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) (Personlig åtkomsttoken för GitHub).   
   ![Skapa GitHub-PAT från användarnamn och lösenord](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Välj den nyligen skapade token från listrutan **Autentiseringsuppgifter** i GitHub Servers-konfigurationen. Välj **Testanslutning** för att verifiera att autentiseringen fungerar.   
   ![Verifiera anslutningen till GitHub när PAT har konfigurerats](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Om ditt GitHub-konto har tvåfaktorautentisering aktiverat kan du skapa token på GitHub och konfigurera Jenkins för att använda den. Granska dokumentationen för [Jenkins GitHub-plugin-programmet](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) för att få fullständig information.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Förgrena exempellagringsplatsen och skapa ett Jenkins-jobb 

1. Öppna [lagringsplatsen för Spring Boot-exempelprogrammet](https://github.com/spring-guides/gs-spring-boot-docker) och förgrena den till ditt eget GitHub-konto genom att välja **Fork** (Förgrena) i det övre högra hörnet.   
    ![Förgrena från GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. I Jenkins-webbkonsolen väljer du **Nytt objekt**, ger det namnet **MyJavaApp**, väljer **Freestyle-projekt** och väljer sedan **OK**.   
    ![Nytt Jenkins Freestyle-projekt](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. I avsnittet **Allmänt** väljer du **GitHub-projekt** och anger URL:en till din förgrenade lagringsplats, till exempel https://github.com/raisa/gs-spring-boot-docker
3. I avsnittet **Källkodshantering** väljer du **Git** och anger `.git`-URL:en till din förgrenade lagringsplats, till exempel https://github.com/raisa/gs-spring-boot-docker.git
4. I avsnittet **Build Triggers** (Bygg utlösare) väljer du **GitHub hook trigger for GITscm polling** (GitHub-hookutlösare för GITScm-avsökning).
5. Under avsnittet **Build** (Bygg) väljer du **Add build step** (Lägg till byggsteg) och sedan **Invoke top-level Maven targets** (Anropa Maven-mål på toppnivå). Ange `package` i fältet **Mål**.
6. Välj **Spara**. Du kan testa ditt jobb genom att välja **Build Now** (Skapa nu) från projektsidan.

## <a name="configure-azure-app-service"></a>Konfigurera Azure App Service 

1. Med hjälp av Azure CLI eller [Cloud Shell](/azure/cloud-shell/overview) skapar du en ny [webbapp på Linux](/azure/app-service/containers/app-service-linux-intro). Webbappens namn i den här självstudien är `myJavaApp`, men du behöver använda ett unikt namn för din egen app.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Skapa en [Azure Container Registry](/azure/container-registry/container-registry-intro) för lagring av Docker-avbildningar som skapas av Jenkins. Det containerregisternamn som används i den här självstudien är `jenkinsregistry`, men du behöver använda ett unikt namn för ditt eget containerregister. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Konfigurera webbappen för att köra Docker-avbildningar som skickas till containerregistret, och ange att den app som körs i containern lyssnar efter begäranden på port 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Konfigurera Jenkins plugin-programmet för Azure App Service

1. I Jenkins-webbkonsolen väljer du det **MyJavaApp**-jobb som du skapade och väljer sedan **Konfigurera** på vänster sida på sidan.
2. Rulla ned till **Post-build Actions** (Åtgärder efter skapande) och välj sedan **Add post-build action** (Lägg till åtgärd efter skapandet) och välj **Publish an Azure Web App** (Publicera en Azure-webbapp).
3. Under **Azure Profile Configuration** (Azure-profilkonfiguration) väljer du **Lägg till** intill **Azure Credentials** (Azure-autentiseringsuppgifter) och väljer **Jenkins**.
4. I dialogrutan **Add Credentials** (Lägg till autentiseringsuppgifter) väljer du **Microsoft Azure Service Principal** (Tjänstens huvudnamn för Microsoft Azure) från listrutan **Kind** (Variant).
5. Skapa ett Active Directory-tjänsthuvudnamn från Azure CLI eller [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Ange autentiseringsuppgifterna från tjänstens huvudnamn i dialogrutan **Add credentials** (Lägg till autentiseringsuppgifter). Om du inte känner till ditt Azure-prenumerations-ID kan du fråga efter det från CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Konfigurera tjänstens huvudnamn för Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Kontrollera att tjänstens huvudnamn autentiserar med Azure genom att välja **Verify Service Principal** (Verifiera tjänstens huvudnamn). 
7. Välj **Lägg till** för att spara autentiseringsuppgifterna.
8. Välj den autentiseringsuppgift för tjänstens huvudnamn som du just lade till från listrutan **Azure Credentials** (Azure-autentiseringsuppgifter) när du är tillbaka på konfigurationen **publicera ett Azure Web Apps**.
9. I **Appkonfiguration** väljer du din resursgrupp och webbappens namn från listrutan.
10. Välj alternativknappen **Publish via Docker** (Publicera via Docker).
11. Ange `complete/Dockerfile` som **Dockerfile-sökväg**.
12. Ange `https://jenkinsregistry.azurecr.io` i fältet **Docker registry URL** (Docker-register-URL).
13. Välj **Lägg till** intill **Registry Credentials** (Registerautentiseringsuppgifter). 
14. Ange användarnamn för administratör för det Azure Container Registry som du skapade för **användarnamnet**.
15. Ange lösenordet för Azure Container-registret i fältet **Lösenord**. Du kan få ditt användarnamn och lösenord från Azure-portalen eller via följande CLI-kommando:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Lägg till dina autentiseringsuppgifter för containerregistret](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Välj **Lägg till** för att spara autentiseringsuppgiften.
16. Välj den nyligen skapade autentiseringsuppgiften från listrutan **Registry credentials** (Registerautentiseringsuppgifter) i panelen **Appkonfiguration** för **Publish an Azure Web App** (Publicera en Azure-webbapp). Den färdiga åtgärden efter skapande bör se ut som på följande bild:   
    ![Konfiguration av åtgärd efter skapande för Azure App Service-distribution](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Välj **Spara** för att spara jobbkonfigurationen.

## <a name="deploy-the-app-from-github"></a>Distribuera appen från GitHub

1. Från Jenkins-projektet väljer du **Build Now** (Skapa nu) för att distribuera exempelappen till Azure.
2. När skapandet är klart är app live på Azure vid dess distributions-URL, till exempel http://myjavaapp.azurewebsites.net.   
   ![Visa den distribuerade appen på Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Skicka ändringar och omdistribuera

1. Från din GitHub-förgrening bläddrar du på webben till `complete/src/main/java/Hello/Application.java`. Välj länken **Edit this file** (Redigera den här filen) på höger sida av GitHub-gränssnittet.
2. Gör följande ändring i metoden `home()` och checka in ändringen till lagringsplatsens huvudgren.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. En ny version startas i Jenkins som utlöses av den nya incheckningen pålagringsplatsens `master`-gren. När det är klart, läser du in appen på nytt på Azure.     
      ![Visa din distribuerade app på Azure](media/jenkins-java-quickstart/hello_docker_world.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda virtuella Azure-datorer som versionsagenter](/azure/jenkins/jenkins-azure-vm-agents)