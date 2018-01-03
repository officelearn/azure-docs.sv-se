---
title: "Översikt över Azure tjänstens hälsa | Microsoft Docs"
description: "Anpassad information om hur dina Azure appar påverkas av problem med aktuella och framtida Azure-tjänsten och underhåll."
services: Resource health
documentationcenter: 
author: rboucher
manager: 
editor: 
ms.assetid: 
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/07/2017
ms.author: robb
ms.openlocfilehash: c463479b7eaee5a0548c8891dd3a20ef070dd39b
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="azure-service-health"></a>Azure Service Health
Tjänstens hälsa för Azure ger lämplig och personlig information när problem i Azure-tjänster påverkar dina tjänster.  Det hjälper dig också för kommande planerat underhåll.

## <a name="service-health-events"></a>Hälsa av tjänsten-händelser
Tjänstens hälsa spårar tre typer av hälsotillstånd händelser som kan påverka dina resurser:
1. **Tjänsten problem** -problem i Azure-tjänster som påverkar dig just nu. 
2. **Planerat underhåll** -kommande underhåll som kan påverka tillgängligheten för dina tjänster i framtiden.  
3. **Hälsotillstånd rekommendationerna** -ändringar i Azure-tjänster som kräver din uppmärksamhet. Exempel är när Azure-funktioner är inaktuella eller om du överskrider en kvot.

    ![Händelser för Hälsotjänst](./media/service-health-overview/azure-service-health-overview-7.png)

## <a name="get-started-with-service-health"></a>Kom igång med tjänstens hälsa
Välj panelen tjänstens hälsa på instrumentpanelen i portalen om du vill starta instrumentpanelen tjänstens hälsa. Om du tidigare har tagit bort panelen eller om du använder anpassade instrumentpanel, söka efter tjänstens hälsa för tjänsten i ”fler tjänster” (nedre vänstra på instrumentpanelen).
![Kom igång med tjänstens hälsa](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Visa aktuella problem som påverkar dina tjänster
Den **tjänsten problem** pågående problem visas i Azure-tjänster som som påverkar dina resurser. Du kan förstå när problemet började och vilka tjänster och regioner som påverkas. Du kan också läsa den senaste uppdateringen för att förstå vad Azure gör för att lösa problemet. 
![Hantera serviceproblem](./media/service-health-overview/azure-service-health-overview-2.png)

Välj den **potentiella påverkan** fliken för att se listan över resurser som du äger och som kan påverkas av problemet. Du kan hämta en CSV-lista över dessa resurser för att dela med ditt team.
![Hantera problem med tjänsten - påverkan](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Hämta länkar och nedladdningsbara förklaringar 
Du kan hämta en länk för problemet ska användas i det problem hanteringssystemet. Du kan hämta PDF och ibland CSV-filer för att dela med användare som inte har åtkomst till Azure-portalen.   
![Hantera problem med tjänsten - problemhantering](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Få support från Microsoft
Kontakta supporten om din resurs lämnas i felaktigt tillstånd, även när problemet är löst.  Använd länkarna stöd till höger på sidan.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fästa en anpassad hälsa karta till din instrumentpanel
Filtrera tjänstens hälsa för att visa dina verksamhetskritiska prenumerationer, regioner och resurstyper. Spara filtret och fästa en anpassad hälsa world karta till instrumentpanelen i portalen. 
![Filter anpassade hälsa kartan](./media/service-health-overview/azure-service-health-overview-6a.png)
![fästa en anpassad hälsa karta](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurera aviseringar för tjänstens hälsa
Tjänstens hälsa för Azure integreras med Azure-Övervakaren för att meddela dig via e-post och textmeddelanden webhook meddelanden när resurserna verksamhetskritiska påverkas. Konfigurera en varning för logg för tjänstens hälsa för händelsen. Vidarebefordra den här aviseringen till rätt personer i organisationen med hjälp av åtgärdsgrupper. Mer information finns i [konfigurera aviseringar för tjänstens hälsa](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

 
