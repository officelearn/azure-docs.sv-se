---
title: ta med fil
description: ta med fil
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123049"
---
Azure Compute ger VM-storlekar som isoleras till en specifik maskinvarutyp och dedikerad till en enda kund.  Dessa VM-storlekar passar bäst för arbetsbelastningar som kräver en hög grad av isolering från andra kunder för arbetsbelastningar som innehåller element som efterlevnad och regelkrav.  Kunder kan också välja att ytterligare dela upp resurserna i de isolerade virtuella datorerna med hjälp av [Azure-stöd för kapslade virtuella datorer](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Användning av en isolerad storlek garanterar att den virtuella datorn är den enda som körs på den aktuella Server instansen.  De aktuella isolerade virtuella dator erbjudandena är:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Du kan lära dig mer om var och en av de tillgängliga isolerade storlekarna [här](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).