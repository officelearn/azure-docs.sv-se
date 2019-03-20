---
title: Azure Service Fabric reliable services application manifest exempel | Microsoft Docs
description: Lär dig hur du konfigurerar programmet och tjänstinställningar manifestet för en Service Fabric reliable services-programmet.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: ryanwi
ms.openlocfilehash: 1d0a0c2c6a965ac14002257484c5f840666e6fc1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885274"
---
# <a name="reliable-services-application-and-service-manifest-examples"></a>Exempel på Reliable Services-program och tjänstmanifest
Här följer några exempel på program och tjänstens manifest för ett Service Fabric-program med en ASP.NET-kärnans webbklient och en tillståndskänslig backend-server. Syftet med de här exemplen är att visa vilka inställningar är tillgängliga och hur de används. Dessa program och tjänstens manifest baseras på den [Snabbstart för Service Fabric .NET](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) manifest.

Följande funktioner visas:

|Manifest|Funktioner|
|---|---|
|[Programmanifest](#application-manifest)| [resursstyrning](service-fabric-resource-governance.md), [köra tjänster som ett lokalt administratörskonto](service-fabric-application-runas-security.md), [gäller en standardprincip för alla kodpaket för tjänsten](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages), [skapa användar- och säkerhetsobjekt](service-fabric-application-runas-security.md), dela ett paket av data mellan instanser av tjänsten, [åsidosätta tjänstslutpunkter](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)| 
|FrontEndService tjänstmanifestet| [Köra ett skript när tjänsten startar](service-fabric-run-script-at-service-startup.md), [definiera en HTTPS-slutpunkt](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest) | 
|BackEndService tjänstmanifestet| [Deklarera ett konfigurationspaket](service-fabric-application-and-service-manifests.md), [deklarera ett paket för data](service-fabric-application-and-service-manifests.md), [konfigurerar en slutpunkt](service-fabric-service-manifest-resources.md)| 

Se [manifest programelement](#application-manifest-elements), [VotingWeb manifest delar](#votingweb-service-manifest-elements), och [VotingData manifest delar](#votingdata-service-manifest-elements) för mer information om specifika XML element.

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
      <PackageSharingPolicy PackageRef="VotingDataPkg.Data"/>

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

## <a name="votingweb-service-manifest"></a>VotingWeb tjänstmanifestet

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

## <a name="votingdata-service-manifest"></a>VotingData tjänstmanifestet

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

## <a name="application-manifest-elements"></a>Application manifest element
### <a name="applicationmanifest-element"></a>ApplicationManifest Element
Beskriver deklarativt programtypen och versionen. Refererar till en eller flera tjänstmanifest dessa tjänster om du ska skapa en typ av program. Konfigurationsinställningarna för dessa tjänster kan åsidosättas med hjälp av parametriserade programinställningar. Som standard tjänster, tjänstmallar, säkerhetsobjekt, principer, ställa in diagnostik och certifikat kan också deklarerats på programnivå. Mer information finns i [ApplicationManifest Element](service-fabric-service-model-schema-elements.md#ApplicationManifestElementApplicationManifestTypeComplexType)

### <a name="parameters-element"></a>Parametrarna-Element
Anger de parametrar som används i den här programmanifestet. Värdet för dessa parametrar kan anges när programmet instantieras och kan användas för att åsidosätta programmet eller tjänsten konfigurationsinställningar. Mer information finns i [parametrar Element](service-fabric-service-model-schema-elements.md#ParametersElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="parameter-element"></a>Parameterelement
En program-parameter som ska användas i manifestet. Parametervärdet kan ändras under programmet instansiering eller, om inget värde anges används standardvärdet. Mer information finns i [parameterelement](service-fabric-service-model-schema-elements.md#ParameterElementanonymouscomplexTypeComplexTypeDefinedInParameterselement)

### <a name="servicemanifestimport-element"></a>ServiceManifestImport Element
Importerar ett tjänstmanifest som skapats av tjänsten-utvecklare. Ett tjänstmanifest måste importeras för varje konstituerande tjänst i programmet. Åsidosätter konfigurationen och principer kan deklareras för tjänstmanifestet. Mer information finns i [ServiceManifestImport Element](service-fabric-service-model-schema-elements.md#ServiceManifestImportElementanonymouscomplexTypeComplexTypeDefinedInApplicationManifestTypecomplexType)

### <a name="servicemanifestref-element"></a>ServiceManifestRef Element
Importerar tjänstmanifestet med referens. För närvarande måste tjänstmanifestfilen (ServiceManifest.xml) finnas i build-paketet. Mer information finns i [ServiceManifestRef Element](service-fabric-service-model-schema-elements.md#ServiceManifestRefElementServiceManifestRefTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="resourceoverrides-element"></a>ResourceOverrides Element
Anger resurs åsidosättningar för slutpunkter som deklarerats i tjänstmanifestresurser. Mer information finns i [ResourceOverrides Element](service-fabric-service-model-schema-elements.md#ResourceOverridesElementResourceOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="endpoints-element"></a>Slutpunkter Element
Slutpunkt(er) att åsidosätta. Mer information finns i [slutpunkter Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourceOverridesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Den slutpunkt som har deklarerats i tjänstmanifestet att åsidosätta. Mer information finns i [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

### <a name="policies-element"></a>Policies Element
Beskriver principer (slutpunkt för bindning, delning, kör som-paketet och säkerhet åtkomst) som ska tillämpas på importerade tjänstmanifestet. Mer information finns i [principer Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definierar principen för resurs-styrning som tillämpas på nivån för hela tjänstpaketet. Mer information finns i [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Anger resursgränser för en codepackage. Mer information finns i [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="packagesharingpolicy-element"></a>PackageSharingPolicy Element
Anger om ett paket med kod, konfig eller data bör delas mellan instanser av tjänsten av tjänsttypen samma. Mer information finns i [PackageSharingPolicy Element](service-fabric-service-model-schema-elements.md#PackageSharingPolicyElementPackageSharingPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexType)

### <a name="securityaccesspolicy-element"></a>SecurityAccessPolicy Element
Beviljar åtkomst till behörigheter för ett huvudnamn för en resurs (till exempel en slutpunkt) har definierats i ett tjänstmanifest. Det är vanligtvis mycket användbart för att styra och begränsa åtkomst till tjänster till olika resurser för att minimera säkerhetsrisker. Detta är särskilt viktigt när du har skapat programmet från en samling från en marketplace-tjänster som utvecklats av olika utvecklare. Mer information finns i [SecurityAccessPolicy Element](service-fabric-service-model-schema-elements.md#SecurityAccessPolicyElementSecurityAccessPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInSecurityAccessPolicieselementDefinedInDigestedEndpointelement)

### <a name="runaspolicy-element"></a>RunAsPolicy Element
Anger lokal användare eller kontot Lokalt system som använder ett tjänstkodpaket. Domänkonton stöds på Windows Server-distributioner där Azure Active Directory är tillgängligt. Program körs under kontot som Fabric.exe processen körs under som standard. Program kan också köra som andra konton, som måste deklareras i avsnittet säkerhetsobjekt. Om du använder en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint-resurser med HTTP-protokollet, måste du även ange en SecurityAccessPolicy för att säkerställa att portarna som allokerats till är de här slutpunkterna korrekt för RunAs för åtkomstkontroll användarkonto som tjänsten körs under. För en HTTPS-slutpunkt måste du också definiera en EndpointBindingPolicy för att ange namnet på certifikatet som ska gå tillbaka till klienten. Mer information finns i [RunAsPolicy Element](service-fabric-service-model-schema-elements.md#RunAsPolicyElementRunAsPolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="defaultservices-element"></a>DefaultServices Element
Anger instanser av tjänsten som skapas automatiskt när ett program instantieras mot den här typen. Mer information finns i [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Service Element
Deklarerar en tjänst som ska skapas automatiskt när programmet instantieras. Mer information finns i [Service Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statefulservice-element"></a>StatefulService Element
Definierar en tillståndskänslig tjänst. Mer information finns i [StatefulService Element](service-fabric-service-model-schema-elements.md#StatefulServiceElementStatefulServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="statelessservice-element"></a>StatelessService Element
Definierar en tillståndslös tjänst. Mer information finns i [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)

### <a name="principals-element"></a>Principals Element
Beskriver de säkerhetsobjekt (användare, grupper) som krävs för det här programmet körs tjänster och säkra resurser. Huvudnamn refereras i avsnitten principer. Mer information finns i [huvudkonton Element](service-fabric-service-model-schema-elements.md#PrincipalsElementSecurityPrincipalsTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="groups-element"></a>Grupper Element
Anger en uppsättning grupper som säkerhet huvudnamn, vilket kan refereras i principer. Grupper är användbara om det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå. Mer information finns i [grupper Element](service-fabric-service-model-schema-elements.md#GroupsElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="group-element"></a>Gruppelementet
Anger en grupp som ett säkerhetsobjekt som kan refereras i principer. Mer information finns i [gruppelementet](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInGroupselement)

### <a name="membership-element"></a>Medlemskap Element
 Mer information finns i [medlemskap Element](service-fabric-service-model-schema-elements.md#MembershipElementanonymouscomplexTypeComplexTypeDefinedInGroupelement)

### <a name="systemgroup-element"></a>SystemGroup Element
 Mer information finns i [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMembershipelement)

### <a name="users-element"></a>Användare-Element
Anger en uppsättning användare som säkerhet huvudnamn, vilket kan refereras i principer. Mer information finns i [användare Element](service-fabric-service-model-schema-elements.md#UsersElementanonymouscomplexTypeComplexTypeDefinedInSecurityPrincipalsTypecomplexType)

### <a name="user-element"></a>User-Element
Anger en användare som ett säkerhetsobjekt som kan refereras i principer. Mer information finns i [User-Element](service-fabric-service-model-schema-elements.md#UserElementanonymouscomplexTypeComplexTypeDefinedInUserselement)

### <a name="memberof-element"></a>MemberOf Element
Användare kan läggas till alla befintliga medlemskap-gruppen, så att den kan ärvas alla egenskaper och säkerhetsinställningar för gruppen medlemskap. Gruppen Medlemskapsgruppen kan användas för att skydda externa resurser som ska användas av olika tjänster eller samma tjänst (på en annan dator). Mer information finns i [MemberOf Element](service-fabric-service-model-schema-elements.md#MemberOfElementanonymouscomplexTypeComplexTypeDefinedInUserelement)

### <a name="systemgroup-element"></a>SystemGroup Element
System-grupp att lägga till användaren.  Systemgruppen måste definieras i avsnittet grupper. Mer information finns i [SystemGroup Element](service-fabric-service-model-schema-elements.md#SystemGroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="group-element"></a>Gruppelementet
Grupp att lägga till användaren.  Gruppen måste ha definierats i avsnittet grupper. Mer information finns i [gruppelementet](service-fabric-service-model-schema-elements.md#GroupElementanonymouscomplexTypeComplexTypeDefinedInMemberOfelement)

### <a name="policies-element"></a>Policies Element
Beskriver principerna (Logginsamling, standard kör som, hälsotillstånd och säkerhetsbehörighet) som ska tillämpas på programnivå. Mer information finns i [principer Element](service-fabric-service-model-schema-elements.md#PoliciesElementApplicationPoliciesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInEnvironmentTypecomplexType)

### <a name="defaultrunaspolicy-element"></a>DefaultRunAsPolicy Element
Ange ett användarkonto för standard för alla kodpaket för tjänsten som inte har en specifik RunAsPolicy som definierats i avsnittet ServiceManifestImport. Mer information finns i [DefaultRunAsPolicy Element](service-fabric-service-model-schema-elements.md#DefaultRunAsPolicyElementanonymouscomplexTypeComplexTypeDefinedInApplicationPoliciesTypecomplexType)




## <a name="votingweb-service-manifest-elements"></a>VotingWeb-tjänsten manifest element
### <a name="servicemanifest-element"></a>ServiceManifest Element
Beskriver deklarativt tjänsttyp och version. Den visar oberoende av varandra kan uppgraderas kod, konfiguration och data-paket som tillsammans utgör inget tjänstepaket för att stödja en eller flera typer av tjänster. Resurser, inställningarna för startdiagnostik och tjänstmetadata, till exempel typ av tjänst, health egenskaper och mätvärden för Utjämning av nätverksbelastning, också anges. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definierar vilka typer av tjänster som stöds av en CodePackage i manifestet. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. Tjänsttyper deklareras i manifestet nivå och inte kodnivå för paketet. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Beskriver en typ av tillståndslösa. Mer information finns i [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som har stöd för en definierad tjänsttyp. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera stöds tjänsttyper vid körning. När det finns flera kodpaket, aktiveras de alla varje gång systemet söker efter någon av de deklarerade tjänsttyper. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="setupentrypoint-element"></a>SetupEntryPoint Element
En privilegierad startpunkt som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis kontot NETWORKSERVICE) innan andra startpunkt. Den körbara filen som anges av EntryPoint är vanligtvis tjänstevärden tidskrävande. Förekomsten av en separat konfigurationsstartpunkten innebär att du slipper att köra tjänstevärden med höga privilegier för längre tid. Mer information finns i [SetupEntryPoint Element](service-fabric-service-model-schema-elements.md#SetupEntryPointElementanonymouscomplexTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Mer information finns i [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementExeHostEntryPointTypeComplexTypeDefinedInSetupEntryPointelement)

### <a name="program-element"></a>Programelement
Filnamn.  Till exempel ”MySetup.bat” eller ”MyServiceHost.exe”. Mer information finns i [programelement](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="arguments-element"></a>Arguments Element
 Mer information finns i [argument Element](service-fabric-service-model-schema-elements.md#ArgumentsElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder Element
Arbetskatalogen för processen i kodpaketet på den noden i klustret där programmet har distribuerats. Du kan ange tre värden: Arbete (standard), CodePackage eller kodbas. Kodbas anger att arbetskatalogen har angetts till katalogen där den exe-filen definieras i kodpaketet. CodePackage anger arbetskatalogen är roten för kodpaketet oavsett var den exe-filen definieras i paketkatalogen kod. Arbete anger arbetskatalogen till en unik mapp som skapas på noden.  Den här mappen är samma för hela programmet-instansen. Arbetskatalog för alla processer i programmet är som standard till programmappen för arbetet. Det här är där processerna kan skriva data. Skrivning av data i kodpaketet eller kodbas rekommenderas inte eftersom dessa mappar kan delas mellan olika programinstanser och kan tas bort. Mer information finns i [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="consoleredirection-element"></a>ConsoleRedirection Element

> [!WARNING]
> Använda omdirigering av konsol i ett produktionsprogram inte, endast använda den för lokal utveckling och felsökning. Omdirigerar konsolens utdata från startskriptet att en utdatafil i programmappen ”logg” med namnet på den noden i klustret där programmet har distribuerats och körs. Mer information finns i [ConsoleRedirection Element](service-fabric-service-model-schema-elements.md#ConsoleRedirectionElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis tjänstevärden tidskrävande. Förekomsten av en separat konfigurationsstartpunkten innebär att du slipper att köra tjänstevärden med höga privilegier för längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutas. Resulterande processen övervakas och startas om (början igen med SetupEntryPoint) om det skulle avslutas eller kraschar. Mer information finns i [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Mer information finns i [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Anger en mapp med namnet av attributet namn under PackageRoot som innehåller en Settings.xml-fil. Den här filen innehåller avsnitt av inställningar för användardefinierade, nyckel / värde-par som processen kan läsa tillbaka vid körning. Vid en uppgradering om det bara ConfigPackage version har ändrats startas sedan processen inte. I stället meddelar en motringning den process som konfigurationsinställningarna har ändrats så att de kan läsas dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resurser-Element
Beskriver de resurser som används av den här tjänsten, som kan vara deklarerats utan att ändra kompilerad kod och ändras när tjänsten har distribuerats. Åtkomst till dessa resurser styrs via huvudnamn och principer för avsnitt i programmanifestet. Mer information finns i [resurser Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slutpunkter Element
Definierar slutpunkter för tjänsten. Mer information finns i [slutpunkter Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Den slutpunkt som har deklarerats i tjänstmanifestet att åsidosätta. Mer information finns i [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)



## <a name="votingdata-service-manifest-elements"></a>VotingData service manifest element
### <a name="servicemanifest-element"></a>ServiceManifest Element
Beskriver deklarativt tjänsttyp och version. Den visar oberoende av varandra kan uppgraderas kod, konfiguration och data-paket som tillsammans utgör inget tjänstepaket för att stödja en eller flera typer av tjänster. Resurser, inställningarna för startdiagnostik och tjänstmetadata, till exempel typ av tjänst, health egenskaper och mätvärden för Utjämning av nätverksbelastning, också anges. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definierar vilka typer av tjänster som stöds av en CodePackage i manifestet. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. Tjänsttyper deklareras i manifestet nivå och inte kodnivå för paketet. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statefulservicetype-element"></a>StatefulServiceType Element
Beskriver en tillståndskänslig tjänsttyp. Mer information finns i [StatefulServiceType Element](service-fabric-service-model-schema-elements.md#StatefulServiceTypeElementStatefulServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som har stöd för en definierad tjänsttyp. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera stöds tjänsttyper vid körning. När det finns flera kodpaket, aktiveras de alla varje gång systemet söker efter någon av de deklarerade tjänsttyper. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis tjänstevärden tidskrävande. Förekomsten av en separat konfigurationsstartpunkten innebär att du slipper att köra tjänstevärden med höga privilegier för längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutas. Resulterande processen övervakas och startas om (början igen med SetupEntryPoint) om det skulle avslutas eller kraschar. Mer information finns i [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="exehost-element"></a>ExeHost Element
 Mer information finns i [ExeHost Element](service-fabric-service-model-schema-elements.md#ExeHostElementanonymouscomplexTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="program-element"></a>Programelement
Filnamn.  Till exempel ”MySetup.bat” eller ”MyServiceHost.exe”. Mer information finns i [programelement](service-fabric-service-model-schema-elements.md#ProgramElementxs:stringComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="workingfolder-element"></a>WorkingFolder Element
Arbetskatalogen för processen i kodpaketet på den noden i klustret där programmet har distribuerats. Du kan ange tre värden: Arbete (standard), CodePackage eller kodbas. Kodbas anger att arbetskatalogen har angetts till katalogen där den exe-filen definieras i kodpaketet. CodePackage anger arbetskatalogen är roten för kodpaketet oavsett var den exe-filen definieras i paketkatalogen kod. Arbete anger arbetskatalogen till en unik mapp som skapas på noden.  Den här mappen är samma för hela programmet-instansen. Arbetskatalog för alla processer i programmet är som standard till programmappen för arbetet. Det här är där processerna kan skriva data. Skrivning av data i kodpaketet eller kodbas rekommenderas inte eftersom dessa mappar kan delas mellan olika programinstanser och kan tas bort. Mer information finns i [WorkingFolder Element](service-fabric-service-model-schema-elements.md#WorkingFolderElementanonymouscomplexTypeComplexTypeDefinedInExeHostEntryPointTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Anger en mapp med namnet av attributet namn under PackageRoot som innehåller en Settings.xml-fil. Den här filen innehåller avsnitt av inställningar för användardefinierade, nyckel / värde-par som processen kan läsa tillbaka vid körning. Vid en uppgradering om det bara ConfigPackage version har ändrats startas sedan processen inte. I stället meddelar en motringning den process som konfigurationsinställningarna har ändrats så att de kan läsas dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage Element
Anger en mapp med namnet av attributet namn under PackageRoot som innehåller filer som statiska data ska användas av processen vid körning. Service Fabric kommer Papperskorgen alla exe-filerna och DLLHOSTs som anges i paket som värd och support när data-paket som anges i tjänstmanifestet uppgraderas. Mer information finns i [DataPackage-Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resurser-Element
Beskriver de resurser som används av den här tjänsten, som kan vara deklarerats utan att ändra kompilerad kod och ändras när tjänsten har distribuerats. Åtkomst till dessa resurser styrs via huvudnamn och principer för avsnitt i programmanifestet. Mer information finns i [resurser Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slutpunkter Element
Definierar slutpunkter för tjänsten. Mer information finns i [slutpunkter Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Den slutpunkt som har deklarerats i tjänstmanifestet att åsidosätta. Mer information finns i [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

