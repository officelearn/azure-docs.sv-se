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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Skapa en Azure DNS-zon med CLI

> [!div class="op_single_selector"]
> * [Azure-portalen](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

Den här artikeln tar dig steg för steg igenom procedurerna för att skapa en DNS-zon med hjälp av plattformsoberoende Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också skapa en DNS-zon med hjälp av PowerShell- eller Azure-portalen.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera den senaste versionen av Azure CLI. Den finns tillgänglig för Windows, Linux och MAC. Mer information finns på [Installera Azure CLI](../xplat-cli-install.md).

## <a name="step-1---sign-in-and-create-a-resource-group"></a>Steg 1 – Logga in och skapa en resursgrupp

### <a name="switch-cli-mode"></a>Växla CLI-läge

Azure DNS använder Azure Resource Manager. Se till att du byter CLI-läge så att du kan använda ARM-kommandon.

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Du uppmanas att autentisera dig med dina autentiseringsuppgifter. Tänk på att du bara kan använda OrgID-konton.

```azurecli
azure login
```

### <a name="select-the-subscription"></a>Välja prenumerationen

Kontrollera prenumerationerna för kontot.

```azurecli
azure account list
```

Välj vilka av dina Azure-prenumerationer som du vill använda.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registrera resursprovider

Azure DNS-tjänsten hanteras av Microsoft.Network-resursprovidern. Din Azure-prenumeration måste vara registrerad att använda den här resursprovidern innan du kan använda Azure DNS. Det här är en engångsåtgärd för varje prenumeration.

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>Steg 2 – Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `azure network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `azure network dns zone create -h`.

Exemplet nedan skapar en DNS-zon med namnet *contoso.com* i resursgruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>Steg 3 – Verifiera

### <a name="view-records"></a>Visa poster

När du skapar en DNS-zon skapas även följande DNS-poster:

* SOA-posten (”Start of Authority”). Den här posten finns i roten på varje DNS-zon.
* Posterna för auktoritativa namnservrar (NS). Dessa poster visar vilka namnservrar som är värdar för zonen. Azure DNS använder en pool med namnservrar, vilket innebär att namnservrar kan tilldelas till olika zoner i Azure DNS. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

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

Om du inte har delegerat domänen att använda den nya zonen i Azure DNS än, så måste du dirigera DNS-frågan direkt till en av namnservrarna för zonen. Namnservrarna för zonen anges i NS-posterna från ”azure network dns record-set show” ovan. Var noga med att ersätta värdena för din zon i kommandot nedan.

I följande exempel används ”dig” för att fråga domänen contoso.com med hjälp av namnservrarna som tilldelats DNS-zonen. Frågan måste peka på en namnserver, där vi använde *@\<namnservern för zonen\>* och zonnamnet med ”dig”.

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

När du har skapat en DNS-zon kan du skapa [postuppsättningar och poster](dns-getstarted-create-recordset-cli.md) och skapa DNS-poster för din Internetdomän.




<!--HONumber=Dec16_HO2-->


