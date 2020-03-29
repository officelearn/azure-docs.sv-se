---
title: Hur Personalizer Fungerar - Personalizer
description: _Personalizer-loopen_ använder maskininlärning för att skapa modellen som förutsäger den översta åtgärden för ditt innehåll. Modellen tränas uteslutande på dina data som du skickade till den med Rank and Reward-samtalen.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623754"
---
# <a name="how-personalizer-works"></a>Så här fungerar Personanpassning

Personalizer-resursen, din _inlärningsslinga,_ använder maskininlärning för att skapa modellen som förutsäger den översta åtgärden för ditt innehåll. Modellen tränas uteslutande på dina data som du skickade till den med **Rank and** **Reward-samtalen.** Varje slinga är helt oberoende av varandra.

## <a name="rank-and-reward-apis-impact-the-model"></a>Rank- och belönings-API:er påverkar modellen

Du skickar _åtgärder med funktioner_ och _kontextfunktioner_ till Rank API. **Rank** API beslutar att använda antingen:

* _Utnyttja_: Den nuvarande modellen för att avgöra den bästa åtgärden baserat på tidigare data.
* _Utforska_: Välj en annan åtgärd i stället för den översta åtgärden. Du [konfigurerar den här procentsatsen](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) för din Personalizer-resurs i Azure-portalen.

Du bestämmer belöningspoängen och skickar den poängen till belönings-API:et. Belönings-API:et: **Reward**

* Samlar in data för att träna modellen genom att registrera funktionerna och belöningspoängen för varje rankningsanrop.
* Använder dessa data för att uppdatera modellen baserat på den konfiguration som anges i _utbildningsprincipen_.

## <a name="your-system-calling-personalizer"></a>Ditt system ringer Personalizer

Följande bild visar det arkitektoniska flödet av att anropa rank- och belöningssamtalen:

![alternativ text](./media/how-personalizer-works/personalization-how-it-works.png "Så här fungerar anpassning")

1. Du skickar _åtgärder med funktioner_ och _kontextfunktioner_ till Rank API.

    * Personalizer bestämmer om att utnyttja den nuvarande modellen eller utforska nya val för modellen.
    * Rankningsresultatet skickas till EventHub.
1. Den översta rankningen returneras till ditt system som _belöningsåtgärds-ID._
    Ditt system presenterar det innehållet och bestämmer en belöningspoäng baserat på dina egna affärsregler.
1. Ditt system returnerar belöningspoängen till inlärningsslingan.
    * När Personalizer får belöningen skickas belöningen till EventHub.
    * Rang och belöning är korrelerade.
    * AI-modellen uppdateras baserat på korrelationsresultaten.
    * Inferensmotorn uppdateras med den nya modellen.

## <a name="personalizer-retrains-your-model"></a>Personalizer begränsar din modell

Personalizer begränsar din modell baserat på din **modellfrekvensuppdateringsinställning** på din Personalizer-resurs i Azure-portalen.

Personalizer använder alla data som för närvarande lagras, baserat på inställningen **Datakvarhållning** i antal dagar på din Personalizer-resurs i Azure-portalen.

## <a name="research-behind-personalizer"></a>Forskning bakom Personalizer

Personalizer bygger på spetsforskning och forskning inom [området Reinforcement Learning,](concepts-reinforcement-learning.md) inklusive uppsatser, forskningsverksamhet och pågående områden för utforskning inom Microsoft Research.

## <a name="next-steps"></a>Nästa steg

Läs mer om [de bästa scenarierna](where-can-you-use-personalizer.md) för Personalizer