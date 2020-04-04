---
title: Programöversikt i Azure Application Insights | Microsoft-dokument
description: Övervaka komplexa programtopologier med programkartan
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.openlocfilehash: 0823dd5d880c778f9b7a231ac14f1cbba1940927
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657394"
---
# <a name="application-map-triage-distributed-applications"></a>Programkarta: Triage distribuerade program

Application Map hjälper dig att upptäcka flaskhalsar eller felpunkter för prestanda i alla komponenter i det distribuerade programmet. Varje nod på kartan representerar en programkomponent eller dess beroenden. och har status för hälso-KPI och aviseringar. Du kan klicka dig vidare från valfri komponent till mer detaljerad diagnostik, till exempel Application Insights-händelser. Om din app använder Azure-tjänster kan du också klicka dig vidare till Azure-diagnostik, till exempel SQL Database Advisor-rekommendationer.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenter är oberoende av de delar av ditt distribuerade/mikrotjänstprogram. Utvecklare och driftteam har synlighet på kodnivå eller åtkomst till telemetri som genereras av dessa programkomponenter. 

* Komponenter skiljer sig från "observerade" externa beroenden som SQL, EventHub etc. som ditt team/organisation kanske inte har åtkomst till (kod eller telemetri).
* Komponenter körs på valfritt antal instanser av server-/roll/behållar.
* Komponenter kan vara separata Instrumenteringsnycklar för Application Insights (även om prenumerationer är olika) eller olika roller som rapporterar till en enda Application Insights-instrumenteringsnyckel. Förhandsgranskningskartupplevelsen visar komponenterna oavsett hur de ställs in.

## <a name="composite-application-map"></a>Karta över sammansatta program

Du kan se den fullständiga programtopologin på flera nivåer av relaterade programkomponenter. Komponenter kan vara olika Application Insights-resurser eller olika roller i en enda resurs. Appkartan hittar komponenter genom att följa HTTP-beroendeanrop som görs mellan servrar med Application Insights SDK installerat. 

Den här upplevelsen börjar med progressiv upptäckt av komponenterna. När du först läser in programmappningen utlöses en uppsättning frågor för att identifiera komponenterna som är relaterade till den här komponenten. En knapp längst upp till vänster uppdateras med antalet komponenter i programmet när de upptäcks. 

När du klickar på "Uppdatera kartkomponenter" uppdateras kartan med alla komponenter som upptäcks fram till den punkten. Beroende på komplexiteten i ditt program kan det ta en minut att läsa in.

Om alla komponenter är roller i en enda Application Insights-resurs krävs inte det här identifieringssteget. Den första belastningen för ett sådant program kommer att ha alla dess komponenter.

![Skärmdump av programkarta](media/app-map/app-map-001.png)

Ett av de viktigaste målen med denna erfarenhet är att kunna visualisera komplexa topologier med hundratals komponenter.

Klicka på en komponent för att se relaterade insikter och gå till prestanda och fel triage erfarenhet för den komponenten.

![Flyout](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Undersöka fel

Välj **undersöka fel** för att starta fönstret fel.

![Skärmbild av knappen Undersöka fel](media/app-map/investigate-failures.png)

![Skärmbild av felupplevelse](media/app-map/failures.png)

### <a name="investigate-performance"></a>Undersök prestanda

Om du vill felsöka prestandaproblem väljer du **undersöka prestanda**.

![Skärmbild av prestandaknappen undersök](media/app-map/investigate-performance.png)

![Skärmbild av prestandaupplevelse](media/app-map/performance.png)

### <a name="go-to-details"></a>Gå till detaljer

Välj **gå till detaljer** för att utforska transaktionsupplevelsen från slutna till sluten tid, som kan erbjuda vyer ner till samtalsstacken.

![Skärmbild av knappen Gå till detaljer](media/app-map/go-to-details.png)

![Skärmbild av transaktionsinformation från sluten till](media/app-map/end-to-end-transaction.png)

### <a name="view-logs-analytics"></a>Visa loggar (Analytics)

Om du vill fråga efter och undersöka dina programdata ytterligare klickar du på **Visa i Loggar (Analytics).**

![Skärmbild av vyn i analysknappen](media/app-map/view-logs.png)

![Skärmbild av analysupplevelse. Linjediagram som sammanfattar den genomsnittliga svarstiden för en begäran under de senaste 12 timmarna.](media/app-map/log-analytics.png)

### <a name="alerts"></a>Aviseringar

Om du vill visa aktiva aviseringar och de underliggande regler som gör att aviseringarna utlöses väljer du **aviseringar**.

![Skärmbild av knappen Aviseringar](media/app-map/alerts.png)

![Skärmbild av analysupplevelse](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Ange namn på molnroll

Programmappning använder egenskapen **molnrollnamn** för att identifiera komponenterna på kartan. Application Insights SDK lägger automatiskt till egenskapen för molnrollnamn i telemetri som avges av komponenter. SDK lägger till till exempel till ett namn eller ett tjänstrollnamn till egenskapen för molnrollnamn. Det finns dock fall där du kanske vill åsidosätta standardvärdet. Så här åsidosätter du molnets rollnamn och ändrar vad som visas på programöversikten:

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

**Skriv anpassad TelemetryInitializer enligt nedan.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here
                telemetry.Context.Cloud.RoleName = "Custom RoleName";
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

**ASP.NET appar: Ladda initializer till den aktiva TelemetryConfiguration**

I ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

En alternativ metod för ASP.NET webbappar är att instansiera initializern i kod, till exempel i Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

> [!NOTE]
> Att lägga `ApplicationInsights.config` till `TelemetryConfiguration.Active` initializer med eller använda är inte giltigt för ASP.NET Core-program. 

**ASP.NET Core-appar: Ladda initializern till TelemetryConfiguration**

För [ASP.NET Core-program](asp-net-core.md#adding-telemetryinitializers) görs `TelemetryInitializer` det genom att lägga till en ny genom att lägga till den i behållaren för beroendeinsprutning, som visas nedan. Detta görs `ConfigureServices` med metoden `Startup.cs` för din klass.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

# <a name="java"></a>[Java](#tab/java)

Från och med Application Insights Java SDK 2.5.0 kan `<RoleName>` du `ApplicationInsights.xml` ange molnetrollnamnet genom att lägga till filen, t.ex.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
   <RoleName>** Your role name **</RoleName>
   ...
</ApplicationInsights>
```

Om du använder Spring Boot med Application Insights Spring Boot starter, är den enda nödvändiga ändringen att ange ditt anpassade namn för programmet i application.properties-filen.

`spring.application.name=<name-of-app>`

Startstart för fjäderstart tilldelar automatiskt molnrollnamnet till det värde du anger för egenskapen spring.application.name.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();
appInsights.defaultClient.context.tags["ai.cloud.role"] = "your role name";
appInsights.defaultClient.context.tags["ai.cloud.roleInstance"] = "your role instance";
```

### <a name="alternate-method-for-nodejs"></a>Alternativ metod för Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY').start();

appInsights.defaultClient.addTelemetryProcessor(envelope => {
    envelope.tags["ai.cloud.role"] = "your role name";
    envelope.tags["ai.cloud.roleInstance"] = "your role instance"
});
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
appInsights.queue.push(() => {
appInsights.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```
---

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Förstå molnrollnamn inom ramen för programöversikten

När det gäller hur man tänker på **molnrollnamn**kan det vara bra att titta på en programkarta som har flera molnrollnamn närvarande:

![Skärmdump av programkarta](media/app-map/cloud-rolename.png)

I programkartan ovanför var och en av namnen i gröna rutor finns molnrollnamnsvärden för olika aspekter av det här distribuerade programmet. Så för denna app dess `Authentication` `acmefrontend`roller `Inventory Management`består `Payment Processing Worker Role`av: , , , a . 

När det gäller den här appen representerar var och en av dessa molnrollnamn också en annan unik Application Insights-resurs med sina egna instrumenteringsnycklar. Eftersom ägaren av det här programmet har tillgång till var och en av dessa fyra olika Application Insights-resurser kan Program map sy ihop en karta över de underliggande relationerna.

För de [officiella definitionerna:](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93)

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Alternativt kan **molnrollinstans** vara till hjälp för scenarier där **molnrollnamnet** talar om för dig att problemet finns någonstans i webbfronten, men du kanske kör din webbfrontsida över flera belastningsbalanserade servrar så att du kan borra i ett lager djupare via Kusto-frågor och veta om problemet påverkar alla webbfrontendservrar/-instanser eller bara en kan vara extremt viktig.

Ett scenario där du kanske vill åsidosätta värdet för molnrollinstansen kan vara om din app körs i en containermiljö där det kanske inte finns tillräckligt med information för att hitta ett visst problem.

Mer information om hur du åsidosätter egenskapen för molnrollnamn med telemetriinitierare finns [i Lägga till egenskaper: ITelemetryInitializer](api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att få programkartan att fungera som förväntat kan du prova följande steg:

### <a name="general"></a>Allmänt

1. Kontrollera att du använder en SDK som stöds officiellt. SDK:er som inte stöds eller community-SDK:er har kanske inte stöd för korrelation.

    Se den här [artikeln](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) för en lista över SDK:er som stöds.

2. Uppgradera alla komponenter till den senaste SDK-versionen.

3. Om du använder Azure Functions med C#uppgraderar du till [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Bekräfta att [molnrollnamnet](#set-cloud-role-name) är korrekt konfigurerat.

5. Om du saknar ett beroende kontrollerar du att det finns i listan över [automatiskt insamlade beroenden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Om inte så kan du ändå spåra det manuellt med ett [beroendespårningsanrop](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>För många noder på kartan

Programmappningen skapar en programnod för varje unikt molnrollnamn som finns i din begäran telemetri och en beroendenod för varje unik kombination av typ, mål och molnrollnamn i ditt beroendetelemetri. Om det finns fler än 10 000 noder i telemetrin kan programkartan inte hämta alla noder och länkar, så kartan blir ofullständig. Om detta inträffar visas ett varningsmeddelande när kartan visas.

Dessutom stöder programöversikten endast upp till 1 000 separata ogrupperade noder som återges samtidigt. Programmappning minskar visuell komplexitet genom att gruppera beroenden tillsammans som har samma typ och anropare, men om telemetrin har för många unika molnrollnamn eller för många beroendetyper, kommer den gruppningen att vara otillräcklig och kartan inte kan återges.

För att åtgärda detta måste du ändra instrumenteringen så att de anger fälten för molnrollnamn, beroendetyp och beroende.

* Beroendemålet bör representera det logiska namnet på ett beroende. I många fall motsvarar det beroendets server- eller resursnamn. När det till exempel gäller HTTP-beroenden anges den till värdnamnet. Den bör inte innehålla unika ID:er eller parametrar som ändras från en begäran till en annan.

* Beroendetypen bör representera den logiska typen av ett beroende. HTTP, SQL eller Azure Blob är till exempel typiska beroendetyper. Den bör inte innehålla unika ID:er.

* Syftet med molnrollnamnet beskrivs i avsnittet [ovan](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Feedback från portalen

Om du vill ge feedback använder du feedbackalternativet.

![Bild av MapLink-1](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur korrelation fungerar i Application Insights finns i [korrelationsartikeln](correlation.md)för telemetri .
* Diagnostikupplevelsen från på på säkerhet korrelerar telemetri på serversidan från alla application [insights-övervakade](transaction-diagnostics.md) komponenter till en enda vy.
* För avancerade korrelationsscenarier i ASP.NET Core och ASP.NET finns i artikeln [spåra anpassade åtgärder.](custom-operations-tracking.md)
