---
title: Ankare relationer och sätt att hitta i Azure Spatial ankare | Microsoft Docs
description: Beskriv den konceptuella modellen bakom ankare relationer. Beskriv processen för att ansluta ankare inom ett blanksteg och hur du med i närheten API för att uppfylla ett scenario med sätt-effekt. Peka utvecklare på vår exempelappar som gör Närliggande så att de kan komma igång genomföra det här scenariot i sina egna appar efter förklarar den konceptuella modellen.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56f59a76ac3d11677d5b1f76904cf74e933fa7f6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56753346"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Ankare relationer och sätt att hitta i Azure Spatial ankare

Ankare relationer kan du skapa anslutna fästpunkter med blanksteg och ställa frågor om dem som:

* Finns det ankare Närliggande?
* Hur långt bort är de?

## <a name="examples"></a>Exempel

Vissa – du kan aktivera med anslutna ankare omfattar:

1. En arbetsprocess måste utföra en procedur som inbegriper att gå till olika platser i en industriella datafabrik. Fabriken har placerats spatial fästpunkter på varje plats som ingår i proceduren. HoloLens- eller mobilapp hjälper till att guiden arbetaren från en plats till nästa. Det skulle efterfråga spatial ankare som finns i närheten, och sedan hjälper arbetaren till nästa plats. Appen visar visuella indikatorer om allmänna riktning och avståndet till nästa plats att slutföra uppgiften.

2. En museum skapar spatial fästpunkter på offentliga skärmar som tillsammans skapar en specifik rundtur bland museum, till exempel ”en timmes genomgång av grundläggande offentliga visar”. När besökare finns på en offentlig visning, kan de öppna den museum Mixad verklighet appen på sin mobila enhet. De skulle peka telefonen runt området och via en kamera-feed, och se övergripande riktning och avståndet till de offentliga visar på den guidade visningen. När användaren startar som leder till en av de offentliga visar uppdaterar appen progressivt Allmänt riktning och avståndet till hjälper användarna att guiden det.

## <a name="way-finding"></a>Sätt-effekt

Anta att appen använder ”rad med för att se” riktning och avståndet mellan fästpunkter ge tips vägledning till användarna. Vi kallar detta övergripande sätt-effekt. Det är viktigt att notera att sätt hitta skiljer sig från aktivera genom att aktivera navigering. I sin tur genom att aktivera navigering vägleds användare runt väggar, via dörrar och mellan golv. Med sättet att hitta får användaren tips om allmänna målet. Men användarens inferens eller kunskap om området hjälper också till att navigera genom struktur till målet.

Att skapa en sätt-effekt-upplevelse innebär att förbereda ett utrymme för upplevelse och utvecklar en app som slutanvändarna kommer att interagera med. Konceptuell stegen är:

1. Planera utrymmet: Fastställa platser inom området som deltar i sätt-effekt-upplevelse. I tidigare exempel har kan den här aktiviteten genomföras med factory övervakaren eller museum rundtur koordinatorn.
2. Ansluta fästpunkter: Någon besöker på valda platser och skapar det spatial fästpunkter. Den här åtgärden kan utföras med en administratörsläge av slutanvändaren appen eller en annan app helt och hållet. Via den här processen är fästpunkterna för varje ansluten eller relaterade till de andra. Dessa relationer underhålls i tjänsten.
3. Startar slutanvändarens upplevelse: Det första steget för slutanvändare är att hitta någon av de ankare som använder en app, som kan vara i något av de valda platserna. Avgör de platser där användarna kan ange upplevelsen är en del av Designa den övergripande processen.
4. Att söka efter närliggande fästpunkter: När du har hittat en fästpunkt, kan appen begära Närliggande fästpunkter. Den här proceduren returnerar en attityd mellan enheten och dessa fästpunkter.
5. Guidar användaren: Appen kan dra nytta av attityden till var och en av dessa ankare att återge hjälp att få tips om deras allmänna riktning och avstånd. Exempelvis kan det finnas en ikon och pilen på en kamera flöde i en mobilapp som representerar varje möjligt mål som i bilden nedan.
6. Förfina vägledningen: När användaren går kan appen med jämna mellanrum att beräkna en ny attityd mellan enheten och mål-ankare. Appen fortsätter att förfina tipsen vägledning som hjälper användaren når målet.

![Mötets plats](./media/meeting-spot.png)

## <a name="connecting-anchors"></a>Ansluta ankare

Om du vill skapa en sätt-effekt upplevelse, måste du placera anslutna fästpunkter på valda platser. Nedan antar vi det här arbetet utförs av en administratör av appen.

### <a name="connecting-anchors-in-a-single-session"></a>Ansluta fästpunkter i en enda session

Stegen som ingår i ansluter fästpunkter är:

1. Administratören får för att den första platsen och skapar ankare A med hjälp av en CloudSpatialAnchorSession.
2. Administratören går till den andra platsen medan den underliggande MR/AR-plattformen fortsätter att spåra användaren.
3. Administratören skapar ankare B med samma CloudSpatialAnchorSession. Fästpunkter A och B är nu ansluten och den här relationen underhålls av tjänsten Azure Spatial fästpunkter.
4. Fortsätt proceduren för alla ankare som du vill ansluta till.

### <a name="multiple-sessions"></a>Flera sessioner

Du kan också ansluta spatial fästpunkter för flera sessioner. Den här metoden kan du skapa och ansluta vissa ankare i taget och senare skapa och ansluta fler fästpunkter. Så här ansluter ankare med flera sessioner:

1. Appen skapar vissa fästpunkter i en CloudSpatialAnchorSession.
2. Senare, till exempel på en annan dag appen söker efter en av dessa ankare med en ny CloudSpatialAnchorSession (till exempel ankare A).
3. Användaren går till en ny plats medan den underliggande MR/AR-plattformen fortsätter att spåra användaren.
4. Med samma CloudSpatialAnchorSession kan användaren skapar ankare C. ankare A, B och C är nu ansluten och den här relationen underhålls av Azure Spatial fästpunkter.
5. Du kan fortsätta den här proceduren för fler fästpunkter och fler sessioner med tiden.

### <a name="verifying-anchor-connections"></a>Verifiera ankare anslutningar

Appen kan kontrollera att två fästpunkter är ansluten genom att utfärda en fråga i närheten fästpunkter. När resultatet av frågan innehåller önskat mål fästpunkt, har appen bekräftelse att fästpunkter är anslutna. Om de inte är anslutna försök appen under anslutningen igen. Här följer några orsaker till varför ankare misslyckas med att ansluta:

1. Underliggande MR/AR spåraren förlorade spårning under processen för att ansluta fästpunkter.
2. Det uppstod ett fel i kommunikationen med tjänsten Azure Spatial ankare och ankar-anslutningen kunde inte sparas.

### <a name="sample-code"></a>Exempelkod

Du kan se exempelkod som visar hur du ansluter ankare och göra Närliggande frågor. Referera till Azure Spatial ankare exempelappar på GitHub.
