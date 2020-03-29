---
title: Öka slutpunktskvoten – LUIS
titleSuffix: Azure Cognitive Services
description: Språkförståelse (LUIS) ger möjlighet att öka kvoten för slutpunktsbegäran utöver en enskild nyckels kvot. Detta görs genom att skapa fler nycklar för LUIS och lägga till dem i LUIS-programmet på sidan **Publicera** i avsnittet **Resurser och nycklar.**
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70256610"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Använda Microsoft Azure Traffic Manager för att hantera slutpunktskvot över nycklar
Språkförståelse (LUIS) ger möjlighet att öka kvoten för slutpunktsbegäran utöver en enskild nyckels kvot. Detta görs genom att skapa fler nycklar för LUIS och lägga till dem i LUIS-programmet på sidan **Publicera** i avsnittet **Resurser och nycklar.** 

Klientprogrammet måste hantera trafiken över nycklarna. LUIS gör inte det. 

I den här artikeln beskrivs hur du hanterar trafiken mellan nycklar med Azure [Traffic Manager][traffic-manager-marketing]. Du måste redan ha en tränad och publicerad LUIS-app. Om du inte har någon följer du [snabbstarten](luis-get-started-create-app.md)för den fördefinierade domänen . 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Ansluta till PowerShell i Azure-portalen
Öppna [Azure][azure-portal] PowerShell-fönstret i Azure-portalen. Ikonen för PowerShell-fönstret är **den>_** i det övre navigeringsfältet. Genom att använda PowerShell från portalen får du den senaste PowerShell-versionen och du autentiseras. PowerShell i portalen kräver ett [Azure Storage-konto.](https://azure.microsoft.com/services/storage/) 

![Skärmbild av Azure-portalen med Powershell-fönstret öppet](./media/traffic-manager/azure-portal-powershell.png)

I följande avsnitt används [PowerShell-cmdlets i Traffic Manager.](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager)

## <a name="create-azure-resource-group-with-powershell"></a>Skapa Azure-resursgrupp med PowerShell
Innan du skapar Azure-resurserna skapar du en resursgrupp som innehåller alla resurser. Namnge resursgruppen `luis-traffic-manager` och använda `West US`regionen är . Resursgruppens region lagrar metadata om gruppen. Det kommer inte att sakta ner dina resurser om de är i en annan region. 

Skapa resursgrupp med **[Cmdlet New-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)**

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>Skapa LUIS-nycklar för att öka den totala slutpunktskvoten
1. Skapa två **språk understanding-nycklar** i Azure-portalen, en i `West US` och en i `East US`. Använd den befintliga resursgruppen, som skapades `luis-traffic-manager`i föregående avsnitt, med namnet . 

    ![Skärmbild av Azure-portalen med två LUIS-nycklar i luis-traffic-manager-resursgruppen](./media/traffic-manager/luis-keys.png)

2. På [LUIS-webbplatsen,][LUIS] i avsnittet **Hantera,** på sidan **Azure Resources,** tilldelar du nycklarna till appen och publicerar om appen genom att välja knappen **Publicera** längst upp till höger. 

    Exempel-URL:en i **slutpunktskolumnen** använder en GET-begäran med slutpunktsnyckeln som frågeparameter. Kopiera de två nya nycklarnas slutpunktsadresser. De används som en del av Traffic Manager-konfigurationen senare i den här artikeln.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Hantera LUIS-slutpunktsbegäranden mellan nycklar med Traffic Manager
Traffic Manager skapar en ny DNS-åtkomstpunkt för dina slutpunkter. Det fungerar inte som en gateway eller proxy utan strikt på DNS-nivå. Det här exemplet ändrar inga DNS-poster. Den använder ett DNS-bibliotek för att kommunicera med Traffic Manager för att få rätt slutpunkt för den specifika begäran. _Varje_ begäran som är avsedd för LUIS kräver först en Traffic Manager-begäran för att avgöra vilken LUIS-slutpunkt som ska användas. 

### <a name="polling-uses-luis-endpoint"></a>Avsökning använder LUIS-slutpunkt
Traffic Manager avstÃrr er med jämna mellanrum för att kontrollera att slutpunkten fortfarande är tillgänglig. Den tillfrågade Traffic Manager-URL:en måste vara tillgänglig med en GET-begäran och returnera en 200.The Traffic Manager URL polled needs to be accessible with a GET request and return a 200. Slutpunkts-URL:en på sidan **Publicera** gör detta. Eftersom varje slutpunktsnyckel har olika väg- och frågesträngparametrar behöver varje slutpunktsnyckel en annan avsökningssökväg. Varje gång Traffic Manager avsökningar kostar den en kvotbegäran. Frågesträngparametern **q** för LUIS-slutpunkten är uttrycket som skickas till LUIS. Den här parametern används i stället för att skicka ett uttryck för traffic manager-avsökning i LUIS-slutpunktsloggen som felsökningsteknik samtidigt som Traffic Manager konfigureras.

Eftersom varje LUIS-slutpunkt behöver en egen sökväg behöver den en egen Traffic Manager-profil. Skapa en [ _kapslad_ Traffic Manager-arkitektur](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) för att hantera profiler. En föräldraprofil pekar på barnprofilerna och hanterar trafik över dem.

När Traffic Manager har konfigurerats, kom ihåg att ändra sökvägen för att använda parametern logg=false query string så att loggen inte fylls med avsökning.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Konfigurera Traffic Manager med kapslade profiler
I följande avsnitt skapas två underordnade profiler, en för östUTEN-tangenten och en för nyckeln West LUIS. Sedan skapas en överordnad profil och de två underordnade profilerna läggs till i den överordnade profilen. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Skapa profilen East US Traffic Manager med PowerShell
Om du vill skapa profilen Östra USA Traffic Manager finns det flera steg: skapa profil, lägg till slutpunkt och ange slutpunkt. En Traffic Manager-profil kan ha många slutpunkter, men varje slutpunkt har samma verifieringssökväg. Eftersom LUIS-slutpunktsadresserna för de östra och västra prenumerationerna skiljer sig åt på grund av region- och slutpunktsnyckeln, måste varje LUIS-slutpunkt vara en enda slutpunkt i profilen. 

1. Skapa profil med **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet

    Använd följande cmdlet för att skapa profilen. Se till att `appIdLuis` `subscriptionKeyLuis`ändra och . Prenumerationsnyckeln är för öst-USA LUIS-nyckeln. Om sökvägen inte är korrekt, inklusive LUIS-app-ID och slutpunktsnyckeln, är Traffic Manager-avsökningen `degraded` en status eftersom Trafikhantering inte kan begära LUIS-slutpunkten. Kontrollera att värdet `q` `traffic-manager-east` för är så att du kan se det här värdet i LUIS-slutpunktsloggarna.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    I den här tabellen förklaras varje variabel i cmdleten:
    
    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Name|luis-profil-eastus|Traffic Manager-namn i Azure-portalen|
    |-ResourceGroupName|luis-trafik-chef|Skapad i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [Routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda måste URL-begäran till Traffic Manager komma från användarens region. Om du går igenom en chatbot eller något annat program är det chatbotens ansvar att efterlikna regionen i samtalet till Trafikhanteraren. |
    |-RelativeDnsName|luis-dns-eastus|Detta är underdomänen för tjänsten: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Avsökningsintervall, 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Ersätt `<appIdLuis>` `<subscriptionKeyLuis>` och med dina egna värderingar.|
    
    En lyckad begäran har inget svar.

2. Lägg till öst-USA-slutpunkt med **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    I den här tabellen förklaras varje variabel i cmdleten:

    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Slutpunktnamn|luis-öst-slutpunkt|Slutpunktsnamn som visas under profilen|
    |-TrafficManagerProfile|$eastprofile|Använda profilobjekt som skapats i steg 1|
    |-Typ|Externapunktspunkter|Mer information finns i [Traffic Manager-slutpunkten][traffic-manager-endpoints] |
    |-Mål|eastus.api.cognitive.microsoft.com|Det här är domänen för LUIS-slutpunkten.|
    |-EndpointLocation|"Eastus"|Slutpunktens region|
    |-SlutpunktStatus|Enabled|Aktivera slutpunkt när den skapas|

    Det lyckade svaret ser ut:

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

    Ett lyckat svar kommer att vara samma svar som steg 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Skapa west US Traffic Manager-profilen med PowerShell
Så här skapar du profilen West US Traffic Manager: skapa profil, lägg till slutpunkt och ange slutpunkt.

1. Skapa profil med **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    Använd följande cmdlet för att skapa profilen. Se till att `appIdLuis` `subscriptionKeyLuis`ändra och . Prenumerationsnyckeln är för öst-USA LUIS-nyckeln. Om sökvägen inte är korrekt, inklusive LUIS-app-ID och slutpunktsnyckel, är Traffic Manager-avsökningen `degraded` en status eftersom Trafikhantering inte kan begära LUIS-slutpunkten. Kontrollera att värdet `q` `traffic-manager-west` för är så att du kan se det här värdet i LUIS-slutpunktsloggarna.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    I den här tabellen förklaras varje variabel i cmdleten:
    
    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Name|luis-profil-westus|Traffic Manager-namn i Azure-portalen|
    |-ResourceGroupName|luis-trafik-chef|Skapad i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [Routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda måste URL-begäran till Traffic Manager komma från användarens region. Om du går igenom en chatbot eller något annat program är det chatbotens ansvar att efterlikna regionen i samtalet till Trafikhanteraren. |
    |-RelativeDnsName|luis-dns-westus|Detta är underdomänen för tjänsten: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Avsökningsintervall, 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Ersätt `<appId>` `<subscriptionKey>` och med dina egna värderingar. Kom ihåg att den här slutpunktsnyckeln skiljer sig från den östra slutpunktsnyckeln|
    
    En lyckad begäran har inget svar.

2. Lägg till slutpunkt för västra USA med **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Slutpunktnamn|luis-väst-slutpunkt|Slutpunktsnamn som visas under profilen|
    |-TrafficManagerProfile|$westprofile|Använda profilobjekt som skapats i steg 1|
    |-Typ|Externapunktspunkter|Mer information finns i [Traffic Manager-slutpunkten][traffic-manager-endpoints] |
    |-Mål|westus.api.cognitive.microsoft.com|Det här är domänen för LUIS-slutpunkten.|
    |-EndpointLocation|"Westus"|Slutpunktens region|
    |-SlutpunktStatus|Enabled|Aktivera slutpunkt när den skapas|

    Det lyckade svaret ser ut:

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

3. Ange slutpunkt för västra USA med **[Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Ett lyckat svar är samma svar som steg 2.

### <a name="create-parent-traffic-manager-profile"></a>Skapa profil för överordnad Traffic Manager
Skapa den överordnade Traffic Manager-profilen och länka två underordnade Traffic Manager-profiler till den överordnade.

1. Skapa överordnad profil med **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Name|luis-profil-förälder|Traffic Manager-namn i Azure-portalen|
    |-ResourceGroupName|luis-trafik-chef|Skapad i föregående avsnitt|
    |-TrafficRoutingMethod|Prestanda|Mer information finns i [Routningsmetoder för Traffic Manager][routing-methods]. Om du använder prestanda måste URL-begäran till Traffic Manager komma från användarens region. Om du går igenom en chatbot eller något annat program är det chatbotens ansvar att efterlikna regionen i samtalet till Trafikhanteraren. |
    |-RelativeDnsName|luis-dns-förälder|Detta är underdomänen för tjänsten: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Avsökningsintervall, 30 sekunder|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Port och protokoll för LUIS är HTTPS/443|
    |-MonitorPath|`/`|Den här sökvägen spelar ingen roll eftersom de underordnade slutpunktsbanorna används i stället.|

    En lyckad begäran har inget svar.

2. Lägg till underordnad profil i östra USA till överordnad med text av typen **[Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** och **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Slutpunktnamn|använd för barn-endpoint|Östprofil|
    |-TrafficManagerProfile|$parentprofile|Profil för att tilldela den här slutpunkten till|
    |-Typ|KapsladeNdpoints|Mer information finns i [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$eastprofile. Id|ID för den underordnade profilen|
    |-SlutpunktStatus|Enabled|Slutpunktsstatus efter tillägg i överordnad|
    |-EndpointLocation|"Eastus"|[Namn på resursen Azure-region](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minsta antal till underordnade slutpunkter|

    Det lyckade svaret ser ut `child-endpoint-useast` så här och innehåller den nya slutpunkten:    

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

3. Lägg till underordnad profil i västra USA till överordnad med text av **[typen Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet och **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    I den här tabellen förklaras varje variabel i cmdleten:

    |Parameter för konfiguration|Variabelnamn eller värde|Syfte|
    |--|--|--|
    |-Slutpunktnamn|barn-endpoint-uswest|Västprofil|
    |-TrafficManagerProfile|$parentprofile|Profil för att tilldela den här slutpunkten till|
    |-Typ|KapsladeNdpoints|Mer information finns i [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig). |
    |-TargetResourceId|$westprofile. Id|ID för den underordnade profilen|
    |-SlutpunktStatus|Enabled|Slutpunktsstatus efter tillägg i överordnad|
    |-EndpointLocation|"Westus"|[Namn på resursen Azure-region](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|Minsta antal till underordnade slutpunkter|

    Det lyckade svaret ser ut `child-endpoint-useast` och innehåller `child-endpoint-uswest` både föregående slutpunkt och den nya slutpunkten:

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

    Ett lyckat svar är samma svar som steg 3.

### <a name="powershell-variables"></a>PowerShell-variabler
I föregående avsnitt skapades tre PowerShell-variabler: `$eastprofile`, `$westprofile`, `$parentprofile`. Dessa variabler används mot slutet av Traffic Manager-konfigurationen. Om du väljer att inte skapa variablerna, eller glömt till, eller om powershell-fönstret är tidsfördubblade, kan du använda PowerShell-cmdleten **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** för att hämta profilen igen och tilldela den till en variabel. 

Byt ut objekten `<>`i vinkelparenteser med rätt värden för var och en av de tre profiler du behöver. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verifiera att Traffic Manager fungerar
För att kontrollera att Traffic Manager-profilerna fungerar `Online` måste profilerna ha statusen Den här statusen baserat på slutpunktens avsökningssökväg. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Visa nya profiler i Azure-portalen
Du kan kontrollera att alla tre profilerna skapas genom att titta på resurserna i `luis-traffic-manager` resursgruppen.

![Skärmbild av Luis-Traffic-Manager för Azure-resursgrupp](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Verifiera att profilstatusen är Online
Traffic Manager avsöker sökvägen för varje slutpunkt för att se till att den är online. Om den är online är `Online`statusen för de underordnade profilerna . Detta visas i **översikten över** varje profil. 

![Skärmbild av profilöversikten för Azure Traffic Manager som visar övervakarstatus för online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Validera avsökning av Traffic Manager-avsökningar
Ett annat sätt att validera trafikhanterarens avsökningsarbeten är med LUIS-slutpunktsloggarna. På [LUIS][LUIS] listsidan för LUIS-webbplatsappar exporterar du slutpunktsloggen för programmet. Eftersom Traffic Manager avstämt ofta för de två slutpunkterna finns det poster i loggarna även om de bara har varit på några minuter. Kom ihåg att leta efter poster `traffic-manager-`där frågan börjar med .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Validera DNS-svar från Traffic Manager-arbeten
Om du vill verifiera att DNS-svaret returnerar en LUIS-slutpunkt begär du den överordnade profil-DNS:en trafikhanterar med hjälp av ett DNS-klientbibliotek. DNS-namnet för den `luis-dns-parent.trafficmanager.net`överordnade profilen är .

Följande Node.js-kod gör en begäran om den överordnade profilen och returnerar en LUIS-slutpunkt:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

Det lyckade svaret med LUIS-slutpunkten är:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Använda den överordnade profil i Traffic Manager
För att hantera trafik över slutpunkter måste du infoga ett anrop till Traffic Manager DNS för att hitta LUIS-slutpunkten. Det här anropet görs för varje LUIS-slutpunktsbegäran och måste simulera den geografiska platsen för användaren av LUIS-klientprogrammet. Lägg till DNS-svarskoden mellan LUIS-klientprogrammet och begäran till LUIS för slutpunktsförutsägelsen. 

## <a name="resolving-a-degraded-state"></a>Lösa ett försämrat tillstånd

Aktivera [diagnostikloggar](../../traffic-manager/traffic-manager-diagnostic-logs.md) för Traffic Manager för att se varför slutpunktsstatus försämras.

## <a name="clean-up"></a>Rensa
Ta bort de två LUIS-slutpunktsnycklarna, de tre Traffic Manager-profilerna och resursgruppen som innehöll dessa fem resurser. Detta görs från Azure-portalen. Du tar bort de fem resurserna från resurslistan. Ta sedan bort resursgruppen. 

## <a name="next-steps"></a>Nästa steg

Granska [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) alternativ i BotFramework v4 för att förstå hur denna trafikhanteringskod kan läggas till en BotFramework bot. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
