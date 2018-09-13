---
title: Azure CLI-exempel för Load Balancer | Microsoft Docs
description: Azure CLI-exempel
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 41e80e04051a24fd32086c61c65bc3eec1564c57
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35963907"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-exempel för belastningsutjämnare

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| | |
|-|-|
| [Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet](./scripts/load-balancer-linux-cli-sample-nlb.md) | Skapar flera virtuella datorer med hög tillgänglighet och belastningsutjämnade konfiguration. |
| [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Skapar tre virtuella datorer i olika tillgänglighetszoner inom en region och en Standard Load Balancer med zonredundant frontend IP-adress. Den här konfigurationen för belastningsutjämnaren hjälper dig för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. |
|[Belastningsutjämna virtuella datorer i en specifik tillgänglighetszon](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Skapar tre virtuella datorer, en Standard Load Balancer med zonindelad frontend-IP som hjälper dig att justera datasökväg och resurser i en enskild zon för en viss region.|
| [Balansera belasntning för flera webbplatser på virtuella datorer](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Skapar två virtuella datorer med flera IP-konfigurationer, ansluten till en Azure-Tillgänglighetsuppsättning är tillgängliga via en Azure Load Balancer. |
| | |

