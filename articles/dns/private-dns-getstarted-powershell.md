---
title: Komma igång med privata Azure DNS-zoner med PowerShell | Microsoft Docs
description: Läs om hur du skapar en privat DNS-zon och en DNS-post i Azure DNS. Det här är en steg-för-steg-guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Komma igång med privata Azure DNS-zoner med PowerShell

Den här artikeln visar hur du skapar din första privata DNS-zon och DNS-post med Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Vi kallar dessa ”virtuella lösningsnätverk”.  Du kan även ange ett virtuellt nätverk där Azure DNS ska bibehålla värddatorposter varje gång en virtuell dator skapas, ändrar IP-adress eller förstörs.  Vi kallar detta ett ”virtuellt registreringsnätverk”.

# <a name="get-the-preview-powershell-modules"></a>Hämta förhandsversionen av PowerShell-modulen
Dessa instruktioner förutsätter att du redan har installerat och loggat in på Azure PowerShell, och även försäkrat dig om att du har de moduler som krävs för privatzonsfunktionen. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som ska innehålla DNS-zonen innan du skapar DNS-zonen. Kommandot visas i följande exempel.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Skapa en privat DNS-zon

Du skapar en DNS-zon med hjälp av `New-AzureRmDnsZone` -cmdleten tillsammans med värdet ”Private” för parametern ZoneType. I följande exempel skapas en DNS-zon med namnet *contoso.local* i resursgruppen som heter *MyResourceGroup* och det gör DNS-zonen tillgänglig för det virtuella nätverket som heter *MyAzureVnet*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

Observera att om ZoneType-parametern utelämnas skapas zonen som en offentlig zon. Med andra ord krävs den om du vill skapa en privat zon. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Om du vill att Azure ska skapa värdnamnsposter i zonen automatiskt använder du parametern *RegistrationVirtualNetworkId* i stället för *ResolutionVirtualNetworkId*.  Registrering av virtuella nätverk är automatiskt aktiverad för lösning.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Du skapar postuppsättningar med hjälp av cmdleten `New-AzureRmDnsRecordSet`. I följande exempel skapas en post med det relativa namnet "db" i resursgruppen "MyResourceGroup" i DNS-zonen "contoso.local". Postuppsättningens fullständigt kvalificerade namn är ”db.contoso.local”. Posttypen är ”A” , IP-adressen är 10.0.0.4 och TTL är 3600 sekunder.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Information om andra posttyper, postuppsättningar med fler än en post och ändring av befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure PowerShell](dns-operations-recordsets.md). 

## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Lista privata DNS-zoner

Genom att utelämna zonnamnet från `Get-AzureRmDnsZone` kan du räkna upp alla zoner i en resursgrupp. Den här åtgärden returnerar en matris med zonobjekt.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Genom att utelämna både zonnamnet och resursgruppsnamnet från `Get-AzureRmDnsZone` kan du räkna upp alla zoner i Azure-prenumerationen.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Uppdatera en privat DNS-zon

Du kan göra ändringar i en DNS-zonresurs med `Set-AzureRmDnsZone`. Den här cmdleten uppdaterar inte någon av DNS-postuppsättningarna i zonen (mer information finns i [Hantera DNS-poster](dns-operations-recordsets.md)). Den används endast för att uppdatera zonresursens egenskaper. De skrivbara zonegenskaperna är för tillfället begränsade till [Azure Resource Manager-taggar för zonresursen](dns-zones-records.md#tags) liksom parametrarna RegistrationVirtualNetworkId och ResolutionVirtualNetworkId för privata zoner.

Exemplet nedan ersätter det virtuella nätverk för registrering som är länkat till en zon med ett nytt MyNewAzureVnet.

Observera att du inte behöver ange parametern ZoneType vid uppdatering, till skillnad från när du skapar nytt. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

Exemplet nedan ersätter det virtuella lösningsnätverk som är länkat till en zon med ett nytt, som heter MyNewAzureVnet.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Ta bort en privat DNS-zon

Du kan ta bort privata DNS-zoner med `Remove-AzureRmDnsZone`-cmdleten, precis som med offentliga zoner.

> [!NOTE]
> Om du tar bort en DNS-zon så tar du även bort DNS-posterna i zonen. Du kan inte ångra den här åtgärden. Om DNS-zonen används, så misslyckas de tjänster som använder zonen när zonen tas bort.
>
>Hur du gör för att förebygga oavsiktlig zonborttagning beskrivs i [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

Använd något av följande två sätt när du ska ta bort en DNS-zon:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Ange zonen med zonnamnet och resursgruppens namn

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Ange zonen med ett $zone-objekt

Du kan ange zonen som ska tas bort med hjälp av ett `$zone`-objekt som returnerats av `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Zonobjektet kan även pipas i stället för att skickas som en parameter:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Bekräftelsemeddelanden

Cmdletarna `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` och `Remove-AzureRmDnsZone` stöder alla bekräftelsemeddelanden.

Både `New-AzureRmDnsZone` och `Set-AzureRmDnsZone` frågar efter bekräftelse om `$ConfirmPreference` PowerShell-inställningsvariabeln har värdet `Medium` eller lägre. På grund av den potentiellt höga effekten av att ta bort en DNS-zon så uppmanar `Remove-AzureRmDnsZone`-cmdleten dig att bekräfta att `$ConfirmPreference` PowerShell-variabeln har något värde annat än `None`.

Eftersom standardvärdet för `$ConfirmPreference` är `High`, så frågar endast `Remove-AzureRmDnsZone` om bekräftelse som standard.

Du kan åsidosätta den aktuella `$ConfirmPreference`-inställningen med hjälp av parametern `-Confirm`. Om du anger `-Confirm` eller `-Confirm:$True`, så uppmanar cmdleten dig att bekräfta detta innan den körs. Om du anger `-Confirm:$False`, som ber cmdleten dig inte om bekräftelse.

Mer information om `-Confirm` och `$ConfirmPreference` finns i [Om inställningsvariabler](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).


## <a name="delete-all-resources"></a>Ta bort alla resurser

Så här tar du bort alla resurser som skapats i den här artikeln:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner).

Läsa mer om några vanliga scenarier [privatzonsscenarier](./private-dns-scenarios.md) som kan genomföras med privata zoner i Azure DNS.

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar i Azure DNS med PowerShell](dns-operations-recordsets.md).

