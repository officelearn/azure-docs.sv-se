---
title: Så här övervakar du Azure Data Share
description: Lär dig hur du övervakar inbjudningsstatus, dela prenumerationer och ögonblicksbildhistorik i Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490505"
---
# <a name="monitor-azure-data-share"></a>Övervaka Azure Data Share  

I den här artikeln beskrivs hur du kan övervaka dina dataresurser med Hjälp av Azure Data Share. Som dataleverantör kan du övervaka olika aspekter av dina datadelningsrelationer. Information om huruvida dina datakonsumenter har accepterat din inbjudan till dataresursen, samt om de har skapat en delningsprenumeration och börjat använda dina data är alla tillgängliga för övervakning. 

Som datakonsument kan du övervaka ögonblicksbilder som har utlösts i din Azure-prenumeration. 

## <a name="monitor-invitation-status"></a>Övervaka inbjudningsstatus

Visa status för dina inbjudningar till datadelning genom att navigera till Skickade resurser -> inbjudningar. 

![Status för inbjudan](./media/invitation-status.png "Status för inbjudan") 

Det finns tre tillstånd som din inbjudan kan vara i:

* Väntande - Mottagare av datadelning har ännu inte accepterat inbjudan.
* Accepterad - Mottagare av datadelning har accepterat inbjudan.
* Avvisad - Dataresursmottagaren har avvisat inbjudan.

> [!IMPORTANT]
> Om du tar bort en inbjudan efter att den redan har accepterats är det inte likvärdigt med att återkalla åtkomsten. Om du vill förhindra att framtida ögonblicksbilder kopieras till ditt lagringskonto för datakonsumenter måste du återkalla åtkomsten via fliken *Dela prenumerationer.* 

## <a name="monitor-share-subscriptions"></a>Övervaka aktieprenumerationer

Visa status för dina aktieprenumerationer genom att navigera till Skickade resurser -> Aktieprenumerationer. Detta ger dig information om aktiva prenumerationer som skapats av dina datakonsumenter efter att ha accepterat din inbjudan. Du kan stoppa framtida uppdateringar av din datakonsument genom att välja delningsprenumerationen och välja *Återkalla*. 

## <a name="snapshot-history"></a>Ögonblicksbild historia 

På fliken historik kan du visa de ögonblicksbilder som har kopierats till datakonsumentens klientorganisation. Du kan övervaka frekvensen och varaktigheten för varje ögonblicksbildintervall. 

![Ögonblicksbild historia](./media/sent-shares.png "Ögonblicksbild historia") 

Du kan visa mer information om varje ögonblicksbild som körs genom att klicka på startdatumet för körningen. 

Upp till 30 dagars ögonblicksbildhistorik visas som standard. Om du behöver se historiken för mer än 30 dagar navigerar du till Övervaknings-> diagnostikinställningar och väljer **Lägg till diagnostikinställning**. Du måste välja ett lagringskonto för att lagra dessa loggar i. 

![Ögonblicksbild historia](./media/diagnostic-settings.png "Diagnostikinställningar") 

## <a name="next-steps"></a>Efterföljande moment 

Läs mer om [terminologin för Azure Data Share](terminology.md)