---
title: "Värd för zoner för omvänd DNS-sökning i Azure DNS | Microsoft Docs"
description: "Lär dig hur du använder Azure DNS som värd för omvänd DNS-sökningszoner för IP-adressintervall"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Värd för omvänd sökning DNS-zoner i Azure DNS

Den här artikeln beskriver hur du värd zoner omvänd DNS-sökning för din tilldelade IP-adressintervall i Azure DNS. IP-adressintervall som representeras av zon för omvänd sökning måste tilldelas för din organisation, vanligtvis av din Internetleverantör.

Om du vill konfigurera omvänd DNS för Azure-ägs IP-adressen till din Azure-tjänst finns [konfigurera omvänd sökning för IP-adresser som allokerats till Azure-tjänstens](dns-reverse-dns-for-azure-services.md).

Innan du läser den här artikeln bör du vara bekant med den här [översikt över omvänd DNS- och support i Azure](dns-reverse-dns-overview.md).

Den här artikeln vägleder dig genom stegen för att skapa din första omvänd sökning DNS-zon och registrera med Azure-portalen, Azure PowerShell, Azure CLI 1.0 eller 2.0 för Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Skapa en DNS-zon för omvänd sökning

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. På navmenyn klickar du på och klicka på **ny** > **nätverk** > och klicka sedan på **DNS-zonen** att öppna den **skapa DNS-zonen** bladet.

   ![DNS-zon](./media/dns-reverse-dns-hosting/figure1.png)

1. På den **skapa DNS-zonen** bladet namnge din DNS-zon. Namnet på zonen är utformade på olika sätt för IPv4 och IPv6-prefix. Följ instruktionerna för [IPV4](#ipv4) eller [IPv6](#ipv6) att namnge din tidszon. När du är klar klickar du på **skapa** att skapa zonen.

### <a name="ipv4"></a>IPv4

Namnet på en IPv4 zon för omvänd sökning baseras på IP-adressintervall som representerar. Det bör vara i följande format: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Exempel finns i [översikt över omvänd DNS- och support i Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> När du skapar classless omvänd DNS-zoner för sökning i Azure DNS, måste du använda ett bindestreck (`-`) i stället för ett snedstreck ('/ ') i zonnamnet.
>
> Till exempel för IP-intervallet 192.0.2.128/26, du måste använda `128-26.2.0.192.in-addr.arpa` som zonnamnet i stället för `128/26.2.0.192.in-addr.arpa`.
>
> Detta beror på att när båda stöds av DNS-standarden DNS zonen namn som innehåller snedstreck (`/`) tecken stöds inte i Azure DNS.

I följande exempel visas hur du skapar en klass C omvänd DNS-zon som heter `2.0.192.in-addr.arpa` i Azure DNS via Azure portal:

 ![Skapa DNS-zon](./media/dns-reverse-dns-hosting/figure2.png)

'Resursgruppens plats' definierar plats för resursgruppen och har ingen inverkan på DNS-zonen. Plats för DNS-zonen är alltid ”globala' och visas inte.

Följande exempel visar hur du utför den här uppgiften med Azure PowerShell och Azure CLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Namnet på en zon för omvänd sökning IPv6 bör vara i följande format: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Exempel finns i [översikt över omvänd DNS- och support i Azure](dns-reverse-dns-overview.md#ipv6).


I följande exempel visas hur du skapar en IPv6 omvänd DNS-sökningszon med namnet `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` i Azure DNS via Azure portal:

 ![Skapa DNS-zon](./media/dns-reverse-dns-hosting/figure3.png)

'Resursgruppens plats' definierar plats för resursgruppen och har ingen inverkan på DNS-zonen. Plats för DNS-zonen är alltid ”globala' och visas inte.

Följande exempel visar hur du utför den här uppgiften med Azure PowerShell och Azure CLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegera en zon för omvänd DNS-sökning

Att ha skapat en zon för omvänd DNS-sökning, måste du kontrollera att zonen delegeras från den överordnade zonen. DNS-delegering kan DNS-namnmatchningen att hitta namnservrar som värd för en zon för omvänd DNS-sökning. Detta gör att servrarna namn att besvara omvänd DNS-frågor för IP-adresser i ditt-adressintervall.

För zoner för vanlig sökning beskrivs processen för att delegera en DNS-zon i [Delegera din domän till Azure DNS](dns-delegate-domain-azure-dns.md). Delegering av zoner för omvänd sökning fungerar på samma sätt. Den enda skillnaden är att du måste konfigurera namnservrarna med Internetleverantören som tillhandahåller IP-adressintervall i stället för domännamnsregistratorn.

## <a name="create-a-dns-ptr-record"></a>Skapa en DNS PTR-post

### <a name="ipv4"></a>IPv4

I följande exempel vägleder dig genom processen att skapa en PTR-post i en omvänd DNS-zon i Azure DNS. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1.  Välj **+ Postuppsättning** längst upp på bladet **DNS-zon** för att öppna bladet **Lägg till uppsättning av poster**.

 ![DNS-zon](./media/dns-reverse-dns-hosting/figure4.png)

1. På den **lägga till postuppsättning** bladet. 
1. Välj **PTR** från posten ”**typen**” menyn.  
1. Namnet på posten för en PTR-post måste vara resten av IPv4-adressen i omvänd ordning. I det här exemplet fylls de tre första oktetterna redan som en del av zonnamnet (.2.0.192). Därför anges bara den sista oktetten i namnfältet. Till exempel namnger du din postuppsättning ”**15**” för en resurs vars IP-adressen är 192.0.2.15.  
1. I den ”**domännamn**”, ange det fullständigt kvalificerade domännamnet (FQDN) på resursen med hjälp av IP-Adressen.
1. Klicka på **OK** längst ned på bladet för att skapa DNS-posten.

 ![Lägg till en postuppsättning](./media/dns-reverse-dns-hosting/figure5.png)

Här följer några exempel på hur du utför den här uppgiften med PowerShell och AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>AzureCLI 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

I följande exempel vägleder dig genom processen att skapa ny 'PTR-post. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Längst upp i den **DNS-zonen bladet**väljer **+ postuppsättningen** att öppna den **lägga till postuppsättning** bladet.

  ![DNS-zonen bladet](./media/dns-reverse-dns-hosting/figure6.png)

2. På den **lägga till postuppsättning** bladet. 
3. Välj **PTR** från posten ”**typen**” menyn.  
4. Namnet på posten för en PTR-post måste vara resten av IPv6-adressen i omvänd ordning. Det får inte innehålla noll komprimering. I det här exemplet fylls redan första 64 bitarna i IPv6 som en del av zonnamnet (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Därför tillhandahålls bara de senaste 64-bitarna i namnfältet. Senaste 64-bitars av IP-adressen anges i omvänd ordning med en period som avgränsare mellan varje hexadecimalt tal. Till exempel namnger du din postuppsättning ”**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**” för en resurs vars IP-adressen är 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
5. I den ”**domännamn**”, ange det fullständigt kvalificerade domännamnet (FQDN) på resursen med hjälp av IP-Adressen.
6. Klicka på **OK** längst ned på bladet för att skapa DNS-posten.

![postuppsättningen bladet Lägg till](./media/dns-reverse-dns-hosting/figure7.png)

Här följer några exempel på hur du utför den här uppgiften med PowerShell och AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>AzureCLI 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>AzureCLI 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Visa poster

Gå till din DNS-zonen i Azure portal om du vill visa de poster som du skapade. I den nedre delen av den **DNS-zonen** bladet hittar du på posterna för DNS-zonen. Du bör se NS- och SOA-standardposterna (dessa skapas i varje zon) plus eventuella nya poster som du har skapat.

### <a name="ipv4"></a>IPv4

DNS-zonen bladet visar IPv4 PTR-poster:

![DNS-zonen bladet](./media/dns-reverse-dns-hosting/figure8.png)

Följande exempel visar hur du visar PTR-poster med PowerShell eller Azure CLI:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

DNS-zonen bladet visar IPv6 PTR-poster:

![DNS-zonen bladet](./media/dns-reverse-dns-hosting/figure9.png)

Här följer några exempel på hur du visar posterna med PowerShell och AzureCLI:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>Azure CLI 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>Azure CLI 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan jag vara värd zoner för omvänd DNS-sökning för min ISP-tilldelad IP-Adressblock på Azure DNS?

Ja. Värd för zoner för omvänd sökning (ARPA) för din egen IP-adressintervall i Azure DNS stöds fullt ut.

Skapa en zon för omvänd sökning i DNS-Azure som beskrivs i den här artikeln och sedan arbeta med Leverantören [delegera zonen](dns-domain-delegation.md).  Du kan sedan hantera PTR-poster för varje omvänd sökning på samma sätt som andra typer av poster.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Hur mycket har värd min omvänd DNS-sökning zonen kostnaden?

Värd för DNS-zonen för omvänd sökning för din ISP-tilldelad IP-Adressblock i Azure DNS debiteras med [Azure DNS standardpriser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan jag vara värd för omvänd DNS-sökningszoner för både IPv4 och IPv6-adresser i Azure DNS?

Ja. Den här artikeln beskriver hur du skapar både IPv4 och IPv6 omvänd DNS-sökning i Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kan jag importera en befintlig omvänd DNS-sökningszon?

Ja. Du kan använda Azure CLI för att importera befintliga DNS-zoner i Azure DNS. Detta fungerar för både zoner för vanlig sökning och zoner för omvänd sökning.

Mer information finns i [importera och exportera en DNS-zonfilen med hjälp av Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om omvänd DNS [omvänd DNS-sökning på Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [hantera omvänd DNS-posterna för din Azure-tjänster](dns-reverse-dns-for-azure-services.md).
