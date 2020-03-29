---
title: Flytta Azure Networking-resurser till ny prenumeration eller resursgrupp
description: Använd Azure Resource Manager för att flytta virtuella nätverk och andra nätverksresurser till en ny resursgrupp eller prenumeration.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485239"
---
# <a name="move-guidance-for-networking-resources"></a>Flytta vägledning för nätverksresurser

I den här artikeln beskrivs hur du flyttar virtuella nätverk och andra nätverksresurser för specifika scenarier.

## <a name="dependent-resources"></a>Beroende resurser

När du flyttar ett virtuellt nätverk måste du också flytta dess beroende resurser. För VPN-gateways måste du flytta IP-adresser, virtuella nätverksgateways och alla associerade anslutningsresurser. Lokala nätverksgateways kan finnas i en annan resursgrupp.

Om du vill flytta en virtuell dator med ett nätverkskort till en ny prenumeration måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverkskortet, alla andra nätverkskort för det virtuella nätverket och VPN-gateways.

Mer information finns i [Scenario för flyttning mellan prenumerationer](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Peered virtuellt nätverk

Om du vill flytta ett peer-virtuellt nätverk måste du först inaktivera den virtuella nätverks peering. När du är inaktiverad kan du flytta det virtuella nätverket. Efter flytten kan du återinrätta den virtuella nätverks peering.

## <a name="subnet-links"></a>Nätlänkar

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om till exempel en Azure Cache för Redis-resurs distribueras till ett undernät har det undernätet en resursnavigeringslänk.

## <a name="next-steps"></a>Nästa steg

Kommandon som ska flyttas finns i [Flytta resurser till ny resursgrupp eller prenumeration](../move-resource-group-and-subscription.md).
