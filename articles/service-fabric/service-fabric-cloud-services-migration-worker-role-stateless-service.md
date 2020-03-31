---
title: Konvertera Azure Cloud Services-appar till Service Fabric
description: Den här guiden jämför molntjänster webb- och arbetarroller och tjänst fabric-tillståndslösa tjänster för att migrera från molntjänster till serviceinfrastruktur.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: caf067f793ca2086bc068907e86a82266627d128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463336"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Guide till konvertering av webb- och arbetarroller till tillståndslösa service- och tjänst fabric-tjänster
I den här artikeln beskrivs hur du migrerar dina molntjänsters webb- och arbetarroller till tjänst fabric-tillståndslösa tjänster. Det här är den enklaste migreringsvägen från Molntjänster till Service Fabric för program vars övergripande arkitektur kommer att förbli ungefär densamma.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Cloud Service-projekt till service fabric-programprojekt
 Ett Cloud Service-projekt och ett Service Fabric Application-projekt har en liknande struktur och representerar båda distributionsenheten för ditt program - det vill ha definierar de alla det kompletta paketet som distribueras för att köra ditt program. Ett molntjänstprojekt innehåller en eller flera webb- eller arbetsroller. På samma sätt innehåller ett serviceprogramprogram en eller flera tjänster. 

Skillnaden är att Cloud Service-projektet kopplar ihop programdistributionen med en VM-distribution och därmed innehåller vm-konfigurationsinställningar i det, medan service fabric-programmet-projektet bara definierar ett program som ska distribueras till en uppsättning befintliga virtuella datorer i ett Service Fabric-kluster. Själva Service Fabric-klustret distribueras bara en gång, antingen via en Resource Manager-mall eller via Azure-portalen, och flera Service Fabric-program kan distribueras till den.

![Projektjämnad mellan serviceinfrastruktur och molntjänster][3]

## <a name="worker-role-to-stateless-service"></a>Arbetarroll till tillståndslös tjänst
Begreppsmässigt representerar en arbetarroll en tillståndslös arbetsbelastning, vilket innebär att varje instans av arbetsbelastningen är identisk och begäranden kan dirigeras till valfri instans när som helst. Varje instans förväntas inte komma ihåg den tidigare begäran. Tillstånd som arbetsbelastningen fungerar på hanteras av ett externt tillståndsarkiv, till exempel Azure Table Storage eller Azure Cosmos DB. I Service Fabric representeras den här typen av arbetsbelastning av en tillståndslös tjänst. Den enklaste metoden för att migrera en arbetarroll till serviceinfrastruktur kan göras genom att konvertera arbetarrollskod till en tillståndslös tjänst.

![Arbetarroll till tillståndslös tjänst][4]

## <a name="web-role-to-stateless-service"></a>Webbroll till tillståndslös tjänst
I likhet med Arbetarrollen representerar en webbroll också en tillståndslös arbetsbelastning, och så konceptuellt kan den också mappas till en tjänst för service fabric. Till skillnad från webbroller stöder dock service fabric inte IIS. För att migrera ett webbprogram från en webbroll till en tillståndslös tjänst måste du först flytta till ett webbramverk som kan vara självvärd och inte är beroende av IIS eller System.Web, till exempel ASP.NET Core 1.

| **Program** | **Stöds** | **Migreringsväg** |
| --- | --- | --- |
| ASP.NET webbformulär |Inga |Konvertera till ASP.NET Core 1 MVC |
| ASP.NET MVC |Med migrering |Uppgradera till ASP.NET Core 1 MVC |
| ASP.NET Web API |Med migrering |Använda självvärdserver eller ASP.NET Core 1 |
| ASP.NET Kärna 1 |Ja |Ej tillämpligt |

## <a name="entry-point-api-and-lifecycle"></a>API och livscykel för startpunkt
Arbetsroll och service fabric-api:er erbjuder liknande startpunkter: 

| **Ingång** | **Arbetarroll** | **Service Fabric-tjänst** |
| --- | --- | --- |
| Bearbetar |`Run()` |`RunAsync()` |
| Start av virtuell dator |`OnStart()` |Ej tillämpligt |
| VM-stopp |`OnStop()` |Ej tillämpligt |
| Öppen lyssnare för klientförfrågningar |Ej tillämpligt |<ul><li> `CreateServiceInstanceListener()`för statslösa</li><li>`CreateServiceReplicaListener()`för tillståndskänsliga</li></ul> |

### <a name="worker-role"></a>Arbetarroll
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

### <a name="service-fabric-stateless-service"></a>Service Fabric tillståndslös tjänst
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

Båda har en primär "Kör" åsidosättning där du kan börja bearbeta. Service `Run`Fabric-tjänster `Start`kombinerar `Stop` , och till `RunAsync`en enda startpunkt. Tjänsten ska börja `RunAsync` fungera när den startas `RunAsync` och bör sluta fungera när metodens CancellationToken signaleras. 

Det finns flera viktiga skillnader mellan livscykeln och livslängden för Worker Roles och Service Fabric-tjänster:

* **Livscykel:** Den största skillnaden är att en arbetarroll är en virtuell dator och dess livscykel är därför knuten till den virtuella datorn, vilket inkluderar händelser för när den virtuella datorn startar och stoppar. En Service Fabric-tjänst har en livscykel som är skild från den virtuella datorns livscykel, så den innehåller inte händelser för när värddatorn eller datorn startar och stoppas, eftersom de inte är relaterade.
* **Livslängd:** En worker role-instans återvinns om `Run` metoden avslutas. Metoden `RunAsync` i en Service Fabric-tjänst kan dock köras till slutförande och serviceinstansen förblir uppe. 

Service Fabric tillhandahåller en valfri kommunikationsinställningsanspunkt för tjänster som lyssnar efter klientbegäranden. Både Startpunkten för RunAsync och kommunikation är valfria åsidosättningar i Service Fabric-tjänster – tjänsten kan välja att bara lyssna på klientbegäranden, eller bara köra en bearbetningsloop, eller båda - vilket är anledningen till att Metoden RunAsync tillåts avsluta utan att starta om serviceinstansen, eftersom den kan fortsätta att lyssna efter klientbegäranden.

## <a name="application-api-and-environment"></a>Api och miljö för program
Cloud Services-miljö-API:et innehåller information och funktioner för den aktuella VM-instansen samt information om andra VM-rollinstanser. Service Fabric innehåller information om dess körning och viss information om den nod som en tjänst körs på. 

| **Miljöuppgift** | **Molntjänster** | **Service Fabric** |
| --- | --- | --- |
| Konfigurationsinställningar och ändringsmeddelande |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokal lagring |`RoleEnvironment` |`CodePackageActivationContext` |
| Information om slutpunkt |`RoleInstance` <ul><li>Aktuell instans:`RoleEnvironment.CurrentRoleInstance`</li><li>Andra roller och instanser:`RoleEnvironment.Roles`</li> |<ul><li>`NodeContext`för aktuell nodadress</li><li>`FabricClient`och `ServicePartitionResolver` för identifiering av tjänstens slutpunkt</li> |
| Miljö emulering |`RoleEnvironment.IsEmulated` |Ej tillämpligt |
| Samtidig ändringshändelse |`RoleEnvironment` |Ej tillämpligt |

## <a name="configuration-settings"></a>Konfigurationsinställningar
Konfigurationsinställningar i Molntjänster är inställda för en VM-roll och gäller för alla instanser av den virtuella datorn-rollen. Dessa inställningar är nyckelvärdespar som anges i ServiceConfiguration.*.cscfg-filer och kan nås direkt via RoleEnvironment. I Service Fabric gäller inställningarna individuellt för varje tjänst och för varje program, i stället för för en virtuell dator, eftersom en virtuell dator kan vara värd för flera tjänster och program. En tjänst består av tre paket:

* **Kod:** innehåller tjänstens körbara filer, binärfiler, DLL-filer och alla andra filer som en tjänst behöver köra.
* **Config:** alla konfigurationsfiler och inställningar för en tjänst.
* **Data:** statiska datafiler som är associerade med tjänsten.

Vart och ett av dessa paket kan göras oberoende av en version och uppgraderas. I likhet med Molntjänster kan ett konfigurationspaket nås programmässigt via ett API och händelser är tillgängliga för att meddela tjänsten om en konfigurationspaketändring. En Settings.xml-fil kan användas för konfiguration av nyckelvärde och programmatisk åtkomst som liknar avsnittet appinställningar i en App.config-fil. Till skillnad från Molntjänster kan dock ett Service Fabric-config-paket innehålla alla konfigurationsfiler i alla format, oavsett om det är XML, JSON, YAML eller ett anpassat binärt format. 

### <a name="accessing-configuration"></a>Komma åt konfiguration
#### <a name="cloud-services"></a>Molntjänster
Konfigurationsinställningar från ServiceConfiguration.*.cscfg kan `RoleEnvironment`nås via . Dessa inställningar är globalt tillgängliga för alla rollinstanser i samma Cloud Service-distribution.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Varje tjänst har ett eget individuellt konfigurationspaket. Det finns ingen inbyggd mekanism för globala konfigurationsinställningar som är tillgängliga för alla program i ett kluster. När du använder Service Fabric speciella Settings.xml-konfigurationsfil i ett konfigurationspaket kan värden i Settings.xml skrivas över på programnivå, vilket gör konfigurationsinställningarna på programnivå möjliga.

Konfigurationsinställningarna är åtkomster inom varje `CodePackageActivationContext`tjänstinstans via tjänstens .

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

### <a name="configuration-update-events"></a>Konfigurationsuppdateringshändelser
#### <a name="cloud-services"></a>Molntjänster
Händelsen `RoleEnvironment.Changed` används för att meddela alla rollinstanser när en ändring sker i miljön, till exempel en konfigurationsändring. Detta används för att använda konfigurationsuppdateringar utan att återvinna rollinstanser eller starta om en arbetsprocess.

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
Var och en av de tre pakettyperna i en tjänst - Kod, Config och Data - har händelser som meddelar en tjänstinstans när ett paket uppdateras, läggs till eller tas bort. En tjänst kan innehålla flera paket av varje typ. En tjänst kan till exempel ha flera config-paket, varje individuellt version och uppgraderingsbar. 

Dessa händelser är tillgängliga för att använda ändringar i tjänstpaket utan att starta om tjänstinstansen.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Startuppgifter
Startuppgifter är åtgärder som vidtas innan ett program startar. En startuppgift används vanligtvis för att köra installationsskript med förhöjda privilegier. Både Cloud Services och Service Fabric stöder startuppgifter. Den största skillnaden är att i Cloud Services är en startuppgift knuten till en virtuell dator eftersom den är en del av en rollinstans, medan en startuppgift i Service Fabric är knuten till en tjänst som inte är knuten till en viss virtuell dator.

| Service Fabric | Molntjänster |
| --- | --- |
| Konfigurationsplats |ServiceDefinition.csdef |
| Behörigheter |"begränsad" eller "förhöjd" |
| Sekvensering |"enkel", "bakgrund", "förgrund" |

### <a name="cloud-services"></a>Molntjänster
I Molntjänster konfigureras en startpunkt per roll i ServiceDefinition.csdef. 

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
I Service Fabric konfigureras en startpunkt per tjänst i ServiceManifest.xml:

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
Både Cloud Services och Service Fabric är integrerade med Visual Studio med projektmallar och stöd för felsökning, konfiguration och distribution både lokalt och till Azure. Både Molntjänster och Service Fabric ger också en lokal utvecklingskörningsmiljö. Skillnaden är att medan Cloud Service-utvecklingens körning emulerar Azure-miljön som den körs på, använder Service Fabric inte en emulator - den använder hela Service Fabric-körningen. Service Fabric-miljön som du kör på din lokala utvecklingsmaskin är samma miljö som körs i produktion.

## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric Reliable Services och de grundläggande skillnaderna mellan molntjänst och service fabric-programarkitektur för att förstå hur du kan dra nytta av alla Service Fabric-funktioner.

* [Komma igång med Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Konceptuell guide till skillnaderna mellan molntjänster och serviceinfrastruktur](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
