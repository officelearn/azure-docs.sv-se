---
title: inkludera fil
description: inkludera fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "77279531"
---
Klientprogrammet behöver veta om ett yttrande inte är meningsfullt eller lämpligt för programmet. **Ingen** avsikt har lagts till i varje program som en del i skapande processen för att avgöra om en uttryck inte ska besvaras av klient programmet.

Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen eller ge fler anvisningar för att fortsätta konversationen.

Om du lämnar **none** -avsikten tom, kommer en uttryck som ska förutsägas utanför ämnes domänen att förutsägas i ett av de befintliga ämnes domänernas avsikter. Resultatet är att klient programmet, till exempel en chat-robot, utför felaktiga åtgärder baserat på felaktig förutsägelse.

1. Välj **Intents** (Avsikter) på den vänstra panelen.

1. Välj avsikten **None** (Ingen). Lägg till tre yttranden som användaren kan ange men som inte är relevanta för din pizza-ordning:

    |`None` exempel yttranden|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Dessa exempel bör inte använda ord som du förväntar dig i din ämnes domän, till exempel `pizza` ,, `cheese` `crust` , `pickup` `deliver` .