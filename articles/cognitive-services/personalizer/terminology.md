---
title: Terminologi - Personalizer
description: Personalizer använder terminologi från förstärkning lärande. Dessa termer används i Azure-portalen och API:erna.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624279"
---
# <a name="terminology"></a>Terminologi

Personalizer använder terminologi från förstärkning lärande. Dessa termer används i Azure-portalen och API:erna.

## <a name="conceptual-terminology"></a>Begreppsmässig terminologi

* **Learning Loop:** Du skapar en Personalizer-resurs, som kallas _en inlärningsloop,_ för varje del av ditt program som kan dra nytta av anpassning. Om du har mer än en upplevelse att anpassa skapar du en loop för varje.

* **Modell:** En Personalizer-modell samlar in alla data som lärs ut om användarbeteende, får träningsdata från kombinationen av de argument du skickar till Rank and Reward-samtal och med ett träningsbeteende som bestäms av utbildningsprincipen.

## <a name="personalizer-configuration"></a>Konfiguration av Personalizer

Personalizer är konfigurerad från [Azure-portalen](https://portal.azure.com).

* **Belöningar:** konfigurera standardvärdena för belöningsväntetid, standardbelöning och belöningsaggregeringsprincip.

* **Utforskning:** konfigurera procentandelen Rank-anrop som ska användas för prospektering

* **Modelluppdateringsfrekvens**: Hur ofta modellen tränas om.

* **Datalagring**: Hur många dagar av data att lagra. Detta kan påverka offlineutvärderingar, som används för att förbättra din inlärningsloop.

## <a name="use-rank-and-reward-apis"></a>Använd ranknings- och belönings-API:er

* **Rank**: Med tanke på åtgärderna med funktioner och sammanhangsfunktioner, använd utforska eller utnyttja för att returnera den översta åtgärden (innehållsobjekt).

    * **Åtgärder**: Åtgärder är innehållsobjekt, till exempel produkter eller kampanjer, att välja mellan. Personalizer väljer den översta åtgärden (returnerade belöning åtgärd ID) för att visa för dina användare via Rank API.

    * **Sammanhang**: För att ge en mer exakt rang, ge information om ditt sammanhang, till exempel:
        * Din användare.
        * Enheten de är på.
        * Den aktuella tiden.
        * Andra uppgifter om den aktuella situationen.
        * Historiska data om användaren eller kontexten.

        Ditt specifika program kan ha annan kontextinformation.

    * **[Funktioner](concepts-features.md)**: En informationsenhet om ett innehållsobjekt eller en användarkontext. Se till att bara använda funktioner som är aggregerade. Använd inte specifika tider, användar-ID:er eller andra icke-aggregerade data som funktioner.

        * En _åtgärdsfunktion_ är metadata om innehållet.
        * En _kontextfunktion_ är metadata om den kontext där innehållet presenteras.

* **Utforskning:** Personalizer-tjänsten utforskar när, istället för att returnera den bästa åtgärden, väljer den en annan åtgärd för användaren. Personalizer-tjänsten undviker drift, stagnation och kan anpassa sig till pågående användarbeteende genom att utforska.

* **Utnyttjande**: Personalizer-tjänsten använder den aktuella modellen för att bestämma den bästa åtgärden baserat på tidigare data.

* **Experimentvaraktighet**: Den tid som Personalizer-tjänsten väntar på en belöning, från det ögonblick då rank-samtalet inträffade för den händelsen.

* **Inaktiva händelser**: En inaktiv händelse är en händelse där du ringde Rank, men du är inte säker på att användaren någonsin kommer att se resultatet, på grund av beslut om klientprogram. Med inaktiva händelser kan du skapa och lagra anpassningsresultat och sedan välja att ignorera dem senare utan att påverka maskininlärningsmodellen.


* **Belöning:** Ett mått på hur användaren svarade på Rank API: s returnerade belöning åtgärd id, som en poäng mellan 0 till 1. Värdet 0 till 1 anges av din affärslogik, baserat på hur valet hjälpte till att uppnå dina affärsmål om personalisering. Inlärningsloopen lagrar inte den här belöningen som individuell användarhistorik.

## <a name="offline-evaluations"></a>Offlineutvärderingar

* **Utvärdering**: En offlineutvärdering avgör den bästa inlärningspolicyn för din loop baserat på loopens data.

* **Utbildningspolitik**: Hur Personalizer tränar en modell på varje händelse kommer att avgöras av vissa parametrar som påverkar hur maskininlärningsalgoritmen fungerar. En ny inlärningsloop börjar med en **standardutbildningsprincip**, som kan ge måttlig prestanda. När Du kör [Utvärderingar](concepts-offline-evaluation.md)skapar Personalizer nya utbildningsprinciper som är särskilt optimerade för användningsfallen i loopen. Personalizer kommer att prestera betydligt bättre med principer som är optimerade för varje specifik loop, som genereras under utvärderingen. Utbildningsprincipen heter _utbildningsinställningar_ på **modell- och utbildningsinställningarna** för Personalizer-resursen i Azure-portalen.