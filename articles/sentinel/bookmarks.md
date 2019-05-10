---
title: Håll koll på data medan jakt i Sentinel-förhandsversionen av Azure med hjälp av jakt bokmärken | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Sentinel jakt bokmärken för att hålla reda på data.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: b1a438b9645dbb37d852eb0092355850d816872d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65207487"
---
# <a name="keep-track-of-data-during-hunting"></a>Håll koll på data under jakt

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Threat jakt kräver normalt att granska bergen av loggdata som söker efter tecken på skadligt beteende. Under den här processen kan hitta fler människor händelser som ska komma ihåg, gå tillbaka och analysera som en del av verifiera potentiella hypoteser och förstå helheten för en kompromettering.
Jakt bokmärken hjälper dig att göra detta genom att bevara de frågor som du körde i Log Analytics, tillsammans med frågeresultatet som du anser relevanta. Du kan även spela in din sammanhangsberoende observationer och referera till dina resultat genom att lägga till kommentarer och taggar. Bokmärkta data är synliga för dig och dina gruppmedlemmar för enkelt samarbete.   

Du kan gå tillbaka till dina bokmärken data när som helst på den **bokmärke** fliken den **jakt** sidan. Du kan använda filtrering och söka alternativ för att snabbt hitta specifika data för din aktuella undersökning. Alternativt kan du visa dina bokmärken data direkt i den **HuntingBookmark** tabellen i Log Analytics. På så sätt kan du filtrera sammanfatta och ansluta till bokmärkta data med andra datakällor, vilket gör det enkelt att söka efter bekräftar bevis.

Du kan också visualisera dina bokmärken data genom att klicka på **Undersök**. Detta startar felsökningsfunktionen där du kan visa, undersöka och förmedla visuellt dina resultat med hjälp av en interaktiv entitet-graph diagram och en tidslinje.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Kör en Log Analytics-fråga från Sentinel-Azure

1. Sentinel-Azure-portalen klickar du på **jakt** att köra frågor för misstänkta och avvikande beteende.

1. Om du vill köra en jakt kampanj, väljer du en av frågorna som jakt och på vänster och granska resultaten. 

1. Klicka på **visa frågeresultaten** i jakt frågan **information** sidan kan du se frågan resulterar i Log Analytics. Här är ett exempel på vad som visas om du körde en anpassad fråga för SSH bruteforce attack.
  
   ![Visa resultat](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Lägga till ett bokmärke

1. Expandera den rad som innehåller den information som du hittar intressant i resultatlistan för Log Analytics-fråga.

4. Välj ellipsen (...) i slutet av raden och välj **lägga till jakt bokmärken**.
5. Till höger i den **information** sidan uppdatera namn och lägga till taggar och kommentarer för att identifiera vad var intressant om objektet.
6. Klicka på **spara** att genomföra ändringarna. Alla bokmärken data delas med andra fler människor och är ett första steg mot att en samarbetsfunktioner felsökningsfunktionen.

   ![Visa resultat](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Du kan också använda bokmärken med valfri Log Analytics-frågor som startas från Azure Sentinel-Log Analytics loggar sidan eller frågor som skapats på menyn från sidan Log Analytics och öppnade från sidan jakt. Du kommer inte att kunna lägga till ett bokmärke om du startar Log Analytics från utanför Azure Sentinel. 

## <a name="view-and-update-bookmarks"></a>Visa och uppdatera bokmärken 

1. Sentinel-Azure-portalen klickar du på **jakt**. 
2. Klicka på den **bokmärken** fliken mitt sidan för att visa en lista över bokmärken.
3. Använd sökalternativen för box eller filter för att hitta ett specifikt bokmärke.
4. Välj enskilda bokmärken i rutnätet nedan för att visa information om bokmärke i informationsfönstret höger.
5. För att uppdatera taggar och kommentarer, klicka på de redigerbara textrutorna och på **spara** att bevara dina ändringar.

   ![Visa resultat](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Visa bokmärken data i Log Analytics 

Det finns flera alternativ för att visa dina bokmärken data i Log Analytics. 

Det enklaste sättet att visa bokmärkta frågor, resultat eller historik är genom att välja önskad bokmärket i den **bokmärken** tabellen och använda länkarna i informationsfönstret. Alternativen är: 
- Klicka på **vyfrågan** visa källfrågan i Log Analytics.  
- Klicka på **visa bokmärke historik** att se alla bokmärk metadata, inklusive: vem som lagade uppdateringen, de uppdaterade värdena och den tid som uppdateringen genomfördes. 

- Du kan också visa rå bokmärke data för alla bokmärken genom att klicka på **bokmärk loggar** ovanför rutnätet bokmärke. Den här vyn visas alla dina bokmärken i tabellen jakt bokmärke med tillhörande metadata. Du kan använda KQL frågor för att filtrera fram den senaste versionen av specifika bokmärket du letar efter.  


> [!NOTE]
> Det kan vara betydande fördröjning (mätt i minuter) mellan skapandet av ett bokmärke och när den visas i den **HuntingBookmark** tabell. Vi rekommenderar att skapa dina bokmärken först och sedan analysera dem när data matas in. 

## <a name="delete-a-bookmark"></a>Ta bort ett bokmärke
Om du vill ta bort ett bokmärke genom att göra följande: 
1.  Öppna th **jakt bokmärke** fliken. 
2.  Välj Målet bokmärket.
3.  Välj ellipsen (...) i slutet av raden och välj **ta bort bokmärke**.
    
Tar bort bokmärket försvinner bokmärket från listan i den **bokmärke** fliken.  Log Analytics ”HuntingBookmark” tabell fortsätter att finnas poster som föregående bokmärke, men den senaste posten kommer att ändras i **SoftDelete** värdet till SANT, vilket gör det enkelt att filtrera bort gamla bokmärken.  Tar bort ett bokmärke tar inte bort några entiteter från undersökning som är associerade med andra bokmärken eller aviseringar. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du kör en jakt undersökning använda bokmärken i Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:


- [Hitta proaktivt hot](hunting.md)
- [Använda anteckningsböcker för att köra automatiserade jakt kampanjer](notebooks.md)