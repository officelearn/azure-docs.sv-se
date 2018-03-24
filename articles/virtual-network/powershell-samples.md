---
title: Azure PowerShell-exempel för virtuellt nätverk | Microsoft Docs
description: Azure PowerShell-exempel för det virtuella nätverket.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: c9cb8da2a6a7512daa6721af90d5b21c6ba5e8e5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-exempel för det virtuella nätverket

Följande tabell innehåller länkar till Azure Powershell-skript:

| | |
|----|----|
| [Skapa ett virtuellt nätverk för flera nivåer program](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Skapar ett virtuellt nätverk med frontend och backend-undernät. Trafik till undernätet för frontend är begränsad till HTTP, medan trafik till backend-undernät är begränsad till SQL, port 1433. |
| [Peer-två virtuella nätverk](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Skapar och ansluter två virtuella nätverk i samma region. |
| [Vidarebefordra trafik via en virtuell nätverksenhet](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Skapar ett virtuellt nätverk med frontend och backend-undernät och en virtuell dator som kan vidarebefordra trafik mellan två undernät. |
| [Filtrera inkommande och utgående nätverkstrafik för VM](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Skapar ett virtuellt nätverk med frontend och backend-undernät. Inkommande nätverkstrafik till undernätet frontend är begränsad till HTTP och HTTPS. Utgående trafik till internet från backend-undernät är inte tillåtet. |
