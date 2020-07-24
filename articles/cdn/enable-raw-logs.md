---
title: Azure CDN HTTP RAW-loggar
description: I den här artikeln beskrivs Azure CDN HTTP RAW-loggar.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/22/2020
ms.author: allensu
ms.openlocfilehash: 3b36e528a013403a2ed664d3011338d92f37a3db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040167"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP RAW-loggar
Obehandlade loggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning och fel sökning. Obehandlade loggar skiljer sig från aktivitets loggar. Aktivitets loggarna ger insyn i de åtgärder som utförs på Azure-resurser. Obehandlade loggar innehåller en post med åtgärder för din resurs. Rå logg innehåller omfattande information om varje begäran som CDN tar emot. 

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig för Azure CDN från Microsoft.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Konfiguration – Azure Portal

Så här konfigurerar du obehandlade loggar för din Azure CDN från Microsoft-profilen: 

1. Välj **alla resurser**på Azure Portal-menyn  >>  **\<your-CDN-profile>** .

2. Under **Övervakning** väljer du **Diagnostikinställningar**.

3. Välj **+ Lägg till diagnostisk inställning**.

    ![Inställning av CDN-diagnostik](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Råa loggar är bara tillgängliga på profil nivå medan sammanställda HTTP-statuskod loggar är tillgängliga på slut punkts nivå.

4. Under **diagnostikinställningar**anger du ett namn för den diagnostiska inställningen under **diagnostiska**inställnings namn.

5. Välj **loggen** och ange kvarhållning i dagar.

6. Välj **mål information**. Mål alternativen är:
    * **Skicka till Log Analytics**
        * Välj **prenumerationen** och **Log Analytics arbets ytan**.
    * **Arkivera till ett lagrings konto**
        * Välj **prenumerationen** och **lagrings kontot**.
    * **Strömma till en Event Hub**
        * Välj **prenumeration**, **Event Hub-namnrymd**, **Event Hub-namn (valfritt)** och **princip namn för Event Hub**.

    ![Inställning av CDN-diagnostik](./media/cdn-raw-logs/raw-logs-02.png)

7. Välj **Spara**.

## <a name="configuration---azure-powershell"></a>Konfiguration – Azure PowerShell

Använd [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) för att konfigurera den diagnostiska inställningen för obehandlade loggar.

Kvarhållning av data definieras av alternativet **-RetentionInDays** i kommandot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostikloggar i ett lagrings konto

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Om du vill aktivera diagnostikloggar i ett lagrings konto anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Aktivera diagnostikloggar för Log Analytics arbets yta

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för en arbets yta Log Analytics anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Aktivera diagnostikloggar för Event Hub-namnområde

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för ett Event Hub-namnområde anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Egenskaper för RAW-loggar

Azure CDN från Microsoft-tjänsten tillhandahåller för närvarande obehandlade loggar. Obehandlade loggar innehåller enskilda API-begäranden med varje post med följande schema: 

| Egenskap              | Beskrivning                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Den unika referens strängen som identifierar en begäran som betjänas av en front dörr, som också skickas som X-Azure-ref-huvud till klienten. Krävs för att söka efter information i åtkomst loggarna för en speciell begäran. |
| HttpMethod            | HTTP-metod som används av begäran.                                                                                                                                                                     |
| HttpVersion           | Typ av begäran eller anslutning.                                                                                                                                                                   |
| RequestUri            | URI för den mottagna begäran.                                                                                                                                                                         |
| RequestBytes          | Storleken på HTTP Request-meddelandet i byte, inklusive begärandehuvuden och begär ande texten.                                                                                                   |
| ResponseBytes         | Byte som skickats av backend-servern som svar.                                                                                                                                                    |
| UserAgent             | Webbläsarens typ som används av klienten.                                                                                                                                                               |
| ClientIp              | IP-adressen för klienten som gjorde begäran.                                                                                                                                                  |
| TimeTaken             | Hur lång tid åtgärden tog, i millisekunder.                                                                                                                                            |
| SecurityProtocol      | TLS/SSL-protokollets version som används av begäran eller null om ingen kryptering.                                                                                                                           |
| Slutpunkt              | CDN-slutpunktens värd har kon figurer ATS under den överordnade CDN-profilen.                                                                                                                                   |
| Server dels värd namn     | Namnet på backend-värden eller ursprunget där förfrågningar skickas.                                                                                                                                |
| Skickas till ursprungs sköld </br> (föråldrad) * **Se anteckna om föråldrade nedan.** | Om värdet är true innebär det att begäran besvarades från ursprungs sköldens cacheminne i stället för Edge-pop. Ursprungs sköld är ett överordnat cacheminne som används för att förbättra förhållandet mellan cacheträffar.                                       |
| isReceivedFromClient | Om värdet är true innebär det att begäran kom från klienten. Om det här värdet är falskt, är begäran en brist i kanten (underordnad POP) och är besvarad från ursprungs skölden (överordnad POP). 
| HttpStatusCode        | HTTP-statuskod som returnerades från proxyservern.                                                                                                                                                        |
| HttpStatusDetails     | Resulterande status för begäran. Innebörd av detta sträng värde finns i en status referens tabell.                                                                                              |
| Popup                   | Edge-pop, som svarade på användarens begäran. Pop-förkortningar är flyg plats koder för deras respektive Metros.                                                                                   |
| Cache-status          | Indikerar om objektet returnerades från cachen eller kom från ursprunget.                                                                                                             |
> [!NOTE]
> Loggarna kan visas under din Log Analytics profil genom att köra en fråga. En exempel fråga skulle se ut som AzureDiagnostics | där Category = = "AzureCdnAccessLog"


### <a name="sent-to-origin-shield-deprecation"></a>Skickas till ursprungs sköldens utfasning
Den råa logg egenskapen **isSentToOriginShield** har ersatts och ersatts av ett nytt fält **isReceivedFromClient**. Använd det nya fältet om du redan använder det inaktuella fältet. 

Obehandlade loggar innehåller loggar som skapats från både CDN-Edge (underordnad POP) och ursprungs sköld. Ursprungs sköld syftar på överordnade noder som är strategiskt placerade över hela världen. Dessa noder kommunicerar med ursprungs servrar och minskar belastningen på belastningen på ursprunget. 

För varje begäran som går till ursprungs sköld finns det två logg poster:

* En för Edge-noder
* En för ursprungs sköld. 

Om du vill särskilja utgången eller svaren från Edge-noderna eller ursprungs skölden kan du använda fältet isReceivedFromClient för att hämta rätt data. 

Om värdet är false innebär det att begäran besvaras från ursprungs sköld till Edge-noder. Den här metoden är effektiv för att jämföra obehandlade loggar med fakturerings data. Avgifter debiteras inte för utgående från ursprungs sköld till Edge-noderna. Avgifter debiteras för utgående från Edge-noderna till klienter. 

**Kusto Query-exempel för att utesluta loggar som skapats på ursprungs sköld i Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Funktionen HTTP RAW-loggar är tillgänglig automatiskt för profiler som skapats eller uppdaterats efter **25 februari 2020**. För CDN-profiler som skapats tidigare bör du uppdatera CDN-slutpunkten när du har konfigurerat loggning. Till exempel kan en navigera till geo-filtrering under CDN-slutpunkter och blockera alla länder/regioner som inte är relevanta för deras arbets belastning och tryck på Spara. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du aktiverat HTTP RAW-loggar för Microsoft CDN-tjänsten.

Mer information om Azure CDN och de andra Azure-tjänsterna som nämns i den här artikeln finns i:

* [Analysera](cdn-log-analysis.md) Azure CDN användnings mönster.

* Läs mer om [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Konfigurera [Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
