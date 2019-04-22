---
title: Felsök i Azure Service Bus AMQP | Microsoft Docs
description: Innehåller en lista över AMQP fel du kan få när du använder Azure Service Bus och orsaka av dessa fel.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58910052"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP-fel i Azure Service Bus
Den här artikeln innehåller några av de fel som du får när du använder AMQP med Azure Service Bus. De är alla standard beteenden för tjänsten. Du kan undvika dem genom att skicka och ta emot samtal på anslutning/länk som automatiskt återskapar anslutningen/länk.

## <a name="link-is-closed"></a>Länken är stängd 
Du ser följande fel när AMQP-anslutning och länk är aktiv, men inga anrop (till exempel skicka eller ta emot) görs med hjälp av länken i 10 minuter. Därför har länken stängts. Anslutningen är fortfarande öppen.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Anslutningen är stängd
Du ser följande fel på AMQP-anslutning när alla länkar i anslutningen har stängts eftersom det var ingen aktivitet (inaktiv) och en ny länk har inte skapats på 5 minuter.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Länk skapas inte 
Du ser detta fel när en ny AMQP-anslutning har skapats men inte skapa en länk inom 1 minut av skapandet av AMQP-anslutning.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Nästa steg

Mer information om AMQP och Service Bus finns i följande länkar:

* [Översikt över Service Bus AMQP]
* [AMQP 1.0-protokollguide]
* [AMQP i Service Bus för Windows Server]

[Översikt över Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protokollguide]: service-bus-amqp-protocol-guide.md
[AMQP i Service Bus för Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
