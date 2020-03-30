---
title: Vanliga frågor och svar om Azure Resource Health
description: Översikt över Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 5a15ded1c279e700fb71e5ea7aa866e5afc29f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912774"
---
# <a name="azure-resource-health-faq"></a>Vanliga frågor och svar om Azure Resource Health
Lär dig svaren på vanliga frågor om Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Vad är Azure Resource Health?
Resource Health hjälper dig att diagnostisera och få support när ett problem med Azure påverkar dina resurser. Det informerar dig om det aktuella och tidigare hälsotillståndet för dina resurser och hjälper dig att åtgärda problem. Resource Health ger teknisk support när du behöver hjälp med problem med Azure-tjänster.  

## <a name="what-is-the-resource-health-intended-for"></a>Vad är resurshälsan avsett för?
När ett problem med en resurs har upptäckts kan Resource Health hjälpa dig att diagnostisera grundorsaken. Det ger hjälp för att minska problemet och teknisk support när du behöver mer hjälp med Azure-tjänstproblem.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Vilka hälsokontroller utförs av Resource Health?
Resurshälsa utför olika kontroller baserat på [resurstypen](resource-health-checks-resource-types.md). Dessa kontroller är utformade för att genomföra tre typer av problem: 
- Oplanerade händelser, till exempel en oväntad värdomstart
- Planerade händelser, till exempel schemalagda uppdateringar av värdoperativsystem
- Händelser som utlöses av användaråtgärder, till exempel en användare som startar om en virtuell dator

## <a name="what-does-each-of-the-health-status-mean"></a>Vad betyder var och en av hälsotillståndet?
Det finns tre olika hälsotillstånd:
- Tillgängligt: Det finns inga kända problem i Azure-plattformen som kan påverka den här resursen
- Ej tillgänglig: Resurshälsan har upptäckt problem som påverkar resursen
- Okänd: Resurshälsa kan inte avgöra hälsotillståndet för en resurs eftersom den har slutat ta emot information om den. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Vad betyder den okända statusen? Är det något fel på min resurs?
Hälsostatusen är okänd när Resurshälsa slutar ta emot information om en viss resurs. Även om den här statusen inte är en slutgiltig indikation på resursens tillstånd, kan det tyda på att det finns ett Azure-problem.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hur kan jag få hjälp med en resurs som inte är tillgänglig?
Du kan skicka en supportbegäran från bladet Resurshälsa. Du behöver inget supportavtal med Microsoft för att öppna en begäran när resursen inte är tillgänglig eftersom plattformshändelser.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Har Resource Health skilja mellan otillgänglighet fall av plattformsproblem kontra något jag gjorde?
Ja, när en resurs inte är tillgänglig identifierar Resource Health grundorsaken i någon av dessa kategorier: 
-   Åtgärden initierad användare
-   Planerad händelse 
-   Oplanerad händelse

I portalen visas användarinitierade åtgärder med hjälp av en blå meddelandeikon, medan planerade och oplanerade händelser visas med hjälp av en röd varningsikon. Mer information finns i [översikten Resurshälsa](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Kan jag integrera Resurshälsa med mina övervakningsverktyg?
Resurshälsa har [förhandsgranskningsstöd](resource-health-alert-arm-template-guide.md) för aktivitetsloggbaserade aviseringar. Aktivitetsloggaviseringar använder [åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) för att meddela användarna att en avisering har utlösts. Åtgärdsgrupper stöder en mängd olika meddelandekanaler som e-post, SMS, webhook och ITSM-åtgärder.

## <a name="where-do-i-find-resource-health"></a>Var hittar jag Resurshälsa?
När du har loggat in på Azure-portalen finns det flera sätt att komma åt Resurshälsa:
- Navigera till din resurs. I den vänstra navigeringen väljer du **Resurshälsa**
- Gå till Azure Service Health-bladet.  I den vänstra navigeringen väljer du **Resurshälsa**.
- Öppna **bladet Hjälp + Support** genom att välja frågetecknet i det övre högra hörnet på portalen och sedan välja **Hjälp + Support**. När bladet öppnas väljer du **Resurshälsa**

Du kan också använda API:et för resurshälsa för att få information om hälsotillståndet för dina resurser.

## <a name="is-resource-health-available-for-all-resource-types"></a>Är Resurshälsa tillgänglig för alla resurstyper?
Listan över hälsokontroller och resurstyper som stöds via Resource Health finns [här](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Vad ska jag göra om min resurs visar tillgänglig men jag tror att det inte är?"
När du kontrollerar hälsotillståndet för en resurs, precis under hälsostatus kan du klicka på **Rapportera felaktig hälsostatus**. Innan du skickar rapporten kan du ange ytterligare information om varför du anser att den aktuella hälsostatusen är felaktig.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Är Resurshälsa tillgängligt för alla Azure-regioner? 
Resurshälsa är tillgängligt i alla Azure-geos.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Hur skiljer sig Resurshälsa från Azure-status eller instrumentpanelen för tjänsthälsa?
Informationen som tillhandahålls av Resource Health är mer specifik än vad som tillhandahålls av Azure-status eller instrumentpanelen för tjänsthälsa.

Medan [Azure-status](https://status.azure.com) och instrumentpanelen Service Health informerar dig om serviceproblem som påverkar en bred uppsättning kunder (till exempel en Azure-region), visar Resource Health mer detaljerade händelser som endast är relevanta för den specifika resursen. Om till exempel en värd oväntat startas om, varnar Resource Health endast de kunder vars virtuella datorer kördes på den värden.

Det är viktigt att notera att för att ge dig fullständig synlighet för händelser som påverkar dina resurser, Resurshälsa också ytor händelser som publicerats i Service Health instrumentpanelen.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Måste jag aktivera Resurshälsan för varje resurs?
Nej, hälsoinformation är tillgänglig för alla resurstyper som är tillgängliga via Resurshälsa. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Behöver vi aktivera Resurshälsa för min organisation?
Nej.  Azure Resource Health är tillgängligt i Azure-portalen utan några inställningskrav.

## <a name="is-resource-health-available-free-of-charge"></a>Är Resource Health tillgängligt kostnadsfritt?
Ja.  Azure Resource Health är kostnadsfritt.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Vilka rekommendationer ger Resource Health?
Baserat på hälsotillståndet ger Resource Health dig rekommendationer med målet att minska den tid du har felsökt. För tillgängliga resurser fokuserar rekommendationerna på hur man löser de vanligaste problemen som kunderna stöter på. Om resursen inte är tillgänglig på grund av en oplanerad Azure-händelse kommer fokus att ligga på att hjälpa dig under och efter återställningsprocessen. 

## <a name="next-steps"></a>Nästa steg

Läs mer om Resurshälsa:
-  [Hälsoöversikt över Azure Resource](Resource-health-overview.md)
-  [Resurstyper och hälsokontroller är tillgängliga genom Azure Resource Health](resource-health-checks-resource-types.md)
