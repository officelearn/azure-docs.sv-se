---
title: Hur tillämpas en rabatt på Azure Databricks Pre-Purchase
description: Lär dig hur en rabatt på Azure Databricks Pre-Purchase gäller för din användning.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827643"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Hur tillämpas Azure Databricks Pre-Purchase rabatt

Du kan använda köpt Azure Databricks commit enheter (DBCU) när som helst under perioden för köp. Alla Azure Databricks är dras från köpt DBCUs automatiskt.

Till skillnad från virtuella datorer, inte köpt enheter går ut per timme. Du kan använda dem när som helst under perioden för köpet. För att få Pre-Purchase rabatter, behöver du inte distribuera om eller associera en köpt plan för din Azure Databricks-arbetsytor för användning.

Pre-Purchase rabatten gäller enbart för användningen av Azure Databricks-enheter (DBU). Andra avgifter, till exempel beräkning, lagring och nätverk debiteras separat.

## <a name="pre-purchase-discount-application"></a>Förhandsköpa tillämpningen av rabatter

Databricks Pre-Purchase gäller för alla Databricks arbetsbelastningar och nivåer. Du kan tänka dig Pre-Purchase som en pool av förbetald Databricks commit-enheter. Användning dras från poolen, oavsett arbetsbelastning eller nivå. Användning av i följande förhållanden:

| **Arbetsbelastning** | **Förhållandet mellan DBU-program – Standard-nivån** | **Förhållandet mellan DBU-program – Premium-nivån** |
| --- | --- | --- |
| Datanalys | 0.4 | 0.55 |
| Datateknik | 0.15 | 0,30 |
| Datateknik (lättversion) | 0.07 | 0.22 |

Till exempel när en mängd Data Analytics – Standard-nivån används härleds köpt Databricks commit enheter efter 0,4 enheter. När en mängd Data Engineering Light – Standard-nivån används, köpt Databricks commit enheten dras av 0,07 enheter

## <a name="determine-plan-use"></a>Fastställa plan

Om du vill fastställa din DBCU plan, gå till Azure-portalen > **reservationer** och klicka på den köpta Databricks-planen. Din användning hittills visas med alla återstående enheter. Mer information om hur du avgör din reservation använda kan du läsa den [Se reservation användning](billing-reservation-apis.md#see-reservation-usage) artikeln.

## <a name="how-discount-application-shows-in-usage-data"></a>Hur tillämpningen av rabatter visas i användningsdata

När Pre-Purchase rabatten gäller för din Databricks-användning, visas på begäran-kostnader som noll i användningsdata. Mer information om reservation kostnader och användning finns i [hämta Enterprise-avtal reservation kostnader och användning](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Läs hur du hanterar en reservation i [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).
- Läs mer om förköpa Azure Databricks för att spara pengar i [optimera Azure Databricks kostnaderna med en Pre-Purchase](billing-prepay-databricks-reserved-capacity.md).
- Om du vill veta mer om Azure reservationer, finns i följande artiklar:
  - [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](billing-manage-reserved-vm-instance.md)
  - [Förstå användningen av reservation för en prenumeration med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
  - [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
