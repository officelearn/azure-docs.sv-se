---
title: Använd Azure Data Factory för att migrera data från data Lake och Data Warehouse till Azure | Microsoft Docs
description: Använd Azure Data Factory för att migrera data från data Lake och data lagret till Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 937a076b3e0e3c5170779d3449776f0aa1cf5b49
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259007"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Använd Azure Data Factory för att migrera data från data Lake eller data lager till Azure 

Azure Data Factory kan vara verktyget för datamigrering när du vill migrera data Lake eller Enterprise Data Warehouse (ANALYSLÖSNINGAR) till Azure. Migreringen av data Lake och Data Warehouse är relaterad till följande scenarier: 

- Stor migrering av data arbets belastning från AWS S3, lokal Hadoop-filsystem till Azure. 
- ANALYSLÖSNINGAR-migrering från Oracle Exadata, Netezza, Teradata, AWS RedShift till Azure. 

Azure Data Factory kan flytta PBs för data för data Lake migration och flera på flera TB-data för migrering av data lager. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Varför Azure Data Factory kan användas för datamigrering 

- Azure Data Factory kan enkelt skala upp mängden häst krafter för att flytta data på ett effektivt sätt med hög prestanda, återhämtning och skalbarhet och bara betala för det du använder.  
  - Azure Data Factory har ingen begränsning för data volym och antal filer.
  - Azure Data Factory kan 100% använda din nätverks-och lagrings bandbredd för att uppnå det högsta data flödet i din miljö.   
  - Azure Data Factory följer strategin för att betala per användning, så att du bara behöver betala för tiden när du använder Azure Data Factory för att migrera data till Azure.  
- Azure Data Factory har möjlighet att utföra historisk historiska inläsningar och schemalagda stegvisa inläsningar. 
- Azure Data Factory använder Azure IR för att flytta data mellan offentligt tillgängliga data Lake/Warehouse-slutpunkter, eller alternativt använda IR med egen värd för att flytta data för data Lake/Warehouse-slutpunkter i VNet eller bakom brand vägg. 
- Azure Data Factory har säkerhet i företags klass: Använd antingen MSI eller tjänst identitet för skyddad tjänst-till-tjänst-integrering eller Använd Azure Key Vault för hantering av autentiseringsuppgifter. 
- Azure Data Factory ger en kostnads fri redigerings upplevelse och en inbyggd instrument panel för övervakning.  

## <a name="online-vs-offline-data-migration"></a>Online vs. offline-datamigrering

Azure Data Factory är ett typiskt verktyg för datamigrering online för att överföra data över nätverket (Internet, ER eller VPN) där datamigrering offline gör att personer fysiskt levererar data överförings enheter från din organisation till Azure Data Center.  

Det finns tre viktiga överväganden när du väljer metod för online-eller offline-migrering:  

- Storlek på data som ska migreras. 
- Nätverks bandbredd. 
- Migrations fönster.   

Om du vill slutföra datamigreringen inom två veckor (migreringstabellen) kan du se en klipp ut rad i bilden nedan för att visa när det är bra att använda Migreringsverktyg för online (Azure Data Factory) med annan data storlek och nätverks bandbredd.   

![Online vs. offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> Fördelen med att migrera online är att du kan uppnå både historiska data inläsningar och stegvisa flöden från slut punkt till slut punkt med ett verktyg.  Genom att göra det kan data hållas synkroniserade mellan befintliga och nya butiker under hela migreringstabellen så att du kan återskapa din ETL-logik på den nya butiken med uppdaterade data. 


## <a name="next-steps"></a>Nästa steg

- [Migrera data från AWS S3 till Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrera data från ett lokalt Hadoop-kluster till Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrera data från den lokala Netezza-servern till Azure](data-migration-guidance-netezza-azure-sqldw.md)
