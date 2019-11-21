---
title: Manage DNS zones in Azure DNS - PowerShell | Microsoft Docs
description: You can manage DNS zones using Azure Powershell. This article describes how to update, delete and create DNS zones on Azure DNS
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: allensu
ms.openlocfilehash: 7a838f10d44f3be0af79bba5d303467884159cbf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211702"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>How to manage DNS Zones using PowerShell

> [!div class="op_single_selector"]
> * [Portalen](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassisk Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

This article shows you how to manage your DNS zones by using Azure PowerShell. You can also manage your DNS zones using the cross-platform [Azure CLI](dns-operations-dnszones-cli.md) or the Azure portal.

This guide specifically deals with Public DNS zones. For information on using Azure PowerShell to manage Private Zones in Azure DNS, see [Get started with Azure DNS Private Zones using Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzureRmDnsZone`.

The following example creates a DNS zone called *contoso.com* in the resource group called *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

The following example shows how to create a DNS zone with two [Azure Resource Manager tags](dns-zones-records.md#tags), *project = demo* and *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS also supports private DNS zones.  Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner). Ett exempel på hur man kan skapa en privat DNS-zon finns i [Kom igång med privata Azure DNS-zoner med PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Get a DNS zone

To retrieve a DNS zone, use the `Get-AzureRmDnsZone` cmdlet. This operation returns a DNS zone object corresponding to an existing zone in Azure DNS. The object contains data about the zone (such as the number of record sets), but does not contain the record sets themselves (see `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Lista DNS-zoner

Genom att utelämna zonnamnet från `Get-AzureRmDnsZone` kan du räkna upp alla zoner i en resursgrupp. Den här åtgärden returnerar en matris med zonobjekt.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Genom att utelämna både zonnamnet och resursgruppsnamnet från `Get-AzureRmDnsZone` kan du räkna upp alla zoner i Azure-prenumerationen.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Uppdatera en DNS-zon

Du kan göra ändringar i en DNS-zonresurs med `Set-AzureRmDnsZone`. Den här cmdleten uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets.md)). Den används endast för att uppdatera zonresursens egenskaper. The writable zone properties are currently limited to the [Azure Resource Manager ‘tags’ for the zone resource](dns-zones-records.md#tags).

Use one of the following two ways to update a DNS zone:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Specify the zone using the zone name and resource group

This approach replaces the existing zone tags with the values specified.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Ange zonen med ett $zone-objekt

This approach retrieves the existing zone object, modifies the tags, and then commits the changes. In this way, existing tags can be preserved.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

When using `Set-AzureRmDnsZone` with a $zone object, [Etag checks](dns-zones-records.md#etags) are used to ensure concurrent changes are not overwritten. You can use the optional `-Overwrite` switch to suppress these checks.

## <a name="delete-a-dns-zone"></a>Delete a DNS Zone

DNS zones can be deleted using the `Remove-AzureRmDnsZone` cmdlet.

> [!NOTE]
> Om du tar bort en DNS-zon så tar du även bort DNS-posterna i zonen. Du kan inte ångra den här åtgärden. Om DNS-zonen används, så misslyckas de tjänster som använder zonen när zonen tas bort.
>
>Hur du gör för att förebygga oavsiktlig zonborttagning beskrivs i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).


Använd något av följande två sätt när du ska ta bort en DNS-zon:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Ange zonen med zonnamnet och resursgruppens namn

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Ange zonen med ett $zone-objekt

Du kan ange zonen som ska tas bort med hjälp av ett `$zone`-objekt som returnerats av `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Zonobjektet kan även pipas i stället för att skickas som en parameter:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

As with `Set-AzureRmDnsZone`, specifying the zone using a `$zone` object enables Etag checks to ensure concurrent changes are not deleted. Use the `-Overwrite` switch to suppress these checks.

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` och `Remove-AzureRmDnsZone` stöder alla bekräftelsemeddelanden.

Både `New-AzureRmDnsZone` och `Set-AzureRmDnsZone` frågar efter bekräftelse om `$ConfirmPreference` PowerShell-inställningsvariabeln har värdet `Medium` eller lägre. På grund av den potentiellt höga effekten av att ta bort en DNS-zon så uppmanar `Remove-AzureRmDnsZone`-cmdleten dig att bekräfta att `$ConfirmPreference` PowerShell-variabeln har något värde annat än `None`.

Eftersom standardvärdet för `$ConfirmPreference` är `High`, så frågar endast `Remove-AzureRmDnsZone` om bekräftelse som standard.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse.

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Nästa steg

Learn how to [manage record sets and records](dns-operations-recordsets.md) in your DNS zone.
<br>
Learn how to [delegate your domain to Azure DNS](dns-domain-delegation.md).
<br>
Review the [Azure DNS PowerShell reference documentation](/powershell/module/azurerm.dns).

