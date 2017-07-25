---
title: "Skapa en Azure Service Fabric-behållarapp | Microsoft Docs"
description: "Skapa din första Windows-behållarapp på Azure Service Fabric.  Skapa en Docker-avbildning med en Python-app, överför avbildningen till ett behållarregister och skapa och distribuera en Service Fabric-behållarapp."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 8c9d6c65666b5ffedf058e0a83d4fc41fff80235
ms.contentlocale: sv-se
ms.lasthandoff: 07/15/2017

---

# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Skapa din första Service Fabric-behållarapp i Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Du behöver inga göra några ändringar i din app för att köra en befintlig app i en Windows-behållare i ett Service Fabric-kluster. Den här artikeln vägleder dig genom att skapa en Docker-avbildning som innehåller ett Python [Flask](http://flask.pocoo.org/)-program och distribuera den till ett Service Fabric-kluster.  Du kan också dela programmet via [Azure Container-registret](/azure/container-registry/).  Den här artikeln förutsätter att du har grundläggande kunskaper om Docker. Mer information om Docker finns i [Docker Overview](https://docs.docker.com/engine/understanding-docker/) (Översikt över Docker).

## <a name="prerequisites"></a>Krav
En utvecklingsdator som kör:
* Visual Studio 2015 eller Visual Studio 2017.
* [Service Fabric SDK och verktyg](service-fabric-get-started.md).
*  Docker för Windows.  [Hämta Docker CE för Windows (stabil)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Efter installationen startar du Docker, högerklickar på ikonen för fack och väljer **Switch to Windows containers** (Växla till Windows-behållare). Detta krävs för att köra Docker-avbildningar baserade på Windows.

Ett Windows-kluster med tre eller fler noder som kör Windows Server 2016 med behållare – [Skapa ett kluster](service-fabric-cluster-creation-via-portal.md) eller [prova Service Fabric utan kostnad](https://aka.ms/tryservicefabric). 

Ett register i Azure Container Registry – [Skapa ett behållarregister](../container-registry/container-registry-get-started-portal.md) i din Azure-prenumeration. 

## <a name="define-the-docker-container"></a>Definiera dockerbehållare
Skapa en avbildning baserat på [Python-avbildningen](https://hub.docker.com/_/python/) på Docker Hub. 

Definiera Docker-behållaren i en Dockerfile. Dockerfile innehåller instruktioner för att ställa in miljön i di behållare, läsa in programmet som du vill köra och mappa portar. Dockerfile är indata för kommandot `docker build` som skapar avbildningen. 

Skapa en tom katalog och skapa filen *Dockerfile* (utan filtillägget). Lägg till följande i *Dockerfile* och spara dina ändringar:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Läs [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) för mer information.

## <a name="create-a-simple-web-application"></a>Skapa en enkel webbapp
Skapa en flask-webbapplikation som lyssnar på port 80 och returnerar ”Hello World”!.  Skapa filen *requirements.txt* i samma katalog.  Lägg till följande och spara dina ändringar:
```
Flask
```

Skapa även filen *app.py* och lägg till följande:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():
    
    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="build-the-image"></a>Skapa avbildningen
Kör kommandot `docker build` för att skapa avbildningen som kör ditt webbprogram. Öppna ett PowerShell-fönster och navigera till den katalog som innehåller din Dockerfile. Kör följande kommando:

```
docker build -t helloworldapp .
```

Med det här kommandot skapas den nya avbildningen med hjälp av instruktionerna i din Dockerfile och avbildningen får namnet "helloworldapp" (-t-taggning). Genom att skapa en avbildning hämtas basavbildningen från Docker Hub och en ny avbildning skapas som lägger till ditt program ovanpå basavbildningen.  

När build-kommandot har slutförts kör du `docker images`-kommandot för att se information om den nya avbildningen:

```
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Kör programmet lokalt
Kontrollera avbildningen lokalt innan du överför den till behållarregistret.  

Kör programmet:

```
docker run -d --name my-web-site helloworldapp
```

*name* namnger den behållare som körs (i stället för behållar-ID:t).

När behållaren har startat letar du reda på dess IP-adress så att du kan ansluta till den behållare som körs via en webbläsare:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Anslut till den behållare som körs.  Öppna en webbläsare som pekar på den returnerade IP-adressen, till exempel ”http://172.31.194.61”. Nu visas normalt rubriken "Hello World!" i webbläsaren.

Om du vill stoppa behållaren kör du:

```
docker stop my-web-site
```

Ta bort behållaren från utvecklingsdatorn:

```
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Överför avbildningen till behållarregistret
När du har kontrollerat att behållaren körs på utvecklingsdatorn överför du avbildningen till registret i Azure Container Registry.

Kör ``docker login`` för att logga in till behållarregistret med dina [autentiseringsuppgifter för registret](../container-registry/container-registry-authentication.md).

I följande exempel skickas ID:t och lösenordet för ett Azure Active Directory [-tjänstobjekt](../active-directory/active-directory-application-objects.md). Du kanske till exempel har tilldelat ett tjänstobjekt till registret för ett automatiseringsscenario. Du kan också logga in med ditt användarnamn och lösenord för registret.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Följande kommando skapar en tagg, eller ett alias, för avbildningen, med en fullständigt kvalificerad sökväg till registret. I det här exemplet placeras avbildningen i ```samples```-namnområdet för att undvika oreda i registrets rot.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Överför avbildningen till behållarregistret:

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
6. Om tjänsten behöver en slutpunkt för kommunikation kan du nu lägga till protokollet, porten och typen i en ```Endpoint``` i filen ServiceManifest.xml. Behållartjänsten för den här artikeln lyssnar på port 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    ```
    Genom att tillhandahålla ```UriScheme``` registreras automatiskt behållarslutpunkten med namngivningstjänsten för Service Fabric för identifiering. En fullständig ServiceManifest.xml-exempelfil finns i slutet av den här artikeln. 
7. Konfigurera behållarens portmappning (port till värd) genom att ange en ```PortBinding```-princip i ```ContainerHostPolicies``` för filen ApplicationManifest.xml.  I den här artikeln är ```ContainerPort``` 8081 (behållaren exponerar port 80, som anges i Dockerfile) och ```EndpointRef``` är ”Guest1TypeEndpoint” (slutpunkt som definierats i tjänstmanifestet).  Inkommande begäranden till tjänsten på port 8081 mappas till port 80 på behållaren.  Om behållaren behöver autentiseras med en privat lagringsplats lägger du till ```RepositoryCredentials```.  I den här artikeln lägger du till kontonamnet och lösenordet för behållarregistret myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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

## <a name="deploy-the-container-application"></a>Distribuera behållarappen
Om du vill publicera appen högerklickar du på **MyFirstContainer** i Solution Explorer och väljer **Publish** (Publicera).

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) är ett webbaserat verktyg för att granska och hantera appar och noder i ett Service Fabric-kluster. Öppna en webbläsare och gå till http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ och följ programdistributionen.  Appen distribueras men är i ett feltillstånd tills avbildningen har laddats ned på klusternoderna (vilket kan ta en stund beroende på avbildningens storlek): ![Fel][1]

Appen är klar när den har ```Ready```status: ![Ready][2] (Klar)

Öppna en webbläsare och navigera till http://containercluster.westus2.cloudapp.azure.com:8081. Nu visas normalt rubriken "Hello World!" i webbläsaren.

## <a name="clean-up"></a>Rensa
Det kostar pengar så länge klustret körs. Fundera på om du vill [ta bort klustret](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  [Party-kluster](http://tryazureservicefabric.westus.cloudapp.azure.com/) tas bort automatiskt efter ett par timmar.

När du har överfört avbildningen till behållarregistret kan du ta bort den lokala avbildningen från utvecklingsdatorn:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Komplett exempel på Service Fabric-app och tjänstmanifest
Här är de fullständiga tjänst- och appmanifesten som används i den här artikeln.

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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
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
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
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
* Läs kursen [Distribuera ett .NET-program i en behållare](service-fabric-host-app-in-a-container.md).
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
* Se [kodexempel för Service Fabric-behållare](https://github.com/Azure-Samples/service-fabric-dotnet-containers) på GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

