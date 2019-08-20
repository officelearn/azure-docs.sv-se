---
title: Flytta Azures nätverks resurser till en ny prenumeration eller resurs grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta virtuella nätverk och andra nätverks resurser till en ny resurs grupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: c41ab1c3309243fa3a96d907f620ffeff10376a1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626267"
---
# <a name="move-guidance-for-networking-resources"></a>Flytta vägledning för nätverks resurser

Den här artikeln beskriver hur du flyttar virtuella nätverk och andra nätverks resurser för vissa scenarier.

## <a name="dependent-resources"></a>Beroende resurser

När du flyttar ett virtuellt nätverk, måste du även flytta beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverksgatewayer och alla associerade anslutningsresurser. Lokala nätverksgatewayer kan finnas i en annan resursgrupp.

Om du vill flytta en virtuell dator med ett nätverkskort måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverks gränssnitts kortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayerna.

## <a name="state-of-dependent-resources"></a>Tillstånd för beroende resurser

Om käll-eller mål resurs gruppen innehåller ett virtuellt nätverk, kontrol leras tillståndet för alla beroende resurser för det virtuella nätverket under flytten. Om någon av dessa resurser är i ett felaktigt tillstånd blockeras flyttningen. Om till exempel en virtuell dator som använder det virtuella nätverket har misslyckats blockeras flyttningen. Flyttningen blockeras även om den virtuella datorn inte är en av de resurser som flyttas och inte finns i någon av resurs grupperna för flytten. Undvik det här problemet genom att flytta dina resurser till en resurs grupp som inte har ett virtuellt nätverk.

## <a name="peered-virtual-network"></a>Peer-kopplat virtuellt nätverk

Om du vill flytta en peer-kopplade virtuella nätverket måste du först inaktivera virtuell nätverkspeering. Du kan flytta det virtuella nätverket när inaktiverat. Återaktivera virtuell nätverkspeering efter överflyttningen.

## <a name="subnet-links"></a>Under näts länkar

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om Azure Cache för Redis-resursen har distribuerats i ett undernät har till exempel en resursnavigeringslänken i det undernätet.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../resource-group-move-resources.md).
