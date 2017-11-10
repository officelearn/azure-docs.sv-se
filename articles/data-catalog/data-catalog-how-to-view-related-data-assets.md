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
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: da7ac8f323f9b2ec6303fbc85aa570373a538bcf
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hur du visar relaterade datatillgångar i Azure Data Catalog?
Azure Data Catalog kan du visa datatillgångar som rör en valda data tillgången och visa relationer mellan dem. 

## <a name="supported-data-sources"></a>Datakällor som stöds 
När du registrerar datatillgångar från följande datakällor registrerar Azure Data Catalog automatiskt metadata om kopplingsrelationer mellan de valda datatillgångarna. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Du måste registrera båda resurserna för Data Catalog import av relationen mellan två datatillgångar på samma gång. Om du har lagt till en av dem separat, lägger du till den igen och andra datatillgång import av relationen mellan dem.

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