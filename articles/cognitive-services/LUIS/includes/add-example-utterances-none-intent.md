---
title: ta med fil
description: ta med fil
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279531"
---
Klientprogrammet behöver veta om ett yttrande inte är meningsfullt eller lämpligt för programmet. **Ingen** avsikt läggs till varje program som en del av skapandet processen för att avgöra om ett uttryck inte bör besvaras av klientprogrammet.

Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen eller ge fler anvisningar för att fortsätta konversationen.

Om du lämnar **ingen** avsikt tom, en yttrande som bör förutsägas utanför ämnesdomänen kommer att förutsägas i en av de befintliga ämnesdomän avsikter. Resultatet är att klientprogrammet, till exempel en chattrobot, kommer att utföra felaktiga åtgärder baserat på en felaktig förutsägelse.

1. Välj **Intents** (Avsikter) på den vänstra panelen.

1. Välj avsikten **None** (Ingen). Lägg till tre yttranden som användaren kan ange men som inte är relevanta för din pizzabeställningsapp:

    |`None`exempel yttranden|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    De här exemplen bör inte använda ord `pizza`som `cheese` `crust`du `pickup` `deliver`förväntar dig i ämnesdomänen, till exempel , , , .