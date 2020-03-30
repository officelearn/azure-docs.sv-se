---
title: IP-adresssamling för Azure Application Insights | Microsoft-dokument
description: Förstå hur IP-adresser och geolokalisering hanteras med Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656525"
---
# <a name="geolocation-and-ip-address-handling"></a>Geolokalisering och IP-adresshantering

I den här artikeln beskrivs hur geolokaliseringshantering och IP-adresshantering sker i Application Insights tillsammans med hur du ändrar standardbeteendet.

## <a name="default-behavior"></a>Standardbeteende

Som standard samlas IP-adresser in tillfälligt, men lagras inte i Application Insights. Den grundläggande processen ser ut så här:

IP-adresser skickas till Application Insights som en del av telemetridata. När ip-adressen når slutpunkten för inmatning i Azure används den för att utföra en geolokaliseringssökning med [GeoLite2 från MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Resultatet av den här sökningen används för `client_City` `client_StateOrProvince`att `client_CountryOrRegion`fylla i följande fält , , . Nu ignoreras IP-adressen och `0.0.0.0` skrivs till `client_IP` fältet.

* Browser telemetri: Vi samlar tillfälligt avsändarens IP-adress. IP-adressen beräknas av slutpunkten för inmatning.
* Servertelemetri: Modulen Application Insights samlar tillfälligt in klient-IP-adressen. Det samlas inte `X-Forwarded-For` in om den är inställd.

Detta beteende är avsiktligt för att undvika onödig insamling av personuppgifter. När det är möjligt rekommenderar vi att man undviker insamling av personuppgifter. 

## <a name="overriding-default-behavior"></a>Åsidosätta standardbeteende

Även om standardbeteendet är att minimera insamlingen av personuppgifter, erbjuder vi fortfarande flexibiliteten att samla in och lagra IP-adressdata. Innan du väljer att lagra personuppgifter som IP-adresser rekommenderar vi starkt att du kontrollerar att detta inte bryter mot några efterlevnadskrav eller lokala bestämmelser som du kan omfattas av. Mer information om hantering av personuppgifter i Application Insights finns i [vägledningen för personuppgifter](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Lagra IP-adressdata

För att möjliggöra IP-insamling `DisableIpMasking` och lagring måste egenskapen för `true`application insights-komponenten anges till . Den här egenskapen kan ställas in antingen via Azure Resource Manager-mallar eller genom att anropa REST API. 

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

Om du bara behöver ändra beteendet för en enda Application Insights-resurs är det enklaste sättet att åstadkomma detta via Azure-portalen.  

1. Gå till >**exportmallen** **för** > programstatistik 

    ![Exportera mall](media/ip-collection/export-template.png)

2. Välj **distribuera**

    ![Knappen Distribuera markerad i rött](media/ip-collection/deploy.png)

3. Välj **Redigera mall**. (Om mallen har ytterligare egenskaper eller resurser som inte visas i den här exempelmallen bör du vara försiktig med att se till att alla resurser accepterar malldistributionen som en inkrementell ändring/uppdatering.)

    ![Redigera mall](media/ip-collection/edit-template.png)

4. Gör följande ändringar i json för din resurs och klicka sedan på **Spara:**

    ![Skärmdump lägger till ett kommatecken efter "IbizaAIExtension" och lägga till en ny rad nedan med "DisableIpMasking": sant](media/ip-collection/save.png)

    > [!WARNING]
    > Om du får ett felmeddelande som säger: ** _Resursgruppen finns på en plats som inte stöds av en eller flera resurser i mallen. Välj en annan resursgrupp._** Välj tillfälligt en annan resursgrupp i listrutan och välj sedan om den ursprungliga resursgruppen för att lösa felet.

5. Välj **Jag godkänner** > **Köp**. 

    ![Redigera mall](media/ip-collection/purchase.png)

    I det här fallet inget nytt köps, uppdaterar vi bara config av den befintliga Application Insights resurs.

6. När distributionen är klar kommer nya telemetridata att registreras.

    Om du skulle välja och redigera mallen igen skulle du bara se standardmallen och skulle inte se din nyligen tillagda egenskap och dess associerade värde. Om du inte ser IP-adressdata och `"DisableIpMasking": true` vill bekräfta att det är inställt. Kör följande PowerShell: `Fabrikam-dev` (Ersätt med lämpligt resurs- och resursgruppsnamn.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    En lista över egenskaper returneras som ett resultat. En av egenskaperna `DisableIpMasking: true`ska läsa . Om du kör PowerShell innan du distribuerar den nya egenskapen med Azure Resource Manager finns inte egenskapen.

### <a name="rest-api"></a>API för vila

[Resten API](https://docs.microsoft.com/rest/api/azure/) nyttolast för att göra samma ändringar är följande:

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

## <a name="telemetry-initializer"></a>Telemetriinitierare

Om du behöver ett `DisableIpMasking` mer flexibelt alternativ än att registrera hela eller delar av IP-adresser kan du använda en [telemetriinitierare](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) för att kopiera hela eller delar IP-adressen till ett anpassat fält. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET Kärna

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
> Om du inte kan `ISupportProperties`komma åt kontrollerar du och kontrollerar att du kör den senaste stabila versionen av Application Insights SDK. `ISupportProperties`är avsedda för höga kardinalitetsvärden, medan `GlobalProperties` de är lämpligare för låga kardinalitetsvärden som regionnamn, miljönamn etc. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Aktivera telemetriinitierare för ASP.NET

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

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Aktivera telemetriinitierare för ASP.NET Core

Du kan skapa telemetriinitieraren på samma sätt för ASP.NET Core som ASP.NET men för att aktivera initialiseraren använder du följande exempel som referens:

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

### <a name="client-side-javascript"></a>JavaScript för klientsidan

Till skillnad från SDK:erna på serversidan beräknar inte Javascript SDK på klientsidan IP-adressen. Som standard IP-adress beräkning för telemetri på klientsidan utförs vid inmatningslutpunkten i Azure vid telemetriankomst. Detta innebär att om du skickar klientdata till en proxy och sedan vidarebefordrar till slutpunkten för inmatning, kan IP-adressberäkningen visa PROXYNS IP-adress och inte klienten. Om ingen proxy används bör detta inte vara ett problem.

Om du vill beräkna IP-adress direkt på klientsidan måste du lägga till din egen anpassade `ai.location.ip` logik för att utföra den här beräkningen och använda resultatet för att ställa in taggen. När `ai.location.ip` är inställt utförs inte IP-adressberäkningen av slutpunkten för inmatning och den angivna IP-adressen är uppstämd och används för att utföra geo-sökningen. I det här fallet nollställs IP-adressen fortfarande som standard. 

Om du vill behålla hela IP-adressen som beräknas utifrån din anpassade logik kan du använda `ai.location.ip` en telemetriinitierare som kopierar IP-adressdata som du angav i ett separat anpassat fält. Men återigen till skillnad från serversidan SDK: er, utan att förlita sig på 3: e parts bibliotek eller din egen anpassade klient-side IP-samling logik klientsidan SDK kommer inte att beräkna IP för dig.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Visa resultaten av din telemetriinitierare

Om du sedan utlöser ny trafik mot din webbplats och väntar cirka 2-5 minuter för att säkerställa att den hade tid att förtäras, kan du köra en Kusto-fråga för att se om IP-adresssamling fungerar:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nyligen insamlade IP-adresser `customDimensions_client-ip` ska visas i kolumnen. `client-ip` Standardkolumnen har fortfarande alla 4 oktetter antingen nollställda eller bara visar de tre första oktetterna beroende på hur du har konfigurerat IP-adresssamling på komponentnivå. Om du testar lokalt efter implementering av telemetriinitieraren och `customDimensions_client-ip` `::1` värdet du ser för är detta förväntat beteende. `::1`representerar loopback-adressen i IPv6. Det motsvarar `127.0.01` i IPv4 och är det resultat du kommer att se när du testar från localhost.

## <a name="next-steps"></a>Efterföljande moment

* Läs mer om [insamling av personuppgifter](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) i Application Insights.

* Läs mer om hur [IP-adresssamling](https://apmtips.com/blog/2016/07/05/client-ip-address/) i Application Insights fungerar. (Detta är en äldre externt blogginlägg skrivet av en av våra ingenjörer. Det föregår det aktuella standardbeteendet `0.0.0.0`där IP-adressen registreras som , men det `ClientIpHeaderTelemetryInitializer`går in mer på djupet på mekaniken i den inbyggda .)
