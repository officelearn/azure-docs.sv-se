---
title: "Azure Behållarinstanser region och resurs tillgänglighet | Azure-dokument"
description: "Identifiera vilka Azure-regioner stöd för distribution av behållarinstanser och gränser CPU och minne för dessa instanser."
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
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Regional tillgänglighet för Azure-Behållarinstanser

Under förhandsgranskning är Azure Behållarinstanser tillgängliga i följande regioner med angivna CPU och minne.

| Plats | Operativsystem | Processor | Minne (GB) |
| -------- | -- | :---: | :-----------: |
| Västra Europa, västra USA, östra USA | Linux | 2 | 7 |
| Västra Europa, västra USA, östra USA | Windows | 2 | 3.5 |

## <a name="resource-availability"></a>Resurstillgänglighet

Behållarinstanser som skapats inom gränserna för dessa regleras av tillgänglighet för regionen distribution. När en region är hårt belastad kan uppstå det ett fel när du distribuerar instanser.

Försök att distribuera instanser med lägre CPU och minnesinställningar för att åtgärda en distributionsfel, eller försök distributionen vid ett senare tillfälle.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du felsöker behållaren instans distribution finns [felsöka distributionsproblem med Azure Container instanser](container-instances-troubleshooting.md).