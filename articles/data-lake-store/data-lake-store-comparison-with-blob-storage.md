---
title: Azure Data Lake Store jämförelse med Azure Storage Blob | Microsoft Docs
description: Azure Data Lake Store jämförelse med Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: d394c856b2f27446ab28c44fe4fed2dfd59ae62f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Jämförelse mellan Azure Data Lake Store och Azure Blob Storage
Tabellen nedan sammanfattar skillnaderna mellan Azure Data Lake Store och Azure Blob Storage längs vissa viktiga aspekter av stora databearbetning. Azure Blob Storage är en generell, lager skalbara objekt som är avsedd för en mängd olika scenarier för lagring. Azure Data Lake Store är en storskalig lagringsplats som är optimerad för arbetsbelastningar för analyser av stordata.

|  | Azure Data Lake Store | Azure Blob Storage |
| --- | --- | --- |
| Syfte |Optimerad lagring för arbetsbelastningar av stordata analytics |Generella objektet store för en mängd olika scenarier för lagring, inklusive stordata |
| Användningsfall |Batch, interaktiva, streaming analytics och data för machine learning som loggfiler, IoT data, klickar du på strömmar, stora datauppsättningar |Alla typer av text eller binär data, till exempel program tillbaka slutet, säkerhetskopierade data, Medialagring för strömning och allmänna ändamål data. Dessutom fullständigt stöd för analytics arbetsbelastningar; batch, interaktiva, streaming analytics och data för machine learning som loggfiler, IoT data, klickar du på strömmar, stora datauppsättningar |
| Viktiga begrepp |Data Lake Store-konto innehåller mappar, som i sin tur innehåller data som lagras som filer |Lagringskontot har behållare, som i sin tur har data i form av blobbar |
| struktur |Hierarkiskt filsystem |Objektet store med platt namnområde |
| API |REST API via HTTPS |REST API via HTTP/HTTPS |
| API för serversidan |[WebHDFS-kompatibelt REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST-API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop-filen System klienten |Ja |Ja |
| Dataåtgärder - autentisering |Baserat på [identiteter med Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) |Baserat på delade hemligheter - [Åtkomstnycklarna konto](../storage/common/storage-create-storage-account.md#manage-your-storage-account) och [delade snabbtangenter signatur](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Dataåtgärder - autentiseringsprotokoll |OAuth 2.0. Anrop måste innehålla en giltig JWT som (JSON Web Token) utfärdat av Azure Active Directory |Hashbaserad meddelandeautentiseringskod (HMAC). Anrop måste innehålla en Base64-kodad SHA-256-hash över en del av HTTP-begäran. |
| Dataåtgärder - auktorisering |POSIX åtkomstkontrollistor (ACL).  ACL: er baserat på Azure Active Directory identiteter kan ställas in på fil- och nivå. |Kontonivå auktorisering – Använd [Åtkomstnycklarna för konto](../storage/common/storage-create-storage-account.md#manage-your-storage-account)<br>Använd för konto, behållare eller auktorisering av blob - [delade signatur snabbtangenter](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Dataåtgärder - granskning |Tillgänglig. Se [här](data-lake-store-diagnostic-logs.md) information. |Tillgänglig |
| Krypteringsdata i vila |<ul><li>Transparent, på serversidan</li> <ul><li>Med service-hanterade nycklar</li><li>Med kundhanterad nycklar i Azure KeyVault</li></ul></ul> |<ul><li>Transparent, på serversidan</li> <ul><li>Med service-hanterade nycklar</li><li>Med kundhanterad nycklar i Azure KeyVault (förhandsgranskning)</li></ul><li>Kryptering av klientsidan</li></ul> |
| Hanteringsåtgärder (t.ex. Skapa konto) |[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) tillhandahålls av Azure för kontohantering |[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) tillhandahålls av Azure för kontohantering |
| SDK: er för utvecklare |.NET, Java, Python, Node.js |.NET, Java, Python, Node.js, C++, Ruby, PHP, gå, Android, iOS |
| Analytics Arbetsbelastningsprestanda |Optimal prestanda för parallell analytics arbetsbelastningar. Högt genomflöde och IOPS. |Optimal prestanda för parallell analytics arbetsbelastningar. |
| Storleksgränser |Inga gränser för kontostorlekar, filstorlekar eller många filer |Gränser dokumenterade [här](../storage/common/storage-scalability-targets.md). Större konto begränsar tillgänglig genom att kontakta [Azure-Support](https://azure.microsoft.com/support/faq/) |
| GEO-redundans |Lokalt redundant (flera kopior av data i en Azure-region) |Lokalt redundant (LRS), zonen redundant (ZRS), globalt redundant (GRS), läsbehörighet är globalt redundant (RA-GRS). Se [här](../storage/common/storage-redundancy.md) mer information |
| Tjänsttillstånd |Allmänt tillgänglig |Allmänt tillgänglig |
| Regional tillgänglighet |Se [här](https://azure.microsoft.com/regions/#services) |Tillgänglig i alla Azure-regioner |
| Pris |Se [priser](https://azure.microsoft.com/pricing/details/data-lake-store/) |Se [priser](https://azure.microsoft.com/pricing/details/storage/) |


