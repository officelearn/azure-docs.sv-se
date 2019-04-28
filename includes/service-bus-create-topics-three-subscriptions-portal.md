---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60333572"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Skapa ett ämne med Azure Portal
1. På sidan **Service Bus Namespace** (Service Bus-namnrymd) väljer du **Ämnen** på den vänstra menyn.
2. Välj **+ Ämne** i verktygsfältet. 
4. Ange ett **namn** för ämnet. Lämna standardvärdena för de andra alternativen.
5. Välj **Skapa**.

    ![Skapa ämne](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Skapa prenumerationer till ämnet
1. Välj det **ämne** som du skapade i föregående avsnitt. 
    
    ![Välja ämna](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. På sidan **Service Bus-ämne** väljer du **Prenumerationer** från den vänstra menyn och sedan **+ Prenumeration** i verktygsfältet. 
    
    ![Knappen Lägg till prenumeration](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. På den **skapa prenumeration** anger **S1** för **namn** för prenumerationen och välj sedan **skapa**. 

    ![Sidan Skapa prenumeration](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Upprepa föregående steg två gånger för att skapa prenumerationer med namnet **S2** och **S3**.