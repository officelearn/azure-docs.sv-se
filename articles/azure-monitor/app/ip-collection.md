---
title: Azure Application Insights IP-webbplatssamling | Microsoft Docs
description: Förstå hur IP-adresser och geolokalisering hanteras med Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mbullwin
ms.openlocfilehash: 3a504fe4475cee8e2949ee121c632b792f349758
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694289"
---
# <a name="geolocation-and-ip-address-handling"></a>Hantering av geolokalisering och IP-adresser

Den här artikeln förklarar hur geolokalisering och IP-adress hantering sker i Application Insights tillsammans med hur du ändrar standard beteendet.

## <a name="default-behavior"></a>Standardbeteende

Som standard samlas IP-adresser in tillfälligt, men lagras inte i Application Insights. Den grundläggande processen är följande:

IP-adresser skickas till Application Insights som en del av telemetri-data. När inläsnings slut punkten nåtts i Azure används IP-adressen för att genomföra en geolokalisering med [GeoLite2 från MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Resultatet av sökningen används för att fylla i följande fält `client_City`, `client_StateOrProvince`, `client_CountryOrRegion`. I det här läget ignoreras IP-adressen och `0.0.0.0` den skrivs `client_IP` till fältet.

* Webb läsar telemetri: Vi samlar tillfälligt in avsändarens IP-adress. IP-adressen beräknas av inmatnings slut punkten.
* Server telemetri: Application Insights-modulen samlar tillfälligt in klientens IP-adress. Den samlas inte in om `X-Forwarded-For` har angetts.

Det här beteendet är avsiktligt för att undvika onödig insamling av personliga data. När det är möjligt rekommenderar vi att du undviker insamling av person uppgifter. 

## <a name="overriding-default-behavior"></a>Åsidosätter standard beteende

Även om standard beteendet är att minimera insamling av personliga data, erbjuder vi fortfarande flexibiliteten att samla in och lagra IP-Datadata. Innan du väljer att lagra personliga data, t. ex. IP-adresser, rekommenderar vi starkt att du verifierar att detta inte bryter mot eventuella krav på efterlevnad eller lokala regler som du kan omfattas av. Mer information om personlig data hantering i Application Insights finns i [rikt linjerna för person uppgifter](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-partial-ip-address-data"></a>Delar IP-Datadata

För att kunna aktivera partiell IP-samling och lagring måste `DisableIpMasking` egenskapen för Application Insights-komponenten anges till. `true` Den här egenskapen kan anges antingen via Azure Resource Manager mallar eller genom att anropa REST API. IP-adresser registreras med den sista oktetten nollställt.


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portalen 

Om du bara behöver ändra beteendet för en enskild Application Insights resurs är det enklaste sättet att göra detta via Azure Portal.  

1. Gå till din Application Insights resurs > **Inställningar** > **Exportera mall** 

    ![Exportera mall](media/ip-collection/export-template.png)

2. Välj **distribuera**

    ![Knappen distribuera är markerad i rött](media/ip-collection/deploy.png)

3. Välj **Redigera mall**. (Om din mall har ytterligare egenskaper eller resurser som inte visas i den här exempel mal len, fortsätter du med varningen för att se till att alla resurser kommer att acceptera mallen för distribution som en stegvis ändring/uppdatering.)

    ![Redigera mall](media/ip-collection/edit-template.png)

4. Gör följande ändringar i JSON för resursen och klicka sedan på **Spara**:

    ![Skärm bilden lägger till ett kommatecken efter "IbizaAIExtension" och lägger till en ny rad nedan med "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Om det uppstår ett fel som säger: **_Resurs gruppen finns på en plats som inte stöds av en eller flera resurser i mallen. Välj en annan resurs grupp._** Välj tillfälligt en annan resurs grupp i list rutan och välj sedan den ursprungliga resurs gruppen igen för att lösa problemet.

5. Välj **Jag accepterar** > **köp**. 

    ![Redigera mall](media/ip-collection/purchase.png)

    I det här fallet köps inget nytt, men vi uppdaterar bara konfigurationen av den befintliga Application Insights-resursen.

6. När distributionen är klar registreras nya telemetridata med de första tre oktetterna som är ifyllda med IP-adressen och den sista oktetten har nollställts.

    Om du valde och redigera mallen igen skulle du bara se standardmallen och inte se den nyligen tillagda egenskapen och det associerade värdet. Om du inte ser IP-Datadata och vill bekräfta att `"DisableIpMasking": true` har angetts. Kör följande PowerShell: (Ersätt `Fabrikam-dev` med lämpligt resurs-och resurs grupps namn.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    En lista med egenskaper kommer att returneras som ett resultat. En av egenskaperna ska läsas `DisableIpMasking: true`. Om du kör PowerShell innan du distribuerar den nya egenskapen med Azure Resource Manager, finns inte egenskapen.

### <a name="rest-api"></a>REST-API

[REST API](https://docs.microsoft.com/rest/api/azure/) -nyttolasten för att göra samma ändringar är följande:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Telemetri-initierare

Om du behöver registrera hela IP-adressen i stället för bara de tre första oktetterna kan du använda en [telemetri](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer) -initierare för att kopiera IP-adressen till ett anpassat fält som inte kommer att maskeras.

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Om du inte kan komma åt `ISupportProperties`kontrollerar du och kontrollerar att du kör den senaste stabila versionen av Application Insights SDK. `ISupportProperties`är avsedda för höga kardinal värden, och `GlobalProperties` är mer lämpliga för låga kardinal-värden som region namn, miljö namn osv. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Aktivera telemetri-initieraren för. ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Aktivera telemetri-initierare för ASP.NET Core

Du kan skapa en telemetri-initierare på samma sätt för ASP.NET Core som ASP.NET, men om du vill aktivera initieraren använder du följande exempel som referens:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>Java Script på klient Sidan

Till skillnad från SDK: er för Server sidan beräknar inte Java Script SDK på klient sidan IP-adress. Som standard utförs IP-adress beräkning för telemetri på klient sidan vid inmatnings slut punkten i Azure när telemetri anländer. Det innebär att om du skickar data på klient sidan till en proxy och sedan vidarebefordrar till inmatnings slut punkten, kan IP-adress beräkningen Visa IP-adressen för proxyservern och inte klienten. Om ingen proxy används bör detta inte vara ett problem.

Om du vill beräkna IP-adressen direkt på klient sidan måste du lägga till din egen anpassade logik för att utföra den här beräkningen och använda resultatet för att ange `ai.location.ip` taggen. När `ai.location.ip` har angetts utförs inte beräkning av IP-adress av inmatnings slut punkten och den angivna IP-adressen används för att utföra geo-sökningen. I det här scenariot är IP-adressen fortfarande nollställd som standard. 

Om du vill behålla hela IP-adressen som beräknas från den anpassade logiken kan du använda en telemetri-initierare som kopierar de IP-Datadata som du angav i `ai.location.ip` till ett separat anpassat fält. Men återigen till skillnad från SDK: er på Server sidan utan att behöva lita på bibliotek från tredje part eller en egen anpassad IP-samling på klient sidan, beräknar SDK för klient sidan inte IP-adressen åt dig.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>Visa resultatet av telemetri-initieraren

Om du sedan utlöser en ny trafik mot din webbplats och väntar cirka 2-5 minuter för att se till att den har tid att mata in, kan du köra en Kusto-fråga för att se om IP-adress samlingen fungerar:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nyligen insamlade IP-adresser ska `customDimensions_client-ip` visas i kolumnen. Standard `client-ip` kolumnen kommer fortfarande att ha alla 4 oktetter antingen noll eller bara visa de första tre oktetterna beroende på hur du har konfigurerat IP-adresspoolen på komponent nivå. Om du testar lokalt när du har implementerat telemetri-initieraren och värdet du ser `customDimensions_client-ip` för `::1` är detta förväntat beteende. `::1`representerar loopback-adressen i IPv6. Den motsvarar `127.0.01` i IPv4 och är resultatet som visas när du testar från localhost.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [insamling av personliga data](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) i Application Insights.

* Lär dig mer om hur [IP-webbplatssamling](https://apmtips.com/blog/2016/07/05/client-ip-address/) i Application Insights fungerar. (Det här är ett äldre externt blogg inlägg som skrivits av någon av våra tekniker. Detta fördaterar det aktuella standard beteendet där IP-adressen registreras `0.0.0.0`som, men den ökar djupet på Mechanics för det inbyggda `ClientIpHeaderTelemetryInitializer`.)