---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 08328b3c872ce9e8a25db6075b00e0cee2fdf4fd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480343"
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
