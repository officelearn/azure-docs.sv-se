---
title: Azure Data Factory mappning väljer Dataomvandling för Flow
description: Azure Data Factory mappning väljer Dataomvandling för Flow
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364483"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory mappning väljer Dataomvandling för Flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd den här transformationen för kolumnen selektivitet (minskar antalet kolumner) eller till stream namn och alias kolumner.

Välj transformeringen kan du till alias en hela stream eller kolumner i dataströmmen, tilldela olika namn (alias) och sedan referera till de nya namnen senare i ditt dataflöde. Den här transformeringen är användbart för självkoppling scenarier. Sätt att implementera en självkoppling i ADF dataflöde är att ta en dataström, gren det med ”ny gren” och sedan omedelbart efteråt lägga till en ”Välj” transformering. Dataströmmen har nu ett nytt namn som du kan använda för att ansluta till den ursprungliga stream, skapar en självkoppling:

![Självkoppling](media/data-flow/selfjoin.png "självkoppling")

I diagrammet ovan är väljer vi högst upp. Det här är alias ursprungliga direkt för att ”OrigSourceBatting”. Du kan se att vi använder dataströmmen väljer alias som kopplingen till höger så att vi kan referera till samma nyckel i både vänster och höger sida av den inre koppling i higlighted Join transformeringen under den.

Välj kan också användas som ett sätt avmarkerar kolumner från ditt dataflöde. Till exempel om du har 6 kolumner som definierats i dina mottagare, men du vill välja en specifik 3 för att omvandla och sedan flöda till mottagaren, kan du välja de 3 med hjälp av väljer transformeringen.

> [!NOTE]
> Du måste stänga av ”Markera alla” att välja endast specifika kolumner

Alternativ

Standardinställningen för ”Välj” är att inkludera alla inkommande kolumner och behålla de ursprungliga namn. Du kan alias dataströmmen genom att ange namnet på väljer transformeringen.

Avmarkera ”Markera alla” till alias enskilda kolumner, och Använd kolumnmappningen längst ned på sidan.

![Välj omvandling](media/data-flow/select001.png "Välj Alias")
