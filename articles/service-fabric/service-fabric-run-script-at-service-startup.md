---
title: Köra ett skript när en Azure Service Fabric-tjänsten startar | Microsoft Docs
description: Lär dig hur du konfigurerar en princip för Service Fabric-tjänstens konfigurationsstartpunkt och köra ett skript på tid att starta tjänsten.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3ae43f7427996f8be15b22fec4406bbdfe8aa4fe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838450"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Köra skript för start av tjänster som ett lokalt konto för användaren eller systemet
Innan en Service Fabric-tjänst som körbara startas kan det bli nödvändigt att köra vissa konfiguration eller installation arbete.  Till exempel konfigurera miljövariabler. Du kan ange ett skript köras innan tjänsten körbara som startas i tjänstmanifestet för tjänsten. Konfigurera en princip för RunAs för tjänstens konfigurationsstartpunkt du kan ändra vilket konto körs installationsprogrammets under.  En separat konfigurationsstartpunkten kan du köra privilegierad konfigurationen för en kort tidsperiod, så tjänstevärden körbara inte behöver köra med höga privilegier under längre tid.

Konfigurationsstartpunkten (**SetupEntryPoint** i den [tjänstmanifestet](service-fabric-application-and-service-manifests.md)) är en privilegierad startpunkt som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis den  *NetworkService* konto) innan andra startpunkt. Den körbara filen som anges av **EntryPoint** är vanligtvis tjänstevärden tidskrävande. Den **EntryPoint** körbar fil körs efter den **SetupEntryPoint** körbar fil har avslutas. Resulterande processen övervakas och startas om, och börjar igen med **SetupEntryPoint** om det skulle avslutas eller kraschar. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurera tjänstens konfigurationsstartpunkt
Följande är ett enkelt exempel på manifest för en tillståndslös tjänst som anger ett installationsskript *MySetup.bat* i tjänsten **SetupEntryPoint**.  **Argument** används för att skicka argument till skriptet när den körs.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurera principen för en tjänstens konfigurationsstartpunkt
Som standard körs den tjänsten installationsprogrammet körbara filen under samma autentiseringsuppgifter som Service Fabric (vanligtvis den *NetworkService* konto).  I applikationsmanifestet, kan du ändra säkerhetsbehörigheter för att startskriptet körs under ett lokalt systemkonto eller ett administratörskonto.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurera principen med hjälp av ett lokalt systemkonto
I följande application manifest exempel visas hur du konfigurerar tjänstens konfigurationsstartpunkt körs under användarkontot Administratör (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Skapa först en **huvudkonton** med ett användarnamn, t.ex SetupAdminUser. Användarkontot SetupAdminUser är medlem i systemgruppen Administrators.

Sedan under den **ServiceManifestImport** avsnittet måste du konfigurera en princip för att tillämpa den här huvudnamn till **SetupEntryPoint**. Den här principen anger för Service Fabric att om den **MySetup.bat** fil körs den ska köras som SetupAdminUser (med administratörsbehörighet). Eftersom du har *inte* tillämpas den huvudsakliga startpunkten koden i en princip **MyServiceHost.exe** körs under systemet **NetworkService** konto. Det här är det standardkonto som alla startpunkter för tjänsten körs.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurera principen med hjälp av lokala systemkontona
Ofta är det bättre att startskriptet körs med ett lokalt systemkonto i stället för ett administratörskonto. Kör en RunAs-princip som en medlem i gruppen administratörer vanligtvis fungerar inte bra eftersom datorer har User Access Control (UAC) är aktiverat som standard. I sådana fall är rekommendationen att köra SetupEntryPoint som LocalSystem, i stället för som en lokal användare som lagts till i gruppen Administratörer. I följande exempel visas att SetupEntryPoint ska köras som LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> För Linux-kluster för att köra en tjänst eller installationen startpunkt som **rot**, kan du ange den **AccountType** som **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Köra ett skript från konfigurationsstartpunkten
Lägg nu till ett skript att starta projektet ska köras med administratörsbehörighet. 

Högerklicka på service-projekt i Visual Studio och Lägg till en ny fil med namnet *MySetup.bat*.

Därefter kontrollerar du att den *MySetup.bat* fil som ingår i tjänstpaketet. Som standard är det inte. Markera filen, högerklicka för att hämta snabbmenyn och välj **egenskaper**. I dialogrutan Egenskaper kontrollerar du att **kopiera till utdatakatalog** är inställd på **kopiera om nyare**. Se följande skärmbild.

![Visual Studio-CopyToOutput för SetupEntryPoint kommandofil][image1]

Nu redigera den *MySetup.bat* filen och Lägg till följande kommandon ställa in en systemmiljövariabel och matar ut en textfil:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Sedan skapa och distribuera lösningen till ett lokalt utvecklingskluster. När tjänsten har startats, enligt [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), ser du att filen MySetup.bat lyckades på två sätt. Öppna en PowerShell-Kommandotolken och skriv:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anteckna namnet på den nod där tjänsten distribueras och igång i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Till exempel nod 2. Nu ska du navigera till mappen program instans arbete att hitta filen out.txt som visar värdet för **TestVariable**. Till exempel om den här tjänsten har distribuerats till nod 2, sedan går du till den här sökvägen för den **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Kör PowerShell-kommandon från en startpunkt för installationen
Att köra PowerShell från den **SetupEntryPoint** -platsen som du kan köra **PowerShell.exe** i en batchfil som pekar på en PowerShell-fil. Lägg först till en PowerShell-fil till i tjänstens projekt – till exempel **MySetup.ps1**. Kom ihåg att ange den *kopiera om nyare* egenskapen så att filen ingår också i tjänstpaketet. I följande exempel visas en exempelfil i batch som startar en PowerShell-fil som heter MySetup.ps1 som anger en systemmiljövariabel kallas **TestVariable**.

MySetup.bat att starta en PowerShell-fil:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Lägg till följande om du vill ange en systemmiljövariabel i PowerShell-filen:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Som standard när kommandofilen körs den tittar på programmappen som heter **fungerar** för filer. I det här fallet när MySetup.bat körs, vill vi att hitta filen MySetup.ps1 i samma mapp, vilket är programmet **kodpaketet** mapp. Ange arbetsmappen för att ändra den här mappen:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Felsöka ett startskript lokalt med hjälp av omdirigering av konsol
Ibland kan är det användbart för felsökning i konsolens utdata från att köra ett installationsskript. Du kan ange en princip för omdirigering av konsol på konfigurationsstartpunkten i tjänstmanifestet som skriver utdata till en fil. Filen utdata skrivs till programmappen kallas **log** till klusternoden där programmet har distribuerats och körs. 

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som har distribuerats i produktionsmiljön, eftersom detta kan påverka program redundans. *Endast* använda detta för lokal utveckling och felsökning.  
> 
> 

I följande exempel på manifest visar ställa in omdirigering av konsol med ett FileRetentionCount-värde:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Om du ändrar nu MySetup.ps1-filen för att skriva en **Echo** kommandot detta ska skrivas till utdatafilen för felsökning:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> När du felsöka skriptet du omedelbart ta bort den här principen för omdirigering av konsol.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Lär dig mer om program- och Tjänstsäkerhet](service-fabric-application-and-service-security.md)
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
