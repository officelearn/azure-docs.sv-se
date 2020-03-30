---
title: Ankarrelationer och vägsökning
description: Lär dig mer om den konceptuella modellen bakom ankarrelationer. Lär dig att ansluta fästpunkter inom ett utrymme och använda API:et i närheten för att uppfylla ett scenario för att hitta ett sätt.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f2fd8f4b7d03be8822c3ec12e2be589054942ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270607"
---
# <a name="anchor-relationships-and-way-finding-in-azure-spatial-anchors"></a>Förankra relationer och vägsökning i Azure Spatial Anchors

Genom att använda ankarrelationer kan du skapa anslutna ankare i ett utrymme och sedan ställa frågor som dessa:

* Finns det ankare i närheten?
* Hur långt bort är de?

## <a name="examples"></a>Exempel

Du kan använda anslutna ankare i fall som dessa:

* En arbetstagare måste slutföra en uppgift som innebär att besöka olika platser i en industrifabrik. Fabriken har rumsliga ankare på varje plats. En HoloLens- eller mobilapp hjälper till att guida arbetaren från en plats till en annan. Appen frågar först efter de närliggande rumsliga ankare och guidar sedan arbetaren till nästa plats. Appen visar visuellt den allmänna riktningen och avståndet till nästa plats.

* Ett museum skapar rumsliga ankare på offentliga utställningar. Tillsammans bildar dessa ankare en timmes rundtur i museets viktiga offentliga utställningar. På en offentlig visning kan besökarna öppna museets app för mixad verklighet på sin mobila enhet. Sedan pekar de sin telefonkamera runt utrymmet för att se den allmänna riktningen och avståndet till de andra offentliga skärmarna på turnén. När en användare går mot en offentlig visning uppdaterar appen den allmänna riktningen och avståndet för att hjälpa användaren.

## <a name="set-up-way-finding"></a>Konfigurera vägsökning

En app som använder siktlinjeriktning och avstånd mellan fästpunkter för att ge vägledning använder *vägsökning*. Väg-finding skiljer sig från turn-by-turn navigering. I turn-by-turn navigering, användare styrs runt väggar, genom dörrar, och mellan våningar. Med väg-finding, får användaren tips om den allmänna riktningen för destinationen. Men inferens eller kunskap om utrymmet hjälper också användaren att navigera genom strukturen till destinationen.

Om du vill skapa en upplevelse för att hitta ett sätt kan du först förbereda ett utrymme för upplevelsen och utveckla en app som användarna interagerar med. Dessa är de begreppsmässiga stegen:

1. **Planera utrymmet:** Bestäm vilka platser inom utrymmet som ska vara en del av upplevelseupplevelsen. I våra scenarier kan fabrikschefen eller museets resekoordinator bestämma vilka platser som ska ingå i upplevelseupplevelsen.
2. **Ansluta ankare:** Besök de valda platserna för att skapa rumsliga ankare. Du kan göra detta i ett administratörsläge för slutanvändarens app eller i en annan app helt och hållet. Du ansluter eller relaterar varje ankare till de andra. Tjänsten upprätthåller dessa relationer.
3. **Starta slutanvändarupplevelsen:** Användare kör appen för att hitta ett ankare, som kan finnas på någon av de valda platserna. Din övergripande design bör bestämma de platser där användarna kan ange upplevelsen.
4. **Hitta fästpunkter i närheten**: När användaren har hittat ett ankare kan appen begära fästpunkter i närheten. Den här proceduren returnerar en attityd mellan enheten och dessa ankare.
5. **Guide användaren**: Appen kan använda pose till var och en av dessa ankare för att ge vägledning om användarens allmänna riktning och avstånd. Kameraflödet i appen kan till exempel visa en ikon och pil som representerar varje potentiell destination, vilket visas i följande bild.
6. **Förfina vägledningen**: När användaren går kan appen regelbundet beräkna en ny attityd mellan enheten och målankaret. Appen fortsätter att förfina vägledningstipsen som hjälper användaren att komma fram till målet.

    ![Ett exempel på hur en app kan visa vägledning för vägsökning](./media/meeting-spot.png)

## <a name="connect-anchors"></a>Anslut ankare

För att skapa en upplevelse för att hitta ett sätt måste du först placera ankarea på de valda platserna. I det här avsnittet antar vi att appens administratör redan har slutfört det här arbetet.

### <a name="connect-anchors-in-a-single-session"></a>Ansluta fästpunkter i en enda session

Så här ansluter du ankare:

1. Gå till den första platsen och skapa Anchor A med hjälp av en CloudSpatialAnchorSession.
2. Gå till den andra platsen. Den underliggande MR/AR-plattformen spårar rörelsen.
3. Skapa Anchor B med samma CloudSpatialAnchorSession. Ankare A och B är nu anslutna. Tjänsten Spatial Anchors upprätthåller denna relation.
4. Fortsätt proceduren för de återstående ankare.

### <a name="connect-anchors-in-multiple-sessions"></a>Ansluta fästpunkter i flera sessioner

Du kan ansluta rumsliga ankare över flera sessioner. Med den här metoden kan du skapa och ansluta några ankare samtidigt och sedan skapa och ansluta fler fästpunkter.

Så här ansluter du fästpunkter över flera sessioner:

1. Appen skapar några ankare i en CloudSpatialAnchorSession.
2. Vid en annan tidpunkt hittar appen ett av dessa ankare (till exempel Anchor A) med hjälp av en ny CloudSpatialAnchorSession.
3. Gå till en ny plats. Den underliggande plattformen för mixad verklighet eller förstärkt verklighet spårar rörelsen.
4. Skapa Anchor C med samma CloudSpatialAnchorSession. Ankare A, B och C är nu anslutna. Tjänsten Spatial Anchors upprätthåller denna relation.

Du kan fortsätta den här proceduren för fler fästpunkter och fler sessioner över tid.

### <a name="verify-anchor-connections"></a>Verifiera ankaranslutningar

Appen kan verifiera att två fästpunkter är anslutna genom att utfärda en fråga för närliggande fästpunkter. När frågans resultat innehåller målankaret verifieras ankaranslutningen. Om ankaret inte är anslutna kan appen försöka ansluta dem igen.

Här är några anledningar till varför ankare kan misslyckas med att ansluta:

* Den underliggande plattformen för mixad verklighet eller förstärkt verklighet förlorade spårning under processen att ansluta ankare.
* På grund av ett nätverksfel under kommunikationen med tjänsten Spatial Anchors kunde ankaranslutningen inte sparas.

### <a name="find-sample-code"></a>Hitta exempelkod

Information om hur du hittar exempelkod som visar hur du ansluter fästpunkter och gör närliggande frågor finns i [Exempelappar för Spatial Anchors](https://github.com/Azure/azure-spatial-anchors-samples).
