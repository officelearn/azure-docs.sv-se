---
title: Köra ett skript när en Azure Service Fabric-tjänst startar
description: Lär dig hur du konfigurerar en princip för en tjänstkonfigurationsanspunkt för tjänst och kör ett skript vid starttid för tjänsten.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464285"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Köra skript för start av tjänster som ett lokalt konto för användaren eller systemet
Innan en körbar tjänst för Service Fabric startar kan det vara nödvändigt att köra vissa konfigurations- eller installationsarbeten.  Till exempel konfigurera miljövariabler. Du kan ange ett skript som ska köras innan tjänsten körsbar startar i tjänstmanifestet för tjänsten. Genom att konfigurera en RunAs-princip för startpunkten för tjänstinställningar kan du ändra vilket konto som inställningen körs under.  Med en separat installationsanstã¤s kan du köra konfiguration med hög behörighet under en kort tidsperiod så att tjänstvärden inte behöver köras med hög behörighet under längre tidsperioder.

Inställningsanspunkten (**SetupEntryPoint** i [tjänstmanifestet)](service-fabric-application-and-service-manifests.md)är en privilegierad startpunkt som som standard körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis *NetworkService-kontot)* före någon annan startpunkt. Den körbara fil som anges av **EntryPoint** är vanligtvis den tidskrävande tjänstvärden. Den körbara **Filen i EntryPoint** körs efter att den körbara **installationen** har avslutats. Den resulterande processen övervakas och startas om och börjar igen med **SetupEntryPoint** om den någonsin avslutas eller kraschar. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurera tjänstens konfigurationsstartpunkt
Följande är ett enkelt tjänstmanifestexempel för en tillståndslös tjänst som anger ett installationsskript *MySetup.bat* i tjänsten **SetupEntryPoint**.  **Argument** används för att skicka argument till skriptet när det körs.

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
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurera principen för en startpunkt för tjänstinställningar
Som standard körs startpunkten för tjänstinställningar under samma autentiseringsuppgifter som Service Fabric (vanligtvis *NetworkService-kontot).*  I programmanifestet kan du ändra säkerhetsbehörigheterna för att köra startskriptet under ett lokalt systemkonto eller ett administratörskonto.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurera principen med hjälp av ett lokalt systemkonto
I följande programmanifestexempel visas hur du konfigurerar startpunkten för tjänstinställningar för att köras under användarkontot (SetupAdminUser).

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

Skapa först ett **huvudnamnavsnitt** med ett användarnamn, till exempel SetupAdminUser. Installationsanseendeanvändarnas användarkonto är medlem i systemgruppen Administratörer.

Konfigurera sedan en princip för att tillämpa det här huvudbeloppet på **SetupEntryPoint**under avsnittet **ServiceManifestImport** . Den här principen talar om för Service Fabric att när filen **MySetup.bat** körs ska den köras som SetupAdminUser ( med administratörsbehörighet). Eftersom du *inte* har tillämpat en princip på huvudstartpunkten körs koden i **MyServiceHost.exe** under kontot för **systemnätverkstjänst.** Det här är standardkontot som alla tjänsttransaktionspunkter körs som.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurera principen med hjälp av lokala systemkonton
Ofta är det bättre att köra startskriptet med ett lokalt systemkonto i stället för ett administratörskonto. Köra RunAs-principen som medlem i gruppen Administratörer fungerar vanligtvis inte bra eftersom datorer har UAC (User Access Control) aktiverat som standard. I sådana fall är rekommendationen att köra SetupEntryPoint som LocalSystem, i stället för som en lokal användare som läggs till i gruppen Administratörer. I följande exempel visas hur du ställer in installationsprogrammetEntryPoint för att köras som LocalSystem:

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
> För Linux-kluster, om du vill köra en tjänst eller installationsanspunkten som **rot,** kan du ange **AccountType** som **LocalSystem**.

## <a name="run-a-script-from-the-setup-entry-point"></a>Köra ett skript från installationsanspunkten
Lägg nu till ett startskript i projektet för att köras under administratörsbehörighet. 

Högerklicka på serviceprojektet i Visual Studio och lägg till en ny fil med namnet *MySetup.bat*.

Se sedan till att *filen MySetup.bat* ingår i servicepaketet. Som standard är det inte. Markera filen, högerklicka för att hämta snabbmenyn och välj **Egenskaper**. Kontrollera att kopiera till **utdatakatalog** är inställt på **Kopiera om den är nyare**i dialogrutan Egenskaper. Se följande skärmbild.

![Visual Studio CopyToOutput för SetupEntryPoint-kommandofil][image1]

Redigera nu filen *MySetup.bat* och lägg till följande kommandon som en systemmiljövariabel och mata ut en textfil:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Skapa och distribuera sedan lösningen till ett lokalt utvecklingskluster. När tjänsten har startats, som visas i [Service Fabric Explorer,](service-fabric-visualizing-your-cluster.md)kan du se att filen MySetup.bat lyckades på två sätt. Öppna en PowerShell-kommandotolk och skriv:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anteckna sedan namnet på noden där tjänsten distribuerades och startades i [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). Till exempel Nod 2. Navigera sedan till arbetsmappen för programinstansen för att ta reda på filen out.txt som visar värdet för **TestVariable**. Om den här tjänsten till exempel har distribuerats till nod 2 kan du gå till den här sökvägen för **MyApplicationType:**

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Köra PowerShell-kommandon från en installationsanstringspunkt
Om du vill köra PowerShell från **setupEntryPoint-punkten** kan du köra **PowerShell.exe** i en kommandofil som pekar på en PowerShell-fil. Lägg först till en PowerShell-fil i serviceprojektet – till exempel **MySetup.ps1**. Kom ihåg att ange egenskapen *Kopiera om den nyare* så att filen också inkluderas i servicepaketet. I följande exempel visas en exempelbatchfil som startar en PowerShell-fil som heter MySetup.ps1, som anger en systemmiljövariabel som kallas **TestVariable**.

MySetup.bat för att starta en PowerShell-fil:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Lägg till följande i PowerShell-filen för att ange en systemmiljövariabel:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> När kommandofilen körs tittar den som standard på programmappen **work** for files. I det här fallet, när MySetup.bat körs, vill vi att detta ska hitta filen MySetup.ps1 i samma mapp, som är **programkodpaketet.** Om du vill ändra den här mappen anger du arbetsmappen:
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

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Felsöka ett startskript lokalt med hjälp av omdirigering av konsolen
Ibland är det användbart för felsökning för att se konsolen utdata från att köra ett installationsskript. Du kan ange en princip för omdirigering av konsolen på installationsanspunkten i tjänstmanifestet, som skriver utdata till en fil. Filutdata skrivs till programmappen som kallas **logga** in på klusternoden där programmet distribueras och körs. 

> [!WARNING]
> Använd aldrig principen om omdirigering av konsolen i ett program som distribueras i produktion eftersom detta kan påverka programmets redundans. *Använd* endast detta för lokal utveckling och felsökning.  
> 
> 

I exemplet med följande tjänstmanifest visas inställningen av omdirigering av konsolen med värdet FileRetentionCount:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Om du nu ändrar filen MySetup.ps1 för att skriva ett **Echo-kommando** skrivs detta till utdatafilen för felsökning:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> När du har felsökt skriptet tar du omedelbart bort den här princip för omdirigering av konsolen.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Läs mer om program- och tjänstsäkerhet](service-fabric-application-and-service-security.md)
* [Förstå programmodellen](service-fabric-application-model.md)
* [Ange resurser i ett tjänstmanifest](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
