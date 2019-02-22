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
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588678"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Skapa ett ämne med Azure Portal
1. På den **Service Bus Namespace** väljer **ämnen** på den vänstra menyn.
2. Välj **+ ämne** i verktygsfältet. 
4. Ange en **namn** för ämnet. Lämna standardvärdena för de andra alternativen.
5. Välj **Skapa**.

    ![Skapa ämne](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Skapa prenumerationer till ämnet
1. Välj den **avsnittet** som du skapade i föregående avsnitt. 
    
    ![Välj ämne](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. På den **Service Bus-ämne** väljer **prenumerationer** från den vänstra menyn och välj sedan **+ prenumeration** i verktygsfältet. 
    
    ![Lägg till prenumeration knapp](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. På den **skapa prenumeration** anger **S1** för **namn** för prenumerationen och välj sedan **skapa**. 

    ![Skapa prenumeration](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Upprepa föregående steg två gånger för att skapa prenumerationer med namnet **S2** och **S3**.