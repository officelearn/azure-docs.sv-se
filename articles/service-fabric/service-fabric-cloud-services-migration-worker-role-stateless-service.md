---
title: Konvertera Azure Cloud Services-appar till Service Fabric | Microsoft Docs
description: Den här guiden jämför Cloud Services Web och Worker-roller och Service Fabric tillståndslösa tjänster för att migrera från Cloud Services till Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 5880ebb3-8b54-4be8-af4b-95a1bc082603
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 10fb44b0e76282ad78e7687beaa2e50e819e5cd9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667726"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guide för att konvertera webb- och Worker-roller till tillståndslös Service Fabric-tjänster
Den här artikeln beskriver hur du migrerar dina Cloud Services Web och Worker-roller till tillståndslös Service Fabric-tjänster. Det här är den enklaste migreringsvägen från Cloud Services till Service Fabric för program vars övergripande arkitekturen kommer att vara ungefär detsamma.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Molntjänstprojekt till projektet för Service Fabric
 En Cloud Service-projekt och ett Service Fabric-program-projekt har en liknande struktur och båda representerar distributionsenhet för ditt program – det vill säga de definierar det fullständiga paket som har distribuerats för att köra dina program. En Cloud Service-projektet innehåller en eller flera webb- eller Worker-roller. På samma sätt kan innehåller ett projekt för Service Fabric-program en eller flera tjänster. 

Skillnaden är att Cloud Service-projekt kombinerar programdistribution med en VM-distribution och därför innehåller konfigurationsinställningar för virtuella datorer, Service Fabric-program-projektet bara definierar ett program som ska distribueras till en uppsättning befintliga virtuella datorer i ett Service Fabric-kluster. Själva Service Fabric-klustret distribueras bara en gång, antingen via en Resource Manager-mall eller via Azure-portalen och flera Service Fabric-program kan distribueras till den.

![Jämförelse mellan Service Fabric och Cloud Services-projekt][3]

## <a name="worker-role-to-stateless-service"></a>Worker-roll till tillståndslös tjänst
Den övergripande representerar en Arbetsroll en tillståndslös arbetsbelastning, vilket innebär att varje instans av arbetsbelastning är identiska och kan begäranden dirigeras till en valfri instans när som helst. Varje instans förväntades inte att komma ihåg tidigare begäran. Tillstånd som arbetsbelastningen som körs på hanteras av en extern tillståndslager, till exempel Azure Table Storage eller Azure Document DB. I Service Fabric kan representeras den här typen av arbetsbelastning av en tillståndslös tjänst. Det enklaste sättet att migrera en Arbetsroll till Service Fabric kan göras genom att konvertera Arbetsroll kod till en tillståndslös tjänst.

![Worker-roll till tillståndslös tjänst][4]

## <a name="web-role-to-stateless-service"></a>Webbroll för tillståndslös tjänst
Liknar Worker-roll, en Webbroll också representerar en tillståndslös arbetsbelastning och så begreppsmässigt de för kan mappas till en tillståndslös Service Fabric-tjänst. Men till skillnad från Web-roller, har Service Fabric inte stöd för IIS. Om du vill migrera en webbplats kräver en Webbroll program till en tillståndslös tjänst först flytta till ett webbramverk som kan vara egenvärdbaserat och är inte beroende av IIS eller System.Web, till exempel ASP.NET Core-1.

| **Programmet** | **Stöds** | **Migreringsvägen** |
| --- | --- | --- |
| ASP.NET Web Forms |Nej |Konvertera till ASP.NET Core 1 MVC |
| ASP.NET MVC |Med migrering |Uppgradera till ASP.NET Core 1 MVC |
| ASP.NET Web API |Med migrering |Använda lokal server eller ASP.NET Core 1 |
| ASP.NET Core 1 |Ja |Gäller inte |

## <a name="entry-point-api-and-lifecycle"></a>Posten punkt API och livscykelhantering
Worker-roll och Service Fabric-tjänsten för liknande startpunkter för API: er för erbjudande: 

| **Startpunkt** | **Worker-roll** | **Service Fabric-tjänst** |
| --- | --- | --- |
| Bearbetar |`Run()` |`RunAsync()` |
| VM start |`OnStart()` |Gäller inte |
| VM stop |`OnStop()` |Gäller inte |
| Öppna lyssnaren för klientbegäranden |Gäller inte |<ul><li> `CreateServiceInstanceListener()` för tillståndslösa</li><li>`CreateServiceReplicaListener()` för tillståndskänslig</li></ul> |

### <a name="worker-role"></a>Worker-roll
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

### <a name="service-fabric-stateless-service"></a>Service Fabric Stateless Service
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

Båda har en primär ”kör” åsidosättning där du vill börja bearbetning. Service Fabric-tjänster kombinera `Run`, `Start`, och `Stop` i en enda kontaktpunkt `RunAsync`. Din tjänst ska börja arbeta när `RunAsync` startas och bör slutar fungera när den `RunAsync` metodens CancellationToken signaleras. 

Det finns flera viktiga skillnader mellan livscykel och livslängden för Worker-roller och Service Fabric-tjänster:

* **Livscykel:** Den största skillnaden är att en Arbetsroll är en virtuell dator och så livscykeln är kopplad till den virtuella datorn, vilket innefattar händelser för när den virtuella datorn startar och stoppar. En Service Fabric-tjänst har en livscykel som är separat från VM-livscykeln, så att den inte omfattar händelser för när värden VM eller datorn startar och stoppar, eftersom de inte är relaterade.
* **Livstid:** En Worker-rollinstans kommer Papperskorgen om den `Run` metoden avslutas. Den `RunAsync` -metod i en Service Fabric-tjänst men kan köra slutförandet och tjänstinstansen kommer vara igång. 

Service Fabric tillhandahåller en startpunkt för installationen av valfritt kommunikation för tjänster som lyssnar efter klientbegäranden. Startpunkten för både RunAsync och kommunikation är valfritt åsidosättningar i Service Fabric-tjänster - tjänsten kan välja att bara lyssna kundernas begäranden eller endast köra en loop för bearbetning eller båda - vilket är anledningen till RunAsync-metod kan avsluta utan att starta om tjänstinstansen, eftersom den kan fortsätta att lyssna efter klientbegäranden.

## <a name="application-api-and-environment"></a>Program-API och -miljö
Cloud Services-miljö API innehåller information och funktioner för den aktuella VM-instans, samt information om andra VM-rollinstanser. Service Fabric tillhandahåller information som rör dess runtime och viss information om noden en tjänst körs på. 

| **Miljö-aktivitet** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Konfigurationsinställningar och ändringsmeddelande |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokal lagring |`RoleEnvironment` |`CodePackageActivationContext` |
| Slutpunktsinformation |`RoleInstance` <ul><li>Aktuell instans: `RoleEnvironment.CurrentRoleInstance`</li><li>Andra roller och instans: `RoleEnvironment.Roles`</li> |<ul><li>`NodeContext` för den aktuella noden adress</li><li>`FabricClient` och `ServicePartitionResolver` för tjänstidentifiering för slutpunkt</li> |
| Miljö emulering |`RoleEnvironment.IsEmulated` |Gäller inte |
| Samtidiga Ändringshändelse |`RoleEnvironment` |Gäller inte |

## <a name="configuration-settings"></a>Konfigurationsinställningar
Konfigurationsinställningar i molntjänster som har angetts för en VM-roll och gäller för alla instanser av den Virtuella datorrollen. Dessa inställningar är nyckel / värde-par i ServiceConfiguration.*.cscfg filer och kan nås direkt via RoleEnvironment. I Service Fabric inställningarna individuellt för varje tjänst och till varje program, i stället för en virtuell dator, eftersom en virtuell dator kan vara värd för flera tjänster och program. En tjänst består av tre paket:

* **Kod:** innehåller tjänsten körbara filer, binärfiler, DLL-filer och andra filer som en tjänst ska köras.
* **Config:** alla konfigurationsfiler och inställningar för en tjänst.
* **Data:** Statiska datafiler som är kopplade till tjänsten.

Var och en av dessa paket kan vara olika versionsnummer och uppgraderas. Liknar Cloud Services, ett konfigurationspaket kan nås via programmering via ett API och händelser är tillgängliga att meddela tjänsten av en ändrad för paketet. En Settings.xml-fil kan användas för nyckel / värde-konfiguration och Programmeringsåtkomst liknar avsnittet appen inställningar för en App.config-fil. Men till skillnad från Cloud Services, kan ett Service Fabric config-paket innehålla alla konfigurationsfiler i valfritt format, oavsett om det är XML, JSON, YAML eller ett anpassat binärt format. 

### <a name="accessing-configuration"></a>Åtkomst till konfiguration
#### <a name="cloud-services"></a>Cloud Services
Konfigurationsinställningar från ServiceConfiguration.*.cscfg kan nås via `RoleEnvironment`. De här inställningarna är globalt tillgängliga för alla rollinstanser i samma molntjänst-distribution.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Varje tjänst har sin egen enskilda konfigurationspaket. Det finns ingen inbyggd mekanism för konfigurationsinställningar tillgänglig med alla program i ett kluster. När du använder Service Fabric särskilda Settings.xml konfigurationsfilen inom ett konfigurationspaket kan kan värdena i Settings.xml åsidosättas på programnivå som gör det möjligt programnivå konfigurationsinställningar.

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
Den `RoleEnvironment.Changed` händelsen används för att meddela alla rollinstanser när en ändring sker i miljön, till exempel en konfigurationsändring. Det här används för att använda uppdateringar utan återvinning rollinstanser eller starta om en arbetsprocess.

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
Var och en av tre pakettyper i en tjänst - kod, konfiguration och Data – har händelser som meddelar en tjänstinstans när ett paket uppdateras, har lagts till eller tas bort. En tjänst kan innehålla flera paket för varje typ. En tjänst kan till exempel ha flera config paket, vart och versionsnummer och kan uppgraderas. 

Dessa händelser är tillgängliga att använda ändringar i tjänstpaket utan att starta om tjänstinstansen.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Startåtgärder
Startåtgärder är åtgärder som vidtas innan ett program startar. Normalt används en startåtgärd för att köra installationsprogrammet skript med utökade privilegier. Både molntjänster och Service Fabric stöder startåtgärder. Den största skillnaden är att i Cloud Services, en startåtgärd är knutna till en virtuell dator eftersom det är en del av en rollinstans medan i Service Fabric en startåtgärd är knuten till en tjänst som inte är kopplat till en viss virtuell dator.

| Service Fabric | Cloud Services |
| --- | --- |
| Plats för konfiguration |ServiceDefinition.csdef |
| Behörigheter |”begränsad” eller ”utökad” |
| Ordningsföljd |"simple", "background", "foreground" |

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
I Service Fabric är en startpunkt för start konfigurerad per tjänst i ServiceManifest.xml:

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
Både molntjänster och Service Fabric är integrerade med Visual Studio-projektmallar och support för felsökning, konfigurera och distribuera både lokalt och till Azure. Både molntjänster och Service Fabric får en körningsmiljö för lokal utveckling. Skillnaden är att medan molntjänst utveckling runtime emulerar Azure-miljön där den körs, Service Fabric använder inte en emulator – den har slutförts Service Fabric-körningen. Service Fabric-miljö som du kör på din lokala utvecklingsdator är i samma miljö som körs i produktion.

## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric Reliable Services och grundläggande skillnaderna mellan Cloud Services och arkitektur för Service Fabric-program för att du ska kunna dra nytta av en fullständig uppsättning funktioner för Service Fabric.

* [Komma igång med Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Konceptuell guide till skillnaderna mellan Cloud Services och Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
