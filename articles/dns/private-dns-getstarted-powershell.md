---
title: "Komma igång med privata Azure DNS-zoner med PowerShell | Microsoft Docs"
description: "Läs om hur du skapar en privat DNS-zon och en DNS-post i Azure DNS. Det här är en steg-för-steg-guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med PowerShell."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 4613e152336eda7ce7cdc4c44b0c6b5e96abac10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Komma igång med privata Azure DNS-zoner med PowerShell

Den här artikeln visar hur du skapar din första privata DNS-zon och DNS-post med Azure PowerShell.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Vi kallar dessa ”lösningsnätverk”.  Du kan även ange en uppsättning virtuella nätverk där Azure DNS ska bibehålla värddatorposter varje gång en virtuell dator skapas, ändrar IP-adress eller förstörs.  Vi kallar dessa ”registreringsnätverk”.

Eftersom den här funktionen för närvarande är en hanterad förhandsversion tillhandahålls en förhandsversion av PowerShell-modulen när du skickar e-post till [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com).

## <a name="get-the-preview-powershell-module"></a>Hämta förhandsversionen av PowerShell-modulen

För att skapa en privat DNS-zon med PowerShell skickar du e-post till [AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com) för att hämta förhandsversionen av PowerShell-modulen.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som ska innehålla DNS-zonen innan du skapar DNS-zonen. Nedan visas kommandot.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzureRmDnsZone`. I följande exempel skapas en DNS-zon med namnet *contoso.local* i resursgruppen som heter *MyResourceGroup* och det gör DNS-zonen tillgänglig för det virtuella nätverket som heter *MyAzureVnet*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

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

## <a name="delete-all-resources"></a>Ta bort alla resurser

Så här tar du bort alla resurser som skapats i den här artikeln:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner).

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar i Azure DNS med PowerShell](dns-operations-recordsets.md).

