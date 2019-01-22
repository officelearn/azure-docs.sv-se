---
title: Azure SQL Data Warehouse viktig December 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 074276e4550b9b2e347e5cd30c597a1d09f6cb2f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440255"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Vad är nytt i Azure SQL Data Warehouse-version - 10.0.10106.0?
Azure SQL Data Warehouse (SQL DW) förbättrar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i SQL DW version 10.0.10106.0.

## <a name="query-restartability---ctas-and-insertselect"></a>Fråga Restartability - CTAS och infoga/välja
I sällsynta fall (det vill säga återkommande problem med nätverksanslutning, nodfel) frågor kan köras i Azure SQL DW misslyckas. Kör längre instruktioner, till exempel [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) och infoga-Välj operations exponeras mer för det här potentiella problemet. Med den här versionen implementerar Azure SQL DW logik för omprövning för CTAS och INSERT SELECT-instruktioner (utöver SELECT-instruktioner som tidigare meddelats), vilket gör att systemet för att hantera dessa tillfälliga problem transparent och hindra frågor misslyckas. Antal nya försök och listan över tillfälliga fel hanteras är system som har konfigurerats.

## <a name="return-order-by-optimization"></a>Returnera Order By-optimering
VÄLJ... ORDER BY-frågor får en prestandaökning i den här versionen.  Motorn för körning av fråga skulle tidigare, ordna resultaten på varje beräkningsnod och strömma dem till control-noden som skulle sedan Sammanfoga resultatet. Med den här förbättringen compute alla noder i stället skicka resultaten till en enda beräkningsnod som sedan slår ihop dem och returnerar sorterade resultat för användaren via Beräkningsnoden.  Det ger betydande prestandafördelar när frågeresultatet innehåller ett stort antal rader.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Förbättringar för flytt av data för PartitionMove och BroadcastMove
I Azure SQL Data Warehouse Gen2 data movement steg av typen ShuffleMove utnyttja omedelbar data movement metoder som beskrivs i den [prestanda förbättringar här blogg](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  Med den här versionen drivs förflyttning datatyper PartitionMove och BroadcastMove nu också av samma rörelsetekniker för omedelbar data.  Användarfrågor som använder dessa typer av data movement steg visas en prestandaökning.  Några ändringar i koden krävs för att dra nytta av dessa prestandavinster.

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du är nybörjare på Azure kan du upptäcka det [Azure-ordlistan] [ Azure glossary] till hjälp när du lär dig hur ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
