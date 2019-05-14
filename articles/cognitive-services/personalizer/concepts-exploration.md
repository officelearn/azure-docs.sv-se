---
title: Utforskning - Personalizer
titleSuffix: Azure Cognitive Services
description: Med undersökning är Personalizer kan fortsätta leverera bra resultat, även när användarbeteende ändras. Att välja en inställning för utforskning är ett affärsbeslut om andelen användarinteraktioner att utforska med, för att förbättra modellen.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: 0e11dd962f4cc1ff9b1938b309a1b405dbf20232
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607072"
---
# <a name="exploration-and-exploitation"></a>Utforskning och utnyttjande

Med undersökning är Personalizer kan fortsätta leverera bra resultat, även när användarbeteende ändras.

När Personalizer tar emot ett rangordnas anrop, returneras en RewardActionID som antingen:
* Använder utnyttjande för att matcha den mest sannolika användarnas beteende baserat på den aktuella machine learning-modellen.
* Använder undersökning, vilket inte matchar den åtgärd som har det bästa skyddet i rangordningen.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Personalizer för närvarande använder en algoritm som kallas *epsilon girig* att utforska. 

## <a name="choosing-an-exploration-setting"></a>Välja en inställning för Kunskapsutveckling

Du konfigurerar procentandel av trafiken för Kunskapsutveckling i Azure portal **inställningar** för Personalizer. Den här inställningen anger procentandelen rangordnas anrop som utför utforskning. 

Personalizer avgör om du vill utforska eller utnyttja med sannolikheten på varje rangordnas anrop. Detta skiljer sig från beteendet i vissa A / B-ramverk som låser en behandling på specifika användar-ID.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Bästa metoder när du väljer en inställning för Kunskapsutveckling

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

Att välja en inställning för utforskning är ett affärsbeslut om andelen användarinteraktioner att utforska med, för att förbättra modellen. 

Inställningen noll ska negeras många av fördelarna med Personalizer. Med den här inställningen använder Personalizer inga användarinteraktioner för att upptäcka bättre användarinteraktioner. Detta leder till modellen stagnation, drift och slutligen lägre prestanda.

En inställning för hög ska negeras fördelarna med att lära användarbeteende. Ange värdet till 100% innebär en konstant slumpmässig och eventuella inlärda beteende hos användare inte skulle påverka resultatet.

Det är viktigt att inte ändra programmets beteende baserat på om du ser om Personalizer utforska eller utnyttjar. Detta skulle leda till learning eventuella fördomar som slutligen skulle minska potentiella prestanda.

## <a name="next-steps"></a>Nästa steg

[Förstärkande inlärning](concepts-reinforcement-learning.md) 