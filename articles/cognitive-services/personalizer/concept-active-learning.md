---
title: Aktiv inlärning – Personanpassare
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663716"
---
# <a name="active-learning-and-learning-policies"></a>Aktiva principer för inlärning och inlärning 

När programmet anropar rang-API: et får du en rangordning av innehållet. Affärs logik kan använda den här rangordningen för att avgöra om innehållet ska visas för användaren. När du visar det rankade innehållet, är det ett _aktivt_ rang-händelse. När programmet inte visar det rankade innehållet, är det ett inaktivt rang-händelse. 

Information om aktiva ranknings händelser returneras till Personanpassaren. Den här informationen används för att fortsätta träna modellen genom den aktuella inlärnings principen.

## <a name="active-events"></a>Aktiva händelser

Aktiva händelser bör alltid visas för användaren och belönings anropet ska returneras för att stänga inlärnings slingan. 

### <a name="inactive-events"></a>Inaktiva händelser 

Inaktiva händelser bör inte ändra den underliggande modellen eftersom användaren inte fick möjlighet att välja från det rankade innehållet.

## <a name="dont-train-with-inactive-rank-events"></a>Träna inte med inaktiva ranknings händelser 

För vissa program kan du behöva anropa rang-API: et utan att veta om ditt program kommer att visa resultatet för användaren. 

Detta händer när:

* Du kanske för hands återger ett användar gränssnitt som användaren kanske inte kan se. 
* Ditt program kan göra en förutsägelse anpassning i vilka Rangbaserade anrop görs med mindre real tids sammanhang och deras utdata kan eventuellt inte användas av programmet. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Inaktivera aktiv inlärning för inaktiva ranknings händelser under rang anrop

Om du vill inaktivera automatisk inlärning, anropa `learningEnabled = False`rangordning med.

Inlärningen för en inaktiv händelse aktive ras implicit om du skickar en belöning för rankningen.

## <a name="learning-policies"></a>Utbildnings principer

Inlärnings policyn avgör de speciella *disponeringsparametrarna* för modell träningen. Två modeller av samma data, tränade på olika inlärnings principer, fungerar annorlunda.

### <a name="importing-and-exporting-learning-policies"></a>Importera och exportera utbildnings principer

Du kan importera och exportera learning policy-filer från Azure Portal. På så sätt kan du spara befintliga principer, testa dem, ersätta dem och arkivera dem i käll kods kontrollen som artefakter för framtida referens och granskning.

### <a name="learning-policy-settings"></a>Policy inställningar för inlärning

Inställningarna i inlärnings **principen** är inte avsedda att ändras. Ändra bara inställningarna när du förstår hur de påverkar Personanpassaren. Om du ändrar inställningar utan den här kunskapen kan det orsaka sido effekter, inklusive invalidering av anpassnings modeller.

### <a name="comparing-effectiveness-of-learning-policies"></a>Jämför effektiviteten för utbildnings principer

Du kan jämföra hur olika inlärnings principer har utförts mot tidigare data i personanpassa loggar genom att göra [offline](concepts-offline-evaluation.md)-utvärderingar.

[Ladda upp dina egna utbildnings principer](how-to-offline-evaluation.md) för att jämföra med den aktuella inlärnings policyn.

### <a name="discovery-of-optimized-learning-policies"></a>Identifiering av optimerade utbildnings principer

En personanpassare kan skapa en mer optimerad inlärnings princip när du gör en [offline-utvärdering](how-to-offline-evaluation.md). En mer optimerad inlärnings policy, som visas som en bättre belöning i en offline-utvärdering, ger bättre resultat när de används online i Personanpassaren.

När en optimerad inlärnings princip har skapats kan du tillämpa den direkt på en Personligre så att den ersätter den aktuella principen omedelbart, eller så kan du spara den för ytterligare utvärdering och besluta i framtiden om du vill ta bort, Spara eller tillämpa den senare.