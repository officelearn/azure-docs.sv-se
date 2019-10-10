---
title: Övervaka för hands versionen av Azure Data Share
description: Lär dig hur du övervakar status för inbjudan, delar prenumerationer och ögonblicks bilds historik i Azure Data repreview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 22c95f1ac541e1288494ed85cc9654d42780ea60
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169129"
---
# <a name="monitor-azure-data-share-preview"></a>Övervaka för hands versionen av Azure Data Share 

Den här artikeln förklarar hur du kan övervaka dina data resurser med hjälp av Azure Data repreview. Som data leverantör kan du övervaka olika aspekter av dina data delnings relationer. Information som till exempel om dina data konsumenter har accepterat din inbjudan till data resursen, samt om de har skapat en resurs prenumeration och börjat använda dina data, är tillgängliga för övervakning. 

Som data konsument kan du övervaka ögonblicks bilderna som har utlösts i din Azure-prenumeration. 

## <a name="monitor-invitation-status"></a>Status för övervaka inbjudan

Visa statusen för dina inbjudningar för data delning genom att navigera till skickade resurser – > inbjudningar. 

![](./media/invitation-status.png "Status") för Inbjudnings status för inbjudan 

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

Ögonblicks(./media/sent-shares.png "bild") historik för ![ögonblicks bild historik] 

Du kan visa mer information om varje ögonblicks bild körning genom att klicka på start datum för körning. 

Upp till 30 dagar från ögonblicks bilds historik visas som standard. Om du behöver se historiken mer än 30 dagar går du till övervakning-> diagnostikinställningar och väljer **Lägg till diagnostisk inställning**. Du måste välja ett lagrings konto för att lagra loggarna i. 

(./media/diagnostic-settings.png "Diagnostiska inställningar") för ![ögonblicks bild historik] 

## <a name="next-steps"></a>Nästa steg 

Lär dig mer om [Azure Data Share-terminologi](terminology.md)