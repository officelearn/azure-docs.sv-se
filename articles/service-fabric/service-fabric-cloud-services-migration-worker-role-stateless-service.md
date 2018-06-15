---
title: Omvandla appar i Azure Cloud Services till mikrotjänster | Microsoft Docs
description: Den här guiden jämför Cloud Services webb- och arbetsroller- och Service Fabric tillståndslösa tjänster för att migrera från molntjänster till Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c6bdd6f88c9008a8d9c15d22bdcf263190424649
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206690"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Att konvertera webb- och arbetsroller till Service Fabric tillståndslösa tjänster
Den här artikeln beskriver hur du migrerar din Cloud Services webb- och arbetsroller till Service Fabric tillståndslösa tjänster. Detta är den enklaste migreringsvägen från molntjänster till Service Fabric för program som ska vara ungefär samma vars övergripande arkitektur.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Molntjänstprojekt till Service Fabric projektet
 Cloud Service-projekt och ett tjänstprogram för Fabric-projekt har en liknande struktur och båda representerar distributionsenhet för ditt program – det vill säga de definiera hela paketet distribueras för att köra programmet. En Cloud Service-projekt innehåller en eller flera rollerna webb eller arbetare. På liknande sätt kan innehåller ett tjänstprogram för Fabric-projekt en eller flera tjänster. 

Skillnaden är att Cloud Service-projekt kombinerar programdistribution med en distribution av Virtuella datorer och därmed innehåller inställningar för virtuell dator, Service Fabric programprojekt bara definierar ett program som ska distribueras till en uppsättning av befintliga virtuella datorer i ett Service Fabric-kluster. Själva Service Fabric-klustret distribueras bara en gång, antingen via en Resource Manager-mall eller Azure-portalen och flera Service Fabric-program kan distribueras till den.

![Jämförelse mellan Service Fabric och Cloud Services-projekt][3]

## <a name="worker-role-to-stateless-service"></a>Worker-rollen till tillståndslösa tjänsten
En Arbetsroll representerar begreppsmässigt en tillståndslös arbetsbelastning, vilket innebär att varje förekomst av arbetsbelastningen är identiska och begäranden kan vidarebefordras till en valfri instans när som helst. Varje instans förväntas inte att komma ihåg föregående begäran. Tillstånd som arbetsbelastningen som körs på hanteras av en extern tillståndslager, till exempel Azure Table Storage eller Azure-dokumentet DB. Den här typen av arbetsbelastning representeras av en tjänst för tillståndslösa i Service Fabric. Det enklaste sättet att migrera en Arbetsroll till Service Fabric kan göras genom att konvertera Arbetsrollen koden till en tillståndslös tjänst.

![Worker-rollen till tillståndslösa tjänsten][4]

## <a name="web-role-to-stateless-service"></a>Webbrollen till tillståndslösa tjänsten
Liknar Arbetsrollen, en Webbroll också representerar en tillståndslös arbetsbelastning och så begreppsmässigt de för kan mappas till en tillståndslös Service Fabric-tjänsten. Men till skillnad från webbroller stöder Service Fabric inte IIS. Om du vill migrera en webbplats kräver program från en Webbroll till en tillståndslösa tjänsten först flytta till en webbramverk som kan finnas själv och är inte beroende av IIS eller System.Web, till exempel ASP.NET Core 1.

| **Programmet** | **Stöds** | **Sökväg för migrering** |
| --- | --- | --- |
| ASP.NET Web Forms |Nej |Omvandla till ASP.NET Core 1 MVC |
| ASP.NET MVC |Med migrering |Uppgradera till ASP.NET Core 1 MVC |
| ASP.NET Webb-API |Med migrering |Använda automatisk värdbaserade servern eller ASP.NET Core 1 |
| ASP.NET Core 1 |Ja |Gäller inte |

## <a name="entry-point-api-and-lifecycle"></a>Posten punkt API och livscykel
Worker-rollen och Service Fabric-API: er erbjuder liknande startpunkter: 

| **Startpunkt** | **Worker-rollen** | **Service Fabric-tjänsten** |
| --- | --- | --- |
| Bearbetning |`Run()` |`RunAsync()` |
| Starta VM |`OnStart()` |Gäller inte |
| Stoppa VM |`OnStop()` |Gäller inte |
| Öppna lyssnaren för klientbegäranden |Gäller inte |<ul><li> `CreateServiceInstanceListener()` för tillståndslösa</li><li>`CreateServiceReplicaListener()` för stateful</li></ul> |

### <a name="worker-role"></a>Worker-rollen
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Service Fabric tillståndslösa tjänsten
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Båda har en primär ”kör” åsidosättning att påbörja behandlingen. Service Fabric services kombinera `Run`, `Start`, och `Stop` till en enda kontaktpunkt `RunAsync`. Din tjänst ska börja arbeta när `RunAsync` startar och ska upphöra att fungera när den `RunAsync` metodens CancellationToken signaleras. 

Det finns flera viktiga skillnader mellan livscykel och livslängden för arbetsroller och Service Fabric-tjänster:

* **Livscykel:** den största skillnaden är att en Arbetsroll är en virtuell dator så livscykeln är knutna till den virtuella datorn, vilket innefattar händelser för när den virtuella datorn startar och stoppar. En Service Fabric-tjänst har en livscykel som skiljer sig från VM-livscykel, så att den inte innehåller händelser för när värden VM eller datorn startar och stoppar, eftersom de inte är relaterade.
* **Livslängd:** Arbetsrollen-instansen kommer att återanvändas om den `Run` metoden avslutar. Den `RunAsync` metod i en tjänst för Service Fabric men kan köra kan slutföras och tjänstinstansen ska fortsätta. 

Service Fabric ger en startpunkt för valfri kommunikation installationsprogrammet för tjänster som lyssnar efter förfrågningar från klienter. Startpunkten för både RunAsync och kommunikation är valfria åsidosättningar i Service Fabric-services - tjänsten kan välja att bara lyssna på förfrågningar från klienter eller endast köra en loop i bearbetning eller båda - vilket är anledningen till RunAsync-metoden tillåts att avsluta utan att starta om tjänstinstansen eftersom den kan fortsätta att lyssna efter begäranden från klienter.

## <a name="application-api-and-environment"></a>Program-API och miljö
Molntjänster miljön API innehåller information och funktioner för den aktuella VM-instans, samt information om andra rollinstanser för VM. Service Fabric innehåller information som rör dess runtime och viss information om noden en tjänst körs på. 

| **Miljö aktivitet** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Konfigurationsinställningar och ändringsmeddelande |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokal lagring |`RoleEnvironment` |`CodePackageActivationContext` |
| Slutpunkten |`RoleInstance` <ul><li>Aktuell instans: `RoleEnvironment.CurrentRoleInstance`</li><li>Andra roller och instans: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` för den aktuella noden adressen</li><li>`FabricClient` och `ServicePartitionResolver` för identifiering av tjänst slutpunkt</li> |
| Miljö emulering |`RoleEnvironment.IsEmulated` |Gäller inte |
| Samtidiga Ändringshändelse |`RoleEnvironment` |Gäller inte |

## <a name="configuration-settings"></a>Konfigurationsinställningar
Konfigurationsinställningarna i molntjänster har angetts för en VM-roll och gäller för alla instanser av den Virtuella datorrollen. De här inställningarna är nyckel / värde-par i ServiceConfiguration.*.cscfg filer och kan nås direkt via RoleEnvironment. I Service Fabric inställningarna individuellt för varje tjänst och varje program i stället för en virtuell dator eftersom en virtuell dator kan vara värd för flera tjänster och program. En tjänst består av tre paket:

* **Kod:** innehåller tjänsten körbara filer, binärfiler, DLL-filer och andra filer som en tjänst måste köras.
* **Config:** alla konfigurationsfiler och inställningarna för en tjänst.
* **Data:** Statiska filer som är associerad med tjänsten.

Var och en av dessa paket kan vara oberoende versionsnummer och uppgraderade. Liknar Cloud Services, en konfigurationspaketet kan nås via programmering genom en API och händelser som är tillgängliga att meddela tjänsten av en ändring för config-paketet. En Settings.xml-fil kan användas för nyckel / värde-konfiguration och Programmeringsåtkomst liknar avsnittet app inställningar i en App.config-fil. Men till skillnad från molntjänster, kan ett Service Fabric config-paket innehålla konfigurationsfiler i valfritt format, oavsett om det är XML, JSON, YAML eller ett anpassat binärt format. 

### <a name="accessing-configuration"></a>Åtkomst till konfiguration
#### <a name="cloud-services"></a>Cloud Services
Konfigurationsinställningar från ServiceConfiguration.*.cscfg kan nås via `RoleEnvironment`. De här inställningarna är globalt tillgängliga för alla rollinstanser i samma molntjänst-distribution.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Varje tjänst har sin egen enskilda konfigurationspaket. Det finns ingen inbyggd mekanism för globala inställningar nås av alla program i ett kluster. När du använder Service Fabric särskilda Settings.xml konfigurationsfilen inom ett konfigurationspaket kan värden i Settings.xml åsidosättas på programnivå, gör det möjligt på programnivå konfigurationsinställningar.

Konfigurationsinställningarna är öppningar i varje tjänstinstans via tjänstens `CodePackageActivationContext`.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Update-konfigurationshändelser
#### <a name="cloud-services"></a>Cloud Services
Den `RoleEnvironment.Changed` händelsen används för att meddela alla rollinstanser när en ändring inträffar i miljön, till exempel en konfigurationsändring. Detta används för att använda konfigurationsuppdateringar utan återvinning rollinstanser eller omstart av en arbetsprocess.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Var och en av tre pakettyper i en tjänst - kod, konfiguration och Data - ha händelser som meddelar en tjänstinstans när ett paket uppdateras, lägga till eller tas bort. En tjänst kan innehålla flera paket för varje typ. En tjänst kan till exempel ha flera config paket, varje enskilt version och kan uppgraderas. 

Dessa händelser är tillgängliga att använda ändringar i servicepaket utan att starta om tjänstinstansen.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Start-uppgifter
Start-aktiviteter är åtgärder som vidtas innan ett program startas. En startåtgärd normalt används för att köra installationsprogrammet skript med utökade privilegier. Både molntjänster och Service Fabric stöder uppstart uppgifter. Den största skillnaden är att i Cloud Services, en startaktivitet är knuten till en virtuell dator eftersom det är en del av en rollinstans medan i Service Fabric en startaktivitet är knuten till en tjänst som inte är kopplad till någon särskild virtuell dator.

| Service Fabric | Cloud Services |
| --- | --- | --- |
| Platsen för konfigurationen |ServiceDefinition.csdef |
| Behörigheter |”begränsad” eller ”utökade” |
| Sekvensering |”enkla”, ”bakgrund”, ”förgrunden” |

### <a name="cloud-services"></a>Cloud Services
I Cloud Services konfigureras en startpunkt för start per roll i ServiceDefinition.csdef. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
I Service Fabric konfigureras en startpunkt för start per tjänst i ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>En anteckning om utvecklingsmiljö
Både molntjänster och Service Fabric är integrerade med Visual Studio med projektmallar och stöd för felsökning, konfigurera och distribuera både lokalt och till Azure. Både molntjänster och Service Fabric ger också en körningsmiljö för lokal utveckling. Skillnaden är att när Molntjänsten development runtime emulerar Azure-miljön där den körs, Service Fabric inte använder en emulator - fullständig Service Fabric runtime används. Service Fabric-miljö som du kör på utvecklingsdatorn lokala är i samma miljö som körs i produktion.

## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric Reliable Services och grundläggande skillnaderna mellan molntjänster och arkitektur för Service Fabric-programmet för att förstå hur du dra nytta av hela uppsättningen av Service Fabric-funktioner.

* [Komma igång med Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Konceptuell guide till skillnaderna mellan molntjänster och Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
