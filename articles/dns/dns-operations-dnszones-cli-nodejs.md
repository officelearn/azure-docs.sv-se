---
title: Hantera DNS-zoner i Azure DNS - Azure CLI 1.0 | Microsoft Docs
description: "Du kan hantera DNS-zoner som använder Azure CLI 1.0. Den här artikeln visar hur du uppdatera, ta bort och skapa DNS-zoner på Azure DNS."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: 588c87749f049eff5b9e0729f6769c8367ba41e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-10"></a>Hur du hanterar DNS-zoner i Azure DNS med hjälp av Azure CLI 1.0

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)

Den här guiden visar hur du hanterar DNS-zoner med hjälp av plattformsoberoende Azure CLI 1.0, som är tillgänglig för Windows, Mac och Linux. Du kan också hantera DNS-zoner med [Azure PowerShell](dns-operations-dnszones.md) eller Azure-portalen.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften

Du kan slutföra uppgiften med någon av följande CLI-versioner:

* [Azure CLI 1.0](dns-operations-dnszones-cli-nodejs.md) – vår CLI för distributionsmodellerna klassisk och resurshantering.
* [Azure CLI 2.0](dns-operations-dnszones-cli.md) – vår nästa generations CLI för distributionsmodellen resurshantering.

## <a name="introduction"></a>Introduktion

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="getting-help"></a>Få hjälp

Alla CLI 1.0-kommandon som är relaterade till Azure DNS börja med `azure network dns`. Hjälp är tillgänglig för varje kommando med hjälp av den `--help` alternativet (kort form `-h`).  Exempel:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `azure network dns zone create`. Om du vill ha hjälp, så gå till `azure network dns zone create -h`.

I följande exempel skapas en DNS-zon som kallas *contoso.com* i resursgruppen kallas *MyResourceGroup*:

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Skapa en DNS-zon med taggar

I följande exempel visas hur du skapar en DNS-zon med två [Azure Resource Manager taggar](dns-zones-records.md#tags), *projekt = demo* och *env = test*, med hjälp av den `--tags` parameter (kort form `-t`):

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Hämta en DNS-zon

Använd för att hämta en DNS-zon `azure network dns zone show`. Om du vill ha hjälp, så gå till `azure network dns zone show -h`.

I följande exempel returneras DNS-zonen *contoso.com* och dess associerade data från resursgruppen *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com
```

Följande exempel är svaret.

```
info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Observera att DNS-poster inte returneras av `azure network dns zone show`. Använd om du vill visa en lista med DNS-poster `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Lista över DNS-zoner

För att räkna upp DNS-zoner, Använd `azure network dns zone list`. Om du vill ha hjälp, så gå till `azure network dns zone list -h`.

Anger resursgruppens namn visas endast de zonerna i resursgruppen:

```azurecli
azure network dns zone list MyResourceGroup
```

Om du utesluter resursgruppen visar en lista över alla zoner i prenumerationen:

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Uppdatera en DNS-zon

En DNS-zon resurs kan ändras med hjälp av `azure network dns zone set`. Om du vill ha hjälp, så gå till `azure network dns zone set -h`.

Det här kommandot uppdaterar inte någon DNS-postuppsättningar i zonen (se [hur du hanterar DNS-poster](dns-operations-recordsets-cli-nodejs.md)). Den används endast för att uppdatera egenskaper för resursen zonen. Dessa egenskaper är för tillfället begränsad till den [Azure Resource Manager ”-taggar'](dns-zones-records.md#tags) för zonen resursen.

I följande exempel visas hur du uppdaterar taggarna i en DNS-zon. De befintliga taggarna ersättas med det angivna värdet.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Ta bort en DNS-zon

DNS-zoner kan tas bort med `azure network dns zone delete`. Om du vill ha hjälp, så gå till `azure network dns zone delete -h`.

> [!NOTE]
> En DNS-zon också tar du bort DNS-poster i zonen. Den här åtgärden kan inte ångras. Om DNS-zonen misslyckas tjänster med hjälp av zonen när zonen tas bort.
>
>För att skydda mot oavsiktlig zonen borttagning finns [Skydda DNS-zoner och poster](dns-protect-zones-recordsets.md).

Det här kommandot uppmanas att bekräfta. Det valfria `--quiet` växla (kort form `-q`) förhindrar det här meddelandet.

I följande exempel visas hur du tar bort zonen *contoso.com* från resursgruppen *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hantera uppsättningar av poster och poster](dns-getstarted-create-recordset-cli-nodejs.md) i DNS-zonen.

Lär dig hur du [Delegera din domän till Azure DNS](dns-domain-delegation.md).

