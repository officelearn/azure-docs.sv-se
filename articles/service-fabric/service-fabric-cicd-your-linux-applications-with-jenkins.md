---
title: Kontinuerlig bygg- och -integrering för dina Azure Service Fabric Linux-program med Jenkins | Microsoft Docs
description: Kontinuerlig bygg- och -integrering för ditt Service Fabric Linux-program med hjälp av Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 3ee4041390c9452153a7a276b3d5da66249dbde6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Använd Jenkins för att skapa och distribuera Linux-program
Jenkins är ett populärt verktyg för kontinuerlig integrering och distribution av appar. Så här skapar och distribuerar du ett Azure Service Fabric-program med Jenkins.

## <a name="topic-overview"></a>Avsnittet Översikt
Den här artikeln täcker flera möjliga sätt att ställa in miljön Jenkins samt olika sätt att distribuera programmet till ett Service Fabric-kluster när den har skapats. Följ dessa allmänna steg när du har installera Jenkins, hämtar ändringar från GitHub, skapa programmet och distribuera den till klustret:

1. Kontrollera att du installerar den [krav](#prerequisites).
2. Följ stegen i ett av dessa avsnitt för att ställa in Jenkins:
   * [Ställ in Jenkins i ett Service Fabric-kluster](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Ställ in Jenkins utanför ett Service Fabric-kluster](#set-up-jenkins-outside-a-service-fabric-cluster), eller
   * [Installera Service Fabric plugin-programmet i en befintlig miljö Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
3. När du har konfigurerat Jenkins följer du stegen i [skapa och konfigurera ett Jenkins jobb](#create-and-configure-a-jenkins-job) att ställa in GitHub att utlösa Jenkins när ändringar görs i ditt program och konfigurera din Jenkins jobbet pipeline igenom build-steg för att dra den ändringar från GitHub och skapa din app. 
4. Konfigurera slutligen Jenkins jobbet efter build steg om du vill distribuera programmet till Service Fabric-klustret. Det finns två sätt att konfigurera Jenkins om du vill distribuera programmet till ett kluster:    
   * Använd för utvecklings- och testmiljöer [konfigurera distribution med hantering av klusterslutpunkten](#configure-deployment-using-cluster-management-endpoint). Det här är den enklaste distributionsmetoden att ställa in.
   * Produktionsmiljöer, Använd [konfigurera distribution med autentiseringsuppgifter för Azure](#configure-deployment-using-azure-credentials). Microsoft rekommenderar den här metoden för produktionsmiljöer eftersom med autentiseringsuppgifter för Azure kan du begränsa åtkomsten som en Jenkins jobbet har till Azure-resurser. 

## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att Git installeras lokalt. Du kan installera Git rätt version från [Git hämtar sidan](https://git-scm.com/downloads) baserat på ditt operativsystem. Om du har använt Git mer information om den från den [Git dokumentationen](https://git-scm.com/docs).
- Den här artikeln används den *Service Fabric komma igång exempel* på GitHub: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) för programmet för att skapa och distribuera. Du kan duplicera den här lagringsplatsen för att följa instruktionerna eller använda din egen GitHub-projekt med vissa ändringar av instruktionerna.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installera Service Fabric-plugin-programmet i en befintlig Jenkins-miljö
Om du vill lägga till Service Fabric-plugin-programmet i en befintlig Jenkins miljö, behöver du följande:

- Den [Service Fabric CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Se till att installera CLI på systemnivå snarare än på användarnivå, så Jenkins kan köra CLI-kommandona. 
   >

- Om du vill distribuera Java-program, installera både [Gradle och öppna JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Om du vill distribuera .NET Core 2.0-program, installera den [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

När du har installerat kraven för din miljö kan du söka efter Azure Service Fabric plugin-programmet i Jenkins marketplace och installera den.

När du har installerat plugin-programmet kan du gå vidare till [skapa och konfigurera ett jobb för Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurera Jenkins i ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. Följande avsnitt visar hur du konfigurerar det i ett kluster när ett Azure storage-konto för att spara tillståndet för behållaren-instans.

### <a name="prerequisites"></a>Förutsättningar
- Har ett Service Fabric Linux-kluster med Docker installerad. Service Fabric-kluster som körs i Azure redan har installerat Docker. Om du kör klustret lokalt (OneBox utvecklingsmiljö), kontrollera om Docker är installerat på datorn med den `docker info` kommando. Om den inte är installerad installerar du det med hjälp av följande kommandon:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Kontrollera att 8081 port har angetts som en anpassad slutpunkt i klustret. Om du använder lokala klustret kan du kontrollera att port 8081 är öppen på värddatorn och att den har en offentlig IP-adress.
   >

### <a name="steps"></a>Steg
1. Klona programmet, med hjälp av följande kommandon:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

3. Spara tillståndet för behållaren Jenkins i en filresurs:
   1. Skapa ett Azure storage-konto i den **samma region** som klustret med ett namn som `sfjenkinsstorage1`.
   2. Skapa en **filresurs** under lagringen konto med ett namn som `sfjenkins`.
   3. Klicka på **Anslut** för filresurser och Observera värdena visas **ansluter från Linux**, värdet bör likna exemplet nedan:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Att montera cifs-resurser som du behöver ha cifs-verktyg för webbplatsuppgradering paketet installerad på klusternoderna.      
   >

4. Uppdatera platshållarvärdena i den `setupentrypoint.sh` skriptet med azure storage-information från steg 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Ersätt `[REMOTE_FILE_SHARE_LOCATION]` med värdet `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` från utdata från connect i steg 2 ovan.
   * Ersätt `[FILE_SHARE_CONNECT_OPTIONS_STRING]` med värdet `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` från steg 2 ovan.

5. **Säker kluster:** 
   
   För att konfigurera distribution av program på en säker kluster från Jenkins måste klustret vara tillgänglig i Jenkins-behållaren. I den *ApplicationManifest.xml* filen under den **ContainerHostPolicies** taggen lägga till den här referensen för certifikatet och uppdatera värdet tumavtrycket med certifikat för klustret.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Dessutom lägger du till följande rader under den **ApplicationManifest** (rottaggen) i den *ApplicationManifest.xml* filen och uppdatera tumavtrycket värdet med det certifikat för klustret.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

6. Anslut till klustret och installera behållarprogrammet.

   **Skydda klustret**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Föregående kommando hämtar certifikatet i PEM-format. Om ditt certifikat i PFX-format använda du följande kommando för att konvertera den. Om din PFX-filen inte är lösenordsskyddad, ange den **passin** parameter som `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Oskyddade kluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Detta installerar en Jenkins-behållare på klustret och kan övervakas med Service Fabric Explorer.

   > [!NOTE]
   > Det kan ta några minuter för Jenkins avbildningen hämtas i klustret.
   >

7. Gå till `http://PublicIPorFQDN:8081` i webbläsaren. På sidan visas sökvägen till det ursprungliga administratörslösenordet som krävs för att logga in. 
2. Titta på den Service Fabric Explorer för att avgöra vilken nod behållaren Jenkins körs. Secure Shell (SSH) logga in på den här noden.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
3. Hämta behållarens instans-ID med `docker ps -a`.
4. Secure Shell (SSH) logga in på behållaren och klistra in den sökväg som visades i Jenkins portal. Om exempelvis i portalen visas sökvägen `PATH_TO_INITIAL_ADMIN_PASSWORD`, kör följande kommandon:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
5. På sidan Jenkins komma igång väljer du väljer plugin-program om du vill installera alternativet väljer du den **ingen** kryssrutan och klicka på installera.
6. Skapa en användare eller Välj för att fortsätta eftersom en administratör.

När du har konfigurerat Jenkins gå vidare till [skapa och konfigurera ett jobb för Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurera Jenkins utanför ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar Jenkins utanför ett kluster.

### <a name="prerequisites"></a>Förutsättningar
- Kontrollera att Docker är installerad på datorn. Följande kommandon kan användas för att installera Docker från terminalen:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  När du kör `docker info` till terminalen och utdata ska visa att Docker-tjänsten körs.

### <a name="steps"></a>Steg
1. Hämta behållaravbildningen för Service Fabric Jenkins:`docker pull rapatchi/jenkins:latest`. Plugin-programmet för Service Fabric Jenkins är förinstallerat i avbildningen.
2. Kör behållaravbildningen: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
3. Hämta ID:t för behållaravbildningsinstansen. Du kan visa en lista med alla Docker-behållare med hjälp av kommandot `docker ps –a`
4. Logga in på portalen Jenkins med följande steg:

   1. Logga in på ett Jenkins gränssnitt från värden. Använd de första fyra siffrorna i behållaren-ID. Om behållar-ID är till exempel `2d24a73b5964`, Använd `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   2. Hämta administratörslösenordet för din instans av behållare i gränssnittet Jenkins:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   3. Logga in på Jenkins instrumentpanelen genom att öppna följande URL i en webbläsare: `http://<HOST-IP>:8080`. Använda lösenord från föregående steg för att låsa upp Jenkins.
   4. (Valfritt.) När du loggar in för första gången du kan skapa ditt eget konto och använda som i följande steg eller du kan fortsätta att använda administratörskontot. Om du skapar en användare måste du fortsätta med den aktuella användaren.
5. Ställ in GitHub för att arbeta med Jenkins med hjälp av stegen i [Generera en ny SSH-nyckel och lägga till SSH-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Följ instruktionerna från GitHub för att skapa SSH-nyckeln och lägga till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
   * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
   * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Se till att det kluster eller den dator där Jenkins behållare bilden finns har en offentlig IP-adress. Detta gör att Jenkins-instansen kan ta emot meddelanden från GitHub.

När du har konfigurerat Jenkins fortsätta till nästa avsnitt, [skapa och konfigurera ett jobb för Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

Stegen i det här avsnittet visar hur du konfigurerar ett Jenkins jobb för att svara på ändringar i en GitHub-lagringsplatsen, hämta ändringarna och skapa dem. I slutet av det här avsnittet har du dirigeras till de sista stegen för att konfigurera jobbet om du vill distribuera programmet baserat på om du distribuerar till en miljö för utveckling och testning eller till en produktionsmiljö. 

1. Klicka på instrumentpanelen Jenkins **nytt objekt**.
2. Ange ett namn (till exempel **MyJob**). Välj **free-style project** (freestyle-projekt) och klicka på **OK**.
3. Konfigurationssidan jobb öppnas. (För att hämta konfigurationen från Jenkins instrumentpanelen, klickar du på projektet och klicka sedan på **konfigurera**).

4. På den **allmänna** fliken markerar du kryssrutan för **GitHub projekt**, och ange URL för GitHub. Den här URL:en är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

5. På den **källa kod Management** väljer **Git**. Ange URL för databasen som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Du kan även ange vilken gren att skapa (till exempel `/master`).
6. Konfigurera din *GitHub* lagringsplatsen för att kommunicera med Jenkins:

   a. På sidan GitHub-lagringsplatsen, gå till **inställningar** > **integreringar och tjänster**.

   b. Välj **Lägg till tjänst**, skriv **Jenkins** och välj **Jenkins GitHub-plugin-programmet**.

   c. Ange din Jenkins-webhooksadress (som standard ska den vara `http://<PublicIPorFQDN>:8081/github-webhook/`). Klicka på **Lägg till/Uppdatera tjänsten**.

   d. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och projektet skapas.

7. På den **Skapa utlösare** i Jenkins, Välj som skapa alternativ. I det här exemplet du vill utlösa en version när ett push till databasen sker, så Välj **GitHub hook utlösare för GITScm avsökning**. (Tidigare hette det här alternativet **Build when a change is pushed to GitHub**) (Bygg när en ändring skickas till GitHub).
8. På den **skapa** gör något av följande beroende på om du utvecklar ett Java-program eller ett .NET Core-program:

   * **För Java-program:** från den **Lägg till build steg** listrutan, Välj **anropa Gradle skriptet**. Klicka på **avancerade**. Ange sökvägen till på menyn Avancerat **rot skapa skript** för ditt program. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt. För den [ActorCounter programmet](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), detta är: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric Jenkins Build-åtgärd][build-step]

   * **För .NET Core-program:** från den **Lägg till build steg** listrutan, Välj **köra Shell**. I kommandorutan måste katalogen först ändras till sökvägen där filen build.sh finns. När katalogen har ändrats, build.sh-skript kan köras och att skapa programmet.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Följande skärmbild visar ett exempel på de kommandon som används för att skapa den [räknaren Service](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) exempel med CounterServiceApplication Jenkins Jobbnamnet.

      ![Service Fabric Jenkins Build-åtgärd][build-step-dotnet]

9. Om du vill konfigurera Jenkins för att distribuera din app till ett Service Fabric-kluster i efter build-åtgärder måste platsen för det klustret certifikatet i Jenkins-behållaren. Välj något av följande beroende på om din Jenkins behållare körs i eller utanför klustret och Anteckna platsen för certifikat för klustret:

   * **För Jenkins som körs i klustret:** sökväg till certifikatet kan hittas av eko värdet för den *Certificates_JenkinsOnSF_Code_MyCert_PEM* miljövariabeln i behållaren.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **För Jenkins körs utanför klustret:** Följ dessa steg om du vill kopiera kluster certifikat till din behållare:
      1. Certifikatet måste vara i PEM-format. Om du inte har en PEM-fil kan skapa du en från certifikatets PFX-fil. Om din PFX-filen inte är lösenordsskyddad, kör du följande kommando från värddatorn:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Om PFX-filen är lösenordsskyddad, ta med lösenordet i den `-passin` parameter. Exempel:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      2. För att få behållar-ID för Jenkins-behållare, köra `docker ps` från värden.
      3. Kopiera PEM-filen till din behållare med Docker följande kommando:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Du är nästan klar! Behåll Jenkins jobbet öppen. Den enda kvarvarande aktiviteten är att konfigurera efter build-stegen för att distribuera programmet till Service Fabric-kluster:

* Om du vill distribuera till en miljö för utveckling eller testning, följer du stegen i [konfigurera distribution med hantering av klusterslutpunkten](#configure-deployment-using-cluster-management-endpoint).
* Om du vill distribuera till en produktionsmiljö, följer du stegen i [konfigurera distribution med autentiseringsuppgifter för Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurera distribution med hantering av klusterslutpunkten
Du kan använda management klusterslutpunkten för utvecklings- och testmiljöer för att distribuera ditt program. Konfigurera efter build-åtgärd med management klusterslutpunkten att distribuera ditt program måste du ställa in minst. Om du distribuerar till en produktionsmiljö, gå vidare till [konfigurera distribution med autentiseringsuppgifter för Azure](#configure-deployment-using-azure-credentials) att konfigurera en Azure Active Directory-tjänstens huvudnamn ska användas vid distributionen.    

1. I Jenkins jobbet, klickar du på den **efter build åtgärder** fliken. 
2. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
3. Under **Service Fabric-klusterkonfiguration**, Välj den **fyller Service Management Infrastruktursslutpunkt** knappen.
4. För **Management värden**, ange Anslutningens slutpunkt för klustret, till exempel `{your-cluster}.eastus.cloudapp.azure.com`.
5. För **klientens nyckel** och **klienten Cert**, ange platsen för PEM-filen i din Jenkins behållare, till exempel `/var/jenkins_home/clustercert.pem`. (Du kopierade certifikatet var det sista steget i [skapa och konfigurera ett jobb för Jenkins](#create-and-configure-a-jenkins-job).)
6. Under **programkonfigurationen**, konfigurera den **programnamn**, **programtyp**, och (relativ) **sökvägen till programmets Manifest** fält.

   ![Service Fabric Jenkins efter Build åtgärden konfigurerar av hanteringsslutpunkten](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

7. Klicka på **verifiera konfigurationen av**. Klicka på lyckad verifiering **spara**. Din Jenkins jobbet pipeline har nu konfigurerats helt. Gå vidare till [nästa steg](#next-steps) att testa distributionen.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurera distribution med autentiseringsuppgifter för Azure
För produktionsmiljöer rekommenderas konfigurera en Azure-autentiseringsuppgift om du vill distribuera programmet starkt. Det här avsnittet visar hur du konfigurerar en Azure Active Directory-tjänstens huvudnamn du använder för att distribuera ditt program i efter build-åtgärd. Du kan tilldela roller tjänstens huvudnamn i katalogen för att begränsa behörigheterna för Jenkins jobbet. 

Du kan konfigurera autentiseringsuppgifter för Azure eller hanteringsslutpunkten klustret att distribuera ditt program för utvecklings- och testmiljöer. Mer information om hur du konfigurerar en slutpunkt för hantering av klustret finns [konfigurera distribution med hantering av klusterslutpunkten](#configure-deployment-using-cluster-management-endpoint).   

1. Om du vill skapa ett Azure Active Directory tjänstens huvudnamn och tilldela behörigheter i din Azure-prenumeration, följer du stegen i [använda portalen för att skapa ett Azure Active Directory huvudnamn för applikationen eller tjänsten](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Ta hänsyn till följande:

   * När följa stegen i avsnittet måste du kopiera och spara följande värden: *program-ID*, *programnyckeln*, *katalog-ID (klient-ID)*, och *Prenumerations-ID*. Du behöver dem för att konfigurera autentiseringsuppgifter för Azure i Jenkins.
   * Om du inte har den [nödvändiga behörigheter](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) på din katalog måste du be en administratör bevilja behörigheter eller skapa tjänstens huvudnamn för dig eller måste du konfigurera av hanteringsslutpunkten för din klustret i den **efter Build åtgärder** för ditt projekt i Jenkins.
   * I den [skapa ett Azure Active Directory-program](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) avsnittet som du kan ange en giltig URL för den **inloggnings-URL**.
   * I den [tilldela program till en roll](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) avsnitt, kan du tilldela programmet på *Reader* roll i resursgruppen för klustret.

2. Klicka på tillbaka i jobbet Jenkins den **efter build åtgärder** fliken.
3. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
4. Under **Service Fabric-klusterkonfiguration**, Välj den **Välj Service Fabric-kluster** knappen. Klicka på **Lägg till** bredvid **Azure-autentiseringsuppgifterna**. Klicka på **Jenkins** att välja Jenkins autentiseringsuppgifter Provider.
5. I den Jenkins autentiseringsuppgifter väljer du **Microsoft Azure Service Principal** från den **typ** listrutan.
6. Använd värdena som du sparade när du ställer in din tjänstens huvudnamn i steg 1 för att ange följande fält:

   * **Klient-ID**: *program-ID*
   * **Klienthemlighet**: *tangent*
   * **Klient-ID**: *katalog-ID*
   * **Prenumerations-ID**: *prenumerations-ID*
6. Ange ett beskrivande **ID** att du använder för att markera autentiseringsuppgifterna i Jenkins och en kort **beskrivning**. Klicka på **Kontrollera tjänstens huvudnamn**. Om verifieringen lyckas klickar du på **Lägg till**.

   ![Service Fabric Jenkins ange autentiseringsuppgifter för Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
7. Tillbaka under **Service Fabric-klusterkonfiguration**, se till att dina nya autentiseringsuppgifter är markerat för **Azure-autentiseringsuppgifterna**. 
8. Från den **resursgruppen** listrutan, Välj den resursgrupp i klustret som du vill distribuera programmet till.
9. Från den **Service Fabric** listrutan, markerar det kluster som du vill distribuera programmet till.
10. För **Klientnyckel** och **klienten Cert**, ange platsen för PEM-filen i Jenkins-behållaren. Till exempel `/var/jenkins_home/clustercert.pem`. 
11. Under **programkonfigurationen**, konfigurera den **programnamn**, **programtyp**, och (relativ) **sökvägen till programmets Manifest** fält.
    ![Tjänsten Infrastrukturresursåtgärden Jenkins efter Build konfigurera autentiseringsuppgifter för Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
12. Klicka på **verifiera konfigurationen av**. Klicka på lyckad verifiering **spara**. Din Jenkins jobbet pipeline har nu konfigurerats helt. Vidare till [nästa steg](#next-steps) att testa distributionen.

## <a name="next-steps"></a>Nästa steg
GitHub och Jenkins har nu konfigurerats. Överväg att göra vissa exempel ändras i den `reliable-services-actor-sample/Actors/ActorCounter` projekt i din förgrening av databasen, https://github.com/Azure-Samples/service-fabric-java-getting-started. Pusha ändringarna till fjärransluten `master` gren (eller valfri gren som du har konfigurerat för att fungera med). Detta utlöser Jenkins-jobbet (`MyJob`) som du konfigurerade. Hämtar ändringarna från GitHub, skapar dem och distribuerar programmet till klustret som du angav i efter build-åtgärder.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

