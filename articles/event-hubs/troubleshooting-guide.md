---
title: Fel söknings guide – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en lista över undantag och föreslagna åtgärder för Azure Event Hubs Messaging.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124677"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure Event Hubs – fel söknings guide
Den här artikeln innehåller fel söknings tips och rekommendationer för några problem som kan uppstå när du använder Azure-EventHubs.

## <a name="connectivity-certificate-or-timeout-issues"></a>Problem med anslutning, certifikat eller tids gräns
Följande steg kan hjälpa dig med fel sökning av problem med anslutning/certifikat/tids gräns för alla tjänster under *. servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Det hjälper till med att kontrol lera om du har problem med IP-filtrering eller problem med virtuella nätverk eller certifikat kedjan (vanligt vis när du använder Java SDK).

    Ett exempel på ett slutfört meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på fel meddelande:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrol lera om någon port är blockerad i brand väggen. Portarna som används är 443 (HTTPS), 5671 (AMQP) och 9093 (Kafka). Beroende på vilket bibliotek du använder används även andra portar. Här är exempel kommandot som kontrollerar om 5671-porten är blockerad.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    I Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot görs ett försök att upprätta 25 olika TCP-anslutningar varje sekund med tjänsten. Sedan kan du kontrol lera hur många av dem som har lyckats/misslyckats och även se TCP-anslutningssträngen. Du kan hämta `psping` verktyget härifrån. [here](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc` , `ping` och så vidare. 
- Få en nätverks spårning om föregående steg inte hjälper och analyseras med verktyg som [wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problem som kan uppstå med tjänst uppgraderingar/omstarter
Uppgraderingar och omstarter av backend-tjänsten kan orsaka följande påverkan på dina program:

- Begär Anden kan begränsas tillfälligt.
- Det kan finnas en minskning av inkommande meddelanden/begär Anden.
- Logg filen kan innehålla fel meddelanden.
- Programmen kan vara frånkopplade från tjänsten under några sekunder.

Om program koden använder SDK är principen för återförsök redan inbyggd och aktiv. Programmet återansluter utan betydande påverkan på programmet/arbets flödet.


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en Händelsehubben](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
