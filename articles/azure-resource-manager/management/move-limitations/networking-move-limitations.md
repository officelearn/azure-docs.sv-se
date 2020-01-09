---
title: Flytta Azure nätverks resurser till en ny prenumeration eller resurs grupp
description: Använd Azure Resource Manager för att flytta virtuella nätverk och andra nätverks resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 0cd6887d3489f2ffede0f5e3d63533a33a6ccc04
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485239"
---
# <a name="move-guidance-for-networking-resources"></a>Flytta vägledning för nätverks resurser

Den här artikeln beskriver hur du flyttar virtuella nätverk och andra nätverks resurser för vissa scenarier.

## <a name="dependent-resources"></a>Beroende resurser

När du flyttar ett virtuellt nätverk, måste du även flytta beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverksgatewayer och alla associerade anslutningsresurser. Lokala nätverksgatewayer kan finnas i en annan resursgrupp.

Om du vill flytta en virtuell dator med ett nätverkskort till en ny prenumeration måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverks gränssnitts kortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayerna.

Mer information finns i [scenario för att flytta mellan prenumerationer](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions).

## <a name="peered-virtual-network"></a>Peer-kopplat virtuellt nätverk

Om du vill flytta en peer-kopplade virtuella nätverket måste du först inaktivera virtuell nätverkspeering. Du kan flytta det virtuella nätverket när inaktiverat. Återaktivera virtuell nätverkspeering efter överflyttningen.

## <a name="subnet-links"></a>Under näts länkar

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om Azure Cache för Redis-resursen har distribuerats i ett undernät har till exempel en resursnavigeringslänken i det undernätet.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).
