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
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Advisor-rekommendationer för hög tillgänglighet

Azure Advisor hjälper dig att kontrollera och förbättra kontinuiteten i dina verksamhetskritiska program. Du kan få rekommendationer för hög tillgänglighet av Advisor från den **hög tillgänglighet** på Advisor-instrumentpanelen.

![Hög tillgänglighet-knappen på Advisor-instrumentpanelen](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>Se till att virtuella feltolerans

Advisor identifierar virtuella datorer som inte är en del av en tillgänglighetsuppsättning och rekommenderar flytta dem till en tillgänglighetsuppsättning. För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse, är tillgänglig och uppfyller SLA för Azure-dator. Du kan välja att skapa en tillgänglighetsuppsättning för den virtuella datorn eller lägga till den virtuella datorn i en befintlig tillgänglighetsuppsättning.

> [!NOTE]
> Om du väljer att skapa en tillgänglighetsuppsättning måste du lägga till minst en virtuell dator för mer till den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighetsgrupp anger att se till att minst en dator som är tillgänglig under ett avbrott.

![Advisor-rekommendationer: Använd tillgänglighetsuppsättningar för redundans för virtuell dator,](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>Kontrollera tillgänglighet feltolerans 

Advisor identifierar tillgänglighetsuppsättningar som innehåller en enda virtuell dator och rekommenderas att lägga till en eller flera virtuella datorer. För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse, är tillgänglig och uppfyller SLA för Azure-dator. Du kan välja antingen skapa en virtuell dator eller att använda en befintlig virtuell dator och lägga till den i tillgänglighetsuppsättningen.  

![Advisor-rekommendationer: Lägg till en eller flera virtuella datorer till den här tillgänglighetsuppsättningen](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>Se till att programmet gateway feltolerans
För att garantera kontinuitet för verksamhetskritiska program som tillhandahålls av programgatewayer, Advisor identifierar programmet gateway-instanser som inte är konfigurerade för feltolerans och åtgärder som du kan vidta föreslås. Advisor identifierar medelstora eller stora enkelinstansprogram gateways och rekommenderar att lägga till minst en mer instansen. Den identifierar en eller flera instance små programgatewayer och rekommenderar att du migrerar till medelstora eller stora SKU: er. Advisor rekommenderar dessa åtgärder för att se till att din gateway programinstanser konfigureras för att uppfylla de SLA krav som ställs för dessa resurser.

![Advisor-rekommendationer: distribuera minst två medelstora eller stora storlek gateway programinstanser](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Förbättra prestanda och tillförlitlighet för virtuella diskar

Advisor identifierar virtuella datorer med standarddiskar och rekommenderar att du uppgraderar till premiumdiskar.
 
Azure Premium Storage ger stöd för virtuella datorer som körs I/O-intensiva arbetsbelastningar diskar med hög prestanda, låg latens. Virtuella diskar som använder premiumlagringskonton data som lagras på SSD-enheter (SSD). För bästa prestanda för ditt program rekommenderar vi att du migrerar alla virtuella diskar som kräver hög IOPS till premium-lagring. 

Om diskarna inte behöver höga IOPS, kan du begränsa kostnader genom att hålla dem i standardlagring. Standardlagring lagrar data för virtuell disk på hårddiskar (HDD) i stället för SSD-enheter. Du kan välja att migrera dina virtuella datordiskar till premiumdiskar. Premiumdiskar stöds i de flesta virtuella SKU: er. Dock i vissa fall kan behöva om du vill använda premiumdiskar kan du uppgradera den virtuella datorn SKU: er samt.

![Advisor-rekommendationer: förbättra tillförlitligheten för virtuella diskar genom att uppgradera till premiumdiskar](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda dina data för virtuell dator tas bort av misstag
Advisor identifierar virtuella datorer där säkerhetskopiering inte har aktiverats och rekommenderar att aktivera säkerhetskopiering. Konfigurera säkerhetskopiering av virtuella datorer garanterar tillgängligheten för affärskritiska data och ger skydd mot oavsiktlig borttagning eller skadade data.

![Advisor-rekommendationer: Konfigurera säkerhetskopiering av virtuella datorer för att skydda dina kritiska data](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Rekommendationer för åtkomst med hög tillgänglighet i Advisor

1. Logga in på [Azure Portal](https://portal.azure.com).

2. I den vänstra rutan klickar du på **fler tjänster**.

3. I fönstret service menyn under **övervakning och hantering av**, klickar du på **Azure Advisor**.  
 Advisor-instrumentpanelen visas.

4. Advisor-instrumentpanelen, klicka på den **hög tillgänglighet** och välj den prenumeration som du vill få rekommendationer.

> [!NOTE]
> Om du vill komma åt Advisor-rekommendationer, måste du först *registrera prenumerationen* med Advisor. En prenumeration registreras när en *prenumeration ägare* startar Advisor instrumentpanelen och klickar på den **få rekommendationer** knappen. Det här är en *engångsåtgärd*. När prenumerationen har registrerats kan du komma åt Advisor-rekommendationer som *ägare*, *deltagare*, eller *Reader* för en prenumeration, resursgrupp eller en viss resurs.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnad Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)

