---
title: Visa relaterade datatillgångar i Azure Data Catalog
description: I den här artikeln beskrivs hur du visar relaterade datatillgångar för en vald datatillgång i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68737017"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hur visar du relaterade datatillgångar i Azure Data Catalog?
Med Azure Data Catalog kan du visa datatillgångar som är relaterade till en vald datatillgång och visa relationer mellan dem. 

## <a name="supported-data-sources"></a>Datakällor som stöds 
När du registrerar datatillgångar från följande datakällor registrerar Azure Data Catalog automatiskt metadata om kopplingsrelationer mellan de valda datatillgångarna. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> För att datakatalogen ska kunna importera relation mellan två datatillgångar måste du registrera båda tillgångarna samtidigt. Om du hade lagt till en av dem separat lägger du till den igen och den andra datatillgången för att importera relationen mellan dem.

## <a name="view-related-data-assets"></a>Visa relaterade datatillgångar
Om du vill visa datatillgångar som är relaterade till en markerad datauppsättning använder du fliken **Relationer** som visas i följande bild: 

![Azure Data Catalog - Visa relaterade datatillgångar](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

I det här exemplet finns det två relationer för den valda **productSubcategory-datatillgången:** 

- Kolumnen ProductSubcategoryID i tabellen Produkt har en sekundärnyckelrelation med kolumnen ProductSubcategoryID i den valda tabellen ProductSubcategory. 
- Kolumnen ProductCategoryID i tabellen ProductSubCategory har en sekundärnyckelrelation med kolumnen ProductCategoryID i den valda productcategory-tabellen.

> [!NOTE]
> Lägg märke till pilens riktning i relationsträdsvyn.  

Om du vill se mer information, till exempel det fullständigt kvalificerade namnet på kolumnen, flyttar du musen över och du ser ett popup-fönster som liknar följande bild: 

![Azure Data Catalog - Relation popup](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Om du vill inkludera relationer mellan tillgångar som redan har registrerats registrerar du dessa tillgångar.

## <a name="next-steps"></a>Nästa steg
- [Hantera datatillgångar](data-catalog-how-to-manage.md)