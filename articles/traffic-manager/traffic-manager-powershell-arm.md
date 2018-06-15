---
title: Använda PowerShell för att hantera Traffic Manager i Azure | Microsoft Docs
description: Med hjälp av PowerShell för Traffic Manager med Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 951e845e23a1ed0cbdc83fc24a97a545f00c52ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526649"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Använda PowerShell för att hantera Traffic Manager

Azure Resource Manager är det primära gränssnittet för tjänster i Azure. Azure Traffic Manager-profiler kan hanteras med Azure Resource Manager-baserade API: er och verktyg.

## <a name="resource-model"></a>Resursmodell

Azure Traffic Manager konfigureras med hjälp av en samling inställningar som kallas en Traffic Manager-profilen. Den här profilen innehåller DNS-inställningar, trafikdirigeringsinställningar, slutpunktsövervakningsinställningar och en lista över slutpunkter som trafik dirigeras.

Varje trafikhanterarprofil representeras av en resurs av typen 'TrafficManagerProfiles'. På REST API-nivå är URI: N för varje profil:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Konfigurera Azure PowerShell

Dessa anvisningar använder Microsoft Azure PowerShell. I följande artikel förklarar hur du installerar och konfigurerar du Azure PowerShell.

* [Installera och konfigurera Azure PowerShell](/powershell/azure/overview)

Exemplen i den här artikeln förutsätter att du har en befintlig resursgrupp. Du kan skapa en resursgrupp med följande kommando:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager kräver att alla resursgrupper har en plats. Den här platsen används som standard för resurser som skapats i resursgruppen. Men eftersom resurser för Traffic Manager-profil är globala, inte regional är påverkar valet av resursgruppens plats inte på Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Trafikhanterarprofil

Du kan skapa en Traffic Manager-profil med den `New-AzureRmTrafficManagerProfile` cmdlet:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

I följande tabell beskrivs parametrarna:

| Parameter | Beskrivning |
| --- | --- |
| Namn |Resursnamnet för resursen Traffic Manager-profilen. Profiler i samma resursgrupp måste ha unika namn. Det här namnet skiljer sig från DNS-namn som används för DNS-frågor. |
| resourceGroupName |Namnet på resursgruppen som innehåller profilen för resursen. |
| TrafficRoutingMethod |Anger den metod för routning av nätverkstrafik som används för att avgöra vilken slutpunkt returneras svar en DNS-fråga. Möjliga värden är 'Prestanda', 'Viktat' eller 'Priority'. |
| RelativeDnsName |Anger hostname-delen av DNS-namn som tillhandahålls av den här trafikhanterarprofilen. Det här värdet kombineras med DNS-domännamnet som används av Azure Traffic Manager för att bilda fullständigt kvalificerade domännamnet (FQDN) för profilen. Till exempel blir ange värdet för ”contoso” contoso.trafficmanager.net. |
| TTL-VÄRDE |Anger DNS-Time-to-Live (TTL), i sekunder. Den här TTL informerar den lokala DNS-matchare och DNS-klienter, hur lång tid att cache DNS-svar för den här Traffic Manager-profilen. |
| MonitorProtocol |Anger vilket protokoll som ska använda för att övervaka hälsotillståndet för slutpunkten. Möjliga värden är 'HTTP' och 'HTTPS'. |
| MonitorPort |Anger den TCP-port som används för att övervaka hälsotillståndet för slutpunkten. |
| MonitorPath |Anger sökväg i förhållande till slutpunkten domännamnet till avsökning för slutpunkten health. |

Cmdleten skapar en Traffic Manager-profil i Azure och returnerar ett motsvarande profil-objekt till PowerShell. Profilen som innehåller nu inte några slutpunkter. Mer information om att lägga till slutpunkter i en Traffic Manager-profilen finns [att lägga till slutpunkter för Traffic Manager](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Hämta en Trafikhanterarprofil

Använd för att hämta ett befintligt objekt för Traffic Manager-profilen i `Get-AzureRmTrafficManagerProfle` cmdlet:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Denna cmdlet returnerar ett objekt för Traffic Manager-profilen.

## <a name="update-a-traffic-manager-profile"></a>Uppdatera en Traffic Manager-profil

Ändra Traffic Manager-profiler följer en process i steg 3:

1. Hämta en profil med hjälp av `Get-AzureRmTrafficManagerProfile` eller använda den profil som returneras av `New-AzureRmTrafficManagerProfile`.
2. Ändra profilen. Du kan lägga till och ta bort slutpunkter eller ändra slutpunkten eller profil parametrar. Dessa ändringar är offline åtgärder. Endast ändrar det lokala objektet i minnet som representerar profilen.
3. Genomför ändringarna med hjälp av den `Set-AzureRmTrafficManagerProfile` cmdlet.

Alla profilegenskaper kan ändras utom profilens RelativeDnsName. Om du vill ändra RelativeDnsName, måste du ta bort profil och en ny profil med ett nytt namn.

Exemplet nedan visar hur du ändrar den profilen TTL-värde:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Det finns tre typer av slutpunkter för Traffic Manager:

1. **Azure-slutpunkter** är tjänster som finns på Azure
2. **Externa slutpunkter** är tjänster som ligger utanför Azure
3. **Kapslade slutpunkter** används för att skapa kapslade hierarkier av Traffic Manager-profiler. Kapslade slutpunkter aktivera avancerade routning av nätverkstrafik konfigurationer för komplexa program.

I samtliga tre fall kan du lägga till slutpunkter på två sätt:

1. Med hjälp av en process för 3 steg som beskrivs ovan. Fördelen med den här metoden är att flera endpoint kan ändras i en enda uppdatering.
2. Med hjälp av cmdlet New-AzureRmTrafficManagerEndpoint. Denna cmdlet lägger till en slutpunkt i en befintlig Traffic Manager-profil i en enda åtgärd.

## <a name="adding-azure-endpoints"></a>Lägga till Azure-slutpunkter

Azure-slutpunkter referera till tjänster i Azure. Två typer av Azure-slutpunkter stöds:

1. Azure Web Apps
2. Azure PublicIpAddress-resurser (som kan kopplas till en belastningsutjämnare eller en virtuell dator NIC). PublicIpAddress måste ha ett DNS-namn som tilldelats som ska användas i Traffic Manager.

I varje fall:

* Tjänsten har angetts med parametern 'targetResourceId' för `Add-AzureRmTrafficManagerEndpointConfig` eller `New-AzureRmTrafficManagerEndpoint`.
* 'Target' och 'EndpointLocation' underförstås av TargetResourceId.
* Ange 'vikten' är valfritt. Vikten används bara om profilen är konfigurerad för att använda metoden 'Viktat' routning av nätverkstrafik. I annat fall ignoreras de. Om anges måste värdet vara ett tal mellan 1 och 1000. Standardvärdet är '1'.
* Ange den 'prioriteten' är valfritt. Prioriteringar används bara om profilen är konfigurerad för att använda metoden 'Priority' routning av nätverkstrafik. I annat fall ignoreras de. Giltiga värden är från 1 till 1000 med lägre värden som anger en högre prioritet. Om för en slutpunkt, måste de anges för alla slutpunkter. Om det utelämnas används standardvärden från '1' i ordningsföljden av slutpunkterna.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exempel 1: Lägga till slutpunkter för webbprogram med hjälp av `Add-AzureRmTrafficManagerEndpointConfig`

I det här exemplet vi skapa en Traffic Manager-profilen och lägga till två slutpunkter för webbprogram som använder den `Add-AzureRmTrafficManagerEndpointConfig` cmdlet.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exempel 2: Lägga till en publicIpAddress-slutpunkten med `New-AzureRmTrafficManagerEndpoint`

I det här exemplet läggs en offentlig IP-adressresurs till Traffic Manager-profilen. Den offentliga IP-adressen måste ha ett DNS-namn som har konfigurerats och kan bindas till nätverkskort på en virtuell dator eller till en belastningsutjämnare.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Lägga till externa slutpunkter

Traffic Manager använder externa slutpunkter för att dirigera trafik till tjänster som ligger utanför Azure. Som med Azure-slutpunkter externa slutpunkter kan läggas till antingen med hjälp av `Add-AzureRmTrafficManagerEndpointConfig` följt av `Set-AzureRmTrafficManagerProfile`, eller `New-AzureRMTrafficManagerEndpoint`.

När du anger externa slutpunkter:

* Slutpunkten domännamnet måste anges med parametern 'Target'
* Om metoden routning av nätverkstrafik, prestanda, används krävs EndpointLocation. Annars är valfria. Värdet måste vara en [giltig Azure regionnamn](https://azure.microsoft.com/regions/).
* 'Vikt' och 'Priority' är valfria.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exempel 1: Lägga till externa slutpunkter som använder `Add-AzureRmTrafficManagerEndpointConfig` och `Set-AzureRmTrafficManagerProfile`

I det här exemplet vi skapa en Traffic Manager-profil, lägga till två externa slutpunkter och spara ändringarna.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exempel 2: Lägga till externa slutpunkter med hjälp av `New-AzureRmTrafficManagerEndpoint`

I det här exemplet vi lägga till en extern slutpunkt i en befintlig profil. Profilen har angetts med hjälp av gruppnamn profil och resurs.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Lägga till 'kapslade-slutpunkter

Varje Traffic Manager-profil anger en enda metod för routning av nätverkstrafik. Det finns emellertid scenarier som kräver mer avancerad routning av nätverkstrafik än routning tillhandahålls som en enskild Traffic Manager-profil. Du kan kapsla Traffic Manager-profiler för att kombinera fördelarna med fler än en metod för routning av nätverkstrafik. Kapslade profiler kan du åsidosätta standardbeteendet för Traffic Manager att stödja större och mer komplexa programdistributioner. Mer detaljerad exemplen finns [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

Kapslade slutpunkter har konfigurerats på den överordnade-profil med hjälp av en viss slutpunkts-typ, 'NestedEndpoints'. När du anger kapslade slutpunkter:

* Slutpunkten måste anges med parametern 'targetResourceId'
* Om metoden routning av nätverkstrafik, prestanda, används krävs EndpointLocation. Annars är valfria. Värdet måste vara en [giltig Azure regionnamn](http://azure.microsoft.com/regions/).
* 'Vikt' och 'Priority' är valfria, som Azure-slutpunkter.
* 'MinChildEndpoints'-parametern är valfri. Standardvärdet är '1'. Om antalet tillgängliga slutpunkter understiger tröskelvärdet, anses överordnade profilen underordnade profilen 'försämrad' och diverts trafik till andra slutpunkter i profilen för överordnade.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exempel 1: Lägga till kapslade slutpunkter som använder `Add-AzureRmTrafficManagerEndpointConfig` och `Set-AzureRmTrafficManagerProfile`

I det här exemplet vi skapa ny Traffic Manager underordnade och överordnade profiler, lägga till underordnad som en kapslad slutpunkt till överordnat och spara ändringarna.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Planeringsaspekter i det här exemplet vi inte till andra slutpunkter i över- eller underordnad-profiler.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exempel 2: Lägga till kapslade slutpunkter med hjälp av `New-AzureRmTrafficManagerEndpoint`

I det här exemplet vi lägga till en befintlig profil för underordnade som en kapslad slutpunkt i en befintlig överordnad profil. Profilen har angetts med hjälp av gruppnamn profil och resurs.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Att lägga till slutpunkter från en annan prenumeration

Traffic Manager fungerar med slutpunkter från olika prenumerationer. Du måste växla till prenumerationen med den slutpunkt som du vill lägga till för att hämta nödvändiga indata i Traffic Manager. Du måste växla till prenumerationer med Traffic Manager-profilen och lägga till encpoint. I exemplet nedan visar hur du gör detta med en offentlig IP-adress.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Uppdatera en Traffic Manager-slutpunkt

Det finns två sätt att uppdatera en befintlig Traffic Manager-slutpunkt:

1. Hämta Traffic Manager-profil med `Get-AzureRmTrafficManagerProfile`, uppdatera egenskaperna för slutpunkten inom profilen och genomför ändringarna med `Set-AzureRmTrafficManagerProfile`. Den här metoden har fördelen att kunna uppdatera mer än en slutpunkt i en enda åtgärd.
2. Hämta Traffic Manager-slutpunkten med `Get-AzureRmTrafficManagerEndpoint`, uppdaterar slutpunktsegenskaperna och genomför ändringarna med `Set-AzureRmTrafficManagerEndpoint`. Den här metoden är enklare, eftersom det inte kräver att indexera i matrisen slutpunkter i profilen.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exempel 1: Uppdatera slutpunkter med hjälp av `Get-AzureRmTrafficManagerProfile` och `Set-AzureRmTrafficManagerProfile`

I det här exemplet ändrar vi prioritet på två slutpunkter i en befintlig profil.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exempel 2: Uppdatera slutpunkten med `Get-AzureRmTrafficManagerEndpoint` och `Set-AzureRmTrafficManagerEndpoint`

I det här exemplet ändrar vi vikten för en enda slutpunkt i en befintlig profil.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Aktiverar och inaktiverar slutpunkter och profiler

Traffic Manager kan enskilda slutpunkter aktiverade och inaktiverade, samt att tillåta aktivering och inaktivering av hela profiler.
Dessa ändringar kan göras genom att hämta/uppdatera/inställningen endpoint eller profil resurser. Om du vill förenkla dessa vanliga åtgärder stöds de också via dedikerade cmdlets.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exempel 1: Aktivera och inaktivera Traffic Manager-profilen

Aktivera en Traffic Manager-profil med `Enable-AzureRmTrafficManagerProfile`. Profilen kan anges med en profilobjektet. Profil-objekt kan skickas via pipeline eller genom att använda den '-TrafficManagerProfile-parametern. I det här exemplet anger vi profil med namnet på profilen och resurs.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Inaktivera Traffic Manager-profilen:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Inaktivera AzureRmTrafficManagerProfile cmdlet uppmanas att bekräfta. Den här uppmaningen kan undertryckas med hjälp av den '-Force-parametern.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exempel 2: Aktivera och inaktivera Traffic Manager-slutpunkt

Aktivera en Traffic Manager-slutpunkt med `Enable-AzureRmTrafficManagerEndpoint`. Det finns två sätt att ange slutpunkten

1. Med hjälp av en TrafficManagerEndpoint-objektet som överförs via pipeline eller den '-TrafficManagerEndpoint-parameter
2. Med hjälp av namnet på slutpunkten, typen av slutpunkt, profilnamn och resursgruppens namn:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

På liknande sätt kan du inaktivera Traffic Manager-slutpunkten:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Precis som med `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` cmdlet uppmanas att bekräfta. Den här uppmaningen kan undertryckas med hjälp av den '-Force-parametern.

## <a name="delete-a-traffic-manager-endpoint"></a>Ta bort en Traffic Manager-slutpunkt

Ta bort enskilda slutpunkter med den `Remove-AzureRmTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Denna cmdlet uppmanas att bekräfta. Den här uppmaningen kan undertryckas med hjälp av den '-Force-parametern.

## <a name="delete-a-traffic-manager-profile"></a>Ta bort en Traffic Manager-profil

Ta bort en Traffic Manager-profilen genom att använda den `Remove-AzureRmTrafficManagerProfile` cmdlet, anger gruppnamn profil och resursen:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Denna cmdlet uppmanas att bekräfta. Den här uppmaningen kan undertryckas med hjälp av den '-Force-parametern.

Också du kan ange profilen som ska tas bort med hjälp av en profil-objekt:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Den här sekvensen kan också skickas:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Nästa steg

[Traffic Manager-övervakning](traffic-manager-monitoring.md)

[Prestandaöverväganden för Traffic Manager](traffic-manager-performance-considerations.md)
