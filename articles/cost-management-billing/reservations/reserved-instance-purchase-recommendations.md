---
title: Så här skapas Azure-reservationsrekommendationer
description: Lär dig hur Azure-reservationsrekommendationer skapas för virtuella datorer.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 90967e740b87c2f93bd46bfb78684af96f36193a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508487"
---
# <a name="how-reservation-recommendations-are-created"></a>Så här skapas reservationsrekommendationer

Inköpsrekommendationer för reserverade Azure-instanser (RI) tillhandahålls av [API:et för reservationsrekommendationer](/rest/api/consumption/reservationrecommendations) för Azure Consumption, [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) och när du köper reservationer i Microsoft Azure-portalen.

Följande steg definierar hur rekommendationer beräknas:

1. Rekommendationsmotorn utvärderar hur mycket dina resurser används per timme inom det angivna omfånget som kan utgöras av de senaste 7, 30 eller 60 dagarna.
2. Motorn simulerar dina kostnader med och utan reservationer enligt dessa förbrukningsdata.
3. Kostnaderna simuleras enligt olika kvantiteter och den kvantitet som maximerar dina besparingar rekommenderas.
4. Om dina resurser stängs av regelbundet kommer simuleringen inte att hitta några besparingsmöjligheter och inte ge dig någon inköpsrekommendation.

## <a name="recommendations-in-azure-advisor"></a>Rekommendationer i Azure Advisor

Azure Advisor tillhandahåller rekommendationer om inköp av reservationer för virtuella datorer. Tänk på följande punkter:

- Advisor tillhandahåller endast rekommendationer som omfattas av en prenumeration.
- Advisor tillhandahåller rekommendationer som bygger på dina data från de senaste 30 dagarna.
- Om du köper en reservation med delad omfattning kan det ta upp till 30 dagar innan Advisors rekommendationer slutar att visas.

## <a name="other-expected-api-behavior"></a>Annat förväntat API-beteende

- Om du försöker få rekommendationer som bygger på data från de senaste sju dagarna kanske detta misslyckas om de virtuella datorerna har stängts ned i mer än en dag.

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur rabatten för Azure-reservation tillämpas på virtuella datorer](../manage/understand-vm-reservation-charges.md).
