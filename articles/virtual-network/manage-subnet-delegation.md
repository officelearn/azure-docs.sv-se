---
title: Lägga till eller ta bort en under näts delegering i ett virtuellt Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur du lägger till eller tar bort ett delegerat undernät för en tjänst i Azure i Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 9bf44aa5ab18f94ec91650548dc13360ce1b1f3d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938504"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Lägga till eller ta bort en under näts delegering

Under näts delegering ger explicita behörigheter till tjänsten för att skapa tjänstespecifika resurser i under nätet med en unik identifierare när tjänsten distribueras. Den här artikeln beskriver hur du lägger till eller tar bort ett delegerat undernät för en Azure-tjänst.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet skapar du ett virtuellt nätverk och under nätet som du senare kommer att delegera till en Azure-tjänst.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Name | Ange *MyVirtualNetwork*. |
    | Adressutrymme | Ange *10.0.0.0/16*. |
    | Subscription | Välj din prenumeration.|
    | Resource group | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **öster**.|
    | Undernät – Namn | Ange *undernät*. |
    | Undernät – adressintervall | Ange *10.0.0.0/24*. |
    |||
1. Lämna resten som standard och välj sedan **skapa**.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegera ett undernät till en Azure-tjänst

I det här avsnittet delegerar du det undernät som du skapade i föregående avsnitt till en Azure-tjänst.

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I Sök resultaten väljer du *myVirtualNetwork*.
3. Välj **undernät**under **Inställningar**och välj sedan **undernät**.
4. På sidan för **under näts Delegerings** listan *väljer du bland* de tjänster som listas under **delegera undernät till en tjänst** (till exempel **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Ta bort under näts delegering från en Azure-tjänst

1. I portalens sökfält anger du *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
2. I Sök resultaten väljer du *myVirtualNetwork*.
3. Välj **undernät**under **Inställningar**och välj sedan **undernät**.
4. Välj **ingen** från tjänsterna som listas under **delegera undernät till en tjänst**för under **näts delegering** *på sidan för undernät.* 

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [hanterar undernät i Azure](virtual-network-manage-subnet.md).
