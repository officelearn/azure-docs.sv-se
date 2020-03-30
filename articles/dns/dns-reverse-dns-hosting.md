---
title: Värdens omvända DNS-uppslagszoner i Azure DNS
description: Lär dig hur du använder Azure DNS för att vara värd för de omvända DNS-sökzonerna för dina IP-intervall
author: rohinkoul
ms.service: dns
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 78fc3428274be5e1998abe9189bea996f15e278c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454269"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Värdens omvända DNS-uppslagszoner i Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här artikeln beskrivs hur du är värd för de omvända DNS-uppslagszonerna för dina tilldelade IP-intervall i Azure DNS. IP-intervallen som representeras av zoner för omvänd sökning måste tilldelas din organisation, vanligtvis av din Isp.

Information om hur du konfigurerar omvänd DNS för en Azure-ägd IP-adress som har tilldelats din Azure-tjänst finns i [Konfigurera omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).

Innan du läser den här artikeln bör du känna till [översikten över omvänd DNS och support i Azure](dns-reverse-dns-overview.md).

I den här artikeln får du lära dig stegen för att skapa din första omvända sökning DNS-zon och post med hjälp av Azure-portalen, Azure PowerShell, Azure classic CLI eller Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Skapa en omvänd uppslagnings-DNS-zon

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Nytt** > **nätverk**på **Hub-menyn** och välj sedan **DNS-zon**.

   !["DNS-zon" val](./media/dns-reverse-dns-hosting/figure1.png)

1. Namnge **DNS-zonen i fönstret Skapa DNS-zon.** Namnet på zonen skapas på olika sätt för IPv4- och IPv6-prefix. Använd instruktionerna för [IPv4](#ipv4) eller [IPv6](#ipv6) för att namnge din zon. När du är klar väljer du **Skapa** för att skapa zonen.

### <a name="ipv4"></a>IPv4

Namnet på en omvänd uppslagszon i IPv4 baseras på det IP-intervall som den representerar. Det bör vara i `<IPv4 network prefix in reverse order>.in-addr.arpa`följande format: . Exempel finns i [Översikt över omvänd DNS och support i Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> När du skapar klasslösa omvända DNS-uppslagszoner i Azure`-`DNS måste du`/`använda ett bindestreck ( ) i stället för ett snedstreck ( ) i zonnamnet.
>
> För IP-intervallet 192.0.2.128/26 måste du `128-26.2.0.192.in-addr.arpa` till exempel använda `128/26.2.0.192.in-addr.arpa`som zonnamn i stället för .
>
> Även om DNS-standarderna stöder båda metoderna stöder Azure DNS inte`/`DNS-zonnamn som innehåller för framåtskurna ( ) tecken.

I följande exempel visas hur du skapar `2.0.192.in-addr.arpa` en omvänd DNS-zon av klass C som namnges i Azure DNS via Azure-portalen:

 ![Fönstret "Skapa DNS-zon" med rutor ifyllda](./media/dns-reverse-dns-hosting/figure2.png)

**Resursgruppsplatsen** definierar platsen för resursgruppen. Det påverkar inte DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

Följande exempel visar hur du slutför den här uppgiften med hjälp av Azure PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Namnet på en omvänd uppslagszon i IPv6 `<IPv6 network prefix in reverse order>.ip6.arpa`ska vara i följande formulär: .  Exempel finns i [Översikt över omvänd DNS och support i Azure](dns-reverse-dns-overview.md#ipv6).


I följande exempel visas hur du skapar en omvänd `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` DNS-sökningszon i IPv6 med namnet Azure DNS via Azure-portalen:

 ![Fönstret "Skapa DNS-zon" med rutor ifyllda](./media/dns-reverse-dns-hosting/figure3.png)

**Resursgruppsplatsen** definierar platsen för resursgruppen. Det påverkar inte DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

Följande exempel visar hur du slutför den här uppgiften med hjälp av Azure PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegera en omvänd DNS-uppslagszon

Nu när du har skapat den omvända DNS-uppslagszonen måste du se till att zonen delegeras från den överordnade zonen. DNS-delegering gör det möjligt för DNS-namnmatchningsprocessen att hitta de namnservrar som är värdar för den omvända DNS-sökzonen. Dessa namnservrar kan sedan svara på omvända DNS-frågor för IP-adresserna i adressintervallet.

För zoner för sökning framåt beskrivs hur en DNS-zon delegeras [till Azure DNS](dns-delegate-domain-azure-dns.md). Delegering för zoner för omvänd sökning fungerar på samma sätt. Den enda skillnaden är att du behöver konfigurera namnservrarna med den Isp som angav ditt IP-intervall, i stället för din domännamnsregistrare.

## <a name="create-a-dns-ptr-record"></a>Skapa en DNS PTR-post

### <a name="ipv4"></a>IPv4

I följande exempel går du igenom processen att skapa en PTR-post i en omvänd DNS-zon i Azure DNS. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Högst upp i **DNS-zonfönstret** väljer du **+ Postuppsättning** för att öppna fönstret **Lägg till postuppsättning.**

   ![Knapp för att skapa en postuppsättning](./media/dns-reverse-dns-hosting/figure4.png)

1. Namnet på den post som angetts för en PTR-post måste vara resten av IPv4-adressen i omvänd ordning. 

   I det här exemplet fylls de tre första oktetterna redan som en del av zonnamnet (.2.0.192). Därför anges endast den sista oktetten i rutan **Namn.** Du kan till exempel namnge posten **15** för en resurs vars IP-adress är 192.0.2.15.  
1. För **Typ**väljer du **PTR**.  
1. För **DOMÄNNAMN**anger du det fullständigt kvalificerade domännamnet (FQDN) för den resurs som använder IP-adressen.
1. Välj **OK** längst ned i fönstret för att skapa DNS-posten.

   ![Fönstret "Lägg till postuppsättning" med rutor ifyllda](./media/dns-reverse-dns-hosting/figure5.png)

Följande exempel visar hur du slutför den här uppgiften med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

I följande exempel går du igenom processen att skapa ny PTR-post. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Högst upp i **DNS-zonfönstret** väljer du **+ Postuppsättning** för att öppna fönstret **Lägg till postuppsättning.**

   ![Knapp för att skapa en postuppsättning](./media/dns-reverse-dns-hosting/figure6.png)

2. Namnet på den post som angetts för en PTR-post måste vara resten av IPv6-adressen i omvänd ordning. Det får inte innehålla någon nollkomprimering. 

   I det här exemplet fylls de första 64 bitarna av IPv6 redan som en del av zonnamnet (0.0.0.0.c.d.b.a.8.b.d.0.1.0.2.ip6.arpa). Därför anges bara de senaste 64 bitarna i rutan **Namn.** De sista 64 bitarna av IP-adressen anges i omvänd ordning, med en period som avgränsare mellan varje hexadecimalt tal. Du kan till exempel namnge postuppsättningen **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** för en resurs vars IP-adress är 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. För **Typ**väljer du **PTR**.  
4. För **DOMÄNNAMN**anger du FQDN för den resurs som använder IP-adressen.
5. Välj **OK** längst ned i fönstret för att skapa DNS-posten.

![Fönstret "Lägg till postuppsättning" med rutor ifyllda](./media/dns-reverse-dns-hosting/figure7.png)

Följande exempel visar hur du slutför den här uppgiften med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Visa poster

Om du vill visa de poster som du har skapat bläddrar du till din DNS-zon i Azure-portalen. I den nedre delen av **DNS-zonfönstret** kan du se posterna för DNS-zonen. Du bör se standard-NS- och SOA-posterna, plus alla nya poster som du har skapat. NS- och SOA-posterna skapas i varje zon. 

### <a name="ipv4"></a>IPv4

I **fönstret DNS-zon** visas IPv4 PTR-posterna:

![Fönstret "DNS-zon" med IPv4-poster](./media/dns-reverse-dns-hosting/figure8.png)

Följande exempel visar hur du visar PTR-posterna med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

I **fönstret DNS-zon** visas IPv6 PTR-posterna:

![Fönstret "DNS-zon" med IPv6-poster](./media/dns-reverse-dns-hosting/figure9.png)

Följande exempel visar hur du visar posterna med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan jag vara värd för omvända DNS-uppslagszoner för mina IP-ip-block som tilldelats isp på Azure DNS?

Ja. Att vara värd för arpa-zoner (Reverse Lookup) för dina egna IP-intervall i Azure DNS stöds fullt ut.

Skapa zonen för omvänd sökning i Azure DNS enligt beskrivningen i den här artikeln och arbeta sedan med leverantören för att [delegera zonen](dns-domain-delegation.md). Du kan sedan hantera PTR-posterna för varje omvänd sökning på samma sätt som andra posttyper.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Hur mycket kostar värd för min omvända DNS-uppslagszon?

Att vara värd för den omvända DNS-sökzonen för ditt IP-block som tilldelats internet i Azure DNS debiteras enligt [standard-Azure DNS-hastigheter](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan jag vara värd för omvända DNS-sökzoner för både IPv4- och IPv6-adresser i Azure DNS?

Ja. I den här artikeln beskrivs hur du skapar både IPv4- och IPv6-zoner för omvänd DNS-sökning i Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kan jag importera en befintlig omvänd DNS-uppslagszon?

Ja. Du kan använda Azure CLI för att importera befintliga DNS-zoner till Azure DNS. Den här metoden fungerar för både zoner för sökning framåt och zoner för omvänd sökning.

Mer information finns i [Importera och exportera en DNS-zonfil med Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Nästa steg

Mer information om omvänd DNS finns i [omvänd DNS-sökning på Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [hanterar omvända DNS-poster för dina Azure-tjänster](dns-reverse-dns-for-azure-services.md).
