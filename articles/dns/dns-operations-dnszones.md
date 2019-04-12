---
title: Hantera DNS-zoner i Azure DNS – PowerShell | Microsoft Docs
description: Du kan hantera DNS-zoner med Azure Powershell. Den här artikeln beskriver hur du uppdaterar, ta bort och skapa DNS-zoner i Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: 2d3bb37998a037dcb878a988d4aa01245584ca82
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496428"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Så här hanterar du DNS-zoner med PowerShell

> [!div class="op_single_selector"]
> * [Portalen](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Klassisk Azure CLI](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

Den här artikeln visar hur du hanterar DNS-zoner med hjälp av Azure PowerShell. Du kan också hantera dina DNS-zoner med flera plattformar [Azure CLI](dns-operations-dnszones-cli.md) eller Azure-portalen.

Den här guiden behandlar specifikt offentliga DNS-zoner. Information om hur du använder Azure PowerShell för att hantera privata zoner i Azure DNS finns i [Kom igång med Azure DNS Private Zones med Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzureRmDnsZone`.

I följande exempel skapas en DNS-zon med namnet *contoso.com* i resursgruppen med namnet *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

I följande exempel visas hur du skapar en DNS-zon med två [Azure Resource Manager-taggar](dns-zones-records.md#tags), *project = demo* och *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

Azure DNS stöder nu också privata DNS-zoner (för närvarande i förhandsversion).  Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner). Ett exempel på hur man kan skapa en privat DNS-zon finns i [Kom igång med privata Azure DNS-zoner med PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Hämta en DNS-zon

Använd för att hämta en DNS-zon i `Get-AzureRmDnsZone` cmdlet. Den här åtgärden returnerar ett objekt för DNS-zon som motsvarar en befintlig zon i Azure DNS. Objektet innehåller information om zonen (till exempel antal postuppsättningar), men innehåller inte postuppsättningarna själva (se `Get-AzureRmDnsRecordSet`).

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

Du kan göra ändringar i en DNS-zonresurs med `Set-AzureRmDnsZone`. Den här cmdleten uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets.md)). Den används endast för att uppdatera zonresursens egenskaper. De skrivbara zonegenskaperna är för närvarande begränsade till den [Azure Resource Manager-taggar för zonresursen](dns-zones-records.md#tags).

Använd någon av följande två sätt att uppdatera en DNS-zon:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Ange zonen med namn och resursgrupp zoner

Den här metoden ersätter de befintliga taggarna för zonen med de angivna värdena.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Ange zonen med ett $zone-objekt

Den här metoden hämtar det befintliga objektet i zonen, ändrar taggar och sedan skickar ändringarna. På så sätt kan kan befintliga taggar bevaras.

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

När du använder `Set-AzureRmDnsZone` med ett objekt för $zone [Etag kontrollerar](dns-zones-records.md#etags) används för att se till att samtidiga ändringar inte skrivs över. Du kan använda den valfria `-Overwrite` växel för att ignorera dessa kontroller.

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

DNS-zoner kan tas bort med den `Remove-AzureRmDnsZone` cmdlet.

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

Precis som med `Set-AzureRmDnsZone`, att ange en zon med hjälp av en `$zone` objekt gör det möjligt för Etag-kontroller för att se till att samtidiga ändringar inte tas bort. Använd den `-Overwrite` växel för att ignorera dessa kontroller.

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` och `Remove-AzureRmDnsZone` stöder alla bekräftelsemeddelanden.

Både `New-AzureRmDnsZone` och `Set-AzureRmDnsZone` frågar efter bekräftelse om `$ConfirmPreference` PowerShell-inställningsvariabeln har värdet `Medium` eller lägre. På grund av den potentiellt höga effekten av att ta bort en DNS-zon så uppmanar `Remove-AzureRmDnsZone`-cmdleten dig att bekräfta att `$ConfirmPreference` PowerShell-variabeln har något värde annat än `None`.

Eftersom standardvärdet för `$ConfirmPreference` är `High`, så frågar endast `Remove-AzureRmDnsZone` om bekräftelse som standard.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse.

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](/powershell/module/microsoft.powershell.core/about/about_preference_variables.1).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hantera postuppsättningar och poster](dns-operations-recordsets.md) i din DNS-zon.
<br>
Lär dig hur du [Delegera din domän till Azure DNS](dns-domain-delegation.md).
<br>
Granska den [referensdokumentation för Azure DNS PowerShell](/powershell/module/azurerm.dns).

