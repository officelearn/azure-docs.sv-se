---
title: Azure Data Factory mappa data flöde ny gren omvandling
description: Azure Data Factory mappa data flöde ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029917"
---
# <a name="mapping-data-flow-union-transformation"></a>Mappa data Flow union-transformering



Union kombinerar flera data strömmar till en, med SQL-unionen av dessa strömmar som nya utdata från Union-omvandlingen. Alla scheman från varje indataströmmen kommer att kombineras i ditt data flöde, utan att ha en kopplings nyckel.

Du kan kombinera n-antal strömmar i inställnings tabellen genom att välja ikonen "+" bredvid varje konfigurerad rad, inklusive både käll data och strömmar från befintliga transformationer i ditt data flöde.

Unions ![omtransformerings](media/data-flow/union.png "union")

I det här fallet kan du kombinera olika metadata från flera källor (i det här exemplet tre olika källfiler) och kombinera dem till en enda ström:

![Översikt över union-omvandling],(media/data-flow/union111.png "union 1")

För att åstadkomma detta lägger du till ytterligare rader i unions inställningarna genom att inkludera alla källor som du vill lägga till. Det finns inget behov av en gemensam Sök-eller kopplings nyckel:

(media/data-flow/unionsettings.png "Inställningar") för ![union Transformation Settings]

Om du ställer in en Välj omvandling efter din union, kan du byta namn på överlappande fält eller fält som inte har namngetts från huvudets huvud källor. Klicka på "inspektera" om du vill se kombinera metadata med 132 totala kolumner i det här exemplet från tre olika källor:

![Union-transformering slut](media/data-flow/union333.png "union 3")

## <a name="name-and-position"></a>Namn och position

När du väljer "union efter namn", kommer varje kolumn värde att släppas i motsvarande kolumn från varje källa med ett nytt sammanfogat metadata-schema.

Om du väljer "union by position" kommer varje kolumn värde att släppas till den ursprungliga positionen från varje motsvarande källa, vilket resulterar i en ny kombinerad data ström där data från varje källa läggs till i samma ström:

![Union]output-utdata i(media/data-flow/unionoutput.png "union")

## <a name="next-steps"></a>Nästa steg

Utforska liknande omvandlingar inklusive [Join](data-flow-join.md) och [exists](data-flow-exists.md).
