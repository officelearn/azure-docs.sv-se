---
title: Övervaka loggar för Azure Web Application-brandväggen
description: Lär dig hur du aktiverar och hanterar loggar och för Azure Web Application Firewall
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: cb1af86e04c0b4ba0b59398161fa111fd8065042
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310057"
---
# <a name="diagnostic-logs-for-azure-web-application-firewall"></a>Diagnostikloggar för Azure Web Application-brandväggen

Du kan övervaka webbprogram brandväggsresurser med hjälp av loggar. Du kan spara prestanda, åtkomst och andra data eller använda dem från en resurs i övervakningssyfte.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Diagnostikloggar

Du kan använda olika typer av loggar i Azure för att hantera och felsöka programgateways. Du kan komma åt vissa av de här loggarna via portalen. Alla loggar kan extraheras från Azure Blob-lagring och visas i olika verktyg, till exempel [Azure Monitor-loggar,](../../azure-monitor/insights/azure-networking-analytics.md)Excel och Power BI. Du kan läsa mer om de olika typerna av loggar från följande lista:

* **Aktivitetslogg:** Du kan använda [Azure-aktivitetsloggar](../../azure-resource-manager/management/view-activity-logs.md) (tidigare kallade driftloggar och granskningsloggar) för att visa alla åtgärder som skickas till din Azure-prenumeration och deras status. Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.
* **Åtkomstlogg:** Du kan använda den här loggen för att visa åtkomstmönster för Application Gateway och analysera viktig information. Detta inkluderar anroparens IP, begärd URL, svarssvarstid, returkod och byte in och ut. En åtkomstlogg samlas in var 300:e sekund. Den här loggen innehåller en post per instans av Application Gateway. Application Gateway-instansen identifieras av egenskapen instanceId.
* **Prestandalogg:** Du kan använda den här loggen för att visa hur Application Gateway-instanser fungerar. Den här loggen samlar in prestandainformation för varje instans, inklusive totala begäranden, dataflöde i byte, totalt antal begäranden, antal misslyckade begäranden och felfritt antal serverd-instanser. En prestandalogg samlas in var 60:e sekund. Prestandaloggen är endast tillgänglig för v1 SKU. För v2 SKU använder du [Mått](../../application-gateway/application-gateway-metrics.md) för prestandadata.
* **Brandväggslogg:** Du kan använda den här loggen för att visa de begäranden som loggas via identifierings- eller förebyggande läge för en programgateway som är konfigurerad med brandväggen för webbprogrammet.

> [!NOTE]
> Loggar är endast tillgängliga för resurser som distribueras i Azure Resource Manager-distributionsmodellen. Du kan inte använda loggar för resurser i den klassiska distributionsmodellen. För en bättre förståelse av de två modellerna, se [Förstå Resurshanteraren distribution och klassisk distribution](../../azure-resource-manager/management/deployment-models.md) artikel.

Du har tre alternativ för att lagra dina loggar:

* **Storage-konto**: Storage-konton passar bäst när loggarna ska lagras en längre tid och granskas vid behov.
* **Event hubbar:** Event hubbar är ett bra alternativ för att integrera med andra säkerhetsinformation och händelsehantering (SIEM) verktyg för att få aviseringar på dina resurser.
* **Azure Monitor loggar:** Azure Monitor loggar används bäst för allmän realtidsövervakning av ditt program eller titta på trender.

### <a name="enable-logging-through-powershell"></a>Aktivera loggning via PowerShell

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Du måste aktivera åtkomst- och prestandaloggning för att kunna börja samla in tillgängliga data via dessa loggar. Så här aktiverar du loggning:

1. Anteckna resurs-ID:t för det lagringskonto där loggdata lagras. Det här värdet har följande format: /subscriptions/\<prenumerations-ID\>/resourceGroups/\<resursgruppsnamn\>/providers/Microsoft.Storage/storageAccounts/\<lagringskontonamn\>. Du kan använda valfritt lagringskonto i din prenumeration. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för lagringskonto](../media/web-application-firewall-logs/diagnostics1.png)

2. Observera programgatewayens resurs-ID som loggning är aktiverat för. Det här värdet är av formuläret\<:/subscriptions/ subscriptionId\>\</resourceGroups/ resource group name\>\</providers/Microsoft.Network/applicationGateways/application gateway name\>. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för programgateway](../media/web-application-firewall-logs/diagnostics2.png)

3. Aktivera diagnostisk loggning med följande PowerShell-cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Aktivitetsloggar kräver inget separat lagringskonto. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

### <a name="enable-logging-through-the-azure-portal"></a>aktivera loggning via Azure Portal

1. Leta reda på din resurs i Azure-portalen och välj **Diagnostikinställningar**.

   För Application Gateway finns tre loggar tillgängliga:

   * Åtkomstlogg
   * Prestandalogg
   * Brandväggslogg

2. Om du vill börja samla in data väljer du **Aktivera diagnostik**.

   ![Aktivera diagnostik][1]

3. På sidan **Diagnostikinställningar** kan du göra inställningar för diagnostikloggarna. I det här exemplet lagrar Log Analytics loggarna. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![Starta konfigurationsprocessen][2]

5. Skriv ett namn på inställningarna, bekräfta inställningarna och välj **Spara**.

### <a name="activity-log"></a>Aktivitetslogg

Azure genererar aktivitetsloggen som standard. Loggarna bevaras i 90 dagar i Azure-händelseloggarkivet. Läs mer om dessa loggar genom att läsa artikeln [Visa händelser och aktivitetslogg.](../../azure-resource-manager/management/view-activity-logs.md)

### <a name="access-log"></a>Åtkomstlogg

Åtkomstloggen genereras bara om du har aktiverat den på varje Application Gateway-instans, enligt beskrivningen i föregående steg. Data lagras i det lagringskonto som du angav när du aktiverade loggningen. Varje åtkomst till Application Gateway loggas i JSON-format, vilket visas i följande exempel för v1:

|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway-instans som serverade begäran.        |
|klientIP     | Ursprungs-IP för begäran.        |
|clientPort (klientPort)     | Ursprungsport för begäran.       |
|httpMethod (på)     | HTTP-metod som används av begäran.       |
|begärUri     | URI för den mottagna begäran.        |
|Begäranstry     | **Server-dirigerad**: Backend poolinstans som skickades begäran.</br>**X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som används för begäran. Den kan användas för att felsöka trafikproblem på backend-servrarna. </br>**SERVER-STATUS**: HTTP-svarskod som Application Gateway har tagits emot från serverdelen.       |
|Useragent     | Användaragent från HTTP-begäranden.        |
|httpStatus (Status)     | HTTP-statuskoden returneras till klienten från Application Gateway.       |
|httpVersion (På ett sätt)     | HTTP-version av begäran.        |
|receivedBytes     | Storleken på det mottagna paketet, i byte.        |
|sentByte| Storleken på paketet som skickas, i byte.|
|timeTaken (timeTaken)| Tid (i millisekunder) som det tar för en begäran som ska behandlas och dess svar skickas. Detta beräknas som intervallet från den tidpunkt då Application Gateway tar emot den första bytet av en HTTP-begäran till den tidpunkt då svarssändningsåtgärden avslutas. Det är viktigt att notera att fältet Tidstaget vanligtvis innehåller den tid som förfrå och svarspaketen färdas över nätverket. |
|sslEnabled| Om kommunikation till backend-poolerna använde TLS/SSL. Giltiga värden är på och av.|
|värd| Värdnamnet som begäran har skickats till serverdservern för server. Om värdnamn för backend åsidosätts återspeglar det här namnet det.|
|originalHost (originalHost)| Värdnamnet som begäran togs emot med av Application Gateway från klienten.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
För Application Gateway och WAF v2 visar loggarna lite mer information:

|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway-instans som serverade begäran.        |
|klientIP     | Ursprungs-IP för begäran.        |
|clientPort (klientPort)     | Ursprungsport för begäran.       |
|httpMethod (på)     | HTTP-metod som används av begäran.       |
|begärUri     | URI för den mottagna begäran.        |
|Useragent     | Användaragent från HTTP-begäranden.        |
|httpStatus (Status)     | HTTP-statuskoden returneras till klienten från Application Gateway.       |
|httpVersion (På ett sätt)     | HTTP-version av begäran.        |
|receivedBytes     | Storleken på det mottagna paketet, i byte.        |
|sentByte| Storleken på paketet som skickas, i byte.|
|timeTaken (timeTaken)| Tid (i millisekunder) som det tar för en begäran som ska behandlas och dess svar skickas. Detta beräknas som intervallet från den tidpunkt då Application Gateway tar emot den första bytet av en HTTP-begäran till den tidpunkt då svarssändningsåtgärden avslutas. Det är viktigt att notera att fältet Tidstaget vanligtvis innehåller den tid som förfrå och svarspaketen färdas över nätverket. |
|sslEnabled| Om kommunikation till backend-poolerna använde TLS. Giltiga värden är på och av.|
|sslCipher (sslCipher)| Cipher-svit som används för TLS-kommunikation (om TLS är aktiverat).|
|sslProtocol (sslProtocol)| TLS-protokollet används (om TLS är aktiverat).|
|serverUtträckt| Den server som programmets gateway dirigerar begäran till.|
|serverStatus| HTTP-statuskod för serverdservern.|
|serverResponseLatency| Svarstid för svaret från serverdservern.|
|värd| Adress som anges i värdhuvudet för begäran.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Prestandalogg

Prestandaloggen genereras bara om du har aktiverat den på varje Application Gateway-instans, enligt beskrivningen i föregående steg. Data lagras i det lagringskonto som du angav när du aktiverade loggningen. Prestandaloggdata genereras i 1-minutersintervall. Den är endast tillgänglig för v1 SKU. För v2 SKU använder du [Mått](../../application-gateway/application-gateway-metrics.md) för prestandadata. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|instanceId     |  Application Gateway-instans för vilken prestandadata genereras. För en programgateway med flera instanser finns det en rad per instans.        |
|friskaHostCount     | Antal friska värdar i backend-poolen.        |
|unHealthyHostCount     | Antal felaktiga värdar i backend-poolen.        |
|requestCount(requestCount)     | Antal begäranden som har delgivits.        |
|Latens | Genomsnittlig svarstid (i millisekunder) för begäranden från instansen till serverdelen som betjänar begäranden. |
|misslyckadesRequestCount| Antal misslyckade begäranden.|
|dataflöde| Genomsnittligt dataflöde sedan den senaste loggen, mätt i byte per sekund.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Svarstiden beräknas från den tidpunkt då den första bytet av HTTP-begäran tas emot till den tidpunkt då den sista bytet av HTTP-svaret skickas. Det är summan av application gateway-bearbetningstiden plus nätverkskostnaden till baksidan, plus den tid som backend tar att bearbeta begäran.

### <a name="firewall-log"></a>Brandväggslogg

Brandväggsloggen genereras bara om du har aktiverat den för varje programgateway, enligt beskrivningen i föregående steg. Den här loggen kräver också att brandväggen för webbprogrammet är konfigurerad på en programgateway. Data lagras i det lagringskonto som du angav när du aktiverade loggningen. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway-instans för vilken brandväggsdata genereras. För en programgateway med flera instanser finns det en rad per instans.         |
|klientTips     |   Ursprungs-IP för begäran.      |
|clientPort (klientPort)     |  Ursprungsport för begäran.       |
|begärUri     | URL för den mottagna begäran.       |
|ruleSetType     | Typ av regeluppsättning. Det tillgängliga värdet är OWASP.        |
|regelSetVersion     | Regeluppsättningsversion används. Tillgängliga värden är 2.2.9 och 3.0.     |
|ruleId (härska)     | Regel-ID för den utlösande händelsen.        |
|meddelande     | Användarvänligt meddelande för utlösande händelse. Mer information finns i informationsavsnittet.        |
|åtgärd     |  Åtgärder som vidtagits på begäran. Tillgängliga värden är blockerade och tillåtna.      |
|webbplats     | Plats som loggen skapades för. För närvarande visas endast Global eftersom reglerna är globala.|
|Detaljer     | Information om utlösande händelse.        |
|details.message     | Beskrivning av regeln.        |
|detaljer.data     | Specifika data hittades i begäran som matchade regeln.         |
|details.file     | Konfigurationsfil som innehöll regeln.        |
|details.line (på andra)     | Radnummer i konfigurationsfilen som utlöste händelsen.       |
|Värdnamn   | Värdnamn eller IP-adress för programgatewayen.    |
|transaktionId  | Unikt ID för en viss transaktion som hjälper till att gruppera flera regelöverträdelser som inträffade inom samma begäran.   |
|policyId   | Unikt ID för brandväggsprincipen som är associerad med programgateway, lyssnare eller sökväg.   |
|policyScope (principScope)    | Platsen för principen - värden kan vara "Global", "Lyssnare" eller "Plats".   |
|policyScopeName   | Namnet på objektet där principen tillämpas.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg**: Hämta information från aktivitetsloggen via Azure PowerShell, Azure-CLI:t, Azure REST-API:t eller Azure Portal. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../../azure-resource-manager/management/view-activity-logs.md).
* **Power BI**: Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Genom att använda [Power BI-mallapparna](https://docs.microsoft.com/power-bi/service-template-apps-overview)kan du analysera dina data.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visa och analysera åtkomst-, prestanda- och brandväggsloggar

[Azure Monitor-loggar](../../azure-monitor/insights/azure-networking-analytics.md) kan samla in räknar- och händelseloggfiler från ditt Blob-lagringskonto. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analysera accessloggar via GoAccess

Vi har publicerat en Resource Manager-mall [GoAccess](https://goaccess.io/) som installerar och kör den populära GoAccess-logganalysatorn för Application Gateway Access Logs. GoAccess tillhandahåller värdefull HTTP-trafikstatistik som unika besökare, begärda filer, värdar, operativsystem, webbläsare, HTTP-statuskoder med mera. Mer information finns [i Readme-filen i mallmappen Resurshanteraren i GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknar- och händelseloggar med hjälp av [Azure Monitor-loggar](../../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure-aktivitetslogg med Power](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) BI-blogginlägg.
* [Visa och analysera Azure-aktivitetsloggar i Power BI och mer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
