---
title: Azure SQL Data Warehouse viktig augusti 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 1f3b17f3163c29f9b9e1e47e14ccdbc1e37e1010
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39635585"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Augusti 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i augusti 2018.

## <a name="automatic-intelligent-insights"></a>Automatisk smarta insikter
Microsoft har infört [automatisk intelligent insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) att leverera moln automatisering för ditt informationslager. Du behöver inte längre att övervaka ditt informationslager för data skeva och icke-optimal tabellstatistik. SQL Data Warehouse hämtar smarta insikter för alla instanser av Gen2 utan extra kostnad. Genom att integrera med [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), får du automatiskt rekommendationer om bästa praxis att förbättra prestandan för din aktiva arbetsbelastningar. SQL Data Warehouse analyserar din arbetsbelastning och hämtar rekommendationer baserat på din användning. Den här analysen sker varje dag så att du kan övervaka användningsrapporter och rekommendationer för förbättringar för din arbetsbelastning.

Du kan visa rekommendationer i Azure Advisor-portalen: ![Azure Advisor Portal rekommendationer för Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Du kan öka detaljnivån i varje kategori om du vill se rekommendationer för den specifika aviseringen: ![rekommendationsdetaljer för Azure Advisor-portalen för Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

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