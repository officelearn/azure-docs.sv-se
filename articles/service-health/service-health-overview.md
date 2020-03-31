---
title: Översikt över tjänstens hälsa | Microsoft-dokument
description: Anpassad information om hur dina Azure-appar påverkas av aktuella och framtida Problem med Azure-tjänster och underhåll.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898445"
---
# <a name="service-health-overview"></a>Översikt över tjänstens hälsotillstånd

Service Health ger dig en anpassningsbar instrumentpanel som spårar hälsotillståndet för dina Azure-tjänster i de regioner där du använder dem. I den här instrumentpanelen kan du spåra aktiva händelser som pågående serviceproblem, kommande planerat underhåll eller relevanta hälsorekommendationer. När händelser blir inaktiva placeras de i din sjukdomshistoria i upp till 90 dagar. Slutligen kan du använda instrumentpanelen Service Health för att skapa och hantera tjänsthälsovarningar som proaktivt meddelar dig när serviceproblem påverkar dig.

## <a name="service-health-events"></a>Hälsoevenemang för tjänster

Service Health spårar fyra typer av hälsohändelser som kan påverka dina resurser:

1. **Serviceproblem** - Problem i Azure-tjänsterna som påverkar dig just nu. 
2. **Planerat underhåll** - Kommande underhåll som kan påverka tillgängligheten för dina tjänster i framtiden.  
3. **Hälsorekommendationer** – Ändringar i Azure-tjänster som kräver din uppmärksamhet. Exempel är när Azure-funktioner är inaktuella eller om du överskrider en användningskvot.
4. **Säkerhetsrekommendationer (förhandsversion)** – Säkerhetsrelaterade meddelanden som kan påverka tillgängligheten för dina Azure-tjänster.

> [!NOTE]
> Om du vill visa servicehälsohändelser måste användarna [beviljas rollen Läsare](../role-based-access-control/role-assignments-portal.md) i en prenumeration.

## <a name="get-started-with-service-health"></a>Komma igång med Service Health

Om du vill starta instrumentpanelen Service Health väljer du panelen Tjänsthälsa på portalens instrumentpanel. Om du tidigare har tagit bort panelen eller om du använder anpassad instrumentpanel söker du efter tjänsten Service Health i "Fler tjänster" (längst ned till vänster på instrumentpanelen).

![Komma igång med Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Se aktuella problem som påverkar dina tjänster

Vyn **Service-problem** visar eventuella pågående problem i Azure-tjänster som påverkar dina resurser. Du kan förstå när problemet började och vilka tjänster och regioner som påverkas. Du kan också läsa den senaste uppdateringen för att förstå vad Azure gör för att lösa problemet. 

![Hantera serviceproblem](./media/service-health-overview/azure-service-health-overview-2.png)

Välj fliken **Potentiell påverkan** om du vill visa den specifika resurslista som du äger och som kan påverkas av problemet. Du kan hämta en CSV-lista över dessa resurser som du kan dela med ditt team.

![Hantera serviceproblem - Påverkan](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Få länkar och nedladdningsbara förklaringar 

Du kan få en länk för problemet att använda i ditt problemhanteringssystem. Du kan hämta PDF- och ibland CSV-filer för att dela med personer som inte har åtkomst till Azure-portalen.   

![Hantera serviceproblem - Problemhantering](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Få support från Microsoft

Kontakta support om din resurs lämnas i dåligt tillstånd även efter att problemet har lösts.  Använd supportlänkarna till höger på sidan.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fästa en anpassad hälsokarta på instrumentpanelen

Filtrera tjänstens hälsotillstånd om du vill visa dina affärskritiska prenumerationer, regioner och resurstyper. Spara filtret och fäst en anpassad världskarta med hälsotillstånd på din portalinstrumentpanel. 

![Filtrera personlig hälsokarta](./media/service-health-overview/azure-service-health-overview-6a.png)

![Fästa en personlig hälsokarta](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurera hälsovarningar för tjänsten

Tjänsten Hälsa integreras med Azure Monitor för att varna dig via e-postmeddelanden, textmeddelanden och webhook meddelanden när dina affärskritiska resurser påverkas. Ställ in en aktivitetsloggavisering för rätt tjänsthälsohändelse. Dirigera den aviseringen till rätt personer i organisationen med hjälp av åtgärdsgrupper. Mer information finns i [Konfigurera aviseringar för tjänstens hälsotillstånd](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Nästa steg

Ställ in aviseringar så att du får ett meddelande om hälsoproblem. Mer information finns i [Metodtips för att konfigurera Hälsoaviseringar för Azure Service](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
