---
title: Vad är Personanpassning?
description: Personalizer är en molnbaserad API-tjänst som låter dig välja den bästa upplevelsen att visa för dina användare, lära av deras realtidsbeteende.
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 3ae425479d764c0a6bf6c63bdd54a964c48af8b6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687266"
---
# <a name="what-is-personalizer"></a>Vad är Personanpassning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Personalizer är en molnbaserad API-tjänst som hjälper _content_ ditt klientprogram att välja det bästa, enskilda innehållsobjektet för att visa varje användare. Tjänsten väljer det bästa objektet, från innehållsobjekt, baserat på kollektiv realtidsinformation som du anger om innehåll och kontext.

När du har presenterat innehållsobjektet för användaren övervakar systemet användarnas beteende och rapporterar tillbaka en belöningspoäng till Personalizer för att förbättra dess förmåga att välja det bästa innehållet baserat på den kontextinformation som tas emot.

**Innehåll** kan vara vilken informationsenhet som helst, till exempel text, bilder, webbadresser eller e-postmeddelanden som du vill välja mellan för att visa för din användare.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hur väljer Personalizer det bästa innehållsobjektet?

Personalizer använder **förstärkningsinlärning** för att välja det bästa objektet (_åtgärd_) baserat på kollektivt beteende och belöningspoäng för alla användare. Åtgärder är innehållsobjekt, till exempel nyhetsartiklar, specifika filmer eller produkter att välja mellan.

**Rank-anropet** tar åtgärdsobjektet tillsammans med funktioner i åtgärden och sammanhangsfunktioner för att välja det översta åtgärdsobjektet:

* **Åtgärder med funktioner** - innehållsobjekt med funktioner som är specifika för varje objekt
* **Kontextfunktioner** – användarnas funktioner, deras kontext eller miljö när de använder appen

Rank-anropet returnerar ID:n för vilket innehållsobjekt, __åtgärd__, som ska visas för användaren, i fältet **Belöningsåtgärds-ID.**
Åtgärden __som__ visas för användaren väljs med maskininlärningsmodeller som försöker maximera den totala mängden belöningar över tid.

Flera exempel scenarier är:

|Innehållstyp|**Åtgärder (med funktioner)**|**Sammanhangsfunktioner**|Åtgärds-ID för returnerad belöning<br>(visa det här innehållet)|
|--|--|--|--|
|Nyhetslista|a. `The president...`(nationellt, politik, [text])<br>b. `Premier League ...`(global, sport, [text, bild, video])<br> c. `Hurricane in the ...`(regional, väder, [text,bild]|Enhetsnyheter läss från<br>Månad eller säsong<br>|A`The president...`|
|Lista över filmer|1. `Star Wars` (1977, [handling, äventyr, fantasi], George Lucas)<br>2. `Hoop Dreams` (1994, [dokumentär, sport], Steve James<br>3. `Casablanca` (1942, [romantik, drama, krig], Michael Curtiz)|Enhetsfilmen visas från<br>skärmstorlek<br>Typ av användare<br>|3.`Casablanca`|
|Lista över produkter|i. `Product A`(3 kg, $$$$, leverera på 24 timmar)<br>ii. `Product B`(20 kg, $$, 2 veckors frakt med tullen)<br>iii. `Product C`(3 kg, $$$, leverans i 48 timmar)|Enhetsshopping läss från<br>Utgiftsnivå för användare<br>Månad eller säsong|ii. `Product B`|

Personalizer används förstärkning lärande för att välja den enskilt bästa åtgärden, känd som _belöning åtgärd ID_, baserat på en kombination av:
* Tränad modell - tidigare information som Personalizer-tjänsten fick
* Aktuella data - Specifika åtgärder med funktioner och sammanhangsfunktioner

## <a name="when-to-call-personalizer"></a>När ska personalizer ringa

Personalizer's **Rank** [API](https://go.microsoft.com/fwlink/?linkid=2092082) anropas varje _gång_ du presenterar innehåll, i realtid. Detta kallas en **händelse**, som noteras med ett _händelse-ID_.

Personalizer's **Reward** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) kan anropas i realtid eller försenas för att bättre passa din infrastruktur. Du bestämmer belöningspoängen baserat på dina affärsbehov. Belöningspoängen är mellan 0 och 1. Det kan vara ett enda värde som 1 för gott och 0 för dåligt, eller ett nummer som produceras av en algoritm som du skapar med tanke på dina affärsmål och mått.

## <a name="personalizer-content-requirements"></a>Krav på personaliserarinnehåll

Använd Personalizer när ditt innehåll:

* Har en begränsad uppsättning objekt (max ~50) att välja mellan. Om du har en större lista [använder du en rekommendationsmotor](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) för att minska listan ned till 50 objekt.
* Har information som beskriver det innehåll du vill ha rangordnat: _åtgärder med funktioner_ och _sammanhangsfunktioner_.
* Har minst ~ 1k/day innehållsrelaterade händelser för Personalizer att vara effektiv. Om Personalizer inte får den minsta trafik som krävs tar tjänsten längre tid att bestämma det enskilt bästa innehållsobjektet.

Eftersom Personalizer använder kollektiv information i nära realtid för att returnera det enskilt bästa innehållsobjektet, gör tjänsten inte:
* Bevara och hantera information om användarprofiler
* Logga enskilda användares inställningar eller historik
* Kräv rensat och märkt innehåll

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Så här utformar och implementerar du Personalizer för ditt klientprogram

1. [Designa](concepts-features.md) och planera för innehåll, **_åtgärder_** och **_kontext_**. Bestäm belöningsalgoritmen för **_belöningspoängen._**
1. Varje [Personalizer-resurs](how-to-settings.md) som du skapar anses vara 1 Utbildningsloop. Loopen tar emot både Rank- och Reward-samtalen för innehållet eller användarupplevelsen.
1. Lägg till Personalizer på din webbplats eller ditt innehållssystem:
    1. Lägg till ett **rank-samtal** till Personalizer i ditt program, din webbplats eller ditt system för att avgöra det bästa, enskilda _innehållsobjektet_ innan innehållet visas för användaren.
    1. Visa bästa, _content_ enda innehållsobjekt, som är det returnerade _belöningsåtgärds-ID:_ n, för användaren.
    1. Använd _algoritm_ för insamlad information om hur användaren uppförde sig, för att bestämma **belöningspoängen,** till exempel:

        |Beteende|Beräknad belöningspoäng|
        |--|--|
        |Användaren valt bäst, ett enda _innehållsobjekt_ (belöningsåtgärds-ID)|**1**|
        |Användarvalt annat innehåll|**0**|
        |Användaren pausade, bläddra runt obeslutsamt, innan du väljer bästa, enda _innehåll_ objekt (belöning åtgärd ID)|**0,5**|

    1. Lägg till ett **belöningssamtal** som skickar en belöningspoäng mellan 0 och 1
        * Omedelbart efter att du visat ditt innehåll
        * Eller någon gång senare i ett offline-system
    1. [Utvärdera loopen](concepts-offline-evaluation.md) med en offlineutvärdering efter en användningsperiod. En offlineutvärdering gör att du kan testa och bedöma personalizer-tjänstens effektivitet utan att ändra koden eller påverka användarupplevelsen.

## <a name="next-steps"></a>Nästa steg


* [Så här fungerar Personanpassning](how-personalizer-works.md)
* [Vad är Reinforcement Learning?](concepts-reinforcement-learning.md)
* [Läs mer om funktioner och åtgärder för Rank-begäran](concepts-features.md)
* [Läs mer om hur du bestämmer poängen för belöningsbegäran](concept-rewards.md)
* [Snabbstarter](sdk-learning-loop.md)
* [Självstudier](tutorial-use-azure-notebook-generate-loop-data.md)
* [Använd den interaktiva demon](https://personalizationdemo.azurewebsites.net/)
