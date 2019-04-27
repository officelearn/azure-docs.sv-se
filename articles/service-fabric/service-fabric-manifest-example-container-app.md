---
title: Azure Service Fabric-behållaren application manifest-exempel | Microsoft Docs
description: Lär dig hur du konfigurerar programmet och tjänstinställningar manifestet för en Service Fabric-program med flera behållare.
services: service-fabric
documentationcenter: na
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 622e6f7552d91cdb9ccf3668c302496c68a5920f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719251"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exempel på program med flera behållare och tjänstmanifest
Här följer några exempel på manifest för program och tjänster för en Service Fabric-program med flera behållare. Syftet med de här exemplen är att visa vilka inställningar är tillgängliga och hur de används. Dessa program och tjänstens manifest baseras på den [Windows Server 2016 behållare exempel](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows) manifest.

Följande funktioner visas:

|Manifest|Funktioner|
|---|---|
|[Programmanifest](#application-manifest)| [åsidosätt miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurerar du mappning av behållare till värd](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery), [konfigurera autentisering av behållarregister](service-fabric-get-started-containers.md#configure-container-registry-authentication), [resursstyrning](service-fabric-resource-governance.md), [Anger isoleringsläge](service-fabric-get-started-containers.md#configure-isolation-mode), [ange OS build-specifika behållaravbildningar](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[FrontEndService tjänstmanifestet](#frontendservice-service-manifest)| [Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurerar en slutpunkt](service-fabric-get-started-containers.md#configure-communication), skicka kommandon till behållaren, [importera ett certifikat till en behållare](service-fabric-securing-containers.md)| 
|[BackEndService tjänstmanifestet](#backendservice-service-manifest)|[Ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurerar en slutpunkt](service-fabric-get-started-containers.md#configure-communication), [Konfigurera drivrutiner för volym](service-fabric-containers-volume-logging-drivers.md)| 

Se [manifest programelement](#application-manifest-elements), [FrontEndService manifest delar](#frontendservice-service-manifest-elements), och [BackEndService manifest delar](#backendservice-service-manifest-elements) mer information om specifika XML-element.

## <a name="application-manifest"></a>Programmanifest

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Container.ApplicationType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="BackEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="FrontEndService_InstanceCount" DefaultValue="-1" />
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="BlockIOWeight" DefaultValue="200" />
    <Parameter Name="MaximumIOBandwidth" DefaultValue="1024" />
    <Parameter Name="MemoryReservationInMB" DefaultValue="1024" />
    <Parameter Name="MemorySwapInMB" DefaultValue="4084"/>
    <Parameter Name="MaximumIOps" DefaultValue="20"/>
    <Parameter Name="MemoryFront" DefaultValue="4084" />
    <Parameter Name="MemoryBack" DefaultValue="2048" />
    <Parameter Name="CertThumbprint" DefaultValue=""/>
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="BackEndServicePkg" ServiceManifestVersion="1.0.0" />    
    
    <!-- Policies to be applied to the imported service manifest. -->
    <Policies>
      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryFront]"/>

      <!-- Set resource governance at the code package level. -->
      <ResourceGovernancePolicy CodePackageRef="Code" CpuPercent="10" MemoryInMB="[MemoryFront]" BlockIOWeight="[BlockIOWeight]" MaximumIOBandwidth="[MaximumIOBandwidth]" MaximumIOps="[MaximumIOps]" MemoryReservationInMB="[MemoryReservationInMB]" MemorySwapInMB="[MemorySwapInMB]"/>
      
      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">
        
        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>
        
        <!-- This binds the port the container is listening on (8905 in this sample) to an endpoint resource named "BackEndServiceTypeEndpoint", which is defined in the service manifest.  -->
        <PortBinding ContainerPort="8905" EndpointRef="BackEndServiceTypeEndpoint"/>
        
        <!-- Configure the Azure Files volume plugin.  Bind the source folder on the host VM or a remote share to the destination folder within the running container. -->
        <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
          <!-- Driver options to be passed to driver. The Azure Files volume plugin supports the following driver options:
            shareName (the Azure Files file share that provides the volume for the container), storageAccountName (the Azure storage account
            that contains the Azure Files file share), storageAccountKey (Access key for the Azure storage account that contains the Azure Files file share).
            These three driver options are required. -->
          <DriverOption Name="shareName" Value="" />
          <DriverOption Name="storageAccountName" Value="MY-STORAGE-ACCOUNT-NAME" />
          <DriverOption Name="storageAccountKey" Value="MY-STORAGE-ACCOUNT-KEY" />
        </Volume>
        
        <!-- Windows Server containers may not be compatible across different versions of the OS.  You can specify multiple OS images per container and tag 
        them with the build versions of the OS. Get the build version of the OS by running "winver" at a Windows command prompt. -->
        <ImageOverrides>
          <!-- If the underlying OS is build version 16299 (Windows Server version 1709), Service Fabric picks the container image tagged with Os="16299". -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_1709" Os="16299" />
          
          <!-- An untagged container image is assumed to work across all versions of the OS and overrides the image specified in the service manifest. -->
          <Image Name="sfsamples.azurecr.io/sfsamples/servicefabricbackendservice_default" />          
        </ImageOverrides>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>

  <!-- Policies to be applied to the imported service manifest. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    
    <!-- This enables you to provide different values for environment variables when creating a FrontEndService
         Theses environment variables are declared in the FrontEndServiceType service manifest-->
    <EnvironmentOverrides CodePackageRef="Code">
      <EnvironmentVariable Name="BackendServiceName" Value="Container.Application/BackEndService"/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentOverrides>
    
    <!-- This policy maps the  port of the container (80) to the endpoint declared in the service, 
         FrontEndServiceTypeEndpoint which is exposed as port 80 on the host-->    
    <Policies>

      <!-- Set resource governance at the service package level. -->
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]" MemoryInMB="[MemoryBack]"/>

      <!-- Policies for activating container hosts. -->
      <ContainerHostPolicies CodePackageRef="Code" Isolation="process">

        <!-- Credentials for the repository hosting the container image.-->
        <RepositoryCredentials AccountName="sfsamples" Password="ENCRYPTED-PASSWORD" PasswordEncrypted="true"/>

        <!-- Binds an endpoint resource (declared in the service manifest) to the exposed container port. -->
        <PortBinding ContainerPort="80" EndpointRef="FrontEndServiceTypeEndpoint"/>

        <!-- Import a certificate into the container.  The certificate must be installed in the LocalMachine store of all the cluster nodes.
          When the application starts, the runtime reads the certificate and generates a PFX file and password (on Windows) or a PEM file (on Linux).
          The PFX file and password are accessible in the container using the Certificates_ServicePackageName_CodePackageName_CertName_PFX and 
          Certificates_ServicePackageName_CodePackageName_CertName_Password environment variables. The PEM file is accessible in the container using the 
          Certificates_ServicePackageName_CodePackageName_CertName_PEM and Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey environment variables.-->
        <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[CertThumbprint]" />

        <!-- If the certificate is already in PFX or PEM form, you can create a data package inside your application and reference that certificate here. -->
        <CertificateRef Name="MyCert2" DataPackageRef="Data" DataPackageVersion="1.0.0" RelativePath="MyCert2.PFX" Password="ENCRYPTED-PASSWORD" IsPasswordEncrypted="true"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
        
    <Service Name="FrontEndService" >
      <StatelessService ServiceTypeName="FrontEndServiceType" InstanceCount="[FrontEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
        <Service Name="BackEndService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="BackEndServiceType" InstanceCount="[BackEndService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="frontendservice-service-manifest"></a>FrontEndService tjänstmanifestet

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="FrontEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="FrontEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricfrontendservice:v1</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container or exe.  These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="IsContainer" Value=""/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />
  
  <!-- Data package is the contents of the Data directory under PackageRoot that contains an 
       independently-updateable and versioned static data that's consumed by the process at runtime. -->
  <DataPackage Name="Data" Version="1.0.0"/>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme and port 80 -->
      <Endpoint Name="FrontEndServiceTypeEndpoint" UriScheme="http" Port="80"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="backendservice-service-manifest"></a>BackEndService tjänstmanifestet

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="BackEndServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="BackEndServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ContainerHost>
        <!--The repo and image on https://hub.docker.com or Azure Container Registry. -->
        <ImageName>sfsamples.azurecr.io/sfsamples/servicefabricbackendservice:v1</ImageName>
        
        <!-- Pass comma delimited commands to your container. -->
        <Commands> dotnet, myproc.dll, 5 </Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container. These variables are overridden in the application manifest. -->
    <EnvironmentVariables>
      <EnvironmentVariable Name="IsContainer" Value="true"/>
    </EnvironmentVariables>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the host port on which to 
           listen. For a guest executable is used to register with the NamingService at its REST endpoint
           with http scheme. In this case since no port is specified, one is created and assigned dynamically
           to the service. This dynamically assigned host port is mapped to the container port (8905 in this sample),
            which was specified in the application manifest.-->
      <Endpoint Name="BackEndServiceTypeEndpoint" UriScheme="http" />
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

### <a name="policies-element"></a>Policies Element
Beskriver principer (slutpunkt för bindning, delning, kör som-paketet och säkerhet åtkomst) som ska tillämpas på importerade tjänstmanifestet. Mer information finns i [principer Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy Element
Definierar principen för resurs-styrning som tillämpas på nivån för hela tjänstpaketet. Mer information finns i [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Anger resursgränser för ett kodpaket. Mer information finns i [ResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies Element
Anger principer för att aktivera behållare-värdar. Mer information finns i [ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials Element
Autentiseringsuppgifter för behållaren avbildningslagringsplatsen för pull-avbildningar från. Mer information finns i [RepositoryCredentials Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding Element
Anger vilken slutpunktsresurs som ska binda till behållaren exponerade port. Mer information finns i [PortBinding Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Volume Element
Anger volymen som ska bindas till behållaren. Mer information finns i [volymelement](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>DriverOption Element
Drivrutiner som ska skickas till drivrutinen. Mer information finns i [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides Element
Windows Server-behållare kan inte vara kompatibla mellan olika versioner av Operativsystemet.  Du kan ange flera operativsystemsavbildningar per behållare och tagga dem med build-versioner av Operativsystemet. Hämta build-versionen av Operativsystemet genom att köra ”winver” i en kommandotolk i Windows. Om det underliggande Operativsystemet är version version 16299 (Windows Server version 1709), Service Fabric används den behållaravbildning som taggats med Os = ”16299”. En ej taggad behållaravbildning antas fungerar i alla versioner av Operativsystemet och åsidosätter den avbildning som angetts i tjänstmanifestet. Mer information finns i [ImageOverrides Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Image Element
Behållaravbildning som motsvarar OS build-versionsnumret som ska startas. Om Os-attributet inte anges, antas fungerar i alla versioner av Operativsystemet behållaravbildningen och åsidosätter den avbildning som angetts i tjänstmanifestet. Mer information finns i [bildelement](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides Element
 Mer information finns i [EnvironmentOverrides Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>EnvironmentVariable Element
Miljövariabeln. Mer information finns i [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>CertificateRef Element
Anger information om en X509 certifikat som ska göras tillgänglig för behållarmiljön. Certifikatet måste vara installerad på arkivet LocalMachine för alla klusternoder.
När programmet startas, läser certifikatet körningen och genererar en PFX-fil och lösenord (Windows) eller en PEM-fil (på Linux).
PFX-filen och lösenordet är tillgängliga i behållaren med hjälp av miljövariablerna Certificates_ServicePackageName_CodePackageName_CertName_PFX och Certificates_ServicePackageName_CodePackageName_CertName_Password. PEM-filen är tillgänglig i behållaren med miljövariabler Certificates_ServicePackageName_CodePackageName_CertName_PEM och Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey. Mer information finns i [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>DefaultServices Element
Anger instanser av tjänsten som skapas automatiskt när ett program instantieras mot den här typen. Mer information finns i [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Service Element
Deklarerar en tjänst som ska skapas automatiskt när programmet instantieras. Mer information finns i [Service Element](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>StatelessService Element
Definierar en tillståndslös tjänst. Mer information finns i [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>FrontEndService service manifest element
### <a name="servicemanifest-element"></a>ServiceManifest Element
Beskriver deklarativt tjänsttyp och version. Den visar oberoende av varandra kan uppgraderas kod, konfiguration och data-paket som tillsammans utgör inget tjänstepaket för att stödja en eller flera typer av tjänster. Resurser, inställningarna för startdiagnostik och tjänstmetadata, till exempel typ av tjänst, health egenskaper och mätvärden för Utjämning av nätverksbelastning, också anges. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definierar vilka typer av tjänster som stöds av en CodePackage i manifestet. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. Tjänsttyper deklareras i manifestet nivå och inte kodnivå för paketet. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Beskriver en typ av tillståndslösa. Mer information finns i [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som har stöd för en definierad tjänsttyp. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera stöds tjänsttyper vid körning. När det finns flera kodpaket, aktiveras de alla varje gång systemet söker efter någon av de deklarerade tjänsttyper. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis tjänstevärden tidskrävande. Förekomsten av en separat konfigurationsstartpunkten innebär att du slipper att köra tjänstevärden med höga privilegier för längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutas. Resulterande processen övervakas och startas om (början igen med SetupEntryPoint) om det skulle avslutas eller kraschar. Mer information finns i [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost Element
 Mer information finns i [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName Element
Lagringsplatsen och avbildning på https://hub.docker.com eller Azure Container Registry. Mer information finns i [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables-Element
Skicka miljövariabler till din behållare eller EXE-filen.  Mer information finns i [EnvironmentVariables-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable Element
Miljövariabeln. Mer information finns i [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Deklarerar en mapp med namnet av attributet namn, som innehåller en Settings.xml fil. Den här filen innehåller avsnitt av inställningar för användardefinierade, nyckel / värde-par som processen kan läsa tillbaka vid körning. Vid en uppgradering om det bara ConfigPackage version har ändrats startas sedan processen inte. I stället meddelar en motringning den process som konfigurationsinställningarna har ändrats så att de kan läsas dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage Element
Anger en mapp med namnet av attributet namn som innehåller statiska datafiler. Service Fabric kommer Papperskorgen alla exe-filerna och DLLHOSTs som anges i paket som värd och support när data-paket som anges i tjänstmanifestet uppgraderas. Mer information finns i [DataPackage-Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resurser-Element
Beskriver de resurser som används av den här tjänsten, som kan vara deklarerats utan att ändra kompilerad kod och ändras när tjänsten har distribuerats. Åtkomst till dessa resurser styrs via huvudnamn och principer för avsnitt i programmanifestet. Mer information finns i [resurser Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slutpunkter Element
Definierar slutpunkter för tjänsten. Mer information finns i [slutpunkter Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Mer information finns i [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>BackEndService service manifest element
### <a name="servicemanifest-element"></a>ServiceManifest Element
Beskriver deklarativt tjänsttyp och version. Den visar oberoende av varandra kan uppgraderas kod, konfiguration och data-paket som tillsammans utgör inget tjänstepaket för att stödja en eller flera typer av tjänster. Resurser, inställningarna för startdiagnostik och tjänstmetadata, till exempel typ av tjänst, health egenskaper och mätvärden för Utjämning av nätverksbelastning, också anges. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>ServiceTypes Element
Definierar vilka typer av tjänster som stöds av en CodePackage i manifestet. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. Tjänsttyper deklareras i manifestet nivå och inte kodnivå för paketet. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>StatelessServiceType Element
Beskriver en typ av tillståndslösa. Mer information finns i [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som har stöd för en definierad tjänsttyp. När en tjänst instantieras mot en av dessa typer av tjänster, aktiveras alla kodpaket som deklarerats i manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera stöds tjänsttyper vid körning. När det finns flera kodpaket, aktiveras de alla varje gång systemet söker efter någon av de deklarerade tjänsttyper. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis tjänstevärden tidskrävande. Förekomsten av en separat konfigurationsstartpunkten innebär att du slipper att köra tjänstevärden med höga privilegier för längre tid. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutas. Resulterande processen övervakas och startas om (början igen med SetupEntryPoint) om det skulle avslutas eller kraschar. Mer information finns i [EntryPoint Element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost Element
Mer information finns i [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName Element
Lagringsplatsen och avbildning på https://hub.docker.com eller Azure Container Registry. Mer information finns i [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Kommandon Element
Skicka en kommaavgränsad lista över kommandon till behållaren. Mer information finns i [kommandon Element](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables-Element
Skicka miljövariabler till din behållare eller EXE-filen.  Mer information finns i [EnvironmentVariables-Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>EnvironmentVariable Element
Miljövariabeln. Mer information finns i [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage Element
Deklarerar en mapp med namnet av attributet namn, som innehåller en Settings.xml fil. Den här filen innehåller avsnitt av inställningar för användardefinierade, nyckel / värde-par som processen kan läsa tillbaka vid körning. Vid en uppgradering om det bara ConfigPackage version har ändrats startas sedan processen inte. I stället meddelar en motringning den process som konfigurationsinställningarna har ändrats så att de kan läsas dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resurser-Element
Beskriver de resurser som används av den här tjänsten, som kan vara deklarerats utan att ändra kompilerad kod och ändras när tjänsten har distribuerats. Åtkomst till dessa resurser styrs via huvudnamn och principer för avsnitt i programmanifestet. Mer information finns i [resurser Element](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Slutpunkter Element
Definierar slutpunkter för tjänsten. Mer information finns i [slutpunkter Element](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
 Mer information finns i [Endpoint Element](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

