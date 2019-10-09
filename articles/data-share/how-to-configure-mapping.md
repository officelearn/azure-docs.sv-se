---
title: Konfigurera en data uppsättnings mappning i för hands versionen av Azure Data Share
description: Lär dig hur du konfigurerar en data uppsättnings mappning för en mottagen resurs med hjälp av Azure Data Share Preview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169141"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Så här konfigurerar du en data uppsättnings mappning för en mottagen resurs i för hands versionen av Azure Data Share

Den här artikeln beskriver hur du konfigurerar en data uppsättnings mappning för en mottagen resurs med hjälp av Azure Data Share Preview. Du måste göra detta om du har accepterat en inbjudan om data delning men valt att godkänna och konfigurera senare. Annars kanske du bara vill ändra mål lagrings kontot för dina mottagna data. 

## <a name="navigate-to-a-received-data-share"></a>Navigera till en mottagen data resurs

I Azure Data Share-tjänsten navigerar du till din mottagna resurs och väljer fliken **information** . 

Mappning(./media/dataset-mapping.png "av data uppsättning") för data ![uppsättning mappning] 

Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål till och klicka på **+ Mappa till mål**. Du kan behöva göra en mappning först om du redan har konfigurerat ett mål lagrings konto och du vill ändra mappningen till ett annat lagrings konto. 

![Mappa till]mål(./media/dataset-map-target.png "kartan") 

## <a name="select-a-new-storage-account"></a>Välj ett nytt lagrings konto 

Välj ett lagrings konto som du vill att data ska landa i. Observera att alla data som redan finns i tidigare mappade lagrings konton inte kommer att flyttas automatiskt till det nya lagrings kontot.

Mål(./media/map-target.png "lagring") för ![mål lagrings konto] 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .



