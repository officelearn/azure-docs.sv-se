---
title: Kontinuerlig konstruktion för Linux-program med Jenkins
description: Kontinuerlig uppbyggnad och integration för ditt Service Fabric Linux-program med Jenkins
keywords: jenkins, azurblå, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77675241"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Använd Jenkins för att skapa och distribuera dina Linux-program

Den här självstudien täcker flera möjliga sätt att konfigurera din Jenkins-miljö samt olika sätt att distribuera ditt program till ett Service Fabric-kluster efter att det har byggts. Följ de här allmänna stegen för att konfigurera Jenkins, hämta ändringar från GitHub, skapa ditt program och distribuera det till klustret:

1. Se till att du installerar [förutsättningarna](#prerequisites).
1. Följ sedan stegen i något av följande avsnitt för att ställa in Jenkins:
   * [Konfigurera Jenkins i ett Service Fabric-kluster](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Konfigurera Jenkins utanför ett Service Fabric-kluster](#set-up-jenkins-outside-a-service-fabric-cluster)eller
   * [Installera plugin-programmet Service Fabric i en befintlig Jenkins-miljö](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. När du har konfigurerat Jenkins följer du stegen i [Skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job) för att konfigurera GitHub för att utlösa Jenkins när ändringar görs i ditt program och för att konfigurera jenkins-jobbpipelinen genom byggsteget för att hämta ändringarna från GitHub och skapa ditt program. 
1. Konfigurera slutligen jenkins-jobbet efter uppbyggnadssteget för att distribuera ditt program till ditt Service Fabric-kluster. Det finns två sätt att konfigurera Jenkins att distribuera ditt program till ett kluster:    
   * För utvecklings- och testmiljöer använder du [Konfigurera distribution med slutpunkten för klusterhantering](#configure-deployment-using-cluster-management-endpoint). Det här är den enklaste distributionsmetoden som ska konfigureras.
   * För produktionsmiljöer använder du [Konfigurera distribution med Azure-autentiseringsuppgifter](#configure-deployment-using-azure-credentials). Microsoft rekommenderar den här metoden för produktionsmiljöer eftersom du med Azure-autentiseringsuppgifter kan begränsa åtkomsten som ett Jenkins-jobb har till dina Azure-resurser. 

## <a name="prerequisites"></a>Krav

- Kontrollera att Git är installerat lokalt. Du kan installera rätt Git-version från [Git-sidan](https://git-scm.com/downloads) baserat på ditt operativsystem. Om du inte har tidigare i Git kan du läsa mer om det i [Git-dokumentationen](https://git-scm.com/docs).
- I den här artikeln används *exempel på komma igång för serviceinfrastruktur* på GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) för programmet att skapa och distribuera. Du kan forga den här databasen för att följa med, eller, med vissa ändringar i instruktionerna, använda ditt eget GitHub-projekt.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installera plugin-programmet Service Fabric i en befintlig Jenkins-miljö

Om du lägger till plugin-programmet Service Fabric i en befintlig Jenkins-miljö måste du göra följande:

- [Service Fabric CLI (sfctl)](../service-fabric/service-fabric-cli.md). Installera CLI på systemnivå i stället för på användarnivå, så att Jenkins kan köra CLI-kommandon. 
- Om du vill distribuera Java-program installerar du både [Gradle och Open JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Om du vill distribuera .NET Core 2.0-program installerar du [.NET Core 2.0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

När du har installerat de förutsättningar som krävs för din miljö kan du söka efter Azure Service Fabric Plugin på Jenkins-marknadsplats och installera den.

När du har installerat plugin-programmet går du vidare till [Skapa och konfigurerar ett Jenkins-jobb](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurera Jenkins i ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. Följande avsnitt visar hur du konfigurerar det i ett kluster när du använder ett Azure-lagringskonto för att spara tillståndet för behållarinstansen.

1. Kontrollera att du har ett Service Fabric Linux-kluster med Docker installerat. Service Fabric-kluster som körs i Azure har redan Docker installerat. Om du kör klustret lokalt (OneBox dev-miljö) kontrollerar du `docker info` om Docker är installerat på datorn med kommandot. Om den inte är installerad installerar du den med hjälp av följande kommandon:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Kontrollera att 8081-porten anges som en anpassad slutpunkt i klustret. Om du använder ett lokalt kluster kontrollerar du att port 8081 är öppen på värddatorn och att den har en offentlig IP-adress.
   >

1. Klona programmet med hjälp av följande kommandon:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Beständigt tillståndet för Jenkins-behållaren i en filresurs:
   1. Skapa ett Azure-lagringskonto i **samma region** som `sfjenkinsstorage1`klustret med ett namn som .
   1. Skapa en **filresurs** under lagringskontot `sfjenkins`med ett namn som .
   1. Klicka på **Anslut** för fildelning och notera de värden som visas under **Ansluta från Linux,** bör värdet se ut ungefär som den nedan:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Om du vill montera cifs-resurser måste du ha cifs-utils-paketet installerat i klusternoderna.      
   >

1. Uppdatera platshållarvärdena `setupentrypoint.sh` i skriptet med azure-storage-information från steg 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Ersätt `[REMOTE_FILE_SHARE_LOCATION]` med `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` värdet från utgången av kontakten i steg 2 ovan.
   * Ersätt `[FILE_SHARE_CONNECT_OPTIONS_STRING]` med `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` värdet från steg 2 ovan.

1. **Endast säkert kluster:** 
   
   Om du vill konfigurera distributionen av program på ett säkert kluster från Jenkins måste klustercertifikatet vara tillgängligt i Jenkins-behållaren. I filen *ApplicationManifest.xml* lägger du till den här certifikatreferensen under taggen **ContainerHostPolicies** och uppdaterar tumavtrycket med värdet för klustercertifikatet.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Lägg dessutom till följande rader under taggen **ApplicationManifest** (root) i filen *ApplicationManifest.xml* och uppdatera tumavtrycksvärdet med värdet för klustercertifikatet.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Anslut till klustret och installera behållarprogrammet.

   **Säkert kluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Kommandot Föregående tar certifikatet i PEM-format. Om certifikatet är i PFX-format kan du använda följande kommando för att konvertera det. Om PFX-filen inte är lösenordsskyddad anger du `-passin pass:`parametern **passin** som .
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Osäkert kluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Detta installerar en Jenkins-container på klustret och kan övervakas med Service Fabric Explorer.

   > [!NOTE]
   > Det kan ta ett par minuter innan Jenkins-avbildningen hämtas i klustret.
   >

1. Gå till `http://PublicIPorFQDN:8081` i webbläsaren. På sidan visas sökvägen till det ursprungliga administratörslösenordet som krävs för att logga in. 
1. Titta på Service Fabric Explorer för att avgöra vilken nod Jenkins-behållaren körs. Säker Shell (SSH) logga in på den här noden.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Hämta containerns instans-ID med `docker ps -a`.
1. Säker Shell (SSH) logga in på behållaren och klistra in sökvägen som du visades på Jenkins-portalen. Om den till exempel i portalen visar sökvägen `PATH_TO_INITIAL_ADMIN_PASSWORD`kör du följande kommandon:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. På sidan Komma igång med Jenkins väljer du alternativet Välj plugin-program för att installera, markerar kryssrutan **Ingen** och klickar på installera.
1. Skapa en användare eller välj för att fortsätta som administratör.

När du har konfigurerat Jenkins går du vidare till [Skapa och konfigurerar ett Jenkins-jobb](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurera Jenkins utanför ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar Jenkins utanför ett kluster.

1. Kontrollera att Docker är installerad `docker info` på datorn genom att köra i terminalen. Utdata anger om Docker-tjänsten körs.

1. Om Docker inte är installerat kör du följande kommandon:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Hämta containeravbildningen för Service Fabric Jenkins:`docker pull rapatchi/jenkins:latest`. Plugin-programmet för Service Fabric Jenkins är förinstallerat i avbildningen.
1. Kör containeravbildningen: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Hämta ID:t för containeravbildningsinstansen. Du kan visa en lista med alla Docker-containrar med hjälp av kommandot `docker ps –a`
1. Logga in på Jenkins-portalen med följande steg:

   1. Logga in på ett Jenkins-skal från din värd. Använd de första fyra siffrorna i behållar-ID:et. Om behållar-ID:t `2d24a73b5964`till `2d24`exempel är bruk .

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Från Jenkins-skalet får du administratörslösenordet för din behållarinstans:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Om du vill logga in på Jenkins-instrumentpanelen `http://<HOST-IP>:8080`öppnar du följande URL i en webbläsare: . Använd lösenordet från föregående steg för att låsa upp Jenkins.
   1. (Valfritt.) När du har loggat in för första gången kan du skapa ett eget användarkonto och använda det för följande steg, eller så kan du fortsätta att använda administratörskontot. Om du skapar en användare måste du fortsätta med den användaren.
1. Konfigurera GitHub så att det fungerar med Jenkins genom att använda stegen för [att generera en ny SSH-nyckel och lägga till den i SSH-agenten](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Följ instruktionerna från GitHub för att skapa SSH-nyckeln och lägga till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
   * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
   * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Kontrollera att klustret eller datorn där Jenkins-behållaravbildningen finns har en offentlig IP-adress. Detta gör att Jenkins-instansen kan ta emot meddelanden från GitHub.

När du har konfigurerat Jenkins fortsätter du till nästa avsnitt, [Skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

Stegen i det här avsnittet visar hur du konfigurerar ett Jenkins-jobb för att svara på ändringar i en GitHub-repo, hämtar ändringarna och skapar dem. I slutet av det här avsnittet dirigeras du till de sista stegen för att konfigurera jobbet för att distribuera programmet baserat på om du distribuerar till en utvecklings-/testmiljö eller till en produktionsmiljö. 

1. Klicka på Nytt **objekt**på Jenkins-instrumentpanelen .
1. Ange ett namn (till exempel **MyJob**). Välj **free-style project** (freestyle-projekt) och klicka på **OK**.
1. Sidan Jobbkonfiguration öppnas. (Om du vill komma till konfigurationen från Jenkins-instrumentpanelen klickar du på jobbet och sedan på **Konfigurera**).

1. Markera kryssrutan för **GitHub-projektet**på fliken **Allmänt** och ange GitHub-projekt-URL:en. Den här URL:en är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Välj **Git**på fliken **Hantering av källkod** . Ange URL för databasen som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Du kan också ange vilken gren `/master`som ska byggas (till exempel ).
1. Konfigurera *GitHub-databasen* så att den pratar med Jenkins:

   a. På databassidan i GitHub går du till **Inställningar** > **integreringar och tjänster**.

   b. Välj **Lägg till tjänst**, skriv **Jenkins** och välj **Jenkins GitHub-plugin-programmet**.

   c. Ange din Jenkins-webhooksadress (som standard ska den vara `http://<PublicIPorFQDN>:8081/github-webhook/`). Klicka på **Lägg till/Uppdatera tjänsten**.

   d. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och projektet skapas.

1. På fliken **Bygg triggers** i Jenkins väljer du vilket byggalternativ du vill använda. I det här exemplet vill du utlösa en version när en push till databasen inträffar, så välj **GitHub-krokutlösare för GITScm polling**. (Tidigare hette det här alternativet **Build when a change is pushed to GitHub**) (Bygg när en ändring skickas till GitHub).
1. Gör något av följande beroende på om du skapar ett Java-program eller ett .NET Core-program på fliken **Skapa:**

   * **För Java-program:** Välj **Anropa Gradle**Script i **listrutan Lägg till byggsteg** . Klicka på **Avancerat**. Ange sökvägen till Root **build-skriptet** för ditt program på den avancerade menyn. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt. För [Programmet ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)är `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`detta: .

     ![Service Fabric Jenkins Build-åtgärd](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **För .NET Core-program:** Välj **Kör Shell**i listrutan Lägg **till byggsteg** . I kommandorutan som visas måste katalogen först ändras till sökvägen där build.sh filen finns. När katalogen har ändrats kan build.sh skriptet köras och bygga programmet.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Följande skärmbild visar ett exempel på de kommandon som används för att skapa exemplet [motrtjänst](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) med ett Jenkins-jobbnamn för CounterServiceApplication.

      ![Service Fabric Jenkins Build-åtgärd](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Om du vill konfigurera Jenkins att distribuera din app till ett Service Fabric-kluster i post-build-åtgärderna behöver du platsen för klustrets certifikat i Jenkins-behållaren. Välj något av följande beroende på om Jenkins-behållaren körs i eller utanför klustret och notera platsen för klustercertifikatet:

   * **För Jenkins som körs i klustret:** Sökvägen till certifikatet kan hittas genom att upprepa värdet *för den Certificates_JenkinsOnSF_Code_MyCert_PEM* miljövariabeln inifrån behållaren.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **För Jenkins som körs utanför klustret:** Så här kopierar du klustercertifikatet till behållaren:
     1. Ditt certifikat måste vara i PEM-format. Om du inte har en PEM-fil kan du skapa en från certifikatet PFX-filen. Om PFX-filen inte är lösenordsskyddad kör du följande kommando från värden:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Om PFX-filen är lösenordsskyddad tar `-passin` du med lösenordet i parametern. Ett exempel:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Om du vill hämta behållar-ID:t för Jenkins-behållaren kör du `docker ps` från värden.
     1. Kopiera PEM-filen till behållaren med följande Docker-kommando:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Du är nästan klar! Håll Jenkins-jobbet öppet. Den enda återstående uppgiften är att konfigurera stegen efter build för att distribuera programmet till service fabric-klustret:

* Om du vill distribuera till en utvecklings- eller testmiljö följer du stegen i [Konfigurera distribution med hjälp av slutpunkten för klusterhantering](#configure-deployment-using-cluster-management-endpoint).
* Om du vill distribuera till en produktionsmiljö följer du stegen i [Konfigurera distribution med Azure-autentiseringsuppgifter](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurera distribution med slutpunkten för klusterhantering

För utvecklings- och testmiljöer kan du använda slutpunkten för klusterhantering för att distribuera ditt program. Konfigurera post-build-åtgärden med slutpunkten för klusterhantering för att distribuera ditt program kräver minst uppsättning. Om du distribuerar till en produktionsmiljö går du vidare till [Konfigurera distribution med Azure-autentiseringsuppgifter](#configure-deployment-using-azure-credentials) för att konfigurera ett huvudnamn för Azure Active Directory-tjänsten som ska användas under distributionen.    

1. Klicka på fliken **Efterbyggda åtgärder i Jenkins-jobbet.** 
1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
1. Under **Service Fabric Cluster Configuration**väljer du alternativknappen Fyll i **ändpunkt för tjänstinfrastrukturhantering.**
1. För **Hanteringsvärd**anger du anslutningsslutpunkten för klustret. till `{your-cluster}.eastus.cloudapp.azure.com`exempel .
1. För **klientnyckel** och **klientcert**anger du platsen för PEM-filen i Jenkins-behållaren. till `/var/jenkins_home/clustercert.pem`exempel . (Du kopierade platsen för certifikatet det sista steget [i Skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).)
1. Under **Programkonfiguration**konfigurerar du **fälten Programnamn**, **Programtyp**och (relativ) **sökväg till programmanifest.**

   ![Service Fabric Jenkins Post-Build-åtgärd konfigurerar hanteringsslutpunkten](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klicka på **Verifiera konfiguration**. Vid lyckad verifiering klickar du på **Spara**. Din Jenkins jobbpipeline är nu helt konfigurerad. Hoppa vidare till [Nästa steg](#next-steps) för att testa distributionen.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurera distribution med Azure-autentiseringsuppgifter

För produktionsmiljöer rekommenderas starkt att konfigurera en Azure-autentiseringsuppgifter för att distribuera ditt program. I det här avsnittet visas hur du konfigurerar ett huvudnamn för Tjänsten Azure Active Directory som ska användas för att distribuera ditt program i åtgärden efter build. Du kan tilldela tjänsthuvudnamn till roller i katalogen för att begränsa behörigheterna för Jenkins-jobbet. 

För utvecklings- och testmiljöer kan du konfigurera antingen Azure-autentiseringsuppgifter eller slutpunkten för klusterhantering för att distribuera ditt program. Mer information om hur du konfigurerar en slutpunkt för klusterhantering finns i [Konfigurera distribution med slutpunkten för klusterhantering](#configure-deployment-using-cluster-management-endpoint).   

1. Om du vill skapa ett huvudnamn för Azure Active Directory-tjänsten och tilldela den behörigheter i din Azure-prenumeration följer du stegen i [Använd portalen för att skapa ett Azure Active Directory-program och tjänsthuvudnamn](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Var uppmärksam på följande:

   * När du följer stegen i avsnittet måste du kopiera och spara följande värden: *Program-ID*, *Programnyckel*, *Katalog-ID (klient-ID)* och *Prenumerations-ID*. Du behöver dem för att konfigurera Azure-autentiseringsuppgifterna i Jenkins.
   * Om du inte har de [behörigheter som krävs](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) för katalogen måste du be en administratör att antingen ge dig behörigheterna eller skapa tjänstens huvudnamn åt dig, eller så måste du konfigurera hanteringsslutpunkten för klustret i **Post-Build-åtgärderna** för jobbet i Jenkins.
   * I avsnittet [Skapa ett Azure Active Directory-program](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) kan du ange alla välformade URL:en för **inloggnings-URL:en**.
   * I avsnittet [Tilldela program till en roll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) kan du tilldela programmet rollen *Läsare* i resursgruppen för klustret.

1. Klicka på fliken **Efterbyggningsåtgärder** igen i Jenkins-jobbet.
1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
1. Klicka på **Välj Service Fabric-kluster**under **Klusterkonfiguration**för Service Fabric. Klicka på **Lägg till** **bredvid Azure-autentiseringsuppgifter**. Klicka på **Jenkins** om du vill välja Jenkins-autentiseringsprovidern.
1. I Jenkins-autentiseringsprovidern väljer du **Microsoft Azure Service Principal** i listrutan **Typ.**
1. Använd de värden som du sparade när du konfigurerade tjänstens huvudnamn i steg 1 för att ange följande fält:

   * **Klient-ID:** *Program-ID*
   * **Klienthemlighet:** *Programnyckel*
   * **Klient-ID:** *Katalog-ID*
   * **Prenumerations-ID:** *Prenumerations-ID*
1. Ange ett beskrivande **ID** som du använder för att välja autentiseringsuppgifterna i Jenkins och en kort **beskrivning**. Klicka sedan på **Verifiera tjänstens huvudnamn**. Om verifieringen lyckas klickar du på **Lägg till**.

   ![Service Fabric Jenkins anger Azure-autentiseringsuppgifter](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Kontrollera **Service Fabric Cluster Configuration**att den nya autentiseringsuppgifterna har valts för **Azure-autentiseringsuppgifter**. 
1. Välj resursgruppen för det kluster som du vill distribuera programmet till i listrutan **Resursgrupp.**
1. Välj det kluster som du vill distribuera programmet till i listrutan **Service Fabric.**
1. För **klientnyckel** och **klientcert**anger du platsen för PEM-filen i Jenkins-behållaren. Till exempel `/var/jenkins_home/clustercert.pem`. 
1. Under **Programkonfiguration**konfigurerar du **fälten Programnamn**, **Programtyp**och (relativ) **sökväg till programmanifest.**
    ![Service Fabric Jenkins Post-Build-åtgärd – Konfigurera Azure-autentiseringsuppgifter](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klicka på **Verifiera konfiguration**. Vid lyckad verifiering klickar du på **Spara**. Din Jenkins jobbpipeline är nu helt konfigurerad. Fortsätt till [Nästa steg](#next-steps) för att testa distributionen.

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på några buggar med Jenkins plugins, lämna in ett problem i [Jenkins JIRA](https://issues.jenkins-ci.org/) för den specifika komponenten.

## <a name="ideas-to-try"></a>Idéer att prova

GitHub och Jenkins har nu konfigurerats. Överväg att göra `reliable-services-actor-sample/Actors/ActorCounter` några exempeländringar i projektet https://github.com/Azure-Samples/service-fabric-java-getting-startedi gaffeln i databasen. Skicka ändringarna till `master` fjärrgrenen (eller någon gren som du har konfigurerat för att fungera med). Detta utlöser Jenkins-jobbet (`MyJob`) som du konfigurerade. Ändringarna hämtas från GitHub, skapar dem och distribuerar programmet till det kluster som du angav i åtgärder efter uppbyggnaden.  

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/Jenkins/)