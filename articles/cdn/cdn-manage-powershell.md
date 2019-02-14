---
title: Hantera Azure CDN med PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell-cmdlets för att hantera Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 5dc05b08e84decf958481d19b9cfb820947fae78
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237052"
---
# <a name="manage-azure-cdn-with-powershell"></a>Hantera Azure CDN med PowerShell
PowerShell får du en av de mest flexibla metoderna för att hantera dina Azure CDN-profiler och slutpunkter.  Du kan använda PowerShell interaktivt eller genom att skriva skript för att automatisera hanteringsuppgifter.  Den här kursen visar flera av de vanligaste uppgifterna som du kan utföra med PowerShell för att hantera dina Azure CDN-profiler och slutpunkter.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill hantera dina Azure CDN-profiler och slutpunkter med hjälp av PowerShell, måste du ha Azure PowerShell-modulen installerad.  Lär dig hur du installerar Azure PowerShell och Anslut till Azure med hjälp av den `Connect-AzAccount` cmdlet, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Du måste logga in med `Connect-AzAccount` innan du kan köra Azure PowerShell-cmdlets.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Visa en lista över Azure CDN-cmdletar
Du kan lista alla Azure CDN-cmdletar med den `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Få hjälp
Du kan få hjälp med dessa cmdlets med hjälp av den `Get-Help` cmdlet.  `Get-Help` ger användning och syntax och du kan också visas exempel.

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

## <a name="listing-existing-azure-cdn-profiles"></a>Lista över befintliga Azure CDN-profiler
Den `Get-AzCdnProfile` cmdlet: en utan några parametrar hämtar alla dina befintliga CDN-profiler.

```powershell
Get-AzCdnProfile
```

Dessa utdata kan skickas till cmdlets för uppräkningen.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Du kan också returnera en enskild profil genom att ange namn och resursgrupp profilgrupp.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Det är möjligt att ha flera CDN-profiler med samma namn, så länge som de är i olika resursgrupper.  Om du utesluter den `ResourceGroupName` parametern returnerar alla profiler med ett matchande namn.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Lista över befintliga CDN-slutpunkter
`Get-AzCdnEndpoint` Hämta en enskild slutpunkt eller alla slutpunkter i en profil.  

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
`New-AzCdnProfile` och `New-AzCdnEndpoint` används för att skapa CDN-profiler och slutpunkter. Följande SKU: er stöds:
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

## <a name="checking-endpoint-name-availability"></a>Kontroll av namntillgänglighet för slutpunkt
`Get-AzCdnEndpointNameAvailability` Returnerar ett objekt som visar om slutpunktsnamnet är tillgänglig.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Att lägga till en anpassad domän
`New-AzCdnCustomDomain` lägger till ett anpassat domännamn till en befintlig slutpunkt.

> [!IMPORTANT]
> Du måste konfigurera CNAME med din DNS-leverantör som beskrivs i [så här mappar du anpassade domäner till Content Delivery Network (CDN) slutpunkt](cdn-map-content-to-custom-domain.md).  Du kan testa mappningen innan du ändrar din slutpunkt med hjälp av `Test-AzCdnCustomDomain`.
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
`Set-AzCdnEndpoint` ändrar en befintlig slutpunkt.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Rensa/Förproduktions-loading CDN tillgångar
`Unpublish-AzCdnEndpointContent` återställningspunkter cachelagrade tillgångar, medan `Publish-AzCdnEndpointContent` förväg läser in tillgångar för slutpunkter som stöds.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Starta/Stoppa CDN-slutpunkter
`Start-AzCdnEndpoint` och `Stop-AzCdnEndpoint` kan användas för att starta och stoppa enskilda slutpunkter eller grupper av slutpunkter.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Ta bort CDN-resurser
`Remove-AzCdnProfile` och `Remove-AzCdnEndpoint` kan användas för att ta bort profiler och slutpunkter.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Nästa steg
Läs mer om hur man automatiserar Azure CDN med [.NET](cdn-app-dev-net.md) eller [Node.js](cdn-app-dev-node.md).

Läs om CDN-funktioner i [översikt över CDN](cdn-overview.md).

