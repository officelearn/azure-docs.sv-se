---
title: Haveri beredskap för Azure Data Share
description: Haveri beredskap för Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 12/18/2019
ms.openlocfilehash: 8c53450094801825b86b74505fbe34dc8defe4f6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86110973"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Haveri beredskap för Azure Data Share

I den här artikeln går vi igenom hur du konfigurerar en katastrof återställnings miljö för Azure Data Share. Avbrott i Azure Data Center är sällsynt, men kan gå från några minuter till timmar. Avbrott i data centret kan orsaka avbrott i miljöer som förlitar sig på att data delas av data leverantören. Genom att följa stegen i den här artikeln kan data leverantörer fortsätta att dela data med sina data konsumenter i händelse av ett avbrott i data centret för den primära region som är värd för din data resurs. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Uppnå affärs kontinuitet för Azure Data Share

För att förbereda för ett avbrott i data centret kan dataprovidern ha en data resurs miljö etablerad i en sekundär region. Det finns mått som kan vidtas som säkerställer en smidig redundansväxling i händelse av att ett avbrott i data centret inträffar. 

Data leverantörer kan etablera sekundära Azure-dataresurser i en annan region. Dessa data resurs resurser kan konfigureras för att inkludera data uppsättningar som finns i den primära data resurs miljön. Data konsumenter kan läggas till i data resursen när du konfigurerar DR-miljön eller läggs till i en senare tidpunkt (dvs. som en del av stegen för manuell redundans).

Om data konsumenter har en aktiv resurs prenumeration i en sekundär miljö som har skapats för DR, kan de aktivera ögonblicks bilds schemat som en del av en redundansväxling. Om data konsumenter inte vill prenumerera på en sekundär region i DR-syfte, kan de bjudas in till den sekundära data resursen vid ett senare tillfälle. 

Data konsumenter kan antingen ha en aktiv resurs prenumeration som är inaktiv i DR-syfte, eller så kan data leverantörer lägga till dem i en senare tidpunkt som en del av manuella redundans-procedurer. 

## <a name="related-information"></a>Relaterad information

- [Verksamhets kontinuitet och haveri beredskap](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [Bygg in hög tillgänglighet i din BCDR-strategi](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .




