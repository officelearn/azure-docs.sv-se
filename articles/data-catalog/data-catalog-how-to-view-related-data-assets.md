---
title: "Visa relaterade datatillgångar i Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln förklarar hur du visar relaterade datatillgångar för en tillgång med markerade data i Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/17/2017
ms.author: maroche
ms.openlocfilehash: d45f2cabe712a7982f99a9d280fed4494fc4d377
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hur du visar relaterade datatillgångar i Azure Data Catalog?
Azure Data Catalog kan du visa datatillgångar som rör en valda data tillgången och visa relationer mellan dem. 

## <a name="supported-data-sources"></a>Datakällor som stöds 
När du registrerar datatillgångar från följande datakällor registrerar Azure Data Catalog automatiskt metadata om kopplingsrelationer mellan de valda datatillgångarna. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>Visa relaterade datatillgångar
Du kan visa datatillgångar som är relaterade till en vald datamängd den **relationer** fliken som visas i följande bild: 

![Azure Data Catalog – Visa relaterade datatillgångar](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

I det här exemplet har två relationer för den valda **ProductSubcategory** datatillgång: 

- ProductSubcategoryID kolumnen i tabellen produkten har en sekundärnyckelrelation med ProductSubcategoryID kolumn i den markerade tabellen ProductSubcategory. 
- ProductCategoryID kolumnen i tabellen ProductSubCategory har en sekundärnyckelrelation med ProductCategoryID kolumn i den markerade tabellen produktkategori.

> [!NOTE]
> Observera riktning på pilen i trädvyn relationer.  

Om du vill se mer information, till exempel det fullständigt kvalificerade namnet på kolumnen flyttar musen över och du ser ett popup-fönster liknar följande bild: 

![Azure Data Catalog - relation popup](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Om du vill inkludera relationer mellan tillgångar som redan har registrerats, registrera dessa tillgångar.

## <a name="next-steps"></a>Nästa steg
- [Hantera datatillgångar](data-catalog-how-to-manage.md)