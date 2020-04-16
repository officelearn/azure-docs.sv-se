---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400806"
---
Prenumerationsnyckeln och regionen Talresurs krävs för att skapa ett talkonfigurationsobjekt. Konfigurationsobjektet behövs för att instansiera ett talidentifierareobjekt.

Den igenkännings-instansen visar flera sätt att känna igen tal. I det här exemplet känns tal igen en gång. Med den här funktionen kan taltjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta känna igen tal. När resultatet har getts skriver koden igenkänningsorsaken till konsolen.

> [!TIP]
> Tal-SDK kommer som `en-us` standard att känna igen med hjälp av språket, se [Ange källspråk för tal till text](../../../how-to-specify-source-language.md) för information om hur du väljer källspråk.