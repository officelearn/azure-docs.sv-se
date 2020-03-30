---
title: Skapa en behållare för Apache Tomcat på Linux
description: Skapa Linux-behållare för att exponera ett program som körs på Apache Tomcat-server på Azure Service Fabric. Skapa en Docker-avbildning med ditt program och Apache Tomcat-server, skicka avbildningen till ett behållarregister, skapa och distribuera ett Service Fabric-behållarprogram.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614425"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Skapa Service Fabric-behållare som kör Apache Tomcat-server på Linux
Apache Tomcat är en populär, öppen källkod genomförandet av Java Servlet och Java Server teknik. Den här artikeln visar hur du skapar en behållare med Apache Tomcat och ett enkelt webbprogram, distribuerar behållaren till ett Service Fabric-kluster som kör Linux och ansluter till webbprogrammet.  

Mer information om Apache Tomcat finns på [Apache Tomcats hemsida](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Krav
* En utvecklingsdator som kör:
  * [Service Fabric SDK och verktyg](service-fabric-get-started-linux.md).
  * [Docker CE för Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Service Fabric CLI](service-fabric-cli.md)

* Ett behållarregister i Azure Container Registry. Du kan skapa ett behållarregister i din Azure-prenumeration med [Azure-portalen](../container-registry/container-registry-get-started-portal.md) eller [Azure CLI](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Skapa en Tomcat-avbildning och kör den lokalt
Följ stegen i det här avsnittet för att skapa en Docker-avbildning baserat på en Apache Tomcat-avbildning och en enkel webbapp och kör den sedan i en behållare på ditt lokala system. 
 
1. Klona [servicetyget som kommer igång med Java-exempeldatabasen](https://github.com/Azure-Samples/service-fabric-java-getting-started) på utvecklingsdatorn.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Ändra kataloger till exempelkatalogen för Apache Tomcat-servern (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Skapa en Docker-fil baserat på den officiella [Tomcat-avbildningen](https://hub.docker.com/_/tomcat/) som finns på Docker Hub och exemplet på Tomcat-servern. Skapa en fil med namnet *Dockerfile* (utan filtillägg) i katalogen *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample.* Lägg till följande i *Dockerfile* och spara dina ändringar:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Mer information finns i [Dockerfile-referensen.](https://docs.docker.com/engine/reference/builder/)


4. Kör `docker build` kommandot för att skapa den bild som kör webbprogrammet:

   ```bash
   docker build . -t tomcattest
   ```

   Det här kommandot skapar den nya avbildningen med hjälp av instruktionerna i Dockerfile, namnge (-t-märkning) avbildningen `tomcattest`. Om du vill skapa en behållaravbildning hämtas basavbildningen först ned från Docker Hub och programmet läggs till i den. 

   När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Kontrollera att det behållarprogrammet körs lokalt innan du trycker på det i behållarregistret:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name`namnger behållaren, så att du kan referera till den med ett eget namn i stället för dess ID.
   * `-p`anger portmappningen mellan behållaren och värdoperativsystemet. 

   > [!Note]
   > Porten som du `-p` öppnar med parametern ska vara den port som Tomcat-programmet lyssnar på begäranden på. I det aktuella exemplet finns det en anslutning konfigurerad i *apachetomcat/conf/server.xml-filen* för att lyssna på port 8080 för HTTP-begäranden. Den här porten mappas till port 8080 på värden. 

   Mer information om andra parametrar finns i [Dokumentationen för Docker-körning](https://docs.docker.com/engine/reference/commandline/run/).

1. Om du vill testa behållaren öppnar du en webbläsare och anger en av följande webbadresser. Du kommer att se en variant av "Hello World!" välkomstskärmen för varje webbadress.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Hej värld / sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Stoppa behållaren och ta bort den från utvecklingsdatorn:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Skicka Tomcat-avbildningen till behållarregistret
Nu när du har verifierat att Tomcat-avbildningen körs i en behållare på utvecklingsdatorn skickar du den till en databas i ett behållarregister. I den här artikeln används Azure Container Registry för att lagra avbildningen, men med vissa ändringar av stegen kan du använda alla behållarregister som du väljer. I den här artikeln registret namn antas vara *mitt register* och det fullständiga registret namn är myregistry.azurecr.io. Ändra dessa på lämpligt sätt för ditt scenario. 

1. Kör `docker login` för att logga in på behållarregistret med [registerautentiseringsuppgifterna](../container-registry/container-registry-authentication.md).

   I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/develop/app-objects-and-service-principals.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario. Eller så kan du logga in med ditt användarnamn och lösenord för registret.

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Skapa och distribuera service fabric-behållarprogrammet
Nu när du har pushat Tomcat-avbildningen till ett behållarregister kan du skapa och distribuera ett Service Fabric-behållarprogram som hämtar Tomcat-avbildningen från registret och kör den som en behållartjänst i klustret. 

1. Skapa en ny katalog utanför den lokala klonen (utanför *katalogträdet service-fabric-java-getting-started).* Växla till den och använd Yeoman för att skapa en byggnadsställning för ett behållarprogram: 

   ```bash
   yo azuresfcontainer 
   ```
   Ange följande värden när du uppmanas att göra det:

   * Namn på ditt program: ServiceFabricTomcat
   * Programtjänstens namn: TomcatService
   * Mata in bildnamnet: Ange URL:en för behållaravbildningen i behållarregistret. till exempel myregistry.azurecr.io/samples/tomcattest.
   * Kommandon: Lämna det här tomt. Eftersom den här avbildningen har en definierad startpunkt arbetsbelastningen måste du inte uttryckligen ange inkommande kommandon (kommandon körs i den container som kommer att hålla den container som körs efter start).
   * Antal instanser av gästcontainerprogram: 1

   ![Service Fabric Yeoman-generator för containrar](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. I tjänstmanifestet (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) lägger du till följande XML under roten **ServiceManfest-taggen** för att öppna porten som programmet lyssnar på begäranden på. **Slutpunktstaggen** deklarerar protokollet och porten för slutpunkten. I den här artikeln lyssnar den containeriserade tjänsten på port 8080: 

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

11. Lägg till följande XML under taggen **ServiceManifestImport** i programmanifestimport (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*). Ersätt **kontonamnet** och **lösenordet** i taggen **RepositoryCredentials** med namnet på behållarregistret och det lösenord som krävs för att logga in på det.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Taggen **ContainerHostPolicies** anger principer för att aktivera behållarvärdar.
    
   * **PortBinding-taggen** konfigurerar principen för mappning av behållarport till värdport. **Attributet ContainerPort** är inställt på 8080 eftersom behållaren exponerar port 8080, enligt angivet i Dockerfile. **Attributet EndpointRef** är inställt på "endpointTest", slutpunkten som definierats i tjänstmanifestet i föregående steg. Inkommande begäranden till tjänsten på port 8080 mappas därför till port 8080 på behållaren. 
   * Taggen **RepositoryCredentials** anger de autentiseringsuppgifter som behållaren behöver autentisera med den (privata) databas där bilden hämtas från. Du behöver inte den här principen om bilden hämtas från en offentlig databas.
    

12. Anslut till serviceinfrastrukturklustret i mappen *ServiceFabricTomcat.* 

   * Om du vill ansluta till det lokala servicetygets kluster kör du:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Om du vill ansluta till ett säkert Azure-kluster kontrollerar du att klientcertifikatet finns som en PEM-fil i *ServiceFabricTomcat-katalogen* och kör: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     Ersätt `your-certificate.pem` med namnet på klientcertifikatfilen i föregående kommando. I utvecklings- och testmiljöer används klustercertifikatet ofta som klientcertifikat. Om ditt certifikat inte är självsignerat utelämnar du parametern. `-no-verify` 
       
     Klustercertifikat hämtas vanligtvis lokalt som PFX-filer. Om du inte redan har ditt certifikat i PEM-format kan du köra följande kommando för att skapa en PEM-fil från en PFX-fil:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Om PFX-filen inte är lösenordsskyddad använder du `-passin pass:` den sista parametern.


13. Kör installationsskriptet som finns i mallen för att distribuera programmet till klustret. Skriptet kopierar programpaketet till klustrets bildarkiv, registrerar programtypen och skapar en instans av programmet.

     ```bash
     ./install.sh
     ```

   När du har kört installationsskriptet öppnar du en webbläsare och navigerar till Service Fabric Explorer:
    
   * Använd `http://localhost:19080/Explorer` (ersätt *localhost* med den privata IP-adressen för den virtuella datorn om du använder Vagrant på Mac OS X) i ett lokalt kluster.
   * Använd `https://PublicIPorFQDN:19080/Explorer`i ett säkert Azure-kluster . 
    
   Expandera **noden Program** och observera att det nu finns en post för din programtyp, **ServiceFabricTomcatType**och en annan för den första instansen av den typen. Det kan ta några minuter för programmet att helt distribuera, så ha tålamod.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Om du vill komma åt programmet på Tomcat-servern öppnar du ett webbläsarfönster och anger någon av följande webbadresser. Om du har distribuerats till det lokala klustret använder du *localhost* för *PublicIPorFQDN*. Du kommer att se en variant av "Hello World!" välkomstskärmen för varje webbadress.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Rensa
Använd avinstallationsskriptet som finns i mallen för att ta bort programinstansen från klustret och avregistrera programtypen.

```bash
./uninstall.sh
```

När du har överfört avbildningen till containerregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Nästa steg
* Snabbsteg på ytterligare Linux-behållarfunktioner finns i [Skapa ditt första Service Fabric-behållarprogram på Linux](service-fabric-get-started-containers-linux.md).
* Mer detaljerade steg om Linux-behållare finns i [självstudiekursen Skapa en Linux-behållarapp.](service-fabric-tutorial-create-container-images.md)
* Mer information om hur du kör [containrar i Service Fabric](service-fabric-containers-overview.md).


