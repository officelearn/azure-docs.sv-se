---
title: "Använda Jenkins för att distribuera ditt webbprogram till Azure | Microsoft Docs"
description: "Ställ in kontinuerlig integration från GitHub till Azure App Service för din Java-webbappar med Jenkins och Docker."
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: 
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: 3a2635ac968d843226f05dc51cf4a5f078235c11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Ställ in kontinuerlig integrering och distribution till Azure App Service med Jenkins

Den här självstudiekursen ställer in kontinuerlig integrering och distribution (CI/CD) av en exempelwebbapp för Java som utvecklats med den [Vårversionen Start](http://projects.spring.io/spring-boot/) framework [Azure App Service Web App på Linux](/azure/app-service/containers/app-service-linux-intro) med Jenkins.

Du utför följande uppgifter i den här självstudiekursen:

> [!div class="checklist"]
> * Installera Jenkins plugin-program krävs för att distribuera till Azure App Service.
> * Definiera ett Jenkins jobb för att skapa Docker-avbildningar från en GitHub-repo-när ett nytt genomförande skickas.
> * Definiera en ny Azure-Webbapp för Linux och konfigurera den för att distribuera Docker-avbildningar pushas till Azure-behållare registret.
> * Konfigurera Azure App Service Jenkins plugin-programmet.
> * Distribuera sample-appen till Azure App Service med en manuell version.
> * Utlös en Jenkins version och uppdatera webbappen genom att skicka ändringarna till GitHub.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här kursen behöver du:

* [Jenkins](https://jenkins.io/) med JDK och Maven verktyg som har konfigurerats. Om du inte har ett Jenkins system kan skapa en nu i Azure från den [Jenkins lösningsmall](/azure/jenkins/install-jenkins-solution-template).
* En [GitHub](https://github.com) konto.
* [Azure CLI 2.0](/cli/azure/overview), antingen från din lokala kommandoraden eller i den [Azure Cloud Shell](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Installera Jenkins plugin-program

1. Öppna en webbläsare för att webbkonsolen Jenkins och välj **hantera Jenkins** i den vänstra menyn, Välj **hantera plugin-program**.
2. Välj den **tillgänglig** fliken.
3. Leta upp och markera kryssrutan bredvid följande plugin-program:   

    - [Azure Apptjänst plugin-program](https://plugins.jenkins.io/azure-app-service)
    - [GitHub gren källa plugin-program](https://plugins.jenkins.io/github-branch-source)

    Om de plugin-program inte visas, kontrollera att de inte redan har installerats genom att kontrollera den **installerad** fliken.

1. Välj **nu hämta och installera efter omstart** att aktivera plugin-program i konfigurationen Jenkins.

## <a name="configure-github-and-jenkins"></a>Konfigurera GitHub och Jenkins

Konfigurera Jenkins att ta emot [GitHub webhooks](https://developer.github.com/webhooks/) när nya incheckningar pushas till en lagringsplatsen i ditt konto.

1. Välj **hantera Jenkins**, sedan **konfigurera systemet**. I den **GitHub** Kontrollera **hantera hook** är markerad och välj sedan **hanterar ytterligare åtgärder för GitHub** och välj **konvertera inloggning och lösenord för att token**.
2. Välj den **från inloggningsnamn och lösenord** alternativknappen och ange ditt GitHub-användarnamn och lösenord. Välj **skapa token autentiseringsuppgifter** att skapa en ny [GitHub personlig åtkomst-Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Skapa GitHub klappa lätt från inloggningsnamn och lösenord](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Välj nyligen skapade token från den **autentiseringsuppgifter** listrutan i GitHub servrar konfigurationen. Välj **Anslutningstestet** att verifiera att autentiseringen fungerar.   
   ![Verifiera anslutning till GitHub när PATRIK har konfigurerats](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Om ditt GitHub-konto har tvåfaktorsautentisering aktiverad, skapa token på GitHub och konfigurera Jenkins för att använda den. Granska de [Jenkins GitHub plugin](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) dokumentationen för fullständig information.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Duplicera exempel lagringsplatsen och skapa ett Jenkins-jobb 

1. Öppna den [Vårversionen start exempel programmet lagringsplatsen](https://github.com/spring-guides/gs-spring-boot-docker) och duplicera den egna GitHub-konto genom att välja **Återställningsförgreningar** i det övre högra hörnet.   
    ![Förgrening från GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. Välj i webbkonsolen Jenkins **nytt objekt**, ge det ett namn **MyJavaApp**väljer **Freestyle projektet**och välj **OK**.   
    ![Nytt Jenkins Freestyle-projekt](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Under den **allmänna** väljer **GitHub** projektet och ange din andelen vridvuxna lagringsplatsen URL, till exempel https://github.com/raisa/gs-spring-boot-docker
3. Under den **datakällan kod management** väljer **Git**, ange din andelen vridvuxna lagringsplatsen `.git` URL, till exempel https://github.com/raisa/gs-spring-boot-docker.git
4. Under den **Skapa utlösare** väljer **GitHub hook utlösare för GITscm avsökning**.
5. Under den **skapa** väljer **Lägg till build steg** och välj **anropa översta Maven mål**. Ange `package` i den **mål** fältet.
6. Välj **Spara**. Du kan testa ditt jobb genom att välja **skapa nu** från sidan projekt.

## <a name="configure-azure-app-service"></a>Konfigurera Azure App Service 

1. Med hjälp av Azure CLI eller [moln Shell](/azure/cloud-shell/overview), skapa en ny [webbprogrammet på Linux](/azure/app-service/containers/app-service-linux-intro). Webbprogramnamnet i den här kursen är `myJavaApp`, men du måste använda ett unikt namn för din egen app.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan
    ```

2. Skapa en [Azure Container registret](/azure/container-registry/container-registry-intro) att lagra Docker-bilder med Jenkins. Registret behållarnamn används i den här kursen är `jenkinsregistry`, men du måste använda ett unikt namn för behållaren registret. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Konfigurera webbappen för att köra Docker bilder pushas till registret för behållaren och ange att appen körs i behållaren lyssnar efter förfrågningar på port 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Konfigurera Azure App Service Jenkins plugin-program

1. I webbkonsolen Jenkins väljer du den **MyJavaApp** jobbet du skapade och välj sedan **konfigurera** på vänster sida av sidan.
2. Rulla ned till **efter build åtgärder**och välj **Lägg till efter build åtgärd** och välj **publicera ett Azure Web App**.
3. Under **Azure Profilkonfigurationen**väljer **Lägg till** bredvid **Azure-autentiseringsuppgifterna** och välj **Jenkins**.
4. I den **lägga till autentiseringsuppgifter** markerar **Microsoft Azure Service Principal** från den **typ** listrutan.
5. Skapa en Active Directory-tjänstens huvudnamn från Azure CLI eller [moln Shell](/azure/cloud-shell/overview).
    
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
6. Ange autentiseringsuppgifterna från tjänsten huvudnamn i den **lägga till autentiseringsuppgifter** dialogrutan. Om du inte vet Azure prenumerations-ID, kan den fråga från CLI:
     
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

    ![Konfigurera Azure tjänstens huvudnamn](media/jenkins-java-quickstart/azure_service_principal.png)
6. Kontrollera tjänstens huvudnamn autentiserar med Azure genom att välja **Kontrollera tjänstens huvudnamn**. 
7. Välj **Lägg till** spara autentiseringsuppgifterna.
8. Välj service principal autentiseringsuppgifter som du just har lagt till från den **Azure-autentiseringsuppgifterna** listrutan när du är tillbaka till den **publicera ett Azure Web App** konfiguration.
9. I **Appkonfiguration**, Välj din resursgrupp och webbappens namn från listrutan.
10. Välj den **publicera via Docker** knappen.
11. Ange `complete/Dockerfile` för **Dockerfile sökvägen**.
12. Ange `https://jenkinsregistry.azurecr.io` i den **Docker registret URL** fältet.
13. Välj **Lägg till** bredvid **registret autentiseringsuppgifter**. 
14. Ange administratörsanvändarnamnet för i registret för Azure-behållare som du skapade för den **användarnamn**.
15. Ange lösenordet för Azure-behållare registret i den **lösenord** fältet. Du kan hämta ditt användarnamn och lösenord från Azure-portalen eller via följande CLI-kommando:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Lägg till autentiseringsuppgifterna behållaren registret](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Välj **Lägg till** att spara autentiseringsuppgiften.
16. Markera den nyligen skapade referensen från den **registret autentiseringsuppgifter** listrutan i den **Appkonfiguration** panelen för den **publicera ett Azure Web App**. Den färdiga efter build-åtgärden bör se ut som följande bild:   
    ![Post build konfigurationen för att distribuera till Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Välj **spara** spara jobbkonfigurationen av.

## <a name="deploy-the-app-from-github"></a>Distribuera appen från GitHub

1. Välj projektet Jenkins **skapa nu** att distribuera sample-appen till Azure.
2. Appen är aktiv på Azure på dess publishing URL, till exempel http://myjavaapp.azurewebsites.net när versionen har slutförts.   
   ![Visa den distribuerade appen på Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Skicka ändringar och distribuera

1. Bläddra på webben till dina Github-förgrening `complete/src/main/java/Hello/Application.java`. Välj den **redigera filen** länk från höger sida av GitHub-gränssnittet.
2. Ändra följande till den `home()` metoden och spara ändringen till mastergrenen på lagringsplatsen.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. En ny version startar i Jenkins som utlöses av nya genomförandet på den `master` grenen av lagringsplatsen. När den är klar kan du ladda din app på Azure.     
      ![Visa den distribuerade appen på Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Nästa steg

- [Använda Azure virtuella datorer medan skapar agenter](/azure/jenkins/jenkins-azure-vm-agents)
- [Hantera resurser i jobb och pipelines med Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline)
 
