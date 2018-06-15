---
title: Förklarar beräkna enheter i Azure-databas för PostgreSQL | Microsoft Docs
description: 'Azure DB för PostgreSQL: den här artikeln förklarar begreppet Compute enheter och vad som händer när din arbetsbelastning når det maximala antalet Compute-enheter.'
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: dbb9f733455fa0492358b24b178c8c637ff08c71
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678422"
---
# <a name="explaining-compute-units-in-azure-database-for-postgresql"></a>Förklarar beräkning enheter i Azure-databas för PostgreSQL
Det här avsnittet beskriver begreppet Compute enheter och vad som händer när din arbetsbelastning når den maximala nivån för Compute-enheter.

## <a name="what-are-compute-units"></a>Vad är Compute enheter?
Beräkna enheter är ett mått på CPU bearbetning dataflödet som garanterat ska vara tillgängliga för en Azure-databas för PostgreSQL-servern. En Compute-enhet är en blandning av processor-och minnesresurser. I allmänhet del 50 Compute enheter hälften av en kärna. 100 enheter för compute del en kärna. 2000 compute enheter del 20 kärnor garanterad bearbetning genomströmning tillgängliga för servern.

Mängden minne per Compute-enhet är optimerad för Basic och Standard prisnivåer. Dubblerade Compute-enheter genom att öka prestandanivå är lika med dubblerade mängden CPU och minne för att den enda Azure-databasen för PostgreSQL.

Ett Standard 800 Compute enheter innehåller till exempel 8 x mer CPU genomflöde och minne än en 100 Compute standardenheter konfiguration. Medan 100 Compute standardenheter ge samma CPU-kapaciteten som grundläggande 100 Compute-enheter, är mängden minne som är förkonfigurerad i Standard prisnivån dubbla mängden minne som konfigurerats för den grundläggande prisnivån. Därför ger Standard prisnivån bättre arbetsbelastningsprestanda och transaktionen kortare svarstid än grundläggande prisnivån med samma Compute enheter markerad.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Hur tar jag reda på hur många Compute enheter som behövs för min arbetsbelastning?
Om du vill migrera en befintlig PostgreSQL-server som körs lokalt eller på en virtuell dator kan du bestämma antalet Compute enheter genom att uppskatta hur många kärnor bearbetning genomströmning din arbetsbelastning behöver. 

Om din befintliga lokala eller virtuella server använder för närvarande 4 kärnor (oräknade CPU hyperthread), börja med att konfigurera 400 Compute enheter för din Azure-databas för PostgreSQL-servern. Compute-enheter kan dynamiskt skalas upp eller ned beroende på dina behov för arbetsbelastning med nästan inga driftavbrott. 

Övervaka diagrammet mätvärden i Azure-portalen eller skriva Azure CLI-kommandona för att mäta Compute-enheter. Relevanta mått för att övervaka är Compute enhet procent och beräkna enhet gränsen.

>[!IMPORTANT]
> Om du hittar lagring IOPS inte är fullt utnyttjade maximalt bör du övervaka hur Compute-enheter. Höja Compute-enheter kan ha högre i/o-genomflöde av inskränkning flaskhalsar på grund av begränsad CPU eller minne.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Vad händer när jag träffar min max Compute-enheter?
Prestandanivåer kalibrera och regleras för att tillhandahålla resurser för att köra din databas arbetsbelastning upp till gränsvärden för den valda prisnivå tjänstnivå och prestandanivå nivån. 

Om din arbetsbelastning når de maximala gränserna i Compute enheter eller etablerade IOPS, du kan fortsätta att använda resurser på den högsta tillåtna nivå, men dina frågor är troligt att ökad latens. Dessa gränser kommer inte några fel, men i stället en nedgång i arbetsbelastningen, om inte minskningen blir så allvarliga som frågar timeout. 

Om din arbetsbelastning når de maximala gränsen för antalet anslutningar, aktiveras explicit fel. Mer information om resurser begränsar finns [begränsningar i Azure-databas för PostgreSQL](concepts-limits.md).

## <a name="next-steps"></a>Nästa steg
Mer information om prisnivåer finns [Azure-databas för PostgreSQL prisnivåer](./concepts-service-tiers.md).
