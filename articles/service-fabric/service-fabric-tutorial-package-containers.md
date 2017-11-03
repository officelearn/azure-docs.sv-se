---
title: "Paketera och distribuera ett program för Service Fabric-behållare | Microsoft Docs"
description: "Lär dig hur du skapar en definition av en Azure Service Fabric-program med hjälp av Yeoman och paketera programmet."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker-behållare, Mikrotjänster, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 9b1498d76680185b45edf9ac7e1747bfa6794eec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="package-and-deploy-containers-as-a-service-fabric-application"></a>Paketera och distribuera behållare som ett Service Fabric-program

Den här kursen ingår två i en serie. I den här självstudiekursen används en mall-generatorn (Yeoman) för att generera en definition för Service Fabric. Det här programmet kan sedan användas för att distribuera behållare till Service Fabric. I den här självstudiekursen får du lära du dig att: 

> [!div class="checklist"]
> * Installera Yeoman  
> * Skapa ett programpaket med hjälp av Yeoman
> * Konfigurera inställningar i programpaket för användning med behållare
> * Skapa programmet  
> * Distribuera och köra programmet 
> * Rensa programmet

## <a name="prerequisites"></a>Krav

- Behållaren bilder pushas till Azure-behållare registret som skapats i [del 1](service-fabric-tutorial-create-container-images.md) serie som används i den här kursen.
- Linux-utvecklingsmiljö är [konfigurera](service-fabric-tutorial-create-container-images.md).

## <a name="install-yeoman"></a>Installera Yeoman
Service fabric innehåller scaffold-teknik för verktyg för att skapa program från terminal med Yeoman mall generator. Följ stegen nedan för att kontrollera att du har Yeoman mall generator. 

1. Installera nodejs och NPM på din dator. Observera att, Mac OS x-användare måste använda package manager Homebrew

    ```bash
    sudo apt-get install npm && sudo apt install nodejs-legacy
    ```
2. Installera Yeoman mall generator på datorn via NPM 

    ```bash
    sudo npm install -g yo
    ```
3. Installera Service Fabric Yeoman behållare generator

    ```bash 
    sudo npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Paketera en Docker-avbildningsbehållare med Yeoman

1. Om du vill skapa en behållare för Service Fabric program i katalogen 'självstudie om behållaren' på klonade databasen, kör följande kommando.

    ```bash
    yo azuresfcontainer
    ```
2. Namnge programmet ”TestContainer” och namnge programmet service ”azurevotefront”.
3. Ange den behållare avbildningssökvägen i ACR för klientdel lagringsplatsen – till exempel 'test.azurecr.io/azure-vote-front:v1'. 
4. Tryck på RETUR för att avsluta kommandona avsnittet tomt.
5. Ange ett instansantal på 1.

Följande visar indata och utdata för körs den yo kommando:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Om du vill lägga till en till behållartjänst till ett program som redan har skapats med hjälp av yeoman utför du följande steg:

1. Gå till katalogen för den **TestContainer** directory
2. Kör `yo azuresfcontainer:AddService` 
3. Name service azurevoteback
4. Ange avbildning behållare i ACR för backend-lagringsplatsen – till exempel 'test.azurecr.io/azure-vote-back:v1'
5. Tryck på RETUR för att avsluta kommandona avsnittet tom
6. Ange ett instansantal på ”1”.

Posterna för att lägga till den tjänst som används visas alla:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: <acrName>.azurecr.io/azure-vote-back:v1
? Commands: 
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Vi arbetar för resten av den här kursen den **TestContainer** directory.

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Konfigurera applikationsmanifestet med autentiseringsuppgifter för Azure-behållare registret
Vi behöver ange autentiseringsuppgifter i för Service Fabric och hämtar behållaren bilder från registret för Azure-behållaren i **ApplicationManifest.xml**. 


Logga in på din ACR-instans. Använd den [az acr inloggning](/cli/azure/acr#az_acr_login) kommando för att slutföra åtgärden. Ange unika namnet på behållaren registret när den skapades.

```bash
az acr login --name <acrName>
```

Kommandot returnerar en **inloggningen har slutförts** meddelande när den har slutförts.

Kör följande kommando för att hämta lösenordet för behållaren registret. Det här lösenordet används av Service Fabric för att autentisera med ACR att hämta avbildningar för behållaren.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

I den **ApplicationManifest.xml**, Lägg till kodstycke under den **ServiceManifestImport** element för varje tjänst. Infoga din **acrName** för den **AccountName** fältet och lösenordet som returnerades från föregående kommando används för den **lösenord** fältet. En fullständig **ApplicationManifest.xml** har angetts i slutet av det här dokumentet. 

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Konfigurera kommunikation och portmappning mellan behållare och värd

### <a name="configure-communication-port"></a>Konfigurera kommunikationsport

Konfigurera en HTTP-slutpunkt så att klienter kan kommunicera med din tjänst.  Öppna den *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* filen och deklarera en slutpunkt-resurs i den **ServiceManifest** element.  Lägg till protokollet, porten och namnet. Tjänsten lyssnar på port 80 för den här självstudiekursen. 
  
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
  
På liknande sätt kan ändra Service Manifest för backend-tjänsten. Den här självstudien bevaras 6379 redis standardvärdet.
```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" UriScheme="http" Port="6379" Protocol="http"/>
  </Endpoints>
</Resources>
```
Att tillhandahålla den **UriScheme**automatiskt registreras behållaren slutpunkten med namngivning av Service Fabric-tjänsten för synlighet. En fullständig ServiceManifest.xml-exempelfil för serverdelstjänsten tillhandahålls som ett exempel i slutet av den här artikeln. 

### <a name="map-container-ports-to-a-service"></a>Mappa behållaren portar till en tjänst
    
För att exponera behållare i klustret, behöver vi också skapa en port-bindning i ApplicationManifest.xml. Den **PortBinding** principen refererar till den **slutpunkter** vi har definierat i den **ServiceManifest.xml** filer. Inkommande begäranden till dessa slutpunkter hämta mappas till behållaren portarna som öppnats och avgränsas här. I den **ApplicationManifest.xml** lägger du till följande kod för att binda port 80 och 6379 till slutpunkterna. En fullständig **ApplicationManifest.xml** är tillgängliga i slutet av det här dokumentet. 
  
```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Lägg till ett DNS-namn till serverdelstjänsten
  
För Service Fabric att tilldela den här DNS-namn till serverdelstjänsten namnet måste anges i den **ApplicationManifest.xml**. Lägg till den **ServiceDnsName** attribut till den **Service** element som visas: 
  
```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

Frontend-tjänsten läser en miljövariabel för att känna till DNS-namnet för Redis-instansen. Miljövariabeln har definierats i Dockerfile som visas:
  
```Dockerfile
ENV REDIS redisbackend.testapp
```
  
Python-skriptet som återger front end använder den här DNS-namn att matcha och ansluta till arkivet backend redis som visas:

```python
# Get DNS Name
redis_server = os.environ['REDIS'] 

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Nu i självstudierna är mallen för servicepaket program tillgängliga för distribution till ett kluster. Det här programmet distribueras i efterföljande självstudierna och kördes i ett Service Fabric-kluster.

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster
Om du vill distribuera programmet till ett kluster i Azure kan du antingen använda ett eget kluster, eller använda ett partykluster.

Partykluster är kostnadsfria, tidsbegränsade Service Fabric-kluster som finns på Azure. Den hanteras av Service Fabric-teamet där alla kan distribuera program och lär dig mer om plattformen. [Följ dessa instruktioner](http://aka.ms/tryservicefabric) för att få åtkomst till ett partykluster. 

Information om hur du skapar ett eget kluster finns i [Skapa ditt första Service Fabric-kluster i Azure](service-fabric-get-started-azure-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Skapa och distribuera programmet till klustret
Du kan distribuera programmet Azure klustret med hjälp av Service Fabric-CLI. Om Service Fabric CLI inte installerat på datorn, följer du anvisningarna [här](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) att installera den. 

Anslut till Service Fabric-klustret i Azure.

```bash
sfctl cluster select --endpoint http://lin4hjim3l4.westus.cloudapp.azure.com:19080
```

Använd installationsskriptet som anges i den **TestContainer** directory för att kopiera programpaketet till klustrets avbildningsarkivet registrera programtypen och skapa en instans av programmet.

```bash
./install.sh
```

Öppna en webbläsare och gå till Service Fabric-Utforskaren på http://lin4hjim3l4.westus.cloudapp.azure.com:19080/Explorer. Expandera noden program och Observera att det finns en post för din typ av program och en annan för instansen.

![Service Fabric Explorer][sfx]

Öppna en webbläsare och gå till kluster-url – till exempel http://lin0823ryf2he.cloudapp.azure.com:80 för att ansluta till program som körs. Du bör se röst-programmet i webbgränssnittet.

![votingapp][votingapp]

## <a name="clean-up"></a>Rensa
Använd avinstallationsskriptet som medföljer mallen för att ta bort programinstansen från klustret och avregistrera programtypen. Det här kommandot tar tid att rensa instansen och kommandot 'install.sh' kan inte köras omedelbart efter det här skriptet. 

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Exempel på slutförda manifest

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies> 
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies> 
        <ContainerHostPolicies CodePackageRef="Code">
          <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>Frontend ServiceManifest.xml 
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>my.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="8080" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>Redis ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>my.azurecr.io/azure-vote-back:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" UriScheme="http" Port="6379" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```
## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har flera behållare paketeras till en Service Fabric-program med hjälp av Yeoman. Det här programmet har sedan distribueras och körs på ett Service Fabric-kluster. Följande steg har slutförts:

> [!div class="checklist"]
> * Installera Yeoman  
> * Skapa ett programpaket med hjälp av Yeoman
> * Konfigurera inställningar i programpaket för användning med behållare
> * Skapa programmet  
> * Distribuera och köra programmet 
> * Rensa programmet

Gå vidare till nästa kurs mer information om växling vid fel och skalning av programmet i Service Fabric.

> [!div class="nextstepaction"]
> [Mer information om växling vid fel och skala program](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png


