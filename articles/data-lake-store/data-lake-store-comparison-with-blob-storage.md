---
title: Azure Data Lake Storage Gen1 jämförelse med Azure Storage Blob | Microsoft-dokument
description: Azure Data Lake Storage Gen1 jämförelse med Azure Storage Blob
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: b199525b-84de-4f79-9eb6-69a613b8b217
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 7c958c3ed4d6ddaabd87f053005fcfc1eba8c842
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438726"
---
# <a name="comparing-azure-data-lake-storage-gen1-and-azure-blob-storage"></a>Jämföra Azure Data Lake Storage Gen1 och Azure Blob Storage

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)] 

Tabellen i den här artikeln sammanfattar skillnaderna mellan Azure Data Lake Storage Gen1 och Azure Blob Storage längs några viktiga aspekter av stordatabearbetning. Azure Blob Storage är ett allmänt, skalbart objektarkiv som är utformat för en mängd olika lagringsscenarier. Azure Data Lake Storage Gen1 är en lagringsplats i hyperskala som är optimerad för arbetsbelastningar för stordataanalys.

|  | Azure Data Lake Storage Gen1 | Azure Blob Storage |
| --- | --- | --- |
| Syfte |Optimerad lagring för arbetsbelastningar för stordataanalys |Objektarkiv för allmänt ändamål för en mängd olika lagringsscenarier, inklusive stordataanalys |
| Användningsfall |Batch-, interaktiva, strömmande analys- och maskininlärningsdata som loggfiler, IoT-data, klickströmmar, stora datauppsättningar |Alla typer av text eller binära data, till exempel programbakslut, säkerhetskopieringsdata, medielagring för direktuppspelning och data för allmänna ändamål. Dessutom fullt stöd för analysarbetsbelastningar; batch-, interaktiva, strömmande analys- och maskininlärningsdata som loggfiler, IoT-data, klickströmmar, stora datauppsättningar |
| Viktiga begrepp |Data Lake Storage Gen1-kontot innehåller mappar, som i sin tur innehåller data som lagras som filer |Lagringskonto har behållare, som i sin tur har data i form av blobbar |
| Struktur |Hierarkiskt filsystem |Objektbutik med platt namnområde |
| API |REST API över HTTPS |REST API över HTTP/HTTPS |
| API på serversidan |[WebHDFS-kompatibelt REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) |[Azure Blob Storage REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx) |
| Hadoop-filsystemklient |Ja |Ja |
| Dataåtgärder - Autentisering |Baserat på [Azure Active Directory-identiteter](../active-directory/develop/authentication-scenarios.md) |Baserat på delade hemligheter - [Kontoåtkomstnycklar](../storage/common/storage-account-keys-manage.md) och [signaturenycklar för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md). |
| Dataåtgärder - autentiseringsprotokoll |OAuth 2.0. Anrop måste innehålla en giltig JWT (JSON Web Token) som utfärdats av Azure Active Directory |Hash-baserad meddelandeautentiseringskod (HMAC) . Anrop måste innehålla en Base64-kodad SHA-256-hash över en del av HTTP-begäran. |
| Dataoperationer - Auktorisering |KASSA-åtkomstkontrollistor (ACL).  ACL:er som baseras på Azure Active Directory-identiteter kan anges på fil- och mappnivå. |För auktorisering på kontonivå – Använd [kontoåtkomstnycklar](../storage/common/storage-account-keys-manage.md)<br>För konto-, behållar- eller blob-auktorisering – Använd [signaturenycklar för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) |
| Dataoperationer - Granskning |Tillgängliga. Se [här](data-lake-store-diagnostic-logs.md) för information. |Tillgängligt |
| Krypteringsdata i vila |<ul><li>Transparent, Serversida</li> <ul><li>Med tjänsthanterade nycklar</li><li>Med kundhanterade nycklar i Azure KeyVault</li></ul></ul> |<ul><li>Transparent, Serversida</li> <ul><li>Med tjänsthanterade nycklar</li><li>Med kundhanterade nycklar i Azure KeyVault (förhandsversion)</li></ul><li>Kryptering av klientsidan</li></ul> |
| Hanteringsåtgärder (t.ex. skapa konton) |[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) som tillhandahålls av Azure för kontohantering |[Rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC) som tillhandahålls av Azure för kontohantering |
| Utvecklare SDK |.NET, Java, Python, Node.js |.Net, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS |
| Prestanda för analysarbetsbelastning |Optimerad prestanda för parallella analysarbetsbelastningar. Högt dataflöde och IOPS. |Optimerad prestanda för parallella analysarbetsbelastningar. |
| Storleksbegränsningar |Inga gränser för kontostorlekar, filstorlekar eller antal filer |Specifika begränsningar finns i [Skalbarhetsmål för standardlagringskonton](../storage/common/scalability-targets-standard-account.md) och [Skalbarhets- och prestandamål för Blob-lagring](../storage/blobs/scalability-targets.md). Större kontogränser tillgängliga genom att kontakta [Azure Support](https://azure.microsoft.com/support/faq/) |
| Geo-redundans |Lokalt redundant (flera kopior av data i en Azure-region) |Lokalt redundant (LRS), zonundant (ZRS), globalt redundant (GRS), läsåtkomst globalt redundant (RA-GRS). Se [här](../storage/common/storage-redundancy.md) för mer information |
| Tjänsttillstånd |Allmänt tillgänglig |Allmänt tillgänglig |
| Regional tillgänglighet |Se [här](https://azure.microsoft.com/regions/#services) |Tillgängligt i alla Azure-regioner |
| Pris |Se [Priser](https://azure.microsoft.com/pricing/details/data-lake-store/) |Se [Priser](https://azure.microsoft.com/pricing/details/storage/) |


