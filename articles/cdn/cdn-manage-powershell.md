---
title: Hantera Azure CDN med PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell-cmdlets för att hantera Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5634ecdec04f023d9eb901c4ad0fb21b13bcfdc1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31592444"
---
# <a name="manage-azure-cdn-with-powershell"></a>Hantera Azure CDN med PowerShell
PowerShell innehåller något av de mest flexibla metoderna för att hantera dina Azure CDN-profiler och slutpunkter.  Du kan använda PowerShell interaktivt eller genom att skriva skript för att automatisera hanteringsuppgifter.  Den här självstudiekursen visas flera av de vanligaste uppgifterna som du kan utföra med PowerShell för att hantera dina Azure CDN-profiler och slutpunkter.

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda PowerShell för att hantera dina Azure CDN-profiler och slutpunkter, måste du ha installerat Azure PowerShell-modulen.  Mer information om hur du installerar Azure PowerShell och ansluta till Azure med hjälp av `Connect-AzureRmAccount` cmdlet, finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Du måste logga in med `Connect-AzureRmAccount` innan du kan köra Azure PowerShell-cmdlets.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Visar en lista över Azure CDN-cmdlets
Lista alla Azure CDN cmdlet: ar med den `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Få hjälp
Du kan få hjälp med dessa cmdlets med hjälp av den `Get-Help` cmdlet.  `Get-Help` ger användning och syntax och du kan också visas exempel.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Lista över befintliga Azure CDN-profiler
Den `Get-AzureRmCdnProfile` cmdlet utan några parametrar hämtar alla dina befintliga CDN-profiler.

```powershell
Get-AzureRmCdnProfile
```

Den här utdatan kan skickas till cmdletar för uppräkningen.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Du kan också returnera en enskild profil genom att ange namn och resursen profilgruppen.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Det är möjligt att ha flera CDN-profiler med samma namn, så länge de är i olika resursgrupper.  Om du utesluter den `ResourceGroupName` parametern returnerar alla profiler med ett matchande namn.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Lista över befintliga CDN-slutpunkter
`Get-AzureRmCdnEndpoint` Hämta en enskild slutpunkt eller alla slutpunkter för en profil.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Skapa CDN-profiler och slutpunkter
`New-AzureRmCdnProfile` och `New-AzureRmCdnEndpoint` används för att skapa CDN-profiler och slutpunkter.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Kontrollera tillgänglighet för slutpunkt-namn
`Get-AzureRmCdnEndpointNameAvailability` Returnerar ett objekt som anger om det finns en namnet på slutpunkten.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Lägga till en anpassad domän
`New-AzureRmCdnCustomDomain` lägger till ett anpassat domännamn i en befintlig slutpunkt.

> [!IMPORTANT]
> Du måste ställa in CNAME hos din DNS-leverantör som beskrivs i [hur du mappar en anpassad domän till innehåll innehållsleveransnätverk (CDN) slutpunkt](cdn-map-content-to-custom-domain.md).  Du kan testa mappningen innan du ändrar slutpunkten med `Test-AzureRmCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Ändra en slutpunkt
`Set-AzureRmCdnEndpoint` ändrar en befintlig slutpunkt.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Rensa/Förproduktion-loading CDN tillgångar
`Unpublish-AzureRmCdnEndpointContent` återställningspunkter cachelagras tillgångar, medan `Publish-AzureRmCdnEndpointContent` före läser in tillgångar på slutpunkter som stöds.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Starta/Stoppa CDN-slutpunkter
`Start-AzureRmCdnEndpoint` och `Stop-AzureRmCdnEndpoint` kan användas för att starta och stoppa enskilda slutpunkter eller grupper av slutpunkter.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Ta bort CDN-resurser
`Remove-AzureRmCdnProfile` och `Remove-AzureRmCdnEndpoint` kan användas för att ta bort profiler och slutpunkter.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Nästa steg
Läs mer om hur man automatiserar Azure CDN med [.NET](cdn-app-dev-net.md) eller [Node.js](cdn-app-dev-node.md).

Läs om CDN funktioner i [CDN-översikt](cdn-overview.md).

