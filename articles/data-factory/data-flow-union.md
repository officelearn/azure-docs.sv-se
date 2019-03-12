---
title: Azure Data dataflöde Factory mappning ny gren omvandling
description: Azure Data dataflöde Factory mappning ny gren omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 1eebc879ad56ba4f35e6a8a1b857ae877a6a2f01
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726268"
---
# <a name="mapping-data-flow-union-transformation"></a>Mappning av union Dataomvandling för flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Union att kombinera flera dataströmmar till en med den SQL unionen av dessa strömmar som nya utdata från Union transformeringen. Alla schemat från varje Indataströmmen kommer att kombineras i ditt dataflöde, utan att behöva ha en join-nyckel.

Du kan kombinera n antalet dataströmmar i inställningstabellen genom att välja ikonen ”+” bredvid varje konfigurerade rad.

![Union omvandling](media/data-flow/union.png "Union")

I det här fallet kan du kombinera olika metadata från flera källor (i det här exemplet tre olika källfiler) och kombinera dem i en enda dataström:

![Översikt över Union omvandling](media/data-flow/union111.png "Union 1")

Om du vill uppnå genom att lägga till ytterligare rader i Union inställningarna genom att inkludera alla källa som du vill lägga till. Det finns inget behov av en gemensam lookup- eller join-nyckel:

![Inställningar för Union omvandling](media/data-flow/unionsettings.png "Union inställningar")

Om du ställer in en Select transformation efter din Union kommer du att kunna överlappande fält eller fält som inte med namnet från headerless källor. Klicka på ”Granska” för att se kombinera metadata med 132 kolumner i det här exemplet från tre olika källor:

![Union omvandling sista](media/data-flow/union333.png "Union 3")
