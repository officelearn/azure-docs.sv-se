---
title: Hur fungerar Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer använder maskininlärning att identifiera vilken åtgärd som ska användas i ett sammanhang. Varje learning slinga har en modell som har tränats exklusivt på data som du har skickat till den via rangordning och utmärkelse anropar. Varje learning loop är helt oberoende av varandra.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027133"
---
# <a name="how-personalizer-works"></a>Hur Personalizer fungerar

Personalizer använder maskininlärning att identifiera vilken åtgärd som ska användas i ett sammanhang. Varje learning slinga har en modell som har tränats exklusivt på data som du har skickat till det via **rangordning** och **utmärkelse** anrop. Varje learning loop är helt oberoende av varandra. Skapa en learning skapas för varje del eller beteendet för ditt program som du vill anpassa.

För varje loop **anropa rangordning API med** baserat på den aktuella kontexten med:

* Listan över möjliga åtgärder: objekt från att välja övre åtgärd.
* Lista över [kontext funktioner](concepts-features.md): sammanhangsmässigt relevanta data, till exempel användare, innehåll och sammanhang.

Den **rangordning** API bestämmer sig att använda antingen:

* _Utnyttja_: Den aktuella modellen för att avgöra den bästa åtgärd baserat på senaste data.
* _Utforska_: Välj en annan åtgärd i stället för den översta åtgärden.

Den **utmärkelse** API:

* Samlar in data för att träna modellen genom att spela in funktioner och utmärkelse poäng för varje rangordnas anrop.
* Använder dessa data för att uppdatera modellen baserat på inställningarna i den _Learning princip_.

## <a name="architecture"></a>Arkitektur

Följande bild visar arkitekturen flödet för att anropa rangordning samt belöningar anrop:

![alternativ text](./media/how-personalizer-works/personalization-how-it-works.png "så här fungerar för anpassning")

1. Personalizer använder en intern AI-modell för att fastställa rangordningen för åtgärden.
1. Tjänsten bestämmer sig för att utnyttja den aktuella modellen eller utforska nya alternativ för modellen.  
1. Rangordning resultatet skickas till EventHub.
1. När Personalizer får trafik, skickas trafik till EventHub. 
1. Rangordning och utmärkelse kopplas ihop.
1. AI-modellen uppdateras baserat på resultatet korrelation.
1. Inferens-motorn har uppdaterats med den nya modellen. 

## <a name="research-behind-personalizer"></a>Utvecklades Personalizer

Personalizer baseras på banbrytande vetenskap och forskning i området [förstärkande inlärning](concepts-reinforcement-learning.md) inklusive dokument, forskning och pågående delar av utforskning i Microsoft Research.

## <a name="terminology"></a>Terminologi

* **Learning Loop**: Du kan skapa en learning skapas för varje del av ditt program som kan dra nytta av anpassning. Om du har mer än en lösning för att anpassa kan skapa en loop för varje. 

* **Åtgärder**: Åtgärder är innehållsposter, till exempel produkter eller kampanjer att välja bland. Personalizer väljer den översta åtgärden ska visas för användarna, som ibland kallas den _belöna åtgärd_, via API: et för rangordning. Varje åtgärd kan ha funktioner som skickas med rangordnas begäran.

* **Kontext**: Innehåller information om din kontext, till exempel för att ge en mer exakta rangordning:
    * Dina användare.
    * Den enhet som de är anslutna. 
    * Den aktuella tiden.
    * Andra data om den aktuella situationen.
    * Historiska data för användaren eller kontext.

    Specifika programmet kan ha olika kontextinformation. 

* **[Funktioner](concepts-features.md)**: En enhet med information om ett innehållsobjekt eller en användarkontext.

* **Utmärkelse**: Ett mått på hur användaren svarade rangordning-API: et returnerade åtgärden som en poäng mellan 0 och 1. Värdet 0 till 1 anges av din affärslogik som baseras på hur valet hjälpte uppnå affärsmålen för anpassning. 

* **Utforskning**: Tjänsten Personalizer möjligheterna när istället för att returnera den bästa åtgärden den väljer en annan åtgärd för användaren. Tjänsten Personalizer undviker drift, stagnation, och kan anpassas till Pågående användarbeteende genom att utforska. 

* **Experimentlängd**: Hur lång tid som tjänsten Personalizer väntar ersättning, med början från den tidpunkt då rangordnas anropet har inträffat för händelsen.

* **Inaktiva händelser**: En inaktiv händelse är en där du kallas rankning, men du inte vet någonsin visas resultatet, på grund av klienten programmet beslut. Inaktiva händelser kan du skapa och lagra personanpassning resultatet och sedan bestämmer dig för att ignorera dem senare utan att påverka machine learning-modell.

* **Modellen**: En modell för Personalizer samlar in alla data som har lärt dig om användarnas beteende och hämta utbildningsdata från en kombination av de argument som du skickar till rangordning samt belöningar anrop och med ett beteende för utbildning som bestäms av Learning principen. 

## <a name="next-steps"></a>Nästa steg

Förstå [där du kan använda Personalizer](where-can-you-use-personalizer.md).