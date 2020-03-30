---
title: Exempel på tillförlitliga tjänster app manifest
description: Lär dig hur du konfigurerar inställningar för program- och tjänstmanifest för ett tillförlitligt serviceprogram för Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: f40e54f5260f827f0b18c833d23d1f57b5ebc3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282359"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exempel på Reliable Services-program och tjänstmanifest
Följande är exempel på program- och tjänstmanifest för ett Service Fabric-program med en ASP.NET Core-webbklämning och en tillståndskänslig backend. Syftet med dessa exempel är att visa vilka inställningar som är tillgängliga och hur de ska användas. Dessa program- och tjänstmanifest baseras på [snabbstartsmanifesten för Service Fabric.NET.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)

Följande funktioner visas:

|Manifest|Funktioner|
|---|---|
|[Programmanifest](#application-manifest)| [resursstyrning](service-fabric-resource-governance.md), [kör en tjänst som ett lokalt administratörskonto](service-fabric-application-runas-security.md), tillämpa en [standardprincip på alla tjänstkodpaket,](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages) [skapa användar- och grupphuvudnamn,](service-fabric-application-runas-security.md)dela ett datapaket mellan tjänstinstanser, [åsidosätta tjänstslutpunkter](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|Servicemanifest för FrontEndService| [Köra ett skript vid start av tjänsten](service-fabric-run-script-at-service-startup.md), definiera en [HTTPS-slutpunkt](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|Tjänstmanifest för BackEndService| [Deklarera ett konfigurationspaket](service-fabric-application-and-service-manifests.md), [deklarera ett datapaket](service-fabric-application-and-service-manifests.md), [konfigurera en slutpunkt](service-fabric-service-manifest-resources.md)| 

Se [Manifestelement för program,](#application-manifest-elements) [VotingWeb-tjänstmanifestelement](#votingweb-service-manifest-elements)och Manifestelement för [VotingData-tjänst](#votingdata-service-manifest-elements) för mer information om specifika XML-element.

## <a name="application-manifest"></a>Programmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="Memory" DefaultValue="4084" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="Port" DefaultValue="8081" />
    <Parameter Name="Protocol" DefaultValue="tcp" />
    <Parameter Name="Type" DefaultValue="internal" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <!-- Override endpoints declared in the service manifest. -->
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="DataEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
      </Endpoints>
    </ResourceOverrides>

    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[Memory]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[Memory]" BlockIOWeight="[BlockIOWeight]" 
                                MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" 
                                MemorySwapInMB="[MemorySwapInMB]"/>

      <!-- Share the data package across multiple instances of the VotingData service-->
      <PackageSharingPolicy PackageRef="Data"/>

      <!-- Give read rights on the "DataEndpoint" endpoint to the Customer2 account.-->
      <SecurityAccessPolicy GrantRights="Read" PrincipalRef="Customer2" ResourceRef="DataEndpoint" ResourceType="Endpoint"/>         
    </Policies>
  </ServiceManifestImport>
  
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Run the setup entry point (defined in the imported service manifest) as the SetupAdminUser account 
      (declared in the following Principals section). -->
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      
    </Policies>

  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <!-- Define users and groups required to run the services and access resources.  Principals are used in the Policies section(s). -->
  <Principals>
    <!-- Declare a set of groups as security principals, which can be referenced in policies. Groups are useful if there are multiple users 
    for different service entry points and they need to have certain common privileges that are available at the group level. -->
    <Groups>
      <!-- Create a group that has administrator privileges. -->
      <Group Name="LocalAdminGroup">
        <Membership>
          <SystemGroup Name="Administrators" />
        </Membership>
      </Group>
    </Groups>
    <Users>
      <!-- Declare a user and add the user to the Administrators system group. The SetupAdminUser account is used to run the 
      setup entry point of the VotingWebPkg code package (described in the preceding Policies section).-->
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
      <!-- Create a user. Local user accounts are created on the machines where the application is deployed. By default, these accounts 
      do not have the same names as those specified here. Instead, they are dynamically generated and have random passwords. -->
      <User Name="Customer1" >
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as a local user with the specified account name and password. Local user accounts are created on the machines 
      where the application is deployed. -->
      <User Name="Customer2" AccountType="LocalUser" AccountName="Customer1" Password="MyPassword">
        <MemberOf>
          <!-- Add the user to the local administrators group.-->
          <Group NameRef="LocalAdminGroup" />
        </MemberOf>
      </User>
      <!-- Create a user as NetworkService. -->
      <User Name="MyDefaultAccount" AccountType="NetworkService" />      
    </Users>
  </Principals>
  <!-- Policies applied at the application level. -->
  <Policies>
    <!-- Specify a default user account for all code packages that don’t have a specific RunAsPolicy defined in 
    the ServiceManifestImport section(s). -->
    <DefaultRunAsPolicy UserRef="MyDefaultAccount" />
    
  </Policies>
</ApplicationManifest>

```

## <a name="votingweb-service-manifest"></a>Röstweb-tjänstmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <!-- A privileged entry point that by default runs with the same credentials as Service Fabric (typically the NetworkService account) before 
    any other entry point. Use the setup entry point to set system environment variables, give the account running the service (NETWORK SERVICE, by default) 
    access to a certificate's private key, or perform other setup tasks. In the application manifest, you can change the security permissions to run the startup script 
    under a local system account or an administrator account.  -->
    <SetupEntryPoint>
      <ExeHost>
        <!-- The setup script to run. -->
        <Program>Setup.bat</Program>
        <!-- Pass arguments to the script when it runs.-->
        <Arguments>MyValue</Arguments>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
        <!-- Warning! Do not use console redirection in a production application, only use it for local development and debugging. Redirects console output from the startup
        script to an output file in the application folder called "log" on the cluster node where the application is deployed and run. Also set the number of output files
        to retain and the maximum file size (in KB). -->
        <ConsoleRedirection FileRetentionCount="10" FileMaxSizeInKb="20480"/>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- Configure a HTTPS endpoint on port 443. This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

## <a name="votingdata-service-manifest"></a>Tjänstmanifest för VotingData

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingDataPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="VotingDataType"  HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingData.exe</Program>
        <!-- The working directory for the process in the code package on the node where the application is deployed. CodePackage sets the working directory to be 
        the root of the code package regardless of where the EXE is defined in the code package directory. This is where the processes can write the data. Writing data 
        in the code package or code base is not recommended as those folders could be shared between different application instances and may get deleted.-->
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Declares a folder, named by the Name attribute, under PackageRoot that contains a Settings.xml file. This file contains sections of user-defined, 
  key-value pair settings that the process can read back at run time. During an upgrade, if only the ConfigPackage version has changed, 
  then the running process is not restarted. Instead, a callback notifies the process that configuration settings have changed so they can be reloaded dynamically. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Declares a folder, named by the Name attribute, under PackageRoot which contains static data files to be consumed by the process at run time. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- Define an internal (used for intra-application communication) TCP endpoint. Since no port is specified, one is created and assigned dynamically
           to the service.-->
      <Endpoint Name="DataEndpoint" Protocol="tcp" Type="Internal" />
    </Endpoints>
  </Resources>

</ServiceManifest>

```

## <a name="application-manifest-elements"></a>Manifestelement för program
### <a name="applicationmanifest-element"></a>AnsökanManifest Element
Deklarativt beskriver programtypen och versionen. En eller flera tjänstmanifest för de ingående tjänsterna refereras till för att skriva en programtyp. Konfigurationsinställningar för de ingående tjänsterna kan åsidosättas med hjälp av parameteriserade programinställningar. Standardtjänster, tjänstmallar, huvudnamn, principer, diagnostikkonfiguration och certifikat kan också deklareras på programnivå. Mer information finns i [ApplicationManifest Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parameterelement
Deklarerar de parametrar som används i det här programmanifestet. Värdet på dessa parametrar kan anges när programmet instansieras och kan användas för att åsidosätta inställningar för program- eller tjänstkonfiguration. Mer information finns i [Parameterelement](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameterelement
En programparameter som ska användas i det här manifestet. Parametervärdet kan ändras under programmets instansiering, eller om inget värde anges används standardvärdet. Mer information finns i [Parameterelement](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport-element
Importerar ett tjänstmanifest som skapats av tjänstutvecklaren. Ett tjänstmanifest måste importeras för varje komponenttjänst i programmet. Konfigurationsidosättningar och principer kan deklareras för tjänstmanifestet. Mer information finns i [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Importerar tjänstemanifestet med hänvisning. För närvarande måste tjänstmanifestfilen (ServiceManifest.xml) finnas i byggpaketet. Mer information finns i [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides Element
Anger resursöver åsidosättningar för slutpunkter som deklarerats i tjänstmanifestresurser. Mer information finns i [Element för ResourceOverrides](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Element för slutpunkter
Slutpunkterna som ska åsidosättas. Mer information finns i [Element för slutpunkter](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Slutpunkten, som deklareras i tjänstmanifestet, att åsidosätta. Mer information finns i [Slutpunktselement](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Element för principer
Beskriver principer (slutpunktsbindning, paketdelning, körning och säkerhetsåtkomst) som ska tillämpas på det importerade tjänstmanifestet. Mer information finns i [Policy Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-element
Definierar den resursstyrningsprincip som tillämpas på nivån för hela servicepaketet. Mer information finns i [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Anger resursgränser för ett codepackage. Mer information finns i [Element resourcegovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy-element
Anger om en kod, konfiguration eller ett datapaket ska delas mellan tjänstinstanser av samma tjänsttyp. Mer information finns i [PackageSharingPolicy-elementet](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy-element
Ger åtkomstbehörigheter till ett huvudnamn för en resurs (till exempel en slutpunkt) som definierats i ett tjänstmanifest. Vanligtvis är det mycket användbart att kontrollera och begränsa åtkomsten av tjänster till olika resurser för att minimera säkerhetsrisker. Detta är särskilt viktigt när programmet är byggt från en samling tjänster från en marknadsplats som utvecklas av olika utvecklare. Mer information finns i [SecurityAccessPolicy-elementet](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy-element
Anger det lokala användarkonto eller det lokala systemkonto som ett servicekodpaket ska köras som. Domänkonton stöds på Windows Server-distributioner där Azure Active Directory är tillgängligt. Som standard körs program under kontot som Fabric.exe-processen körs under. Program kan också köras som andra konton, som måste deklareras i avsnittet Huvudmän. Om du tillämpar en RunAs-princip på en tjänst och tjänstmanifestet deklarerar slutpunktsresurser med HTTP-protokollet, måste du också ange en SecurityAccessPolicy för att säkerställa att portar som allokerats till dessa slutpunkter är korrekt åtkomstkontroll listade för RunAs användarkonto som tjänsten körs under. För en HTTPS-slutpunkt måste du också definiera en EndpointBindingPolicy för att ange namnet på certifikatet som ska returneras till klienten. Mer information finns i [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>Standardtjänstelement
Deklarerar tjänstinstanser som skapas automatiskt när ett program instansieras mot den här programtypen. Mer information finns i [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Serviceelement
Deklarerar en tjänst som ska skapas automatiskt när programmet instansieras. Mer information finns i [Serviceelement](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>Tillståndskänsligttjänstelement
Definierar en tillståndskänslig tjänst. Mer information finns i [StatefulService Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>Tillståndslöst tjänstelement
Definierar en tillståndslös tjänst. Mer information finns i [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Huvudnamn Element
Beskriver de säkerhetsobjekt (användare, grupper) som krävs för att programmet ska köra tjänster och skydda resurser. Huvudmän refereras i policyavsnitten. Mer information finns i [Principals Element](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Element för grupper
Deklarerar en uppsättning grupper som säkerhetsobjekt, som kan refereras i principer. Grupper är användbara om det finns flera användare för olika tjänststartpunkter och de måste ha vissa vanliga privilegier som är tillgängliga på gruppnivå. Mer information finns i [Grupperelement](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Gruppelement
Deklarerar en grupp som ett säkerhetsobjekt som kan refereras i principer. Mer information finns i [Gruppelement](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Element för medlemskap
 Mer information finns i [Medlemselementet](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>Element för Systemgrupp
 Mer information finns i [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Element för användare
Deklarerar en uppsättning användare som säkerhetsobjekt, som kan refereras i principer. Mer information finns i [Användarelement](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>Användarelement
Deklarerar en användare som ett säkerhetsobjekt, som kan refereras i principer. Mer information finns i [Användarelement](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf Element
Användare kan läggas till i en befintlig medlemsgrupp, så att den kan ärva alla egenskaper och säkerhetsinställningar för den medlemsgruppen. Medlemsgruppen kan användas för att skydda externa resurser som måste nås av olika tjänster eller samma tjänst (på en annan dator). Mer information finns i [MemberOf Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>Element för Systemgrupp
Den systemgrupp som användaren ska läggas till i.  Systemgruppen måste definieras i avsnittet Grupper. Mer information finns i [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Gruppelement
Gruppen som användaren ska läggas till i.  Gruppen måste definieras i avsnittet Grupper. Mer information finns i [Gruppelement](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Element för principer
Beskriver de principer (loggsamling, standardkörning, hälso- och säkerhetsåtkomst) som ska tillämpas på programnivå. Mer information finns i [Policy Element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy-element
Ange ett standardanvändarkonto för alla tjänstkodpaket som inte har en specifik RunAsPolicy definierad i avsnittet ServiceManifestImport. Mer information finns i [DefaultRunAsPolicy-element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>Manifestelement för röstningsweb-tjänst
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativt beskriver tjänsttypen och versionen. Den listar oberoende uppgraderingsbara kod- och konfigurations- och datapaket som tillsammans utgör ett servicepaket för att stödja en eller flera tjänsttyper. Resurser, diagnostikinställningar och tjänstmetadata, till exempel tjänsttyp, hälsoegenskaper och belastningsutjämningsmått, anges också. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elementet ServiceTypes
Definierar vilka tjänsttyper som stöds av ett CodePackage i det här manifestet. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. Tjänsttyper deklareras på manifestnivå och inte kodpaketnivån. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Tillståndslöst ServiceType-element
Beskriver en tillståndslös tjänsttyp. Mer information finns i [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som stöder en definierad tjänsttyp. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera de tjänsttyper som stöds vid körning. När det finns flera kodpaket aktiveras alla när systemet letar efter någon av de deklarerade tjänsttyperna. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint Element
En privilegierad startpunkt som som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis NETWORKSERVICE-kontot) före någon annan startpunkt. Den körbara filen som anges av EntryPoint är vanligtvis den tjänstvärd som körs. Förekomsten av en separat inställningsanspunkt undviker att behöva köra tjänstvärden med hög behörighet under längre tidsperioder. Mer information finns i [SetupEntryPoint-element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost-element
 Mer information finns i [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Programelement
Det körbara namnet.  Till exempel "MySetup.bat" eller "MyServiceHost.exe". Mer information finns i [Programelement](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Argument Element
 Mer information finns i [Argumentelement](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element för arbetsmapp
Arbetskatalogen för processen i kodpaketet på klusternoden där programmet distribueras. Du kan ange tre värden: Arbete (standard), CodePackage eller CodeBase. CodeBase anger att arbetskatalogen är inställd på den katalog där EXE definieras i kodpaketet. CodePackage anger att arbetskatalogen ska vara roten till kodpaketet oavsett var EXE definieras i kodpaketkatalogen. Arbetskatalogen till en unik mapp som skapats på noden.  Den här mappen är densamma för hela programinstansen. Som standard är arbetskatalogen för alla processer i programmet inställd på programarbetsmappen. Det är här processerna kan skriva data. Det rekommenderas inte att skriva data i kodpaketet eller kodbasen eftersom dessa mappar kan delas mellan olika programinstanser och kan tas bort. Mer information finns i [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection Element

> [!WARNING]
> Använd inte omdirigering av konsolen i ett produktionsprogram, använd den bara för lokal utveckling och felsökning. Omdirigerar konsolutdata från startskriptet till en utdatafil i programmappen "log" på klusternoden där programmet distribueras och körs. Mer information finns i [ConsoleRedirection Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis den tjänstvärd som körs. Förekomsten av en separat inställningsanspunkt undviker att behöva köra tjänstvärden med hög behörighet under längre tidsperioder. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutats. Den resulterande processen övervakas och startas om (börjar igen med SetupEntryPoint) om den någonsin avslutas eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost-element
 Mer information finns i [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp, namngiven av attributet Name, under PackageRoot som innehåller filen Settings.xml. Den här filen innehåller avsnitt av användardefinierade inställningar för nyckelvärdespar som processen kan läsa upp vid körning. Under en uppgradering, om bara ConfigPackage-versionen har ändrats, startas inte den pågående processen om. I stället meddelar en motringning processen att konfigurationsinställningarna har ändrats så att de kan laddas om dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resurselement
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomsten till dessa resurser kontrolleras via avsnitten Huvudmän och principer i programmanifestet. Mer information finns i [Resurselement](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element för slutpunkter
Definierar slutpunkter för tjänsten. Mer information finns i [Element för slutpunkter](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Slutpunkten, som deklareras i tjänstmanifestet, att åsidosätta. Mer information finns i [Slutpunktselement](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>Manifestelement för VotingData-tjänsten
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativt beskriver tjänsttypen och versionen. Den listar oberoende uppgraderingsbara kod- och konfigurations- och datapaket som tillsammans utgör ett servicepaket för att stödja en eller flera tjänsttyper. Resurser, diagnostikinställningar och tjänstmetadata, till exempel tjänsttyp, hälsoegenskaper och belastningsutjämningsmått, anges också. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elementet ServiceTypes
Definierar vilka tjänsttyper som stöds av ett CodePackage i det här manifestet. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. Tjänsttyper deklareras på manifestnivå och inte kodpaketnivån. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>Tillståndskänsligt typelement
Beskriver en tillståndskänslig tjänsttyp. Mer information finns i [StatefulServiceType Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som stöder en definierad tjänsttyp. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera de tjänsttyper som stöds vid körning. När det finns flera kodpaket aktiveras alla när systemet letar efter någon av de deklarerade tjänsttyperna. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis den tjänstvärd som körs. Förekomsten av en separat inställningsanspunkt undviker att behöva köra tjänstvärden med hög behörighet under längre tidsperioder. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutats. Den resulterande processen övervakas och startas om (börjar igen med SetupEntryPoint) om den någonsin avslutas eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost-element
 Mer information finns i [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Programelement
Det körbara namnet.  Till exempel "MySetup.bat" eller "MyServiceHost.exe". Mer information finns i [Programelement](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>Element för arbetsmapp
Arbetskatalogen för processen i kodpaketet på klusternoden där programmet distribueras. Du kan ange tre värden: Arbete (standard), CodePackage eller CodeBase. CodeBase anger att arbetskatalogen är inställd på den katalog där EXE definieras i kodpaketet. CodePackage anger att arbetskatalogen ska vara roten till kodpaketet oavsett var EXE definieras i kodpaketkatalogen. Arbetskatalogen till en unik mapp som skapats på noden.  Den här mappen är densamma för hela programinstansen. Som standard är arbetskatalogen för alla processer i programmet inställd på programarbetsmappen. Det är här processerna kan skriva data. Det rekommenderas inte att skriva data i kodpaketet eller kodbasen eftersom dessa mappar kan delas mellan olika programinstanser och kan tas bort. Mer information finns i [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp, namngiven av attributet Name, under PackageRoot som innehåller filen Settings.xml. Den här filen innehåller avsnitt av användardefinierade inställningar för nyckelvärdespar som processen kan läsa upp vid körning. Under en uppgradering, om bara ConfigPackage-versionen har ändrats, startas inte den pågående processen om. I stället meddelar en motringning processen att konfigurationsinställningarna har ändrats så att de kan laddas om dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage-element
Deklarerar en mapp, namngiven av attributet Name, under PackageRoot som innehåller statiska datafiler som ska förbrukas av processen vid körning. Service Fabric kommer att återvinna alla EXEs och DLLHOSTs som anges i värden och supportpaket när något av de datapaket som anges i tjänstmanifestet uppgraderas. Mer information finns i [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resurselement
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomsten till dessa resurser kontrolleras via avsnitten Huvudmän och principer i programmanifestet. Mer information finns i [Resurselement](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element för slutpunkter
Definierar slutpunkter för tjänsten. Mer information finns i [Element för slutpunkter](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Slutpunkten, som deklareras i tjänstmanifestet, att åsidosätta. Mer information finns i [Slutpunktselement](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

