---
title: Skapa och kontinuerligt-integrering för dina Azure Service Fabric Linux-program med hjälp av Jenkins | Microsoft Docs
description: Skapa och kontinuerligt för ditt Service Fabric Linux-program med hjälp av Jenkins-integrering
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: f381285d29d70d6f5da6a6cd319c682cd0c6a235
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444546"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Skapa och distribuera ditt Linux-program med hjälp av Jenkins
Jenkins är ett populärt verktyg för kontinuerlig integrering och distribution av appar. Så här skapar och distribuerar du ett Azure Service Fabric-program med Jenkins.

## <a name="topic-overview"></a>Avsnittet Översikt
Den här artikeln beskriver flera möjliga sätt på hur du konfigurerar din Jenkins-miljö samt olika sätt att distribuera programmet till ett Service Fabric-kluster när det har skapats. Följ stegen för att konfigurera Jenkins, hämta ändringarna från GitHub, ett program i och distribuera den till ditt kluster har:

1. Se till att du installerar den [krav](#prerequisites).
1. Följ anvisningarna i någon av dessa avsnitt för att konfigurera Jenkins:
   * [Konfigurera Jenkins i ett Service Fabric-kluster](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Konfigurera Jenkins utanför ett Service Fabric-kluster](#set-up-jenkins-outside-a-service-fabric-cluster), eller
   * [Installera Service Fabric-plugin-programmet i en befintlig Jenkins-miljö](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. När du har konfigurerat Jenkins, följer du stegen i [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job) att konfigurera GitHub för att utlösa Jenkins när ändringar görs i ditt program och konfigurera din pipeline för Jenkins-jobb via ett byggsteg för att hämta den ändras från GitHub och skapa ditt program. 
1. Slutligen kan konfigurera Jenkins-jobb efter skapandet steg om du vill distribuera programmet till Service Fabric-klustret. Det finns två sätt att konfigurera Jenkins för att distribuera programmet till ett kluster:    
   * Använd för utvecklings- och testmiljöer [konfigurera distributionen med hjälp av slutpunkten för klusterhantering](#configure-deployment-using-cluster-management-endpoint). Det här är den enklaste distributionsmetoden att ställa in.
   * För produktionsmiljöer, använda [konfigurera distributionen med hjälp av autentiseringsuppgifter för Azure](#configure-deployment-using-azure-credentials). Microsoft rekommenderar den här metoden för produktionsmiljöer eftersom med autentiseringsuppgifter för Azure kan du begränsa åtkomsten som ett Jenkins-jobb har till din Azure-resurser. 

## <a name="prerequisites"></a>Förutsättningar

- Kontrollera att Git har installerats lokalt. Du kan installera lämplig Git-version från [nedladdningssidan för Git](https://git-scm.com/downloads) baserat på ditt operativsystem. Om du inte har använt Git kan läsa mer om den från den [Git-dokumentationen](https://git-scm.com/docs).
- Den här artikeln används den *Service Fabric komma igång exempel* på GitHub: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) att skapa och distribuera programmet. Du kan duplicera den här lagringsplatsen att följa instruktionerna, eller, med vissa ändringar till anvisningarna kan använda ditt eget GitHub-projektet.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Installera Service Fabric-plugin-programmet i en befintlig Jenkins-miljö
Om du lägger till Service Fabric-plugin-programmet till en befintlig Jenkins-miljö, behöver du följande:

- Den [Service Fabric CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Glöm inte att installera CLI på systemnivå i stället för att på användarnivå, så Jenkins kan köra CLI-kommandon. 
   >

- För att distribuera Java-program, installera både [Gradle och öppna JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Om du vill distribuera .NET Core 2.0-program, installera den [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

När du har installerat kraven för din miljö kan du söka efter Azure Service Fabric-plugin-programmet Jenkins Marketplace och installera den.

När du har installerat plugin-programmet kan du gå vidare till [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurera Jenkins i ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar jenkins i ett kluster när ett Azure storage-konto för att spara tillståndet för container-instans.

### <a name="prerequisites"></a>Förutsättningar
- Har ett Service Fabric Linux-kluster med Docker installerat. Service Fabric-kluster som körs i Azure redan ha Docker installerat. Om du kör klustret lokalt (OneBox-utvecklingsmiljö), kontrollera om Docker är installerat på datorn med den `docker info` kommando. Om det inte är installerat installerar du det med hjälp av följande kommandon:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Kontrollera att port 8081 anges som en anpassad slutpunkt i klustret. Om du använder ett lokalt kluster kan du kontrollera att port 8081 är öppen på värddatorn och att den har en offentlig IP-adress.
   >

### <a name="steps"></a>Steg
1. Klona programmet, med hjälp av följande kommandon:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Bevara tillståndet för Jenkins-behållaren i en filresurs:
   1. Skapa ett Azure storage-konto i den **samma region** som klustret med ett namn som `sfjenkinsstorage1`.
   1. Skapa en **filresurs** under lagringen konto med ett namn som `sfjenkins`.
   1. Klicka på **Connect** för fildelning och anteckna värdena visas **ansluter från Linux**, värdet bör likna följande:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Du måste ha cifs-utils-paketet installeras på noderna i klustret för att montera cifs shares.      
   >

1. Uppdatera platshållarvärdena i den `setupentrypoint.sh` skript med azure-storage-information från steg 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Ersätt `[REMOTE_FILE_SHARE_LOCATION]` med värdet `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` från utdata från connect i steg 2 ovan.
   * Ersätt `[FILE_SHARE_CONNECT_OPTIONS_STRING]` med värdet `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` från steg 2 ovan.

1. **Säkra klustret:** 
   
   Om du vill konfigurera distribution av program på ett säkert kluster från Jenkins måste klustercertifikatet vara tillgänglig i Jenkins-behållaren. I den *ApplicationManifest.xml* filen under den **ContainerHostPolicies** taggen lägger du till den här referensen för certifikatet och uppdatera värdet tumavtryck med klustercertifikatet.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Dessutom lägger du till följande rader under den **ApplicationManifest** (rot)-tagg i den *ApplicationManifest.xml* filen och uppdatera tumavtrycksvärde med det för klustercertifikatet.

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
   Föregående kommando tar certifikatet i PEM-format. Om ditt certifikat finns i PFX-format, kan du använda följande kommando för att konvertera den. Om din PFX-filen inte är lösenordsskyddad, ange den **passin** parametern som `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Oskyddade kluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Detta installerar en Jenkins-container på klustret och kan övervakas med Service Fabric Explorer.

   > [!NOTE]
   > Det kan ta några minuter för Jenkins-avbildningen hämtas i klustret.
   >

1. Gå till `http://PublicIPorFQDN:8081` i webbläsaren. På sidan visas sökvägen till det ursprungliga administratörslösenordet som krävs för att logga in. 
1. Titta på Service Fabric Explorer för att avgöra vilken nod Jenkins-behållaren körs. Secure Shell (SSH) logga in på den här noden.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Hämta containerns instans-ID med `docker ps -a`.
1. Secure Shell (SSH) som inloggning till behållaren och klistra in den sökväg som visades i Jenkins-portalen. Exempel: om portalen visas i sökvägen `PATH_TO_INITIAL_ADMIN_PASSWORD`, kör följande kommandon:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. På sidan Jenkins komma igång väljer du väljer plugin-program om du vill installera alternativet väljer du den **ingen** kryssrutan och klicka på installera.
1. Skapa en användare eller välja för att fortsätta som en administratör.

När du har konfigurerat Jenkins, gå vidare till [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurera Jenkins utanför ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar Jenkins utanför ett kluster.

### <a name="prerequisites"></a>Förutsättningar
- Se till att Docker är installerat på din dator. Följande kommandon kan användas för att installera Docker från terminalen:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  När du kör `docker info` i terminalen utdata ska visa att Docker-tjänsten körs.

### <a name="steps"></a>Steg
1. Hämta containeravbildningen för Service Fabric Jenkins:`docker pull rapatchi/jenkins:latest`. Plugin-programmet för Service Fabric Jenkins är förinstallerat i avbildningen.
1. Kör containeravbildningen: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Hämta ID:t för containeravbildningsinstansen. Du kan visa en lista med alla Docker-containrar med hjälp av kommandot `docker ps –a`
1. Logga in på Jenkins-portalen med följande steg:

   1. Logga in på Jenkins-gränssnittet från värden. Använd de första fyra siffrorna i behållar-ID. Om behållar-ID är till exempel `2d24a73b5964`, använda `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Hämta admin-lösenordet för din behållarinstans från Jenkins-gränssnittet:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Öppna följande URL i en webbläsare för att logga in på Jenkins-instrumentpanelen: `http://<HOST-IP>:8080`. Använd lösenordet från föregående steg för att låsa upp Jenkins.
   1. (Valfritt.) När du har loggat in för första gången du kan skapa ditt eget användarkonto och använda det för följande steg, eller du kan fortsätta att använda administratörskontot. Om du skapar en användare, måste du fortsätta med den användaren.
1. Konfigurera GitHub för Jenkins med hjälp av stegen i [skapar en ny SSH-nyckel och lägga till SSH-agenten](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Följ instruktionerna från GitHub för att skapa SSH-nyckeln och lägga till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
   * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
   * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Kontrollera att klustret eller datorn där Jenkins-behållaravbildningen finns har en offentlig IP-adress. Detta gör att Jenkins-instansen kan ta emot meddelanden från GitHub.

När du har konfigurerat Jenkins, Fortsätt till nästa avsnitt, [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

Stegen i det här avsnittet visar hur du konfigurerar ett Jenkins-jobb för att svara på ändringar i en GitHub-lagringsplatsen, att hämta ändringarna och skapa dem. I slutet av det här avsnittet har du skickas till de avslutande stegen för att konfigurera jobbet för att distribuera ditt program baserat på om du distribuerar till en miljö för utveckling/testning eller till en produktionsmiljö. 

1. På Jenkins-instrumentpanelen klickar du på **nytt objekt**.
1. Ange ett namn (till exempel **MyJob**). Välj **free-style project** (freestyle-projekt) och klicka på **OK**.
1. Konfigurationssidan för jobbet öppnas. (Klicka på jobbet för att hämta konfigurationen från Jenkins-instrumentpanelen, och klicka sedan på **konfigurera**).

1. På den **Allmänt** fliken, markerar du kryssrutan för **GitHub-projektet**, och anger ditt GitHub-projekt-URL. Den här URL:en är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. På den **Source Code Management** fliken **Git**. Ange URL för databasen som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Du kan även ange vilken gren som ska byggas (till exempel `/master`).
1. Konfigurera din *GitHub* databasen kommunicera Jenkins:

   a. På GitHub-lagringsplatssidan, går du till **inställningar** > **integreringar och tjänster**.

   b. Välj **Lägg till tjänst**, skriv **Jenkins** och välj **Jenkins GitHub-plugin-programmet**.

   c. Ange din Jenkins-webhooksadress (som standard ska den vara `http://<PublicIPorFQDN>:8081/github-webhook/`). Klicka på **Lägg till/Uppdatera tjänsten**.

   d. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och projektet skapas.

1. På den **Build-utlösare** fliken i Jenkins, väljer du önskat alternativ. I det här exemplet som du vill utlösa en build när en skickas till databasen sker, så Välj **GitHub hook trigger för GITScm-avsökning**. (Tidigare hette det här alternativet **Build when a change is pushed to GitHub**) (Bygg när en ändring skickas till GitHub).
1. På den **skapa** gör något av följande beroende på om du skapar ett Java-program eller en .NET Core-program:

   * **För Java-program:** från den **Lägg till byggsteg** listrutan, väljer **anropa Gradle-skript**. Klicka på **avancerade**. I menyn Avancerat anger sökvägen till **rotbuildskript** för ditt program. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt. För den [ActorCounter programmet](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), detta är: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Service Fabric Jenkins Build-åtgärd][build-step]

   * **För .NET Core-program:** från den **Lägg till byggsteg** listrutan, väljer **köra Shell**. I kommandorutan måste först katalogen ändras till den sökväg där filen build.sh finns. När katalogen har ändrats, kan du köra skriptet build.sh och skapar programmet.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Följande skärmbild visar ett exempel på de kommandon som används för att skapa den [tjänsten för prestandaräknare](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) exemplet med Jenkins-Jobbnamnet CounterServiceApplication.

      ![Service Fabric Jenkins Build-åtgärd][build-step-dotnet]

1. För att konfigurera Jenkins för att distribuera din app till ett Service Fabric-kluster i åtgärderna efter skapandet, måste du platsen för det klustercertifikat i din Jenkins-behållare. Välj något av följande beroende på om din Jenkins-behållare körs i eller utanför klustret och Anteckna platsen för klustercertifikatet:

   * **För Jenkins som körs i klustret:** sökvägen till certifikatet kan hittas genom eko värdet för den *Certificates_JenkinsOnSF_Code_MyCert_PEM* miljövariabeln i behållaren.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **För Jenkins som körs utanför klustret:** följer du stegen nedan för att kopiera klustercertifikatet till behållaren:
      1. Certifikatet måste vara i PEM-format. Om du inte har en PEM-fil kan skapa du en från certifikatets PFX-fil. Om din PFX-filen inte är lösenordsskyddad, kör du följande kommando från värden:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Om PFX-filen är lösenordsskyddad, ta med lösenordet i den `-passin` parametern. Exempel:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      1. För att få behållar-ID för din Jenkins-behållare, köra `docker ps` från värden.
      1. Kopiera PEM-filen till behållaren med kommandot Docker:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Du är nästan klar! Behåll Jenkins-jobbet öppen. Den enda kvarvarande aktiviteten är att konfigurera post-build stegen för att distribuera programmet till Service Fabric-klustret:

* Om du vill distribuera till en miljö för utveckling eller testning, följer du stegen i [konfigurera distributionen med hjälp av slutpunkten för klusterhantering](#configure-deployment-using-cluster-management-endpoint).
* Om du vill distribuera till en produktionsmiljö, följer du stegen i [konfigurera distributionen med hjälp av autentiseringsuppgifter för Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurera distributionen med hjälp av slutpunkten för klusterhantering
Du kan använda slutpunkten för klusterhantering för utvecklings- och testmiljöer för att distribuera ditt program. Konfigurera efter byggåtgärden med slutpunkten för klusterhantering att distribuera ditt program kräver minst konfiguration. Om du distribuerar till en produktionsmiljö, gå vidare till [konfigurera distributionen med hjälp av autentiseringsuppgifter för Azure](#configure-deployment-using-azure-credentials) att konfigurera ett Azure Active Directory-tjänstobjekt som ska användas vid distributionen.    

1. I Jenkins-jobbet klickar du på den **post-build Actions** fliken. 
1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
1. Under **konfigurationen för Service Fabric-kluster**väljer den **Fyll Hanteringsslutpunkten Service Fabric** alternativknappen.
1. För **Management värden**, ange anslutningsslutpunkten för klustret, till exempel `{your-cluster}.eastus.cloudapp.azure.com`.
1. För **Klientnyckel** och **Client Cert**, ange platsen för PEM-filen i din Jenkins-behållare, till exempel `/var/jenkins_home/clustercert.pem`. (Du kopierade platsen för certifikatet till det sista steget i [skapa och konfigurera ett Jenkins-jobb](#create-and-configure-a-jenkins-job).)
1. Under **programkonfiguration**, konfigurera den **programnamn**, **programtyp**, och (relativ) **sökvägen till programmets Manifest** fält.

   ![Byggåtgärden för Service Fabric Jenkins efter konfigurera hanteringsslutpunkten](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klicka på **verifiera konfigurationen**. På lyckad kontrollen är klar klickar du på **spara**. Konfigurationen av din pipeline för Jenkins-jobb är nu slutförd. Gå vidare till [nästa steg](#next-steps) att testa distributionen.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurera distributionen med hjälp av Azure-autentiseringsuppgifter
För produktionsmiljöer rekommenderas konfigurera en Azure-inloggning för att distribuera ditt program starkt. Det här avsnittet visar hur du konfigurerar ett Azure Active Directory-tjänstobjekt som du använder för att distribuera programmet i instruktionen efter skapandet. Du kan tilldela roller tjänstens huvudnamn i din katalog för att begränsa behörigheterna för Jenkins-jobbet. 

Du kan konfigurera Azure-autentiseringsuppgifter eller slutpunkten för klusterhantering att distribuera ditt program för utvecklings- och testmiljöer. Mer information om hur du konfigurerar en slutpunkten för klusterhantering finns i [konfigurera distributionen med hjälp av slutpunkten för klusterhantering](#configure-deployment-using-cluster-management-endpoint).   

1. Om du vill skapa en Azure Active Directory-tjänstobjekt och tilldela den behörigheter i din Azure-prenumeration, följer du stegen i [använda portalen för att skapa en Azure Active Directory-program och tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Ta hänsyn till följande:

   * När du följa stegen i avsnittet måste du kopiera och spara följande värden: *program-ID*, *programnyckel*, *katalog-ID (klient-ID)*, och *Prenumerations-ID*. Du behöver dem för att konfigurera Azure-autentiseringsuppgifter i Jenkins.
   * Om du inte har den [nödvändiga behörigheter](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) på din katalog måste du be en administratör bevilja dig behörigheter eller skapa tjänstens huvudnamn för dig eller måste du konfigurera hanteringsslutpunkten för ditt kluster i den **post-build Actions** för ditt jobb i Jenkins.
   * I den [skapar ett Azure Active Directory-program](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) avsnittet, som du kan ange valfri giltig URL för den **inloggnings-URL**.
   * I den [tilldela program till en roll](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) avsnittet kan du tilldela programmet den *läsare* rollen på resursgruppen för klustret.

1. Tillbaka i Jenkins-jobbet klickar du på den **post-build Actions** fliken.
1. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). 
1. Under **konfigurationen för Service Fabric-kluster**väljer den **väljer Service Fabric-kluster** alternativknappen. Klicka på **Lägg till** bredvid **Azure Credentials**. Klicka på **Jenkins** att välja Provider för Jenkins-autentiseringsuppgifter.
1. I den Jenkins autentiseringsuppgifter väljer du **Microsoft Azure Service Principal** från den **typ** utskriftsjobb.
1. Använd de värden du sparade när du ställer in tjänstens huvudnamn i steg 1 för att ange följande fält:

   * **Klient-ID**: *program-ID*
   * **Klienthemlighet**: *programnyckel*
   * **Klient-ID**: *katalog-ID*
   * **Prenumerations-ID**: *prenumerations-ID*
1. Ange ett beskrivande **ID** att du använder för att markera autentiseringsuppgifterna i Jenkins och en kort **beskrivning**. Klicka sedan på **Kontrollera tjänstens huvudnamn**. Om verifieringen lyckas klickar du på **Lägg till**.

   ![Service Fabric Jenkins ange autentiseringsuppgifter för Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Tillbaka under **konfigurationen för Service Fabric-kluster**, se till att dina nya autentiseringsuppgifter har valts för **Azure Credentials**. 
1. Från den **resursgrupp** listrutan, väljer du resursgruppen för det kluster du vill distribuera programmet till.
1. Från den **Service Fabric** listrutan, väljer du klustret som du vill distribuera programmet till.
1. För **Klientnyckel** och **Client Cert**, ange platsen för PEM-filen i din Jenkins-behållare. Till exempel `/var/jenkins_home/clustercert.pem`. 
1. Under **programkonfiguration**, konfigurera den **programnamn**, **programtyp**, och (relativ) **sökvägen till programmets Manifest** fält.
    ![Byggåtgärden för Service Fabric Jenkins efter konfigurera autentiseringsuppgifter för Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klicka på **verifiera konfigurationen**. På lyckad kontrollen är klar klickar du på **spara**. Konfigurationen av din pipeline för Jenkins-jobb är nu slutförd. Fortsätta till [nästa steg](#next-steps) att testa distributionen.

## <a name="troubleshooting-the-jenkins-plugin"></a>Felsökning av plugin-programmet Jenkins

Om du stöter på buggar med Jenkins plugin-program kan du rapportera problemet i den [Jenkins JIRA](https://issues.jenkins-ci.org/) för en viss komponent.

## <a name="next-steps"></a>Nästa steg
GitHub och Jenkins har nu konfigurerats. Överväg att göra ändringar i den `reliable-services-actor-sample/Actors/ActorCounter` projekt i din förgrening av lagringsplatsen https://github.com/Azure-Samples/service-fabric-java-getting-started. Skicka dina ändringar till en fjärransluten `master` fjärrgrenen (eller valfri gren som du har konfigurerat för att fungera med). Detta utlöser Jenkins-jobbet (`MyJob`) som du konfigurerade. Den hämtar ändringarna från GitHub, bygger dem och distribuerar programmet till klustret som du angav i åtgärderna efter Byggprocessen.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

