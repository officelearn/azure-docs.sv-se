---
title: Azure Event Grid-prenumerationer via portal
description: I den här artikeln beskrivs hur du skapar Event Grid-prenumerationer för källor som stöds, till exempel Azure Blob Storage, med hjälp av Azure-portalen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 599f48ed241010d8551bd110c7f778c9ef508eac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393166"
---
# <a name="subscribe-to-events-through-portal"></a>Prenumerera på evenemang via portal

I den här artikeln beskrivs hur du skapar Event Grid-prenumerationer via portalen.

## <a name="create-event-subscriptions"></a>Skapa händelseprenumerationer

Om du vill skapa en Event Grid-prenumeration för någon av de [händelsekällor](overview.md#event-sources)som stöds använder du följande steg. Den här artikeln visar hur du skapar en Event Grid-prenumeration för en Azure-prenumeration.

1. Välj **Alla tjänster**.

   ![Välj alla tjänster](./media/subscribe-through-portal/select-all-services.png)

1. Sök efter prenumerationer på **eventrutnät** och välj dem bland de tillgängliga alternativen.

   ![Search](./media/subscribe-through-portal/search.png)

1. Välj **+ Händelseprenumeration**.

   ![Lägg till en prenumeration](./media/subscribe-through-portal/add-subscription.png)

1. Välj den typ av prenumeration som du vill skapa. Om du till exempel vill prenumerera på händelser för din Azure-prenumeration väljer du **Azure-prenumerationer** och målprenumerationen.

   ![Välj Azure-prenumeration](./media/subscribe-through-portal/azure-subscription.png)

1. Om du vill prenumerera på alla händelsetyper för den här händelsekällan håller du alternativet **Prenumerera på alla händelsetyper** markerat. Annars väljer du händelsetyperna för den här prenumerationen.

   ![Välj händelsetyper](./media/subscribe-through-portal/select-event-types.png)

1. Ange ytterligare information om händelseprenumerationen, till exempel slutpunkten för hantering av händelser och ett prenumerationsnamn.

   ![Ange prenumerationsinformation](./media/subscribe-through-portal/provide-subscription-details.png)

1. Om du vill aktivera principer för obeställbara bokstäver och anpassa återförsök väljer du **Ytterligare funktioner**.

   ![Välj ytterligare funktioner](./media/subscribe-through-portal/select-additional-features.png)

1. Välj en behållare som ska användas för att lagra händelser som inte levereras och ange hur återförsök ska skickas.

   ![Aktivera obeställbara bokstäver och försök igen](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Välj **Skapa** när du är klar.

## <a name="create-subscription-on-resource"></a>Skapa prenumeration på resurs

Vissa händelsekällor stöder att skapa en händelseprenumeration via portalgränssnittet för den resursen. Välj händelsekälla och leta efter **händelser** i den vänstra rutan.

![Ange prenumerationsinformation](./media/subscribe-through-portal/resource-events.png)

Portalen ger dig alternativ för att skapa en händelseprenumeration som är relevant för den källan.

## <a name="next-steps"></a>Nästa steg

* För information om händelseleverans och återförsök, [Leverans av eventrutnätsmeddelanden och återförsök](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
