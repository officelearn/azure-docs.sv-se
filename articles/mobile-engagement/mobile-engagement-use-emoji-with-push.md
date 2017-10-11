---
title: "Använda Emoji uttryckssymboler i Azure Mobile Engagement"
description: "Hur du använder Emoji uttryckssymboler inom push-meddelanden"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 663317d7-3c93-4e8f-b13d-c6fb342124ee
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: bbb7ce5e95b229a7505c5e97b6866d5a302a1d27
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-emoji-emoticon-within-push-notifications"></a>Använd Emoji uttryckssymbol inom Push-meddelanden
Du kan inkludera Emoji uttryckssymboler i du push-meddelanden i några enkla steg: 

1. Först av allt du behöver för att hitta Emoji vill du skicka meddelandet. Kontrollera att Emoji som du väljer kommer att stödas av målenheten som enheten tillverkar ta lite tid att lägga till nya godkända Emojis plattformar för enheten. 
2. På **Windows** -du kan navigera till den här [länk](http://apps.timwhitlock.info/emoji/tables/unicode) och kopiera ikonen ”inbyggd”.
   
    ![][7] 
3. På **Mac** -du kan hitta Emojis i ordlistan program under Redigera -> Emoji & symboler.
   
    ![][6] 
4. Gå till den **nå** fliken på den Azure Mobile Engagement-portalen. Välj typ av push-meddelande (meddelande, avsöker osv.). Det här exemplet väljer vi ett meddelande push.
5. Ange olika fält för meddelandet tills du når texten i meddelandet. Det är där du ska lägga till uttryckssymbolen Emoji. Du kan välja att placera den i rubriken, meddelandet eller båda. Du måste dra och släppa eller kopiera Emoji som finns på platser som ovan. 
   
    ![][1]
6. Fyll i fälten för meddelandet och spara den. 
7. När du kör ett test eller aktiverar ett meddelande visas ett meddelande med uttryckssymbolen som anges.   
   
    ![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

