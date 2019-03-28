---
title: Riktlinjer för effektiv ankare upplevelser som använder Azure Spatial ankare | Microsoft Docs
description: Riktlinjer och saker att skapa och leta upp ankare effektivt med hjälp av Azure Spatial fästpunkter.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e77dcd96ffa0fbd57aa0ed1b4f857279ca768a7
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520810"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Skapa en effektiv ankar-upplevelse med hjälp av Azure Spatial ankare

Den här artikeln innehåller riktlinjer och överväganden som hjälper dig att effektivt skapa och leta upp ankare med hjälp av Spatial fästpunkter.

## <a name="good-anchors"></a>Bra ankare

Spatial ankare hjälper dig att skapa bra fästpunkter. Det är viktigt att investera tid i utbilda eller guidar användare i din användarupplevelsen (UX) för att skapa bra fästpunkter. Genom att investera i att skapa bra ankare direkt, kan du hjälpa slutanvändarna att hitta fästpunkter på ett tillförlitligt sätt:

- På olika enheter.
- Vid olika tidpunkter.
- I olika belysning.
- Från de önskade perspektiv inom området.

## <a name="static-and-dynamic-locations"></a>Statisk och dynamisk platser

Designar ankar-upplevelsen är att välja platserna. Blir platserna som statiska och definieras av en administratör för utrymmet? Eller kommer de dynamiska som definierats av användaren?

En fullversion arkivhanteraren kanske en statisk butiksupplevelsen att locka användare att besöka. Men utvecklaren av Mixad verklighet tavla spel förmodligen vill låta användarna välja var du vill spela upp.

Du kan lära administratörer att umgås curating utrymme med bra fästpunkter för statiska platser.

För dynamiska platser bör du tycker om hur du undervisar och för användare i din UX skapa bra fästpunkter.

## <a name="stable-visual-features"></a>Stabil visuella funktioner

Visual spårning system används på Mixad verklighet och förhöjd verklighet enheter förlitar sig på visuella funktioner för miljön. Hämta den säkraste upplevelsen:  

- *Gör* skapa fästpunkter på platser som har stabil visuella funktioner (det vill säga funktioner som inte ändras ofta).

- *Inte* skapa fästpunkter på stora tomma ytor som inte har några särskiljande egenskaper.

- *Inte* skapa fästpunkter på reflekterar material.

- *Inte* skapa fästpunkter på ytor där mönstret upprepas, till exempel carpet eller bakgrundsbild.

![Exempel på en bra miljö för ankare och en skadad miljö för fästpunkter](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Olika visning perspektiv

När du skapar en fästpunkt, tänka på de personer som kommer senare att försöka hitta till ankaret.

Överväg till exempel en fästpunkt mitt i ett rum som har två dörrar. Vill du förmodligen att användarna kan ange rummet från antingen dörren. När du skapar till ankaret måste du söker igenom sin position från båda dörrar. Du kan ändra perspektiv för att samla in miljödata runt till ankaret så att användarna kan hitta till ankaret från antingen dörren.

I allmänhet när du skapar en fästpunkt kan du skanna den från perspektiv av personer som försöker att hitta den. Så om du placerar virtuella innehåll på en utomhus skulpturer, bra det att gå runt skulpturer, under sökning efter det, när du skapar till ankaret. Om ankaret är i ett rum hörn, är det bara en riktning att den från. När du skapar den här ankare kan skanna du den bara från det här perspektivet.

## <a name="multiple-anchors"></a>Flera fästpunkter

Belysning kan göra skillnad i de visuella funktioner som identifierar en app. Fästpunkter som skapats i starka fysiska ljus kan vara svårt att hitta i artificiella ljus och vice versa.  

Om du har det här problemet, kan det skapa två fästpunkter. På samma plats, skapar du en fästpunkt i sommartid och en annan artificiella bakgrund. Din app sedan fråga om båda fästpunkter. När antingen ankare har hittats visas har appen en attityd för till ankaret. 

På samma sätt i miljöer där de visuella funktionerna ändrar eftersom de flesta objekt flyttas, kan flera ankare hjälpa. När en fästpunkt blir för svårt att hitta på grund av betydande förändringar i miljön, kan du ersätta till ankaret med en ny. Du kan göra detta, till exempel i en butik där layouten uppdateras varje månaderna.

## <a name="targets-and-rooms"></a>Mål och rum

I många fall är en fästpunkt en startpunkt till din app-upplevelse. Du vill få via det här steget snabbt och tillförlitligt så användare kan ange din upplevelse. Ägna tid på hur användare kommer att hitta fästpunkterna är ett viktigt design steg. Är det bra att tänka på att söka efter fästpunkter när det gäller två breda scenarier: *mål* och *rooms*.

### <a name="targets"></a>Mål

I målscenario är platsen för en fästpunkt väl känd. I en app med fiktiva Mixad verklighet målningen avslutas placerar en användare till exempel en virtuell arbetsyta på väggen. Hon instruerar andra användare i rummet så att den pekar på sina enheter på samma ställe på väggen att leta upp till ankaret och börja upplevelsen.  

Ett annat exempel på ett målscenario med kan vara ett tecken på ett kafé som läser ”avsökning för erbjudanden”. Ett kafé har placerats en fästpunkt här. Som användare skannar tecknet, leta upp till ankaret och ange förhöjd verklighet upplevelsen för att hitta erbjudanden på kaffe.

Mål för scenariot kan foton. Om du visar användare ett foto av det tänkta målet på sin enhet, kan de snabbt identifiera vad du kan söka i verkligheten. Du kan till exempel hjälpa dina användare har kommit inom området Allmänt i ett avsedda mål med hjälp av GPS. När användaren kommer visar din app ett foto av målet. Användaren ser ut runt utrymmet, hittar mål och söker igenom till ankaret.

![Bild av en fästpunkt, som visar ett foto av mål på en användares mobila enhet](./media/start-here-edit.png)

### <a name="rooms"></a>rum

Utrymme för scenariot ange användare kan bara vetskapen om att det finns en fästpunkt här någonstans. Användarna söka efter utrymmet sin enhet och hitta snabbt till ankaret.

Den här upplevelsen vanligtvis måste du skapa väl granskad ankare, enligt beskrivningen i olika perspektiv för visning. Om du skannade rummet ur många olika perspektiv när du skapade till ankaret kan skanna användarna nästan var som helst när de försöker att hitta den.

![Bild av hur en användare kan du skanna ett rum för att hitta en fästpunkt](./media/scan-room.png)

I princip du ägna mer tid åt genomsökning utrymmet när du skapar till ankaret så att senare användarna kan genomsöka och snabbt hitta till ankaret. När du skapar din upplevelse, måste du överväga att kompromissen viktiga.

Exempel på Mixad verklighet målningen avslutas appen som beskrivits tidigare fungerar inte som ett rum scenario. Här är vill den användare som placerar till ankaret andra upplevelsen snabbt. Användarna vill inte vänta upplevelsen tills rummet genomsöks bra. Eftersom alla användare veta exakt var man ska peka sin enhet för att hitta fästpunkter, fungerar det här exemplet bättre som ett målscenario.

## <a name="anchor-location"></a>Plats för fästpunkt

Spårnings-Visual-system är beroende av de visuella funktionerna i en miljö. Fler visuella funktioner som innehåller en sökning, desto högre sannolikheten för att söka efter en fästpunkt.

Följ de allmänna riktlinjerna i det här avsnittet för att skapa en UX som uppmuntrar till en användbar avsökning av miljön.

Om användaren inte hitta en fästpunkt inom några sekunder, bör först appen uppmana användare att ändra enheten för att samla in fler perspektiv. Appen kan även uppmana användare att flytta själva runt miljö för att söka efter till ankaret från flera perspektiv. Ju fler funktionen perspektiv som enheten ser, desto bättre.

Be användaren att flytta runt målet att visa den från olika perspektiv för målscenarier. Med andra ord be användare att fånga målet från nya perspektiv tills till ankaret finns.

Be användaren att skanna långsamt rummet scenarier med utrymme. Exempelvis kan be användaren att Stäng för att avbilda 180 grader eller även 360 grader rummet. Eller be användaren att visa rummet från ett nytt perspektiv. 

Det bästa sättet är att genomsöker rummet. En genomsökning för rummet samlar in mer visuella funktioner för miljön än en skanning på en närliggande vägg, till exempel. En skanning på en närliggande vägg fånga inte så många användbara visuella funktioner för miljön.

Det är inte bra att upprepade gånger ändra enheten från sida till sida när du letar efter en fästpunkt. Detta samlar bara in samma punkter från samma perspektiv.

## <a name="experience-tests"></a>Upplev tester

I den här artikeln har vi diskuterat allmänna riktlinjer. Du skriver appar som samverkar med den verkliga världen med Spatial fästpunkter. På grund av det, bör du ägna tid åt testa din app ankare scenarier i verkliga miljöer. Detta gäller särskilt för miljöer som representerar där du förväntar dig att användarna ska kunna använda appen.
