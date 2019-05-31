---
title: Publicera erbjudande för Azure SaaS-program | Azure Marketplace
description: Publicera processen och stegen för ett SaaS-program erbjuder på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 5574a7ba216eed42896d6fcb1890585f76561834
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258093"
---
# <a name="publish-a-saas-application-offer"></a>Publicera ett erbjudande för SaaS-program

När du har skapat ett nytt erbjudande genom att tillhandahålla informationen om den **nytt erbjudande** kan du publicera erbjudandet. Välj **publicera** att starta publiceringsprocessen.

> [!IMPORTANT] 
> SaaS erbjuder funktioner håller på att migreras till den [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Alla nya utgivare måste använda Partner Center för att skapa nya SaaS-erbjudanden och hantering av befintliga erbjudanden.  Aktuella utgivare med SaaS-erbjudanden migreras batchwise från partnerportalen i molnet till Partner Center.  Cloud Partner Portal visar statusmeddelanden för att ange när specifika befintliga erbjudanden har migrerats.
> Mer information finns i [skapa ett nytt SaaS-erbjudande](../../partner-center-portal/create-new-saas-offer.md).


I följande diagram visas de övergripande stegen för att publicera ett nytt erbjudande för SaaS-program.

![Publicera erbjudande-steg](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Detaljerad beskrivning av publicering av steg

I följande tabell beskrivs varje publishing steg, med en Uppskattad tidsåtgång (max) att slutföra varje steg.

|     **Steg**       |     **Time**      |  **Beskrivning**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Certifiering           |       2 veckor            |          Erbjudandet analyseras av Azure-certifiering-teamet. Det här steget utför genomsökningar efter virus, skadlig kod, säkerhet och säkerhetsproblem. Den kontrollerar också att det här erbjudandet uppfyller alla urvalskriterier (se [krav](./cpp-prerequisites.md)). Feedback tillhandahålls om ett problem hittas.         |
|           Paketering         |       1 timme            |       Erbjudandets tekniska resurser som är packade för kundens användning och lead-system har konfigurerats och konfigurera.            |
|        Utgivaren signering            |         -          |        Sista publisher granska och bekräfta innan erbjudandet lanseras. Du kan distribuera ditt erbjudande i de valda prenumerationerna (i erbjudandet information steg) för att kontrollera att den uppfyller dina krav. Välj **Go Live** så att ditt erbjudande kan flytta till nästa steg.           |
|        Paketering            |        1 timme           |        Slutförda erbjudandet replikeras i regionerna och marketplace produktionssystem.           |
|        Live            |       fyra dagar            |         Erbjudandet är publicerat, replikeras till de nödvändiga regionerna och blir tillgängliga för allmänheten.          |

Tillåt för upp till 10 arbetsdagar att slutföra publiceringsprocessen och erbjudandet släpps. När du har slutfört publiceringsprocessen ditt SaaS-erbjudande visas i den [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Nästa steg

[Uppdatera ett befintligt erbjudande](./cpp-update-existing-offer.md)
