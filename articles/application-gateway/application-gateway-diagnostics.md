---
title: "Övervaka åtkomstloggar, Prestandaloggar, backend-hälsa och mått för Programgateway | Microsoft Docs"
description: "Lär dig att aktivera och hantera åtkomstloggar och Prestandaloggar för Programgateway"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva
ms.openlocfilehash: 12c252340b82aba5ee69b12db83353750782e7c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Backend-hälsotillstånd, diagnostikloggar och mått för Programgateway

Med hjälp av Azure Application Gateway kan övervaka du resurser på följande sätt:

* [Backend-hälsa](#back-end-health): Programgateway ger möjlighet att övervaka hälsotillståndet hos servrarna i backend-pooler via Azure-portalen och via PowerShell. Du kan också hitta hälsotillståndet för backend-pooler via prestanda diagnostikloggar.

* [Loggar](#diagnostic-logs): loggar Tillåt för prestanda, åtkomst och andra data sparas eller förbrukad från en resurs för övervakning.

* [Mått](#metrics): Application Gateway har för närvarande ett enskilt mått. Mätvärdet mäter genomflödet av Programgateway i byte per sekund.

## <a name="back-end-health"></a>Backend-hälsa

Programgateway ger möjlighet att övervaka hälsotillståndet för enskilda medlemmarna i backend-pooler via portalen, PowerShell och kommandoradsgränssnittet (CLI). Du kan också hitta det totala hälsotillståndet för backend-pooler via prestanda diagnostikloggar. 

Backend-hälsorapporten visar utdata för Programgateway hälsoavsökningen till backend-instanser. När avsökning lyckas och serverdelen kan ta emot trafik, är det felfritt. Annars anses vara felaktig.

> [!IMPORTANT]
> Om det finns en nätverkssäkerhetsgrupp (NSG) på ett Application Gateway-undernät, öppna portintervall 65503 65534 i Application Gateway-undernät för inkommande trafik. Dessa portar är obligatoriska för backend-hälsa API för att fungera.


### <a name="view-back-end-health-through-the-portal"></a>Visa backend-hälsa via portalen

Backend-hälsa tillhandahålls automatiskt i portalen. Välj i en befintlig Programgateway **övervakning** > **Backend hälsa**. 

Varje medlem i backend-poolen finns på den här sidan (om det är ett nätverkskort, IP eller FQDN). Backend-namn, port, backend-HTTP-namn och hälsostatus visas. Giltiga värden för hälsostatus **felfri**, **ohälsosam**, och **okänd**.

> [!NOTE]
> Om du ser en backend-hälsostatus **okänd**, med åtkomst till serverdelen inte blockeras av en regel för NSG, en användardefinierad väg (UDR) eller en anpassad DNS i det virtuella nätverket.

![Backend-hälsa][10]

### <a name="view-back-end-health-through-powershell"></a>Visa backend-hälsa via PowerShell

PowerShell följande kod visar hur du visar backend-hälsa genom att använda den `Get-AzureRmApplicationGatewayBackendHealth` cmdlet:

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Visa backend-hälsa genom Azure CLI 2.0

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultat

Följande utdrag visar ett exempel på svaret:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Diagnostikloggar

Du kan använda olika typer av loggar i Azure för att hantera och felsöka programgatewayer. Du kan komma åt vissa av dessa loggar via portalen. Alla loggar kan extraheras från Azure Blob storage och visas i olika verktyg som [logganalys](../log-analytics/log-analytics-azure-networking-analytics.md), Excel och Power BI. Du kan lära dig mer om de olika typerna av loggar från följande lista:

* **Aktivitetsloggen**: du kan använda [Azure aktivitetsloggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) (hette operativa loggar och granskningsloggar) att visa alla åtgärder som skickas till din Azure-prenumeration och deras status. Aktiviteten loggposter som samlas in som standard och du kan visa dem i Azure-portalen.
* **Åtkomstlogg**: du kan använda den här loggen att visa Programgateway åtkomstmönster och analysera viktiga information, inklusive anroparens IP, begärd URL, svar svarstid, returkod och byte in och ut. En åtkomstlogg samlas in varje 300 sekunder. Den här loggfilen innehåller en post per instans av Application Gateway. Application Gateway-instans kan identifieras av instanceId-egenskapen.
* **Prestanda loggen**: du kan använda den här loggen för att visa hur Programgateway instanser utför. Den här loggfilen innehåller prestandainformation om för varje instans, inklusive totalt antal begäranden som betjänats, genomflöde i byte, totalt antal begäranden som betjänats antalet misslyckade begäranden och felfria och feltillstånd backend-instanser. En prestandalogg som samlas in var 60: e sekund.
* **Brandväggsloggen**: du kan använda den här loggfilen för att se begäranden som loggas via identifiering eller förhindra läget för en Programgateway som är konfigurerad med Brandvägg för webbaserade program.

> [!NOTE]
> Loggarna är bara tillgängligt för resurser som har distribuerats i Azure Resource Manager-distributionsmodellen. Du kan inte använda loggar för resurser i den klassiska distributionsmodellen. En bättre förståelse av de två modellerna, finns det [förstå Resource Manager distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md) artikel.

Det finns tre alternativ för att lagra dina loggar:

* **Lagringskontot**: Storage-konton är bäst för loggar när loggar lagras under en längre tid och granskas vid behov.
* **Händelsehubbar**: händelsehubbar är ett bra alternativ för att integrera med andra säkerhetsinformation och händelsehantering (SEIM) verktyg för att få aviseringar för dina resurser.
* **Logga Analytics**: logganalys passar bäst för allmän övervakning i realtid för programmet eller tittar på trender.

### <a name="enable-logging-through-powershell"></a>Aktivera loggning med PowerShell

Aktivitetsloggning aktiveras automatiskt för varje resurs för hanteraren för filserverresurser. Du måste aktivera åtkomst och prestanda som loggning för att börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning:

1. Observera ditt lagringskonto resurs-ID, där loggdata lagras. Det här värdet är i formatet: /subscriptions/\<subscriptionId\>/resourceGroups/\<resursgruppnamn\>/providers/Microsoft.Storage/storageAccounts/\<lagringskontonamnet\>. Du kan använda storage-konto i din prenumeration. Du kan använda Azure-portalen för att hitta den här informationen.

    ![Portal: resurs-ID för lagringskontot](./media/application-gateway-diagnostics/diagnostics1.png)

2. Observera din Programgateway resurs-ID som har aktiverats. Det här värdet är i formatet: /subscriptions/\<subscriptionId\>/resourceGroups/\<resursgruppens namn\>/providers/Microsoft.Network/applicationGateways/\<gateway programnamn\>. Du kan använda portalen för att hitta den här informationen.

    ![Portal: resurs-ID för Programgateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Aktivera diagnostikloggning med hjälp av följande PowerShell-cmdlet:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Aktivitetsloggar kräver inte ett separat lagringskonto. Användningen av lagringsutrymme för åtkomst- och prestandaloggning debiteras tjänsten.

### <a name="enable-logging-through-the-azure-portal"></a>Aktivera loggning via Azure portal

1. Hitta din resurs i Azure-portalen och på **diagnostikloggar**.

   För Programgateway är tre loggarna tillgängliga:

   * Åtkomstlogg
   * Prestandalogg
   * Brandväggen logg

2. Om du vill börja samla in data, klickar du på **aktivera diagnostiken**.

   ![Aktivera diagnostik][1]

3. Den **diagnostikinställningar** bladet innehåller inställningarna för diagnostiska loggar. I det här exemplet lagras logganalys loggarna. Klicka på **konfigurera** under **logganalys** att konfigurera din arbetsyta. Du kan också använda händelsehubbar och ett lagringskonto för att spara diagnostikloggarna.

   ![Starta konfigurationen][2]

4. Välj en befintlig Operations Management Suite (OMS) arbetsyta eller skapa en ny. Det här exemplet använder en befintlig.

   ![Alternativ för OMS arbetsytor][3]

5. Bekräfta inställningarna och klicka på **spara**.

   ![Diagnostik inställningsbladet med val][4]

### <a name="activity-log"></a>Aktivitetslogg

Azure genererar aktivitetsloggen som standard. Loggarna finns kvar i 90 dagar i arkivet Azure händelseloggar. Mer information om de här loggarna genom att läsa den [visa händelser och aktivitetsloggen](../monitoring-and-diagnostics/insights-debugging-with-events.md) artikel.

### <a name="access-log"></a>Åtkomstlogg

Åtkomst-loggen genereras bara om du har aktiverat på varje Application Gateway-instans enligt anvisningarna i föregående steg. Data lagras i storage-konto som du angav när du har aktiverat loggning. Varje åtkomst för Programgateway loggas i JSON-format, som visas i följande exempel:


|Värde  |Beskrivning  |
|---------|---------|
|InstanceId     | Programmet Gateway-instans där begäran behandlades.        |
|ClientIP     | Ursprungliga IP-Adressen för begäran.        |
|clientPort     | Ursprungliga port för begäran.       |
|HttpMethod     | HTTP-metod som används av begäran.       |
|requestUri     | URI för tog emot begäran.        |
|RequestQuery     | **Server-dirigerat**: backend-pool-instans som begäran skickades. </br> **X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som används för begäran. Det kan användas för att felsöka problem med trafik på backend-servrar. </br>**SERVER-STATUS**: HTTP-svarskoden Programgateway togs emot från serverdelen.       |
|UserAgent     | Användaragent från HTTP-huvudet i begäran.        |
|httpStatus     | HTTP-statuskod som returneras till klienten från Application Gateway.       |
|httpVersion     | HTTP-version för begäran.        |
|receivedBytes     | Storleken på paketet tas emot, i byte.        |
|sentBytes| Storleken på paketet skickas, i byte.|
|timeTaken| Lång tid (i millisekunder) som det tar för en begäran att bearbetas och dess svar skickas. Det här beräknas som intervall från den tid när Programgateway tar emot de första byten för en HTTP-begäran mot den tid när åtgärden är slutförd skickar i svaret. Det är viktigt att Observera att fältet Time-Taken vanligtvis innehåller den tid som begäran och svar paket skickas över nätverk. |
|sslEnabled| Om kommunikationen till backend-pooler används SSL. Giltiga värden är på och av.|
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
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>Prestandalogg

Prestanda-loggen genereras bara om du har aktiverat på varje Application Gateway-instans enligt anvisningarna i föregående steg. Data lagras i storage-konto som du angav när du har aktiverat loggning. Loggdata för prestanda genereras på 1 minut. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|InstanceId     |  Programmet Gateway-instans för vilka data som genereras. För en gateway med flera instans programmet finns en rad per instans.        |
|healthyHostCount     | Antalet felfri värdar i backend-poolen.        |
|unHealthyHostCount     | Antalet felaktiga värdar i backend-poolen.        |
|RequestCount     | Antal begäranden som betjänats.        |
|Svarstid | Svarstid (i millisekunder) för förfrågningar från instansen till serverdelen som hanterar begäranden. |
|failedRequestCount| Antal misslyckade begäranden.|
|Dataflöde| Genomsnittlig genomströmning sedan senaste loggen, mätt i byte per sekund.|

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
> Svarstiden är beräknad när den första byten i HTTP-begäran tas emot med tiden då den sista byten av HTTP-svar skickas. Det är summan av bearbetningstid Programgateway plus nätverkskostnad för serverdelen plus den tid som serverdelen som krävs för att bearbeta begäran.

### <a name="firewall-log"></a>Brandväggen logg

Brandväggsloggen genereras bara om du har aktiverat för varje Programgateway enligt anvisningarna i föregående steg. Den här loggfilen kräver också att Brandvägg för webbaserade program har konfigurerats på en Programgateway. Data lagras i storage-konto som du angav när du har aktiverat loggning. Följande data loggas:


|Värde  |Beskrivning  |
|---------|---------|
|InstanceId     | Programmet Gateway-instans för vilken brandvägg genereras data. För en gateway med flera instans programmet finns en rad per instans.         |
|clientIp     |   Ursprungliga IP-Adressen för begäran.      |
|clientPort     |  Ursprungliga port för begäran.       |
|requestUri     | URL till tog emot begäran.       |
|ruleSetType     | Typen regeluppsättning. Tillgängliga värdet är OWASP.        |
|ruleSetVersion     | Regeluppsättning version som används. Tillgängliga värden är 2.2.9 och 3.0.     |
|ruleId     | Regel-ID för den utlösande händelsen.        |
|Meddelande     | Användarvänligt meddelande för utlösande händelsen. Mer information finns i informationsavsnittet.        |
|Åtgärden     |  Åtgärder som vidtas på begäran. Tillgängliga värden är blockerad och tillåtna.      |
|plats     | Plats för vilken loggen skapades. Endast globala visas för närvarande eftersom regler är globala.|
|Information     | Information om den utlösande händelsen.        |
|details.Message     | Beskrivning av regeln.        |
|details.data     | Specifika data hittades i begäran som matchar regeln.         |
|details.File     | Konfigurationsfil som innehåller regeln.        |
|details.Line     | Radnumret i konfigurationsfilen som utlöste händelsen.       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggen

Du kan visa och analysera loggdata för aktiviteten genom att använda någon av följande metoder:

* **Azure-verktyg**: hämta information från aktivitetsloggen via Azure PowerShell, Azure CLI, Azure REST API eller Azure-portalen. Stegvisa instruktioner för varje metod beskrivs i den [aktivitet åtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md) artikel.
* **Power BI**: Om du inte redan har en [Power BI](https://powerbi.microsoft.com/pricing) konto, du kan testa det gratis. Med hjälp av den [Azure aktivitetsloggar content pack för Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), du kan analysera dina data med förkonfigurerade instrumentpaneler som du kan använda eller anpassa.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visa och analysera åtkomst-, prestanda- och brandväggsloggar

Azure [logganalys](../log-analytics/log-analytics-azure-networking-analytics.md) kan samla in prestandaräknare och händelseloggen filer från Blob storage-konto. Det innehåller visualiseringar och avancerade sökfunktioner att analysera dina loggar.

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst och Prestandaloggar. När du har hämtat JSON-filer kan du konvertera dem till CSV och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och grundläggande begrepp för att ändra värdena för variabler i C# och konstanter, kan du använda den [logga konverteraren verktyg](https://github.com/Azure-Samples/networking-dotnet-log-converter) tillgängliga från GitHub.
> 
> 

## <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser där du kan visa prestandaräknare i portalen. För Programgateway är ett enskilt mått tillgängligt nu. Det här måttet är genomflöde och du kan se den i portalen. Bläddra till en Programgateway och på **mått**. Välj om du vill visa värden genomflöde i den **tillgängliga mått** avsnitt. I följande bild kan se du ett exempel med de filter som du kan använda för att visa data i olika tidsintervall.

![Måttvy med filter][5]

Om du vill se en lista över mått finns [stöds mått med Azure-Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

### <a name="alert-rules"></a>Aviseringsregler

Du kan starta Varningsregler baserat på mått för en resurs. En avisering kan anropa en webhook eller e-administratör om genomflödet av programgatewayen är över, under eller med ett tröskelvärde för en angiven period.

I följande exempel vägleder dig genom att skapa en aviseringsregel som skickar ett e-postmeddelande till en administratör efter genomströmning överträdelser en tröskel:

1. Klicka på **Lägg till mått avisering** att öppna den **Lägg till regel** bladet. Du kan också nå det här bladet från bladet mått.

   ![Knappen ”Lägg till mått aviseringen”][6]

2. På den **Lägg till regel** bladet fylla i namn, tillstånd, meddela avsnitt och klickar på **OK**.

   * I den **villkoret** selector, Välj ett av de fyra värdena: **större än**, **större än eller lika med**, **mindre än**, eller **mindre än eller lika med**.

   * I den **Period** selector, Välj en period mellan 5 och 6 timmar.

   * Om du väljer **e-ägare, deltagare och läsare**, e-postmeddelandet kan vara dynamiska baserat på de användare som har åtkomst till resursen. Annars kan du ange en kommaavgränsad lista över användare i den **ytterligare administratören email(s)** rutan.

   ![Lägg till regel bladet][7]

Om tröskelvärdet överskrids, kommer ett e-postmeddelande som liknar det i följande bild:

![E-post för utsatts för intrång tröskelvärde][8]

En lista över aviseringar visas när du skapar en avisering om mått. Den innehåller en översikt över alla Varningsregler.

![Listan över aviseringar och regler][9]

Mer information om aviseringar finns [få aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Om du vill veta mer om webhooks och hur du kan använda dem med aviseringar finns [konfigurera en webhook på en Azure mått avisering](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknare och händelseloggar med [logganalys](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Visualisera dina Azure-aktivitetsloggen med Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogginlägg.
* [Visa och analysera Azure aktivitetsloggar i Power BI och mycket mer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
