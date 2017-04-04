---
title: "Skapa och integrera kontinuerligt för ditt Azure Service Fabric Java-program för Linux med hjälp av Jenkins | Microsoft Docs"
description: "Skapa och integrera kontinuerligt för ditt Java-program för Linux med hjälp av Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 71e3d130f22515d22dc7f486f3dede936b874049
ms.lasthandoff: 03/25/2017


---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Skapa och distribuera ditt Java-program för Linux med hjälp av Jenkins
Jenkins är ett populärt verktyg för kontinuerlig integrering och distribution av appar. Så här skapar och distribuerar du ett Azure Service Fabric-program med Jenkins.

## <a name="general-prerequisites"></a>Allmänna krav
- Du måste ha Git installerat lokalt. På [nedladdningssidan för Git](https://git-scm.com/downloads) kan du installera lämplig Git-version för ditt operativsystem. Om du är nybörjare på Git kan du läsa mer i [Git-dokumentationen](https://git-scm.com/docs).
- Du måste ha plugin-programmet Service Fabric Jenkins till hands. Du kan ladda ned det från [Service Fabric-nedladdningar](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurera Jenkins i ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar Jenkins i ett kluster.

### <a name="prerequisites"></a>Krav
1. Ha ett Service Fabric Linux-kluster redo. Docker finns redan installerat i Service Fabric-kluster som skapas via Azure Portal. Om du kör klustret lokalt kan du kontrollera om Docker är installerat med hjälp av kommandot ``docker info``. Om Docker inte är installerat kan du installera det med följande kommandon:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Distribuera Service Fabric-behållarprogrammet till klustret enligt stegen nedan:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Detta installerar en Jenkins-behållare på klustret och kan övervakas med Service Fabric Explorer.

### <a name="steps"></a>Steg
1. Gå till ``http://PublicIPorFQDN:8081`` i webbläsaren. På sidan visas sökvägen till det ursprungliga administratörslösenordet som krävs för att logga in. Du kan fortsätta att använda Jenkins som administratörsanvändare. Eller så kan du skapa en ny användare och byta till den när du har loggat in med det ursprungliga administratörskontot.

   > [!NOTE]
   > Se till att port 8081 anges som programmets slutpunktsport när du skapar klustret.
   >

2. Hämta behållarens instans-ID med ``docker ps -a``.
3. Logga in med SSH-inloggning (Secure Shell ) till behållaren och klistra in sökvägen som visades i Jenkins-portalen. Om exempelvis sökvägen `PATH_TO_INITIAL_ADMIN_PASSWORD` visas i portalen kan du köra följande:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Konfigurera GitHub för Jenkins genom att utföra åtgärderna som nämns i [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generera en ny SSH-nyckel och lägga till den i SSH-agenten).
    * Använd instruktionerna från GitHub för att skapa SSH-nyckeln och lägg till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
    * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
    * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurera Jenkins utanför ett Service Fabric-kluster

Du kan konfigurera Jenkins i eller utanför ett Service Fabric-kluster. I följande avsnitt visas hur du konfigurerar Jenkins utanför ett kluster.

### <a name="prerequisites"></a>Krav
Du måste ha Docker installerat. Följande kommandon kan användas för att installera Docker från terminalen:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

När du kör ``docker info`` på terminalen visar utdata nu att Docker-tjänsten körs.

### <a name="steps"></a>Steg
  1. Kör behållaren Service Fabric Jenkins, avbildning: ``docker pull raunakpandya/jenkins:v1``
  2. Kör behållaravbildningen: ``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Hämta ID:t för behållaravbildningsinstansen. Du kan visa en lista med alla Docker-behållare med hjälp av kommandot ``docker ps –a``
  4. Logga in på Jenkins-portalen med följande steg:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Om behållar-ID:t är 2d24a73b5964 ska du använda 2d24.
    * Det här lösenordet krävs för att logga in på Jenkins-instrumentpanelen från portalen som är ``http://<HOST-IP>:8080``
    * När du har loggat in för första gången kan du skapa ett eget användarkonto och använda det för senare behov, eller så kan du fortsätta att använda administratörskontot. När du har skapat en användare måste du fortsätta med den användaren.
  5. Konfigurera GitHub för Jenkins genom att utföra åtgärderna som nämns i [Generating a new SSH key and adding it to the SSH agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) (Generera en ny SSH-nyckel och lägga till den i SSH-agenten).
        * Följ instruktionerna från GitHub för att skapa SSH-nyckeln och lägga till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
        * Kör de kommandon som nämns i länken ovan i Jenkins Docker-gränssnittet (och inte på värden).
        * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommandon:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Kontrollera att klustret eller datorn där Jenkins-behållaravbildningen finns har en offentlig IP-adress. Detta gör att Jenkins-instansen kan ta emot meddelanden från GitHub.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>Installera plugin-programmet till Service Fabric Jenkins från portalen

1. Gå till ``http://PublicIPorFQDN:8081``
2. Från Jenkins-instrumentpanelen väljer du **Manage Jenkins (Hantera Jenkins)** > **Manage Plugins (Hantera plugin-program)** > **Avancerat**.
Här kan du ladda upp ett plugin-program. Välj alternativet **Välj fil** och välj sedan den **serviceFabric.hpi**-fil som du har hämtat under Krav. När du väljer alternativet för att **ladda upp** installerar Jenkins automatiskt plugin-programmet åt dig. Tillåt en omstart om det begärs.

## <a name="create-and-configure-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

1. Skapa en **ny post** från instrumentpanelen.
2. Ange ett namn (till exempel **MyJob**). Välj **free-style project** (freestyle-projekt) och klicka på **OK**.
3. Gå till jobbsidan och klicka sedan på **Konfigurera**.

   a. Ange URL:en för GitHub-projektet under **GitHub-projekt** i det allmänna avsnittet. Den här URL:en är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. ``https://github.com/sayantancs/SFJenkins``).

   b. I avsnittet **Source Code Management** (Källkodshantering) väljer du **Git**. Ange URL för databasen som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. ``https://github.com/sayantancs/SFJenkins.git``). Du kan också ange här vilken gren som ska byggas (t.ex. ***/master**).
4. Konfigurera din *GitHub* (som är värd för databasen) så att den kan kommunicera med Jenkins. Använd följande steg:

   a. Gå till GitHub-lagringsplatssidan. Gå till **Inställningar** > **Integrations and Services** (Integreringar och tjänster).

   b. Välj **Lägg till tjänst**, skriv **Jenkins** och välj **Jenkins GitHub-plugin-programmet**.

   c. Ange din Jenkins-webhooksadress (som standard ska den vara ``http://<PublicIPorFQDN>:8081/github-webhook/``). Klicka på **Lägg till/Uppdatera tjänsten**.

   d. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och projektet skapas.

   e. I avsnittet om **build-utlösare** väljer du önskat alternativ. I det här exemplet vill du utlösa en build när något skickas till databasen. Därför väljer du **GitHub hook trigger for GITScm polling** (GitHub-hookutlösare för GITScm-avsökning). (Tidigare hette det här alternativet **Build when a change is pushed to GitHub**) (Bygg när en ändring skickas till GitHub).

   f. Under avsnittet **Build** (Bygg) i listrutan **Add build step** (Lägg till byggsteg) väljer du alternativet **Invoke Gradle Script** (Anropa Gradle-skript). I widgeten som visas anger du sökvägen till **rotbuildskript** för ditt program. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt. Om du skapar ett projekt med namnet ``MyActor`` (med Eclipse-plugin-programmet eller Yeoman-generatorn), ska rotbuildskriptet innehålla ``${WORKSPACE}/MyActor``. Följande skärmbild visar ett exempel på hur det kan se ut:

    ![Service Fabric Jenkins Build-åtgärd][build-step]

   g. I listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). Här måste du ange klusterinformation där Jenkins-kompilerade Service Fabric-programmet skulle distribueras. Du kan även ange ytterligare information som används för att distribuera programmet. Följande skärmbild visar ett exempel på hur det kan se ut:

    ![Service Fabric Jenkins Build-åtgärd][post-build-step]

   > [!NOTE]
   > Det här klustret kan vara detsamma som det kluster som är värd för Jenkins-behållarprogrammet om du använder Service Fabric för att distribuera Jenkins-behållaravbildningen.
   >

## <a name="next-steps"></a>Nästa steg
GitHub och Jenkins har nu konfigurerats. Fundera över om du vill göra ändringar i ditt ``MyActor``-projekt i databasexemplet på https://github.com/sayantancs/SFJenkins. Skicka dina ändringar till en ``master``-fjärrgren (eller valfri gren som du använder i ditt projekt). Detta utlöser Jenkins-jobbet (``MyJob``) som du konfigurerade. Jobbet hämtar ändringarna från GitHub, bygger dem och distribuerar programmet till den klusterslutpunkt som du angav i åtgärderna efter byggprocessen.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

