---
title: Azure CLI-exempel för belastningsutjämnare
titleSuffix: Azure Load Balancer
description: Azure CLI-exempel
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225425"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-exempel för belastningsutjämnare

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
| [Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-linux-cli-sample-nlb.md) | Skapar flera virtuella datorer i en högtillgänglig och belastningsbalanserad konfiguration. |
| [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Skapar tre virtuella datorer i olika tillgänglighetszoner inom en region och standardbelastningsutjämnare med en zonlös ip-adress för klientdel. Den här belastningsutjämnarkonfigurationen hjälper till att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. |
|[Belastningsutjämna virtuella datorer i en specifik tillgänglighetszon](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Skapar tre virtuella datorer, en standardbelastningsutjämning med zonindelnings-klientdels-IP som hjälper till att justera datasökväg och resurser i en enda zon för en viss region.|
| [Belastningsutjämna flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Skapar två virtuella datorer med flera IP-konfigurationer, kopplade till en Azure-tillgänglighetsuppsättning som är tillgänglig via en Azure Load Balancer. |
| | |

