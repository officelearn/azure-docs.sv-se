---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0579b1e21e714e25e197cb5abf46793a38978d06
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53755720"
---
Klientprogrammet behöver veta om ett yttrande inte är meningsfullt eller lämpligt för programmet. Avsikten **Ingen** läggs till i varje program som en del av skapandeprocessen för att avgöra om ett yttrande inte kan besvaras av klientprogrammet.

Om LUIS returnerar avsikten **Ingen** för ett yttrande kan klientprogrammet fråga om användaren vill avsluta konversationen eller ge fler anvisningar för att fortsätta konversationen. 

> [!CAUTION] 
> Lämna inte avsikten **Ingen** tom. 

1. Välj **Intents** (Avsikter) på den vänstra panelen.

2. Välj avsikten **None** (Ingen). Lägg till tre yttranden som användarna kan tänkas ange men som inte är relevanta för Human Resources-appen:

    | Exempel på yttranden|
    |--|
    |Skällande hundar är irriterande|
    |Beställ en pizza åt mig|
    |Pingviner i havet|