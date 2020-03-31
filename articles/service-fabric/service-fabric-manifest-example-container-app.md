---
title: Exempel på Azure Service Fabric-behållarprogrammanifest
description: Lär dig hur du konfigurerar inställningar för program- och tjänstmanifest för ett Service Fabric-program med flera behållare.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: pepogors
ms.openlocfilehash: 5efdbd17db20e69ace33b379ddbb99b2c4a20e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258400"
---
# <a name="multi-container-application-and-service-manifest-examples"></a>Exempel på program med flera container och tjänstmanifest
Följande är exempel på program- och tjänstmanifest för ett Service Fabric-program med flera behållare. Syftet med dessa exempel är att visa vilka inställningar som är tillgängliga och hur de ska användas. Dessa program- och tjänstmanifest baseras på windows [server 2016-behållareexempelmanifesten.](https://github.com/Azure-Samples/service-fabric-containers/tree/master/Windows)

Följande funktioner visas:

|Manifest|Funktioner|
|---|---|
|[Programmanifest](#application-manifest)| [åsidosätta miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurera mappning mellan behållarport till värd,](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery) [konfigurera autentisering av behållarregister](service-fabric-get-started-containers.md#configure-container-repository-authentication), [resursstyrning,](service-fabric-resource-governance.md) [ange isoleringsläge,](service-fabric-get-started-containers.md#configure-isolation-mode) [ange OS-build-specific container images](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)| 
|[Servicemanifest för FrontEndService](#frontendservice-service-manifest)| [ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurera en slutpunkt,](service-fabric-get-started-containers.md#configure-communication)skicka kommandon till behållaren, [importera ett certifikat till en behållare](service-fabric-securing-containers.md)| 
|[Tjänstmanifest för BackEndService](#backendservice-service-manifest)|[ange miljövariabler](service-fabric-get-started-containers.md#configure-and-set-environment-variables), [konfigurera en slutpunkt](service-fabric-get-started-containers.md#configure-communication), [konfigurera volymdrivrutin](service-fabric-containers-volume-logging-drivers.md)| 

Se [Manifestelement för program,](#application-manifest-elements) [FrontEndService-tjänstmanifestelement](#frontendservice-service-manifest-elements)och Manifestelement för [BackEndService-tjänsten](#backendservice-service-manifest-elements) för mer information om specifika XML-element.

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

## <a name="frontendservice-service-manifest"></a>Servicemanifest för FrontEndService

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

## <a name="backendservice-service-manifest"></a>Tjänstmanifest för BackEndService

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

### <a name="policies-element"></a>Element för principer
Beskriver principer (slutpunktsbindning, paketdelning, körning och säkerhetsåtkomst) som ska tillämpas på det importerade tjänstmanifestet. Mer information finns i [Policy Element](service-fabric-service-model-schema-elements.md#PoliciesElementServiceManifestImportPoliciesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="servicepackageresourcegovernancepolicy-element"></a>ServicePackageResourceGovernancePolicy-element
Definierar den resursstyrningsprincip som tillämpas på nivån för hela servicepaketet. Mer information finns i [ServicePackageResourceGovernancePolicy Element](service-fabric-service-model-schema-elements.md#ServicePackageResourceGovernancePolicyElementServicePackageResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInServicePackageTypecomplexType)

### <a name="resourcegovernancepolicy-element"></a>ResourceGovernancePolicy Element
Anger resursgränser för ett kodpaket. Mer information finns i [Element resourcegovernancePolicy](service-fabric-service-model-schema-elements.md#ResourceGovernancePolicyElementResourceGovernancePolicyTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelementDefinedInDigestedEndpointelement)

### <a name="containerhostpolicies-element"></a>ContainerHostPolicies Element
Anger principer för att aktivera behållarvärdar. Mer information finns i [ContainerHostPolicies Element](service-fabric-service-model-schema-elements.md#ContainerHostPoliciesElementContainerHostPoliciesTypeComplexTypeDefinedInServiceManifestImportPoliciesTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="repositorycredentials-element"></a>RepositoryCredentials Element
Autentiseringsuppgifter för behållaravbildningsdatabasen att hämta bilder från. Mer information finns i [RepositoryCredentials Element](service-fabric-service-model-schema-elements.md#RepositoryCredentialsElementRepositoryCredentialsTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="portbinding-element"></a>PortBinding-element
Anger vilken slutpunktsresurs som ska bindas till den exponerade behållarporten. Mer information finns i [PortBinding Element](service-fabric-service-model-schema-elements.md#PortBindingElementPortBindingTypeComplexTypeDefinedInServicePackageContainerPolicyTypecomplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="volume-element"></a>Volymelement
Anger den volym som ska bindas till behållaren. Mer information finns i [Volymelement](service-fabric-service-model-schema-elements.md#VolumeElementContainerVolumeTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="driveroption-element"></a>Element för drivrutinsoption
Drivrutinsalternativ som ska skickas till föraren. Mer information finns i [DriverOption Element](service-fabric-service-model-schema-elements.md#DriverOptionElementDriverOptionTypeComplexTypeDefinedInContainerLoggingDriverTypecomplexTypeDefinedInContainerVolumeTypecomplexType)

### <a name="imageoverrides-element"></a>ImageOverrides-element
Windows Server-behållare kanske inte är kompatibla i olika versioner av operativsystemet.  Du kan ange flera OS-avbildningar per behållare och tagga dem med byggversionerna av operativsystemet. Hämta byggversionen av operativsystemet genom att köra "winver" i en Kommandotolk i Windows. Om det underliggande operativsystemet är version 16299 (Windows Server version 1709) väljer Service Fabric behållaravbildningen som taggats med Os="16299". En otaggad behållaravbildning antas fungera i alla versioner av operativsystemet och åsidosätter avbildningen som anges i tjänstmanifestet. Mer information finns i [ImageOverrides Element](service-fabric-service-model-schema-elements.md#ImageOverridesElementImageOverridesTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="image-element"></a>Bildelement
Behållaravbildning som motsvarar versionsnumret för OS-versionen som ska startas. Om attributet Os inte har angetts antas behållaravbildningen att fungera i alla versioner av operativsystemet och åsidosätter avbildningen som anges i tjänstmanifestet. Mer information finns i [Bildelement](service-fabric-service-model-schema-elements.md#ImageElementImageTypeComplexTypeDefinedInImageOverridesTypecomplexType)

### <a name="environmentoverrides-element"></a>EnvironmentOverrides Element
 Mer information finns i [EnvironmentOverrides Element](service-fabric-service-model-schema-elements.md#EnvironmentOverridesElementEnvironmentOverridesTypeComplexTypeDefinedInServiceManifestImportelement)

### <a name="environmentvariable-element"></a>MiljöVariabelt element
Miljövariabel. Mer information finns i [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="certificateref-element"></a>Elementet CertificateRef
Anger information om ett X509-certifikat som ska exponeras för behållarmiljön. Certifikatet måste vara installerat i LocalMachine-arkivet för alla klusternoder.
När programmet startar läser körningen certifikatet och genererar en PFX-fil och ett lösenord (i Windows) eller en PEM-fil (på Linux).
PFX-filen och lösenordet är tillgängliga i behållaren med hjälp av de Certificates_ServicePackageName_CodePackageName_CertName_PFX och Certificates_ServicePackageName_CodePackageName_CertName_Password miljövariablerna. PEM-filen är tillgänglig i behållaren med hjälp av de Certificates_ServicePackageName_CodePackageName_CertName_PEM och Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey miljövariablerna. Mer information finns i [CertificateRef Element](service-fabric-service-model-schema-elements.md#CertificateRefElementContainerCertificateTypeComplexTypeDefinedInContainerHostPoliciesTypecomplexType)

### <a name="defaultservices-element"></a>Standardtjänstelement
Deklarerar tjänstinstanser som skapas automatiskt när ett program instansieras mot den här programtypen. Mer information finns i [DefaultServices Element](service-fabric-service-model-schema-elements.md#DefaultServicesElementDefaultServicesTypeComplexTypeDefinedInApplicationManifestTypecomplexTypeDefinedInApplicationInstanceTypecomplexType)

### <a name="service-element"></a>Serviceelement
Deklarerar en tjänst som ska skapas automatiskt när programmet instansieras. Mer information finns i [Serviceelement](service-fabric-service-model-schema-elements.md#ServiceElementanonymouscomplexTypeComplexTypeDefinedInDefaultServicesTypecomplexType)

### <a name="statelessservice-element"></a>Tillståndslöst tjänstelement
Definierar en tillståndslös tjänst. Mer information finns i [StatelessService Element](service-fabric-service-model-schema-elements.md#StatelessServiceElementStatelessServiceTypeComplexTypeDefinedInServiceTemplatesTypecomplexTypeDefinedInServiceelement)


## <a name="frontendservice-service-manifest-elements"></a>Manifestelement för FrontEndService-tjänsten
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativt beskriver tjänsttypen och versionen. Den listar oberoende uppgraderingsbara kod- och konfigurations- och datapaket som tillsammans utgör ett servicepaket för att stödja en eller flera tjänsttyper. Resurser, diagnostikinställningar och tjänstmetadata, till exempel tjänsttyp, hälsoegenskaper och belastningsutjämningsmått, anges också. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elementet ServiceTypes
Definierar vilka tjänsttyper som stöds av ett CodePackage i det här manifestet. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. Tjänsttyper deklareras på manifestnivå och inte kodpaketnivån. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Tillståndslöst ServiceType-element
Beskriver en tillståndslös tjänsttyp. Mer information finns i [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som stöder en definierad tjänsttyp. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera de tjänsttyper som stöds vid körning. När det finns flera kodpaket aktiveras alla när systemet letar efter någon av de deklarerade tjänsttyperna. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis den tjänstvärd som körs. Förekomsten av en separat inställningsanspunkt undviker att behöva köra tjänstvärden med hög behörighet under längre tidsperioder. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutats. Den resulterande processen övervakas och startas om (börjar igen med SetupEntryPoint) om den någonsin avslutas eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost-element
 Mer information finns i [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName-element
Repo och avbildningen på [https://hub.docker.com](https://hub.docker.com) eller Azure Container Registry. Mer information finns i [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables Element
Skicka miljövariabler till din behållare eller exe.  Mer information finns i [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>MiljöVariabelt element
Miljövariabel. Mer information finns i [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp, namngiven efter attributet Name, som innehåller filen Settings.xml. Den här filen innehåller avsnitt av användardefinierade inställningar för nyckelvärdespar som processen kan läsa upp vid körning. Under en uppgradering, om bara ConfigPackage-versionen har ändrats, startas inte den pågående processen om. I stället meddelar en motringning processen att konfigurationsinställningarna har ändrats så att de kan laddas om dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="datapackage-element"></a>DataPackage-element
Deklarerar en mapp, namngiven efter attributet Name, som innehåller statiska datafiler. Service Fabric kommer att återvinna alla EXEs och DLLHOSTs som anges i värden och supportpaket när något av de datapaket som anges i tjänstmanifestet uppgraderas. Mer information finns i [DataPackage Element](service-fabric-service-model-schema-elements.md#DataPackageElementDataPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedDataPackageelement)

### <a name="resources-element"></a>Resurselement
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomsten till dessa resurser kontrolleras via avsnitten Huvudmän och principer i programmanifestet. Mer information finns i [Resurselement](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element för slutpunkter
Definierar slutpunkter för tjänsten. Mer information finns i [Element för slutpunkter](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
Mer information finns i [Slutpunktselement](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)


## <a name="backendservice-service-manifest-elements"></a>Manifestelement för BackEndService-tjänsten
### <a name="servicemanifest-element"></a>ServiceManifest Element
Deklarativt beskriver tjänsttypen och versionen. Den listar oberoende uppgraderingsbara kod- och konfigurations- och datapaket som tillsammans utgör ett servicepaket för att stödja en eller flera tjänsttyper. Resurser, diagnostikinställningar och tjänstmetadata, till exempel tjänsttyp, hälsoegenskaper och belastningsutjämningsmått, anges också. Mer information finns i [ServiceManifest Element](service-fabric-service-model-schema-elements.md#ServiceManifestElementServiceManifestTypeComplexType)

### <a name="servicetypes-element"></a>Elementet ServiceTypes
Definierar vilka tjänsttyper som stöds av ett CodePackage i det här manifestet. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. Tjänsttyper deklareras på manifestnivå och inte kodpaketnivån. Mer information finns i [ServiceTypes Element](service-fabric-service-model-schema-elements.md#ServiceTypesElementServiceAndServiceGroupTypesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="statelessservicetype-element"></a>Tillståndslöst ServiceType-element
Beskriver en tillståndslös tjänsttyp. Mer information finns i [StatelessServiceType Element](service-fabric-service-model-schema-elements.md#StatelessServiceTypeElementStatelessServiceTypeTypeComplexTypeDefinedInServiceAndServiceGroupTypesTypecomplexTypeDefinedInServiceTypesTypecomplexType)

### <a name="codepackage-element"></a>CodePackage Element
Beskriver ett kodpaket som stöder en definierad tjänsttyp. När en tjänst instansieras mot en av dessa tjänsttyper aktiveras alla kodpaket som deklarerats i det här manifestet genom att köra sina startpunkter. De resulterande processerna förväntas registrera de tjänsttyper som stöds vid körning. När det finns flera kodpaket aktiveras alla när systemet letar efter någon av de deklarerade tjänsttyperna. Mer information finns i [CodePackage Element](service-fabric-service-model-schema-elements.md#CodePackageElementCodePackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedCodePackageelement)

### <a name="entrypoint-element"></a>EntryPoint Element
Den körbara filen som anges av EntryPoint är vanligtvis den tjänstvärd som körs. Förekomsten av en separat inställningsanspunkt undviker att behöva köra tjänstvärden med hög behörighet under längre tidsperioder. Den körbara filen som anges av EntryPoint körs när SetupEntryPoint har avslutats. Den resulterande processen övervakas och startas om (börjar igen med SetupEntryPoint) om den någonsin avslutas eller kraschar. Mer information finns i [EntryPoint-element](service-fabric-service-model-schema-elements.md#EntryPointElementEntryPointDescriptionTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="containerhost-element"></a>ContainerHost-element
Mer information finns i [ContainerHost Element](service-fabric-service-model-schema-elements.md#ContainerHostElementContainerHostEntryPointTypeComplexTypeDefinedInEntryPointDescriptionTypecomplexType)

### <a name="imagename-element"></a>ImageName-element
Repo och avbildningen på [https://hub.docker.com](https://hub.docker.com) eller Azure Container Registry. Mer information finns i [ImageName Element](service-fabric-service-model-schema-elements.md#ImageNameElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="commands-element"></a>Element för kommandon
Skicka en kommaavgränsad lista med kommandon till behållaren. Mer information finns i [Kommandoelement](service-fabric-service-model-schema-elements.md#CommandsElementxs:stringComplexTypeDefinedInContainerHostEntryPointTypecomplexType)

### <a name="environmentvariables-element"></a>EnvironmentVariables Element
Skicka miljövariabler till din behållare eller exe.  Mer information finns i [EnvironmentVariables Element](service-fabric-service-model-schema-elements.md#EnvironmentVariablesElementEnvironmentVariablesTypeComplexTypeDefinedInCodePackageTypecomplexType)

### <a name="environmentvariable-element"></a>MiljöVariabelt element
Miljövariabel. Mer information finns i [EnvironmentVariable Element](service-fabric-service-model-schema-elements.md#EnvironmentVariableElementEnvironmentVariableOverrideTypeComplexTypeDefinedInEnvironmentOverridesTypecomplexType)

### <a name="configpackage-element"></a>ConfigPackage-element
Deklarerar en mapp, namngiven efter attributet Name, som innehåller filen Settings.xml. Den här filen innehåller avsnitt av användardefinierade inställningar för nyckelvärdespar som processen kan läsa upp vid körning. Under en uppgradering, om bara ConfigPackage-versionen har ändrats, startas inte den pågående processen om. I stället meddelar en motringning processen att konfigurationsinställningarna har ändrats så att de kan laddas om dynamiskt. Mer information finns i [ConfigPackage Element](service-fabric-service-model-schema-elements.md#ConfigPackageElementConfigPackageTypeComplexTypeDefinedInServiceManifestTypecomplexTypeDefinedInDigestedConfigPackageelement)

### <a name="resources-element"></a>Resurselement
Beskriver de resurser som används av den här tjänsten, som kan deklareras utan att ändra kompilerad kod och ändras när tjänsten distribueras. Åtkomsten till dessa resurser kontrolleras via avsnitten Huvudmän och principer i programmanifestet. Mer information finns i [Resurselement](service-fabric-service-model-schema-elements.md#ResourcesElementResourcesTypeComplexTypeDefinedInServiceManifestTypecomplexType)

### <a name="endpoints-element"></a>Element för slutpunkter
Definierar slutpunkter för tjänsten. Mer information finns i [Element för slutpunkter](service-fabric-service-model-schema-elements.md#EndpointsElementanonymouscomplexTypeComplexTypeDefinedInResourcesTypecomplexType)

### <a name="endpoint-element"></a>Slutpunktselement
 Mer information finns i [Slutpunktselement](service-fabric-service-model-schema-elements.md#EndpointElementEndpointOverrideTypeComplexTypeDefinedInEndpointselement)

