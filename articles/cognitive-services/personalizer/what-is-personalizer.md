---
title: Vad är Personanpassning?
description: Personanpassar är en molnbaserad API-tjänst som gör att du kan välja den bästa upplevelsen som ska visas för användarna, lära sig från deras real tids beteende.
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: cf046ada21c4920ea9e3853668a5928b2ca9f33a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586226"
---
# <a name="what-is-personalizer"></a>Vad är Personanpassning?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure-personanpassa är en molnbaserad API-tjänst som hjälper ditt klient program att välja det bästa, enda _innehålls_ objektet för att visa varje användare. Tjänsten väljer det bästa objektet, från innehålls objekt, baserat på samlad real tids information som du tillhandahåller om innehåll och kontext.

När du har presenterat innehålls objekt för din användare övervakar systemet användar beteende och rapporterar en belönings poäng till Personanpassaren för att förbättra möjligheten att välja det bästa innehållet baserat på den Sammanhangs information som den tar emot.

**Innehåll** kan vara vilken enhet som helst information, till exempel text, bilder, webb adresser eller e-postmeddelanden som du vill välja från för att visa för användaren.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Hur väljer Personanpassare det bästa innehålls objektet?

Personanpassare använder **förstärknings inlärning** för att välja det bästa objektet (_åtgärd_) baserat på kollektivt beteende och belönings resultat för alla användare. Åtgärder är innehålls objekt, till exempel nyhets artiklar, vissa filmer eller produkter att välja bland.

**Ranknings** anropet tar objektet Action, tillsammans med funktionerna i åtgärden och kontext funktionerna för att välja det översta objektet:

* **Åtgärder med funktioner** – innehålls objekt med funktioner som är speciella för varje objekt
* **Kontext funktioner** – funktioner för dina användare, deras kontext eller deras miljö när du använder din app

Rang anropet returnerar det ID för vilket innehålls objekt, __åtgärd__, som ska visas för användaren i fältet **belönings åtgärds-ID** .
Den __åtgärd__ som visas för användaren väljs med Machine Learning-modeller, vilket försöker maximera det totala antalet förmåner över tid.

Det finns flera exempel scenarier:

|Innehållstyp|**Åtgärder (med funktioner)**|**Kontext funktioner**|Returnerat åtgärds-ID för belöning<br>(Visa det här innehållet)|
|--|--|--|--|
|Nyhets lista|a. `The president...`(National, politiken, [text])<br>b. `Premier League ...`(global, sport, [text, bild, video])<br> c. `Hurricane in the ...`(regional, väder, [text, bild]|Enhetens Nyheter läses från<br>Månad eller säsong<br>|en`The president...`|
|Film lista|1. `Star Wars` (1977, [Action, Adventure, fantasi], George Lucas)<br>2. `Hoop Dreams` (1994, [dokument, idrotts], Steve Jonas<br>3. `Casablanca` (1942, [romantik, drama, War], Michael Curtiz)|Enhets filmen bevakas från<br>skärm storlek<br>Typ av användare<br>|3.`Casablanca`|
|Produkt lista|i. `Product A`(3 kg, $ $ $ $, leverera på 24 timmar)<br>ii. `Product B`(20 kg, $ $, 2 veckor som levereras med tull)<br>iii. `Product C`(3 kg, $ $ $, leverans på 48 timmar)|Enhets köp läses från<br>Utgifts nivå för användare<br>Månad eller säsong|ii. `Product B`|

Personanpassare använder förstärknings undervisning för att välja den enda bästa åtgärden, som kallas för _belönings åtgärds-ID_, baserat på en kombination av:
* Tränad modell – information om den tidigare tjänsten för att personanpassa tjänsten togs emot
* Aktuella data-/regionsspecifika åtgärder med funktioner och kontext funktioner

## <a name="when-to-call-personalizer"></a>När du ska anropa en Personanpassare

En persons **rang** - [API](https://go.microsoft.com/fwlink/?linkid=2092082) anropas _varje gång_ du presenterar innehåll i real tid. Detta kallas en **händelse**som anges med ett _händelse-ID_.

Personens **belönings** - [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) kan anropas i real tid eller fördröjs för att bättre passa din infrastruktur. Du fastställer belönings poängen utifrån dina affärs behov. Belönings poängen är mellan 0 och 1. Det kan vara ett enda värde, till exempel 1 för bra, och 0 för dåligt, eller ett tal som skapats av en algoritm som du skapar med tanke på dina affärs mål och mät värden.

## <a name="personalizer-content-requirements"></a>Innehålls krav för personligare

Använd Personanpassare när ditt innehåll:

* Har en begränsad uppsättning objekt (max ~ 50) att välja från. Om du har en större lista kan du [använda en rekommendations motor](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) för att minska listan till 50 objekt.
* Innehåller information som beskriver det innehåll som du vill rangordna: _åtgärder med funktioner_ och _kontext funktioner_.
* Har minst ~ 1 – a innehålls relaterade händelser som är relaterade till att personanpassa ska vara effektiva. Om Personanpassaren inte får den lägsta trafik som krävs tar det längre tid för tjänsten att fastställa det enkla innehålls objektet.

Eftersom Personanpassaren använder samlad information i nära real tid för att returnera det enda bästa innehålls objektet, så är tjänsten inte:
* Behåll och hantera information om användar profiler
* Logga enskilda användares inställningar eller historik
* Kräv rensad och märkt innehåll

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Så här utformar och implementerar du personanpassa för ditt klient program

1. [Utforma](concepts-features.md) och planera för innehåll, **_åtgärder_** och **_kontext_**. Fastställ belönings algoritmen för **_belönings_** poängen.
1. Varje [personanpassa resurs](how-to-settings.md) som du skapar betraktas som en inlärnings slinga. Slingan tar emot både rang-och belönings anrop för det innehållet eller användar upplevelsen.

    |Resurstyp| Syfte|
    |--|--|
    |[Lärlings läge](concept-apprentice-mode.md)`E0`|Träna en personanpassa modell utan att påverka ditt befintliga program och distribuera sedan till onlineutbildning i en produktions miljö|
    |Standard`S0`|Utbildnings beteende online i en produktions miljö|
    |Kostnads fria`F0`| Testa inlärnings beteendet i en miljö som inte är en produktions miljö|

1. Lägg till en Personanpassare till ditt program, din webbplats eller ditt system:
    1. Lägg till ett **rang** anrop till personanpassaren i ditt program, din webbplats eller ditt system för att fastställa det bästa, enda _innehålls_ posten innan innehållet visas för användaren.
    1. Visa det bästa, enda _innehålls_ objekt, vilket är det returnerade _Åtgärds-ID: t för belöning_, till användare.
    1. Använd _affärs logik_ för att samla in information om hur användaren beter sig för att fastställa **belönings** poängen, till exempel:

    |Beteende|Beräknad belönings Poäng|
    |--|--|
    |Användaren har valt bästa, enskilt _innehålls_ objekt (ID för belönings åtgärd)|**1**|
    |Användaren har valt annat innehåll|**0**|
    |Användaren pausade, rullade runt på ett avgörande sätt innan du väljer bästa, enskilt _innehålls_ objekt (belönings ÅTGÄRDS-ID)|**0,5**|

    1. Lägg till ett **belönings** samtal som skickar en belönings Poäng mellan 0 och 1
        * Direkt efter att ha visat ditt innehåll
        * Eller någon gång senare i ett offline-system
    1. [Utvärdera din loop](concepts-offline-evaluation.md) med en offline-utvärdering efter en användnings period. Med en offline-utvärdering kan du testa och utvärdera effektiviteten för tjänsten personanpassa utan att ändra din kod eller påverka användar upplevelsen.

## <a name="next-steps"></a>Nästa steg


* [Så här fungerar Personanpassning](how-personalizer-works.md)
* [Vad är förstärknings inlärning?](concepts-reinforcement-learning.md)
* [Lär dig mer om funktioner och åtgärder för rang förfrågan](concepts-features.md)
* [Läs om hur du avgör poängen för belönings förfrågan](concept-rewards.md)
* [Snabbstarter](sdk-learning-loop.md)
* [Självstudie](tutorial-use-azure-notebook-generate-loop-data.md)
* [Använd den interaktiva demonstrationen](https://personalizationdemo.azurewebsites.net/)
