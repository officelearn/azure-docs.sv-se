---
title: "Skapa och integrera kontinuerligt för ditt Azure Service Fabric Java-program för Linux Java med hjälp av Jenkins | Microsoft Docs"
description: "Skapa och integrera kontinuerligt för ditt Java-program för Linux Java med hjälp av Jenkins"
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
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 335f41e2e4a40e64e87382ea338673de3ab79c27
ms.lasthandoff: 03/14/2017


---
# <a name="build-and-deploy-your-linux-java-application-using-jenkins"></a>Skapa och distribuera ditt Linux Java-program med hjälp av Jenkins
Jenkins är ett populärt verktyg för kontinuerlig integrering och distribution. Vi vägleder genom processen med att skapa och distribuera Service Fabric-programmet med Jenkins i det här dokumentet.

## <a name="general-prerequisites"></a>Allmänna krav
1. Du måste ha git installerat lokalt. Du kan installera lämplig git-version för ditt operativsystem [här](https://git-scm.com/downloads). Om du inte har använt git tidigare kan du följa stegen i [dokumentationen](https://git-scm.com/docs) för att bekanta dig med git.
2. Du måste ha plugin-programmet Service Fabric Jenkins till hands. Hämta plugin-programmet Service Fabric Jenkins [här](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="setting-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurera Jenkins i ett Service Fabric-kluster

### <a name="prerequisites"></a>Krav
1. Ha ett Service Fabric Linux-kluster redo. Service Fabric-kluster som skapas från Azure Portal har redan Docker installerat. Om du kör klustret lokalt bör du kontrollera om Docker är installerat eller inte med kommandot ``docker info``, och om det inte är installerat installera det med hjälp av följande kommandon:

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
Detta installerar Jenkins-behållare på klustret och kan övervakas med Service Fabric Explorer.

### <a name="steps"></a>Steg
1. Gå till URL: en ``http://PublicIPorFQDN:8081`` från din webbläsare. Den visar sökvägen till det ursprungliga administratörslösenord som krävs för att logga in. Du kan fortsätta att använda Jenkins som administratörsanvändare eller också kan du skapa och ändra användare när du har loggat in på det första administratörskontot.

  > [!NOTE]
  > Du måste se till att port 8081 anges som programmets slutpunktsport när klustret skapas
  >

2. Hämta behållarens instans-ID med ``docker ps -a``.
3. SSH-inloggning till behållaren genom att kopiera och klistra in den sökväg som visades i Jenkins-portalen. Om exempelvis sökvägen `PATH_TO_INITIAL_ADMIN_PASSWORD` visas i portalen kan du göra följande:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Konfigurera GitHub för att fungera med Jenkins med stegen i [länken](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Använd instruktionerna från GitHub för att skapa SSH-nyckeln och lägg till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
    * Kör de kommandon som nämns i länken ovan i Jenkins docker-gränssnittet (och inte på värden)
    * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="setting-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurera Jenkins utanför ett Service Fabric-kluster

### <a name="prerequisites"></a>Krav
Du måste ha Docker installerat. Följande kommandon kan användas för att installera Docker från terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

När du kör ``docker info`` på terminalen visar utdata nu att Docker-tjänsten körs.

### <a name="steps"></a>Steg
  1. Kör behållaren Service Fabric Jenkins, avbildning: ``docker pull servicefabric-microsoft.azurecr.io/jenkins:v1``
  2. Kör behållaravbildningen: ``docker run -itd -p 8080:8080 servicefabric-microsoft.azurecr.io/jenkins:v1``
  3. Hämta ID:t för behållaravbildningsinstansen. Du kan visa en lista med alla Docker-behållare med hjälp av kommandot ``docker ps –a``
  4. Logga in på Jenkins-portalen med följande steg:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Om behållar-ID är 2d24a73b5964 ska du använda 2d24.
    * Det här lösenordet krävs för att logga in på Jenkins-instrumentpanelen från portalen som är ``http://<HOST-IP>:8080``
    * När du har loggat in för första gången kan du skapa ditt eget användarkonto och använda det för framtida syften eller också kan du fortsätta att använda administratörskontot. När du skapar en användare måste du fortsätta med den användaren.
  5. Konfigurera GitHub för att fungera med Jenkins med stegen i [länken](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
      * Använd instruktionerna från GitHub för att skapa SSH-nyckeln och lägg till SSH-nyckeln på det GitHub-konto som är (blir) värd för databasen.
      * Kör de kommandon som nämns i länken ovan i Jenkins docker-gränssnittet (och inte på värden)
      * Om du vill logga in till Jenkins-gränssnittet från värden ska du använda följande kommandon:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

    > [!NOTE]
    > Kontrollera att klustret eller datorn där Jenkins-behållaravbildningen finns har en offentlig IP-adress så att meddelanden från GitHub tas emot av Jenkins-instansen.
    >

## <a name="install-the-service-fabric-jenkins-plugin-from-portal"></a>Installera plugin-programmet till Service Fabric Jenkins från portalen

1. Gå till ``http://PublicIPorFQDN:8081``
2. Från Jenkins-instrumentpanelen väljer du **Manage Jenkins (Hantera Jenkins)** -> **Manage Plugins (Hantera plugin-program)** -> **Avancerat**.
Här kan du ladda upp ett plugin-program. Välj alternativet **Välj fil** och välj sedan den serviceFabric.hpi-fil som du har hämtat under Krav. När du har valt att ladda upp installerar Jenkins automatiskt plugin-programmet åt dig. Tillåt en omstart om det begärs.

## <a name="creating-and-configuring-a-jenkins-job"></a>Skapa och konfigurera ett Jenkins-jobb

1. Skapa en **ny post** från instrumentpanelen
2. Ange ett namn för objektet, exempelvis **MyJob**, välj freestyle-projekt och klicka på OK
3. Gå sedan till jobbsidan och klicka sedan på **Konfigurera** -
  * I avsnittet Allmänt under **Github project** (Github-projekt) anger du URL för det GitHub-projekt som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flödet (t.ex. ``https://github.com/sayantancs/SFJenkins``).
  * I avsnittet **Source Code Management** (Källkodshantering) väljer du **Git**. Ange URL för databasen som är värd för det Service Fabric Java-program som du vill integrera med Jenkins CI/CD-flöde (t.ex. – ``https://github.com/sayantancs/SFJenkins.git``). Du kan också ange här vilken gren som ska byggas, t.ex. ***/master**.
4. Konfigurera din *GitHub* (d.v.s. som är värd för databasen) så att den kan kommunicera Jenkins med följande steg:
  1. Gå till GitHub-lagringsplatssidan. Gå till **Inställningar** -> **Integrations and Services** (Integreringar och tjänster).
  2. Välj **Lägg till tjänst**, skriv Jenkins och välj **Jenkins Github-plugin-programmet**.
  3. Ange din Jenkins-webhooksadress (som standard ska den vara ``http://<PublicIPorFQDN>:8081/github-webhook/``). Klicka på Lägg till/Uppdatera tjänsten.
  4. En testhändelse skickas till Jenkins-instansen. Du bör se en grön bock vid webhooken i GitHub och projektet skapas!
  5. I avsnittet **Build Triggers** (Bygg utlösare) väljer du önskat versionsalternativ – för det här användningsfallet planerar vi att utlösa en build när vissa överföringar till databasen sker, så motsvarande alternativ är därför **GitHub hook trigger for GITScm polling** (GitHub-hookutlösare för GITScm-avsökning) (tidigare var det ”Build när en ändring skickas till GitHub”)
  6. Under avsnittet **Build** (Bygg) – på listrutan **Add build step** (Lägg till byggsteg) väljer du alternativet **Invoke Gradle Script** (Anropa Gradle-skript). I widgeten som visas anger du sökvägen till **rotbuildskript** för ditt program. Då hämtas build.gradle från den angivna sökvägen och fungerar på motsvarande sätt. Om du skapar ett projekt med namnet ``MyActor`` (med Eclipse-plugin-programmet eller Yeoman-generatorn), ska rotbuildskriptet innehålla ``${WORKSPACE}/MyActor``. Som exempel ser det här avsnittet huvudsakligen ut så här:

    ![Service Fabric Jenkins Build-åtgärd][build-step]
  7. Under listrutan **Post-Build Actions** (Åtgärder efter skapandet) väljer du **Deploy Service Fabric Project** (Distribuera Service Fabric-projekt). Här måste du ange klusterinformation där Jenkins kompilerade Service Fabric-program skulle distribueras och ytterligare information som används för att distribuera programmet. Följande skärmbild kan användas som en referens:

    ![Service Fabric Jenkins Build-åtgärd][post-build-step]

 > [!NOTE]
 > Det här klustret kan vara detsamma som det kluster som är värd för Jenkins-behållarprogrammet om du använder Service Fabric för att distribuera Jenkins-behållaravbildningen.
 >

### <a name="end-to-end-scenario"></a>Slutpunkt till slutpunkt-scenario
Nu bör din GitHub och Jenkins ha konfigurerats och nu kan du göra vissa exempeländringar i ditt ``MyActor``-projekt i databasen t.ex. https://github.com/sayantancs/SFJenkins och skicka dina ändringar till ``master``-fjärrgrenen (eller valfri gren som du har konfigurerat för användning). Detta utlöser nu Jenkins-jobbet ``MyJob`` som du konfigurerade. Vad den skulle göra är i praktiken att hämta ändringarna från GitHub, skapa dem och distribuera programmet till den klusterslutpunkt som du angav i åtgärderna efter skapandet.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

