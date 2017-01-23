---
title: Skapa en DNS-zon med CLI| Microsoft Docs
description: "Läs mer om hur du skapar DNS-zoner i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första DNS-zon med hjälp av Azure CLI."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Skapa en Azure DNS-zon med CLI

> [!div class="op_single_selector"]
> * [Azure-portalen](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

Den här artikeln tar dig steg för steg igenom procedurerna för att skapa en DNS-zon med hjälp av plattformsoberoende Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också skapa en DNS-zon med [Azure PowerShell](dns-getstarted-create-dnszone.md) eller [Azure-portalen](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `azure network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `azure network dns zone create -h`.

Exemplet nedan skapar en DNS-zon som heter *contoso.com* i resursgruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Verifiera din DNS-zon

### <a name="view-records"></a>Visa poster

När du skapar en DNS-zon skapas även följande DNS-poster:

* Posten *Auktoritetsstart* (SOA). Den här posten finns i roten på varje DNS-zon.
* Posterna för auktoritativa namnservrar (NS). De här posterna visar vilka namnservrar som är värdar för zonen. Azure DNS använder en pool med namnservrar, vilket innebär att namnservrar kan tilldelas olika zoner i Azure DNS. Mer information finns i [delegera en domän till Azure DNS](dns-domain-delegation.md).

Om du vill visa de här posterna, så använd `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> Postuppsättningar i roten (eller *toppen*) av en DNS-zon använder **@** som postuppsättningsnamn.

### <a name="test-name-servers"></a>Testa namnservrar

Du kan testa om din DNS-zon är närvarande på Azure DNS-namnservrarna genom att använda DNS-verktyg som nslookup, dig eller PowerShell-cmdleten `Resolve-DnsName`.

Om du inte har delegerat domänen att använda den nya zonen i Azure DNS än, så måste du dirigera DNS-frågan direkt till en av namnservrarna för zonen. Namnservrarna för din zon anges i NS-poster, som de anges av `azure network dns record-set list`.

Följande exempel använder ”dig” för att fråga domänen contoso.com med hjälp av namnservrarna som tilldelats DNS-zonen. Var noga med att ersätta rätt värden för din zon.

     > dig @ns1-01.azure-dns.com contoso.com
     
     <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
     Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
     flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
     WARNING: recursion requested but not available

     OPT PSEUDOSECTION:
     EDNS: version: 0, flags:; udp: 4000
      QUESTION SECTION:
    contoso.com.                        IN      A

     AUTHORITY SECTION:
    contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Nästa steg

När du har skapat en DNS-zon [skapar du DNS-postuppsättningarna och posterna](dns-getstarted-create-recordset-cli.md) i din zon.




<!--HONumber=Dec16_HO3-->


