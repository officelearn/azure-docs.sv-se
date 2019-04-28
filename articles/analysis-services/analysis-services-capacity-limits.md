---
title: Azure Analysis Services-resursen och objektet gränser | Microsoft Docs
description: Beskriver begränsningar för Azure Analysis Services-resurs och objekt.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0ffbffc788baaffd4a0532c3918ed82cc3eaf5c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023565"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services-resurs och objekt gränser

Den här artikeln beskriver resource och modell objekt-gränser.

## <a name="tier-limits"></a>Gränserna för nivån

### <a name="developer-tier"></a>Developer-nivå

Den här nivån rekommenderas för utvärderings-, utvecklings- och testscenarier. En enskild plan inkluderar samma funktioner som på Standard-nivån, men funktionerna för processorkraft, QPU:er och minnesstorlek är begränsade. Frågeutskalning repliken är inte tillgängligt för den här nivån. Den här nivån omfattas inte av något serviceavtal.

|Planera  |QPU:er  |Minne (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Basic-nivå

Nivån rekommenderas för produktionslösningar med mindre tabellmodeller, begränsad användarsamtidighet och enkla krav för datauppdatering. Fråga replica skalbara *är inte tillgänglig* för den här nivån. Perspektiv, flera partitioner och DirectQuery tabellmodell funktioner stöds inte i den här nivån.  

|Planera  |QPU:er  |Minne (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Standard-nivå

Den här nivån passar bäst för verksamhetskritiska produktionsprogram som kräver elastisk användarsamtidighet och har snabbväxande datamodeller. Den har stöd för avancerad datauppdatering för datamodellsuppdateringar i nära realtid och stöder alla funktioner för tabellmodeller.

|Planera  |QPU:er  |Minne (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Inte tillgänglig i alla regioner.  

## <a name="object-limits"></a>Objektet gränser

Dessa gränser är teoretisk. Prestanda kommer följd vid låga tal.

|Object|Maximal storlek/siffror|  
|------------|----------------------------|  
|Databaser i en instans|16,000|  
|Kombinerade antalet tabeller och kolumner i en databas|16,000|  
|Rader i en tabell|Obegränsat<br /><br /> **Varning:** Med begränsningen att ingen enskild kolumn i tabellen kan ha fler än 1,999,999,997 distinkta värden.|  
|Hierarkier i en tabell|15,999|  
|Nivåer i en hierarki|15,999|  
|Relationer|8,000|  
|Nyckelkolumner i alla tabell|15,999|  
|Åtgärder i tabeller|2^31-1 = 2,147,483,647|  
|Celler som returneras av en fråga|2^31-1 = 2,147,483,647|  
|Registrera storleken på källfrågan|64 KB|  
|Längden på objektnamn|512 tecken|  


