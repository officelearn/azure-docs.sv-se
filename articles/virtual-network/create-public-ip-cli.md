---
title: Skapa en offentlig IP-Azure CLI
description: Lär dig hur du skapar en offentlig IP-adress med hjälp av Azure CLI
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 3e6e060f4af2addba66ed02e82d76dd520375f25
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516415"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Snabb start: skapa en offentlig IP-adress med hjälp av Azure CLI

Den här artikeln visar hur du skapar en offentlig IP-adressresurs med Azure CLI. Mer information om vilka resurser som kan kopplas till, skillnaden mellan Basic-och standard-SKU och annan relaterad information finns i [offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  I det här exemplet kommer vi bara att fokusera på IPv4-adresser. Mer information om IPv6-adresser finns i [IPv6 för Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) med namnet **myResourceGroup** på **eastus2** -platsen.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**Standard-SKU – använda zoner**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Följande kommando fungerar för API version 2020-08-01 eller senare.  Mer information om den API-version som används för närvarande finns i [resurs leverantörer och typer](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) för att skapa en standard zon som REDUNDANT offentlig IP-adress med namnet **myStandardZRPublicIP** i **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> För versioner av API: t som är äldre än 2020-08-01 kör du kommandot ovan utan att ange en zon parameter för att skapa en zon-redundant IP-adress. 
>

Använd följande kommando för att skapa en standard zonindelade offentlig IP-adress i Zon 2 med namnet **myStandardZonalPublicIP** i **myResourceGroup** :

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Observera att ovanstående alternativ för zoner endast är giltiga val i regioner med [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standard-SKU – inga zoner**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Följande kommando fungerar för API version 2020-08-01 eller senare.  Mer information om den API-version som används för närvarande finns i [resurs leverantörer och typer](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) för att skapa en offentlig standard-IP-adress som en icke-zonindelade resurs med namnet **myStandardPublicIP** i **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Det här valet är giltigt i alla regioner och är standard valet för offentliga standard-IP-adresser i regioner utan [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-create-public-ip-basic)

Använd [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) för att skapa en grundläggande statisk offentlig IP-adress med namnet **myBasicPublicIP** i **myResourceGroup**.  Grundläggande offentliga IP-adresser har inte konceptet tillgänglighets zoner.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Om det är acceptabelt att IP-adressen kan ändras över tid kan du välja **dynamisk** IP-tilldelning genom att ändra tilldelnings metoden till dynamisk.

---

## <a name="additional-information"></a>Ytterligare information 

Mer information om de enskilda variablerna ovan finns i [hantera offentliga IP-adresser](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Nästa steg
- Associera en [offentlig IP-adress till en virtuell dator](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- Läs mer om [offentliga IP-adresser](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).
