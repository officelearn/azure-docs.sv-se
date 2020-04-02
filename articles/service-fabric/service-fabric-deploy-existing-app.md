---
title: Distribuera en befintlig körbar till Azure Service Fabric
description: Lär dig hur du paketerar ett befintligt program som en körbar gäst, så att det kan distribueras till ett Service Fabric-kluster.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c6c6bc0369593c177b74261da1fd8c15dd73fcb3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520487"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Paketera och distribuera en befintlig körbar till Service Fabric

När du paketerar en befintlig körbar [körbar](service-fabric-guest-executables-introduction.md)fil som gäst kan du välja att antingen använda en Visual Studio-projektmall eller [att skapa programpaketet manuellt](#manually). Med Visual Studio skapas programpaketstrukturen och manifestfilerna av den nya projektmallen åt dig.

> [!TIP]
> Det enklaste sättet att paketera en befintlig Windows körbar till en tjänst är att använda Visual Studio och på Linux för att använda Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Använda Visual Studio för att paketera och distribuera en befintlig körbar

Visual Studio tillhandahåller en servicemall för Service Fabric som hjälper dig att distribuera en körbar gäst till ett Service Fabric-kluster.

1. Välj **Arkiv** > **nytt projekt**och skapa ett Service Fabric-program.
2. Välj **Gäst körbar** som tjänstmall.
3. Klicka på **Bläddra** för att markera mappen med din körbara och fyll i resten av parametrarna för att skapa tjänsten.
   * *Kodpaketbeteende*. Kan ställas in för att kopiera allt innehåll i mappen till Visual Studio Project, vilket är användbart om den körbara filen inte ändras. Om du förväntar dig att den körbara att ändra och vill ha möjlighet att plocka upp nya bygger dynamiskt, kan du välja att länka till mappen istället. Du kan använda länkade mappar när du skapar programprojektet i Visual Studio. Detta länkar till källplatsen inifrån projektet, vilket gör det möjligt för dig att uppdatera gästens körbara i dess källmål. Dessa uppdateringar blir en del av programpaketet på build.
   * *Programmet* anger den körbara fil som ska köras för att starta tjänsten.
   * *Argument* anger de argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
   * *WorkingFolder* anger arbetskatalogen för den process som ska startas. Du kan ange tre värden:
     * `CodeBase`anger att arbetskatalogen ska ställas in på kodkatalogen`Code` i programpaketet (katalogen som visas i den föregående filstrukturen).
     * `CodePackage`anger att arbetskatalogen ska ställas in på roten`GuestService1Pkg` till programpaketet (som visas i den föregående filstrukturen).
     * `Work`anger att filerna placeras i ett underkatalogankallat arbete.
4. Namnge tjänsten och klicka på **OK**.
5. Om din tjänst behöver en slutpunkt för kommunikation kan du nu lägga till protokollet, porten och typen i filen ServiceManifest.xml. Till exempel: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Du kan nu använda paketet och publicera åtgärder mot ditt lokala kluster genom att felsöka lösningen i Visual Studio. När du är klar kan du publicera programmet i ett fjärrkluster eller checka in lösningen för källkontroll.
7. Läs [kontrollera ditt program som körs](#check-your-running-application) för att se hur du visar din körbara gästtjänst som körs i Service Fabric Explorer.

En exempelgenomgång finns i [Skapa ditt första körbara gästprogram med Visual Studio](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Paketera flera körbara filer med Visual Studio

Du kan använda Visual Studio för att skapa ett programpaket som innehåller flera körbara gästar. När du har lagt till den första körbara gästen högerklickar du på programprojektet och väljer **tjänsten Add->New Service Fabric** för att lägga till det andra körbara gästprojektet i lösningen.

> [!NOTE]
> Om du väljer att länka källan i Visual Studio-projektet, bygga Visual Studio-lösningen, kommer att se till att ditt programpaket är uppdaterat med ändringar i källan.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Använd Yeoman för att paketera och distribuera en befintlig körbar på Linux

Proceduren för att skapa och distribuera en gäst körbar på Linux är samma sak som att distribuera en csharp eller java-program.

1. I en terminal, skriver du in `yo azuresfguest`.
2. Namnge ditt program.
3. Namnge tjänsten och ange information, inklusive sökvägen för den körbara filen och de parametrar som den måste anropas med.

Yeoman skapar ett programpaket med lämpliga program- och manifestfiler tillsammans med installations- och avinstallationsskript.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Paketera flera körbara filer med Yeoman på Linux

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg:

1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfguest:AddService` och ge nödvändiga detaljer.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Paketera och distribuera en befintlig körbar

Processen att manuellt paketera en gäst körbar baseras på följande allmänna steg:

1. Skapa paketkatalogstrukturen.
2. Lägg till programmets kod- och konfigurationsfiler.
3. Redigera tjänstmanifestfilen.
4. Redigera programmanifestfilen.

### <a name="create-the-package-directory-structure"></a>Skapa paketkatalogstrukturen

Du kan börja med att skapa katalogstrukturen enligt beskrivningen i [Paketera en Azure Service Fabric App](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Lägga till programmets kod- och konfigurationsfiler

När du har skapat katalogstrukturen kan du lägga till programmets kod- och konfigurationsfiler under koden och konfigurationskatalogerna. Du kan också skapa ytterligare kataloger eller underkataloger under koden eller konfigurationskataloger.

Service Fabric `xcopy` gör ett av innehållet i programmets rotkatalog, så det finns ingen fördefinierad struktur att använda annat än att skapa två toppkataloger, kod och inställningar. (Du kan välja olika namn om du vill. Mer information finns i nästa avsnitt.)

> [!NOTE]
> Se till att du inkluderar alla filer och beroenden som programmet behöver. Service Fabric kopierar innehållet i programpaketet på alla noder i klustret där programmets tjänster ska distribueras. Paketet ska innehålla all kod som programmet behöver köra. Anta inte att beroendena redan är installerade.
>
>

### <a name="edit-the-service-manifest-file"></a>Redigera tjänstmanifestfilen

Nästa steg är att redigera tjänstmanifestfilen så att den innehåller följande information:

* Namnet på tjänsttypen. Detta är ett ID som Service Fabric använder för att identifiera en tjänst.
* Kommandot som ska användas för att starta programmet (ExeHost).
* Alla skript som måste köras för att konfigurera programmet (SetupEntrypoint).

Följande är ett exempel `ServiceManifest.xml` på en fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

Följande avsnitt går igenom de olika delar av filen som du behöver uppdatera.

#### <a name="update-servicetypes"></a>Uppdatera ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Du kan välja vilket namn `ServiceTypeName`du vill ha för . Värdet används i `ApplicationManifest.xml` filen för att identifiera tjänsten.
* Ange `UseImplicitHost="true"`. Det här attributet talar om för Service Fabric att tjänsten är baserad på en fristående app, så allt Service Fabric behöver göra är att starta den som en process och övervaka dess hälsa.

#### <a name="update-codepackage"></a>Uppdatera CodePackage
Elementet CodePackage anger platsen (och versionen) för tjänstens kod.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Elementet `Name` används för att ange namnet på katalogen i programpaketet som innehåller tjänstens kod. `CodePackage`har också `version` attributet. Detta kan användas för att ange vilken version av koden som kan användas och kan även eventuellt användas för att uppgradera tjänstens kod med hjälp av programmets livscykelhanteringsinfrastruktur i Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Valfritt: Uppdatera SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Elementet SetupEntryPoint används för att ange alla körbara filer eller kommandofiler som ska köras innan tjänstens kod startas. Det är ett valfritt steg, så det behöver inte inkluderas om det inte krävs någon initiering. SetupEntryPoint körs varje gång tjänsten startas om.

Det finns bara en SetupEntryPoint, så inställningsskript måste grupperas i en enda kommandofil om programmets inställningar kräver flera skript. SetupEntryPoint kan köra alla typer av filer: körbara filer, batchfiler och PowerShell-cmdletar. Mer information finns i [Konfigurera SetupEntryPoint](service-fabric-application-runas-security.md).

I föregående exempel kör SetupEntryPoint en kommandofil som anropas `LaunchConfig.cmd` i underkatalogen `scripts` till kodkatalogen (förutsatt att ActiveFolder-elementet är inställt på CodeBase).

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

Elementet `EntryPoint` i tjänstmanifestfilen används för att ange hur tjänsten ska startas.

Elementet `ExeHost` anger den körbara (och argument) som ska användas för att starta tjänsten. Du kan också `IsExternalExecutable="true"` lägga `ExeHost` till attributet för att ange att programmet är en extern körbar utanför kodpaketet. Till exempel `<ExeHost IsExternalExecutable="true">`.

* `Program`anger namnet på den körbara fil som ska starta tjänsten.
* `Arguments`anger de argument som ska skickas till den körbara filen. Det kan vara en lista över parametrar med argument.
* `WorkingFolder`anger arbetskatalogen för den process som ska startas. Du kan ange tre värden:
  * `CodeBase`anger att arbetskatalogen ska ställas in på kodkatalogen`Code` i programpaketet (katalogen i den föregående filstrukturen).
  * `CodePackage`anger att arbetskatalogen ska anges till roten för`GuestService1Pkg` programpaketet (i föregående filstruktur).
    * `Work`anger att filerna placeras i ett underkatalogankallat arbete.

WorkingFolder är användbart för att ställa in rätt arbetskatalog så att relativa sökvägar kan användas av antingen program- eller initieringsskripten.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Uppdatera slutpunkter och registrera dig hos namngivningstjänsten för kommunikation

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

I föregående exempel anger `Endpoint` elementet de slutpunkter som programmet kan lyssna på. I det här exemplet lyssnar nod.js-programmet på http på port 3000.

Dessutom kan du be Service Fabric att publicera den här slutpunkten till namngivningstjänsten så att andra tjänster kan identifiera slutpunktsadressen till den här tjänsten. Detta gör att du kan kommunicera mellan tjänster som är gäst körbara filer.
Den publicerade slutpunktsadressen `UriScheme://IPAddressOrFQDN:Port/PathSuffix`är av formuläret . `UriScheme`och `PathSuffix` är valfria attribut. `IPAddressOrFQDN`är IP-adressen eller fullständigt kvalificerat domännamn för noden som den här körbara körbara filen placeras på och den beräknas för dig.

I följande exempel, när tjänsten har distribuerats, i Service `http://10.1.4.92:3000/myapp/` Fabric Explorer visas en slutpunkt som liknar publicerad för tjänstinstansen. Eller om detta är en `http://localhost:3000/myapp/`lokal maskin, ser du .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Du kan använda dessa adresser med [omvänd proxy](service-fabric-reverseproxy.md) för att kommunicera mellan tjänster.

### <a name="edit-the-application-manifest-file"></a>Redigera programmanifestfilen

När du har `Servicemanifest.xml` konfigurerat filen måste du göra `ApplicationManifest.xml` vissa ändringar i filen för att säkerställa att rätt tjänsttyp och namn används.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

I `ServiceManifestImport` elementet kan du ange en eller flera tjänster som du vill inkludera i appen. Tjänster refereras `ServiceManifestName`med , som anger namnet på `ServiceManifest.xml` katalogen där filen finns.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Konfigurera loggning

För gästkörningsbara filer är det bra att kunna se konsolloggar för att ta reda på om program- och konfigurationsskripten visar några fel.
Omdirigering av konsolen `ServiceManifest.xml` kan `ConsoleRedirection` konfigureras i filen med hjälp av elementet.

> [!WARNING]
> Använd aldrig principen om omdirigering av konsolen i ett program som distribueras i produktion eftersom detta kan påverka programmets redundans. *Använd* endast detta för lokal utveckling och felsökning.  
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

`ConsoleRedirection`kan användas för att omdirigera konsolutdata (både stdout och stderr) till en arbetskatalog. Detta ger möjlighet att verifiera att det inte finns några fel under installationen eller körningen av programmet i service fabric-klustret.

`FileRetentionCount`avgör hur många filer som sparas i arbetskatalogen. Värdet 5 innebär till exempel att loggfilerna för de tidigare fem körningarna lagras i arbetskatalogen.

`FileMaxSizeInKb`anger loggfilernas maximala storlek.

Loggfiler sparas i en av tjänstens arbetskataloger. Om du vill ta reda på var filerna finns använder du Service Fabric Explorer för att avgöra vilken nod tjänsten körs på och vilken arbetskatalog som används. Denna process beskrivs senare i den här artikeln.

## <a name="deployment"></a>Distribution

Det sista steget är att [distribuera ditt program](service-fabric-deploy-remove-applications.md). Följande PowerShell-skript visar hur du distribuerar ditt program till det lokala utvecklingsklustret och startar en ny Service Fabric-tjänst.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Komprimera paketet](service-fabric-package-apps.md#compress-a-package) innan du kopierar till bildarkivet om paketet är stort eller har många filer. Läs mer [här](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

En Service Fabric-tjänst kan distribueras i olika "konfigurationer". Den kan till exempel distribueras som en eller flera instanser, eller så kan den distribueras på ett sådant sätt att det finns en instans av tjänsten på varje nod i Service Fabric-klustret.

Parametern `InstanceCount` för `New-ServiceFabricService` cmdlet används för att ange hur många instanser av tjänsten som ska startas i service fabric-klustret. Du kan `InstanceCount` ange värdet, beroende på vilken typ av program som du distribuerar. De två vanligaste scenarierna är:

* `InstanceCount = "1"`. I det här fallet distribueras endast en instans av tjänsten i klustret. Service Fabric's scheduler bestämmer vilken nod tjänsten kommer att distribueras på.
* `InstanceCount ="-1"`. I det här fallet distribueras en instans av tjänsten på varje nod i Service Fabric-klustret. Resultatet är att ha en (och endast en) instans av tjänsten för varje nod i klustret.

Detta är en användbar konfiguration för frontend-program (till exempel en REST-slutpunkt), eftersom klientprogram måste "ansluta" till någon av noderna i klustret för att använda slutpunkten. Den här konfigurationen kan också användas när till exempel alla noder i Service Fabric-klustret är anslutna till en belastningsutjämnare. Klienttrafik kan sedan distribueras över tjänsten som körs på alla noder i klustret.

## <a name="check-your-running-application"></a>Kontrollera ditt program som körs
Identifiera noden där tjänsten körs i Service Fabric Explorer. I det här exemplet körs den på Nod1:

![Nod där tjänsten körs](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Om du navigerar till noden och bläddrar till programmet visas den nödvändiga nodinformationen, inklusive dess plats på disken.

![Plats på disk](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Om du bläddrar till katalogen med hjälp av Server Explorer hittar du arbetskatalogen och tjänstens loggmapp, som visas i följande skärmbild:

![Loggens placering](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du paketerar en körbar gäst och distribuerar den till Service Fabric. Se följande artiklar för relaterad information och uppgifter.

* [Prov för att paketera och distribuera en körbar gäst,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)inklusive en länk till föruteasen av förpackningsverktyget
* [Exempel på två körbara gästar (C# och nodejs) som kommunicerar via namngivningstjänsten med REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Skapa ditt första Service Fabric-program med Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
