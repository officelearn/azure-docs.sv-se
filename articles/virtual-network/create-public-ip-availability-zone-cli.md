---
title: Skapa en zoned offentliga IP-adress med Azure CLI | Microsoft Docs
description: "Skapa en offentlig IP-adress i en zon för tillgänglighet med Azure CLI."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Skapa en offentlig IP-adress i en zon för tillgänglighet med Azure CLI

Du kan distribuera en offentlig IP-adress i en zon för Azure tillgänglighet (förhandsversion). En zon för tillgänglighet är ett fysiskt separat zon i en Azure-region. Lär dig att:

> * Skapa en offentlig IP-adress i en zon för tillgänglighet
> * Identifiera relaterade resurser som skapats i zonen tillgänglighet

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt krävs kursen att du kör en version av Azure CLI högre än version 2.0.17. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Skapa en zonal offentliga IP-adress

Skapa en offentlig IP-adress i en zon för tillgänglighet med hjälp av följande kommando:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="get-zone-information-about-a-public-ip-address"></a>Hämta zonen information om en offentlig IP-adress

Hämta zoninformation av en offentlig IP-adress med följande kommando:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Lär dig mer om [offentliga IP-adresser](../virtual-network/virtual-network-public-ip-address.md) 
