---
title: Flytta virtuella nätverk i Azure-resurser till ny prenumeration eller resursgrupp grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta virtuella nätverk till en ny resursgrupp eller prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 809d403a463048910a284e7f8fcdc18cf7c65b69
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723498"
---
# <a name="move-guidance-for-virtual-networks"></a>Flytta vägledning för virtuella nätverk

Den här artikeln beskrivs hur du flyttar virtuella nätverk för specifika scenarier.

## <a name="dependent-resources"></a>Beroende resurser

När du flyttar ett virtuellt nätverk, måste du även flytta beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverksgatewayer och alla associerade anslutningsresurser. Lokala nätverksgatewayer kan finnas i en annan resursgrupp.

Om du vill flytta en virtuell dator med ett nätverkskort måste du flytta alla beroende resurser. Flytta det virtuella nätverket för nätverkskortet, alla andra nätverkskort för det virtuella nätverket och VPN-gatewayer.

## <a name="peered-virtual-network"></a>Peer-kopplade virtuella nätverket

Om du vill flytta en peer-kopplade virtuella nätverket måste du först inaktivera virtuell nätverkspeering. Du kan flytta det virtuella nätverket när inaktiverat. Återaktivera virtuell nätverkspeering efter överflyttningen.

## <a name="subnet-links"></a>Länkar till undernätverk

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om Azure Cache för Redis-resursen har distribuerats i ett undernät har till exempel en resursnavigeringslänken i det undernätet.

## <a name="next-steps"></a>Nästa steg

Kommandon att flytta resurser finns i [flytta resurser till ny resursgrupp eller prenumeration](../resource-group-move-resources.md).
