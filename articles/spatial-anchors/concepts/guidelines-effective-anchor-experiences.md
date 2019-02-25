---
title: Riktlinjer för effektiv ankare upplevelser i Azure Spatial ankare | Microsoft Docs
description: Riktlinjer och överväganden för att skapa och hitta ankare effektivt med Azure Spatial fästpunkter.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 196958e4818251bd7f2ee78ca472e6f28292d908
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753267"
---
# <a name="guidelines-for-an-effective-anchor-experience-with-azure-spatial-anchors"></a>Riktlinjer för en effektiv fästpunkt erfarenhet av Azure Spatial ankare

Den här artikeln innehåller riktlinjer och överväganden för att skapa och hitta ankare effektivt med Azure Spatial fästpunkter.

## <a name="creating-anchors"></a>Skapa fästpunkter

Skapa bra fästpunkter är en av störst aspekter av arbetet med Azure Spatial fästpunkter. Det är viktigt att investera tid i utbilda eller guidar användare i din UX hur du skapar bra fästpunkter. Genom att investera i ankare skapas direkt, kan du aktivera slutanvändarna att hitta fästpunkter på ett tillförlitligt sätt:

- På olika enheter
- Vid olika tidpunkter
- Vid olika belysning
- Från de önskade perspektiv inom området
- O.s.v.

Nedan finns information och riktlinjer för att hjälpa dig att utforma upplever ankare skapande och plats.

## <a name="static-and-dynamic-locations"></a>Statisk och dynamisk platser

Designar ankare upplevelsen kommer välja de platser som ska deltar. Blir platserna som statiska och definieras av en administratör för utrymmet? Eller kan de dynamiska och användardefinierade?

En fullversion arkivhanteraren kanske vill att en statisk butiksupplevelsen att locka användare att besöka. Medan utvecklaren av Mixad verklighet tavla spel förmodligen vill låta användarna välja var du vill spela upp.

För statiska platser informera du administratörer att umgås curating utrymme med bra fästpunkter.

För dynamiska platser bör du tycker om hur du informera eller för användare i din UX skapa bra fästpunkter.

## <a name="stable-visual-features"></a>Stabil visuella funktioner

Visual spårningssystem som används i Mixad verklighet och förhöjd verklighet enheter förlitar sig på visuella funktioner för miljön. Hämta den säkraste upplevelsen:  

- Skapa fästpunkter på platser med stabila visuella funktioner (det vill säga funktioner som inte ändras ofta).

- Skapa inte fästpunkter på stora tomma ytor med inga särskiljande egenskaper.

- Skapa inte fästpunkter på reflekterar material.

- Skapa inte fästpunkter på Surface-enheter där mönstret bara Upprepa, till exempel carpet eller bakgrundsbild.

![Exempel-avbildningar som representerar en bra och felaktig miljö](./media/stable-visual.png)

## <a name="consider-various-viewing-perspectives"></a>Överväg att olika visning perspektiv

När du skapar en fästpunkt, tänka på de personer som ska komma senare för att hitta till ankaret.

Överväg till exempel en fästpunkt mitt i ett rum med två dörrar. Vill du sannolikt att användarna anger rummet från antingen dörren att hitta till ankaret. När du skapar till ankaret behöver du söker igenom sin position från båda dörrar. Detta samlar in miljödata runt till ankaret från båda perspektiv så att användarna kan hitta till ankaret från antingen dörren.

I allmänhet när du skapar en fästpunkt, ska det sökas igenom från olika platser eller perspektiv som du förväntar dig att vara Ständiga vid försök att hitta den.  

Om du placerar virtuella innehåll på en utomhus skulptur bra det att gå runt skulpturer, medan de tittar på det, när du skapar till ankaret.  

Å andra sidan, om ankaret är i ett rum hörn, är det bara en riktning att den från. När du skapar till ankaret är behöver det bara genomsökas från det här perspektivet.

## <a name="multiple-anchors"></a>Flera fästpunkter

Belysning kan göra skillnad i de visuella funktioner som upptäcks. Fästpunkter som skapats i starka fysiska ljus kan vara svårt att hitta efter mörk i artificiella belysning och vice versa.  

Om du får det här problemet, kan det skapa två fästpunkter – en på sommartid och en annan i artificiella belysning – på samma plats. Din app sedan fråga om båda fästpunkter. Om antingen är placerade, har appen en attityd för till ankaret. 

På samma sätt i miljöer där de visuella funktionerna ändrar eftersom de flesta objekt flyttas, kan flera ankare hjälpa. När en fästpunkt blir för svår att hitta på grund av betydande förändringar i miljön kan ersätta du det med ett nytt lösenord. Det kan vara fallet, till exempel i en butik där layouten uppdateras varje månaderna.

## <a name="targets-and-rooms"></a>Mål och rum

I många fall representerar hitta en fästpunkt en startpunkt till din app unikt upplevelse. Du vill få via det här steget snabbt och tillförlitligt så användare kan ange din upplevelse. Ägna tid på hur användare kommer att hitta fästpunkterna är ett viktigt design steg. Det kan vara bra att tänka detta när det gäller två breda scenarier: **Mål** och **Rooms**.

### <a name="targets"></a>Mål

![Börja här redigera](./media/start-here-edit.png)

I målscenario är platsen för en fästpunkt väl känd. I ett fiktivt MR målningen avslutas app placerar en användare till exempel en virtuell arbetsyta på väggen. Hon instruerar andra användare i rummet så att den pekar på sina enheter på samma ställe på väggen att leta upp till ankaret och börja upplevelsen.  

Ett annat exempel på ett målscenario med kan vara ett tecken som talar om att ”Genomsök för erbjudanden” väntan på raden på ett kafé. En fästpunkt har tidigare placerade här i ett kafé. Som användare skannar tecknet, de letar du upp till ankaret och ange AR-upplevelse för erbjudanden på kaffe.

Mål för scenariot kan foton. Om du kan visa användare ett foto på sin enhet för det avsedda målet, kan de snabbt identifiera vad du kan söka i verkligheten. Du kan till exempel hjälpa användarna tas emot i allmän närheten av en avsedda mål med hjälp av GPS. När användaren kommer din app visar ett foto av målet. Användaren ser ut runt utrymmet, hittar mål och fortsätter att söka efter till ankaret.

### <a name="rooms"></a>rum

![Skanna utrymme](./media/scan-room.png)

Utrymme för scenariot ange användare kan bara vetskapen om att det finns en fästpunkt här någonstans. Användare söka efter området sin enhet och hitta snabbt till ankaret.

Uppnå detta vanligtvis måste du skapa väl granskad ankare enligt beskrivningen i [tänka på olika visning perspektiv](#consider-various-viewing-perspectives) tidigare. Om rummet har genomsökts ur många olika perspektiv när du skapar till ankaret kan sedan användarna genomsöka nästan var som helst när du försöker hitta den.

I princip är den person som skapar till ankaret mer tid för genomsökning utrymme, så att personer som kommer senare kan söka efter och hitta till ankaret snabbt. Det här är ett viktigt kompromiss måste du tänka på för din upplevelse.

MR målningen avslutas appen som beskrivs ovan är ett exempel som inte är bra som ett rum scenario. Här är vill att användaren har lagt till ankaret de andra sätt att ansluta till upplevelsen snabbt. Användarna vill inte vänta tills det är väl skannade för att kunna börja upplevelsen. Eftersom alla användare veta exakt var man ska peka sin enhet för att hitta fästpunkter, behandlas det här scenariot bättre som ett målscenario.

## <a name="effectively-locating-anchors"></a>Ett effektivt sätt att hitta ankare

Visual spårning system är beroende av de visuella funktionerna för en miljö till funktionen. Fler visuella funktioner är en del av en genomsökning, desto högre sannolikheten för att söka efter en fästpunkt.

Det finns några allmänna riktlinjer som du kan följa för att skapa ett användargränssnitt som uppmuntrar en användbar avsökning av miljön.

Om användaren inte hitta en fästpunkt inom några sekunder, bör först appen uppmana användare att ändra enheten så att fler perspektiv fångas.  Appen kan även uppmana användare själva flytta omkring miljön vid sökning efter till ankaret från flera perspektiv. Fler perspektiv som enheten ser samma pekar desto bättre.

Be användaren att flytta runt målet, samtidigt som du visar den från olika perspektiv för mål-scenarier. Med andra ord, be användaren att flytta in en fot avbilda målet från nya perspektiv tills till ankaret finns.

Be användaren att skanna långsamt rummet scenarier med utrymme. Exempelvis kan be användaren att rotera för att samla in 180 eller även 360 grader rummet. Eller be användaren att flytta till ett nytt perspektiv i rummet. Det bästa sättet att genomsöka är att söka i rummet. Detta samlar in mer visuella funktioner för miljön än say som genomsöker en närliggande vägg. Skanna en närliggande vägg in inte som mycket användbart visuella funktioner för miljön.

Det är inte bra att flytta den enheten sida till sidan och om igen när du letar efter en fästpunkt. Detta skulle bara samla in samma punkter från samma perspektiv.

## <a name="testing-the-experience"></a>Testa upplevelsen

Ovanstående är allmänna riktlinjer. Med Azure Spatial ankare skriver du appar som samverkar med verkligheten. Det är därför viktigt att ägna tid åt att testa din app ankare scenarier i verkliga miljöer. Detta gäller särskilt för miljöer som är representativ för där du förväntar dig att användarna ska kunna använda appen.