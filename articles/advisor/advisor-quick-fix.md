---
title: Snabb korrigering för rådgivare-rekommendationer
description: Utför Mass reparation med snabb korrigering i Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: ebd993733c1aca9cd4f7d92f0a75e22d68a87ffe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518070"
---
# <a name="quick-fix-remediation-for-advisor"></a>Snabb korrigering för Advisor
**Snabb korrigering** möjliggör ett snabbare och enklare sätt att åtgärda på flera resurser. Den ger möjlighet till Mass reparationer av resurser och hjälper dig att optimera dina prenumerationer snabbare med reparationer i skala för dina resurser.
Funktionen är endast tillgänglig för vissa rekommendationer, via Azure Portal.


## <a name="steps-to-use-quick-fix"></a>Steg för att använda snabb korrigering

1. I listan över rekommendationer som har etiketten **snabb korrigering** klickar du på rekommendationen.

   ![Snabb korrigering för Advisor](./media/quick-fix-1.png)
   
   *Priserna i bilden är endast till exempel syfte*

2. På sidan rekommendations information visas en lista över resurser som du har den här rekommendationen för. Välj alla resurser som du vill åtgärda för rekommendationen.

   ![Snabb korrigering för Advisor](./media/quick-fix-2.png)
   
   *Priserna i bilden är endast till exempel syfte*

3. När du har valt resurserna klickar du på knappen **snabb korrigering** för att återställa.

   > [!NOTE]
   > Några av de listade resurserna kan vara inaktiverade eftersom du inte har rätt behörighet för att ändra dem.
   
   > [!NOTE]
   > Om det finns andra konsekvenser kan du, utöver de fördelar som anges i Advisor, meddelas i upplevelsen för att hjälpa dig att fatta välgrundade beslut om reparation.
   
4. Du får ett meddelande om reparations åtgärden. Du får ett fel meddelande om det finns resurser som inte åtgärdas och resurser i det valda läget i vyn resurs lista.  


## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer om Advisor-kostnader](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
