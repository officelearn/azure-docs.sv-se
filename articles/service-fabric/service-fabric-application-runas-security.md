---
title: "Lär dig mer om Azure mikrotjänster säkerhetsprinciper | Microsoft Docs"
description: "En översikt över hur du kör ett Service Fabric-program under system och lokala säkerhetskonton, inklusive SetupEntry punkten där ett program behöver utföra Privilegierade åtgärder innan den startar"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: aae828489b708a5b538df1d63c12be23d0423da7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>Konfigurera säkerhetsprinciper för ditt program
Du kan skydda program som körs i kluster under olika användarkonton med hjälp av Azure Service Fabric. Service Fabric hjälper också till att skydda resurser som används av program vid tidpunkten för distribution under användarkonton – till exempel, filer, kataloger och certifikat. Det gör att program som körs, även i en delad värdmiljö säkrare från varandra.

Service Fabric-program körs under kontot som Fabric.exe-processen körs under som standard. Service Fabric ger också möjlighet att köra program under ett lokalt användarkonto eller kontot Lokalt system som anges i programmanifestet. Typer av lokala system som stöds är **Lokalanvändare**, **NetworkService**, **LocalService**, och **LocalSystem**.

 När du kör Service Fabric på Windows Server i ditt datacenter med hjälp av fristående installationsprogram, använder du Active Directory-domänkonton, inklusive grupphanterade tjänstkonton.

Du kan definiera och skapa användargrupper så att en eller flera användare kan läggas till varje grupp som ska hanteras tillsammans. Detta är användbart när det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå.

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurera princip för en startpunkt för installationen av tjänsten
Enligt beskrivningen i den [programmodell](service-fabric-application-model.md), startpunkten installationsprogrammet **SetupEntryPoint**, är en privilegierade startpunkt som körs med samma autentiseringsuppgifter som Service Fabric (vanligtvis den *NetworkService* konto) innan andra startpunkt. Den körbara filen som anges av **EntryPoint** är vanligtvis tjänstvärden tidskrävande. Med en separat installationsprogrammet startpunkten så slipper du köra körbara tjänstvärden med höga privilegier för längre tid. Den körbara filen som **EntryPoint** anger körs efter **SetupEntryPoint** avslutas korrekt. Resulterande processen övervakas och startas om och börjar igen med **SetupEntryPoint** om den aldrig avslutar eller kraschar.

Följande är en enkel service manifest-exempel som visar SetupEntryPoint och den huvudsakliga startadressen för tjänsten.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>Konfigurera principen med hjälp av ett lokalt konto
När du konfigurerar tjänsten om du vill ha en installationsprogrammet startpunkten, kan du ändra de säkerhetsbehörigheter som det körs under i programmanifestet. I följande exempel visar hur du konfigurerar tjänsten körs under användare administratörsbehörighet.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
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

Skapa först en **säkerhetsobjekt** avsnitt med ett användarnamn, t.ex SetupAdminUser. Detta anger att användaren är medlem i gruppen Administratörer system.

Sedan under den **ServiceManifestImport** och konfigurera en princip för att tillämpa den här huvudnamn för **SetupEntryPoint**. Detta meddelar Service Fabric som när den **MySetup.bat** fil körs, bör vara `RunAs` med administratörsbehörighet. Med hänsyn till att du har *inte* tillämpas en princip på Huvudstartadressen koden i **MyServiceHost.exe** körs under systemet **NetworkService** konto. Detta är det standardkonto som alla startpunkter för tjänsten körs.

Vi ska nu lägga till filen MySetup.bat Visual Studio-projekt att testa administratörsbehörighet. Högerklicka på service-projekt i Visual Studio och lägga till en ny fil med namnet MySetup.bat.

Kontrollera därefter att filen MySetup.bat ingår i tjänstpaketet. Som standard är den inte. Markera filen, högerklicka om du vill ha på snabbmenyn och välj **egenskaper**. I dialogrutan Egenskaper kontrollerar du att **kopiera till utdatakatalog** är inställd på **kopiera om nyare**. Se följande skärmbild.

![Visual Studio-CopyToOutput för SetupEntryPoint kommandofil][image1]

Öppna filen MySetup.bat och Lägg till följande kommandon:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Sedan skapa och distribuera lösningen till ett kluster för lokal utveckling. När tjänsten har startats, som visas i Service Fabric Explorer, kan du se att filen MySetup.bat lyckades på två sätt. Öppna ett PowerShell-Kommandotolken och skriv:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Anteckna namnet på den nod där tjänsten har distribuerats och startas i Service Fabric Explorer – till exempel nod 2. Nu ska du navigera till mappen program instans arbete att hitta filen out.txt som visar värdet för **TestVariable**. Till exempel om den här tjänsten har distribuerats till nod 2, sedan går du till den här sökvägen för den **MyApplicationType**:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurera principen med hjälp av lokala systemkontona
Ofta är det bättre att köra startskriptet med ett lokalt systemkonto i stället för ett administratörskonto. Kör en RunAs-princip som en medlem i gruppen administratörer vanligtvis fungerar inte eftersom datorer har User Access Control (UAC) är aktiverat som standard. I sådana fall **rekommenderar vi att du kör SetupEntryPoint som LocalSystem, i stället för som en lokal användare som lagts till i gruppen Administratörer**. I följande exempel visas inställningen SetupEntryPoint att köras som LocalSystem:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

För Linux-kluster för att köra en tjänst eller installationen startpunkt som **rot**, kan du ange den **AccountType** som **LocalSystem**.

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>Starta PowerShell-kommandon från en startpunkt för installationen
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

## <a name="use-console-redirection-for-local-debugging"></a>Använda omdirigering till konsolen för lokala felsökning
Ibland kan vara det bra att se konsolens utdata från att köra ett skript för felsökning. Om du vill göra detta måste ange du konsolen omdirigering av principer för skriver utdata till en fil. Filen utdata skrivs till programmappen kallas **loggen** på den nod där programmet har distribuerats och kör. (Se var du hittar det i föregående exempel).

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som distribuerats i produktionsmiljön eftersom detta kan påverka program för växling vid fel. *Endast* använda detta för lokal utveckling och felsökning.  
> 
> 

I följande exempel visas inställningen omdirigering av konsol med ett FileRetentionCount-värde:

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

**När du felsöka skriptet omedelbart ta bort den här konsolen omdirigeringspolicyn**.

## <a name="configure-a-policy-for-service-code-packages"></a>Konfigurera en princip för servicepaket för kod
I föregående steg sett du hur du använder en RunAs-princip på SetupEntryPoint. Nu ska vi titta lite djupare i hur du skapar olika säkerhetsobjekt som kan användas som service-principer.

### <a name="create-local-user-groups"></a>Skapa lokala användargrupper
Du kan definiera och skapa användargrupper som tillåter att en eller flera användare som ska läggas till en grupp. Detta är användbart om det finns flera användare för olika startpunkter och de måste ha vissa vanliga behörigheter som är tillgängliga på gruppnivå. I följande exempel visas en lokal grupp som kallas **LocalAdminGroup** som har administratörsbehörighet. Två användare, Customer1 och Customer2, som blir medlemmar i den lokala gruppen.

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>Skapa lokala användare
Du kan skapa en lokal användare som kan användas för att skydda en tjänst i programmet. När en **Lokalanvändare** kontotyp som anges i avsnittet säkerhetsobjekt i programmanifestet, Service Fabric skapar lokala användarkonton på datorer där programmet har distribuerats. Som standard har dessa konton inte samma namn som de som anges i programmanifestet (till exempel Customer3 i följande exempel). I stället de genereras dynamiskt och ha slumpmässiga lösenord.

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

Om ett program kräver att användarkonto och lösenord vara samma på alla datorer (till exempel för att aktivera NTLM-autentisering), ange klustermanifestet NTLMAuthenticationEnabled till true. Klustermanifestet måste också ange en NTLMAuthenticationPasswordSecret som används för att generera samma lösenord på alla datorer.

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>Tilldela principer till kod servicepaket
Den **RunAsPolicy** avsnittet för en **ServiceManifestImport** anger kontot från avsnittet säkerhetsobjekt som ska användas för att köra en kodpaketet. Koden paket från service manifest associerar även med användarkonton i avsnittet säkerhetsobjekt. Du kan ange detta för installations- eller huvudsakliga startpunkter eller ange `All` ska gälla för båda. I följande exempel visas olika principer tillämpas:

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

Om **EntryPointType** har inte angetts, standard är inställd på EntryPointType = ”Main”. Ange **SetupEntryPoint** är särskilt användbar när du vill köra vissa åtgärder för höga installationsprogrammet under en system-kontot. Den faktiska kod kan köras under ett konto med lägre behörighet.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Gäller en standardprincip för alla service code-paket
Du använder den **DefaultRunAsPolicy** att ange ett användarkonto för standard för alla paket som inte har en specifik **RunAsPolicy** definieras. Om de flesta av de kod paket som har angetts i tjänstmanifestet som används av ett program måste köras under samma användare, kan programmet bara att definiera en standardprincip RunAs med användarkontot. I följande exempel anger att om en kodpaketet saknar en **RunAsPolicy** anges kodpaketet ska köras under den **MyDefaultAccount** anges i avsnittet säkerhetsobjekt.

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>Använda en Active Directory-domän, grupp eller användare
Du kan köra tjänsten under autentiseringsuppgifterna för en Active Directory-användare eller grupp för en instans av Service Fabric som har installerats på Windows Server med hjälp av fristående installationsprogram. Detta är Active Directory lokalt i din domän och är inte med Azure Active Directory (AD Azure). Genom att använda en domänanvändare eller grupp kan du sedan komma åt andra resurser i domänen (till exempel filresurser) som har behörighet.

I följande exempel visas en Active Directory-användare som kallas *TestUser* med sin domän lösenord krypteras med hjälp av ett certifikat kallas *MyCert*. Du kan använda den `Invoke-ServiceFabricEncryptText` PowerShell-kommando för att skapa den hemliga chiffertext. Se [hantera hemligheter i Service Fabric program](service-fabric-application-secret-management.md) mer information.

Du måste distribuera den privata nyckeln för certifikat för att dekryptera lösenordet till den lokala datorn med hjälp av en out-of-band-metoden (i Azure, är detta via Azure Resource Manager). Sedan när Service Fabric distribuerar tjänstepaketet till datorn, är så möjligt att dekryptera hemligheten och autentisera med Active Directory för att köras under autentiseringsuppgifterna (tillsammans med användarnamnet).

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>Använda en grupp för Hanterat tjänstkonto.
Du kan köra tjänsten som ett grupphanterat tjänstkonto (gMSA) för en instans av Service Fabric som har installerats på Windows Server med hjälp av fristående installationsprogram. Observera att detta är Active Directory lokalt i din domän och är inte med Azure Active Directory (AD Azure). Det finns inget lösenord genom att använda ett gMSA eller krypterade lösenordet lagras i den `Application Manifest`.

I följande exempel visas hur du skapar ett konto för gMSA kallas *svc-Test$*; distribuera det hanterade tjänstkontot till klusternoderna, och hur du konfigurerar användarens huvudnamn.

##### <a name="prerequisites"></a>Förutsättningar.
- Domänen måste en KDS-rotnyckel.
- Domänen måste vara på en Windows Server 2012 eller senare funktionsnivå.

##### <a name="example"></a>Exempel
1. Active directory-domän som administratör skapa en grupp hanteras konto med hjälp av `New-ADServiceAccount` cmdleten igen och kontrollera att den `PrincipalsAllowedToRetrieveManagedPassword` innehåller alla klusternoder för service fabric. Observera att `AccountName`, `DnsHostName`, och `ServicePrincipalName` måste vara unika.
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. På alla klusternoder för service fabric (till exempel `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`), installera och testa gMSA.
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. Konfigurera användarens huvudnamn och konfigurera RunAsPolicy för att referera till användaren.
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Tilldela en säkerhetsprincip åtkomst för HTTP och HTTPS-slutpunkter
Om du använder en RunAs-princip till en tjänst och tjänstmanifestet deklarerar endpoint resurser med HTTP-protokollet, måste du ange en **SecurityAccessPolicy** så att portarna som allokerats till dessa slutpunkter är korrekt-åtkomstkontroll som anges för det RunAs-konto som tjänsten körs under. Annars **http.sys** inte har åtkomst till tjänsten och du får ett fel med anrop från klienten. I följande exempel gäller en slutpunkt som kallas kontot Customer1 **EndpointName**, vilket ger fullständig behörighet.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

För HTTPS-slutpunkten måste du också ange namnet på certifikatet som ska returneras till klienten. Du kan göra detta med hjälp av **EndpointBindingPolicy**, med det certifikat som har angetts i en certifikat-avsnittet i programmanifestet.

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Uppgradera flera program med https-slutpunkter
Du måste vara noga med att inte använda den **samma port** för olika instanser av samma program när du använder http**s**. Anledningen är att Service Fabric inte kommer att kunna uppgradera cert för någon av instanserna för programmet. Om exempelvis programmet båda 1 eller ett program 2 vill uppgradera sina certifikat 1 till cert 2. När uppgraderingen sker kan Service Fabric ha rensas certifikat 1 registreringen med http.sys även om det andra programmet fortfarande använder den. Om du vill förhindra detta identifierar Service Fabric att det finns redan en annan programinstansen registrerade på porten med certifikatet (på grund av http.sys) och misslyckas åtgärden.

Därför Service Fabric stöder inte uppgradering två olika tjänster med hjälp av **samma port** i olika programinstanser. Med andra ord kan du använda samma certifikat på olika tjänster på samma port. Om du behöver ha ett delade certifikat på samma port måste du se till att tjänsterna är placerade på olika datorer med placeringen. Eller Överväg att använda dynamiska portar för Service Fabric om möjligt för varje tjänst i varje instans av programmet. 

Om du ser en uppgradering misslyckas med https, ett fel varning som säger ”Windows HTTP-servern API inte stöder flera certifikat för program som delar en port”.

## <a name="a-complete-application-manifest-example"></a>Ett manifest när hela appen-exempel
Följande programmanifestet visar många av de olika inställningarna:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Förstå programmet modellen](service-fabric-application-model.md)
* [Ange resurser i en tjänstmanifestet](service-fabric-service-manifest-resources.md)
* [Distribuera ett program](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
