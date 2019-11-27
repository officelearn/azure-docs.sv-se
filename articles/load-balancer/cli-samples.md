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
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225425"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-exempel för belastningsutjämnare

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
| [Belastnings Utjämnings trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-linux-cli-sample-nlb.md) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration. |
| [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Skapar tre virtuella datorer i olika tillgänglighetszoner inom en region och en Standard Load Balancer med zonredundant frontend IP-adress. Den här konfigurationen för belastningsutjämnaren hjälper dig för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. |
|[Belastningsutjämna virtuella datorer i en specifik tillgänglighetszon](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Skapar tre virtuella datorer, en Standard Load Balancer med zonindelad frontend-IP som hjälper dig att justera datasökväg och resurser i en enskild zon för en viss region.|
| [Belastningsutjämna flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Skapar två virtuella datorer med flera IP-konfigurationer, ansluten till en Azure-Tillgänglighetsuppsättning är tillgängliga via en Azure Load Balancer. |
| | |

