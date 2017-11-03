---
title: "Hantera flera miljöer i Service Fabric | Microsoft Docs"
description: "Service Fabric-program kan köras på kluster i intervallet från en dator till tusentals datorer. I vissa fall kommer du vill konfigurera ditt program på olika sätt för de olika miljöer. Den här artikeln beskriver hur du definierar parametrar för olika program per miljö."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.openlocfilehash: 671cc9b0f7b7b37fcf5b052f7e34bc98e66b2838
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-application-parameters-for-multiple-environments"></a>Hantera programparametrar för miljöer med flera
Du kan skapa Azure Service Fabric-kluster genom att använda var som helst från en till tusentals datorer. När programmet binärfiler kan köras utan ändringar i den här brett spektrum av miljöer, vill du ofta konfigurera programmet på olika sätt, beroende på antalet datorer som du distribuerar till.

Ett enkelt exempel bör du `InstanceCount` för den tillståndslösa tjänsten. När du kör program i Azure kan vill du vanligtvis ange den här parametern till särskilda värdet ”1”. Den här konfigurationen garanterar att din tjänst körs på varje nod i klustret (eller alla noder i nodtypen om du har angett en begränsning för placering). Den här konfigurationen är dock inte lämpligt för ett kluster med enskild dator eftersom du inte kan ha flera processer som lyssnar på samma slutpunkten på en enskild dator. I stället kan du vanligtvis ange `InstanceCount` till ”1”.

## <a name="specifying-environment-specific-parameters"></a>Ange miljöspecifikt parametrar
Lösningen på det här problemet är en uppsättning parametrar standardtjänster och parametern-programfilerna Fyll i de parametervärden för en given miljö. Standardtjänster och programparametrar konfigureras i manifesten applikationen eller tjänsten. Schemadefinitionen för ServiceManifest.xml och ApplicationManifest.xml har installerats med Service Fabric-SDK och verktyg för *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

### <a name="default-services"></a>Standardtjänster
Service Fabric-program består av en uppsättning instanser av tjänsten. Det är möjligt att skapa ett tomt program och skapa sedan alla tjänstinstanser dynamiskt, har de flesta program en uppsättning kärntjänster som alltid ska skapas när programmet instansieras. Dessa kallas ”standardtjänster”. De har angetts i applikationsmanifestet med platshållare för per miljö configuration ingår i hakparenteser:

```xml
  <DefaultServices>
      <Service Name="Stateful1">
          <StatefulService
              ServiceTypeName="Stateful1Type"
              TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
              MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

              <UniformInt64Partition
                  PartitionCount="[Stateful1_PartitionCount]"
                  LowKey="-9223372036854775808"
                  HighKey="9223372036854775807"
              />
        </StatefulService>
    </Service>
  </DefaultServices>
```

Var och en av de namngivna parametrarna måste definieras inom elementet parametrar i programmanifestet:

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

DefaultValue-attributet anger värdet som ska användas i frånvaron av en mer specifik parameter för en given miljö.

> [!NOTE]
> Alla service-instansparametrar är inte lämplig för konfigurationen av per miljö. I exemplet ovan kan definieras uttryckligen LowKey och HighKey värden för tjänstens partitioneringsschema för alla instanser av tjänsten eftersom intervallet partition är en funktion av data-domänen, inte i miljön.
> 
> 

### <a name="per-environment-service-configuration-settings"></a>Konfigurationsinställningar för per miljö
Den [Service Fabric programmodell](service-fabric-application-model.md) kan tjänsterna omfattar av konfigurationpaket som innehåller anpassade nyckel-värdepar som är läsbart vid körning. Värdena för dessa inställningar kan också skiljas åt av miljö genom att ange en `ConfigOverride` i programmanifestet.

Anta att du har följande inställning i Config\Settings.xml-filen för den `Stateful1` tjänsten:

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
Om du vill åsidosätta det här värdet för en specifik Programmiljö par, skapa en `ConfigOverride` när du importerar tjänstmanifestet i programmanifestet.

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
Den här parametern kan sedan konfigureras av miljö som ovan. Du kan göra detta genom att deklarera i avsnittet parametrar i programmanifestet och ange miljö-specifika värden i parametern programfilerna.

> [!NOTE]
> När det gäller konfigurationsinställningar, det finns tre platser där du kan ange värdet för en nyckel: konfigurationspaket för tjänsten och programmanifestet parameterfilen program. Service Fabric kommer alltid att välja från program parameterfilen första (om det angetts), sedan programmanifestet och slutligen konfigurationspaketet.
> 
> 

### <a name="setting-and-using-environment-variables"></a>Ställa in och använda miljövariabler 
Du kan ange och ange miljövariabler i filen ServiceManifest.xml och åsidosätta dessa i filen ApplicationManifest.xml på grundval av per instans.
Exemplet nedan visar två miljövariabler, en med ett värde har ställts in och den andra åsidosätts. Du kan använda programmet parametrar för att ställa in miljön variabler värden på samma sätt som att dessa användes för config åsidosättningar.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
Om du vill åsidosätta miljövariabler i ApplicationManifest.xml referera kodpaketet i ServiceManifest med den `EnvironmentOverrides` element.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 När instansen för tjänsten har skapats kan du komma åt miljövariablerna från kod. t.ex. I C# kan du göra följande

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Miljövariabler för Service Fabric
Service Fabric har inbyggda miljövariabler som anges för varje service-instans. En fullständig lista över miljövariabler är nedan, där de i fetstil är de som du ska använda i din tjänst, den andra som används av Service Fabric runtime. 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_ [YourServiceName] TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

Koden belows visar hur du listar miljövariablerna Service Fabric
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
Följande är exempel på miljövariabler för en typ av program som kallas `GuestExe.Application` med en typ av kallas `FrontEndService` när det körs på den lokala dev-datorn.

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>Parametern programfiler
Service Fabric application-projekt kan innehålla en eller flera parametern programfiler. Var och en av dem definierar de specifika värdena för parametrarna som definieras i programmanifestet:

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
Som standard innehåller tre parametern programfiler, Local.1Node.xml, Local.5Node.xml och Cloud.xml i ett nytt program:

![Parametern programfiler i Solution Explorer][app-parameters-solution-explorer]

Om du vill skapa en parameterfil helt enkelt kopiera och klistra in en befintlig och ge det ett nytt namn.

## <a name="identifying-environment-specific-parameters-during-deployment"></a>Identifiera miljö-specifika parametrar under distributionen
Vid tidpunkten för distribution måste du välja lämpliga parameterfilen ska användas med ditt program. Du kan göra detta via dialogrutan Publicera i Visual Studio eller PowerShell.

### <a name="deploy-from-visual-studio"></a>Distribuera från Visual Studio
Du kan välja från listan över tillgängliga parametern filer när du publicerar ditt program i Visual Studio.

![Välj en parameterfil i dialogrutan Publicera][publishdialog]

### <a name="deploy-from-powershell"></a>Distribuera från PowerShell
Den `Deploy-FabricApplication.ps1` PowerShell-skript som ingår i programmet projektmall accepterar en publiceringsprofil som en parameter och PublishProfile innehåller en referens till filen med programpaketet som parametrar.

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>Nästa steg
Mer information om några grundläggande begrepp som diskuteras i det här avsnittet finns det [teknisk översikt för Service Fabric](service-fabric-technical-overview.md). Information om andra app-hanteringsfunktioner som är tillgängliga i Visual Studio finns [hantera din Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
