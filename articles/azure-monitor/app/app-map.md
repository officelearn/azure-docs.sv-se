---
title: Programkarta i Azure Application Insights | Microsoft Docs
description: Övervaka komplexa programtopologier med programkartan
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/15/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 618453ec9857254fe14608df8091bb79bd3815b7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509976"
---
# <a name="application-map-triage-distributed-applications"></a>Programkartan: Hantera distribuerade program

Programavbildning hjälper dig att upptäcka flaskhalsar eller fel-anslutningar för alla komponenter i det distribuerade programmet. Varje nod på kartan representerar en programkomponent eller dess beroenden; och har hälsotillstånd KPI och varnar status. Du kan klicka vidare från valfri komponent till mer detaljerad analys, till exempel Application Insights-händelser. Om appen använder Azure-tjänster kan även klicka vidare till Azure-diagnostik, till exempel SQL Database Advisor-rekommendationer.

## <a name="what-is-a-component"></a>Vad är en komponent?

Komponenterna är oberoende av varandra distribuerbar delar av programmet distribueras/mikrotjänster. Utvecklare och åtgärder team har kod-nivå eller åtkomst till telemetri som genereras av dessa programkomponenter. 

* Komponenter skiljer sig från ”observerade” externa beroenden, till exempel SQL, EventHub etc. som ditt team/organisation inte kanske har åtkomst till (kod eller telemetri).
* Komponenter som körs på valfritt antal instanser av server-rollen-behållare.
* Komponenterna kan vara olika Application Insights-instrumenteringsnycklar (även om prenumerationer skiljer sig) eller olika roller som rapporterar till en enda Application Insights-instrumenteringsnyckeln. Förhandsgranskningsupplevelsen för kartan visar komponenterna oavsett hur de är konfigurerade.

## <a name="composite-application-map"></a>Sammansatt Programkarta

Du kan se hela programmets topologi över flera nivåer av relaterade programkomponenter. Komponenterna kan vara olika Application Insights-resurser eller olika roller i en enskild resurs. Programkartan hittar komponenter av följande HTTP-beroendeanrop mellan servrar med Application Insights SDK installerad. 

Den här upplevelsen börjar med progressiv identifiering av komponenter. När du först läser in programkartan utlöses en uppsättning frågor för att identifiera de komponenter som hör till den här komponenten. En knapp i det övre vänstra hörnet uppdateras med antalet komponenter i ditt program när de upptäcks. 

När du klickar på ”Uppdatera kartkomponenter”, uppdateras kartan med alla komponenter som identifieras förrän som pekar. Det kan ta någon minut att läsa in beroende på komplexiteten för ditt program.

Den här identifieringssteget är inte obligatoriskt om alla komponenter är roller i en enda Application Insights-resurs. Den initiala inläsningen för ett sådant program kommer att ha alla dess komponenter.

![Skärmbild av programmet karta](media/app-map/app-map-001.png)

En av de viktiga mål med den här upplevelsen är för att kunna visualisera komplexa topologier med hundratals komponenter.

Klicka på någon komponent för att se relaterade insikter och gå till den prestanda och fel prioritering upplevelse för respektive komponent.

![Utfällt](media/app-map/application-map-002.png)

### <a name="investigate-failures"></a>Undersök fel

Välj **Undersök fel** att starta fönstret fel.

![Skärmbild av undersöka fel-knappen](media/app-map/investigate-failures.png)

![Skärmbild av fel-upplevelsen](media/app-map/failures.png)

### <a name="investigate-performance"></a>Undersök prestanda

Om du vill felsöka prestandaproblem, Välj **Undersök prestanda**.

![Skärmbild av Undersök prestanda-knappen](media/app-map/investigate-performance.png)

![Skärmbild av prestandaupplevelse](media/app-map/performance.png)

### <a name="go-to-details"></a>Gå till information

Välj **går du till information om** att utforska transaktion slutpunkt till slutpunkt-upplevelsen, vilken kan erbjuda vyer anrop stack-nivå.

![Skärmbild av knappen Gå till information](media/app-map/go-to-details.png)

![Skärmbild av transaktionsinformation för slutpunkt till slutpunkt](media/app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Visa i Analytics

Fråga och undersöka programdata ytterligare genom att klicka på **visa i analys**.

![Skärmbild av vyn i knappen analytics](media/app-map/view-in-analytics.png)

![Skärmbild av analytics](media/app-map/analytics.png)

### <a name="alerts"></a>Aviseringar

Om du vill visa aktiva varningar och de underliggande reglerna som orsakar att aviseringarna utlöses, Välj **aviseringar**.

![Skärmbild av knappen för aviseringar](media/app-map/alerts.png)

![Skärmbild av analytics](media/app-map/alerts-view.png)

## <a name="set-cloud-role-name"></a>Ställ in molnrollnamn

Programavbildning använder den **molnrollnamn** egenskapen att identifiera komponenterna på kartan. Application Insights SDK lägger automatiskt till molnet rollen namnegenskapen telemetri som genereras av komponenter. Till exempel SDK: N kommer lägger till en webbplatsens namn eller tjänstnamnet i roll namnegenskapen för cloud-rollen. Men finns det fall där kan du åsidosätta standardvärdet. Att åsidosätta molnrollnamn och ändra det hämtar visas på kartan för programmet:

### <a name="net"></a>.NET

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
                telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance"
            }
        }
    }
}
```

**Läsa in din initieraren**

In ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="CustomInitializer.Telemetry.MyTelemetryInitializer, CustomInitializer"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

En alternativ metod är att skapa en instans av initierare i kod, till exempel i Global.aspx.cs:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
    }
```

### <a name="nodejs"></a>Node.js

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

### <a name="java"></a>Java

Om du använder Spring Boot med Application Insights Spring Boot starter är den enda önskade ändringen att ange ditt eget namn för programmet i application.properties-filen.

`spring.application.name=<name-of-app>`

Spring Boot starter tilldelas automatiskt molnrollnamn till värdet du anger för egenskapen spring.application.name.

Ytterligare information om Java Korrelations- och hur du konfigurerar molnroll namn för icke-SpringBoot program checka ut detta [avsnittet](https://docs.microsoft.com/azure/application-insights/application-insights-correlation#role-name) på korrelation.

### <a name="clientbrowser-side-javascript"></a>Klientens/webbläsaren JavaScript

```javascript
appInsights.queue.push(() => {
appInsights.context.addTelemetryInitializer((envelope) => {
  envelope.tags["ai.cloud.role"] = "your role name";
  envelope.tags["ai.cloud.roleInstance"] = "your role instance";
});
});
```

### <a name="understanding-cloud-role-name-within-the-context-of-the-application-map"></a>Förstå molnrollnamn inom ramen för Programkartan

Vad gäller hur du förhåller dig **molnrollnamn**, det kan vara bra att titta på en karta för program som har flera moln rollnamn finns:

![Skärmbild av programmet karta](media/app-map/cloud-rolename.png)

I programavbildning ovan vart och ett av namnen i gröna rutorna är molnet rollen värden för olika aspekter av det aktuella distribuerade programmet. Så för den här appen dess roller består av: `Authentication`, `acmefrontend`, `Inventory Management`, ett `Payment Processing Worker Role`. 

När det gäller den här appen representerar var och en av dessa rollnamn för molnet också en annan unik Application Insights-resurs med sina egna instrumenteringsnycklar. Eftersom ägaren av det här programmet har åtkomst till var och en av de fyra olika Application Insights-resurserna, kan Programkartan en karta över underliggande relationerna sätta ihop.

För den [officiella definitioner](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/39a5ef23d834777eefdd72149de705a016eb06b0/Schema/PublicSchema/ContextTagKeys.bond#L93):

```
   [Description("Name of the role the application is a part of. Maps directly to the role name in azure.")]
    [MaxStringLength("256")]
    705: string      CloudRole = "ai.cloud.role";
    
    [Description("Name of the instance where the application is running. Computer name for on-premises, instance name for Azure.")]
    [MaxStringLength("256")]
    715: string      CloudRoleInstance = "ai.cloud.roleInstance";
```

Du kan också **molnet rollinstans** kan vara användbart för scenarier där **molnrollnamn** talar om problemet är någonstans i din Webbklient, men du kan köra dina webbklient över flera belastningsutjämnade servrar så att kunna öka detaljnivån i ett lager som är djupare via Kusto-frågor och att känna till om problemet påverkar alla web servrar/klientdelsinstanserna eller bara en kan vara mycket viktigt.

Ett scenario där du kanske vill åsidosätta värdet för molnet rollinstans kan vara om din app körs i en miljö med behållare där bara att känna till den enskilda servern kanske inte tillräckligt med information för att hitta ett visst problem.

Mer information om hur du åsidosätter namnegenskapen molnet rollen med telemetri-initierare finns i [Lägg till egenskaper: ITelemetryInitializer](api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="troubleshooting"></a>Felsökning

Om du har problem med att få Programkartan som fungerar som förväntat, kan du prova de här stegen:

### <a name="general"></a>Allmänt

1. Kontrollera att du använder en SDK som stöds officiellt. SDK:er som inte stöds eller community-SDK:er har kanske inte stöd för korrelation.

    Referera till denna [artikeln](https://docs.microsoft.com/azure/application-insights/app-insights-platforms) en lista över stödda SDK: erna.

2. Uppgradera alla komponenter till den senaste versionen av SDK.

3. Om du använder Azure Functions med C#, uppgradera till [Functions V2](https://docs.microsoft.com/azure/azure-functions/functions-versions).

4. Bekräfta [molnrollnamn](#set-cloud-role-name) är korrekt konfigurerad.

5. Om du saknar ett beroende kontrollerar du att det finns i listan över [automatiskt insamlade beroenden](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies). Om inte så kan du ändå spåra det manuellt med ett [beroendespårningsanrop](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

### <a name="too-many-nodes-on-the-map"></a>För många noderna på kartan

Programavbildning skapar en programnod för varje unikt molnrollnamn finns i din begärandetelemetri och en beroendenod för varje unik kombination av typen, mål och molnrollnamn i din telemetri om beroenden. Om det finns fler än 10 000 noder i telemetrin visas Programkartan inte hämta alla noder och länkar, så att kartan är ofullständig. Om det händer visas ett varningsmeddelande när du visar kartan.

Dessutom Programkartan endast har stöd för upp till 1 000 separat uppdelade noder återges på samma gång. Programavbildning minskar visual komplexiteten genom att gruppera beroenden som har samma typ och anropare, men om din telemetri har för många unika molnet rollnamn eller för många Beroendetyper, gruppen kommer att vara otillräckliga och kartan kommer inte att återge.

Lös problemet genom måste du ändra din instrumentering för att ange korrekt molnrollnamn, beroendetyp och beroende målfälten.

* Beroendemål ska motsvara det logiska namnet för ett beroende. I många fall är det motsvarar servern eller resursnamnet för beroendet. Till exempel är den inställd på värdnamnet för HTTP-beroenden. Det får inte innehålla unika ID: N eller parametrar som ändrar från en begäran till en annan.

* Beroendetyp ska motsvara den logiska typen för ett beroende. Till exempel är HTTP-, SQL- eller Azure Blob typiska Beroendetyper. Det får inte innehålla unika ID: N.

* Syftet med molnrollnamn beskrivs i den [ovan avsnittet](https://docs.microsoft.com/azure/azure-monitor/app/app-map#set-cloud-role-name).

## <a name="portal-feedback"></a>Portalen feedback

Använd alternativet feedback om du vill ge feedback.

![MapLink-1-bild](./media/app-map/14-updated.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå korrelation](https://docs.microsoft.com/azure/application-insights/application-insights-correlation)
