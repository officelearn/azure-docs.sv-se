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
Klientprogrammet behöver veta om ett yttrande inte är meningsfullt eller lämpligt för programmet. The **None** intent is added to each application as part of the creation process to determine if an utterance shouldn't be answered by the client application.

Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen eller ge fler anvisningar för att fortsätta konversationen. 

If you leave the **None** intent empty, an utterance that should be predicted outside the subject domain will be predicted in one of the existing subject domain intents. The result is that the client application, such as a chat bot, will perform incorrect operations based on an incorrect prediction. 

1. Välj **Intents** (Avsikter) på den vänstra panelen.

1. Välj avsikten **None** (Ingen). Add three utterances that your user might enter but are not relevant to your Pizza ordering app:

    |`None` example utterances|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    These examples shouldn't use words you expect in your subject domain such as `pizza`, `cheese`, `crust`, `pickup` `deliver`.