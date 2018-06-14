---
title: Komma igång med Azure DNS med hjälp av Azure CLI 2.0 | Microsoft Docs
description: Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första DNS-zon och DNS-post med Azure CLI 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: d24eaa4974f8bff09b337384e4fd139edb6ebd70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30175247"
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Komma igång med Azure DNS med hjälp av Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Den här artikeln visar hur du skapar din första DNS-zon och DNS-post med hjälp av plattformsoberoende Azure CLI 2.0, som är tillgängligt för Windows, Mac och Linux. Du kan också utföra de här stegen med Azure Portal eller Azure PowerShell.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

Anvisningarna förutsätter att du redan har installerat och loggat in på Azure CLI 2.0. Mer information finns i [Hantera DNS-zoner med hjälp av Azure CLI 2.0](dns-operations-dnszones-cli.md).

Azure DNS stöder nu också privata DNS-zoner (för närvarande i förhandsversion). Mer information om privata DNS-zoner finns i [Using Azure DNS for private domains](private-dns-overview.md) (Använda Azure DNS för privata domäner). Ett exempel på hur man kan skapa en privat DNS-zon finns i [Kom igång med privata Azure DNS-zoner med CLI](./private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp som ska innehålla DNS-zonen innan du skapar DNS-zonen. Nedan visas kommandot.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `az network dns zone create`. Om du vill se hjälpen för det här kommandot skriver du `az network dns zone create -h`.

Exemplet nedan skapar en DNS-zon som heter *contoso.com* i resursgruppen med namnet *MyResourceGroup*. Använd exemplet när du vill skapa en DNS-zon, och ersätt värdena med dina egna.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Skapa en DNS-post

Skapa en DNS-post genom att använda kommandot `az network dns record-set [record type] add-record`. Mer information, om exempelvis A-poster, finns i `azure network dns record-set A add-record -h`.

I följande exempel skapas en post med det relativa namnet "www" i resursgruppen "MyResourceGroup" i DNS-zonen "contoso.com". Postuppsättningens fullständigt kvalificerade namn är ”www.contoso.com”. Postens typ är "A", IP-adressen är "1.2.3.4" och en standard-TTL på 3 600 sekunder (1 timme) används.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Information om andra posttyper, postuppsättningar med fler än en post, alternativa TTL-värden och ändring av befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Visa poster

Om du vill visa en lista med DNS-poster i din zon använder du:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Uppdatera namnservrar

När du är nöjd med konfigurationen av DNS-zonen och DNS-posterna måste du konfigurera ditt domännamn för användning med Azure DNS-namnservrarna. Det gör att andra användare på Internet kan hitta dina DNS-poster.

Namnservrarna för din zon anges av `az network dns zone show`-kommandot. Använd JSON-utdata (se följande exempel) om du vill se namnservernamnen.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Dessa namnservrar ska konfigureras med domännamnsregistratorn (där du köpte domännamnet). Registratorn erbjuder möjligheten att konfigurera namnservrar för domänen. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Ta bort alla resurser
 
Så här tar du bort alla resurser som skapats i den här artikeln:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure DNS i [Översikt över Azure DNS](dns-overview.md).

Mer information om hur du hanterar DNS-zoner i Azure DNS finns i [Hantera DNS-zoner i Azure DNS med Azure CLI 2.0](dns-operations-dnszones-cli.md).

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar i Azure DNS med Azure CLI 2.0](dns-operations-recordsets-cli.md).
