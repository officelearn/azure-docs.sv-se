---
title: Visa ett kunskapsarkiv (förhandsgranskning) med Storage Explorer
titleSuffix: Azure Cognitive Search
description: Visa och analysera ett Azure Cognitive Search-kunskapsarkiv med Azure-portalens Storage Explorer. kunskapsarkivet är för närvarande i offentlig förhandsversion.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754063"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Visa ett kunskapsarkiv med Storage Explorer

> [!IMPORTANT] 
> Knowledge Store är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller förhandsgranskningsfunktioner. Det finns för närvarande begränsat portalstöd och inget .NET SDK-stöd.

I den här artikeln får du exempel lära dig hur du ansluter till och utforskar ett kunskapslager med Storage Explorer i Azure-portalen.

## <a name="prerequisites"></a>Krav

+ Följ stegen i [Skapa ett kunskapsarkiv i Azure-portalen](knowledge-store-create-portal.md) för att skapa exempelkunskapsarkivet som används i den här genomgången.

+ Du behöver också namnet på Azure-lagringskontot som du använde för att skapa kunskapsarkivet, tillsammans med dess åtkomstnyckel från Azure-portalen.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Visa, redigera och fråga ett kunskapsarkiv i Storage Explorer

1. Öppna det lagringskonto som du använde för att skapa [kunskapsarkivet i](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) Azure-portalen.

1. Klicka på **Lagringsutforskaren**i det vänstra navigeringsfönstret för lagringskontot.

1. Expandera listan **TABELLER** om du vill visa en lista över Azure-tabellprojektioner som skapades när du körde guiden **Importera data** på exempeldata för hotellrecensioner.

Markera en tabell om du vill visa utökade data, inklusive nyckelfraser och sentimentpoäng.

   ![Visa tabeller i Storage Explorer](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Visa tabeller i Storage Explorer")

Om du vill ändra datatypen för ett tabellvärde eller ändra enskilda värden i tabellen klickar du på **Redigera**. När du ändrar datatypen för en kolumn på en tabellrad tillämpas den på alla rader.

   ![Redigera tabell i Lagringsutforskaren](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Redigera tabell i Lagringsutforskaren")

Om du vill köra frågor klickar du på **Fråga** i kommandofältet och anger dina villkor.  

   ![Frågetabell i Utforskaren för lagring](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Frågetabell i Utforskaren för lagring")

## <a name="clean-up"></a>Rensa

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Anslut det här kunskapsarkivet till Power BI för djupare analys, eller gå vidare med kod, med HJÄLP av REST API och Postman för att skapa ett annat kunskapslager.

> [!div class="nextstepaction"]
> [Anslut med Power BI](knowledge-store-connect-power-bi.md)
> [Skapa ett kunskapslager i REST](knowledge-store-create-rest.md)
