---
title: Rekommendationer för Azure Advisor hög tillgänglighet | Microsoft Docs
description: Använda Azure Advisor för att förbättra tillgängligheten för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ea8d8d0adbc7cf5a8dfb6e9af51257b9d2ba8db2
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264090"
---
# <a name="advisor-high-availability-recommendations"></a>Advisor-rekommendationer för hög tillgänglighet

Azure Advisor hjälper dig att kontrollera och förbättra affärskontinuitet för dina verksamhetskritiska program. Du kan få rekommendationer för hög tillgänglighet av Advisor från den **hög tillgänglighet** fliken Advisor-instrumentpanelen.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Att skapa feltolerans för virtuell dator

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar virtuella datorer som inte är en del av en tillgänglighetsuppsättning och rekommenderar att flytta dem till en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse är tillgänglig och uppfyller serviceavtalet för Azure-dator. Du kan välja att skapa en tillgänglighetsuppsättning för den virtuella datorn eller lägga till den virtuella datorn till en befintlig tillgänglighetsuppsättning.

> [!NOTE]
> Om du vill skapa en tillgänglighetsuppsättning måste du lägga till minst en virtuell dator till den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning som angetts för att se till att minst en dator är tillgänglig under ett avbrott.

## <a name="ensure-availability-set-fault-tolerance"></a>Se till att tillgänglighetsuppsättningen feltolerans 

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar tillgänglighetsuppsättningar som innehåller en enda virtuell dator och att du lägger till en eller flera virtuella datorer till den. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse är tillgänglig och uppfyller serviceavtalet för Azure-dator. Du kan välja att skapa en virtuell dator eller lägga till en befintlig virtuell dator i tillgänglighetsuppsättningen.  

## <a name="ensure-application-gateway-fault-tolerance"></a>Att skapa feltolerans för application gateway
För att säkerställa kontinuitet för företag för verksamhetskritiska program som drivs av programgatewayer Advisor identifierar application gateway-instanser som inte är konfigurerade för feltolerans och den föreslår åtgärder som du kan vidta. Advisor identifierar medelstora eller stora enkelinstansprogram gatewayer och den rekommenderar att lägga till minst en mer instans. Den identifierar en eller flera instance små programgatewayer och rekommenderar att du migrerar till medelstora eller stora SKU: er. Advisor rekommenderar dessa åtgärder för att kontrollera att din application gateway-instanser är konfigurerade för att uppfylla de aktuella SLA-krav för dessa resurser.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>Förbättra säkerheten och tillförlitligheten för virtuella diskar

Advisor identifierar virtuella datorer med standarddiskar och rekommenderar att du uppgraderar till premium-diskar.
 
Azure Premium Storage tillhandahåller högpresterande och låg latens disksupport för virtuella datorer som kör I/O-intensiva arbetsbelastningar. Virtuella diskar som använder premium storage-konton kan du lagra data på SSD (solid-state drive). För bästa prestanda för ditt program rekommenderar vi att du migrerar alla virtuella diskar som kräver hög IOPS till premium storage. 

Om diskarna inte behöver hög IOPS, kan du begränsa kostnaderna genom att underhålla dem. i standard-lagring. Standard storage lagrar diskdata för virtuell dator på hårddiskar (HDD) i stället för SSD-enheter. Du kan välja att migrera dina virtuella datordiskar till premium-diskar. Premium-diskar stöds i de flesta VM SKU: er. Men i vissa fall kan behöva om du vill använda premium disks kan du uppgradera den virtuella datorn SKU: er samt.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda dina data för virtuella datorer tas bort av misstag

Konfigurera säkerhetskopiering av virtuella datorer garanterar tillgängligheten för dina verksamhetskritiska data och ger skydd mot oavsiktlig borttagning eller skadade data.  Advisor identifierar virtuella datorer där säkerhetskopiering inte är aktiverat, och den rekommenderar att aktivera säkerhetskopiering. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Kontrollera att du har åtkomst till Azure-molnexperter när du behöver det.

När du kör en verksamhetskritiska arbetsbelastning, är det viktigt att ha tillgång till teknisk support när det behövs. Advisor identifierar potentiella affärskritisk prenumerationer som inte har teknisk support ingår i deras supportavtal och rekommenderar att du uppgraderar till en alternativ som omfattar teknisk support.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Skapa Azure Service Health-aviseringar som ska meddelas när Azure problem påverkar dig

Vi rekommenderar att du konfigurerar Azure Service Health-aviseringar kan meddelas när du påverkas av problem i Azure-tjänsten. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnadsfri tjänst som ger anpassad vägledning och support när du påverkas av ett problem med Azure-tjänsten. Advisor identifierar prenumerationer som inte har aviseringar har konfigurerats och rekommenderar att skapa en.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurera Traffic Manager-slutpunkter för återhämtning

Traffic Manager-profiler med mer än en slutpunkt uppleva högre tillgänglighet om alla angivna slutpunkten misslyckas. Placera slutpunkter i olika regioner ytterligare förbättrar tjänstpålitligheten. Advisor identifierar Traffic Manager-profiler där det finns endast en slutpunkt och rekommenderar att lägga till minst en mer slutpunkt i en annan region.

Om alla slutpunkter i en Traffic Manager-profil som är konfigurerad för närhet routning finns i samma region, avbrott användare från andra regioner i anslutningen. Att lägga till eller flytta en slutpunkt till en annan region förbättra prestandan och få bättre tillgänglighet om inte alla slutpunkter i en region. Advisor identifierar Traffic Manager-profiler som konfigurerats för närhet routning där alla slutpunkterna är i samma region och rekommenderar att lägga till eller flytta en slutpunkt till en annan Azure-region.

Om en Traffic Manager-profil har konfigurerats för geografisk routning för dirigeras trafiken till slutpunkterna baserat på definierade regioner. Om det inte går att en region, finns det inga fördefinierade redundans. Med en slutpunkt där Regional gruppering konfigureras att ”alla (världen)” undvika trafik som förloras och förbättra tjänstens tillgänglighet. Advisor identifierar Traffic Manager-profiler som konfigurerats för geografisk routning där det finns ingen slutpunkt som konfigurerats för att ha Regional gruppering som ”alla (världen)” och rekommenderar att göra den konfigurationsändringen.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Ta bort datasnedställning på SQL data warehouse-tabell att öka prestanda för frågor

Datasnedställning kan orsaka onödiga data movement eller resurs flaskhalsar när du kör din arbetsbelastning. Advisor identifierar distributionsdata förskjuta större än 15% och rekommenderar att du distribuera om dina data och gå tillbaka till dina viktiga val för tabell-distribution. Läs mer om identifiera och ta bort skeva i [felsökning skeva](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Skapa eller uppdatera inaktuella tabellstatistik på SQL data warehouse-tabell att öka prestanda för frågor

Advisor identifierar tabeller som inte har uppdaterat [tabellstatistik](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) och rekommenderar att skapa eller uppdatera tabellstatistik. SQL data warehouse-fråga optimering använder uppdaterade statiska värden för att beräkna kardinalitet eller antalet rader i frågeresultatet som gör att frågeoptimerare att skapa en högkvalitativ frågeplan för bästa prestanda.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Hur du kommer åt rekommendationer för hög tillgänglighet i Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på den **hög tillgänglighet** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor kostnadsrekommendationer](advisor-performance-recommendations.md)
* [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)

