---
title: Tjänsten för mätningsmappning för kostnadsfria Azure-konto | Microsoft Docs
description: Förstå tjänsten för mätningsmappning för tjänster som ingår kostnadsfritt konto.
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
ms.author: banders
ms.openlocfilehash: 2468f61c187d9b10ed9fe55ccf76e5d2561d0505
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370738"
---
# <a name="understand-free-service-to-meter-mapping"></a>Förstå kostnadsfria tjänsten för mätningsmappning

Varje Azure-tjänst genererar användning mot mätare som Azure faktureringssystem använder debiterar användare för tjänster. För att bättre förstå användningen av kostnadsfria tjänster, låt oss titta på tjänsten för mätningsmappning för dessa tjänster. Läs hur du skapar kostnadsfria tjänster i [skapa kostnadsfria tjänster med kostnadsfria Azure-konto](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Tjänsten för att mäta mappning kostnadsfritt konto berättigade tjänster 

|    Tjänst   | Mätarnamn på Azure portal | Mätarnamn i filen/API-användning | Mätar-ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S virtuell Linux-dator | Beräkningstimmar: Standard_B1 VM | Beräkningstimmar – kostnadsfri | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | Compute Hours - Standard_B1 VM (Windows) | Beräkningstimmar – kostnadsfri | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - offentliga IP-adresser  | IP-Adresstimmar – offentliga IP-adresser | IP-Adresstimmar – kostnadsfria | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Lagring (GB) – Cosmos DB | Lagring (GB) – kostnadsfri | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 enheter för programbegäran (timmar) - Cosmos DB | 100 enheter för programbegäran (timmar) – kostnadsfri | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standard IO - filer (GB) – lokalt Redundant | Standard IO - filer (GB) – kostnadsfritt | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard IO - Fil läs åtgärdsenheter (i 10 000-tal) | Standard I/O – enheter för filläsningsåtgärder (i 10 000-tal) – kostnadsfri | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard IO - Fil skriv åtgärdsenheter (i 10 000-tal) | Standard IO - enheter för Filskrivningsåtgärder (i 10 000-tal) – kostnadsfritt | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard IO - Fil protokoll åtgärdsenheter (i 10 000-tal) | Standard IO - fil protokoll Åtgärdsenheter (i 10 000-tal) – kostnadsfritt | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard IO - Fil lista åtgärdsenheter (i 10 000-tal) | Standard IO - fil lista Åtgärdsenheter (i 10 000-tal) – kostnadsfritt | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Frekvent Blockblob-lagring | Standard I/O – frekvent Blockblob Läs-och skrivåtgärder (i 10 000-tal) | Standard I/O – frekvent Blockblob Läs-och skrivåtgärder (i 10 000-tal) – kostnadsfri |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Frekvent Blockblob-lagring | Standard I/O – frekvent Blockblob (GB) – lokalt Redundant | Standard I/O – frekvent Blockblob (GB) – kostnadsfri | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Frekvent Blockblob-lagring | Standard I/O – skrivåtgärder för frekvent Blockblob (i 10 000-tal) | Standard IO - frekventa skrivåtgärder Blockblob (i 10 000-tal) – kostnadsfritt | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Frekvent Blockblob-lagring  | Standard I/O – frekvent Blockblob Skriv/Liståtgärder på Blockblob (i 10 000-tal) | Standard IO - varm Blockblob Skriv/Liståtgärder på Blockblob (i 10 000-tal) – kostnadsfritt | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Hanterad Disk *  | Standard – hanterad Disk/ögonblicksbilder (GB) – lokalt Redundant | Standard – hanterad Disk/ögonblicksbilder (GB) – kostnadsfri | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Hanterad Disk *  | Hanterade standarddiskar åtgärder (i 10 000-tal) | Hanterade standarddiskar åtgärder (i 10 000-tal) – kostnadsfri | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Hanterad Disk *  | Premium-lagring – Sidblob/P6 (enheter) – lokalt Redundant | Premium-lagring – Sidblob/P6 (enheter) – kostnadsfri | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standard S0 Databasdagar – SQL-databas | Standard S0 Databasdagar – kostnadsfri | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Delad – bandbredd ** | Dataöverföring ut (GB) | Utgående dataöverföring (GB) – kostnadsfri | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Om du skapar en virtuell Windows-dator och välj hanterad disk, förbrukar mätaren för hanterad disk som en del av den virtuella datorn.

\** Delade taxor kan användas via flera tjänster. Till exempel sända både virtuella datorer och lagringsutrymmen användning mot Data Transfer Out(GB) mätaren.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
