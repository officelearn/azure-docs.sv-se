---
title: Använda Microsoft Azure Traffic Manager för att öka kvoten för slutpunkten i språk förstå (THOMAS) - Azure | Microsoft Docs
description: Använda Microsoft Azure Traffic Manager för att sprida endpoint kvoten över flera prenumerationer i språk förstå (THOMAS) att öka kvoten för slutpunkten
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/07/2018
ms.author: v-geberr
ms.openlocfilehash: 8c8228b13c972c65596f0389e2fdfde585f8a742
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110321"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Använda Microsoft Azure Traffic Manager för att hantera endpoint kvoten över nycklar
Språk förstå (THOMAS) ger dig möjlighet att öka kvoten för slutpunkt-begäran utöver kvoten för en enskild nyckel. Detta görs genom att skapa flera nycklar för THOMAS och lägger till dem THOMAS programmet på den **publicera** sidan i den **resurser och nycklar** avsnitt. 

Klientprogrammet måste hantera trafik över nycklarna. THOMAS göra inte det. 

Den här artikeln förklarar hur du hanterar trafiken över nycklar med Azure [Traffic Manager][traffic-manager-marketing]. Du måste redan ha en tränad och publicerade THOMAS app. Om du inte har något följer färdiga domänen [quickstart](luis-get-started-create-app.md). 

## <a name="connect-to-powershell-in-the-azure-portal"></a>Anslut till PowerShell i Azure-portalen
I den [Azure] [ azure-portal] portal, öppna PowerShell-fönster. Ikonen för PowerShell-fönstret visas den **> _** i det övre navigeringsfältet. Du får den senaste versionen av PowerShell och du autentiseras med hjälp av PowerShell från portalen. PowerShell i portalen kräver en [Azure Storage](https://azure.microsoft.com/services/storage/) konto. 

![Skärmbild av Azure-portalen med Powershell-fönstret öppnas](./media/traffic-manager/azure-portal-powershell.png)

I de följande avsnitten Använd [Traffic Manager PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/?view=azurermps-6.2.0#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Skapa Azure-resursgrupp med PowerShell
Skapa en resursgrupp att innehålla alla resurser innan du skapar Azure-resurser. Kalla resursgruppen `luis-traffic-manager` och använda regionen är `West US`. Det den regionen där resursgruppen lagras metadata om gruppen. Det kommer inte längre tid dina resurser om de finns i en annan region. 

Skapa en resursgrupp med **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.2.0)** cmdlet:

```PowerShell
New-AzureRmResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Skapa THOMAS nycklar för att öka kvoten för total slutpunkt
1. I Azure-portalen kan du skapa två **språk förstå** nycklar i den `West US` och en i den `East US`. Använd en befintlig resursgrupp skapade i föregående avsnitt med namnet `luis-traffic-manager`. 

    ![Skärmbild av Azure-portalen med två THOMAS nycklar i Thomas traffic manager-resursgrupp](./media/traffic-manager/luis-keys.png)

2. I den [THOMAS] [ LUIS] webbplats på den **publicera** sidan, Lägg till nycklar för appen och publicera om appen. 

    ![Skärmbild av THOMAS portal med två THOMAS nycklar på publicera sidan](./media/traffic-manager/luis-keys-in-luis.png)

    Exempel-URL i den **endpoint** kolumn använder en GET-begäran med slutpunktsnyckel som en frågeparameter. Kopiera URL: er för de två nya nycklarna slutpunkt. De används som en del av Traffic Manager konfigurationen.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Hantera begäranden om THOMAS slutpunkt över nycklar med Traffic Manager
Traffic Manager skapar en ny DNS-åtkomstpunkt för dina slutpunkter. Den inte fungera som en gateway eller proxy, men enbart på DNS-nivå. Det här exemplet ändras inte alla DNS-poster. Den använder ett DNS-bibliotek för att kommunicera med Traffic Manager att hämta korrekt slutpunkt för specifik begäran. _Varje_ begäran som är avsedda för THOMAS först kräver en Traffic Manager-begäran för att avgöra vilken THOMAS slutpunkt som ska användas. 

### <a name="polling-uses-luis-endpoint"></a>Avsökningen använder THOMAS slutpunkt
Traffic Manager avsöker slutpunkter med jämna mellanrum för att se till att slutpunkten är fortfarande tillgängliga. Traffic Manager-URL avsökas måste vara tillgänglig med en GET-begäran och returnera ett 200. Slutpunkts-URL på den **publicera** sidan gör detta. Eftersom varje slutpunktsnyckel har en annan väg och fråga string-parametrar, måste varje slutpunktsnyckel en annan avsökning sökväg. Varje gång Traffic Manager avsöker kostar den en kvot-begäran. Frågesträngparametern **q** slutpunkt är THOMAS utterance skickas till THOMAS. Den här parametern i stället för att skicka en utterance används för att lägga till Traffic Manager-avsökning THOMAS endpoint loggen som en teknik som felsökning vid hämtning av Traffic Manager konfigureras.

Eftersom varje THOMAS slutpunkt behöver dess egna sökväg, måste en egen Traffic Manager-profilen. För att hantera över profiler, skapas en [ _kapslade_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) arkitektur. En överordnad profil pekar på profilerna underordnade och hantera trafik över dem.

När Traffic Manager har konfigurerats kan du komma ihåg att ändra sökvägen om du vill använda loggning = false frågesträngparametern så loggen inte fyller upp med avsökning.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurera Traffic Manager med kapslade profiler
I följande avsnitt skapa två underordnade profiler, en för Öst THOMAS nyckeln och en för Väst THOMAS nyckeln. Sedan en överordnad profil har skapats och två underordnade profiler har lagts till i den överordnade profilen. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Skapa östra USA Traffic Manager-profilen med PowerShell
Det finns flera steg för att skapa östra USA Traffic Manager-profilen: skapa profil, Lägg till slutpunkt och ange slutpunkt. Traffic Manager-profilen kan ha många slutpunkter, men varje slutpunkt har samma sökväg för verifiering. Eftersom THOMAS endpoint-URL för prenumerationer Öst- och skiljer sig åt på grund av region och slutpunkten nyckel, måste varje THOMAS slutpunkt vara en enda slutpunkt i profilen. 

1. Skapa profil med **[ny AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** cmdlet

    Använd följande cmdlet för att skapa profilen. Se till att ändra den `appIdLuis` och `subscriptionKeyLuis`. SubscriptionKey är för Öst oss THOMAS nyckeln. Om sökvägen är inte korrekt, inklusive THOMAS app-ID och slutpunkten nyckeln Traffic Manager-avsökning har statusen `degraded` eftersom hantera trafik inte kan begära THOMAS slutpunkten har. Kontrollera att värdet för `q` är `traffic-manager-east` så att du kan se det här värdet i loggarna THOMAS slutpunkt.

    ```PowerShell
    $eastprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Den här tabellen förklarar varje variabel i cmdlet:
    
    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-Namn|Thomas-profil-eastus|Traffic Manager-namn i Azure-portalen|
    |-ResourceGroupName|Thomas traffic manager|Skapade i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda, måste URL-begäran till Traffic Manager komma från regionen för användaren. Om gå via en chatbot eller ett annat program, är det den chatbot ansvar för att efterlikna region i anropet till Traffic Manager. |
    |-RelativeDnsName|Thomas-dns-eastus|Detta är underdomänen för tjänsten: Thomas-dns-eastus.trafficmanager.net|
    |Ttl-|30|Avsökningsintervall 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för THOMAS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Ersätt <appIdLuis> och <subscriptionKeyLuis> med egna värden.|
    
    En lyckad begäran har inget svar.

2. Lägg till östra USA slutpunkt med **[Lägg till AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/add-azurermtrafficmanagerendpointconfig?view=azurermps-6.2.0)** cmdlet

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Den här tabellen förklarar varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|Thomas – Öst-slutpunkt|Namnet på slutpunkten visas under profilen|
    |-TrafficManagerProfile|$eastprofile|Använd profilobjektet som skapade i steg 1|
    |-Typen|ExternalEndpoints|Mer information finns i [Traffic Manager-slutpunkt][traffic-manager-endpoints] |
    |-Mål|eastus.API.cognitive.microsoft.com|Detta är domänen för THOMAS slutpunkten.|
    |-EndpointLocation|”eastus”|Region för slutpunkten|
    |-EndpointStatus|Enabled|Aktivera slutpunkten när den har skapats|

    Det ser ut så lyckat svar:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Ange östra USA slutpunkten med **[Set AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/set-azurermtrafficmanagerprofile?view=azurermps-6.2.0)** cmdlet

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Ett lyckat svar kommer att samma svar som steg 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Skapa västra USA Traffic Manager-profilen med PowerShell
Följ samma steg för att skapa västra USA Traffic Manager-profilen,: skapa profil, Lägg till slutpunkt och ange slutpunkt.

1. Skapa profil med **[ny AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** cmdlet

    Använd följande cmdlet för att skapa profilen. Se till att ändra den `appIdLuis` och `subscriptionKeyLuis`. SubscriptionKey är för Öst oss THOMAS nyckeln. Om sökvägen inte är korrekt inklusive nyckeln THOMAS app-ID och slutpunkten Traffic Manager-avsökning har statusen `degraded` eftersom hantera trafik inte kan begära THOMAS slutpunkten har. Kontrollera att värdet för `q` är `traffic-manager-west` så att du kan se det här värdet i loggarna THOMAS slutpunkt.

    ```PowerShell
    $westprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Den här tabellen förklarar varje variabel i cmdlet:
    
    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-Namn|Thomas-profil-westus|Traffic Manager-namn i Azure-portalen|
    |-ResourceGroupName|Thomas traffic manager|Skapade i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda, måste URL-begäran till Traffic Manager komma från regionen för användaren. Om gå via en chatbot eller ett annat program, är det den chatbot ansvar för att efterlikna region i anropet till Traffic Manager. |
    |-RelativeDnsName|Thomas-dns-westus|Detta är underdomänen för tjänsten: Thomas-dns-westus.trafficmanager.net|
    |Ttl-|30|Avsökningsintervall 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för THOMAS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Ersätt <appId> och <subscriptionKey> med egna värden. Spara den här slutpunkten nyckeln är annat än east-slutpunktsnyckel|
    
    En lyckad begäran har inget svar.

2. Lägg till västra USA slutpunkt med **[Lägg till AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** cmdlet

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Den här tabellen förklarar varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|Thomas-Väst-slutpunkt|Namnet på slutpunkten visas under profilen|
    |-TrafficManagerProfile|$westprofile|Använd profilobjektet som skapade i steg 1|
    |-Typen|ExternalEndpoints|Mer information finns i [Traffic Manager-slutpunkt][traffic-manager-endpoints] |
    |-Mål|westus.API.cognitive.microsoft.com|Detta är domänen för THOMAS slutpunkten.|
    |-EndpointLocation|”westus”|Region för slutpunkten|
    |-EndpointStatus|Enabled|Aktivera slutpunkten när den har skapats|

    Det ser ut så lyckat svar:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. Ange västra USA slutpunkten med **[Set AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** cmdlet

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Ett lyckat svar är samma svar som steg 2.

### <a name="create-parent-traffic-manager-profile"></a>Skapa överordnade Traffic Manager-profilen
Skapa överordnat Traffic Manager-profilen och länka två underordnade Traffic Manager-profiler till överordnat.

1. Skapa överordnade profil med **[ny AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/New-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** cmdlet

    ```PowerShell
    $parentprofile = New-AzureRmTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Den här tabellen förklarar varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-Namn|Thomas-profil-överordnad|Traffic Manager-namn i Azure-portalen|
    |-ResourceGroupName|Thomas traffic manager|Skapade i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda, måste URL-begäran till Traffic Manager komma från regionen för användaren. Om gå via en chatbot eller ett annat program, är det den chatbot ansvar för att efterlikna region i anropet till Traffic Manager. |
    |-RelativeDnsName|Thomas-dns-överordnad|Detta är underdomänen för tjänsten: Thomas-dns-parent.trafficmanager.net|
    |Ttl-|30|Avsökningsintervall 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för THOMAS är HTTPS/443|
    |-MonitorPath|`/`|Den här sökvägen spelar ingen roll eftersom de underordnade slutpunkten sökvägarna används i stället.|

    En lyckad begäran har inget svar.

2. Lägg till östra USA underordnade profilen till överordnad med **[Lägg till AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** och **NestedEndpoints** typ

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Den här tabellen förklarar varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|underordnade-slutpunkt-useast|East-profil|
    |-TrafficManagerProfile|$parentprofile|Den här slutpunkten till-profil|
    |-Typen|NestedEndpoints|Mer information finns i [Lägg till AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-TargetResourceId|$eastprofile. ID|ID för den underordnade profilen|
    |-EndpointStatus|Enabled|Status för endpoint efter tillägg till överordnad|
    |-EndpointLocation|”eastus”|[Namn på Azure-region](https://azure.microsoft.com/global-infrastructure/regions/) för resurs|
    |-MinChildEndpoints|1|Minsta antalet till underordnade slutpunkter|

    Lyckat svar utseende följande och omfattar den nya `child-endpoint-useast` slutpunkt:    

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. Lägg till västra USA underordnade profilen till överordnad med **[Lägg till AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0)** cmdlet och **NestedEndpoints** typ

    ```PowerShell
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Den här tabellen förklarar varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|underordnade-slutpunkt-uswest|West-profil|
    |-TrafficManagerProfile|$parentprofile|Den här slutpunkten till-profil|
    |-Typen|NestedEndpoints|Mer information finns i [Lägg till AzureRmTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager/Add-AzureRmTrafficManagerEndpointConfig?view=azurermps-6.2.0). |
    |-TargetResourceId|$westprofile. ID|ID för den underordnade profilen|
    |-EndpointStatus|Enabled|Status för endpoint efter tillägg till överordnad|
    |-EndpointLocation|”westus”|[Namn på Azure-region](https://azure.microsoft.com/global-infrastructure/regions/) för resurs|
    |-MinChildEndpoints|1|Minsta antalet till underordnade slutpunkter|

    Leta i lyckat svar som och inkluderar den tidigare `child-endpoint-useast` slutpunkt och den nya `child-endpoint-uswest` slutpunkt:

    ```cmd
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Ange slutpunkter med **[Set AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Set-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)** cmdlet 

    ```PowerShell
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Ett lyckat svar är samma svar som steg 3.

### <a name="powershell-variables"></a>PowerShell variabler
I föregående avsnitt tre PowerShell variabler har skapats: `$eastprofile`, `$westprofile`, `$parentprofile`. Dessa variabler används mot slutet av Traffic Manager-konfigurationen. Om du valde att inte skapa variabler eller har glömt att eller PowerShell-fönstret timeout, du kan använda PowerShell-cmdleten  **[Get-AzureRmTrafficManagerProfile](https://docs.microsoft.com/powershell/module/AzureRM.TrafficManager/Get-AzureRmTrafficManagerProfile?view=azurermps-6.2.0)**, för att hämta profilen igen och tilldela den en variabel. 

Ersätta objekt hakparenteser, `<>`, med korrekta värden för var och en av de tre profiler som du behöver. 

```PowerShell
$<variable-name> = Get-AzureRmTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verifiera Traffic Manager fungerar
Kontrollera att Traffic Manager-profiler fungerar, profilerna måste ha statusen för `Online` denna status är baserat på sökvägen avsökning av slutpunkten. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Visa nya profiler i Azure-portalen
Du kan kontrollera att alla tre profiler skapas genom att titta på resurserna i den `luis-traffic-manager` resursgruppen.

![Skärmbild av Azure grupp Thomas-trafik-resurshanteraren](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Kontrollera profilstatusen för är Online
Traffic Manager avsöker sökvägen för varje slutpunkt som du vill kontrollera att den är online. Om den är online, status för underordnade profiler är `Online`. Detta visas på den **översikt** för varje profil. 

![Skärmbild av Azure Traffic Manager-profilen översikt som visar övervaka Status för Online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Validera Traffic Manager avsökning fungerar
Ett annat sätt att verifiera trafikhanterarprofil avsökning fungerar är med THOMAS endpoint loggar. På den [THOMAS] [ LUIS] lista över appar webbplats kan du exportera endpoint loggen för programmet. Eftersom Traffic Manager ofta avsökningar efter ändrade två slutpunkter, finns det poster i loggarna även om de har endast på några minuter. Kom ihåg att söka efter poster där frågan börjar med `traffic-manager-`.

```text
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Verifiera DNS-svaret från Traffic Manager fungerar
Begära trafik hantera överordnade profilen DNS med ett DNS-klientbibliotek för att verifiera att DNS-svaret returnerar en THOMAS slutpunkt. DNS-namn för den överordnade profilen är `luis-dns-parent.trafficmanager.net`.

Följande Node.js-kod gör en begäran för den överordnade profilen och returnerar en THOMAS slutpunkt:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Lyckat svar med THOMAS slutpunkten är:

```cmd
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Använd överordnade trafikhanterarprofil
Du måste infoga ett anrop till Traffic Manager-DNS för att hitta THOMAS slutpunkt för att hantera trafik över slutpunkter. Detta anrop görs för varje THOMAS endpoint-begäran och behöver simulera den geografiska platsen för användare av THOMAS klientprogram. Lägga till DNS-svarskoden between THOMAS klientprogrammet och begäran THOMAS för slutpunkten förutsägelse. 


## <a name="clean-up"></a>Rensa
Ta bort två THOMAS endpoint nycklar, tre Traffic Manager-profiler och resursgruppen som innehåller dessa fem resurser. Detta görs från Azure-portalen. Du kan ta bort de fem resurserna från resurslistan över. Ta sedan bort resursgruppen. 

## <a name="next-steps"></a>Nästa steg

Granska [mellanprogram](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) alternativ i BotFramework v4 att förstå hur den här koden för hantering av trafik kan läggas till en BotFramework bot. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
