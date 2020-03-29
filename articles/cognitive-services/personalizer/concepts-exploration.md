---
title: Utforskning - Personalizer
titleSuffix: Azure Cognitive Services
description: Med utforskning kan Personalizer fortsätta leverera goda resultat, även när användarbeteendet ändras. Att välja en prospekteringsinställning är ett affärsbeslut om andelen användarinteraktioner att utforska med, för att förbättra modellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73490821"
---
# <a name="exploration-and-exploitation"></a>Prospektering och exploatering

Med utforskning kan Personalizer fortsätta leverera goda resultat, även när användarbeteendet ändras.

När Personalizer tar emot ett Rank-samtal returneras ett RewardActionID som antingen:
* Använder utnyttjande för att matcha det mest sannolika användarbeteendet baserat på den aktuella maskininlärningsmodellen.
* Använder utforskning, som inte matchar den åtgärd som har den högsta sannolikheten i rangordningen.

Personalizer använder för närvarande en algoritm som kallas *epsilon girig* att utforska. 

## <a name="choosing-an-exploration-setting"></a>Välja en utforskningsinställning

Du konfigurerar procentandelen trafik som ska användas för utforskning på Azure-portalens **konfigurationssida** för Personalizer. Den här inställningen bestämmer procentandelen rankanrop som utför utforskning. 

Personalizer avgör om att utforska eller utnyttja med denna sannolikhet på varje rang samtal. Detta skiljer sig från beteendet i vissa A/B-ramverk som låser en behandling på specifika användar-ID:er.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Metodtips för att välja en utforskningsinställning

Att välja en prospekteringsinställning är ett affärsbeslut om andelen användarinteraktioner att utforska med, för att förbättra modellen. 

En inställning på noll kommer att förneka många av fördelarna med Personalizer. Med den här inställningen använder Personalizer inga användarinteraktioner för att upptäcka bättre användarinteraktioner. Detta leder till modell stagnation, drift, och i slutändan lägre prestanda.

En inställning som är för hög kommer att förneka fördelarna med att lära av användarbeteende. Att ställa in den på 100 % innebär en konstant randomisering, och alla inlärda beteenden från användare skulle inte påverka resultatet.

Det är viktigt att inte ändra programmets beteende baserat på om du ser om Personalizer utforskar eller utnyttjar. Detta skulle leda till lärande fördomar som i slutändan skulle minska den potentiella prestanda.

## <a name="next-steps"></a>Nästa steg

[Kunskapsförmedling](concepts-reinforcement-learning.md) 