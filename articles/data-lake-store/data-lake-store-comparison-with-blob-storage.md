---
title: Azure Data Lake Storage Gen1 jämförelse med Azure Storage Blob | Microsoft Docs
description: Azure Data Lake Storage Gen1 jämförelse med Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 3f37b996caa60a54c18388cb1f65873bf0aa4ed9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877702"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Jämför Azure Data Lake Storage Gen1 och Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Tabellen i den här artikeln sammanfattas skillnaderna mellan Azure Data Lake Storage Gen1 och Azure Blob Storage med viktiga aspekter av bearbetning av stordata. Azure Blob Storage är en allmän och skalbar objektlagring som är utformad för en mängd olika lagringsscenarier. Azure Data Lake Storage Gen1 är en lagringsplats i hyperskala som är optimerad för stordataanalyser.

|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Syfte |Optimerad lagring för stordataanalyser |Objektlagring för generell användning för en mängd olika lagringsscenarier, inklusive analys av stordata |
| Användningsfall |Batch, interaktiv, streaming analytics och machine learning som loggfiler för IoT-data, klickströmmar, stora datauppsättningar |Alla typer av text eller binära data, till exempel program tillbaka slutet, säkerhetskopierade data, Medialagring för direktuppspelning och allmänt syfte data. Dessutom fullständig support för analytiska arbetsbelastningar för; batch, interaktiv, streaming analytics och machine learning som loggfiler för IoT-data, klickströmmar, stora datauppsättningar |
| Viktiga begrepp |Data Lake Storage Gen1 konto innehåller mappar, som i sin tur innehåller data som lagras som filer |Lagringskontot har behållare, som i sin tur har data i form av BLOB-objekt |
| struktur |Faktiskt hierarkiskt filsystem |Objektlagring med ostrukturerad namnrymd |
| API |REST-API via HTTPS |REST-API via HTTP/HTTPS |
| API för serversidan |[WebHDFS-kompatibla REST-API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[REST API: et för Azure Blob Storage](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop-klienten för System |Ja |Ja |
| Dataåtgärder - autentisering |Baserat på [Azure Active Directory-identiteter](../active-directory/develop/authentication-scenarios.md) |Baserat på delade hemligheter - [Kontoåtkomstnycklar](../storage/common/storage-account-manage.md#access-keys) och [nycklar för signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Dataåtgärder - autentiseringsprotokoll |OAuth 2.0. Anrop måste innehålla en giltig JWT som (JSON Web Token) utfärdat av Azure Active Directory |Hashbaserad meddelandeautentiseringskod (HMAC). Anrop måste innehålla en Base64-kodad SHA-256-hash över en del av HTTP-begäran. |
| Dataåtgärder - auktorisering |POSIX åtkomstkontrollistor (ACL).  Åtkomstkontrollistor baserat på Azure Active Directory-identiteter kan ställas in på nivån filer och mappar. |Kontonivå auktorisering – använda [Kontoåtkomstnycklar](../storage/common/storage-account-manage.md#access-keys)<br>Använd för konto, behållare eller blob-auktorisering - [nycklar för delad åtkomst-signatur](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Dataåtgärder - granskning |Tillgängligt. Se [här](data-lake-store-diagnostic-logs.md) information. |Tillgängligt |
| Vilande krypteringsdata |<ul><li>Transparent, på serversidan</li> <ul><li>Med tjänsten-hanterade nycklar</li><li>Med Kundhanterade nycklar i Azure Key</li></ul></ul> |<ul><li>Transparent, på serversidan</li> <ul><li>Med tjänsten-hanterade nycklar</li><li>Med Kundhanterade nycklar i Azure Key (förhandsversion)</li></ul><li>Kryptering av klientsidan</li></ul> |
| Management-åtgärder (t.ex. Account Create) |[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) som tillhandahålls av Azure för kontohantering |[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) som tillhandahålls av Azure för kontohantering |
| SDK: er för utvecklare |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Analytics Arbetsbelastningens prestanda |Optimerad prestanda för arbetsbelastningar för parallella analyser. Högt dataflöde och IOPS. |Optimerad prestanda för arbetsbelastningar för parallella analyser. |
| Storleksbegränsningar |Inga gränser för kontostorlekar, filstorlekar eller antal filer |Specifika gränser dokumenteras [här](../storage/common/storage-scalability-targets.md). Större konto begränsar tillgängliga genom att kontakta [Support för Azure](https://azure.microsoft.com/support/faq/) |
| GEO-redundans |Lokalt redundant (flera kopior av data i en Azure-region) |Lokalt redundant (LRS), zone redundant (ZRS), globalt redundant (GRS) läsbehörighet är globalt redundant (RA-GRS). Se [här](../storage/common/storage-redundancy.md) för mer information |
| Tjänsttillstånd |Allmänt tillgänglig |Allmänt tillgänglig |
| Regional tillgänglighet |Se [här](https://azure.microsoft.com/regions/#services) |Tillgängliga i alla Azure-regioner |
| Pris |Se [priser](https://azure.microsoft.com/pricing/details/data-lake-store/) |Se [priser](https://azure.microsoft.com/pricing/details/storage/) |


