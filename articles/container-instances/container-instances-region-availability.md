---
title: "Region- och resurstillgänglighet för Azure Container Instances | Azure Docs"
description: "Ta reda på vilka Azure-regioner som har stöd för distribution av behållarinstanser, samt processor- och minnesgränser för dessa instanser."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Regional tillgänglighet för Azure Container Instances

I förhandsversionen är Azure Container Instances tillgängligt i följande regioner med angivna processor- och minnesgränser.

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Europa, västra; USA, västra; USA, östra | Linux | 2 | 7 |
| Europa, västra; USA, västra; USA, östra | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>Resurstillgänglighet

Behållarinstanser som har skapats inom dessa resursgränser finns i mån av tillgång i distributionsregionen. Om en region har hög belastning kan du uppleva fel vid distribution av instanser.

Du kan försöka lindra sådana distributionsfel genom att prova att distribuera instanser med lägre processor- och minnesinställningar. Du kan även prova att genomföra distributionen senare.

## <a name="next-steps"></a>Nästa steg

Mer information om att felsöka distribution av behållarinstanser finns i [Troubleshoot deployment issues with Azure Container Instances](container-instances-troubleshooting.md) (Felsöka distributionsproblem med Azure Container Instances).