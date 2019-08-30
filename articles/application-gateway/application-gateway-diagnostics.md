---
title: Övervaka åtkomst loggar, prestanda loggar, Server dels hälsa och mått för Azure Application Gateway
description: Lär dig hur du aktiverar och hanterar åtkomst loggar och prestanda loggar för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: victorh
ms.openlocfilehash: d9b0c551cdfb92b380a967aaa5bdce7c278fd39e
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183583"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Backend-hälsa, diagnostikloggar och mått för Application Gateway

Genom att använda Azure Application Gateway kan du övervaka resurser på följande sätt:

* [Backend-hälsa](#back-end-health): Application Gateway ger möjlighet att övervaka servrarnas hälso tillstånd i backend-poolerna via Azure Portal och via PowerShell. Du kan också hitta hälso tillståndet för backend-poolerna via diagnostikloggar för prestanda.

* [Loggar](#diagnostic-logging): Loggar gör att prestanda, åtkomst och andra data kan sparas eller förbrukas från en resurs i övervaknings syfte.

* [Mått](#metrics): Application Gateway har för närvarande sju mått för att visa prestanda räknare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Server dels hälsa

Application Gateway ger möjlighet att övervaka hälsan för enskilda medlemmar i backend-pooler via portalen, PowerShell och kommando rads gränssnittet (CLI). Du kan också hitta en samlad hälso översikt över backend-pooler med hjälp av diagnostikloggar för prestanda. 

Backend-hälsorapporten visar utdata från den Application Gateway hälso avsökningen till backend-instanserna. När avsökningen lyckas och Server delen kan ta emot trafik, betraktas den som felfri. Annars betraktas den som ohälsosam.

> [!IMPORTANT]
> Om det finns en nätverks säkerhets grupp (NSG) i ett Application Gateway undernät öppnar du Port intervall 65503-65534 i Application Gateway-undernätet för inkommande trafik. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunder av dessa gateways, inte att kunna initiera några ändringar på dessa slut punkter.


### <a name="view-back-end-health-through-the-portal"></a>Visa Server dels hälsa via portalen

I portalen tillhandahålls Server dels hälsa automatiskt. I en befintlig Application Gateway väljer du **övervakning** > av**Server dels hälsa**. 

Varje medlem i backend-poolen visas på den här sidan (oavsett om det är ett nätverkskort, en IP-adress eller ett fullständigt domän namn). Namn på backend-pool, port, backend-HTTP-inställningar och hälso status visas. Giltiga värden för hälso status är **felfria**, **felaktiga**och **okända**.

> [!NOTE]
> Om du ser en backend-hälsostatus som är **okänd**kontrollerar du att åtkomsten till Server delen inte blockeras av en NSG-regel, en användardefinierad väg (UDR) eller en anpassad DNS i det virtuella nätverket.

![Server dels hälsa][10]

### <a name="view-back-end-health-through-powershell"></a>Visa backend-hälsa via PowerShell

Följande PowerShell-kod visar hur du visar backend-hälsa med hjälp `Get-AzApplicationGatewayBackendHealth` av cmdleten:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Visa backend-hälsa via Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultat

Följande fragment visar ett exempel på svaret:

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

Du kan använda olika typer av loggar i Azure för att hantera och felsöka programgatewayer. Du kan komma åt vissa av de här loggarna via portalen. Alla loggar kan extraheras från Azure Blob Storage och visas i olika verktyg, till exempel [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md), Excel och Power BI. Du kan lära dig mer om de olika typerna av loggar i följande lista:

* **Aktivitets logg**: Du kan använda [Azure aktivitets loggar](../monitoring-and-diagnostics/insights-debugging-with-events.md) (tidigare kallade drift loggar och gransknings loggar) om du vill visa alla åtgärder som skickas till din Azure-prenumeration och deras status. Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.
* **Åtkomst logg**: Du kan använda den här loggen för att Visa Application Gateway åtkomst mönster och analysera viktig information. Detta inkluderar anroparens IP, begärd URL, svars svars tid, retur kod och byte in och ut. En åtkomst logg samlas in var 300: e sekund. Den här loggen innehåller en post per instans av Application Gateway. Application Gateway-instansen identifieras av egenskapen instanceId.
* **Prestanda logg**: Du kan använda den här loggen för att se hur Application Gateway instanser presterar. Den här loggen samlar in prestanda information för varje instans, inklusive totalt antal betjänade begär Anden, data flöde i byte, totalt antal begär Anden, misslyckade begär Anden, antal misslyckade och felfria backend-instanser. En prestanda logg samlas in var 60: e sekund.
* **Brand Väggs logg**: Du kan använda den här loggen för att visa de begär Anden som loggas via antingen identifierings-eller skydds läget för en Programgateway som är konfigurerad med brand väggen för webbaserade program.

> [!NOTE]
> Loggar är endast tillgängliga för resurser som distribueras i Azure Resource Manager distributions modell. Du kan inte använda loggar för resurser i den klassiska distributions modellen. En bättre förståelse för de två modellerna finns i artikeln [förstå distribution av Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md) .

Du har tre alternativ för att lagra dina loggar:

* **Lagringskonto**: Lagrings konton används bäst för loggar när loggar lagras under en längre tid och granskas vid behov.
* **Event Hub**: Event Hub är ett bra alternativ för att integrera med andra verktyg för säkerhets informations-och händelse hantering (SIEM) för att få aviseringar om dina resurser.
* **Azure Monitor loggar**: Azure Monitor loggar används bäst för allmänt real tids övervakning av ditt program eller tittar på trender.

### <a name="enable-logging-through-powershell"></a>Aktivera loggning via PowerShell

Aktivitetsloggning är automatiskt aktiverad för alla Resource Manager-resurser. Du måste aktivera åtkomst-och prestanda loggning för att kunna börja samla in data som är tillgängliga via dessa loggar. Använd följande steg för att aktivera loggning:

1. Anteckna resurs-ID:t för det lagringskonto där loggdata lagras. Det här värdet är av formatet:/Subscriptions/\<subscriptionId\>/resourceGroups/\<resurs grupp namn\>/providers/Microsoft.Storage/storageAccounts/\<lagrings konto namn\>. Du kan använda valfritt lagringskonto i din prenumeration. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för lagrings konto](./media/application-gateway-diagnostics/diagnostics1.png)

2. Anteckna det resurs-ID för Application Gateway som loggning har Aktiver ATS för. Det här värdet är av formatet:/Subscriptions/\<subscriptionId\>/resourceGroups/\<resurs grupp namn\>/providers/Microsoft.Network/applicationGateways/\<Application Gateway Name \>. Du hittar den här informationen i Azure Portal.

    ![Portal: resurs-ID för Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Aktivera diagnostisk loggning med följande PowerShell-cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Aktivitets loggar kräver inget separat lagrings konto. När du använder lagring för åtkomst- och prestandaloggning debiteras avgifter för tjänsten.

### <a name="enable-logging-through-the-azure-portal"></a>aktivera loggning via Azure Portal

1. Leta upp resursen och välj **diagnostikinställningar**i Azure Portal.

   För Application Gateway är tre loggar tillgängliga:

   * Åtkomst logg
   * Prestanda logg
   * Brand Väggs logg

2. Om du vill börja samla in data väljer du **Aktivera diagnostik**.

   ![Aktivera diagnostik][1]

3. På sidan **Diagnostikinställningar** kan du göra inställningar för diagnostikloggarna. I det här exemplet lagrar Log Analytics loggarna. Du kan också använda händelsehubbar och ett lagringskonto till att spara dina diagnostiska loggar.

   ![Starta konfigurations processen][2]

5. Ange ett namn för inställningarna, bekräfta inställningarna och välj **Spara**.

### <a name="activity-log"></a>Aktivitetslogg

Azure genererar aktivitets loggen som standard. Loggarna bevaras för 90 dagar i Azure Event logs-butiken. Läs mer om dessa loggar genom att läsa artikeln [Visa händelser och aktivitets logg](../monitoring-and-diagnostics/insights-debugging-with-events.md) .

### <a name="access-log"></a>Åtkomst logg

Åtkomst loggen skapas endast om du har aktiverat den på varje Application Gateway instans, enligt beskrivningen i föregående steg. Data lagras i det lagrings konto som du angav när du aktiverade loggningen. Varje åtkomst till Application Gateway loggas i JSON-format, som du ser i följande exempel för v1:

|Value  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway instans som hanterade begäran.        |
|clientIP     | Ursprunglig IP för begäran.        |
|clientPort     | Ursprunglig port för begäran.       |
|httpMethod     | HTTP-metod som används av begäran.       |
|requestUri     | URI för den mottagna begäran.        |
|RequestQuery     | **Server-dirigerad**: Backend-tjänstinstans som skickade begäran.</br>**X-AzureApplicationGateway-LOG-ID**: Korrelations-ID som används för begäran. Den kan användas för att felsöka trafik problem på backend-servrarna. </br>**SERVER-STATUS**: HTTP-svarskod som Application Gateway tas emot från Server delen.       |
|UserAgent     | Användar agent från rubriken HTTP-begäran.        |
|httpStatus     | HTTP-statuskod som returnerades till klienten från Application Gateway.       |
|httpVersion     | HTTP-version för begäran.        |
|receivedBytes     | Storleken på det mottagna paketet, i byte.        |
|sentBytes| Storlek på paket som skickas, i byte.|
|timeTaken| Tids längd (i millisekunder) som det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som intervallet från den tidpunkt då Application Gateway tar emot den första byten i en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att det tidsödande fältet vanligt vis innehåller den tid som paket för begäran och svar överförs över nätverket. |
|sslEnabled| Huruvida kommunikationen med backend-pooler använder SSL. Giltiga värden är på och av.|
|host| Det värdnamn som begäran har skickats till backend-servern. Om backend-värdnamnet åsidosätts visas detta namn.|
|originalHost| Det värdnamn som begäran togs emot av Application Gateway från klienten.|
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

|Value  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway instans som hanterade begäran.        |
|clientIP     | Ursprunglig IP för begäran.        |
|clientPort     | Ursprunglig port för begäran.       |
|httpMethod     | HTTP-metod som används av begäran.       |
|requestUri     | URI för den mottagna begäran.        |
|UserAgent     | Användar agent från rubriken HTTP-begäran.        |
|httpStatus     | HTTP-statuskod som returnerades till klienten från Application Gateway.       |
|httpVersion     | HTTP-version för begäran.        |
|receivedBytes     | Storleken på det mottagna paketet, i byte.        |
|sentBytes| Storlek på paket som skickas, i byte.|
|timeTaken| Tids längd (i millisekunder) som det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som intervallet från den tidpunkt då Application Gateway tar emot den första byten i en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att det tidsödande fältet vanligt vis innehåller den tid som paket för begäran och svar överförs över nätverket. |
|sslEnabled| Huruvida kommunikationen med backend-pooler använder SSL. Giltiga värden är på och av.|
|sslCipher| Chiffrering som används för SSL-kommunikation (om SSL är aktiverat).|
|sslProtocol| Det SSL-protokoll som används (om SSL är aktiverat).|
|serverRouted| Backend-servern som Application Gateway dirigerar begäran till.|
|serverStatus| HTTP-statuskod för backend-servern.|
|serverResponseLatency| Svars tid för svaret från backend-servern.|
|host| Adress som anges i värd rubriken för begäran.|
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

### <a name="performance-log"></a>Prestanda logg

Prestanda loggen skapas endast om du har aktiverat den på varje Application Gateway instans, enligt beskrivningen i föregående steg. Data lagras i det lagrings konto som du angav när du aktiverade loggningen. Prestanda logg data genereras i intervall på 1 minut. Följande data loggas:


|Value  |Beskrivning  |
|---------|---------|
|instanceId     |  Application Gateway instans som prestanda data ska skapas för. För en Application Gateway med flera instanser finns det en rad per instans.        |
|healthyHostCount     | Antal felfria värdar i backend-poolen.        |
|unHealthyHostCount     | Antalet värdar som inte är felfria i backend-poolen.        |
|requestCount     | Antal begär Anden som hanteras.        |
|svarstid | Genomsnittlig svars tid (i millisekunder) för begär Anden från instansen till Server delen som hanterar begär Anden. |
|failedRequestCount| Antal misslyckade förfrågningar.|
|throughput| Genomsnittligt data flöde sedan den senaste loggen mätt i byte per sekund.|

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
> Svars tiden beräknas från den tidpunkt då den första byten i HTTP-begäran tas emot till den tidpunkt då den sista byten av HTTP-svaret skickas. Det är summan av Application Gateway bearbetnings tiden plus nätverks kostnaden till Server delen, plus den tid som server delen tar för att bearbeta begäran.

### <a name="firewall-log"></a>Brand Väggs logg

Brand Väggs loggen skapas endast om du har aktiverat den för varje Application Gateway, enligt beskrivningen i föregående steg. Den här loggen kräver också att brand väggen för webb program är konfigurerad på en Programgateway. Data lagras i det lagrings konto som du angav när du aktiverade loggningen. Följande data loggas:


|Value  |Beskrivning  |
|---------|---------|
|instanceId     | Application Gateway instans som brand Väggs data ska skapas för. För en Application Gateway med flera instanser finns det en rad per instans.         |
|clientIp     |   Ursprunglig IP för begäran.      |
|clientPort     |  Ursprunglig port för begäran.       |
|requestUri     | URL för den mottagna begäran.       |
|ruleSetType     | Regel uppsättnings typ. Det tillgängliga värdet är OWASP.        |
|ruleSetVersion     | Regel uppsättnings version används. Tillgängliga värden är 2.2.9 och 3,0.     |
|ruleId     | Regel-ID för Utlös ande händelse.        |
|message     | Användarvänligt meddelande för händelsen som utlöses. Mer information finns i avsnittet information.        |
|action     |  Åtgärd som vidtas på begäran. Tillgängliga värden är blockerade och tillåtna.      |
|webbplats     | Platsen som loggen skapades för. För närvarande visas bara globala grupper eftersom reglerna är globala.|
|details     | Information om händelsen som utlöses.        |
|information. Message     | Beskrivning av regeln.        |
|details.data     | Vissa data som finns i en begäran som matchade regeln.         |
|information. fil     | Konfigurations fil som innehåller regeln.        |
|information. rad     | Rad nummer i konfigurations filen som utlöste händelsen.       |

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

### <a name="view-and-analyze-the-activity-log"></a>Visa och analysera aktivitetsloggar

Du kan visa och analysera aktivitetsloggdata med någon av följande metoder:

* **Azure-verktyg**: Hämta information från aktivitetsloggen via Azure PowerShell, Azure CLI, Azure REST API eller Azure-portalen. Det finns stegvisa instruktioner för respektive metod i artikeln [Aktivitetsåtgärder med Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Om du inte redan har ett [Power BI](https://powerbi.microsoft.com/pricing)-konto kan du prova ett utan kostnad. Med [innehållspaketet Azure aktivitetsloggar för Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) kan du analysera dina data på förkonfigurerade instrumentpaneler, som du både kan anpassa och använda i befintligt skick.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Visa och analysera loggar för åtkomst, prestanda och brand vägg

[Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md) kan samla in räknar-och händelse logg filen från ditt Blob Storage-konto. Där finns visualiseringar och kraftfulla sökfunktioner när du ska analysera dina loggar.

Du kan också ansluta till ditt lagringskonto och hämta JSON-loggposter för åtkomst- och prestandaloggar. När du har laddat ned JSON-filerna kan du konvertera dem till CSV-format och visa dem i Excel, Power BI eller något annat verktyg för visualisering av data.

> [!TIP]
> Om du är bekant med Visual Studio och kan grunderna i att ändra värden för konstanter och variabler i C# så kan du använda [verktygen för loggkonvertering](https://github.com/Azure-Samples/networking-dotnet-log-converter) från GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Analysera åtkomst loggar via GoAccess

Vi har publicerat en Resource Manager-mall som installerar och kör den populära [GoAccess](https://goaccess.io/) log analyzer för Application Gateway åtkomst loggar. GoAccess tillhandahåller värdefull statistik för HTTP-trafik, till exempel unika besökare, begärda filer, värdar, operativ system, webbläsare, HTTP-statuskod med mera. Mer information finns i [Readme-filen i mappen Resource Manager-mall i GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="metrics"></a>Mått

Mått är en funktion för vissa Azure-resurser där du kan visa prestanda räknare i portalen. För Application Gateway är följande mått tillgängliga:

- **Aktuella anslutningar**
- **Misslyckade förfrågningar**
- **Antal felfria värdar**

   Du kan filtrera efter en pool per server del för att Visa felfria/Felaktiga värdar i en viss backend-pool.


- **Svars status**

   Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Dataflöde**
- **Totalt antal förfrågningar**
- **Antal felaktiga värdar**

   Du kan filtrera efter en pool per server del för att Visa felfria/Felaktiga värdar i en viss backend-pool.

Bläddra till en Application Gateway, under **övervakning** Välj **mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

I följande bild visas ett exempel med tre mått som visas under de senaste 30 minuterna:

[![](media/application-gateway-diagnostics/figure5.png "Metric-vy")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Om du vill se en aktuell lista över mått, se [mått som stöds med Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules"></a>Varningsregler

Du kan starta aviserings regler baserat på mått för en resurs. En avisering kan t. ex. anropa en webhook eller skicka en e-post till en administratör om data flödet för programgatewayen är över, under eller vid ett tröskelvärde under en angiven period.

Följande exempel visar hur du skapar en varnings regel som skickar ett e-postmeddelande till en administratör när data flödet har överträtt ett tröskelvärde:

1. Välj **Lägg till mått avisering** för att öppna sidan **Lägg till regel** . Du kan också komma åt den här sidan från sidan mått.

   ![Knappen Lägg till mått avisering][6]

2. På sidan **Lägg till regel** fyller du i avsnitten namn, villkor och meddelande och väljer **OK**.

   * Välj något av de fyra värdena i villkors väljaren: **Större än**, **större än eller lika med**, **mindre än**eller **mindre än eller lika**med.

   * I **period** väljaren väljer du en period på fem minuter till sex timmar.

   * Om du väljer **e-postägare, deltagare och läsare**kan e-postmeddelandet vara dynamiskt baserat på de användare som har åtkomst till resursen. Annars kan du ange en kommaavgränsad lista med användare i rutan **ytterligare administratörs e-post (er)** .

   ![Sidan Lägg till regel][7]

Om tröskelvärdet överskrids kommer ett e-postmeddelande som liknar det som finns i följande bild att bli:

![E-postmeddelande för överträtt tröskel][8]

En lista med aviseringar visas när du har skapat en mått-avisering. Den ger en översikt över alla aviserings regler.

![Lista över aviseringar och regler][9]

Mer information om aviserings aviseringar finns i [ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Om du vill veta mer om webhookar och hur du kan använda dem med aviseringar kan du gå till [Konfigurera en webhook på en Azure Metric-avisering](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknare och händelse loggar med hjälp av [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure aktivitets logg med Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogg inlägg.
* [Visa och analysera Azure aktivitets loggar i Power BI och fler](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogg inlägg.

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
