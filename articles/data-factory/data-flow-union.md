---
title: Mappa omvandling av dataflödesunionen
description: Azure Data Factory mappning dataflöde Ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: adba1eb61676dbebcb356490b14b279ebe69c644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930154"
---
# <a name="azure-data-factory-mapping-data-flow-union-transformation"></a>Azure Data Factory-mappning av dataflödesunionen

Unionen kommer att kombinera flera dataströmmar till en, med SQL Union för dessa strömmar som den nya produktionen från unionsomvandlingen. Alla schema från varje indataström kombineras inuti dataflödet, utan att behöva ha en kopplingsnyckel.

Du kan kombinera n-antal strömmar i inställningstabellen genom att välja "+"-ikonen bredvid varje konfigurerad rad, inklusive både källdata och strömmar från befintliga omvandlingar i dataflödet.

![Unionens omvandling](media/data-flow/union.png "Union")

I det här fallet kan du kombinera olika metadata från flera källor (i det här exemplet tre olika källfiler) och kombinera dem till en enda ström:

![Översikt över unionsomvandling](media/data-flow/union111.png "Union 1")

För att uppnå detta lägger du till ytterligare rader i unionsinställningarna genom att inkludera alla källor som du vill lägga till. Det finns inget behov av en gemensam sökning eller kopplingsnyckel:

![Inställningar för unionsomvandling](media/data-flow/unionsettings.png "Unionsinställningar")

Om du anger en Select-omvandling efter union kan du byta namn på överlappande fält eller fält som inte har namngetts från huvudlösa källor. Klicka på "Inspektera" för att se kombinera metadata med 132 totalt kolumner i det här exemplet från tre olika källor:

![Slutlig final för unionsomvandling](media/data-flow/union333.png "Unionen 3")

## <a name="name-and-position"></a>Namn och befattning

När du väljer "union by name" kommer varje kolumnvärde att sjunka in i motsvarande kolumn från varje källa, med ett nytt sammanfogat metadataschema.

Om du väljer "union by position" kommer varje kolumnvärde att sjunka in i den ursprungliga positionen från varje motsvarande källa, vilket resulterar i en ny kombinerad dataström där data från varje källa läggs till i samma flöde:

![Unionens resultat](media/data-flow/unionoutput.png "Unionens resultat")

## <a name="next-steps"></a>Nästa steg

Utforska liknande omvandlingar, inklusive [Gå med](data-flow-join.md) och [finns](data-flow-exists.md).
