---
title: Flytta Azures nätverks resurser till en ny prenumeration eller resurs grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta virtuella nätverk och andra nätverks resurser till en ny resurs grupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 6ad7f32704d8cb73999a6e3cf60cb2a238268242
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034552"
---
# <a name="move-guidance-for-networking-resources"></a>Flytta vägledning för nätverks resurser

Den här artikeln beskriver hur du flyttar virtuella nätverk och andra nätverks resurser för vissa scenarier.

## <a name="dependent-resources"></a>Beroende resurser

När du flyttar ett virtuellt nätverk, måste du även flytta beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverksgatewayer och alla associerade anslutningsresurser. Lokala nätverksgatewayer kan finnas i en annan resursgrupp.

Om du vill flytta en virtuell dator med ett nätverkskort måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverks gränssnitts kortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayerna.

## <a name="peered-virtual-network"></a>Peer-kopplat virtuellt nätverk

Om du vill flytta en peer-kopplade virtuella nätverket måste du först inaktivera virtuell nätverkspeering. Du kan flytta det virtuella nätverket när inaktiverat. Återaktivera virtuell nätverkspeering efter överflyttningen.

## <a name="subnet-links"></a>Under näts länkar

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om Azure Cache för Redis-resursen har distribuerats i ett undernät har till exempel en resursnavigeringslänken i det undernätet.

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../resource-group-move-resources.md).
