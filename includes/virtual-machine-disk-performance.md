---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664654"
---
Den här artikeln hjälper till att klargöra disk prestanda och hur det fungerar med när du kombinerar Azure Virtual Machines och Azure-diskar. Det beskriver också hur du kan diagnostisera Flask halsar för disk-i/o och de ändringar som du kan göra för att optimera prestanda.

## <a name="how-does-disk-performance-work"></a>Hur fungerar disk prestanda?
Virtuella Azure-datorer har prestanda gränser för IOPS och data flöden baserat på den virtuella datorns typ och storlek. OS-diskar och data diskar, som kan kopplas till virtuella datorer, har sina egna IOPs-och data flödes gränser. När ditt program som körs på dina virtuella datorer begär mer IOPS eller data flöde än vad som är tilldelat för den virtuella datorn eller de anslutna diskarna får ditt program prestanda ett tak. När detta inträffar kommer programmet att uppleva optimala prestanda och kan leda till vissa negativa följder som ökad latens. Vi går igenom ett par exempel för att stärka detta. För att göra dessa exempel lätta att följa, tittar vi bara på IOPs men samma logik gäller även för data flödet.

## <a name="disk-io-capping"></a>Disk-i/o-capping
Konfigurera:
- Standard_D8s_v3 
    - Ej cachelagrad IOPS: 12 800
- E30 OS-disk
    - IOPS: 500 
- 2 E30 data diskar
    - IOPS: 500

![Capping på disk nivå](media/vm-disk-performance/disk-level-throttling.jpg)

Programmet som körs på den virtuella datorn gör en begäran som kräver 10 000 IOPs på den virtuella datorn. Alla tillåts av den virtuella datorn eftersom den Standard_D8s_v3 virtuella datorn kan köra upp till 12 800 IOPs. Dessa 10 000 IOPs-begäranden delas sedan upp i tre olika begär anden till de olika diskarna. 1 000 IOPs begärs till operativ system disken och 4 500 IOPs begärs till varje data disk. Eftersom alla diskar som är anslutna är E30-diskar och bara kan hantera 500 IOPs, svarar de igen med 500 IOPs var. Programmets prestanda är sedan begränsade till de anslutna diskarna och kan bara bearbeta 1 500 IOPs. Det kan arbeta med högsta prestanda vid 10 000 IOPS om bättre diskar skulle användas, t. ex. Premium SSD P30-diskar.

## <a name="virtual-machine-io-capping"></a>IO-capping för virtuell dator
Konfigurera:
- Standard_D8s_v3 
    - Ej cachelagrad IOPS: 12 800
- P30 OS-disk
    - IOPS: 5 000 
- 2 P30 data diskar 
    - IOPS: 5 000

![Capping för virtuell dator nivå](media/vm-disk-performance/vm-level-throttling.jpg)

Programmet som körs på den virtuella datorn gör en begäran som kräver 15 000 IOPs. Tyvärr är den Standard_D8s_v3 virtuella datorn endast etablerad för att hantera 12 800 IOPs. I det här fallet är programmet begränsat till den virtuella datorns gränser och måste sedan allokera den 12 800 IOPs som tilldelats. De 12 800 IOPs-begär Anden delas sedan upp i tre olika begär anden till de olika diskarna. 4 267 IOPs begärs till operativ system disken och 4 266 IOPs begärs till varje data disk. Eftersom alla diskar som är anslutna är P30 diskar, som kan hantera 5 000 IOPs, svarar de med de begärda mängderna.