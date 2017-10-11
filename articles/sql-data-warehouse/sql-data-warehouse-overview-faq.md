---
title: "Vanliga frågor och svar om Azure SQL Data Warehouse | Microsoft Docs"
description: "Den här artikeln innehåller ut vanliga frågor och svar om Azure SQL Data Warehouse från kunder och utvecklare"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 3/1/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 4c00710ecc0c91f8407eca81b78176075fcbd6ad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse vanliga frågor och svar

## <a name="general"></a>Allmänt

FRÅGOR. Vad erbjuder SQL DW för datasäkerhet?

A. SQL DW erbjuder flera lösningar för att skydda data, till exempel TDE och granskning. Mer information finns i [säkerhet].

FRÅGOR. Var hittar jag reda på vilka juridiska eller affärsmässiga standarder SQL DW som är kompatibla med?

A. Besök den [Microsoft Compliance] sidan för olika erbjudanden för efterlevnad av produkten som SOC och ISO. Först välja genom efterlevnad titel och sedan expandera Azure i Microsoft i omfånget services avsnittet moln till höger på sidan för att se vilka tjänster som är Azure services är kompatibla.

FRÅGOR. Kan jag ansluta PowerBI?

A. Visst! Om PowerBI stöder direkt fråga med SQL DW, är den inte avsedd för stort antal användare eller realtidsdata. För produktion av PowerBI bör du använda PowerBI utöver Azure Analysis Services och Analysis Service IaaS. 

FRÅGOR. Vad är SQL Data Warehouse kapacitetsbegränsningar?

A. Se vår aktuella [kapacitetsbegränsningar] sidan. 

FRÅGOR. Varför är min skala / / pausa tar så lång tid?

A. En mängd olika faktorer kan påverka tiden för beräkning hanteringsåtgärder. En gemensam fall för långvariga åtgärder är transaktionell återställning. När en åtgärd för skala eller pausa initieras, blockeras alla inkommande sessioner och frågor är tar slut. För att lämna systemet i ett stabilt tillstånd föras transaktioner tillbaka innan en åtgärd kan påbörjas. Det största antalet och större loggstorleken av transaktioner, desto längre tid åtgärden kommer har stannat återställa systemet till ett stabilt tillstånd.

## <a name="user-support"></a>Stöd för användare

FRÅGOR. Jag har en i funktionsbegäran var skickar jag det?

A. Om du har en funktionsbegäran kan du skicka den på vår [UserVoice] sidan

FRÅGOR. Hur gör jag x?

A. Hjälp med att utveckla med SQL Data Warehouse, du kan ställa frågor på vår [Stack Overflow] sidan. 

FRÅGOR. Hur skickar jag ett supportärende

A. [Supportärenden] kan registreras via Azure portal.

## <a name="sql-languagefeature-support"></a>Stöd för SQL-språk-funktionen 

FRÅGOR. Vilka datatyper stöder SQL Data Warehouse?

A. Se SQL Data Warehouse [datatyper].

FRÅGOR. Vilka table-funktioner stöder?

A. Medan SQL Data Warehouse stöder många funktioner, vissa stöds inte och dokumenteras i [funktioner som inte stöds tabellen].

## <a name="tooling-and-administration"></a>Verktygsuppsättning och administration

FRÅGOR. Stöder du databasprojekt i Visual Studio.

A. Vi stöder för närvarande inte databasprojekt i Visual Studio för SQL Data Warehouse. Om du vill omvandla en röst att hämta den här funktionen kan du besöka vårt User Voice [Database projects funktion begäran].

FRÅGOR. Stöder SQL Data Warehouse REST API: er?

A. Ja. De flesta REST-funktioner som kan användas med SQL-databas är också tillgänglig i SQL Data Warehouse. Du API information i övriga sidor med dokumentation eller [MSDN].


## <a name="loading"></a>Läser in

FRÅGOR. Vilka klientdrivrutiner som stöder?

A. Stöd för drivrutiner för DW kan hittas på den [anslutningssträngar] sidan

F: vad filformat som stöds av PolyBase med SQL Data Warehouse?

S: Orc, RC, parkettgolv och flat avgränsad text

F: Vad kan ansluta till från SQL DW med PolyBase? 

S: [Azure Data Lake Store] och [Azure Storage-Blobbar]

F: går beräkning pushdown vid anslutning till Azure Storage-Blobbar eller ADLS? 

A: SQL DW PolyBase interagerar inte bara lagringskomponenter. 

F: kan jag ansluta till HDI?

S: HDI kan använda ADLS eller WASB som HDFS-lagret. Om du har antingen som HDFS-lager kan du läsa in data till SQL DW. Men kan inte du generera pushdown beräkning till HDI-instans. 

## <a name="next-steps"></a>Nästa steg
Mer information om SQL Data Warehouse som helhet finns i vår [översikt] sidan.


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[anslutningssträngar]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Supportärenden]: ./sql-data-warehouse-get-started-create-support-ticket.md
[säkerhet]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft Compliance]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[kapacitetsbegränsningar]: ./sql-data-warehouse-service-capacity-limits.md
[datatyper]: ./sql-data-warehouse-tables-data-types.md
[funktioner som inte stöds tabellen]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage-Blobbar]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Database projects funktion begäran]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[översikt]: ./sql-data-warehouse-overview-faq.md