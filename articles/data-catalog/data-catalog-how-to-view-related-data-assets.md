---
title: Visa relaterade datatillgångar i Azure Data Catalog
description: Den här artikeln beskrivs hur du visar relaterade datatillgångar för valda datatillgången i Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 156673bfac9bfa38772e4daca166e3431f81c09a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405020"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Så här att visa relaterade datatillgångar i Azure Data Catalog?
Azure Data Catalog kan du visa datatillgångar som rör en valda data tillgången och visa relationer mellan dem. 

## <a name="supported-data-sources"></a>Datakällor som stöds 
När du registrera datatillgångar från följande datakällor registrerar Azure Data Catalog automatiskt metadata om kopplingsrelationer mellan de valda datatillgångarna. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> För Data Catalog för att importera relation mellan två datatillgångar, måste du registrera både tillgångarna på samma gång. Om du har lagt till en av dem separat, lägger du till den igen och andra datatillgången import av relationen mellan dem.

## <a name="view-related-data-assets"></a>Visa relaterade datatillgångar
Du kan visa datatillgångar som är relaterade till en vald datamängd den **relationer** fliken enligt följande bild: 

![Azure Data Catalog – Visa relaterade datatillgångar](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

I det här exemplet finns två relationer för den valda **ProductSubcategory** datatillgång: 

- ProductSubcategoryID kolumn i Product-tabellen har en sekundärnyckelrelation med ProductSubcategoryID kolumn i den markerade tabellen ProductSubcategory. 
- ProductCategoryID kolumnen ProductSubCategory-tabellen har en sekundärnyckelrelation med ProductCategoryID kolumn i den markerade tabellen ProductCategory.

> [!NOTE]
> Observera riktning på pilen i trädvyn relationer.  

Om du vill se mer information, till exempel det fullständigt kvalificerade namnet för kolumnen musen över och du ser ett fönster som liknar följande bild: 

![Azure Data Catalog – popup-fönstret för relationen](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

För att inkludera relationer mellan tillgångar som redan har registrerats, Omregistrera dessa tillgångar.

## <a name="next-steps"></a>Nästa steg
- [Hantera datatillgångar](data-catalog-how-to-manage.md)