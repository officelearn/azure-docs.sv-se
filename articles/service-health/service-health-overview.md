---
title: Översikt över Service Health | Microsoft Docs
description: Anpassad information om hur dina Azure-appar som påverkas av problem med aktuella och kommande Azure-tjänsten och underhåll.
services: service-health
author: stephbaron
ms.author: stbaron
documentationcenter: service-health
ms.service: service-health
ms.topic: article
ms.workload: Supportability
ms.date: 03/27/2018
ms.openlocfilehash: d5385777134b911aa08b751978c44597f79acbc7
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442398"
---
# <a name="service-health"></a>Service Health
Service Health ger dig en anpassningsbar instrumentpanel som spårar hälsotillståndet för dina Azure-tjänster i regioner där du använder. I den här instrumentpanelen kan du spåra aktiva händelser som pågående tjänstproblem, planerat underhåll eller relevanta hälsoråd. När händelser blir inaktiva, placeras de i din hälsotillståndshistoriken i upp till 90 dagar. Slutligen kan du använda Service Health-instrumentpanelen att skapa och hantera service health-aviseringar som proaktivt meddela dig när tjänstproblem påverkar dig.

## <a name="service-health-events"></a>Service Health-händelser
Tjänstehälsa spårar tre typer av health-händelser som kan påverka dina resurser:
1. **Tjänsten problem** -problem i Azure-tjänster som påverkar dig just nu. 
2. **Planerat underhåll** -kommande underhåll som kan påverka tillgängligheten för dina tjänster i framtiden.  
3. **Hälsoråd** -ändringar i Azure-tjänster som kräver din uppmärksamhet. Exempel är när Azure-funktionerna är inaktuella eller om du överskrider en kvot för användning.

> [!NOTE]
> Användarna måste ha rollen Läsare för en prenumeration om du vill visa Service Health-händelser.

## <a name="get-started-with-service-health"></a>Kom igång med Service Health
Välj Service Health-panelen på instrumentpanelen i portalen för att starta Service Health-instrumentpanelen. Om du tidigare har tagit bort panelen eller om du använder en anpassad instrumentpanel, Sök efter Service Health-tjänsten i ”fler tjänster” (vänster på instrumentpanelen i nederkant).

![Kom igång med Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Se aktuella problem som påverkar dina tjänster
Den **tjänsten problem** vyn visar alla pågående problem i Azure-tjänster som påverkar dina resurser. Du kan förstå när problemet började och vilka tjänster och regioner som påverkas. Du kan också läsa den senaste uppdateringen för att förstå vad Azure gör för att lösa problemet. 

![Hantera problem med tjänsten](./media/service-health-overview/azure-service-health-overview-2.png)

Välj den **potentiell påverkan** fliken för att se listan över resurser som du äger och som kan påverkas av problemet. Du kan hämta en CSV-lista över dessa resurser för att dela med ditt team.

![Hantera problem med tjänsten - påverkan](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Hämta länkar och nedladdningsbara förklaringar 
Du kan hämta en länk för problemet som du kan använda i problemhanteringssystemet. Du kan ladda ned PDF och ibland CSV-filer för att dela med personer som inte har åtkomst till Azure-portalen.   

![Hantera problem med tjänsten - problemhantering](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Få support från Microsoft
Kontakta supporten om din resurs lämnas i felaktigt tillstånd, även när problemet är löst.  Använda support länkarna till höger på sidan.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fäst en personligt anpassade hälsotillstånd karta på instrumentpanelen
Filtrera Tjänstehälsa för att visa dina affärskritiska prenumerationer, regioner och resurstyper. Spara filtret och Fäst en personligt anpassade hälsotillstånd världskarta på instrumentpanelen i portalen. 

![Filtrera anpassade hälsotillstånd karta](./media/service-health-overview/azure-service-health-overview-6a.png)

![Fäst en personligt anpassade health-karta](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurera service health-aviseringar
Tjänstehälsa kan integreras med Azure Monitor för att meddela dig via e-post, SMS och webhook-aviseringar när dina affärskritiska resurser påverkas. Ställ in en aktivitetsloggavisering för hälsohändelsen lämplig tjänst. Dirigera den här aviseringen till rätt personer i din organisation med åtgärdsgrupper. Mer information finns i [konfigurera aviseringar för Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Nästa steg
Ställa in aviseringar så att du blir informerad om problem med hälsotillstånd. Mer information finns i [bästa praxis för att konfigurera Azure Service Health-aviseringar](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
