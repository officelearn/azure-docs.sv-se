---
title: Distribuera en befintlig körbar fil till Azure Service Fabric | Microsoft Docs
description: Lär dig hur du paketerar ett befintligt program som gäst körbara, så den kan användas för Service Fabric-klustret.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell;mikhegn
ms.openlocfilehash: 9d1e2f801db2ed7edf262f8a5f221057ef791106
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Paketera och distribuera en befintlig körbar fil till Service Fabric
När Paketera en befintlig körbar fil som en [gäst körbara](service-fabric-guest-executables-introduction.md), kan du antingen använda en mall för Visual Studio-projekt eller [skapa programpaketet manuellt](#manually). Med Visual Studio skapas application paketet struktur och manifest-filer av den nya projektmallen.

> [!TIP]
> Det enklaste sättet att paketera en befintlig Windows körbara till en tjänst är att använda Visual Studio och på Linux för att använda Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Använda Visual Studio för att paketera och distribuera en befintlig körbar fil
Visual Studio har ett Service Fabric-tjänstmall som hjälper dig att distribuera gäst körbara till Service Fabric-klustret.

1. Välj **filen** > **nytt projekt**, och skapa ett Service Fabric-program.
2. Välj **gäst körbara** som tjänstmallen.
3. Klicka på **Bläddra** att välja mappen med den körbara filen och Fyll i resten av parametrar för att skapa tjänsten.
   * *Code paketet beteende*. Kan ställas in för att kopiera allt innehåll i mappen till Visual Studio-projektet, vilket är användbart om den körbara filen inte ändras. Om du tror att den körbara filen för att ändra och vill kunna hämta nya versioner dynamiskt kan välja du att länka till mappen i stället. Du kan använda länkade mappar när du skapar programprojekt i Visual Studio. Detta leder till källplatsen från i projektet, vilket gör det möjligt för dig att uppdatera gästen körbara i dess källa mål. Dessa uppdateringar blir en del av programpaket version.
   * *Programmet* anger körbar fil som ska köras för att starta tjänsten.
   * *Argument* anger argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
   * *WorkingFolder* anger arbetskatalog för processen som ska startas. Du kan ange tre värden:
     * `CodeBase` Anger att arbetskatalogen ska anges till katalogen koden i programpaketet (`Code` directory som visas i föregående filstruktur).
     * `CodePackage` Anger att arbetskatalogen ska anges till roten i programpaketet (`GuestService1Pkg` visas i föregående filstruktur).
     * `Work` Anger att filerna placeras i en underkatalog med namnet arbete.
4. Namnge tjänsten och klicka på **OK**.
5. Om din tjänst måste en slutpunkt för kommunikation, du kan nu lägga till protokollet, porten och typ ServiceManifest.xml-filen. Till exempel: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Du kan nu använder paketet och publicera åtgärd mot ditt lokala kluster med felsökning lösningen i Visual Studio. När du är klar kan du publicera program till ett kluster eller kontrollera i lösningen till källkontroll.
7. Läs [Kontrollera körs programmet](#check-your-running-application) att se hur du visar din körbara gäst-tjänst körs i Service Fabric Explorer.

Ett exempel genomgång finns [skapa ditt första körbara gäst-program som använder Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Använda Yeoman till paketet och distribuera en befintlig körbar fil på Linux

Proceduren för att skapa och distribuera gäst körbara på Linux är samma som distribuerar ett csharp eller java-program.

1. I en terminal, skriver du in `yo azuresfguest`.
2. Namnge ditt program.
3. Namnge din tjänst och ange de information, inklusive sökvägen till den körbara filen och det måste anropas med parametrar.

Yeoman skapar ett programpaket med det aktuella programmet och manifestfiler tillsammans med installera och avinstallera skript.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Paketera och distribuera en befintlig körbar fil manuellt
Manuellt Paketera en körbar fil gäst baseras på följande allmänna steg:

1. Skapa katalogstrukturen paketet.
2. Lägg till programmets kod och configuration-filer.
3. Redigera service manifest-filen.
4. Redigera programmanifestfilen.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Skapa katalogstrukturen paketets
Du kan börja med att skapa katalogstrukturen, enligt beskrivningen i föregående avsnitt, ”programmet paketet filstruktur”.

### <a name="add-the-applications-code-and-configuration-files"></a>Lägg till programmets kod och configuration-filer
När du har skapat katalogstrukturen kan du lägga till programkoden och konfigurationsfiler under koden och konfigurationsversionerna kataloger. Du kan också skapa ytterligare kataloger eller underkataloger på kod eller konfigurationsversion kataloger.

Service Fabric har en `xcopy` av innehållet i tillämpningsprogrammets rotkatalog, så det finns ingen fördefinierad struktur att använda andra än att skapa två översta kataloger, kod och inställningar. (Du kan välja olika namn om du vill. Mer information finns i nästa avsnitt.)

> [!NOTE]
> Kontrollera att du inkluderar alla filer och beroenden som programmet behöver. Service Fabric kopieras innehållet för programpaket på alla noder i klustret där programmets tjänster kommer att distribueras. Paketet innehåller all kod som programmet måste köras. Förutsätter inte att beroenden som redan är installerade.
>
>

### <a name="edit-the-service-manifest-file"></a>Redigera filen service manifest
Nästa steg är att redigera filen service manifest för att inkludera följande information:

* Namnet på service-typen. Det här är ett ID som Service Fabric används för att identifiera en tjänst.
* Kommandot för att starta programmet (ExeHost).
* Alla skript som måste köras för att ställa in programmet (SetupEntrypoint).

Följande är ett exempel på en `ServiceManifest.xml` fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

I följande avsnitt gå igenom de olika delarna av filen som du behöver uppdatera.

#### <a name="update-servicetypes"></a>Uppdatera ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Du kan välja vilket namn som du vill använda för `ServiceTypeName`. Värdet används i den `ApplicationManifest.xml` fil för att identifiera tjänsten.
* Ange `UseImplicitHost="true"`. Det här attributet anger Service Fabric att tjänsten är baserat på en fristående app så att alla Service Fabric behöver göra är att starta som en process och övervaka dess hälsa.

#### <a name="update-codepackage"></a>Uppdatera CodePackage
Elementet CodePackage anger tjänstens kod plats (och version).

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Den `Name` element som används för att ange namnet på katalogen i programpaket som innehåller tjänstens kod. `CodePackage` också har den `version` attribut. Detta kan användas för att ange versionen av koden och också potentiellt kan användas för att uppgradera tjänstens kod med hjälp av programmet livscykel hanteringsinfrastrukturen i Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Valfritt: Uppdatera SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Elementet SetupEntryPoint används för att ange en körbar fil eller en batch-fil som ska köras innan tjänstens koden har startats. Det är ett valfritt steg så inte behöver inkluderas om det inte finns några initiering krävs. SetupEntryPoint körs varje gång tjänsten startas.

Det finns endast en SetupEntryPoint installationsskript behöver grupperas i en enda kommandofil om programmets installationen kräver flera skript. SetupEntryPoint kan köra alla typer av filer: körbara filer, batch-filer och PowerShell-cmdlets. Mer information finns i [konfigurera SetupEntryPoint](service-fabric-application-runas-security.md).

I föregående exempel körs SetupEntryPoint en batchfil som kallas `LaunchConfig.cmd` som finns i den `scripts` kod-mappen (förutsatt att elementet WorkingFolder är inställd på CodeBase).

#### <a name="update-entrypoint"></a>Uppdatera EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Den `EntryPoint` element i service manifest-filen används för att ange hur du vill starta tjänsten. Den `ExeHost` element anger den körbara filen (och argument) som ska användas för att starta tjänsten.

* `Program` Anger namnet på den körbara filen som ska starta tjänsten.
* `Arguments` Anger de argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
* `WorkingFolder` Anger arbetskatalog för processen som ska startas. Du kan ange tre värden:
  * `CodeBase` Anger att arbetskatalogen ska anges till katalogen koden i programpaketet (`Code` katalogen i föregående filstruktur).
  * `CodePackage` Anger att arbetskatalogen ska anges till roten i programpaketet (`GuestService1Pkg` i föregående filstruktur).
    * `Work` Anger att filerna placeras i en underkatalog med namnet arbete.

WorkingFolder är användbar för att ange rätt arbetskatalogen så att relativa sökvägar kan användas av program-eller initiering.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Uppdatera slutpunkter och registrera med namngivningstjänst för kommunikation
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
I föregående exempel är den `Endpoint` element anger de slutpunkter som programmet kan lyssna på. I det här exemplet Node.js-program som lyssnar på http på 3000-port.

Dessutom kan du be Service Fabric att publicera den här slutpunkten till Naming Service så att andra tjänster kan identifiera slutpunktsadress till den här tjänsten. På så sätt kan du kommunicera mellan tjänster som gäst körbara filer.
Publicerade slutpunktsadressen har formatet `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` och `PathSuffix` är valfria attribut. `IPAddressOrFQDN` är IP-adress eller fullständigt kvalificerade domännamnet för den körbara filen hämtar placeras på noden och beräknas åt dig.

I följande exempel, när tjänsten har distribuerats, Service Fabric Explorer visas i en slutpunkt som liknar `http://10.1.4.92:3000/myapp/` publicerats för tjänstinstansen. Eller om det är en lokal dator kan du se `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Du kan använda dessa adresser med [omvänd proxy](service-fabric-reverseproxy.md) för kommunikation mellan tjänster.

### <a name="edit-the-application-manifest-file"></a>Redigera programmanifestfilen
När du har konfigurerat den `Servicemanifest.xml` -fil, måste du göra vissa ändringar i den `ApplicationManifest.xml` filen så att rätt typ och namn används.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
I den `ServiceManifestImport` element, kan du ange en eller flera tjänster som du vill ska ingå i appen. Tjänster refereras med `ServiceManifestName`, som anger namnet på katalogen där den `ServiceManifest.xml` filen finns.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurera loggning
Gästen körbara filer är det praktiskt att kunna se loggarna för konsolen ta reda på om program- och skript visar eventuella fel.
Omdirigering av konsol kan konfigureras i den `ServiceManifest.xml` fil med hjälp av `ConsoleRedirection` element.

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som distribuerats i produktionsmiljön eftersom detta kan påverka program för växling vid fel. *Endast* använda detta för lokal utveckling och felsökning.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` kan användas för att dirigera konsolens utdata (stdout och stderr) till en arbetskatalog. Detta ger dig möjlighet att kontrollera att det inte finns några fel vid installation eller körning av program i Service Fabric-klustret.

`FileRetentionCount` Anger hur många filer sparas i arbetskatalogen. Värdet 5, till exempel innebär att du loggfilerna för föregående fem körningar lagras i arbetskatalogen.

`FileMaxSizeInKb` Anger den maximala storleken för loggfiler.

Loggfilerna sparas i en av tjänstens fungerande kataloger. Att fastställa där filerna finns, Service Fabric Explorer för att bestämma vilken nod som tjänsten körs på och vilka arbetskatalogen används. Den här processen beskrivs senare i den här artikeln.

## <a name="deployment"></a>Distribution
Det sista steget är att [distribuera programmet](service-fabric-deploy-remove-applications.md). Följande PowerShell-skript visar hur du distribuerar ditt program i klustret för lokal utveckling och starta en ny Service Fabric-tjänst.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till image store om paketet är stora eller många filer. Läs mer [här](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Ett Service Fabric-tjänsten kan distribueras i olika ”konfigurationer”. Till exempel den kan distribueras som en eller flera instanser eller mallen kan distribueras på ett sådant sätt att det finns en instans av tjänsten på varje nod i Service Fabric-klustret.

Den `InstanceCount` parameter för den `New-ServiceFabricService` cmdlet som används för att ange hur många instanser av tjänsten ska startas i Service Fabric-klustret. Du kan ange den `InstanceCount` värde, beroende på vilken typ av program som du distribuerar. De två vanligaste scenarierna är:

* `InstanceCount = "1"`. Endast en instans av tjänsten distribueras i det här fallet i klustret. Service Fabric scheduler avgör vilken nod som ska distribueras på tjänsten.
* `InstanceCount ="-1"`. I det här fallet distribueras en instans av tjänsten på varje nod i Service Fabric-klustret. Resultatet är att ha ett (och endast ett) instans av tjänsten för varje nod i klustret.

Detta är en användbar konfiguration för frontend-program (till exempel en REST-slutpunkt), eftersom klientprogram behöver ”ansluta” till någon av noderna i klustret för att använda slutpunkt. Den här konfigurationen kan också användas om till exempel alla noder i Service Fabric-klustret är anslutna till en belastningsutjämnare. Klienttrafik kan sedan distribueras över den tjänst som körs på alla noder i klustret.

## <a name="check-your-running-application"></a>Kontrollera att programmet körs
Service Fabric Explorer för att identifiera den nod där tjänsten körs. I det här exemplet körs den på Nod1:

![Noden som tjänsten körs](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Om du navigerar du till noden och bläddra till programmet, kan du se grundläggande nod-information, inklusive dess plats på disken.

![Plats på disken](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Om du bläddrar till katalogen med hjälp av Server Explorer hittar du arbetskatalogen och tjänstens loggmappen, som visas i följande skärmbild: 

![Plats för logg](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du paketerar ett gäst körbart program och distribuerar den till Service Fabric. Se följande artiklar för relaterad information och uppgifter.

* [Exempel för förpackning och distribution av en gäst körbar fil](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), inklusive en länk till förhandsutgåvan av verktyget paketering
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via Naming service med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
* [Skapa ditt första Service Fabric-program med Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
