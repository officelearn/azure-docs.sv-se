---
title: 'Snabb start: skapa en Azure DNS zon och post-Azure PowerShell'
titleSuffix: Azure DNS
description: Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en stegvis snabbstart om hur du skapar och hanterar din första DNS-zon och DNS-post med Azure PowerShell.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: e33f6fdba1a15032d76b94804d610e292f663d59
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937161"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Snabbstart: Skapa en Azure DNS-zon och post med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här snabbstarten skapar du din första DNS-zon och post med Azure PowerShell. Du kan även utföra de här stegen med [Azure-portalen](dns-getstarted-portal.md) eller [Azure CLI](dns-getstarted-cli.md). 

En DNS-zon används som värd för DNS-poster för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

Azure DNS stöder även skapandet av privata domäner. Stegvisa instruktioner för hur du skapar din första privata DNS-zon och post finns i [Komma igång med privata Azure DNS-zoner med PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Innan du skapar DNS-zonen skapar du en resursgrupp som ska innehålla DNS-zonen:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av cmdleten `New-AzDnsZone`. I följande exempel skapas en DNS-zon med namnet *contoso. xyz* i resurs gruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Du skapar postuppsättningar med hjälp av cmdleten `New-AzDnsRecordSet`. I följande exempel skapas en post med det relativa namnet "www" i DNS-zonen "contoso. xyz" i resurs gruppen "MyResourceGroup". Det fullständigt kvalificerade namnet på post uppsättningen är "www. contoso. xyz". Post typen är "A", med IP-adressen "10.10.10.10" och TTL är 3600 sekunder.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testa namnmatchningen

Nu när du har en DNS-testzon med en ”A”-testpost kan du testa namnmatchningen med ett verktyg som heter *nslookup*. 

**Så här testar du DNS-namnmatchning:**

1. Kör följande cmdlet för att hämta listan över namnservrar för zonen:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Kopiera ett av namn server namnen från utdata från föregående steg.

1. Öppna en kommandotolk och kör följande kommando:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Ett exempel:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Du bör se något som liknar följande skärm:

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Värd namnet **www\.contoso. xyz** matchas till **10.10.10.10**, precis som du konfigurerade det. Resultatet verifierar att namnmatchningen fungerar korrekt.

## <a name="delete-all-resources"></a>Ta bort alla resurser

Du kan ta bort alla resurser som skapats i den här snabbstarten när de inte behövs längre genom att ta bort resursgruppen:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat din första DNS-zon och DNS-post med hjälp av Azure PowerShell kan du skapa poster för en webbapp i en anpassad domän.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)

