---
title: Utforskning – Personanpassare
titleSuffix: Azure Cognitive Services
description: Med utforskningen kan du fortsätta att leverera bättre resultat, även om användar beteendet ändras. Att välja en utforsknings inställning är ett affärs beslut om den andel av användar interaktioner som kan utforskas med, för att förbättra modellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.openlocfilehash: cfecea6a64301d86aa657420dc300c26d4ed6f1e
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663400"
---
# <a name="exploration-and-exploitation"></a>Utforskning och utnyttjande

Med utforskningen kan du fortsätta att leverera bättre resultat, även om användar beteendet ändras.

När en Personanpassare får ett rang anrop, returnerar den en RewardActionID som antingen:
* Använder utnyttjande för att matcha det mest sannolika användar beteendet baserat på den aktuella Machine Learning-modellen.
* Använder utforskning, som inte matchar den åtgärd som har högst sannolikhet i rangordningen.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
I en personanpassare används en algoritm som heter *Epsilon girig* för att utforska. 

## <a name="choosing-an-exploration-setting"></a>Välja en utforsknings inställning

Du konfigurerar den procent andel av trafiken som ska användas för utforskning på Azure Portals inställnings sida för personanpassare. Den här inställningen anger procent andelen rang anrop som utför utforskning. 

Personanpassaren bestämmer om du vill utforska eller utnyttja sannolikheten för varje rang anrop. Detta skiljer sig från beteendet i vissa A/B-ramverk som låser en behandling på vissa användar-ID: n.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Metod tips för att välja en utforsknings inställning

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

Att välja en utforsknings inställning är ett affärs beslut om den andel av användar interaktioner som kan utforskas med, för att förbättra modellen. 

En inställning på noll kommer att negera många av fördelarna med en Personanpassare. Med den här inställningen använder Personanpassaren inga användar interaktioner för att upptäcka bättre användar interaktioner. Detta leder till modell stagnation, drift, och slutligen lägre prestanda.

En inställning som är för hög, kommer att negera fördelarna med inlärning från användar beteendet. Att ställa in det på 100% innebär en konstant slumpmässig het och alla inlärda beteenden från användarna påverkar inte resultatet.

Det är viktigt att inte ändra program beteendet beroende på om du ser om en Personanpassare utforskar eller utnyttjar. Detta leder till inlärnings fördomar som i slut ändan skulle minska den potentiella prestandan.

## <a name="next-steps"></a>Nästa steg

[Förstärka inlärning](concepts-reinforcement-learning.md) 