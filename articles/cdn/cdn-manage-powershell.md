---
title: Hantera Azure CDN med PowerShell | Microsoft-dokument
description: Lär dig hur du använder Azure PowerShell-cmdlets för att hantera Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: allensu
ms.openlocfilehash: 22602a1ea64e3dbca34d0c366cf6aa0dc6f35662
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260555"
---
# <a name="manage-azure-cdn-with-powershell"></a>Hantera Azure CDN med PowerShell
PowerShell är en av de mest flexibla metoderna för att hantera dina Azure CDN-profiler och slutpunkter.  Du kan använda PowerShell interaktivt eller genom att skriva skript för att automatisera hanteringsuppgifter.  Den här självstudien visar flera av de vanligaste uppgifterna du kan utföra med PowerShell för att hantera dina Azure CDN-profiler och slutpunkter.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill använda PowerShell för att hantera dina Azure CDN-profiler och slutpunkter måste Azure PowerShell-modulen vara installerad.  Mer information om hur du installerar Azure `Connect-AzAccount` PowerShell och ansluter till Azure med cmdlet finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Du måste logga `Connect-AzAccount` in med innan du kan köra Azure PowerShell-cmdlets.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Lista Azure CDN-cmdlets
Du kan lista alla Azure CDN-cmdlets med `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Confirm-AzCdnEndpointProbeURL                      1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomain                          1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomainHttps                     1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomainHttps                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEdgeNode                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointResourceUsage                     1.4.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileResourceUsage                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                             1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSupportedOptimizationType          1.4.0      Az.Cdn
Cmdlet          Get-AzCdnSubscriptionResourceUsage                 1.4.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryPolicy                            1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRule                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleAction                        1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleCondition                     1.4.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          New-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                       1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                               1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                                1.4.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                                1.4.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                                 1.4.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                             1.4.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                     1.4.0      Az.Cdn
```

## <a name="getting-help"></a>Få hjälp
Du kan få hjälp med någon av `Get-Help` dessa cmdlets med hjälp av cmdlet.  `Get-Help`ger användning och syntax och visar eventuellt exempel.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Lista befintliga Azure CDN-profiler
Cmdlet `Get-AzCdnProfile` utan några parametrar hämtar alla dina befintliga CDN-profiler.

```powershell
Get-AzCdnProfile
```

Denna utgång kan ledas till cmdlets för uppräkning.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Du kan också returnera en enskild profil genom att ange profilnamn och resursgrupp.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Det är möjligt att ha flera CDN-profiler med samma namn, så länge de finns i olika resursgrupper.  Om du `ResourceGroupName` utelämnar parametern returneras alla profiler med ett matchande namn.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Lista befintliga CDN-slutpunkter
`Get-AzCdnEndpoint`kan hämta en enskild slutpunkt eller alla slutpunkter i en profil.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Skapa CDN-profiler och slutpunkter
`New-AzCdnProfile`och `New-AzCdnEndpoint` används för att skapa CDN-profiler och slutpunkter. Följande SKU:er stöds:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Kontrollera tillgänglighet för slutpunktsnamn
`Get-AzCdnEndpointNameAvailability`returnerar ett objekt som anger om ett slutpunktsnamn är tillgängligt.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Lägga till en anpassad domän
`New-AzCdnCustomDomain`lägger till ett anpassat domännamn i en befintlig slutpunkt.

> [!IMPORTANT]
> Du måste konfigurera CNAME med DNS-leverantören enligt beskrivningen i [Cdn-slutpunkten (Custom Domain to Content Delivery Network).](cdn-map-content-to-custom-domain.md)  Du kan testa mappningen innan du `Test-AzCdnCustomDomain`ändrar slutpunkten med .
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Ändra en slutpunkt
`Set-AzCdnEndpoint`ändrar en befintlig slutpunkt.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Rensning/förinläsning av CDN-tillgångar
`Unpublish-AzCdnEndpointContent`rensar cachelagrade `Publish-AzCdnEndpointContent` tillgångar, medan resurser förinläsningar försänds in på slutpunkter som stöds.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Starta/stoppa CDN-slutpunkter
`Start-AzCdnEndpoint`och `Stop-AzCdnEndpoint` kan användas för att starta och stoppa enskilda slutpunkter eller grupper av slutpunkter.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="creating-standard-rules-engine-policy-and-applying-to-an-existing-cdn-endpoint"></a>Skapa motorprincip för standardregler och tillämpa på en befintlig CDN-slutpunkt
`New-AzCdnDeliveryRule`, `New=AzCdnDeliveryRuleCondition`och `New-AzCdnDeliveryRuleAction` kan användas för att konfigurera Azure CDN Standard Rules-motorn på Azure CDN från Microsoft-profiler. 

```powershell
# Create a new http to https redirect rule
$Condition=New-AzCdnDeliveryRuleCondition -MatchVariable RequestProtocol -Operator Equal -MatchValue HTTP
$Action=New-AzCdnDeliveryRuleAction -RedirectType Found -DestinationProtocol HTTPS
$HttpToHttpsRedirectRule=New-AzCdnDeliveryRule -Name "HttpToHttpsRedirectRule" -Order 2 -Condition $Condition -Action $Action

# Create a path based Response header modification rule. 
$Cond1=New-AzCdnDeliveryRuleCondition -MatchVariable UrlPath -Operator BeginsWith -MatchValue "/images/"
$Action1=New-AzCdnDeliveryRuleAction -HeaderActionType ModifyResponseHeader -Action Overwrite -HeaderName "Access-Control-Allow-Origin" -Value "*"
$PathBasedCacheOverrideRule=New-AzCdnDeliveryRule -Name "PathBasedCacheOverride" -Order 1 -Condition $Cond1 -Action $action1

# Create a delivery policy with above deliveryRules.
$Policy = New-AzCdnDeliveryPolicy -Description "DeliveryPolicy" -Rule $HttpToHttpsRedirectRule,$UrlRewriteRule

# Update existing endpoint with created delivery policy
$ep = Get-AzCdnEndpoint -EndpointName cdndocdemo -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
$ep.DeliveryPolicy = $Policy
Set-AzCdnEndpoint -CdnEndpoint $ep
```

## <a name="deleting-cdn-resources"></a>Ta bort CDN-resurser
`Remove-AzCdnProfile`och `Remove-AzCdnEndpoint` kan användas för att ta bort profiler och slutpunkter.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Efterföljande moment
Läs mer om hur man automatiserar Azure CDN med [.NET](cdn-app-dev-net.md) eller [Node.js](cdn-app-dev-node.md).

Mer information om CDN-funktioner finns i [CDN Översikt](cdn-overview.md).

