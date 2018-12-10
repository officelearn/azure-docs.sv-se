---
title: Snabbstart – skapa en Azure DNS-zon och post med Azure PowerShell
description: Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en stegvis snabbstart om hur du skapar och hanterar din första DNS-zon och DNS-post med Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: db6ac089f11477ecdb1568195855fae00d3eac61
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888366"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Snabbstart: Skapa en Azure DNS-zon och post med Azure PowerShell

I den här snabbstarten skapar du din första DNS-zon och post med Azure PowerShell. Du kan även utföra de här stegen med [Azure-portalen](dns-getstarted-portal.md) eller [Azure CLI](dns-getstarted-cli.md). 

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

Azure DNS stöder även skapandet av privata domäner. Stegvisa instruktioner för hur du skapar din första privata DNS-zon och post finns i [Komma igång med privata Azure DNS-zoner med PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan du skapar DNS-zonen skapar du en resursgrupp som ska innehålla DNS-zonen:

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "eastus"
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

## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>Uppdatera namnservrar

När du är nöjd med konfigurationen av DNS-zonen och DNS-posterna måste du konfigurera ditt domännamn för användning med Azure DNS-namnservrarna. Det gör att andra användare på Internet kan hitta dina DNS-poster.

Namnservrarna för din zon anges av cmdleten `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Dessa namnservrar ska konfigureras med domännamnsregistratorn (där du köpte domännamnet). Registratorn erbjuder möjligheten att konfigurera namnservrar för domänen. Mer information finns i [Självstudie: Använda Azure DNS som värd för din domän](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="delete-all-resources"></a>Ta bort alla resurser

Du kan ta bort alla resurser som skapats i den här snabbstarten när de inte behövs längre genom att ta bort resursgruppen:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat din första DNS-zon och DNS-post med hjälp av Azure PowerShell kan du skapa poster för en webbapp i en anpassad domän.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)

