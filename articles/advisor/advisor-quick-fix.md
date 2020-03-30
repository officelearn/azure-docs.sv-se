---
title: Quick Fix-reparation för advisor-rekommendationer
description: Utföra massåtgärder med Quick Fix i Advisor
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502495"
---
# <a name="quick-fix-remediation-for-advisor"></a>Snabbkorrigeringsreparation för Advisor
**Quick Fix** möjliggör ett snabbare och enklare sätt att åtgärda rekommendation på flera resurser. Det ger möjlighet till massåtgärder för resurser och hjälper dig att optimera dina prenumerationer snabbare med reparation i stor skala för dina resurser.
Funktionen är endast tillgänglig för vissa rekommendationer via Azure-portalen.


## <a name="steps-to-use-quick-fix"></a>Åtgärder för att använda "Quick Fix"

1. Klicka på rekommendationen i listan med rekommendationer som har **Quick Fix-etiketten.**

   ![Snabbkorrigering av rådgivare](./media/quick-fix-1.png)
   
   *Priserna i bilden är till exempel endast i syfte att*

2. På sidan Rekommendationsinformation visas en lista över resurser som du har den här rekommendationen för. Markera alla resurser som du vill åtgärda för rekommendationen.

   ![Snabbkorrigering av rådgivare](./media/quick-fix-2.png)
   
   *Priserna i bilden är till exempel endast i syfte att*

3. När du har valt resurserna klickar du på **snabbkorrigeringsknappen** för att åtgärda för massantentate.

   > [!NOTE]
   > Vissa av de angivna resurserna kan vara inaktiverade eftersom du inte har rätt behörighet att ändra dem.
   
   > [!NOTE]
   > Om det finns andra konsekvenser, förutom de förmåner som nämns i Advisor, kommer du att meddelas i erfarenheten för att hjälpa dig att fatta välgrundade saneringsbeslut.
   
4. Du kommer att få ett meddelande om att reparationen ska slutföras. Ett fel visas om det finns resurser som inte åtgärdas och resurser i det valda läget i resurslistevyn.  


## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md)
* [REST-API för rådgivare](https://docs.microsoft.com/rest/api/advisor/)
