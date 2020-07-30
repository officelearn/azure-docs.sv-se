---
title: Återbetala kostnader för Azure-reservationer
description: Lär dig hur du visar kostnader för Azure-reservationer för återbetalning.
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 04e4e8dfce8549494eb9d2984d6307c18b88dc73
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287322"
---
# <a name="charge-back-azure-reservation-costs"></a>Återbetala kostnader för Azure-reservationer

Faktureringsläsare för Enterprise-avtal och Microsoft-kundavtal kan visa amorterade kostnadsdata för reservationer. De kan använda dessa kostnadsdata för att återbetala det monetära värdet för en prenumeration, en resursgrupp, en resurs eller en tagg till sina partner. I amorterade data är det effektiva priset den beräknade kostnaden per timme för reservationer. Kostnaden är den totala kostnaden för resursens reservationsanvändning den dagen.

Användare med en enskild prenumeration kan hämta amorterade kostnadsdata från sin användningsfil. När en resurs får en reservationsrabatt innehåller avsnittet *AdditionalInfo* i användningsfilen reservationsinformationen. Du kan läsa mer i [Ladda ned användningsinformation från Azure-portalen](https://docs.microsoft.com/azure/cost-management-billing/understand/download-azure-daily-usage#download-usage-from-the-azure-portal-csv).

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Hämta data för återbetalning av reservationer för återbetalning

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **Kostnadshantering och fakturering**.
1. Under **Faktisk kostnad** väljer du måttet **Amorterad kostnad**.
1. Om du vill se vilka resurser som använts av en reservation använder du ett filter för **reservation** och väljer sedan reservationer.
1. Ange **Kornighet** till **Varje månad** eller **Varje dag**.
1. Ange diagramtypen till **Tabell**.
1. Ange alternativet **Gruppera efter** till **Resurs**.

[![Exempel som visar kostnader för reservationsresurser som du kan använda för återbetalning](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Här är en video som visar hur du ser kostnader för reservationsanvändning i Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- Mer information om Azure-reservationer finns i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](manage-reserved-vm-instance.md)
