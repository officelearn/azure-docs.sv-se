---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325925"
---
Klientprogrammet behöver veta om ett yttrande inte är meningsfullt eller lämpligt för programmet. **Ingen** avsikt har lagts till i varje program som en del i skapande processen för att avgöra om en uttryck inte ska besvaras av klient programmet.

Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen eller ge fler anvisningar för att fortsätta konversationen. 

Om du lämnar **none** -avsikten tom, kommer en uttryck som ska förutsägas utanför ämnes domänen att förutsägas i ett av de befintliga ämnes domänernas avsikter. Resultatet är att klient programmet, till exempel en chat-robot, utför felaktiga åtgärder baserat på felaktig förutsägelse. 

1. Välj **Intents** (Avsikter) på den vänstra panelen.

1. Välj avsikten **None** (Ingen). Lägg till tre yttranden som användaren kan ange men som inte är relevanta för din pizza-ordning:

    |`None` exempel yttranden|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    Dessa exempel bör inte använda ord som du förväntar dig i din ämnes domän, till exempel `pizza`, `cheese`, `crust``pickup` `deliver`.