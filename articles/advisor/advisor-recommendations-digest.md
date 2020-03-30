---
title: Rekommendationssammandrag för Azure Advisor
description: Få regelbundna sammanfattningar för dina aktiva rekommendationer
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502469"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Konfigurera periodisk sammanfattning för rekommendationer

Advisor **rekommendation sammanfattningar** ger ett enkelt och proaktivt sätt att hålla koll på dina aktiva rekommendationer, över olika kategorier. Funktionen ger möjlighet att konfigurera regelbundna meddelanden för sammanfattningen av alla dina aktiva rekommendationer, över olika kategorier. Du kan välja önskad kanal för aviseringar som e-post, sms eller andra med hjälp av åtgärdsgrupper. Den här artikeln visar hur du konfigurerar en **rekommendationssammanfattning** för advisorrekommendationerna.


## <a name="setting-up-your-recommendation-digest"></a>Ställa in din rekommendation sammanfattning 

**Rekommendationen sammanfattning** skapande erfarenhet hjälper dig att konfigurera sammanfattningen. Du kan välja nedan parametrar för konfigurationer:
1. Kategori: Vi har rekommendation kategorier som kostnad, hög tillgänglighet, prestanda och operativ excellens. Funktionen är inte tillgänglig för säkerhetsrekommendationer ännu.
2. Frekvensen av sammandrag: Frekvens för sammanfattning anmälningar kan vara varje vecka, varannan vecka och varje månad.
3. Åtgärdsgrupp: Du kan antingen välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp. Mer information om åtgärdsgrupper finns i [skapa och hantera åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).
4. Språk för sammandraget
5. Rekommendation sammanfattning namn: Du kan använda en användarvänlig sträng för att bättre spåra och övervaka digests.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Steg för att skapa rekommendationssammandrag i Azure-portalen

Här är stegen för att skapa **rekommendationssammandrag:**
* **Steg 1:** I Azure-portalen går du till **Advisor** och under **Övervakningsavsnitt** väljer du **Rekommendationssammandrag** 

   ![Ingångspunkt för rekommendationssammandrag](./media/digest-0.png)

* **Steg 2:** Välj **Ny rekommendation sammandrag** från det övre fältet enligt nedan:

   ![Skapa rekommendationssammandrag](./media/digest-5.png)

* **Steg 3:** I **avsnittet om omfattning** väljer du **prenumerationen** för sammanfattningen

   ![Ge rekommendationssammandragingångar](./media/digest-1.png)

* **Steg 4:** I **villkorsavsnittet** väljer du konfigurationer som **kategori,** **frekvens** och **språk**

   ![Ge inmatningsvillkor för rekommendationssammandrag](./media/digest-2.png)

* **Steg 5:** Välj **åtgärdsgruppen** för sammanfattningen i **åtgärdsgruppavsnittet.** Du kan läsa mer här - [Skapa och hantera åtgärdsgrupper](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Ange åtgärdsgrupp för indatainmatning av rekommendation](./media/digest-3.png)

* **Steg 6:** I det här sista avsnittet för **sammanfattningsinformation**kan du tilldela namn och tillstånd till din rekommendationssammandrag. Tryck **på skapa rekommendationssammandrag** för att slutföra upplägget.
   ![Fullständig skapande av rekommendationssammandrag](./media/digest-4.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Azure Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer för rådgivare](advisor-cost-recommendations.md)
* [Advisors prestandarekommendationer](advisor-performance-recommendations.md)
* [Säkerhetsrekommendationer för rådgivare](advisor-security-recommendations.md)
* [Advisor Operational Excellence rekommendationer](advisor-operational-excellence-recommendations.md)
* [REST-API för rådgivare](https://docs.microsoft.com/rest/api/advisor/)
