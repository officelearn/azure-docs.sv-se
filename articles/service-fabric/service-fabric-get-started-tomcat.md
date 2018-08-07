---
title: Skapa en Azure Service Fabric-behållare för Apache Tomcat-servern på Linux | Microsoft Docs
description: Skapa Linux-behållare om du vill exponera ett program som körs på Apache Tomcat-servern på Azure Service Fabric. Skapa en dockeravbildning med ditt program och Apache Tomcat-servern, överför avbildningen till ett behållarregister, skapa och distribuera ett Service Fabric-behållarprogram.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: 29208bcbdbe6ad01d0e1ac7343bd921f3287260a
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581919"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Skapa Service Fabric-behållare som kör Apache Tomcat-servern på Linux
Apache Tomcat är en populär implementering med öppen källkod av Java Servlet och Server med Java-tekniker. Den här artikeln visar hur du skapar en behållare med Apache Tomcat och ett enkelt webbprogram, distribuerar du behållaren till Service Fabric-kluster som kör Linux och ansluta till webbprogrammet.  

Läs mer om Apache Tomcat i den [Apache Tomcat startsidan](http://tomcat.apache.org/). 

## <a name="prerequisites"></a>Förutsättningar
* En utvecklingsdator som kör:
  * [Service Fabric SDK och verktyg](service-fabric-get-started-linux.md).
  * [Docker CE för Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Ett behållarregister i Azure Container Registry. Du kan skapa ett behållarregister i Azure-prenumerationen med [Azure-portalen](../container-registry/container-registry-get-started-portal.md) eller [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Skapa en Tomcat-avbildning och kör lokalt
Följ stegen i det här avsnittet och skapa en Docker-avbildning som är baserat på en Apache Tomcat-avbildning och en enkel webbapp som du kan köra den i en behållare i det lokala systemet. 
 
1. Klona den [komma igång med Java Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started) lagringsplats med exempel på din utvecklingsdator.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Ändra sökvägen till katalogen Apache Tomcat-servern exemplet (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Skapa en Docker-fil utifrån officiellt [Tomcat bild](https://hub.docker.com/_/tomcat/) finns på Docker Hub och exempel för Tomcat-servern. I den *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* katalogen, skapa en fil med namnet *Dockerfile* (med utan filtillägget). Lägg till följande i *Dockerfile* och spara dina ändringar:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Se den [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) för mer information.


4. Kör den `docker build` kommando för att skapa avbildningen som kör ditt webbprogram:

   ```bash
   docker build . -t tomcattest
   ```

   Det här kommandot skapas den nya avbildningen med hjälp av anvisningarna i Dockerfilen naming (-t-taggning) avbildningen `tomcattest`. Om du vill skapa en behållaravbildning basavbildningen är först ladda ned basavbildningen från Docker Hub och programmet har lagts till den. 

   När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Kontrollera att programmet körs lokalt innan du överför den till behållarregistret:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` fältnamnen behållaren, så att du kan referera till den med hjälp av ett eget namn i stället för dess-ID.
   * `-p` Anger portmappning mellan behållaren och värdoperativsystemet. 

   > [!Note]
   > Den port som du öppna med den `-p` parameter ska vara den port som programmets Tomcat lyssnar efter förfrågningar på. I det aktuella exemplet finns en anslutning som konfigurerats i den *ApacheTomcat/conf/server.xml* fil att lyssna efter HTTP-begäranden på port 8080. Den här porten är mappad till port 8080 på värden. 

   Mer information om andra parametrar, finns det [Docker run dokumentation](https://docs.docker.com/engine/reference/commandline/run/).

1. Testa din behållare genom att öppna en webbläsare och ange något av följande webbadresser. Du ser en variant av ”Hello World”! välkomstskärmen för varje URL.

   - http://localhost:8080/hello 
   - http://localhost:8080/hello/sayhello 
   - http://localhost:8080/hello/sayhi 

   ![Hello world /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Stoppa behållaren och ta bort den från utvecklingsdatorn:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Överför Tomcat-avbildningen till behållarregistret
Nu när du har kontrollerat att Tomcat-avbildningen körs i en behållare på utvecklingsdatorn kan du skicka den till en databas i ett behållarregister. Den här artikeln använder Azure Container Registry för att lagra avbildningen, men med vissa ändringar av steg du kan använda valfritt behållarregister som du väljer. I den här artikeln registernamnet antas vara *myregistry* och fullständig registernamnet är myregistry.azurecr.io. Ändra dem på lämpligt sätt för ditt scenario. 

1. Kör `docker login` för att logga in till containerregistret med dina [autentiseringsuppgifter för registret](../container-registry/container-registry-authentication.md).

   I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario. Du kan också logga in med ditt användarnamn och lösenord för registret.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Följande kommando skapar en tagg, eller ett alias, för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet placeras avbildningen i `samples`-namnområdet för att undvika oreda i registrets rot.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Överför avbildningen till containerregistret:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Skapa och distribuera Service Fabric-behållarprogrammet
Nu när du har push-överfört Tomcat-avbildningen till ett behållarregister, kan du skapa och distribuera ett Service Fabric-behållarprogram som hämtar Tomcat-avbildningen från registret och kör den som en tjänst som använder behållare i klustret. 

1. Skapa en ny katalog utanför din lokala klon (utanför den *service-fabric-java-getting-started* katalogträdet). Växla till den och använda Yeoman för att skapa ett kodskelett för ett program med behållare: 

   ```bash
   yo azuresfcontainer 
   ```
   Ange följande värden när du uppmanas till detta:

   * Namnge ditt program: ServiceFabricTomcat
   * Namnet på programtjänsten: TomcatService
   * Ange avbildningens namn: Ange en URL för behållaravbildningen i behållarregistret. till exempel myregistry.azurecr.io/samples/tomcattest.
   * Kommandon: Lämna det tomt. Eftersom den här avbildningen har en definierad startpunkt arbetsbelastningen måste du inte uttryckligen ange inkommande kommandon (kommandon körs i den container som kommer att hålla den container som körs efter start).
   * Antal instanser av gäst-behållarprogram: 1

   ![Service Fabric Yeoman-generator för containrar](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. I tjänstmanifestet (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*), Lägg till följande XML-filen under roten **ServiceManfest** taggen för att öppna porten din programmet lyssnar på begäranden på. Den **Endpoint** taggen deklarerar protokoll och port för slutpunkten. I den här artikeln lyssnar behållartjänsten på port 8080: 

    ```xml
    <Resources>
      <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to 
         listen. Please note that if your service is partitioned, this port is shared with 
         replicas of different partitions that are placed in your code. -->
        <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
      </Endpoints>
    </Resources>
    ```

11. I applikationsmanifestet (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) under den **ServiceManifestImport** indatatagg, Lägg till följande XML-filen. Ersätt den **AccountName** och **lösenord** i den **RepositoryCredentials** tagg med namnet på behållarregistret och ett lösenord för att logga in på den.

    ```xml
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
      </ContainerHostPolicies>
    </Policies>
    ```

    Den **ContainerHostPolicies** taggen anger principer för att aktivera behållare-värdar.
    
    * Den **PortBinding** taggen konfigurerar behållare till värd port mappning av principen. Den **ContainerPort** attributet har angetts till 8080 eftersom behållaren exponerar port 8080, som anges i Dockerfile. Den **EndpointRef** attributet är inställt på ”endpointTest”, slutpunkt som definierats i tjänstmanifestet i föregående steg. Därför mappas inkommande begäranden till tjänsten på port 8080 till port 8080 för behållaren. 
    * Den **RepositoryCredentials** taggen anger de autentiseringsuppgifter som behållaren måste autentiseras mot lagringsplatsen (privat) där den hämtar avbildningen från. Du behöver inte den här principen om avbildningen ska hämtas från en offentlig lagringsplats.
    

12. I den *ServiceFabricTomcat* mapp, ansluta till service fabric-kluster. 

    * Om du vill ansluta till det lokala Service Fabric-klustret kör du:

       ```bash
       sfctl cluster select --endpoint http://localhost:19080
       ```
    
    * Om du vill ansluta till ett säkert kluster i Azure, kontrollera att klientcertifikatet finns som en PEM-fil i den *ServiceFabricTomcat* katalogen och kör: 

       ```bash
       sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
       ```
       I det föregående kommandot ersätter `your-certificate.pem` med namnet på din klientcertifikatfilen. I utvecklings- och testmiljöer används ofta klustercertifikatet som på klientcertifikatet. Om certifikatet inte självsignerade utelämna den `-no-verify` parametern. 
       
       Klustercertifikat hämtas vanligtvis lokalt som PFX-filer. Om du inte redan har certifikatet i PEM-format, kan du köra följande kommando för att skapa en PEM-fil från en PFX-fil:

       ```bash
       openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
       ```

       Om din .pfx-filen inte är lösenordsskyddad, använda `-passin pass:` för den sista parametern.


13. Kör installationsskriptet som medföljer mallen för att distribuera programmet till klustret. Skriptet kopierar programpaketet till klustrets avbildningsarkiv, registrerar programtypen och skapa en instans av programmet.

       ```bash
       ./install.sh
       ```

    När du har kört installationsskriptet, öppna en webbläsare och gå till Service Fabric Explorer:
    
    * I ett lokalt kluster, använda http://localhost:19080/Explorer (Ersätt *localhost* med privata IP-Adressen för den virtuella datorn om du använder Vagrant på Mac OS X).
    * På ett säkert Azure-kluster använder https://PublicIPorFQDN:19080/Explorer. 
    
    Expandera den **program** nod och Observera att det finns nu en post för din programtyp **ServiceFabricTomcatType**, och en annan för den första instansen av den typen. Det kan ta några minuter för programmet kan fullständigt distribuera så ha tålamod.

    ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. För att komma åt programmet på Tomcat-servern, öppna ett webbläsarfönster och ange någon av följande webbadresser. Om du har distribuerat till det lokala klustret kan du använda *localhost* för *PublicIPorFQDN*. Du ser en variant av ”Hello World”! välkomstskärmen för varje URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Rensa
Använd installationsskriptet som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen.

```bash
./uninstall.sh
```

När du har överfört avbildningen till containerregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Nästa steg
* Snabbsteg på extrafunktioner för Linux-behållare, läsa [skapa din första Service Fabric-behållarapp i Linux](service-fabric-get-started-containers-linux.md).
* Mer detaljerade instruktioner för Linux-behållare, läsa den [skapa en Linux-behållare app självstudiekurs](service-fabric-tutorial-create-container-images.md) självstudien.
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-containers-overview.md).


