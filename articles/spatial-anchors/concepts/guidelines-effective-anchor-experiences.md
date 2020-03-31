---
title: Riktlinjer för effektiva ankarupplevelser
description: Riktlinjer och överväganden för att skapa och hitta ankare effektivt med hjälp av Azure Spatial Anchors.
author: mattwojo
manager: jken
services: azure-spatial-anchors
ms.author: mattwoj
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9a24da8d76f401f534eccf33312fbf0c2bee9f5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270523"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Skapa en effektiv ankarupplevelse med hjälp av Azure Spatial Anchors

Den här artikeln innehåller riktlinjer och överväganden som hjälper dig att effektivt skapa och hitta ankare med hjälp av rumsliga fästpunkter.

## <a name="good-anchors"></a>Bra ankare

Spatial Anchors hjälper dig att skapa bra ankare. Det är viktigt att investera tid i att antingen utbilda eller vägleda användare i din användarupplevelse (UX) för att skapa bra ankare. Genom att investera i att skapa bra ankare på framsidan hjälper du slutanvändarna att på ett tillförlitligt sätt hitta ankare:

- Över olika enheter.
- Vid olika tillfällen.
- I olika ljusförhållanden.
- Från önskat perspektiv inom utrymmet.

## <a name="static-and-dynamic-locations"></a>Statiska och dynamiska platser

En del av utformningen av ankaret erfarenhet är att välja platser. Kommer platserna att vara statiska och definieras av en administratör för utrymmet? Eller kommer de att vara dynamiska och definieras av användaren?

En butikschef kanske vill ha en statisk upplevelse i butik för att locka användare att besöka. Men utvecklaren av en mixad verklighet brädspel skulle sannolikt vill låta användarna välja var de ska spela.

För statiska platser kan du lära administratörer att spendera tid på att kurera utrymmet med bra ankare.

För dynamiska platser bör du tänka på hur du lär eller guidar användare i din användarupplevelse för att skapa bra ankare.

## <a name="stable-visual-features"></a>Stabila visuella funktioner

Visuella spårningssystem som används på enheter med mixad verklighet och förstärkt verklighet är beroende av visuella funktioner i miljön. Så här får du den mest tillförlitliga upplevelsen:

- *Skapa* fästpunkter på platser som har stabila visuella funktioner (det vill ha funktioner som inte ändras ofta).

- *Skapa inte* ankare på stora tomma ytor som inte har några särskiljande egenskaper.

- *Skapa inte* ankare på mycket reflekterande material.

- *Skapa inte* ankare på ytor där mönstret upprepas, till exempel matta eller tapeter.

![Exempel på en bra miljö för ankare och en dålig miljö för ankare](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Olika visningsperspektiv

När du skapar ett ankare, tänk på de människor som senare kommer att försöka hitta ankaret.

Tänk till exempel på ett ankare mitt i ett rum som har två dörrar. Du vill förmodligen tillåta användare att komma in i rummet från båda luckan. När du skapar ankaret måste du skanna dess position från båda dörröppningarna. Du ändrar perspektiv för att samla in miljödata runt ankaret så att användarna kan hitta ankaret från båda luckan.

I allmänhet, när du skapar ett ankare, skanna den ur perspektiv av de människor som kommer att försöka hitta den. Så om du placerar virtuellt innehåll på en utomhus skulptur, är det vettigt att gå runt skulpturen, medan skanna den, som du skapar ankaret. Om ankaret är i hörnet av ett rum finns det bara en riktning att närma sig det från. När du skapar det här ankaret kan du skanna det bara ur det perspektivet.

## <a name="multiple-anchors"></a>Flera ankare

Belysning kan göra skillnad i de visuella funktioner som en app upptäcker. Ankare som skapats i starkt naturligt ljus kan vara svåra att hitta i artificiellt ljus, och vice versa.

Om du har det här problemet kan det bidra till att skapa två fästpunkter. På samma plats, skapa ett ankare i dagsljus och en annan i artificiellt ljus. Appen kan sedan fråga efter båda fästpunkterna. När något av ankaret finns har appen en attityd för ankaret.

På samma sätt kan flera ankare hjälpa till i miljöer där de visuella funktionerna ändras eftersom de flesta objekt flyttas. När ett ankare blir för svårt att hitta på grund av betydande förändringar i miljön kan du ersätta ankaret med ett nytt. Du kan till exempel göra detta i en butik där layouten uppdateras med några månaders mellanrum.

## <a name="targets-and-rooms"></a>Mål och rum

I många fall är ett ankare en startpunkt för appens upplevelse. Du vill ta dig igenom det här steget snabbt och tillförlitligt så att användarna kan ange din upplevelse. Spendera tid på hur användarna kommer att hitta dina ankare är ett viktigt designsteg. Det är bra att tänka på att hitta ankare i termer av två breda scenarier: *mål* och *rum*.

### <a name="targets"></a>Mål

I målscenariot är platsen för ett ankare välkänt. I en fiktiv app för måleri med mixad verklighet placerar en användare till exempel en virtuell arbetsyta på väggen. Hon instruerar de andra användarna i rummet att rikta sina enheter på samma plats på väggen för att lokalisera ankaret och börja upplevelsen.

Ett annat exempel på ett målscenario kan vara en skylt på ett kafé med texten "Sök efter erbjudanden". Kaféet har placerat ett ankare här. Som användare skanna tecknet, de lokalisera ankaret och ange augmented-reality erfarenhet för att hitta erbjudanden på kaffe.

I målscenariot kan foton hjälpa till. Om du visar användarna ett foto av det avsedda målet på sin enhet kan de snabbt identifiera vad de ska skanna i den verkliga världen. Du kan till exempel hjälpa användarna att komma fram inom det allmänna området för ett avsett mål med hjälp av GPS. När användaren anländer visar appen ett foto av målet. Användaren tittar runt i utrymmet, hittar målet och söker efter ankaret.

![Illustration av ett ankare som visar ett foto av målet på en användares mobila enhet](./media/start-here-edit.png)

### <a name="rooms"></a>Rum

I rumsscenariot går användarna in i ett utrymme bara för att veta att det finns ett ankare här någonstans. Användare skanna utrymmet med sin enhet och snabbt hitta ankaret.

Den här upplevelsen kräver vanligtvis att du skapar väl kurerade ankare, som diskuteras i olika visningsperspektiv. Om du skannade rummet från många perspektiv när du skapade ankaret kan användarna skanna nästan var som helst när de försöker hitta det.

![Illustration av hur en användare kan skanna ett rum för att hitta ett ankare](./media/scan-room.png)

I huvudsak spenderar du mer tid på att skanna utrymmet när du skapar ankaret så att senare användare kan söka igenom och hitta ankaret snabbt. När du skapar din upplevelse måste du överväga denna viktiga kompromiss.

Exemplet med mixad verklighet målning app som vi diskuterade tidigare fungerar inte bra som ett rum scenario. Här vill användaren som placerar ankaret att andra ska ansluta sig till upplevelsen snabbt. Användarna vill inte vänta med att starta upplevelsen tills rummet är väl skannat. Eftersom alla användare vet exakt var de ska peka sin enhet för att hitta fästpunkterna, fungerar det här exemplet bättre som ett målscenario.

## <a name="anchor-location"></a>Ankarplats

Visuella spårningssystem förlitar sig på de visuella funktionerna i en miljö. Ju fler visuella funktioner som en genomsökning innehåller, desto högre är sannolikheten för att hitta ett ankare.

Följ de allmänna riktlinjerna i det här avsnittet för att skapa en användarupplevelse som uppmuntrar till en användbar genomsökning av miljön.

Om användaren inte hittar ett ankare inom några sekunder bör appen uppmuntra användarna att flytta enheten för att fånga fler perspektiv. Appen kan också uppmuntra användare att flytta sig runt miljön för att söka efter ankaret från fler perspektiv. Ju fler funktionsperspektiv som enheten ser, desto bättre.

Om du vill göra målscenarier ber du användaren att flytta runt målet för att visa det från olika perspektiv. Med andra ord, be användaren att fånga målet från nya perspektiv tills ankaret finns.

Om du vill ha rumsscenarier ber du användaren att långsamt skanna rummet. Be till exempel användaren att vända sig för att fånga 180 grader eller till och med 360 grader av rummet. Eller be användaren att visa rummet från ett nytt perspektiv.

Den mest meningsfulla metoden är att skanna över rummet. En skanning över rummet fångar mer visuella funktioner i miljön än en genomsökning av en vägg i närheten, till exempel. En skanning av en vägg i närheten fångar inte lika många användbara visuella funktioner i miljön.

Det är inte bra att upprepade gånger flytta enheten från sida till sida när du letar efter ett ankare. Detta fångar helt enkelt samma punkter från samma perspektiv.

## <a name="experience-tests"></a>Upplev tester

I den här artikeln har vi diskuterat allmänna riktlinjer. Med Spatial Anchors skriver du appar som interagerar med den verkliga världen. På grund av detta bör du ägna tid åt att testa appens ankarscenarier i verkliga miljöer. Detta gäller särskilt miljöer som representerar var du förväntar dig att användarna ska använda appen.
