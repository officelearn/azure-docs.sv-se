---
title: Aktiv inlärning - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: d758e8fc7952a414003746d39df9368f3274b08b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481399"
---
# <a name="active-learning-and-learning-policies"></a>Aktiv inlärning och utbildning principer 

När programmet anropar API: et för rankning, får du en rangordningen för innehållet. Affärslogik kan använda rangordningen för att fastställa om innehållet ska visas för användaren. När du visar det översta innehållet, det vill säga en _active_ rangordnas händelse. När ditt program visas inte som rangordnas innehåll, det vill säga en _inaktiva_ rangordnas händelse. 

Aktiva rangordnas händelseinformation returneras till Personalizer. Den här informationen används för att fortsätta att träna modellen via den aktuella learning-principen.

## <a name="active-events"></a>Aktiva händelser

Aktiva händelser bör alltid att visas för användaren och utmärkelse anropet ska returneras till cirkeln learning. 

### <a name="inactive-events"></a>Inaktiva händelser 

Inaktiva händelser bör inte ändra den underliggande modellen eftersom användaren inte få möjlighet att välja från det översta innehållet.

## <a name="dont-train-with-inactive-rank-events"></a>Inte träna med inaktiva rangordnas händelser 

För vissa program, kan du behöva anropa rangordning API utan att känna ännu om ditt program visas resultaten för användaren. 

Det här inträffar när:

* Du kan redan återgivningen vissa användargränssnitt som användaren har eller inte kan ta emot att se. 
* Ditt program kan göra förutseende anpassning som rangordnas anrop görs med mindre i realtid kontext och sina utdata kan eller inte kan användas av programmet. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Inaktivera aktiv inlärning för inaktiva rangordnas händelser under rangordnas anrop

Inaktiverar automatisk inlärning, anropa rangordning med `learningEnabled = False`.

Utbildning för en inaktiv händelse aktiveras implicit om du skickar en ersättning för rangordningen.

## <a name="learning-policies"></a>Learning principer

Learning princip anger specifikt *hyperparametrar* modell-utbildning. Två modeller av samma data, tränats på olika learning principer fungerar på olika sätt.

### <a name="importing-and-exporting-learning-policies"></a>Importera och exportera Learning principer

Du kan importera och exportera learning principfiler från Azure-portalen. På så sätt kan du spara befintliga principer, testa dem, ersätta dem och arkivera dem i din källkodskontroll som artefakter för framtida bruk och granskning.

### <a name="learning-policy-settings"></a>Learning principinställningar

Inställningarna i den **Learning princip** är inte avsedda att ändras. Endast ändra inställningarna när du förstår hur de påverkar Personalizer. Ändra inställningar utan den här kunskapen genereras sidoeffekter, inklusive ogiltigförklara Personalizer modeller.

### <a name="comparing-effectiveness-of-learning-policies"></a>Jämföra effektivitet learning principer

Du kan jämföra hur olika principer för Learning skulle ha utfört mot de senaste data i Personalizer loggar rent praktiskt [offline utvärderingar](concepts-offline-evaluation.md).

[Ladda upp dina egna Learning principer](how-to-offline-evaluation.md) ska jämföras med den aktuella learning-principen.

### <a name="discovery-of-optimized-learning-policies"></a>Identifiering av optimerade learning principer

Personalizer kan skapa en mer optimerade learning princip när du gör en [offline utvärdering](how-to-offline-evaluation.md). En mer optimerade learning princip som visas har bättre fördelarna i en offline utvärdering, kommer att ge bättre resultat när det används online i Personalizer.

När du har skapat en princip för optimerad learning tillämpa du den direkt till Personalizer så att den ersätter den aktuella principen direkt eller du kan spara den för framtida utvärderingar och i framtiden bestämma om du vill ta bort, spara eller använda det senare.