---
title: Förbättra tillgängligheten för ditt program med Azure Advisor | Microsoft Docs
description: Använda Azure Advisor för att förbättra tillgängligheten för dina Azure-distributioner.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 42627649145b568b2b25411d182e5a36cdb025b0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881196"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Förbättra tillgängligheten för ditt program med Azure Advisor

Azure Advisor hjälper dig att kontrollera och förbättra affärskontinuitet för dina verksamhetskritiska program. Du kan få rekommendationer för hög tillgänglighet av Advisor från den **hög tillgänglighet** fliken Advisor-instrumentpanelen.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Att skapa feltolerans för virtuell dator

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar virtuella datorer som inte är en del av en tillgänglighetsuppsättning och rekommenderar att flytta dem till en tillgänglighetsuppsättning. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse är tillgänglig och uppfyller serviceavtalet för Azure-dator. Du kan välja att skapa en tillgänglighetsuppsättning för den virtuella datorn eller lägga till den virtuella datorn till en befintlig tillgänglighetsuppsättning.

> [!NOTE]
> Om du vill skapa en tillgänglighetsuppsättning måste du lägga till minst en virtuell dator till den. Vi rekommenderar att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning som angetts för att se till att minst en dator är tillgänglig under ett avbrott.

## <a name="ensure-availability-set-fault-tolerance"></a>Se till att tillgänglighetsuppsättningen feltolerans

För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Advisor identifierar tillgänglighetsuppsättningar som innehåller en enda virtuell dator och att du lägger till en eller flera virtuella datorer till den. Den här konfigurationen garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse är tillgänglig och uppfyller serviceavtalet för Azure-dator. Du kan välja att skapa en virtuell dator eller lägga till en befintlig virtuell dator i tillgänglighetsuppsättningen.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Använd Managed Disks för ökad datatillförlitlighet

Virtuella datorer som finns i en tillgänglighetsuppsättning med diskar som delar antingen lagringskonton eller lagringsskalningsenheter kan inte återhämta sig till enkel lagringsskalningsenhetsfel under avbrott. Advisor ska identifiera dessa tillgänglighetsuppsättningar och rekommenderar att du migrerar till Azure Managed Disks. Se till att diskar på olika virtuella datorer i tillgänglighetsuppsättningen är tillräckligt isolerade för att undvika en enskild felpunkt. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Att skapa feltolerans för application gateway

Denna rekommendation ger kontinuitet för verksamhetskritiska program som drivs av programgatewayer. Advisor identifierar application gateway-instanser som inte är konfigurerade för feltolerans och den föreslår åtgärder som du kan vidta. Advisor identifierar medelstora eller stora enkelinstansprogram gatewayer och den rekommenderar att lägga till minst en mer instans. Den identifierar en eller flera instance små programgatewayer och rekommenderar att du migrerar till medelstora eller stora SKU: er. Advisor rekommenderar dessa åtgärder för att kontrollera att din application gateway-instanser är konfigurerade för att uppfylla de aktuella SLA-krav för dessa resurser.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Skydda dina data för virtuella datorer tas bort av misstag

Konfigurera säkerhetskopiering av virtuella datorer garanterar tillgängligheten för dina verksamhetskritiska data och ger skydd mot oavsiktlig borttagning eller skadade data. Advisor identifierar virtuella datorer där säkerhetskopiering inte är aktiverat, och den rekommenderar att aktivera säkerhetskopiering. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Kontrollera att du har åtkomst till Azure-molnexperter när du behöver det.

När du kör en verksamhetskritiska arbetsbelastning, är det viktigt att ha tillgång till teknisk support när det behövs. Advisor identifierar potentiella affärskritisk prenumerationer som inte har teknisk support ingår i deras supportavtal och rekommenderar att du uppgraderar till en alternativ som omfattar teknisk support.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Skapa Azure Service Health-aviseringar som ska meddelas när Azure problem påverkar dig

Vi rekommenderar att du konfigurerar Azure Service Health-aviseringar kan meddelas när du påverkas av problem i Azure-tjänsten. [Azure Service Health](https://azure.microsoft.com/features/service-health/) är en kostnadsfri tjänst som ger anpassad vägledning och support när du påverkas av ett problem med Azure-tjänsten. Advisor identifierar prenumerationer som inte har aviseringar har konfigurerats och rekommenderar att skapa en.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Konfigurera Traffic Manager-slutpunkter för återhämtning

Traffic Manager-profiler med mer än en slutpunkt uppleva högre tillgänglighet om alla angivna slutpunkten misslyckas. Placera slutpunkter i olika regioner ytterligare förbättrar tjänstpålitligheten. Advisor identifierar Traffic Manager-profiler där det finns endast en slutpunkt och rekommenderar att lägga till minst en mer slutpunkt i en annan region.

Om alla slutpunkter i en Traffic Manager-profil som är konfigurerad för närhet routning finns i samma region, avbrott användare från andra regioner i anslutningen. Att lägga till eller flytta en slutpunkt till en annan region förbättra prestandan och få bättre tillgänglighet om inte alla slutpunkter i en region. Advisor identifierar Traffic Manager-profiler som konfigurerats för närhet routning där alla slutpunkterna är i samma region. Den rekommenderar att lägga till eller flytta en slutpunkt till en annan Azure-region.

Om en Traffic Manager-profil har konfigurerats för geografisk routning för dirigeras trafiken till slutpunkterna baserat på definierade regioner. Om det inte går att en region, finns det inga fördefinierade redundans. Med en slutpunkt där Regional gruppering konfigureras att ”alla (världen)” undvika trafik som förloras och förbättra tjänstens tillgänglighet. Advisor identifierar Traffic Manager-profiler som konfigurerats för geografisk routning för där det finns ingen slutpunkt som konfigurerats för att ha Regional gruppering som ”alla (världen)”. Den rekommenderar att du ändrar konfigurationen för att göra en slutpunkt ”alla (världen).

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Använd mjuk borttagning på ditt Azure Storage-konto för att spara och återställa data efter oavsiktlig överskrivning eller tas bort

Aktivera [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) på ditt lagringskonto så att ta bort blobbar övergången till ett ej permanent Borttaget tillstånd i stället för att permanent ta bort. När data skrivs över, genereras en ej permanent borttagen ögonblicksbild för att spara tillståndet för över data. Använda mjuk borttagning kan du återställa om det finns oavsiktliga borttagningar eller skriver över. Advisor identifierar Azure Storage-konton som inte har aktivera mjuk borttagning och föreslår att du aktiverar den.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurera din VPN-gateway till aktiv-aktiv för anslutningsåterhämtning

I aktiv-aktiv konfiguration, kommer båda instanserna av en VPN-gateway upprätta S2S VPN-tunnlar till din lokala VPN-enhet. När ett planerat underhåll eller en oplanerad händelse inträffar för en gatewayinstans växlar trafiken till den andra aktiva IPsec-tunneln automatiskt. Azure Advisor ska identifiera VPN-gatewayer som inte har konfigurerats som aktiv-aktiv och föreslår att du konfigurerar dem för hög tillgänglighet.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Hur du kommer åt rekommendationer för hög tillgänglighet i Advisor

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna sedan [Advisor](https://aka.ms/azureadvisordashboard).

2.  På Advisor-instrumentpanelen klickar du på den **hög tillgänglighet** fliken.

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Advisor kostnadsrekommendationer](advisor-cost-recommendations.md)
* [Advisor-rekommendationer](advisor-performance-recommendations.md)
* [Advisor säkerhetsrekommendationer](advisor-security-recommendations.md)

