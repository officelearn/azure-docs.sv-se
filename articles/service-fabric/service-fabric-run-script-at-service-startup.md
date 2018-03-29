---
title: Köra ett skript när en Azure Service Fabric-tjänsten startar | Microsoft Docs
description: Lär dig hur du konfigurerar en princip för en startpunkt för Service Fabric-tjänsten installationen och kör ett skript på tid att starta tjänsten.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: bd2bb0d05029237242b42225a2c846c78a7c6de9
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Köra ett skript för start av tjänsten som ett lokalt konto för användaren eller systemet
Innan en körbara Service Fabric-tjänsten startar kan det vara nödvändigt att köra vissa konfiguration eller installationen arbete.  Till exempel konfigurera miljövariabler. Du kan ange ett skript köras innan tjänstens körbara fil som startas i service manifest för tjänsten. Konfigurera en RunAs-princip för den installationsprogrammet startpunkten som du kan ändra vilket konto körs installationsprogrammets under.  En startpunkt för separata inställningar kan du köra hög privilged konfigurationen för en kort tidsperiod så tjänstvärden körbara inte behöver köra med högre behörighet under längre tid.

Startpunkten för installationsprogrammet (**SetupEntryPoint** i den [tjänstmanifestet](service-fabric-application-and-service-manifests.md)) är en privilegierade startpunkt som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis den  *NetworkService* konto) innan andra startpunkt. Den körbara filen som anges av **EntryPoint** är vanligtvis tjänstvärden tidskrävande. Den **EntryPoint** körbar fil körs efter den **SetupEntryPoint** körbar fil har avslutas. Resulterande processen övervakas och startas om och börjar igen med **SetupEntryPoint** om den aldrig avslutar eller kraschar. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurera registreringspunkten för service-inställningar
Följande är en enkel service manifest exempel för en tillståndslös tjänst som anger ett installationsskript *MySetup.bat* i tjänsten **SetupEntryPoint**.  **Argument** används för att ange argument för skriptet när den körs.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurera princip för en startpunkt för installationen av tjänsten
Som standard i installationsprogrammet post punkt körbar fil körs under samma autentiseringsuppgifter som Service Fabric (vanligtvis den *NetworkService* konto).  I programmanifestet, kan du ändra säkerhetsbehörigheter för att kunna köra startskriptet under ett lokalt systemkonto eller ett administratörskonto.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurera principen med hjälp av ett lokalt systemkonto
Application manifest exemplet nedan visar hur du konfigurerar tjänsten installationsprogrammet startpunkten för att köras under användarkontot Administratör (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Skapa först en **säkerhetsobjekt** avsnitt med ett användarnamn, t.ex SetupAdminUser. SetupAdminUser användarkonto är medlem i gruppen Administratörer system.

Sedan under den **ServiceManifestImport** och konfigurera en princip för att tillämpa den här huvudnamn för **SetupEntryPoint**. Den här principen anger Service Fabric som när den **MySetup.bat** -filen körs det ska köras som SetupAdminUser (med administratörsbehörighet). Eftersom du har *inte* tillämpas en princip på Huvudstartadressen koden i **MyServiceHost.exe** körs under systemet **NetworkService** konto. Detta är det standardkonto som alla startpunkter för tjänsten körs.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurera principen med hjälp av lokala systemkontona
Ofta är det bättre att köra startskriptet med ett lokalt systemkonto i stället för ett administratörskonto. Kör en RunAs-princip som en medlem i gruppen administratörer vanligtvis fungerar inte bra eftersom datorerna har User Access Control (UAC) är aktiverat som standard. I sådana fall vi rekommenderar att du kör SetupEntryPoint som LocalSystem, i stället för som en lokal användare som lagts till i gruppen Administratörer. I följande exempel visas inställningen SetupEntryPoint att köras som LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="run-a-script-from-the-setup-entry-point"></a>Köra ett skript från installationsprogrammet startpunkt
Lägg nu till ett skript för att starta i projektet ska köras med administratörsbehörighet. 

Högerklicka på service-projekt i Visual Studio och lägga till en ny fil med namnet *MySetup.bat*.

Kontrollera därefter att den *MySetup.bat* ingår i tjänstpaketet. Som standard är den inte. Markera filen, högerklicka om du vill ha på snabbmenyn och välj **egenskaper**. I dialogrutan Egenskaper kontrollerar du att **kopiera till utdatakatalog** är inställd på **kopiera om nyare**. Se följande skärmbild.

![Visual Studio-CopyToOutput för SetupEntryPoint kommandofil][image1]

Nu redigera den *MySetup.bat* och Lägg till följande kommandon ange en systemmiljövariabler och utdata en textfil:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Sedan skapa och distribuera lösningen till ett kluster för lokal utveckling. När tjänsten har startats, enligt [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), ser du att filen MySetup.bat lyckades på två sätt. Öppna ett PowerShell-Kommandotolken och skriv:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anteckna namnet på den nod där tjänsten har distribuerats och startas i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Till exempel nod 2. Nu ska du navigera till mappen program instans arbete att hitta filen out.txt som visar värdet för **TestVariable**. Till exempel om den här tjänsten har distribuerats till nod 2, sedan går du till den här sökvägen för den **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Kör PowerShell-kommandon från en startpunkt för installationen
Att köra PowerShell från den **SetupEntryPoint** punkt, kan du köra **PowerShell.exe** i en batchfil som pekar på en PowerShell-fil. Lägg först till en PowerShell-fil till service-projekt – till exempel **MySetup.ps1**. Kom ihåg att ange den *kopiera om nyare* egenskapen så att filen ingår också i tjänstpaketet. I följande exempel visas en exempelfil batch som startar en PowerShell-fil som heter MySetup.ps1 som anger en systemmiljövariabler kallas **TestVariable**.

MySetup.bat att starta en PowerShell-fil:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Lägg till följande om du vill ange en systemmiljövariabler i PowerShell-filen:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Som standard när kommandofilen körs det ser ut i programmapp kallas **fungerar** för filer. I det här fallet när MySetup.bat körs, vi vill hitta filen MySetup.ps1 i samma mapp som programmet **kodpaketet** mapp. Ange arbetsmappen för att ändra den här mappen:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Felsöka ett startskript lokalt med hjälp av konsolomdirigering
Ibland är det användbart för felsökning för att se konsolens utdata från att köra ett installationsskript. Du kan ange en princip för omdirigering av konsolen på installationsprogrammet startpunkten i tjänstmanifestet som skriver utdata till en fil. Filen utdata skrivs till programmappen kallas **loggen** på klusternoden där programmet har distribuerats och kör. 

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som distribuerats i produktionsmiljön eftersom detta kan påverka program för växling vid fel. *Endast* använda detta för lokal utveckling och felsökning.  
> 
> 

Service manifest exemplet nedan visar inställningen omdirigering av konsol med ett FileRetentionCount-värde:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Om du ändrar nu filen MySetup.ps1 att skriva en **Echo** kommandot detta kommer att skriva till utdatafilen för felsökning:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> När du felsöka skriptet omedelbart ta bort den här principen för omdirigering av konsolen.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Mer information om program och säkerhet](service-fabric-application-and-service-security.md)
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
