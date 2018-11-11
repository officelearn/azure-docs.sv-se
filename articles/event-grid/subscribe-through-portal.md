---
title: Azure Event Grid-prenumerationer via portalen
description: Beskriver hur du skapar en Event Grid-prenumerationer via portalen.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: eb48e40007a25992a9a399176b6a4f93be89efc8
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344100"
---
# <a name="subscribe-to-events-through-portal"></a>Prenumerera på händelser via portalen

Den här artikeln beskriver hur du skapar en Event Grid-prenumerationer via portalen.

## <a name="create-event-subscriptions"></a>Skapa prenumerationer på händelser

Skapa en Event Grid-prenumeration för någon av stöds [händelsekällor](event-sources.md), Använd följande steg. Den här artikeln visar hur du skapar en Event Grid-prenumeration för en Azure-prenumeration.

1. Välj **Alla tjänster**.

   ![Välj alla tjänster](./media/subscribe-through-portal/select-all-services.png)

1. Sök efter **Event Grid-prenumerationer** och välj bland de tillgängliga alternativen.

   ![Search](./media/subscribe-through-portal/search.png)

1. Välj **+ Händelseprenumeration**.

   ![Lägg till en prenumeration](./media/subscribe-through-portal/add-subscription.png)

1. Välj vilken typ av prenumeration som du vill skapa. Om du vill prenumerera på händelser för din Azure-prenumeration väljer du exempelvis **Azure-prenumerationer** och målprenumerationen.

   ![Välj Azure-prenumeration](./media/subscribe-through-portal/azure-subscription.png)

1. Om du vill prenumerera på alla händelsetyper för den här händelsekällan hålla den **prenumerera på alla händelsetyper** alternativet är markerat. Annars väljer du händelsetyper för den här prenumerationen.

   ![Välj händelsetyper](./media/subscribe-through-portal/select-event-types.png)

1. Ge mer information om händelseprenumerationen, till exempel slutpunkten för hantering av händelser och ett prenumerationsnamn.

   ![Ange information om en prenumeration](./media/subscribe-through-portal/provide-subscription-details.png)

1. Om du vill aktivera obeställbara och anpassa återförsöksprinciper, Välj **extrafunktioner**.

   ![Välj ytterligare funktioner](./media/subscribe-through-portal/select-additional-features.png)

1. Välj en behållare som ska användas för att lagra händelser som inte levereras och ange hur återförsök skickas.

   ![Aktivera obeställbara och försök igen](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Välj **Skapa** när du är klar.

## <a name="create-subscription-on-resource"></a>Skapa prenumeration på resursen

Vissa händelsekällor stöd för att skapa en händelseprenumeration via gränssnittet portalen för den resursen. Välj händelsekällan och leta efter **händelser** i vänstra fönstret.

![Ange information om en prenumeration](./media/subscribe-through-portal/resource-events.png)

Portalen ger alternativ för att skapa en händelseprenumeration som är relevant för den här källan.

## <a name="next-steps"></a>Nästa steg

* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
