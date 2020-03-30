---
title: Använda PowerShell för att hantera Traffic Manager i Azure
description: Med den här utbildningssökvägen kan du börja använda Azure PowerShell för Traffic Manager.
services: traffic-manager
documentationcenter: na
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 7886764a69eefa68be071a801bea65ae995fbdc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938502"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Använda PowerShell för att hantera Traffic Manager

Azure Resource Manager är det önskade hanteringsgränssnittet för tjänster i Azure. Azure Traffic Manager-profiler kan hanteras med Azure Resource Manager-baserade API:er och verktyg.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Resursmodell

Azure Traffic Manager har konfigurerats med hjälp av en samling inställningar som kallas Traffic Manager-profil. Den här profilen innehåller DNS-inställningar, trafikroutningsinställningar, slutpunktsövervakningsinställningar och en lista över tjänstslutpunkter som trafiken dirigeras till.

Varje Traffic Manager-profil representeras av en resurs av typen TrafficManagerProfiles. På REST API-nivå är URI för varje profil följande:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Konfigurera Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I de här anvisningarna används Microsoft Azure PowerShell. I följande artikel beskrivs hur du installerar och konfigurerar Azure PowerShell.

* [Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/overview)

Exemplen i den här artikeln förutsätter att du har en befintlig resursgrupp. Du kan skapa en resursgrupp med följande kommando:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager kräver att alla resursgrupper har en plats. Den här platsen används som standard för resurser som skapats i resursgruppen. Men eftersom Traffic Manager-profilresurser är globala, inte regionala, har valet av resursgruppsplats ingen inverkan på Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Om du vill skapa en `New-AzTrafficManagerProfile` Traffic Manager-profil använder du cmdlet:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

I följande tabell beskrivs parametrarna:

| Parameter | Beskrivning |
| --- | --- |
| Namn |Resursnamnet för profilresursen Traffic Manager. Profiler i samma resursgrupp måste ha unika namn. Det här namnet är skilt från DET DNS-namn som används för DNS-frågor. |
| ResourceGroupName |Namnet på resursgruppen som innehåller profilresursen. |
| TrafikRoutingMethod |Anger den trafikroutningsmetod som används för att avgöra vilken slutpunkt som returneras som svar på en DNS-fråga. Möjliga värden är "Prestanda", "Viktad" eller "Prioritet". |
| RelativeDnsName |Anger värdnamnsdelen av DNS-namnet som tillhandahålls av den här Traffic Manager-profilen. Det här värdet kombineras med DNS-domännamnet som används av Azure Traffic Manager för att bilda det fullständigt kvalificerade domännamnet (FQDN) för profilen. Om du till exempel anger värdet för "contoso" blir det "contoso.trafficmanager.net". |
| TTL |Anger DNS Time-to-Live (TTL) på några sekunder. Den här TTL informerar lokala DNS-resolvers och DNS-klienter hur länge DNS-svaren ska cachelagras för den här Traffic Manager-profilen. |
| MonitorProtocol |Anger vilket protokoll som ska användas för att övervaka slutpunktshälsan. Möjliga värden är "HTTP" och "HTTPS". |
| ÖvervakaPort |Anger den TCP-port som används för att övervaka slutpunktshälsa. |
| MonitorPath (bildskärmssök) |Anger sökvägen i förhållande till slutpunktsdomännamnet som används för att söka efter slutpunktshälsa. |

Cmdlet skapar en Traffic Manager-profil i Azure och returnerar ett motsvarande profilobjekt till PowerShell. Profilen innehåller inga slutpunkter. Mer information om hur du lägger till slutpunkter i en Traffic Manager-profil finns i Lägga till Slutpunkter för Traffic Manager.

## <a name="get-a-traffic-manager-profile"></a>Skaffa en Traffic Manager-profil

Om du vill hämta ett befintligt Traffic Manager-profilobjekt använder du cmdlet: `Get-AzTrafficManagerProfle`

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Den här cmdleten returnerar ett Traffic Manager-profilobjekt.

## <a name="update-a-traffic-manager-profile"></a>Uppdatera en Traffic Manager-profil

Ändra Traffic Manager profiler följer en 3-stegsprocess:

1. Hämta profilen `Get-AzTrafficManagerProfile` med eller använd profilen `New-AzTrafficManagerProfile`som returneras av .
2. Ändra profilen. Du kan lägga till och ta bort slutpunkter eller ändra slutpunkts- eller profilparametrar. Dessa ändringar är offlineåtgärder. Du ändrar bara det lokala objektet i minnet som representerar profilen.
3. Genomför ändringarna med `Set-AzTrafficManagerProfile` hjälp av cmdleten.

Alla profilegenskaper kan ändras förutom profilens RelativeDnsName. Om du vill ändra RelativeDnsName måste du ta bort profilen och en ny profil med ett nytt namn.

I följande exempel visas hur du ändrar profilens TTL:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Det finns tre typer av Traffic Manager-slutpunkter:

1. **Azure-slutpunkter** är tjänster som finns i Azure
2. **Externa slutpunkter** är tjänster som finns utanför Azure
3. **Kapslade slutpunkter** används för att skapa kapslade hierarkier för Traffic Manager-profiler. Kapslade slutpunkter möjliggör avancerade trafikdirigeringskonfigurationer för komplexa program.

I alla tre fallen kan slutpunkter läggas till på två sätt:

1. Använda en 3-stegsprocess som beskrivits tidigare. Fördelen med den här metoden är att flera slutpunktsändringar kan göras i en enda uppdatering.
2. Använda cmdleten New-AzTrafficManagerEndpoint. Den här cmdleten lägger till en slutpunkt i en befintlig Traffic Manager-profil i en enda åtgärd.

## <a name="adding-azure-endpoints"></a>Lägga till Azure-slutpunkter

Azure-slutpunkter referenstjänster som finns i Azure. Två typer av Azure-slutpunkter stöds:

1. Azure Apptjänst
2. Azure PublicIpAddress-resurser (som kan kopplas till en belastningsutjämnare eller ett nätverkskort för virtuell dator). PublicIpAddress måste ha ett DNS-namn tilldelat för att kunna användas i Traffic Manager.

I varje enskilt fall:

* Tjänsten anges med parametern targetResourceId `Add-AzTrafficManagerEndpointConfig` eller `New-AzTrafficManagerEndpoint`.
* Mål- och slutpunktslokaliseringen är underförstådda av TargetResourceId.
* Det är valfritt att ange vikten. Vikter används endast om profilen är konfigurerad för att använda trafikroutningsmetoden "Viktad". Annars ignoreras de. Om det anges måste värdet vara ett tal mellan 1 och 1000. Standardvärdet är "1".
* Det är valfritt att ange prioriteten. Prioriteter används endast om profilen är konfigurerad för att använda trafikroutningsmetoden Prioritet. Annars ignoreras de. Giltiga värden är från 1 till 1000 med lägre värden som anger en högre prioritet. Om det anges för en slutpunkt måste de anges för alla slutpunkter. Om det utelämnas tillämpas standardvärden från '1' i den ordning som slutpunkterna visas.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Exempel 1: Lägga till apptjänstslutpunkter med`Add-AzTrafficManagerEndpointConfig`

I det här exemplet skapar vi en Traffic Manager-profil `Add-AzTrafficManagerEndpointConfig` och lägger till två App Service-slutpunkter med hjälp av cmdleten.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Exempel 2: Lägga till en slutpunkt för offentlig gemensam adress med`New-AzTrafficManagerEndpoint`

I det här exemplet läggs en offentlig IP-adressresurs till i Traffic Manager-profilen. Den offentliga IP-adressen måste ha ett DNS-namn konfigurerat och kan vara bundet antingen till nätverkskortet för en virtuell dator eller till en belastningsutjämnare.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Lägga till externa slutpunkter

Traffic Manager använder externa slutpunkter för att dirigera trafik till tjänster som finns utanför Azure. Precis som med Azure-slutpunkter kan externa slutpunkter `Set-AzTrafficManagerProfile`läggas `New-AzTrafficManagerEndpoint`till antingen följt `Add-AzTrafficManagerEndpointConfig` av eller .

När du anger externa slutpunkter:

* Slutpunktsdomännamnet måste anges med parametern "Mål"
* Om trafikroutningsmetoden "Prestanda" används krävs endpointlocation. Annars är det valfritt. Värdet måste vara ett [giltigt Azure-regionnamn](https://azure.microsoft.com/regions/).
* Vikten och "Prioritet" är valfria.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exempel 1: Lägga till `Add-AzTrafficManagerEndpointConfig` externa slutpunkter med och`Set-AzTrafficManagerProfile`

I det här exemplet skapar vi en Traffic Manager-profil, lägger till två externa slutpunkter och genomför ändringarna.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Exempel 2: Lägga till externa slutpunkter med`New-AzTrafficManagerEndpoint`

I det här exemplet lägger vi till en extern slutpunkt i en befintlig profil. Profilen anges med hjälp av profil- och resursgruppsnamnen.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Lägga till slutpunkter för Kapslade

Varje Traffic Manager-profil anger en enda trafikroutningsmetod. Det finns dock scenarier som kräver mer sofistikerad trafikroutning än den routning som tillhandahålls av en enda Traffic Manager-profil. Du kan kapsla Traffic Manager-profiler för att kombinera fördelarna med mer än en trafikroutningsmetod. Kapslade profiler kan du åsidosätta standard traffic manager-beteendet för att stödja större och mer komplexa programdistributioner. Mer detaljerade exempel finns i [Nested Traffic Manager-profiler](traffic-manager-nested-profiles.md).

Kapslade slutpunkter konfigureras i den överordnade profilen med hjälp av en specifik slutpunktstyp, "NestedEndpoints". När du anger kapslade slutpunkter:

* Slutpunkten måste anges med parametern "targetResourceId"
* Om trafikroutningsmetoden "Prestanda" används krävs endpointlocation. Annars är det valfritt. Värdet måste vara ett [giltigt Azure-regionnamn](https://azure.microsoft.com/regions/).
* Vikten och prioriteten är valfria, som för Azure-slutpunkter.
* Parametern "MinChildEndpoints" är valfri. Standardvärdet är "1". Om antalet tillgängliga slutpunkter under det här tröskelvärdet anser den överordnade profilen att den underordnade profilen är "försämrad" och dirigerar om trafiken till andra slutpunkter i den överordnade profilen.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Exempel 1: Lägga till kapslade slutpunkter med och `Add-AzTrafficManagerEndpointConfig``Set-AzTrafficManagerProfile`

I det här exemplet skapar vi nya underordnade och överordnade profiler i Traffic Manager, lägger till underordnade som en kapslad slutpunkt till den överordnade och genomför ändringarna.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

För enkelhetens skull i det här exemplet har vi inte lagt till några andra slutpunkter till de underordnade eller överordnade profilerna.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Exempel 2: Lägga till kapslade slutpunkter med`New-AzTrafficManagerEndpoint`

I det här exemplet lägger vi till en befintlig underordnad profil som en kapslad slutpunkt i en befintlig överordnad profil. Profilen anges med hjälp av profil- och resursgruppsnamnen.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Lägga till slutpunkter från en annan prenumeration

Traffic Manager kan arbeta med slutpunkter från olika prenumerationer. Du måste växla till prenumerationen med den slutpunkt som du vill lägga till för att hämta den nödvändiga indata till Traffic Manager. Sedan måste du växla till prenumerationerna med Traffic Manager-profilen och lägga till slutpunkten i den. Exemplet nedan visar hur du gör detta med en offentlig IP-adress.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Uppdatera en slutpunkt för Traffic Manager

Det finns två sätt att uppdatera en befintlig Traffic Manager-slutpunkt:

1. Hämta Traffic Manager-profilen med `Get-AzTrafficManagerProfile`, uppdatera slutpunktsegenskaperna i `Set-AzTrafficManagerProfile`profilen och genomföra ändringarna med . Den här metoden har fördelen av att kunna uppdatera mer än en slutpunkt i en enda åtgärd.
2. Hämta Slutpunkten För `Get-AzTrafficManagerEndpoint`Traffic Manager med , uppdatera slutpunktsegenskaperna och genomföra ändringarna med `Set-AzTrafficManagerEndpoint`. Den här metoden är enklare eftersom den inte kräver indexering till slutpunktersmatrisen i profilen.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Exempel 1: Uppdatera slutpunkter med och `Get-AzTrafficManagerProfile``Set-AzTrafficManagerProfile`

I det här exemplet ändrar vi prioriteten för två slutpunkter i en befintlig profil.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Exempel 2: Uppdatera en `Get-AzTrafficManagerEndpoint` slutpunkt med och`Set-AzTrafficManagerEndpoint`

I det här exemplet ändrar vi vikten för en enskild slutpunkt i en befintlig profil.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Aktivera och inaktivera slutpunkter och profiler

Traffic Manager gör att enskilda slutpunkter kan aktiveras och inaktiveras, samt att aktivera och inaktivera hela profiler.
Dessa ändringar kan göras genom att hämta/uppdatera/ange slutpunkts- eller profilresurser. För att effektivisera dessa gemensamma åtgärder stöds de också via dedikerade cmdlets.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exempel 1: Aktivera och inaktivera en Traffic Manager-profil

Om du vill aktivera `Enable-AzTrafficManagerProfile`en Traffic Manager-profil använder du . Profilen kan anges med hjälp av ett profilobjekt. Profilobjektet kan skickas via pipelinen eller med parametern "-TrafficManagerProfile". I det här exemplet anger vi profilen efter profil- och resursgruppsnamnet.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Så här inaktiverar du en Traffic Manager-profil:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Cmdlet-cmdlet-cmdlet-cmdlet-cmdlet-anvisningarna för bekräftelse. Den här prompten kan undertryckas med parametern '-Force'.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exempel 2: Aktivera och inaktivera en Traffic Manager-slutpunkt

Om du vill aktivera en `Enable-AzTrafficManagerEndpoint`Traffic Manager-slutpunkt använder du . Det finns två sätt att ange slutpunkten

1. Använda ett TrafficManagerEndpoint-objekt som skickas via pipelinen eller med parametern "-TrafficManagerEndpoint"
2. Använda slutpunktsnamnet, slutpunktstypen, profilnamnet och resursgruppsnamnet:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

På samma sätt, för att inaktivera en Traffic Manager-slutpunkt:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Som `Disable-AzTrafficManagerProfile`med `Disable-AzTrafficManagerEndpoint` , cmdlet uppmanas för bekräftelse. Den här prompten kan undertryckas med parametern '-Force'.

## <a name="delete-a-traffic-manager-endpoint"></a>Ta bort en slutpunkt för Traffic Manager

Om du vill ta bort `Remove-AzTrafficManagerEndpoint` enskilda slutpunkter använder du cmdlet:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Denna cmdlet uppmanas för bekräftelse. Den här prompten kan undertryckas med parametern '-Force'.

## <a name="delete-a-traffic-manager-profile"></a>Ta bort en Traffic Manager-profil

Om du vill ta bort `Remove-AzTrafficManagerProfile` en Traffic Manager-profil använder du cmdleten och anger profil- och resursgruppsnamnen:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Denna cmdlet uppmanas för bekräftelse. Den här prompten kan undertryckas med parametern '-Force'.

Profilen som ska tas bort kan också anges med hjälp av ett profilobjekt:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Denna sekvens kan också ledas:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Nästa steg

[Trafikchef övervakning](traffic-manager-monitoring.md)

[Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
