---
title: "Azure hög tillgänglighet för Advisor-rekommendationer | Microsoft Docs"
description: "Använda Azure Advisor för att förbättra tillgängligheten för din Azure-distributioner."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: e1cd7948e1969cd4ddb926e428c09b559190a805
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="advisor-high-availability-recommendations"></a>Advisor-rekommendationer för hög tillgänglighet

Azure Advisor hjälper dig att kontrollera och förbättra kontinuiteten i dina verksamhetskritiska program. Du kan få rekommendationer för hög tillgänglighet av Advisor från den **hög tillgänglighet** på Advisor-instrumentpanelen.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Se till att virtuella feltolerans

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar virtuella datorer som inte är en del av en tillgänglighetsuppsättning och rekommenderar flytta dem till en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse, är tillgänglig och uppfyller SLA för Azure-dator. Du kan välja att skapa en tillgänglighetsuppsättning för den virtuella datorn eller lägga till den virtuella datorn till en befintlig tillgänglighetsuppsättning.

> [!NOTE]
> Om du väljer att skapa en tillgänglighetsuppsättning måste du lägga till minst en virtuell dator för mer till den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighetsgrupp anger att se till att minst en dator som är tillgänglig under ett avbrott.

## <a name="ensure-availability-set-fault-tolerance"></a>Kontrollera tillgänglighet feltolerans 

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar tillgänglighetsuppsättningar som innehåller en enda virtuell dator och rekommenderas att lägga till en eller flera virtuella datorer. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse, är tillgänglig och uppfyller SLA för Azure-dator. Du kan välja att skapa en virtuell dator eller lägga till en befintlig virtuell dator i tillgänglighetsuppsättningen.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Se till att programmet gateway feltolerans
För att garantera kontinuitet för verksamhetskritiska program som tillhandahålls av programgatewayer, Advisor identifierar programmet gateway-instanser som inte är konfigurerade för feltolerans och åtgärder som du kan vidta föreslås. Advisor identifierar medelstora eller stora enkelinstansprogram gateways och rekommenderar att lägga till minst en mer instansen. Den identifierar en eller flera instance små programgatewayer och rekommenderar att du migrerar till medelstora eller stora SKU: er. Advisor rekommenderar dessa åtgärder för att se till att din gateway programinstanser konfigureras för att uppfylla de SLA krav som ställs för dessa resurser.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Förbättra prestanda och tillförlitlighet för virtuella diskar

Advisor identifierar virtuella datorer med standarddiskar och rekommenderar att du uppgraderar till premiumdiskar.
 
Azure Premium Storage ger stöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Virtuella diskar som använder premiumlagringskonton data som lagras på SSD-enheter (SSD). För bästa prestanda för ditt program rekommenderar vi att du migrerar alla virtuella diskar som kräver hög IOPS till premium-lagring. 

Om diskarna inte behöver höga IOPS, kan du begränsa kostnader genom att hålla dem i standardlagring. Standardlagring lagrar data för virtuell disk på hårddiskar (HDD) i stället för SSD-enheter. Du kan välja att migrera dina virtuella datordiskar till premiumdiskar. Premiumdiskar stöds i de flesta virtuella SKU: er. Dock i vissa fall kan behöva om du vill använda premiumdiskar kan du uppgradera den virtuella datorn SKU: er samt.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda dina data för virtuell dator tas bort av misstag
Konfigurera säkerhetskopiering av virtuella datorer garanterar tillgängligheten för affärskritiska data och ger skydd mot oavsiktlig borttagning eller skadade data.  Advisor identifierar virtuella datorer där säkerhetskopiering inte har aktiverats och rekommenderar att aktivera säkerhetskopiering. 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Hur du kommer åt rekommendationer för hög tillgänglighet i Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och sedan öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  Advisor-instrumentpanelen, klicka på den **hög tillgänglighet** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnad Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)

