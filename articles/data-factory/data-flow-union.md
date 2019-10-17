---
title: Azure Data Factory mappa data flöde ny gren omvandling
description: Azure Data Factory mappa data flöde ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 4832cd2036f615d1e90d5e7a21c1a9832c2fa837
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387137"
---
# <a name="mapping-data-flow-union-transformation"></a>Mappa data Flow union-transformering



Union kombinerar flera data strömmar till en, med SQL-unionen av dessa strömmar som nya utdata från Union-omvandlingen. Alla scheman från varje indataströmmen kommer att kombineras i ditt data flöde, utan att ha en kopplings nyckel.

Du kan kombinera n-antal strömmar i inställnings tabellen genom att välja ikonen "+" bredvid varje konfigurerad rad, inklusive både käll data och strömmar från befintliga transformationer i ditt data flöde.

![Union-transformering](media/data-flow/union.png "Union")

I det här fallet kan du kombinera olika metadata från flera källor (i det här exemplet tre olika källfiler) och kombinera dem till en enda ström:

![Översikt över union-omvandling](media/data-flow/union111.png "Union 1")

För att åstadkomma detta lägger du till ytterligare rader i unions inställningarna genom att inkludera alla källor som du vill lägga till. Det finns inget behov av en gemensam Sök-eller kopplings nyckel:

![Omvandlings inställningar för union](media/data-flow/unionsettings.png "Unions inställningar")

Om du ställer in en Välj omvandling efter din union, kan du byta namn på överlappande fält eller fält som inte har namngetts från huvudets huvud källor. Klicka på "inspektera" om du vill se kombinera metadata med 132 totala kolumner i det här exemplet från tre olika källor:

![Unions transformering slut](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>Namn och position

När du väljer "union efter namn", kommer varje kolumn värde att släppas i motsvarande kolumn från varje källa med ett nytt sammanfogat metadata-schema.

Om du väljer "union by position" kommer varje kolumn värde att släppas till den ursprungliga positionen från varje motsvarande källa, vilket resulterar i en ny kombinerad data ström där data från varje källa läggs till i samma ström:

![Union-utdata](media/data-flow/unionoutput.png "Union-utdata")

## <a name="next-steps"></a>Nästa steg

Utforska liknande omvandlingar inklusive [Join](data-flow-join.md) och [exists](data-flow-exists.md).
