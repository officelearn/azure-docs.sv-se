---
title: Distribuera en befintlig körbar fil till Azure Service Fabric | Microsoft Docs
description: Lär dig hur du paketerar ett befintligt program som en körbara gästfiler, så att du kan distribuera till ett Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: aljo
ms.openlocfilehash: 2035ee27aecdf2ec1ab8bf1166f667b5595a7cbb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439493"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Paketera och distribuera en befintlig körbar fil till Service Fabric
När paketering en befintlig körbar fil som en [gästfil](service-fabric-guest-executables-introduction.md), kan du välja att använda en projektmall för Visual Studio eller [skapa programpaketet manuellt](#manually). Med Visual Studio skapas application package struktur och manifestfiler med den nya projektmallen för åt dig.

> [!TIP]
> Det enklaste sättet att paketera en befintlig Windows körbara i en tjänst är att använda Visual Studio och på Linux för att använda Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Använda Visual Studio för att paketera och distribuera en befintlig körbar fil
Visual Studio har en Service Fabric-tjänstmall som hjälper dig att distribuera en körbar Gäst till ett Service Fabric-kluster.

1. Välj **filen** > **nytt projekt**, och skapa ett Service Fabric-program.
2. Välj **körbar gäst** som tjänstmallen.
3. Klicka på **Bläddra** att välja mappen med den körbara filen och Fyll i resten av parametrarna för att skapa tjänsten.
   * *Code paketet beteende*. Kan ställas in för att kopiera allt innehåll i mappen i Visual Studio-projektet, vilket är användbart om den körbara filen inte ändras. Om du tror att den körbara filen för att ändra och vill kunna hämta nya versioner dynamiskt kan välja du att länka till mappen i stället. Du kan använda länkade mappar när du skapar programprojektet i Visual Studio. Den här länkar till källplatsen från i projektet, vilket gör det möjligt för dig att uppdatera den körbara gästfiler i dess källa-mål. Dessa uppdateringar bli en del av programpaketet version.
   * *Programmet* anger den körbara filen som ska köras för att starta tjänsten.
   * *Argument* anger argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
   * *WorkingFolder* anger arbetskatalogen för den process som kommer att startas. Du kan ange tre värden:
     * `CodeBase` Anger att arbetskatalogen kommer att vara satt till katalogen kod i programpaketet (`Code` katalogen som visas i den föregående filstrukturen).
     * `CodePackage` Anger att arbetskatalogen kommer att vara satt till roten för programpaketet (`GuestService1Pkg` visas i den föregående filstrukturen).
     * `Work` Anger att filerna är placerade i en underkatalog med namnet arbete.
4. Namnge tjänsten och klicka på **OK**.
5. Om tjänsten behöver en slutpunkt för kommunikation, du kan nu lägga till protokollet, porten och typen i filen servicemanifest.XML. Till exempel: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Du kan nu använda paketet och publicera åtgärd mot ditt lokala kluster genom att lösningen i Visual Studio-felsökning. När du är klar kan du publicera programmet till ett fjärrkluster eller checka in lösningen till källkontroll.
7. Läs [Kontrollera programmet som körs](#check-your-running-application) att se hur du visar dina körbar gästtjänst som körs i Service Fabric Explorer.

En exempel-genomgång finns i [skapa din första körbart gästprogram med Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Använda Yeoman för att paketet och distribuera en befintlig körbar fil på Linux

Proceduren för att skapa och distribuera gäst körbara på Linux är samma som distribuerar ett c# eller java-program.

1. I en terminal, skriver du in `yo azuresfguest`.
2. Namnge ditt program.
3. Namnge din tjänst och ger information inklusive sökvägen till den körbara filen och parametrarna som det måste anropas med.

Yeoman skapar ett programpaket med det aktuella programmet och manifestfiler tillsammans med installera och avinstallera skript.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Paketera och distribuera en befintlig körbar fil manuellt
Manuellt Paketera en körbar gäst baseras på dessa allmänna steg:

1. Skapa paket-katalogstruktur.
2. Lägg till programmets kod- och konfigurationsfiler.
3. Redigera tjänstmanifestfilen.
4. Redigera program-manifestfilen.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Skapa katalogstrukturen paket
Du kan börja med att skapa katalogstrukturen, enligt beskrivningen i [Paketera en Azure Service Fabric-App](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Lägg till programmets kod- och konfigurationsfiler
När du har skapat katalogstrukturen kan du lägga till programkoden och konfigurationsfiler under koden och konfigurationsversionerna kataloger. Du kan också skapa ytterligare kataloger eller underkataloger på kod eller konfigurationsversion kataloger.

Service Fabric har ett `xcopy` av innehållet i programmets rotkatalog, så det finns inga fördefinierade struktur att använda andra än att skapa två översta kataloger, kod och inställningar. (Du kan välja olika namn om du vill. Mer information finns i nästa avsnitt.)

> [!NOTE]
> Kontrollera att du inkluderar alla filer och beroenden som programmet behöver. Service Fabric kopierar innehållet i programpaketet på alla noder i klustret där programmets tjänster kommer att distribueras. Paketet innehåller den kod som programmet ska köras. Förutsätter inte att beroenden som redan är installerade.
>
>

### <a name="edit-the-service-manifest-file"></a>Redigera tjänstmanifestfilen
Nästa steg är att redigera tjänstmanifestfilen för att inkludera följande information:

* Namnet på tjänsttypen. Det här är ett ID som Service Fabric använder för att identifiera en tjänst.
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

I följande avsnitt gå igenom de olika delarna i den fil som du behöver uppdatera.

#### <a name="update-servicetypes"></a>Uppdatera ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Du kan välja vilket namn som du vill använda för `ServiceTypeName`. Värdet används i den `ApplicationManifest.xml` fil för att identifiera tjänsten.
* Ange `UseImplicitHost="true"`. Det här attributet anger Service Fabric att tjänsten är baserat på en självständig app så att alla Service Fabric behöver göra är att starta den som en process och övervaka dess hälsa.

#### <a name="update-codepackage"></a>Uppdatera CodePackage
Elementet CodePackage anger tjänstens kod plats (och version).

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Den `Name` elementet används för att ange namnet på katalogen i programpaket som innehåller tjänstens kod. `CodePackage` har också den `version` attribut. Detta kan användas för att ange versionen av koden och också potentiellt kan användas för att uppgradera tjänstens kod med hjälp av application lifecycle management infrastruktur i Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Valfritt: Uppdatera SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint-elementet används för att ange en körbar fil eller en batch-fil som ska köras innan tjänstens kod startas. Det är ett valfritt steg så inte behöver inkluderas om det finns inga initiering som krävs. SetupEntryPoint körs varje gång tjänsten startas.

Det finns bara en SetupEntryPoint så installationsskripten behöver grupperas i en enskild batch-fil om programmets installationen kräver flera skript. SetupEntryPoint kan köra alla typer av filer: körbara filer, kommandofiler och PowerShell-cmdletar. Mer information finns i [konfigurera SetupEntryPoint](service-fabric-application-runas-security.md).

I föregående exempel SetupEntryPoint körs en batchfil som heter `LaunchConfig.cmd` som finns i den `scripts` underkatalog i katalogen kod (förutsatt att WorkingFolder-elementet är inställt på kodbas).

#### <a name="update-entrypoint"></a>Update EntryPoint
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Den `EntryPoint` element i tjänstmanifestfilen används för att ange hur du vill starta tjänsten.

Den `ExeHost` elementet anger den körbara filen (och argument) som ska användas för att starta tjänsten. Du kan du lägga till den `IsExternalExecutable="true"` attributet `ExeHost` som indikerar att programmet är en extern körbar fil utanför kodpaketet. Till exempel `<ExeHost IsExternalExecutable="true">`.

* `Program` Anger namnet på den körbara filen som ska starta tjänsten.
* `Arguments` Anger argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
* `WorkingFolder` anger arbetskatalogen för den process som kommer att startas. Du kan ange tre värden:
  * `CodeBase` Anger att arbetskatalogen kommer att vara satt till katalogen kod i programpaketet (`Code` katalogen i den föregående filstrukturen).
  * `CodePackage` Anger att arbetskatalogen kommer att vara satt till roten för programpaketet (`GuestService1Pkg` i den föregående filstrukturen).
    * `Work` Anger att filerna är placerade i en underkatalog med namnet arbete.

WorkingFolder är bra att ange rätt arbetskatalogen så att programmet eller initieringen skript kan använda relativa sökvägar.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Uppdatera slutpunkter och registrera med Namngivningstjänsten för kommunikation
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
I föregående exempel, den `Endpoint` elementet anger de slutpunkter som programmet kan lyssna på. I det här exemplet Node.js-program som lyssnar på http på port 3000.

Dessutom kan du be Service Fabric för att publicera den här slutpunkten Naming-tjänsten så att andra tjänster kan identifiera slutpunktsadress till den här tjänsten. På så sätt kan du kommunicera mellan tjänster som är körbara gäster.
Den publicerade slutpunktsadressen är i formatet `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` och `PathSuffix` är valfria attribut. `IPAddressOrFQDN` är IP-adress eller fullständigt kvalificerade domännamnet för den här körbara filen hämtar placeras på noden och beräknas det åt dig.

I följande exempel, när tjänsten har distribuerats i Service Fabric Explorer visas en slutpunkt liknande `http://10.1.4.92:3000/myapp/` publicerade för tjänstinstansen. Eller om det är en lokal dator kan du se `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Du kan använda dessa adresser med [omvänd proxy](service-fabric-reverseproxy.md) för kommunikation mellan tjänster.

### <a name="edit-the-application-manifest-file"></a>Redigera program-manifestfilen
När du har konfigurerat den `Servicemanifest.xml` -fil som du behöver göra några ändringar i den `ApplicationManifest.xml` filen för att se till att rätt tjänsttyp och namn används.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
I den `ServiceManifestImport` element som du kan ange en eller flera tjänster som du vill ska ingå i appen. Refererar till tjänster med `ServiceManifestName`, som anger namnet på katalogen där den `ServiceManifest.xml` filen finns.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurera loggning
För gästfiler är det användbart för att kunna se loggar för konsolen att ta reda på om skripten program- och visar eventuella fel.
Omdirigering av konsol kan konfigureras i den `ServiceManifest.xml` fil med hjälp av den `ConsoleRedirection` element.

> [!WARNING]
> Använd aldrig omdirigeringspolicyn konsolen i ett program som har distribuerats i produktionsmiljön, eftersom detta kan påverka program redundans. *Endast* använda detta för lokal utveckling och felsökning.  
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

`ConsoleRedirection` kan användas för att omdirigera konsolens utdata (stdout och stderr) till en arbetskatalog. Detta ger möjlighet att verifiera att det inte finns några fel vid installation eller körning av programmet i Service Fabric-klustret.

`FileRetentionCount` Anger hur många filer har sparats i arbetskatalogen. Värdet 5, till exempel innebär att tidigare fem körningar i loggfilerna lagras i arbetskatalogen.

`FileMaxSizeInKb` Anger den maximala storleken för loggfiler.

Loggfilerna sparas i en av tjänstens fungerande kataloger. Om du vill fastställa där filerna finns använder Service Fabric Explorer för att avgöra vilken nod som tjänsten körs på och vilka arbetskatalog som används. Den här processen beskrivs senare i den här artikeln.

## <a name="deployment"></a>Distribution
Det sista steget är att [distribuera ditt program](service-fabric-deploy-remove-applications.md). Följande PowerShell-skript visar hur du distribuerar ditt program till det lokala utvecklingsklustret och starta en ny Service Fabric-tjänst.

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
> [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till avbildningsarkivet om paketet är stort eller har många filer. Läs mer [här](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

En Service Fabric-tjänst kan distribueras i olika ”konfigurationer”. Exempelvis kan den distribueras som en eller flera instanser eller den distribueras så att det finns en instans av tjänsten på varje nod i Service Fabric-klustret.

Den `InstanceCount` -parametern för den `New-ServiceFabricService` cmdlet för att ange hur många instanser av tjänsten ska startas i Service Fabric-klustret. Du kan ange den `InstanceCount` värde, beroende på vilken typ av program som du distribuerar. De två vanligaste scenarierna är:

* `InstanceCount = "1"`. I det här fallet distribueras bara en instans av tjänsten i klustret. Service Fabric scheduler avgör vilken nod som tjänsten ska distribueras på.
* `InstanceCount ="-1"`. I det här fallet distribueras en instans av tjänsten på varje nod i Service Fabric-klustret. Resultatet är att ha en (och enda) instans av tjänsten för varje nod i klustret.

Det här är en användbar konfiguration för frontend-program (till exempel en REST-slutpunkt), eftersom klientprogram behöver ”ansluta” till någon av noderna i klustret ska använda slutpunkten. Den här konfigurationen kan också användas när till exempel alla noder i Service Fabric-klustret är anslutna till en belastningsutjämnare. Klienttrafik kan sedan distribueras på den tjänst som körs på alla noder i klustret.

## <a name="check-your-running-application"></a>Kontrollera programmet som körs
Identifiera noden där tjänsten körs i Service Fabric Explorer. I det här exemplet används Nod1:

![Noden där tjänsten körs](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Om du navigera till noden och gå till programmet, se viktiga nodinformation, inklusive dess plats på disken.

![Plats på disken](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Om du bläddrar till katalogen med hjälp av Server Explorer, kan du hitta arbetskatalogen och tjänstens loggmappen, enligt följande skärmbild: 

![Plats för logg](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du paketera en körbar Gäst och distribuera den till Service Fabric. Se följande artiklar för relaterad information och uppgifter.

* [Exempel för att paketera och distribuera en körbar gäst](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), inklusive en länk till förhandsutgåvan av verktyget paketering
* [Exempel på två gäst körbara filer (C# och nodejs) kommunicerar via tjänsten namngivning med hjälp av REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
* [Skapa ditt första Service Fabric-program med Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
