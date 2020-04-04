---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638108"
---
Prenumerationsnyckeln och regionen Talresurs krävs för att skapa ett talkonfigurationsobjekt. Konfigurationsobjektet behövs för att instansiera ett talidentifierareobjekt.

Den igenkännings-instansen visar flera sätt att känna igen tal. I det här exemplet känns tal igen en gång. Med den här funktionen kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal. När resultatet har getts skriver koden igenkänningsorsaken till konsolen.

> [!TIP]
> Tal-SDK kommer som `en-us` standard att känna igen med hjälp av språket, se [Ange källspråk för tal till text](../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.