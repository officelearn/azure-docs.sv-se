---
title: Azure Resource Health vanliga frågor och svar | Microsoft Docs
description: Översikt över Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.workload: Supportability
ms.openlocfilehash: b4062b3f0bc389de4403ac81b56688508f5ea50e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620835"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health vanliga frågor och svar
Lär dig svar på vanliga frågor om Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Vad är Azure Resource Health?
Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.  

## <a name="what-is-the-resource-health-intended-for"></a>Vad är Resource Health avsett för?
När ett problem med en resurs har identifierats, Resource Health kan hjälpa dig att diagnostisera orsaken. Den innehåller hjälp om du vill åtgärda problemet och teknisk support när du behöver mer hjälp med Azure-tjänstproblem.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Vilka hälsokontroller utförs av Resource Health?
Resurshälsa utför olika kontroller baserat på den [resurstyp](resource-health-checks-resource-types.md). De här kontrollerna är utformade för att implementera tre typer av problem: 
- Oplanerade händelser, till exempel en ovänted omstart
- Planerade händelser som schemalagda värdoperativsystemet uppdateringar
- Händelser som utlöses av användaråtgärder, till exempel en användare startar om en virtuell dator

## <a name="what-does-each-of-the-health-status-mean"></a>Vad betyder var och en av hälsostatus?
Det finns tre olika hälsotillstånd statusar:
- Tillgängliga: Det inte finns några kända problem i Azure-plattformen som kan påverka den här resursen
- Inte tillgänglig: Resource health har identifierat problem som påverkar resursen
- Okänt: Resource health kan inte fastställa hälsotillståndet för en resurs eftersom den är stoppad ta emot information om den. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Vad betyder okänd status? Är något fel med min resurs?
Hälsostatus anges till okänd när Resource Health slutar ta emot information om en viss resurs. Även om denna status inte är en slutgiltig indikation på tillståndet för resursen, i fall där du får problem, betyda det att det finns ett Azure-problem.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hur kan jag få hjälp för en resurs som inte är tillgängligt?
Du kan skicka en supportförfrågan via Resource Health-bladet. Du behöver inte ett supportavtal med Microsoft för att öppna en begäran när resursen är inte tillgänglig eftersom plattformshändelser.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Resource Health skilja mellan otillgänglighet bokstäver av plattformsproblem som jämfört med något jag gjorde?
Ja, identifierar Resource Health rotorsaken inom någon av dessa kategorier när en resurs inte är tillgängligt: 
-   Användarinitierade åtgärder
-   Planerade driftstoppet 
-   Oplanerad händelse

I portalen visas användarinitierade åtgärder med hjälp av en blå meddelandeikon när planerade och oplanerade händelser visas med en röd varningsikon. Mer information finns i den [Resource Health översikt](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kan jag integrera Resurshälsa med min övervakningsverktyg?
Resource health har [Förhandsgranska support](resource-health-alert-arm-template-guide.md) för aktivitetsloggen efter aviseringar. Aktivitetsloggen aviseringar Använd [åtgärdsgrupper](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups) att meddela användare att en avisering har utlösts. Åtgärdsgrupper stöder ett antal aviseringskanaler, till exempel e-post, SMS, webhook och ITSM-åtgärder.

## <a name="where-do-i-find-resource-health"></a>Var hittar jag Resource Health?
När du loggar in på Azure Portal, finns det flera sätt att komma åt Resource Health:
- Gå till din resurs. I det vänstra navigeringsfältet väljer **Resource health**
- Gå till Azure Service Health-bladet.  I det vänstra navigeringsfältet väljer **resurshälsa**.
- Öppna den **hjälp + Support** bladet genom att välja frågetecknet i det övre högra hörnet i portalen och sedan välja **hjälp + Support**. När bladet öppnas, Välj **Resource health**

Du kan också använda Resource Health-API: et för att hämta information om hälsotillståndet för dina resurser.

## <a name="is-resource-health-available-for-all-resource-types"></a>Är Resource Health ska vara tillgängliga för alla typer av resurser?
Listan över hälsokontroller och resurstyper som stöds via Resource Health finns [här](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Vad gör jag om min resurs visas tillgängliga men jag tror att det inte är ”?
När du kontrollerar hälsotillståndet för en resurs, direkt under hälsostatus du kan klicka på **Rapportera felaktig hälsostatus**. Innan du skickar in rapporten, har du möjlighet att med ytterligare information om varför du tycker att nuvarande hälsostatus är felaktig.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Är Resource Health tillgänglig för alla Azure-regioner? 
Resource health är tillgängligt i alla Azure-regioner.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Hur skiljer sig Resource Health från Azure-status eller Service Health-instrumentpanelen?
Den information som tillhandahålls av Resource Health är mer specifik än vad som tillhandahålls av Azure-status eller Service Health-instrumentpanelen.

Medan [Azure-status](https://status.azure.com) och Service Health-instrumentpanelen ger information om tjänstproblem som påverkar en rad olika kunder (till exempel en Azure-region), Resource Health visar mer detaljerad händelser som endast är relevanta för den specifik resurs. Om en värd startar om oväntat, varnar Resource Health kunder vars virtuella datorer som kördes på värden.

Det är viktigt att Observera att för att ge dig en fullständig överblick över händelser som påverkar dina resurser, Resource Health hämtar också händelser som publicerats på Service Health-instrumentpanelen.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Behöver jag aktivera Resource Health för varje resurs?
Nej, hälsoinformation är tillgängliga för alla resurstyper som är tillgängliga via Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Behöver vi aktivera Resource Health för min organisation?
Nej.  Azure Resource Health är tillgängligt i Azure-portalen utan att alla krav för nätverksinstallation.

## <a name="is-resource-health-available-free-of-charge"></a>Är Resource Health tillgänglig kostnadsfritt?
Ja.  Azure Resource Health är kostnadsfritt.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Vilka är de rekommendationer som tillhandahåller Resource Health?
Utifrån hälsostatus ger Resource Health dig rekommendationer med målet att minska tid som du har använt för felsökning. Fokus rekommendationer om hur du kan lösa de vanligaste problem som kunderna får för tillgängliga resurser. Om resursen är otillgänglig på grund av en Azure oplanerad händelse är fokus på hjälper dig under och efter återställningen. 

## <a name="next-steps"></a>Nästa steg

Läs mer om Resource Health:
-  [Översikt över Azure Resource Health](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)
