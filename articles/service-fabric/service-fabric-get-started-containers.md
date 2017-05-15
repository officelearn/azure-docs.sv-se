---
title: "Skapa en Azure Service Fabric-behållarapp | Microsoft Docs"
description: "Skapa din första behållarapp på Azure Service Fabric.  Skapa en Docker-avbildning med din app, överför avbildningen till ett behållarregister och skapa och distribuera en Service Fabric-behållarapp."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: df5832478bf7049a7bb883eacac4be961ef65cf9
ms.contentlocale: sv-se
ms.lasthandoff: 05/09/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Skapa din första Service Fabric-behållarapp
Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-behållare i ett Service Fabric-kluster. Den här snabbstarten visar hur du skapar en Docker-avbildning som innehåller en webbapp, överför den nya avbildningen till Azure Container Registry, skapar en Service Fabric-behållarapp och distribuerar behållarappen till ett Service Fabric-kluster.  Den här artikeln förutsätter att du har grundläggande kunskaper om Docker. Mer information om Docker finns i [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Översikt över Docker).

## <a name="prerequisites"></a>Krav
En utvecklingsdator som kör:
* Visual Studio 2015 eller Visual Studio 2017.
* [Service Fabric SDK och verktyg](service-fabric-get-started.md).
*  Docker för Windows.  [Hämta Docker CE för Windows (stabil)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Efter installationen startar du Docker, högerklickar på ikonen för fack och väljer **Switch to Windows containers** (Växla till Windows-behållare). Detta krävs för att köra Docker-avbildningar baserade på Windows.

Ett Windows-kluster med tre eller fler noder som kör Windows Server 2016 med behållare – [Skapa ett kluster](service-fabric-get-started-azure-cluster.md) eller [prova Service Fabric utan kostnad](http://tryazureservicefabrictest.westus.cloudapp.azure.com/). 

Ett register i Azure Container Registry – [Skapa ett behållarregister](../container-registry/container-registry-get-started-portal.md) i din Azure-prenumeration. 

## <a name="create-a-simple-web-app"></a>Skapa en enkel webbapp
Samla alla resurser som du behöver för Docker-avbildningen (för inläsning) på en plats. I den här snabbstarten skapar du en ”Hello World”-webbapp på din utvecklingsdator.

1. Skapa en katalog, t.ex. *c:\temp\helloworldapp*.
2. Skapa en underkatalog, t.ex. *c:\temp\helloworldapp\content*.
3. Skapa filen *index.html* i *c:\temp\helloworldapp\content*.
4. Redigera *index.html* och lägg till följande rad:
    ```
    <h1>Hello World!</h1>
    ```
5. Spara ändringarna i *index.html*.

## <a name="build-the-docker-image"></a>Skapa Docker-avbildningen
Skapa en avbildning baserat på [microsft/iis-avbildningen](https://hub.docker.com/r/microsoft/iis/) på Docker Hub. Microsoft/iis-avbildningen kommer från avbildningen för basoperativsystemet Windows Server Core och innehåller Internet Information Services (IIS).  När du kör den här avbildningen i behållaren startas IIS och installerade webbplatser automatiskt.

Definiera Docker-avbildningen i en Dockerfile. En Dockerfile innehåller instruktioner för att skapa avbildningen och läsa in den app som du vill köra. Dockerfile är indata för kommandot ```docker build``` som skapar avbildningen. 

1. Skapa en *Dockerfile* (utan filtillägg) i *c:\temp\helloworldapp* och lägg till följande:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    Det finns inget ```ENTRYPOINT```-kommando i denna Dockerfile. Du behöver inte kommandot. När du kör Windows Server med IIS är IIS-processen startpunkten, och den är konfigurerad att starta i basavbildningen.

    Läs [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) för mer information.

2. Kör kommandot ```docker build``` för att skapa avbildningen som kör webbappen. Öppna ett PowerShell-fönster och gå till *c:\temp\helloworldapp*. Kör följande kommando:

    ```
    docker build -t helloworldapp .
    ```
    Med det här kommandot skapas den nya avbildningen med hjälp av instruktionerna i din Dockerfile och avbildningen får namnet "helloworldapp" (-t-taggning). När en avbildning skapas hämtas basavbildningen från Docker Hub och en ny avbildning skapas som lägger till din app ovanpå basavbildningen.  [Microsft/iis-avbildningen](https://hub.docker.com/r/microsoft/iis/) och operativsystemavbildningarna är 10,5 GB och tar lång tid att ladda ned och extrahera till din utvecklingsdator.  Det kan vara en god idé att göra detta när du går på lunch eller tar en kaffepaus.  Hämtningen går snabbare om du redan har hämtat avbildningen av basoperativsystemet till utvecklingsdatorn.

3. När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Kontrollera avbildningen lokalt
Kontrollera avbildningen lokalt innan du överför den till behållarregistret.  

1. Starta behållaren med ```docker run```:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name* namnger den behållare som körs (i stället för behållar-ID:t).

2. När behållaren har startat letar du reda på dess IP-adress så att du kan ansluta till den behållare som körs via en webbläsare:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Anslut till den behållare som körs.  Öppna en webbläsare med den IP-adress som returnerades på port 8000, till exempel "http://172.31.194.61:8000". Nu visas normalt rubriken "Hello World!" i webbläsaren.

4. Om du vill stoppa behållaren kör du:

    ```
    docker stop my-web-site
    ```

5. Ta bort behållaren från utvecklingsdatorn:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Överför avbildningen till behållarregistret
När du har kontrollerat att behållaren körs på utvecklingsdatorn överför du avbildningen till registret i Azure Container Registry.

1. Kör ``docker login`` för att logga in till behållarregistret med dina [autentiseringsuppgifter för registret](../container-registry/container-registry-authentication.md).

    I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/active-directory-application-objects.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. Följande kommando skapar en tagg, eller ett alias, för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet placeras avbildningen i ```samples```-namnområdet för att undvika oreda i registrets rot.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Överför avbildningen till behållarregistret:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Skapa och paketera en tjänst som använder behållare i Visual Studio
Service Fabric SDK och verktygen innehåller en tjänstmall som hjälper dig att distribuera en behållare till ett Service Fabric-kluster.

1. Starta Visual Studio.  Välj **Arkiv** > **Nytt** > **Projekt**.
2. Välj **Service Fabric-programmet**, ge det namnet "MyFirstContainer" och klicka på **OK**.
3. Välj **Guest Container** (Gästbehållare) i listan med **tjänstmallar**.
4. I **Avbildningsnamn** skriver du "myregistry.azurecr.io/samples/helloworldapp" (den avbildning som du skickade till lagringsplatsen för behållaren). 
5. Namnge tjänsten och klicka på **OK**.
6. Om tjänsten behöver en slutpunkt för kommunikation kan du nu lägga till protokollet, porten och typen i en ```Endpoint``` i filen ServiceManifest.xml. I den här snabbstarten lyssnar tjänsten på port 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Genom att tillhandahålla ```UriScheme``` registreras automatiskt behållarslutpunkten med namngivningstjänsten för Service Fabric för identifiering. En fullständig ServiceManifest.xml-exempelfil finns i slutet av den här artikeln. 
7. Konfigurera behållarens portmappning (port till värd) genom att ange en ```PortBinding```-princip i ```ContainerHostPolicies``` för filen ApplicationManifest.xml.  I den här snabbstarten är ```ContainerPort``` 8000 (behållaren exponerar port 8000, såsom har angetts i Dockerfile) och ```EndpointRef``` är "Guest1TypeEndpoint" (slutpunkten som har definierats i tjänstmanifestet).  Inkommande begäranden till tjänsten på port 80 mappas till port 8000 för behållaren.  Om behållaren behöver autentiseras med en privat lagringsplats lägger du till ```RepositoryCredentials```.  I den här snabbstarten lägger du till kontonamnet och lösenordet för behållarregistret myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    En fullständig ApplicationManifest.xml -exempelfil finns i slutet av den här artikeln.
8. Konfigurera slutpunkten för klusteranslutningen så att du kan publicera appen i klustret.  Slutpunkten för klientanslutningen finns på översiktsbladet för ditt kluster i [Azure Portal](https://portal.azure.com). I Solution Explorer öppnar du *Cloud.xml* under **MyFirstContainer**->**PublishProfiles**.  Lägg till klusternamnet och anslutningsporten i **ClusterConnectionParameters**.  Exempel:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Spara alla filer och bygg projektet.  

10. Om du vill paketera appen högerklickar du på **MyFirstContainer** i Solution Explorer och väljer **Package** (Paketera). 

## <a name="deploy-the-container-app"></a>Distribuera behållarappen
1. Om du vill publicera appen högerklickar du på **MyFirstContainer** i Solution Explorer och väljer **Publish** (Publicera).

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett webbaserat verktyg för att granska och hantera appar och noder i ett Service Fabric-kluster. Öppna en webbläsare och navigera till http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ och följ anvisningarna för att distribuera appen.  Appen distribueras men är i ett feltillstånd tills avbildningen har laddats ned på klusternoderna (vilket kan ta en stund beroende på avbildningens storlek):  ![Fel][1]

3. Appen är klar när den har status ```Ready```  ![Ready][2] (Klar):

4. Öppna en webbläsare och navigera till http://containercluster.westus2.cloudapp.azure.com. Nu visas normalt rubriken "Hello World!" i webbläsaren.

## <a name="clean-up"></a>Rensa
Det kostar pengar så länge klustret körs. Fundera på om du vill [ta bort klustret](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  [Party-kluster](http://tryazureservicefabrictest.westus.cloudapp.azure.com/) tas bort automatiskt efter ett par timmar.

När du har överfört avbildningen till behållarregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Komplett exempel på Service Fabric-app och tjänstmanifest
Här är de fullständiga tjänst- och appmanifesten som används i den här snabbstarten.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du kör [behållare i Service Fabric](service-fabric-containers-overview.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-behållare](https://github.com/Azure-Samples/service-fabric-dotnet-containers) på GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

