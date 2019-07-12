---
title: Så här övervakar du Azure förhandsgranskning för resursen
description: Så här övervakar du Azure förhandsgranskning för resursen
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789103"
---
# <a name="monitor-azure-data-share-preview"></a>Övervaka Azure resurs förhandsgranskning 

Den här artikeln förklarar hur du kan övervaka dina data-filresurser med hjälp av Azure förhandsgranskning för resursen. Som en dataleverantör kan du övervaka olika aspekter av din Datadelning relationer. Information om till exempel om datakonsumenterna som har accepterat din inbjudan till Dataresurs samt om de har skapat en filresurs-prenumeration och börjat använda dina data är tillgängliga att övervaka. 

Du kan övervaka ögonblicksbilder som har utlösts i din Azure-prenumeration som en datakonsument. 

## <a name="monitor-invitation-status"></a>Övervakarstatus för inbjudan

Visa status för dina data resurs inbjudningar genom att gå till skickade resurser -> inbjudningar. 

![Status för inbjudan](./media/invitation-status.png "status för inbjudan") 

Det finns tre tillstånd som din inbjudan kan vara:

* Väntande - accepterat Data resurs mottagaren inte har inbjudan.
* Godkänd - har Data resurs mottagaren accepterat inbjudan.
* Avvisade - har Data resurs mottagaren avvisat inbjudan.

> [!IMPORTANT]
> Om du tar bort en inbjudan när den har redan accepterats motsvarar det inte att återkalla åtkomst. Om du vill stoppa framtida ögonblicksbilder från att kopieras till dina data konsumenter storage-konto måste du återkalla åtkomst via den *dela prenumerationer* fliken. 

## <a name="monitor-share-subscriptions"></a>Övervaka resurs prenumerationer

Visa status för dina resurs-prenumerationer genom att gå till skickas resurser -> Dela prenumerationer. Detta ger dig information om aktiva prenumerationer som skapats av datakonsumenterna efter att du godkänt din inbjudan. Du kan stoppa framtida uppdateringar till datakonsument genom att välja prenumerationen som resursen och välja *återkalla*. 

## <a name="snapshot-history"></a>Historik för ögonblicksbild 

På fliken Historik omfattar kan du visa ögonblicksbilder som har kopierats till din datakonsumenten klient i. Du kan övervaka frekvens och varaktighet för varje ögonblicksintervallet. 

![Ta en ögonblicksbild historik](./media/sent-shares.png "ögonblicksbild historik") 

Du kan visa mer information om varje ögonblicksbild som körs genom att klicka på Kör startdatumet. 

## <a name="next-steps"></a>Nästa steg 

Läs mer om [Dataresurs för Azure-terminologi](terminology.md)