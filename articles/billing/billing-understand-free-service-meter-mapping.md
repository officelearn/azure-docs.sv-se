---
title: Tjänsten för mätaren mappning Azure gratiskonto | Microsoft Docs
description: Förstå service för mätaren mappning för tjänster som ingår i kostnadsfritt konto.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 3019fe5f8b44eaa32d4ab309f296d6b4574df1d3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061378"
---
# <a name="understand-free-service-to-meter-mapping"></a>Förstå kostnadsfri tjänst för mätaren-mappning

Varje Azure-tjänsten skickar användning mot mätare Azure faktureringssystem använder debiterar användare för tjänster. För att bättre förstå användningen av gratistjänster ska vi titta på tjänsten för mätaren mappning för dessa tjänster. Information om hur du skapar gratistjänster finns [skapa gratistjänster med kostnadsfritt Azure-konto](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Tjänsten ska avläsas mappning kostnadsfritt konto berättigade tjänster 

|    Tjänst   | Mätaren namn på Azure-portalen | Mätaren namn i fil-användning/API | Mätar-ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S virtuell Linux-dator | Beräkna timmar - Standard_B1 VM | Beräkningstimmar – kostnadsfri | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | Beräkna timmar - Standard_B1 VM (Windows) | Beräkningstimmar – kostnadsfri | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - offentliga IP-adresser  | IP-Adresstimmar - offentliga IP-adresser | IP-Adresstimmar – kostnadsfri | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Lagringsutrymme (GB) - Cosmos DB | Lagringsutrymme (GB) - ledigt | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 enheter för programbegäran (timmar) - Cosmos DB | 100 enheter för programbegäran (timmar) – kostnadsfri | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standard-i/o – filer (GB) - lokalt Redundant | Standard-i/o – filer (GB) – kostnadsfri | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard IO - Fil läs åtgärdsenheter (i 10 000-tal) | Standard-i/o - filläsningsåtgärder (i 10 000-tal) – kostnadsfri | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard IO - Fil skriv åtgärdsenheter (i 10 000-tal) | Standard-i/o - Filskrivningsåtgärder (i 10 000-tal) – kostnadsfri | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard IO - Fil protokoll åtgärdsenheter (i 10 000-tal) | Standard-i/o - Filprotokollåtgärder (i 10 000-tal) – kostnadsfri | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard IO - Fil lista åtgärdsenheter (i 10 000-tal) | Standard-i/o - Filliståtgärder (i 10 000-tal) – kostnadsfri | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Varm Blockblob Blob Storage | Standard-i/o – varm Blockblob läsåtgärder (i 10 000-tal) | Standard-i/o – varm Blockblob Läs-och skrivåtgärder (i 10 000-tal) – kostnadsfri |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Varm Blockblob Blob Storage | Standard-i/o – varm Blockblob (GB) - lokalt Redundant | Standard-i/o – varm Blockblob (GB) - ledigt | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Varm Blockblob Blob Storage | Standard-i/o – skrivåtgärder på varm Blockblob (i 10 000-tal) | Standard-i/o – varm skrivåtgärder på Blockblob (i 10 000-tal) - ledigt | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Varm Blockblob Blob Storage  | Standard-IO - Blockblob för frekvent Skriv-/ Liståtgärder åtkomst (i 10 000-tal) | Standard-IO - Blockblob för frekvent Skriv-/ Liståtgärder åtkomst (i 10 000-tal) – kostnadsfri | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Hanterade disken *  | Standard – hanterad Disk/ögonblicksbilder (GB) - lokalt Redundant | Standard – hanterad Disk/ögonblicksbilder (GB) - ledigt | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Hanterade disken *  | Åtgärder för standard hanterade diskar (i 10 000-tal) | Vanliga åtgärder för hanterade diskar (i 10 000-tal) – kostnadsfri | 82cc6ea4-0abd-43ac-ACC0-ec34edf0f14c
| Hanterade disken *  | Premium-lagring – Sidblob/P6 (enheter) – lokalt Redundant | Premium-lagring – Sidblob/P6 (enheter) – kostnadsfri | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standard S0 Databasdagar - SQL-databas | Standard S0 Databasdagar – kostnadsfri | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Delad - bandbredd ** | Dataöverföring ut (GB) | Dataöverföring ut (GB) – kostnadsfri | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Om du skapar en virtuell Windows-dator och välj hanterade diskar förbrukar hanterade diskar mätaren som en del av den virtuella datorn.

\** Delade mätare kan användas via flera tjänster. Till exempel genererar både virtuella datorer och lagring användning mot Data Transfer Out(GB) mätaren.





## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
