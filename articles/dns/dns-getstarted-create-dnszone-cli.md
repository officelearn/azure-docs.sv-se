---
title: Skapa en DNS-zon med Azure CLI 2.0| Microsoft Docs
description: "Läs mer om hur du skapar DNS-zoner i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första DNS-zon med hjälp av Azure CLI 2.0."
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 02/28/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Skapa en Azure DNS-zon med Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

Den här artikeln tar dig steg för steg igenom procedurerna för att skapa en DNS-zon med hjälp av plattformsoberoende Azure CLI, som är tillgängligt för Windows, Mac och Linux. Du kan också skapa en DNS-zon med [Azure PowerShell](dns-getstarted-create-dnszone.md) eller [Azure-portalen](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering.
* [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md) – vår nästa generations CLI för distributionsmodellen resurshantering.

## <a name="introduction"></a>Introduktion

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Konfigurera Azure CLI 2.0 för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

* Installera den senaste versionen av Azure CLI. Den finns tillgänglig för Windows, Linux och MAC. Mer information finns på [Installera Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna ett konsolfönster och autentisera med dina autentiseringsuppgifter. Mer information finns i Logga in i Azure från Azure CLI

```azurecli
az login
```

### <a name="select-the-subscription"></a>Välja prenumerationen

Kontrollera prenumerationerna för kontot.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Välj vilka av dina Azure-prenumerationer som du vill använda.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `az network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `az network dns zone create --help`.

Exemplet nedan skapar en DNS-zon som heter *contoso.com* i resursgruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Verifiera din DNS-zon

### <a name="view-records"></a>Visa poster

När du skapar en DNS-zon skapas även följande DNS-poster:

* Posten *Auktoritetsstart* (SOA). Den här posten finns i roten på varje DNS-zon.
* Posterna för auktoritativa namnservrar (NS). De här posterna visar vilka namnservrar som är värdar för zonen. Azure DNS använder en pool med namnservrar, vilket innebär att namnservrar kan tilldelas olika zoner i Azure DNS. Mer information finns i [delegera en domän till Azure DNS](dns-domain-delegation.md).

Om du vill visa de här posterna, så använd `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

Svaret för `az network dns record-set list` visas nedan:

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> Postuppsättningar i roten (eller *toppen*) av en DNS-zon använder **@** som postuppsättningsnamn.

### <a name="test-name-servers"></a>Testa namnservrar

Du kan testa om din DNS-zon är närvarande på Azure DNS-namnservrarna genom att använda DNS-verktyg som nslookup, dig eller PowerShell-cmdleten `Resolve-DnsName`.

Om du inte har delegerat domänen att använda den nya zonen i Azure DNS än, så måste du dirigera DNS-frågan direkt till en av namnservrarna för zonen. Namnservrarna för din zon anges i NS-poster, som de anges av `az network dns record-set list`.

Följande exempel använder ”dig” för att fråga domänen contoso.com med hjälp av namnservrarna som tilldelats DNS-zonen. Var noga med att ersätta rätt värden för din zon.

```
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
```

## <a name="next-steps"></a>Nästa steg

När du har skapat en DNS-zon [skapar du DNS-postuppsättningarna och posterna](dns-getstarted-create-recordset-cli.md) i din zon.


