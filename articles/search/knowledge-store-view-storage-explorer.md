---
title: Visa ett kunskaps lager med Storage Explorer-Azure Search
description: Visa och analysera en Azure Search kunskaps lager med Azure Portals Storage Explorer.
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 6c90cec91e85f64397f70b015ffde15a2ea6deaf
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962975"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visa ett kunskaps lager med Storage Explorer

> [!Note]
> Kunskaps lagret är i för hands version och bör inte användas i produktionen. [Azure Search REST API version 2019-05-06 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för .NET SDK.
>
I den här artikeln får du lära dig hur du ansluter och utforskar ett kunskaps lager med hjälp av Storage Explorer i Azure Portal. Om du vill skapa ett kunskaps lager exempel som används i den här genom gången, se [skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Förutsättningar

+ Följ stegen i [skapa ett kunskaps lager i Azure Portal](knowledge-store-create-portal.md) för att skapa ett exempel på kunskaps lager som används i den här genom gången.

+ Du kommer också att behöva namnet på det Azure Storage-konto som du använde för att skapa kunskaps lagret, tillsammans med dess åtkomst nyckel från Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visa, redigera och fråga ett kunskaps lager i Storage Explorer

1. Öppna det lagrings konto som du använde för att skapa kunskaps lagret i Azure Portal.

1. I lagrings kontots vänstra navigerings fönster klickar du på **Storage Explorer**.

1. Expandera listan **tabeller** om du vill visa en lista över Azure Table-projektioner som skapades när du körde guiden **Importera data** på din hotell gransknings exempel data.

Välj en tabell för att visa de utförliga data, inklusive nyckel fraser sentiment poäng, latitud data och longitud.

   ![Visa tabeller i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visa tabeller i Storage Explorer")

Om du vill ändra data typen för ett tabell värde eller ändra enskilda värden i tabellen, klickar du på **Redigera**. När du ändrar data typen för en kolumn i en tabell rad, kommer den att tillämpas på alla rader.

   ![Redigera tabell i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Redigera tabell i Storage Explorer")

Om du vill köra frågor klickar du på **fråga** i kommando fältet och anger dina villkor.  

   ![Fråg Bell i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Fråg Bell i Storage Explorer")

## <a name="clean-up"></a>Rensa

När du arbetar med din egen prenumeration är det en bra idé i slutet av ett projekt för att identifiera om du fortfarande behöver de resurser som du har skapat. Resurser som har lämnats igång kostar dig pengar. Du kan ta bort resurser individuellt eller ta bort resurs gruppen för att ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Information om hur du ansluter det här kunskaps lagret till Power BI finns i följande artikel.

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)

Information om hur du skapar ett kunskaps lager med hjälp av REST-API: er och Postman finns i följande artikel.  

> [!div class="nextstepaction"]
> [Skapa ett kunskaps lager i REST](knowledge-store-howto.md)
