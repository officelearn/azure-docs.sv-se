---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138168"
---
Se till att du redan har skapat ett Service Bus-namnområde så som det visas [här][namespace-how-to].

1. Logga in på [Azure Portal][azure-portal].
2. I det vänstra navigeringsfönstret i portalen klickar du på **Service Bus** (om du inte ser **Service Bus** klickar du på **Alla tjänster**).
3. Klicka på det namnområde du vill skapa kön i. I det här fallet är det **sbnstest1**.
   
    ![Skapa en kö][createqueue1]
4. I namnområdesfönstret klickar du på **Köer**. I fönstret **Köer** klickar du sedan på **+ Kö**.
   
    ![Välj köer][createqueue2]
5. Ange ett **namn** för kön och lämna de andra värdena med standardvärdena.
   
    ![Välj ny][createqueue3]
6. Klicka på **Skapa** längst ned i fönstret.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
