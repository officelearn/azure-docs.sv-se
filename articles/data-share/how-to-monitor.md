---
title: Övervaka Azures data resurs
description: Lär dig hur du övervakar status för inbjudan, delar prenumerationer och ögonblicks bilds historik i Azure Data Share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 15089dd99de0471c244a6c0d93931438442599a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490505"
---
# <a name="monitor-azure-data-share"></a>Övervaka Azure Data Share  

Den här artikeln förklarar hur du kan övervaka dina data resurser med Azure Data Share. Som data leverantör kan du övervaka olika aspekter av dina data delnings relationer. Information som till exempel om dina data konsumenter har accepterat din inbjudan till data resursen, samt om de har skapat en resurs prenumeration och börjat använda dina data, är tillgängliga för övervakning. 

Som data konsument kan du övervaka ögonblicks bilderna som har utlösts i din Azure-prenumeration. 

## <a name="monitor-invitation-status"></a>Status för övervaka inbjudan

Visa statusen för dina inbjudningar för data delning genom att navigera till skickade resurser – > inbjudningar. 

![Status för inbjudan](./media/invitation-status.png "Status för inbjudan") 

Din inbjudan kan vara i tre lägen:

* Väntande-data delnings mottagare har ännu inte accepterat inbjudan.
* Godkänd – mottagaren av data delning har accepterat inbjudan.
* Avvisad-data delnings mottagare har avvisat inbjudan.

> [!IMPORTANT]
> Om du tar bort en inbjudan när den redan har godkänts är den inte likvärdig med att återkalla åtkomst. Om du vill stoppa framtida ögonblicks bilder från att kopieras till ditt data konsument lagrings konto måste du återkalla åtkomsten via fliken *dela prenumerationer* . 

## <a name="monitor-share-subscriptions"></a>Övervaka resurs prenumerationer

Visa statusen för dina resurs prenumerationer genom att navigera till skickade resurser-> dela prenumerationer. Detta ger dig information om aktiva prenumerationer som skapats av dina data konsumenter när du har accepterat din inbjudan. Du kan stoppa framtida uppdateringar av din data konsument genom att välja resurs prenumerationen och välja *återkalla*. 

## <a name="snapshot-history"></a>Ögonblicks bilds historik 

På fliken Historik kan du Visa ögonblicks bilderna som har kopierats till din data konsument klient. Du kan övervaka frekvens och varaktighet för varje ögonblicks bilds intervall. 

![Ögonblicks bilds historik](./media/sent-shares.png "Ögonblicks bilds historik") 

Du kan visa mer information om varje ögonblicks bild körning genom att klicka på start datum för körning. 

Upp till 30 dagar från ögonblicks bilds historik visas som standard. Om du behöver se historiken mer än 30 dagar går du till övervakning-> diagnostikinställningar och väljer **Lägg till diagnostisk inställning**. Du måste välja ett lagrings konto för att lagra loggarna i. 

![Ögonblicks bilds historik](./media/diagnostic-settings.png "Diagnostikinställningar") 

## <a name="next-steps"></a>Nästa steg 

Lär dig mer om [Azure Data Share-terminologi](terminology.md)