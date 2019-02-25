---
title: Öka kvoten för slutpunkt
titleSuffix: Azure Cognitive Services
description: Språkförståelse (LUIS) ger dig möjlighet att öka kvoten för slutpunkt-begäran utöver en enda nyckel kvot. Detta görs genom att skapa flera nycklar för LUIS och lägga till dem i LUIS-programmet på den **publicera** sidan i den **resurser och nycklar** avsnittet.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/08/2019
ms.author: diberry
ms.openlocfilehash: a71b09ba8b3e7fa7299c34c3cdc64503ae4e9857
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736557"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Använd Microsoft Azure Traffic Manager för att hantera endpoint kvot över nycklar
Språkförståelse (LUIS) ger dig möjlighet att öka kvoten för slutpunkt-begäran utöver en enda nyckel kvot. Detta görs genom att skapa flera nycklar för LUIS och lägga till dem i LUIS-programmet på den **publicera** sidan i den **resurser och nycklar** avsnittet. 

Klientprogrammet har att hantera trafiken över nycklarna. LUIS göra inte det. 

Den här artikeln förklarar hur du hanterar trafiken över nycklar med Azure [Traffic Manager][traffic-manager-marketing]. Du måste redan ha en tränad och publicerade LUIS-app. Om du inte har något följer fördefinierade domänen [snabbstarten](luis-get-started-create-app.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Ansluta till PowerShell i Azure portal
I den [Azure] [ azure-portal] portal, öppna PowerShell-fönster. Ikonen för PowerShell-fönstret visas den **> _** i det övre navigeringsfältet. Du får den senaste versionen av PowerShell med hjälp av PowerShell från portalen och du är autentiserad. PowerShell i portalen kräver en [Azure Storage](https://azure.microsoft.com/services/storage/) konto. 

![Skärmbild av Azure-portalen med Powershell-fönster öppnas](./media/traffic-manager/azure-portal-powershell.png)

I de följande avsnitten används [Traffic Manager PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Skapa Azure-resursgrupp med PowerShell
Skapa en resursgrupp som innehåller alla resurser innan du skapar Azure-resurser. Ge resursgruppen namnet `luis-traffic-manager` och använda regionen är `West US`. Regionen för resursgruppen lagrar metadata om gruppen. Det kommer inte sakta ned dina resurser om de finns i en annan region. 

Skapa resursgrupp med **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet:

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Skapa LUIS för att öka kvoten för total slutpunkt
1. I Azure-portalen skapar du två **Språkförståelse** nycklar, en i den `West US` och en i den `East US`. Använd den befintliga resursgruppen, skapade i föregående avsnitt med namnet `luis-traffic-manager`. 

    ![Skärmbild av Azure-portalen med två LUIS-nycklar i luis trafikhantering resursgrupp](./media/traffic-manager/luis-keys.png)

2. I den [LUIS] [ LUIS] webbplats, i den **hantera** avsnittet på den **nycklar och slutpunkter** sidan, tilldela nycklar till appen och publicera appen genom att att välja den **publicera** knappen i övre högra menyn. 

    Exempel-URL: en i den **endpoint** kolumnen använder en GET-begäran med slutpunktsnyckeln som en frågeparameter. Kopiera URL: er för de två nya nycklar-slutpunkt. De används som en del av Traffic Manager konfigurationen senare i den här artikeln.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Hantera begäranden om LUIS slutpunkt över nycklar med Traffic Manager
Traffic Manager skapar en ny DNS-åtkomstpunkt för dina slutpunkter. Den agerar inte som en gateway eller proxy, men enbart på DNS-nivå. Det här exemplet ändras inte alla DNS-poster. Den använder ett DNS-bibliotek för att kommunicera med Traffic Manager att hämta rätt slutpunkt för specifik begäran. _Varje_ begäran som är avsedd för LUIS först kräver en Traffic Manager-begäran för att avgöra vilka LUIS-slutpunkt som ska användas. 

### <a name="polling-uses-luis-endpoint"></a>Avsökningen använder LUIS-slutpunkt
Traffic Manager ska avsöka slutpunkter med jämna mellanrum för att se till att slutpunkten är fortfarande tillgänglig. URL: en för Traffic Manager avsökas måste vara tillgängliga med en GET-begäran och returnerar 200. Slutpunkts-URL på den **publicera** sidan sker. Eftersom varje slutpunktsnyckeln har en annan väg och frågesträngsparametrarna, måste varje slutpunktsnyckeln en annan avsökningen sökväg. Varje gång Traffic Manager ska avsöka, kostar det en begäran om ökad kvot. Frågesträngparametern **q** Luis-slutpunkten är uttryck som skickas till LUIS. Den här parametern, i stället för att ett uttryck används för att lägga till Traffic Manager-avsökning in LUIS-slutpunkt som en teknik för felsökning vid hämtning av Traffic Manager som konfigurerats.

Eftersom varje LUIS-slutpunkt behöver en egen sökväg, måste den egna Traffic Manager-profil. För att kunna hantera både premiumprofiler, skapa en [ _kapslade_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) arkitektur. En överordnad profil pekar på de underordnade profilerna och hantera trafik mellan dem.

När Traffic Manager har konfigurerats, Kom ihåg att ändra sökvägen om du vill använda loggning = false frågesträngparametern så att din logg inte fyller upp med avsökning.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurera Traffic Manager med kapslade profiler
I följande avsnitt skapar två underordnade profiler, en för östra LUIS-nyckel och en för västra LUIS-nyckeln. Sedan skapas en överordnad-profil och två underordnade profiler läggs till i den överordnade profilen. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Skapa ÖSTRA Traffic Manager-profilen med PowerShell
Om du vill skapa ÖSTRA Traffic Manager-profilen, det finns flera steg: skapa profil, Lägg till slutpunkt och ange slutpunkt. Traffic Manager-profil kan ha många slutpunkter, men varje slutpunkt har samma sökväg för verifiering. Eftersom URL: er för LUIS-slutpunkten för Öst- och -prenumerationer är olika på grund av region och slutpunkten, måste varje LUIS-slutpunkten ha en enda slutpunkt i profilen. 

1. Skapa profil med **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet

    Använd följande cmdlet för att skapa profilen. Se till att ändra den `appIdLuis` och `subscriptionKeyLuis`. SubscriptionKey är för nyckeln LUIS för östra USA. Om sökvägen är inte korrekt, inklusive LUIS-app-ID och slutpunkten nyckeln, Traffic Manager-avsökningen är statusen `degraded` eftersom Traffic Managers inte kan begära LUIS-slutpunkten har. Kontrollera att värdet för `q` är `traffic-manager-east` så att du kan se det här värdet i loggarna för LUIS-slutpunkten.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Den här tabellen beskriver varje variabel i cmdlet:
    
    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-Namn|Luis-profil-eastus|Traffic Manager-namnet i Azure-portalen|
    |-ResourceGroupName|Luis traffic manager|Föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda, måste URL-begäran till Traffic Manager komma från en region för användaren. Om går igenom en chattrobot eller andra program, är det den chattrobot ansvar att efterlikna regionen i anropet till Traffic Manager. |
    |-RelativeDnsName|Luis-dns-eastus|Det här är underdomänen för tjänsten: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Avsökningsintervall 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Ersätt <appIdLuis> och <subscriptionKeyLuis> med dina egna värden.|
    
    En lyckad begäran har inget svar.

2. Lägg till slutpunkt för östra USA med **[Lägg till AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Den här tabellen beskriver varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|Luis – östra-slutpunkt|Slutpunktsnamn som visas under profilen|
    |-TrafficManagerProfile|$eastprofile|Använd profilen objekt skapade i steg 1|
    |-Type|ExternalEndpoints|Mer information finns i [Traffic Manager-slutpunkt][traffic-manager-endpoints] |
    |-Mål|eastus.API.cognitive.microsoft.com|Detta är domänen för LUIS-slutpunkten.|
    |-EndpointLocation|”eastus”|Region för slutpunkten|
    |-EndpointStatus|Enabled|Aktivera slutpunkten när den har skapats|

    Lyckat svar som liknar:

    ```console
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

3. Ange slutpunkt för östra USA med **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Ett lyckat svar kommer att samma svar som steg 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Skapa västra USA Traffic Manager-profilen med PowerShell
Följ samma steg för att skapa västra USA Traffic Manager-profilen: skapa profil, Lägg till slutpunkt och ange slutpunkt.

1. Skapa profil med **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    Använd följande cmdlet för att skapa profilen. Se till att ändra den `appIdLuis` och `subscriptionKeyLuis`. SubscriptionKey är för nyckeln LUIS för östra USA. Om sökvägen inte är korrekt, inklusive LUIS-app-ID och slutpunkten nyckeln Traffic Manager-avsökningen är statusen `degraded` eftersom Traffic Managers inte kan begära LUIS-slutpunkten har. Kontrollera att värdet för `q` är `traffic-manager-west` så att du kan se det här värdet i loggarna för LUIS-slutpunkten.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Den här tabellen beskriver varje variabel i cmdlet:
    
    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-Namn|Luis-profil-westus|Traffic Manager-namnet i Azure-portalen|
    |-ResourceGroupName|Luis traffic manager|Föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda, måste URL-begäran till Traffic Manager komma från en region för användaren. Om går igenom en chattrobot eller andra program, är det den chattrobot ansvar att efterlikna regionen i anropet till Traffic Manager. |
    |-RelativeDnsName|Luis-dns-westus|Det här är underdomänen för tjänsten: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Avsökningsintervall 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Ersätt <appId> och <subscriptionKey> med dina egna värden. Kom ihåg det här slutpunktsnyckeln skiljer sig från slutpunktsnyckeln östra|
    
    En lyckad begäran har inget svar.

2. Lägg till slutpunkt för USA, västra med **[Lägg till AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Den här tabellen beskriver varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|Luis-Väst-slutpunkt|Slutpunktsnamn som visas under profilen|
    |-TrafficManagerProfile|$westprofile|Använd profilen objekt skapade i steg 1|
    |-Type|ExternalEndpoints|Mer information finns i [Traffic Manager-slutpunkt][traffic-manager-endpoints] |
    |-Mål|westus.API.cognitive.microsoft.com|Detta är domänen för LUIS-slutpunkten.|
    |-EndpointLocation|”westus”|Region för slutpunkten|
    |-EndpointStatus|Enabled|Aktivera slutpunkten när den har skapats|

    Lyckat svar som liknar:

    ```console
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

3. Ange västra USA slutpunkt med **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Ett lyckat svar är samma svaret som steg 2.

### <a name="create-parent-traffic-manager-profile"></a>Skapa överordnade Traffic Manager-profil
Skapa överordnade Traffic Manager-profil och länka två underordnade Traffic Manager-profiler till överordnat.

1. Skapa överordnade profil med **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Den här tabellen beskriver varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-Namn|Luis-profil-överordnad|Traffic Manager-namnet i Azure-portalen|
    |-ResourceGroupName|Luis traffic manager|Föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda, måste URL-begäran till Traffic Manager komma från en region för användaren. Om går igenom en chattrobot eller andra program, är det den chattrobot ansvar att efterlikna regionen i anropet till Traffic Manager. |
    |-RelativeDnsName|Luis-dns-överordnad|Det här är underdomänen för tjänsten: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Avsökningsintervall 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/`|Den här sökvägen spelar ingen roll eftersom underordnade slutpunkten sökvägar används i stället.|

    En lyckad begäran har inget svar.

2. Lägg till USA, östra underordnade profil till överordnad med **[Lägg till AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** och **NestedEndpoints** typ

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Den här tabellen beskriver varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|underordnad-endpoint-useast|Östra profil|
    |-TrafficManagerProfile|$parentprofile|Profilen för att tilldela den här slutpunkten till|
    |-Type|NestedEndpoints|Mer information finns i [Lägg till AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. ID|ID för den underordnade profilen|
    |-EndpointStatus|Enabled|Slutpunktsstatus när du lägger till till överordnad|
    |-EndpointLocation|”eastus”|[Namn på Azure-region](https://azure.microsoft.com/global-infrastructure/regions/) för resursen|
    |-MinChildEndpoints|1|Minsta antal underordnade slutpunkter|

    Lyckat svar se ut så här och innehåller den nya `child-endpoint-useast` slutpunkt:    

    ```console
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

3. Lägg till USA, västra underordnade profil till överordnad med **[Lägg till AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet och **NestedEndpoints** typ

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Den här tabellen beskriver varje variabel i cmdlet:

    |Konfigurationsparameter|Miljövariabelns namn eller värde|Syfte|
    |--|--|--|
    |-EndpointName|underordnad-endpoint-uswest|Västra profil|
    |-TrafficManagerProfile|$parentprofile|Profilen för att tilldela den här slutpunkten till|
    |-Type|NestedEndpoints|Mer information finns i [Lägg till AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. ID|ID för den underordnade profilen|
    |-EndpointStatus|Enabled|Slutpunktsstatus när du lägger till till överordnad|
    |-EndpointLocation|”westus”|[Namn på Azure-region](https://azure.microsoft.com/global-infrastructure/regions/) för resursen|
    |-MinChildEndpoints|1|Minsta antal underordnade slutpunkter|

    Lyckat svar utseende som och innehåller den tidigare `child-endpoint-useast` slutpunkten och den nya `child-endpoint-uswest` slutpunkt:

    ```console
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

4. Ange slutpunkter med **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Ett lyckat svar är samma svaret som steg 3.

### <a name="powershell-variables"></a>PowerShell-variabler
I föregående avsnitt, tre PowerShell variabler har skapats: `$eastprofile`, `$westprofile`, `$parentprofile`. Dessa variabler används mot slutet av Traffic Manager-konfigurationen. Om du har valt att inte skapa variablerna eller har glömt att, eller tidsgränsen uppnås för PowerShell-fönstret kan du använda PowerShell-cmdlet  **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)**, för att hämta profilen igen och tilldela den till en variabeln. 

Ersätta dem i vinkelparenteser, `<>`, med rätt värden för var och en av de tre profiler som du behöver. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verifiera Traffic Manager fungerar
Kontrollera att Traffic Manager postprofiler fungerar genom att profilerna måste ha statusen för `Online` denna status baserat på sökvägen för avsökning av slutpunkten. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Visa nya profiler i Azure portal
Du kan kontrollera att alla tre profilerna skapas genom att titta på resurserna i den `luis-traffic-manager` resursgrupp.

![Skärmbild av Azure resource group luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Kontrollera profilstatusen för är Online
Traffic Manager ska avsöka sökvägen för varje slutpunkt för att kontrollera att den är online. Om den är online, status för underordnade profiler är `Online`. Detta visas på den **översikt** av varje profil. 

![Skärmbild av Azure Traffic Manager-profil översikt som visar övervaka Status för Online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Verifiera Traffic Manager avsökning fungerar
Ett annat sätt att verifiera traffic manager avsökning fungerar är med LUIS endpoint loggarna. På den [LUIS] [ LUIS] webbplats applistan sidan, exportera endpoint loggen för programmet. Eftersom Traffic Manager ska avsöka ofta för två slutpunkter, finns det poster i loggarna även om de har endast på några minuter. Kom ihåg att söka efter poster där frågan börjar med `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Verifiera DNS-svaret från Traffic Manager fungerar
Begära Traffic Managers överordnade profilen DNS med hjälp av ett DNS-klientbibliotek för att verifiera att DNS-svaret returnerar en LUIS-slutpunkt. DNS-namnet för den överordnade profilen är `luis-dns-parent.trafficmanager.net`.

Följande Node.js-kod gör en begäran för den överordnade profilen och returnerar en LUIS-slutpunkt:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Lyckat svar med LUIS-slutpunkten är:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Använd Traffic Manager överordnade profil
För att kunna hantera trafik över slutpunkter, måste du infoga ett anrop till Traffic Manager DNS för att hitta LUIS-slutpunkten. Det här anropet görs för varje slutpunkt LUIS-begäran och behöver simulera den geografiska platsen för användare på LUIS-klientprogrammet. Lägga till DNS-svarskod mellan klientprogrammet LUIS och begäran till LUIS för slutpunkten förutsägelser. 

## <a name="resolving-a-degraded-state"></a>Hur du löser ett degraderat tillstånd

Aktivera [diagnostikloggar](../../traffic-manager/traffic-manager-diagnostic-logs.md) för Traffic Manager att se varför slutpunktsstatus har degraderats.

## <a name="clean-up"></a>Rensa
Ta bort de två LUIS endpoint nycklarna, tre Traffic Manager-profiler och resursgruppen som innehåller dessa fem resurser. Detta görs från Azure-portalen. Du tar bort de fem resurserna i resurslistan. Ta sedan bort resursgruppen. 

## <a name="next-steps"></a>Nästa steg

Granska [mellanprogram](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) alternativen i BotFramework v4 att förstå hur den här koden för hantering av trafik kan läggas till en BotFramework robot. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
