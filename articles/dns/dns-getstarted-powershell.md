---
title: "Komma igång med Azure DNS med PowerShell | Microsoft Docs"
description: "Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första DNS-zon och DNS-post med PowerShell."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: 48f7ba325f61b4a91c0208b4c99058da801bee19
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-dns-using-powershell"></a>Komma igång med Azure DNS med PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Den här artikeln visar hur du skapar din första DNS-zon och DNS-post med Azure PowerShell. Du kan också utföra dessa steg med Azure Portal eller plattformsoberoende Azure CLI.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

Anvisningarna förutsätter att du redan har installerat och loggat in på Azure PowerShell. Mer information finns i [Hantera DNS-zoner med PowerShell](dns-operations-dnszones.md).

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som ska innehålla DNS-zonen innan du skapar DNS-zonen. Nedan visas kommandot.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzureRmDnsZone`. Exemplet nedan skapar en DNS-zon som heter *contoso.com* i resursgruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Du skapar postuppsättningar med hjälp av cmdleten `New-AzureRmDnsRecordSet`. I följande exempel skapas en post med det relativa namnet "www" i resursgruppen "MyResourceGroup" i DNS-zonen "contoso.com". Postuppsättningens fullständigt kvalificerade namn är ”www.contoso.com”. Posttypen är ”A” , IP-adressen är 1.2.3.4 och TTL är 3 600 sekunder.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Information om andra posttyper, postuppsättningar med fler än en post och ändring av befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Uppdatera namnservrar

När du är nöjd med konfigurationen av DNS-zonen och DNS-posterna måste du konfigurera ditt domännamn för användning med Azure DNS-namnservrarna. Det gör att andra användare på Internet kan hitta dina DNS-poster.

Namnservrarna för din zon anges av cmdleten `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -ZoneName contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Dessa namnservrar ska konfigureras med domännamnsregistratorn (där du köpte domännamnet). Registratorn erbjuder möjligheten att konfigurera namnservrar för domänen. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Ta bort alla resurser

Så här tar du bort alla resurser som skapats i den här artikeln:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure DNS i [Översikt över Azure DNS](dns-overview.md).

Mer information om hur du hanterar DNS-zoner i Azure DNS finns i [Hantera DNS-zoner i Azure DNS med PowerShell](dns-operations-dnszones.md).

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar i Azure DNS med PowerShell](dns-operations-recordsets.md).

