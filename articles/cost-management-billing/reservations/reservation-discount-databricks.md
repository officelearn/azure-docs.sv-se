---
title: Så här tillämpas rabatter för förhandsköp av Azure Databricks
description: Lär dig hur rabatter för förhandsköp av Azure Databricks tillämpas på din användning.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 69f83fc1d390fcbea77468045b89ee6024e7fe9a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200324"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Så här tillämpas rabatter för förhandsköp av Azure Databricks

Du kan använda förköpta DBCU:er (Databricks Commit Units) för Azure Databricks när som helst under inköpsperioden. All Azure Databricks-användning dras automatiskt av från de förköpta DBCU:erna.

Förköpta enheter går inte ut per timme, vilket är fallet med virtuella datorer. Du kan använda dem när som helst under inköpsperioden. För att erhålla rabatter för förhandsköp behöver du inte distribuera om eller tilldela en förköpt plan till dina Azure Databricks-arbetsytor för användningen.

Rabatten för förhandsköp gäller endast för användning av Azure Databricks-enheter (DBU). Andra avgifter för till exempel beräkning, lagring och nätverk debiteras separat.

## <a name="pre-purchase-discount-application"></a>Tillämpning av rabatter för förhandsköp

Förhandsköp av Databricks gäller för alla arbetsbelastningar och nivåer i Databricks. Förhandsköp kan liknas vid en pool av förbetalda DBCU:er. Användningen dras av från poolen, oberoende av arbetsbelastning eller nivå. Användningen dras av enligt följande förhållande:

| **Arbetsbelastning** | **DBU-tillämpningsgrad — Standard-nivå** | **DBU-tillämpningsgrad – Premium-nivå** |
| --- | --- | --- |
| Dataanalys | 0,4 | 0,55 |
| Datateknik | 0,15 | 0,30 |
| Datateknik (lättversion) | 0,07 | 0,22 |

Exempelvis dras 0,4 enheter av från de förköpta DBCU:erna när en viss kvantitet av Dataanalys på Standard-nivå använts. När en viss kvantitet av Datateknik (lättversion) – Standard-nivå använts dras 0,07 enheter av från de förköpta DBCU:erna

## <a name="determine-plan-use"></a>Kontrollera plananvändningen

Om du vill kontrollera din DBCU-plananvändning går du till Azure-portalen > **Reservationer** och klickar på den Databricks-plan du köpt. Din användning hittills visas, med eventuella återstående enheter. Mer information om hur du kontrollerar din reservationsanvändning finns i artikeln om [reservationsanvändning](reservation-apis.md#see-reservation-usage).

## <a name="how-discount-application-shows-in-usage-data"></a>Så här visas tillämpningen av rabatter i användningsdata

När rabatten för förhandsköp tillämpas på din Databricks-användning visas ”avgifter på begäran” som noll i användningsdata. Mer information om kostnader för och användning av reservationer finns i [Hämta kostnader för och användning av EA-reservationer](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar en reservation finns i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).
- Om du vill veta mer om hur du kan spara pengar genom att köpa Azure Databricks i förväg läser du [Optimera kostnaderna genom att köpa Azure Databricks i förväg](prepay-databricks-reserved-capacity.md).
- Mer information om Azure-reservationer finns i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Hantera reservationer i Azure](manage-reserved-vm-instance.md)
  - [Förstå reservationsanvändning för en prenumeration med Betala per användning-priser](understand-reserved-instance-usage.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
