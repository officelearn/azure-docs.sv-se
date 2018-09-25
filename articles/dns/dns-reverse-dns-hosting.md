---
title: Vara värd för DNS-zoner för omvänd sökning i Azure DNS | Microsoft Docs
description: Lär dig hur du använder Azure DNS som värd för omvänd DNS-sökningszoner för IP-adressintervall
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: e45b3bde0d5077a5d18369236e81bcd467527940
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990152"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Värden DNS-zoner för omvänd sökning i Azure DNS

Den här artikeln beskriver hur du vara värd för omvänd DNS-sökningszoner för dina tilldelade IP-adressintervall i Azure DNS. IP-adressintervall som representeras av zoner för omvänd sökning måste tilldelas till din organisation, vanligtvis av din Internetleverantör.

För att konfigurera omvänd DNS för en Azure-ägda IP-adress som är tilldelad till din Azure-tjänst, se [konfigurera omvänd DNS för tjänster som hanteras i Azure](dns-reverse-dns-for-azure-services.md).

Innan du läser den här artikeln bör du är bekant med den [översikt över omvänd DNS- och stöd i Azure](dns-reverse-dns-overview.md).

Den här artikeln vägleder dig igenom stegen för att skapa din första DNS-zon för omvänd sökning och -post med hjälp av Azure portal, Azure PowerShell, Azure klassiskt CLI eller Azure CLI.

## <a name="create-a-reverse-lookup-dns-zone"></a>Skapa en DNS-zon för omvänd sökning

1. Logga in på [Azure Portal](https://portal.azure.com).
1. På den **Hub** menyn och välj **New** > **nätverk**, och välj sedan **DNS-zon**.

   ![Val av ”DNS-zonen”](./media/dns-reverse-dns-hosting/figure1.png)

1. I den **skapa DNS-zon** fönstret namnge din DNS-zon. Namnet på zonen är utformade på olika sätt för IPv4 och IPv6-prefix. Följ instruktionerna för [IPv4](#ipv4) eller [IPv6](#ipv6) att namnge din zon. När du är klar väljer du **skapa** att skapa zonen.

### <a name="ipv4"></a>IPv4

Namnet på en IPv4 zon för omvänd sökning baseras på IP-adressintervall som representerar. Det bör vara i följande format: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Exempel finns i [översikt över omvänd DNS- och stöd i Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> När du skapar classless DNS-zoner för omvänd sökning i Azure DNS kan du använda ett bindestreck (`-`) i stället för ett snedstreck (`/`) i zonnamnet.
>
> Till exempel för IP-adressintervall 192.0.2.128/26, du måste använda `128-26.2.0.192.in-addr.arpa` som zonnamnet i stället för `128/26.2.0.192.in-addr.arpa`.
>
> Även om DNS-standarden stöd för båda metoderna, Azure DNS stöder inte DNS-zonnamn som innehåller för snedstreck (`/`) tecken.

I följande exempel visas hur du skapar en klass C omvänd DNS-zon med namnet `2.0.192.in-addr.arpa` i Azure DNS via Azure portal:

 ![”Skapa DNS-zonen” fönstret med rutorna ifyllt](./media/dns-reverse-dns-hosting/figure2.png)

**Resursgruppens plats** definierar platsen för resursgruppen. Det har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

I följande exempel visas hur du utför den här uppgiften med hjälp av Azure PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
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

Namnet på en zon för omvänd sökning av IPv6 ska vara i följande format: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Exempel finns i [översikt över omvänd DNS- och stöd i Azure](dns-reverse-dns-overview.md#ipv6).


I följande exempel visas hur du skapar en IPv6 omvänd DNS-sökningszon med namnet `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` i Azure DNS via Azure portal:

 ![”Skapa DNS-zonen” fönstret med rutorna ifyllt](./media/dns-reverse-dns-hosting/figure3.png)

**Resursgruppens plats** definierar platsen för resursgruppen. Det har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid ”global” och visas inte.

I följande exempel visas hur du utför den här uppgiften med hjälp av Azure PowerShell och Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegera en zon för omvänd DNS-sökning

Nu när du har skapat din zon för omvänd DNS-sökning, måste du kontrollera att zonen delegeras från den överordnade zonen. DNS-delegering kan DNS-namnet matchningsprocessen att hitta namnservrarna som är värdar för din zon för omvänd DNS-sökning. Dessa namnservrar kan sedan besvara omvänd DNS-frågor för IP-adresser i ditt-adressintervall.

För zoner för vanlig sökning beskrivs hur du delegerar en DNS-zon i [Delegera din domän till Azure DNS](dns-delegate-domain-azure-dns.md). Delegering för zoner för omvänd sökning fungerar på samma sätt. Den enda skillnaden är att du behöver konfigurera namnservrarna med Internetleverantören som tillhandahålls av IP-adressintervall i stället för din domännamnsregistrator.

## <a name="create-a-dns-ptr-record"></a>Skapa en DNS PTR-post

### <a name="ipv4"></a>IPv4

I följande exempel vägleder dig genom processen att skapa en PTR-post i en omvänd DNS-zon i Azure DNS. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Överst på den **DNS-zon** väljer **+ postuppsättning** att öppna den **lägga till en postuppsättning** fönstret.

   ![Knappen för att skapa en postuppsättning](./media/dns-reverse-dns-hosting/figure4.png)

1. Namnet på posten för en PTR-post måste vara resten av IPv4-adressen i omvänd ordning. 

   I det här exemplet är de tre första oktetterna redan ifyllda som en del av zonnamnet (.2.0.192). Därför bara den sista oktetten har angetts i den **namn** box. Exempelvis kan du namnge din postuppsättning **15** för en resurs vars IP-adressen är 192.0.2.15.  
1. För **typ**väljer **PTR**.  
1. För **domännamn**, ange det fullständigt kvalificerade domännamnet (FQDN) som använder den IP-Adressen.
1. Välj **OK** längst ned i fönstret för att skapa DNS-post.

 ![”Lägg till uppsättning av poster” fönstret med rutorna ifyllt](./media/dns-reverse-dns-hosting/figure5.png)

I följande exempel visas hur du utför den här uppgiften med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
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

I följande exempel vägleder dig genom processen för att skapa nya PTR-post. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).

1. Överst på den **DNS-zon** väljer **+ postuppsättning** att öppna den **lägga till en postuppsättning** fönstret.

   ![Knappen för att skapa en postuppsättning](./media/dns-reverse-dns-hosting/figure6.png)

2. Namnet på posten för en PTR-post måste vara resten av IPv6-adressen i omvänd ordning. Namnet får inte innehålla noll komprimering. 

   I det här exemplet fylls redan första 64 bitar i IPv6 som en del av zonnamnet (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Därför bara de senaste 64-bitarna tillhandahålls i den **namn** box. De senaste 64-bitarna för IP-adressen har angetts i omvänd ordning med en punkt som avgränsare mellan varje hexadecimalt tal. Exempelvis kan du namnge din postuppsättning **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** för en resurs vars IP-adressen är 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
3. För **typ**väljer **PTR**.  
4. För **domännamn**, ange FQDN för den resurs som använder den IP-Adressen.
5. Välj **OK** längst ned i fönstret för att skapa DNS-post.

![”Lägg till uppsättning av poster” fönstret med rutorna ifyllt](./media/dns-reverse-dns-hosting/figure7.png)

I följande exempel visas hur du utför den här uppgiften med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure CLI

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Visa poster

Bläddra till din DNS-zon i Azure portal om du vill visa de poster som du skapade. I den nedre delen av den **DNS-zon** fönstret ser du poster för DNS-zonen. Du bör se standard NS och SOA-poster, plus eventuella nya poster som du har skapat. NS och SOA-poster skapas i varje zon. 

### <a name="ipv4"></a>IPv4

Den **DNS-zon** visar IPv4 PTR-poster:

![”DNS-zonen” fönstret med IPv4-poster](./media/dns-reverse-dns-hosting/figure8.png)

I följande exempel visas hur du visar PTR-poster med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Den **DNS-zon** visar IPv6 PTR-poster:

![”DNS-zonen” fönstret med IPv6-poster](./media/dns-reverse-dns-hosting/figure9.png)

I följande exempel visas hur du visar poster med hjälp av PowerShell eller Azure CLI.

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassisk Azure CLI

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kan jag vara värd zoner för omvänd DNS-sökning i min ISP-tilldelad IP-Adressblock på Azure DNS?

Ja. Hantera zoner för omvänd sökning (ARPA) för dina egna IP-adressintervall i Azure DNS stöds fullt ut.

Skapa zon för omvänd sökning i Azure DNS som beskrivs i den här artikeln och sedan arbeta med din ISP och [delegera zonen](dns-domain-delegation.md). Du kan sedan hantera PTR-poster för varje omvänd sökning på samma sätt som andra typer av poster.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Hur mycket kostar som är värd för min omvänd DNS-sökning zon kostnad?

Som är värd för DNS-zonen för omvänd sökning för din ISP-tilldelad IP-Adressblock i Azure DNS som ingår debiteras enligt [standardpriser för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kan jag vara värd för DNS-zoner för omvänd sökning för både IPv4 och IPv6-adresser i Azure DNS?

Ja. Den här artikeln beskriver hur du skapar både IPv4 och IPv6 DNS-zoner för omvänd sökning i Azure DNS.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kan jag importera en befintlig omvänd DNS-sökningszon?

Ja. Du kan använda Azure CLI för att importera befintliga DNS-zoner i Azure DNS. Den här metoden fungerar för både zoner för vanlig sökning och zoner för omvänd sökning.

Mer information finns i [importera och exportera en DNS-zonfil med Azure CLI](dns-import-export.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om omvänd DNS [omvänd DNS-sökning på Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Lär dig hur du [hantera omvända DNS-poster för dina Azure-tjänster](dns-reverse-dns-for-azure-services.md).
