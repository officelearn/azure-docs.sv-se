---
title: Använda PowerShell för att hantera Traffic Manager i Azure
description: Med hjälp av PowerShell för Traffic Manager med Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 921788d1cd3ff24140bdff0c9b6a181e4ab7f0a8
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816228"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Använda PowerShell för att hantera Traffic Manager

Azure Resource Manager är det önskade hanteringsgränssnittet för tjänster i Azure. Azure Traffic Manager-profiler kan hanteras med hjälp av Azure Resource Manager-baserade API: er och verktyg.

## <a name="resource-model"></a>Resursmodell

Med Azure Traffic Manager konfigureras med hjälp av en samling inställningar som kallas en Traffic Manager-profil. Den här profilen innehåller DNS-inställningar, inställningar för routning av trafik, inställningarna för övervakning av slutpunkt och en lista över slutpunkter som trafik dirigeras.

Varje Traffic Manager-profil representeras av en resurs av typen 'TrafficManagerProfiles'. På REST API-nivå är URI: N för varje profil på följande sätt:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Konfigurera Azure PowerShell

Dessa anvisningar använder Microsoft Azure PowerShell. I följande artikel beskriver hur du installerar och konfigurerar Azure PowerShell.

* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)

Exemplen i den här artikeln förutsätter att du har en befintlig resursgrupp. Du kan skapa en resursgrupp med hjälp av följande kommando:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager kräver att alla resursgrupper har en plats. Den här platsen används som standard för resurser som skapades i resursgruppen. Men eftersom Traffic Manager profilresurser är globala, inte regionala är har valet av resursgruppens plats ingen inverkan på Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Du kan skapa en Traffic Manager-profil med den `New-AzureRmTrafficManagerProfile` cmdlet:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

I följande tabell beskrivs parametrarna:

| Parameter | Beskrivning |
| --- | --- |
| Namn |Resursnamnet för resursen för Traffic Manager-profil. Profiler i samma resursgrupp måste ha unika namn. Det här namnet skiljer sig från DNS-namn som används för DNS-frågor. |
| ResourceGroupName |Namnet på resursgruppen som innehåller profilen resursen. |
| TrafficRoutingMethod |Anger den metod för routning av nätverkstrafik som används för att avgöra vilken slutpunkt returneras svar en DNS-fråga. Möjliga värden är ”Performance”, ”viktat” eller ”prioritet”. |
| RelativeDnsName |Anger värdnamnet delen av DNS-namn som tillhandahålls av den här Traffic Manager-profilen. Det här värdet kombineras med DNS-domännamnet som används av Azure Traffic Manager för att skapa det fullständigt kvalificerade domännamnet (FQDN) för profilen. Till exempel blir ställa in värdet för ”contoso” contoso.trafficmanager.net. |
| TTL |Anger DNS-Time-to-Live (TTL), i sekunder. Den här TTL informerar den lokala DNS-matchare och DNS-klienter som är hur lång tid att cache DNS-svar för den här Traffic Manager-profilen. |
| MonitorProtocol |Anger vilket protokoll du använder för att övervaka hälsan för slutpunkten. Möjliga värden är ”HTTP” och ”HTTPS”. |
| MonitorPort |Anger TCP-port som används för att övervaka hälsan för slutpunkten. |
| MonitorPath |Anger sökväg i förhållande till domännamnet på slutpunkten används för att söka efter slutpunktshälsa. |

Cmdleten skapar en Traffic Manager-profil i Azure och returnerar ett motsvarande objekt i profilen till PowerShell. Nu kan innehåller profilen inte några slutpunkter. Mer information om att lägga till slutpunkter till en Traffic Manager-profil finns i att lägga till Traffic Manager-slutpunkter.

## <a name="get-a-traffic-manager-profile"></a>Hämta en Traffic Manager-profil

Använd för att hämta ett befintligt objekt för Traffic Manager-profil i `Get-AzureRmTrafficManagerProfle` cmdlet:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Denna cmdlet returnerar ett objekt för Traffic Manager-profil.

## <a name="update-a-traffic-manager-profile"></a>Uppdatera en Traffic Manager-profil

Ändra Traffic Manager-profiler följer en process i 3 steg:

1. Hämta en profil med hjälp av `Get-AzureRmTrafficManagerProfile` eller använda den profil som returneras av `New-AzureRmTrafficManagerProfile`.
2. Ändra profilen. Du kan lägga till och ta bort slutpunkter eller ändra slutpunkten eller profilen för parametrar. Dessa ändringar är offline åtgärder. Endast ändrar det lokala objektet i minnet som representerar profilen.
3. Genomför ändringarna med hjälp av den `Set-AzureRmTrafficManagerProfile` cmdlet.

Alla profilegenskaper kan ändras utom profilens RelativeDnsName. Om du vill ändra RelativeDnsName, måste du ta bort profil och en ny profil med ett nytt namn.

I följande exempel visar hur du ändrar profilens TTL:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Det finns tre typer av Traffic Manager-slutpunkter:

1. **Azure-slutpunkter** är i Azure-tjänster
2. **Externa slutpunkter** är utanför Azure-tjänster
3. **Kapslade slutpunkter** används för att skapa kapslade hierarkier av Traffic Manager-profiler. Kapslade slutpunkter kan avancerade routning av nätverkstrafik konfigurationer för komplexa program.

I samtliga tre fall kan du lägga till slutpunkter på två sätt:

1. Med hjälp av en process i 3 steg som beskrivs ovan. Fördelen med den här metoden är att flera slutpunkt kan du göra ändringar i en enda uppdatering.
2. Med hjälp av cmdleten New-AzureRmTrafficManagerEndpoint. Denna cmdlet lägger till en slutpunkt i en befintlig Traffic Manager-profil i en enda åtgärd.

## <a name="adding-azure-endpoints"></a>Att lägga till Azure-slutpunkter

Azure-slutpunkter referera till tjänster i Azure. Två typer av Azure-slutpunkter stöds:

1. Azure App Service
2. Azure PublicIpAddress-resurser (som kan kopplas till en belastningsutjämnare eller en virtuell dator NIC). PublicIpAddress måste ha ett DNS-namn som tilldelats som ska användas i Traffic Manager.

I båda fallen:

* Tjänsten har angetts med parametern ”targetResourceId” av `Add-AzureRmTrafficManagerEndpointConfig` eller `New-AzureRmTrafficManagerEndpoint`.
* ”Mål” och ”EndpointLocation” underförstådd av TargetResourceId.
* Det är valfritt att ange den ”vikten”. Vikterna används bara om profilen är konfigurerad för att använda metoden 'Viktat' routning av nätverkstrafik. I annat fall ignoreras de. Om anges måste värdet vara ett tal mellan 1 och 1000. Standardvärdet är '1'.
* Det är valfritt att ange den ”prioriteten”. Prioriteter används bara om profilen är konfigurerad för att använda metoden ”prioritet” routning av nätverkstrafik. I annat fall ignoreras de. Giltiga värden är mellan 1 och 1000 med lägre värden som anger en högre prioritet. Om anges för en slutpunkt måste de anges för alla slutpunkter. Om det utelämnas används tillämpas standardvärden som börjar med ”1” i angiven ordning av slutpunkterna.

### <a name="example-1-adding-app-service-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exempel 1: Lägga till App Service-slutpunkter med hjälp av `Add-AzureRmTrafficManagerEndpointConfig`

I det här exemplet skapar vi en Traffic Manager-profil och lägga till två App Service-slutpunkter med hjälp av den `Add-AzureRmTrafficManagerEndpointConfig` cmdlet.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exempel 2: Att lägga till en publicIpAddress-slutpunkten med `New-AzureRmTrafficManagerEndpoint`

I det här exemplet läggs en offentlig IP-adressresurs till Traffic Manager-profilen. Offentliga IP-adress måste ha ett DNS-namn som har konfigurerats och kan vara kopplat till nätverkskort för en virtuell dator eller till en belastningsutjämnare.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Lägga till externa slutpunkter

Traffic Manager använder externa slutpunkter för att dirigera trafik till tjänster som ligger utanför Azure. Som med Azure-slutpunkter, externa slutpunkter kan läggas till antingen med hjälp av `Add-AzureRmTrafficManagerEndpointConfig` följt av `Set-AzureRmTrafficManagerProfile`, eller `New-AzureRMTrafficManagerEndpoint`.

När du anger externa slutpunkter:

* Slutpunkt-domännamnet måste anges med parametern ”mål”
* Om ”Performance” trafikdirigeringsmetoden används krävs EndpointLocation. Annars är valfritt. Värdet måste vara en [giltiga Azure-regionnamn](https://azure.microsoft.com/regions/).
* ”Vikt” och ”prioritet” är valfria.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exempel 1: Lägga till externa slutpunkter med hjälp av `Add-AzureRmTrafficManagerEndpointConfig` och `Set-AzureRmTrafficManagerProfile`

I det här exemplet vi skapa en Traffic Manager-profil, lägga till två externa slutpunkter och sedan spara ändringarna.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exempel 2: Lägga till externa slutpunkter med hjälp av `New-AzureRmTrafficManagerEndpoint`

I det här exemplet lägger vi till en extern slutpunkt i en befintlig profil. Profilen har angetts med hjälp av namnen på profilen och resursen.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Lägga till ”kapslade' slutpunkter

Varje Traffic Manager-profil anger en enda metod för routning av nätverkstrafik. Det finns dock scenarier som kräver mer sofistikerade routning av nätverkstrafik än routning tillhandahålls som en enda Traffic Manager-profil. Du kan kapsla Traffic Manager-profiler om du vill kombinera fördelarna med fler än en metod för routning av nätverkstrafik. Kapslade profiler kan du åsidosätta standardbeteendet för Traffic Manager stöd för större och mer komplexa distributioner av program. Mer detaljerade exempel finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

Kapslade slutpunkter har konfigurerats på den överordnade-profil som använder en viss slutpunkts-typ, 'NestedEndpoints'. När du anger kapslade slutpunkter:

* Slutpunkten måste anges med parametern ”targetResourceId”
* Om ”Performance” trafikdirigeringsmetoden används krävs EndpointLocation. Annars är valfritt. Värdet måste vara en [giltiga Azure-regionnamn](https://azure.microsoft.com/regions/).
* ”Vikt” och ”prioritet” är valfria, som Azure-slutpunkter.
* ”MinChildEndpoints”-parametern är valfri. Standardvärdet är '1'. Om antalet tillgängliga slutpunkter sjunker under det här tröskelvärdet, anses den överordnade profilen underordnade profilen 'degraderad' och diverts trafik till de andra slutpunkterna i den överordnade profilen.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exempel 1: Lägga till kapslade slutpunkter med hjälp av `Add-AzureRmTrafficManagerEndpointConfig` och `Set-AzureRmTrafficManagerProfile`

I det här exemplet vi skapar nya Traffic Manager över- och underordnade profiler, Lägg till underordnad som en kapslad slutpunkt i överordnat och sedan spara ändringarna.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Kortfattat i det här exemplet vi inte lägga till andra slutpunkter till över- eller underordnad-profiler.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exempel 2: Lägga till kapslade slutpunkter med hjälp av `New-AzureRmTrafficManagerEndpoint`

I det här exemplet vi lägga till en befintlig profil för underordnade som en kapslad slutpunkt i en befintlig överordnad profil. Profilen har angetts med hjälp av namnen på profilen och resursen.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Lägga till slutpunkter från en annan prenumeration

Traffic Manager kan arbeta med slutpunkter från olika prenumerationer. Du måste växla till en prenumeration med den slutpunkt som du vill lägga till för att hämta nödvändiga indata till Traffic Manager. Sedan måste växla till prenumerationer med Traffic Manager-profilen och lägga till encpoint till den. I exemplet nedan visar hur du gör detta med en offentlig IP-adress.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Uppdatera en Traffic Manager-slutpunkt

Det finns två sätt att uppdatera en befintlig Traffic Manager-slutpunkt:

1. Hämta Traffic Manager-profil med `Get-AzureRmTrafficManagerProfile`, uppdatera Slutpunktsegenskaper i profilen och genomför ändringarna med `Set-AzureRmTrafficManagerProfile`. Den här metoden har fördelen att kunna uppdatera mer än en slutpunkt i en enda åtgärd.
2. Hämta Traffic Manager-slutpunkten med `Get-AzureRmTrafficManagerEndpoint`, uppdatera Slutpunktsegenskaper för och genomför ändringarna med `Set-AzureRmTrafficManagerEndpoint`. Den här metoden är enklare, eftersom det inte kräver att indexera i matrisen slutpunkter i profilen.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exempel 1: Uppdaterar slutpunkter med `Get-AzureRmTrafficManagerProfile` och `Set-AzureRmTrafficManagerProfile`

I det här exemplet ändra vi prioritet på två slutpunkter i en befintlig profil.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exempel 2: Uppdaterar en slutpunkt med hjälp av `Get-AzureRmTrafficManagerEndpoint` och `Set-AzureRmTrafficManagerEndpoint`

I det här exemplet ändra vi vikten för en enda slutpunkt i en befintlig profil.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Aktiverar och inaktiverar slutpunkter och profiler

Traffic Manager kan enskilda slutpunkter som är aktiverade och inaktiverade, och låter aktivering och inaktivering av hela profiler.
Dessa ändringar kan göras genom att hämta/uppdatera/inställningen slutpunkt eller profil-resurser. För att effektivt dessa vanliga åtgärder kan stöds de också via dedikerade cmdletar.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exempel 1: Aktivera och inaktivera en Traffic Manager-profil

Om du vill aktivera en Traffic Manager-profil, använda `Enable-AzureRmTrafficManagerProfile`. Profilen kan anges med en profilobjektet. Profilobjektet kan skickas via pipelinen eller genom att använda den '-TrafficManagerProfile-parametern. I det här exemplet anger vi profil med namnet på profilen och resursen.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Så här inaktiverar en Traffic Manager-profil:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Cmdleten Disable-AzureRmTrafficManagerProfile uppmanas att bekräfta. Varningen kan förhindras med hjälp av den ”-Force-parametern.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exempel 2: Aktivera och inaktivera en Traffic Manager-slutpunkt

Om du vill aktivera en Traffic Manager-slutpunkt, använda `Enable-AzureRmTrafficManagerEndpoint`. Det finns två sätt att ange slutpunkten

1. Med hjälp av ett TrafficManagerEndpoint-objekt som skickas via pipelinen eller den ”-TrafficManagerEndpoint” parametern
2. Med hjälp av namnet på slutpunkten, typ av slutpunkt, namn och resursgruppens namn:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

På samma sätt kan du inaktivera Traffic Manager-slutpunkten:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Precis som med `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` cmdlet uppmanas att bekräfta. Varningen kan förhindras med hjälp av den ”-Force-parametern.

## <a name="delete-a-traffic-manager-endpoint"></a>Ta bort en Traffic Manager-slutpunkt

Ta bort enskilda slutpunkter med den `Remove-AzureRmTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Denna cmdlet uppmanas att bekräfta. Varningen kan förhindras med hjälp av den ”-Force-parametern.

## <a name="delete-a-traffic-manager-profile"></a>Ta bort en Traffic Manager-profil

Ta bort en Traffic Manager-profil genom att använda den `Remove-AzureRmTrafficManagerProfile` cmdlet, ange namnen på profilen och resurs:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Denna cmdlet uppmanas att bekräfta. Varningen kan förhindras med hjälp av den ”-Force-parametern.

Också du kan ange profilen som ska tas bort med hjälp av en profil-objekt:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Den här sekvensen kan även pipas:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Nästa steg

[Traffic Manager-övervakning](traffic-manager-monitoring.md)

[Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
