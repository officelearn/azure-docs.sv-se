---
title: Azure Data dataflöde Factory mappning ny gren omvandling
description: Azure Data dataflöde Factory mappning ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792624"
---
# <a name="mapping-data-flow-union-transformation"></a>Mappning av union Dataomvandling för flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Union att kombinera flera dataströmmar till en med den SQL unionen av dessa strömmar som nya utdata från Union transformeringen. Alla schemat från varje Indataströmmen kommer att kombineras i ditt dataflöde, utan att behöva ha en join-nyckel.

Du kan kombinera n antalet dataströmmar i inställningstabellen genom att välja ikonen ”+” bredvid varje konfigurerade rad, inklusive såväl källdata som dataströmmar från befintliga transformeringar i ditt dataflöde.

![Union omvandling](media/data-flow/union.png "Union")

I det här fallet kan du kombinera olika metadata från flera källor (i det här exemplet tre olika källfiler) och kombinera dem i en enda dataström:

![Översikt över Union omvandling](media/data-flow/union111.png "Union 1")

Om du vill uppnå genom att lägga till ytterligare rader i Union inställningarna genom att inkludera alla källa som du vill lägga till. Det finns inget behov av en gemensam lookup- eller join-nyckel:

![Inställningar för Union omvandling](media/data-flow/unionsettings.png "Union inställningar")

Om du ställer in en Select transformation efter din Union kommer du att kunna överlappande fält eller fält som inte med namnet från headerless källor. Klicka på ”Granska” för att se kombinera metadata med 132 kolumner i det här exemplet från tre olika källor:

![Union omvandling sista](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Namn och placering

När du väljer ”union med namnet” kommer varje kolumnvärde släpper i motsvarande kolumn från varje källa, med ett nytt metadataschema sammanslagna.

Om du väljer ”union efter position”, kommer varje kolumnvärde släpper i den ursprungliga placeringen från varje motsvarande källa, vilket resulterar i en ny kombinerade dataström där data från varje källa har lagts till i samma dataström:

![Union utdata](media/data-flow/unionoutput.png "Union utdata")

## <a name="next-steps"></a>Nästa steg

Utforska liknande transformeringar, inklusive [ansluta](data-flow-join.md) och [Exists](data-flow-exists.md).
