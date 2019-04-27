---
title: Ankare relationer och sätt att hitta i Azure Spatial ankare | Microsoft Docs
description: 'Läs mer om den konceptuella modellen bakom ankare relationer. Lär dig att ansluta ankare inom ett blanksteg och om du vill använda i närheten API: et för att uppfylla ett scenario med sätt-effekt.'
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 619cd051eccce3434469ae909f69496a254d0d9a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565141"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Ankare relationer och sätt att hitta i Azure Spatial ankare

Med ankare relationer kan du skapa anslutna fästpunkter i ett utrymme och sedan ställa frågor som dessa:

* Finns det ankare Närliggande?
* Hur långt bort är de?

## <a name="examples"></a>Exempel

Du kan använda anslutna ankare i fall som dessa:

* En worker behöver slutföra en uppgift som inbegriper att gå till olika platser i en industriella datafabrik. Fabriken har spatial fästpunkter på varje plats. HoloLens- eller mobilapp hjälper till att guiden arbetaren från en plats till nästa. Appen först begär i närheten spatial ankare och sedan hjälper arbetaren till nästa plats. Appen visar visuellt Allmänt riktning och avståndet till nästa plats.

* En museum skapar spatial fästpunkter på offentliga visar. Tillsammans utgör de här ankare en timmes genomgång av den museum grundläggande offentliga visar. Besökare kan öppna den museum Mixad verklighet appen på sina mobila enheter på en offentlig visning. De peka sedan phone kameran runt utrymme för att se övergripande riktning och avståndet till de offentliga visar på den guidade visningen. När en användare går mot en offentlig visning, uppdaterar appen Allmänt riktning och avståndet vägleder användaren.

## <a name="set-up-way-finding"></a>Konfigurera sätt-effekt

Med hjälp av en app som använder rad med för att se riktning och avståndet mellan fästpunkter för att ge vägledning *sätt-effekt*. Sätt-effekt skiljer sig från aktivera genom att aktivera navigering. I sin tur genom att aktivera navigering vägleds användare runt väggar, via dörrar och mellan golv. Med sättet att hitta kommer användaren tips om allmänna målet. Men inferens eller kunskap om området hjälper också till att användaren navigerar struktur till målet.

Om du vill skapa en sätt-effekt upplevelse, förbereda ett utrymme för upplevelse och utveckla en app som användarna kommer att interagera med. Detta är de konceptuella steg:

1. **Planera utrymmet**: Bestäm vilka platser inom området kommer att ingå i sätt-effekt-upplevelse. I vårt fall välja factory övervakaren eller museum rundtur koordinatorn vilka platser som ska ingå i sätt-effekt-upplevelse.
2. **Ansluta ankare**: Gå till de valda platserna för att skapa spatial fästpunkter. Du kan göra detta i ett läge för appens slutanvändaren eller i en annan app helt och hållet. Du måste ansluta eller avser de andra varje ankare. Tjänsten underhåller dessa relationer.
3. **Starta slutanvändarens upplevelse**: Användarna kör appen för att hitta en fästpunkt, vilket kan vara i något av de valda platserna. Din övergripande design bestämma de platser där användare kan ange upplevelsen.
4. **Hitta Närliggande ankare**: När du hittar en fästpunkt, kan appen begära Närliggande fästpunkter. Den här proceduren returnerar en attityd mellan enheten och dessa fästpunkter.
5. **Hjälper användaren att**: Appen kan använda attityd till var och en av dessa fästpunkter för att ge vägledning om användarens Allmänt riktning och avståndet. Kamerans användning i appen kan till exempel visa en ikon och en pil för att representera varje möjligt mål, så som visas i följande bild.
6. **Förfina riktlinjerna**: När användaren går kan appen med jämna mellanrum att beräkna en ny attityd mellan enheten och mål-ankare. Appen fortsätter att förfina tipsen vägledning som hjälper användaren når målet.

    ![Ett exempel på hur en app kan visa att sätt hitta vägledning](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Ansluta ankare

Om du vill skapa en sätt-effekt upplevelse, måste du först placera fästpunkter på valda platser. I det här avsnittet antar vi appens administratör har redan slutfört arbetet.

### <a name="connect-anchors-in-a-single-session"></a>Ansluta fästpunkter i en enda session

Så här ansluter fästpunkter:

1. Gå till den första platsen och skapa ankare A med hjälp av en CloudSpatialAnchorSession.
2. Gå till den andra platsen. Den underliggande plattformen MR/AR spårar flödet.
3. Skapa fästpunkt B med samma CloudSpatialAnchorSession. Fästpunkter A och B är nu ansluten. Tjänsten Spatial ankare underhåller den här relationen.
4. Fortsätt proceduren för återstående fästpunkter.

### <a name="connect-anchors-in-multiple-sessions"></a>Ansluta ankare i flera sessioner

Du kan ansluta spatial fästpunkter för flera sessioner. Med den här metoden kan kan du skapa och ansluta vissa fästpunkter på en gång och sedan senare skapa och ansluta fler fästpunkter. 

Så här ansluter fästpunkter för flera sessioner:

1. Appen skapar vissa fästpunkter i en CloudSpatialAnchorSession. 
2. Vid en annan tidpunkt hittar någon av dessa ankare (till exempel ankare A) med hjälp av en ny CloudSpatialAnchorSession i appen.
3. Gå till en ny plats. Den underliggande plattformen för Mixad verklighet eller förhöjd verklighet spårar flödet.
4. Skapa fästpunkt C med samma CloudSpatialAnchorSession. Fästpunkter A, B och C är nu ansluten. Tjänsten Spatial ankare underhåller den här relationen.

Du kan fortsätta den här proceduren för fler fästpunkter och fler sessioner med tiden.

### <a name="verify-anchor-connections"></a>Verifiera ankare anslutningar

Appen kan kontrollera att två fästpunkter är ansluten genom att utfärda en fråga i närheten fästpunkter. När frågans resultat innehåller target fästpunkt, har ankar-anslutningen verifierats. Om fästpunkter inte ansluts kan appen försöka ansluta dem igen. 

Här följer några orsaker till varför ankare misslyckas med att ansluta:

* Underliggande Mixad verklighet förhöjd verklighet plattformen eller förlorade spårning under processen för att ansluta fästpunkter.
* På grund av ett nätverksfel vid kommunikation med tjänsten Spatial ankare kunde inte ankar-anslutningen sparas.

### <a name="find-sample-code"></a>Hitta exempelkod

Exempelkod som visar hur du ansluter ankare och för att utföra Närliggande frågor finns i [Spatial ankare exempelappar](https://github.com/Azure/azure-spatial-anchors-samples).
