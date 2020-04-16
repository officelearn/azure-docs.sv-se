---
title: Migrera data från datasjö och datalager till Azure
description: Använd Azure Data Factory för att migrera data från datasjö och datalager till Azure.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4408546c892299e5bbbc22b00a4b334c36eda616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416433"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Använda Azure Data Factory för att migrera data från datasjön eller datalagret till Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om du vill migrera datasjöen eller företagets informationslager (EDW) till Microsoft Azure kan du överväga att använda Azure Data Factory. Azure Data Factory är väl lämpat för följande scenarier:

- Migrering av big data-arbetsbelastning från Amazon Simple Storage Service (Amazon S3) eller ett lokalt Hadoop Distributed File System (HDFS) till Azure
- EDW-migrering från Oracle Exadata, Netezza, Teradata eller Amazon Redshift till Azure

Azure Data Factory kan flytta petabyte (PB) med data för datasjömigrering och tiotals terabyte (TB) data för migrering av datalager.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Varför Azure Data Factory kan användas för datamigrering

- Azure Data Factory kan enkelt skala upp mängden processorkraft för att flytta data på ett serverlöst sätt med hög prestanda, återhämtning och skalbarhet. Och du betalar bara för det du använder. Observera också följande: 
  - Azure Data Factory har inga begränsningar för datavolymen eller antalet filer.
  - Azure Data Factory kan använda nätverks- och lagringsbandbredden fullt ut för att uppnå den högsta mängden dataförflyttningsdataflöde i din miljö.
  - Azure Data Factory använder en användningsbaserad metod, så att du bara betalar för den tid du faktiskt använder för att köra datamigreringen till Azure.  
- Azure Data Factory kan utföra både en historisk belastning och schemalagda inkrementella belastningar.
- Azure Data Factory använder Azure integration runtime (IR) för att flytta data mellan allmänt tillgängliga datasjö och lagerslutpunkter. Den kan också använda självvärd iR för att flytta data för datasjö- och lagerslutpunkter i Azure Virtual Network (VNet) eller bakom en brandvägg.
- Azure Data Factory har säkerhet i företagsklass: Du kan använda Windows Installer (MSI) eller Service Identity för säker integrering mellan tjänst och tjänst eller använda Azure Key Vault för hantering av autentiseringsuppgifter.
- Azure Data Factory ger en kodfri redigeringsupplevelse och en omfattande, inbyggd övervakningsinstrumentpanel.  

## <a name="online-vs-offline-data-migration"></a>Migrering av online- och offlinedata

Azure Data Factory är ett standardverktyg för datamigrering online för överföring av data via ett nätverk (internet, ER eller VPN). Med offline-datamigrering skickar användare fysiskt dataöverföringsenheter från sin organisation till ett Azure Data Center.  

Det finns tre viktiga överväganden när du väljer mellan en online- och offlinemigreringsmetod:  

- Storleken på de data som ska migreras
- Nätverksbandbredd
- Fönstret Migrering

Anta till exempel att du planerar att använda Azure Data Factory för att slutföra datamigrering inom två veckor *(migreringsfönstret).* Lägg märke till den rosa/blå klipplinjen i följande tabell. Den lägsta rosa cellen för en viss kolumn visar den datastorlek/nätverksbandbredd som paras ihop vars migreringsfönster är närmast men mindre än två veckor. (Alla storlekar/bandbreddsparering i en blå cell har ett onlinemigreringsfönster på mer än två veckor.) 

![online kontra](media/data-migration-guidance-overview/online-offline.png) offline Den här tabellen hjälper dig att avgöra om du kan uppfylla ditt avsedda migreringsfönster via onlinemigrering (Azure Data Factory) baserat på storleken på dina data och din tillgängliga nätverksbandbredd. Om onlinemigreringsfönstret är mer än två veckor bör du använda offlinemigrering.

> [!NOTE]
> Genom att använda onlinemigrering kan du uppnå både historisk datainläsning och inkrementella flöden från slutpunkt till slutpunkt via ett enda verktyg.  Med den här metoden kan dina data synkroniseras mellan det befintliga arkivet och det nya arkivet under hela migreringsfönstret. Det innebär att du kan återskapa ETL-logiken i det nya arkivet med uppdaterade data.


## <a name="next-steps"></a>Nästa steg

- [Migrera data från AWS S3 till Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrera data från lokalt hadoop-kluster till Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrera data från lokal Netezza-server till Azure](data-migration-guidance-netezza-azure-sqldw.md)
