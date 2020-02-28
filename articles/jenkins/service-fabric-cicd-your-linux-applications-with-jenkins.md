---
title: Kontinuerlig build för Linux-program med Jenkins
description: Kontinuerlig build och integrering för ditt Service Fabric Linux-program med Jenkins
keywords: Jenkins, Azure, DevOps, cicd, Linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675241"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Använd Jenkins för att bygga och distribuera Linux-program

I den här självstudien beskrivs flera olika sätt att konfigurera din Jenkins-miljö samt olika sätt att distribuera programmet till ett Service Fabric kluster när det har skapats. Följ dessa allmänna steg för att konfigurera Jenkins, Hämta ändringar från GitHub, skapa ditt program och distribuera det till klustret:

1. Se till att du installerar [nödvändiga komponenter](#prerequisites).
1. Följ sedan stegen i något av följande avsnitt för att konfigurera Jenkins:
   * [Konfigurera Jenkins i ett Service Fabric kluster](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Konfigurera Jenkins utanför ett Service Fabric kluster](#set-up-jenkins-outside-a-service-fabric-cluster)eller
   * [Installera Service Fabric-plugin-programmet i en befintlig Jenkins-miljö](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. När du har konfigurerat Jenkins följer du stegen i [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job) för att ställa in GitHub för att utlösa Jenkins när ändringar görs i programmet och för att konfigurera din Jenkins jobb-pipeline genom build-steget för att hämta ändringarna från GitHub och skapa ditt program. 
1. Konfigurera slutligen Jenkins-jobbet efter build-steget för att distribuera programmet till ditt Service Fabric-kluster. Det finns två sätt att konfigurera Jenkins för att distribuera programmet till ett kluster:    
   * För utvecklings-och test miljöer använder du [Konfigurera distribution med hjälp av kluster hanterings slut punkt](#configure-deployment-using-cluster-management-endpoint). Det här är den enklaste distributions metoden att konfigurera.
   * För produktions miljöer använder du [Konfigurera distribution med Azure-autentiseringsuppgifter](#configure-deployment-using-azure-credentials). Microsoft rekommenderar den här metoden för produktions miljöer eftersom du kan begränsa åtkomsten som ett Jenkins-jobb har till dina Azure-resurser med Azure-autentiseringsuppgifter. 

## <a name="prerequisites"></a>Förutsättningar

- Kontrol lera att git är installerat lokalt. Du kan installera rätt git-version från [sidan git-nedladdningar](https://git-scm.com/downloads) baserat på operativ systemet. Om du inte har använt git igen kan du läsa mer om det i [git-dokumentationen](https://git-scm.com/docs).
- Den här artikeln använder *Service Fabric komma igång-exemplet* på GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) för programmet för att bygga och distribuera. Du kan använda ditt eget GitHub-projekt för att förgrena den här lagrings platsen eller, med vissa ändringar i anvisningarna.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installera Service Fabric-plugin-programmet i en befintlig Jenkins-miljö

Om du lägger till Service Fabric-plugin-programmet i en befintlig Jenkins-miljö måste du utföra följande steg:

- [Service Fabric CLI (sfctl)](../service-fabric/service-fabric-cli.md). Installera CLI på system nivå i stället för på användar nivå, så att Jenkins kan köra CLI-kommandon. 
- Installera både [Gradle och öppna JDK 8,0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development)för att distribuera Java-program. 
- Installera .net Core [2,0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development)för att distribuera .net Core 2,0-program. 

När du har installerat de nödvändiga förutsättningarna för din miljö kan du söka efter Azure Service Fabric-plugin-programmet i Jenkins Marketplace och installera det.

När du har installerat plugin-programmet går du vidare till [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurera Jenkins i ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar det i ett kluster när du använder ett Azure Storage-konto för att spara behållar instansens tillstånd.

1. Se till att du har ett Service Fabric Linux-kluster med Docker installerat. Docker har redan Docker installerat på Service Fabric kluster som körs i Azure. Om du kör klustret lokalt (Onebox behållaravbildningen dev Environment) kontrollerar du om Docker är installerat på datorn med kommandot `docker info`. Om den inte är installerad installerar du den med hjälp av följande kommandon:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Kontrol lera att 8081-porten har angetts som en anpassad slut punkt i klustret. Om du använder ett lokalt kluster ser du till att port 8081 är öppen på värddatorn och att den har en offentlig IP-adress.
   >

1. Klona programmet med hjälp av följande kommandon:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Behåll statusen för Jenkins-behållaren i en fil resurs:
   1. Skapa ett Azure Storage-konto i **samma region** som klustret med ett namn som `sfjenkinsstorage1`.
   1. Skapa en **fil resurs** under lagrings kontot med ett namn som `sfjenkins`.
   1. Klicka på **Anslut** för fil resursen och anteckna värdena som visas under **anslutning från Linux**. värdet bör se ut ungefär så här:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Om du vill montera CIFS-resurser måste du ha CIFS-utils-paketet installerat i klusternoderna.      
   >

1. Uppdatera plats hållarnas värden i `setupentrypoint.sh`-skriptet med informationen om Azure-lagring från steg 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Ersätt `[REMOTE_FILE_SHARE_LOCATION]` med värdet `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` från utdata från anslutningen i steg 2 ovan.
   * Ersätt `[FILE_SHARE_CONNECT_OPTIONS_STRING]` med värdet `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` från steg 2 ovan.

1. **Endast säkra kluster:** 
   
   Om du vill konfigurera distributionen av program i ett säkert kluster från Jenkins måste kluster certifikatet vara tillgängligt i Jenkins-behållaren. I filen *ApplicationManifest. XML* går du till **ContainerHostPolicies** -taggen och lägger till den här certifikat referensen och uppdaterar tumavtrycket med klustrets certifikats värde.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Lägg dessutom till följande rader under taggen **ApplicationManifest** (root) i filen *ApplicationManifest. XML* och uppdatera tumavtrycket med klustrets certifikats värde.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Anslut till klustret och installera behållar programmet.

   **Säkert kluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Föregående kommando tar certifikatet i PEM-format. Om ditt certifikat är i PFX-format kan du använda följande kommando för att konvertera det. Om PFX-filen inte är lösenordsskyddad, anger du parametern **Passin** som `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Oskyddat kluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Detta installerar en Jenkins-container på klustret och kan övervakas med Service Fabric Explorer.

   > [!NOTE]
   > Det kan ta några minuter innan Jenkins-avbildningen har laddats ned i klustret.
   >

1. Gå till `http://PublicIPorFQDN:8081` i webbläsaren. På sidan visas sökvägen till det ursprungliga administratörslösenordet som krävs för att logga in. 
1. Titta på Service Fabric Explorer för att avgöra vilken nod som behållaren Jenkins körs på. Secure Shell (SSH)-inloggning till den här noden.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Hämta containerns instans-ID med `docker ps -a`.
1. Secure Shell (SSH)-inloggning till behållaren och klistra in den sökväg som visades i Jenkins-portalen. Om till exempel i portalen visar sökvägen `PATH_TO_INITIAL_ADMIN_PASSWORD`kör du följande kommandon:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. På sidan Jenkins Komma igång väljer du alternativet Välj plugin-program att installera, markerar kryss rutan **ingen** och klickar på installera.
1. Skapa en användare eller Välj att fortsätta som administratör.

När du har konfigurerat Jenkins går du vidare till [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurera Jenkins utanför ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar Jenkins utanför ett kluster.

1. Se till att Docker är installerat på datorn genom att köra `docker info` i terminalen. Utdata anger om Docker-tjänsten körs.

1. Om Docker inte är installerat kör du följande kommandon:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Hämta containeravbildningen för Service Fabric Jenkins:`docker pull rapatchi/jenkins:latest`. Plugin-programmet för Service Fabric Jenkins är förinstallerat i avbildningen.
1. Kör containeravbildningen: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Hämta ID:t för containeravbildningsinstansen. Du kan visa en lista med alla Docker-containrar med hjälp av kommandot `docker ps –a`
1. Logga in på Jenkins-portalen med följande steg:

   1. Logga in på ett Jenkins-gränssnitt från värden. Använd de första fyra siffrorna i container-ID: t. Om behållar-ID: t är `2d24a73b5964`använder du `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Hämta administratörs lösen ordet för din behållar instans från Jenkins-gränssnittet:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Öppna följande URL i en webbläsare om du vill logga in på Jenkins-instrumentpanelen: `http://<HOST-IP>:8080`. Använd lösen ordet från föregående steg för att låsa upp Jenkins.
   1. (Valfritt) När du har loggat in för första gången kan du skapa ett eget användar konto och använda det för följande steg, eller så kan du fortsätta att använda administratörs kontot. Om du skapar en användare måste du fortsätta med den användaren.
1. Konfigurera GitHub så att det fungerar med Jenkins genom att följa stegen i [skapa en ny SSH-nyckel och lägga till den i SSH-agenten](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Följ instruktionerna från GitHub för att skapa SSH-nyckeln och lägga till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
   * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
   * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Kontrol lera att klustret eller datorn där Jenkins behållar avbildningen har en offentlig IP-adress. Detta gör att Jenkins-instansen kan ta emot meddelanden från GitHub.

När du har konfigurerat Jenkins kan du fortsätta till nästa avsnitt, [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

Stegen i det här avsnittet visar hur du konfigurerar ett Jenkins-jobb som svarar på ändringar i en GitHub-lagrings platsen, hämtar ändringarna och skapar dem. I slutet av det här avsnittet dirigeras du till de sista stegen för att konfigurera jobbet för att distribuera programmet baserat på om du distribuerar till en utvecklings-/test miljö eller till en produktions miljö. 

1. Klicka på **nytt objekt**på Jenkins-instrumentpanelen.
1. Ange ett namn (till exempel **MyJob**). Välj **free-style project** (freestyle-projekt) och klicka på **OK**.
1. Sidan jobb konfiguration öppnas. (Du kommer till konfigurationen från Jenkins-instrumentpanelen genom att klicka på jobbet och sedan på **Konfigurera**).

1. På fliken **Allmänt** markerar du kryss rutan för **GitHub-projekt**och anger URL: en för GitHub-projektet. Den här URL:en är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Välj **git**på fliken **hantering av käll kods hantering** . Ange URL för databasen som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Du kan också ange vilken gren som ska byggas (till exempel `/master`).
1. Konfigurera din *GitHub* -lagringsplats för att prata med Jenkins:

   a. På sidan GitHub-lagringsplats går du till **inställningar** > **integreringar och tjänster**.

   b. Välj **Lägg till tjänst**, skriv **Jenkins** och välj **Jenkins GitHub-plugin-programmet**.

   c. Ange din Jenkins-webhooksadress (som standard ska den vara `http://<PublicIPorFQDN>:8081/github-webhook/`). Klicka på **Lägg till/Uppdatera tjänsten**.

   d. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och projektet skapas.

1. På fliken **build-utlösare** i Jenkins väljer du vilket build-alternativ du vill använda. I det här exemplet vill du aktivera en version när en push-överföring till databasen sker, så välj **GitHub Hook-utlösare för gitscm polling (-avsökning**. (Tidigare hette det här alternativet **Build when a change is pushed to GitHub**) (Bygg när en ändring skickas till GitHub).
1. På fliken **skapa** gör du något av följande, beroende på om du skapar ett Java-program eller ett .net Core-program:

   * **För Java-program:** I list rutan **Lägg till build-steg** väljer du **anropa Gradle-skript**. Klicka på **Avancerat**. I menyn Avancerat anger du sökvägen till **rot versions skriptet** för ditt program. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt. För [ActorCounter-programmet](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)är detta: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric Jenkins Build-åtgärd](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **För .net Core-program:** I list rutan **Lägg till build-steg** väljer du **Kör gränssnitt**. I kommando rutan som visas måste katalogen först ändras till den sökväg där build.sh-filen finns. När katalogen har ändrats kan build.sh-skriptet köras och programmet skapas.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Följande skärm bild visar ett exempel på de kommandon som används för att bygga [Counter service-](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) exemplet med ett Jenkins jobbnamn på CounterServiceApplication.

      ![Service Fabric Jenkins Build-åtgärd](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Om du vill konfigurera Jenkins för att distribuera din app till ett Service Fabric kluster i åtgärder efter skapande måste du ha platsen för klustrets certifikat i din Jenkins-behållare. Välj något av följande beroende på om din Jenkins-behållare körs i eller utanför klustret och notera platsen för kluster certifikatet:

   * **För Jenkins som körs i klustret:** Sökvägen till certifikatet kan hittas genom att värdet för *Certificates_JenkinsOnSF_Code_MyCert_PEM* -miljövariabeln anges i behållaren.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **För Jenkins som körs utanför klustret:** Följ dessa steg om du vill kopiera kluster certifikatet till din behållare:
     1. Ditt certifikat måste vara i PEM-format. Om du inte har en PEM-fil kan du skapa en från certifikatets PFX-fil. Om PFX-filen inte är lösenordsskyddad, kör du följande kommando från värden:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Om PFX-filen är lösenordsskyddad tar du med lösen ordet i parametern `-passin`. Några exempel:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Kör `docker ps` från värden för att hämta behållar-ID för din Jenkins-behållare.
     1. Kopiera PEM-filen till din behållare med följande Docker-kommando:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Du är nästan klar! Låt Jenkins-jobbet vara öppet. Den enda återstående aktiviteten är att konfigurera de steg som krävs för att distribuera programmet till Service Fabric klustret:

* Om du vill distribuera till en utvecklings-eller test miljö följer du stegen i [Konfigurera distribution med hjälp av kluster hanterings slut punkt](#configure-deployment-using-cluster-management-endpoint).
* Om du vill distribuera till en produktions miljö följer du stegen i [Konfigurera distribution med Azure-autentiseringsuppgifter](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurera distribution med hjälp av kluster hanterings slut punkt

För utvecklings-och test miljöer kan du använda slut punkten för kluster hantering för att distribuera programmet. Konfiguration av åtgärden efter build med slut punkten för kluster hantering för att distribuera programmet kräver minst mängd konfiguration. Om du distribuerar till en produktions miljö kan du gå vidare till [Konfigurera distribution med Azure-autentiseringsuppgifter](#configure-deployment-using-azure-credentials) för att konfigurera en Azure Active Directory tjänstens huvud namn som ska användas under distributionen.    

1. Klicka på fliken **åtgärder efter version** i Jenkins-jobbet. 
1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
1. Under **Service Fabric kluster konfiguration**väljer du alternativ knappen **Fyll Service Fabric hanterings slut punkt** .
1. För **hanterings värden**anger du anslutnings slut punkten för klustret. till exempel `{your-cluster}.eastus.cloudapp.azure.com`.
1. För **klient nyckel** och **klient certifikat**anger du platsen för PEM-filen i Jenkins-behållaren. till exempel `/var/jenkins_home/clustercert.pem`. (Du har kopierat platsen för certifikatet till det sista steget i [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).)
1. Under **program konfiguration**konfigurerar du **program namn**, **program typ**och (relativ) **sökvägen till program manifest** fält.

   ![Service Fabric Jenkins efter build-åtgärd konfigurera hanterings slut punkt](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klicka på **Verifiera konfiguration**. När verifieringen är klar klickar du på **Spara**. Din pipeline för Jenkins-jobbet har nu kon figurer ATS helt. Gå vidare till [Nästa steg](#next-steps) för att testa distributionen.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurera distribution med Azure-autentiseringsuppgifter

För produktions miljöer rekommenderar vi att du konfigurerar en Azure-autentiseringsuppgift för att distribuera programmet. I det här avsnittet visas hur du konfigurerar ett Azure Active Directory tjänstens huvud namn som ska användas för att distribuera programmet i åtgärds åtgärden efter kompileringen. Du kan tilldela tjänstens huvud namn till roller i din katalog för att begränsa behörigheterna för Jenkins-jobbet. 

I utvecklings-och test miljöer kan du konfigurera antingen Azure-autentiseringsuppgifter eller slut punkten för kluster hantering för att distribuera programmet. Mer information om hur du konfigurerar en slut punkt för kluster hantering finns i [Konfigurera distribution med slut punkt för kluster hantering](#configure-deployment-using-cluster-management-endpoint).   

1. Om du vill skapa en Azure Active Directory tjänstens huvud namn och tilldela IT-behörigheter i din Azure-prenumeration följer du stegen i [använda portalen för att skapa ett Azure Active Directory program och tjänstens huvud namn](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Observera följande:

   * När du följer stegen i avsnittet måste du kopiera och spara följande värden: *program-ID*, *program nyckel*, *katalog-ID (klient-ID)* och *prenumerations-ID*. Du behöver dem för att konfigurera Azure-autentiseringsuppgifterna i Jenkins.
   * Om du inte har de [behörigheter som krävs](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) för din katalog måste du be en administratör antingen bevilja dig behörigheterna eller skapa tjänstens huvud namn för dig, eller så måste du konfigurera hanterings slut punkten för klustret i **åtgärder efter skapandet** för jobbet i Jenkins.
   * I avsnittet [skapa ett Azure Active Directory program](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) kan du ange en korrekt formaterad URL för **inloggnings-URL: en**.
   * I avsnittet [tilldela program till en roll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) kan du tilldela programmet rollen *läsare* i resurs gruppen för klustret.

1. Gå tillbaka till Jenkins-jobbet och klicka på fliken **åtgärder efter version** .
1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
1. Under **Service Fabric kluster konfiguration**klickar **du på Välj Service Fabric klustret**. Klicka på **Lägg till** bredvid **autentiseringsuppgifter för Azure**. Klicka på **Jenkins** för att välja providern för Jenkins-autentiseringsuppgifter.
1. I Jenkins-providern för autentiseringsuppgifter väljer du **Microsoft Azure tjänstens huvud namn** från List rutan **typ** .
1. Använd de värden som du sparade när du konfigurerade tjänstens huvud namn i steg 1 för att ange följande fält:

   * **Klient-ID**: *program-ID*
   * **Klient hemlighet**: *program nyckel*
   * **Klient-ID**: *katalog-ID*
   * **Prenumerations-ID**: *prenumerations-ID*
1. Ange ett beskrivande **ID** som du använder för att välja autentiseringsuppgifter i Jenkins och en kort **Beskrivning**. Klicka sedan på **Verifiera tjänstens huvud namn**. Om verifieringen lyckas klickar du på **Lägg till**.

   ![Service Fabric Jenkins ange Azure-autentiseringsuppgifter](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Se till att den nya autentiseringsuppgiften är vald för **Azure-autentiseringsuppgifter**under **Service Fabric kluster konfiguration**. 
1. I list rutan **resurs grupp** väljer du resurs gruppen för det kluster som du vill distribuera programmet till.
1. I list rutan **Service Fabric** väljer du det kluster som du vill distribuera programmet till.
1. För **klient nyckel** och **klient certifikat**anger du platsen för PEM-filen i Jenkins-behållaren. Till exempel `/var/jenkins_home/clustercert.pem`. 
1. Under **program konfiguration**konfigurerar du **program namn**, **program typ**och (relativ) **sökvägen till program manifest** fält.
    ![Service Fabric Jenkins efter build-åtgärd – konfigurera Azure-autentiseringsuppgifter](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klicka på **Verifiera konfiguration**. När verifieringen är klar klickar du på **Spara**. Din pipeline för Jenkins-jobbet har nu kon figurer ATS helt. Fortsätt till [Nästa steg](#next-steps) för att testa distributionen.

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av Jenkins-plugin-programmet

Om du stöter på buggar med Jenkins-plugin-programmet kan du rapportera problemet i [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="ideas-to-try"></a>Förslag på försök

GitHub och Jenkins har nu konfigurerats. Överväg att göra några exempel ändringar i `reliable-services-actor-sample/Actors/ActorCounter`-projektet i din förgrening av lagrings platsen https://github.com/Azure-Samples/service-fabric-java-getting-started. Skicka ändringarna till den fjärranslutna `master` grenen (eller en gren som du har konfigurerat för att arbeta med). Detta utlöser Jenkins-jobbet (`MyJob`) som du konfigurerade. Den hämtar ändringarna från GitHub, skapar dem och distribuerar programmet till det kluster som du angav i åtgärder efter skapandet.  

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Jenkins på Azure](/azure/Jenkins/)