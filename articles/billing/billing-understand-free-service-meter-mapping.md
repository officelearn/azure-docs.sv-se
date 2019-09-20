---
title: Tjänst-till-mätar-mappning för kostnadsfritt Azure-konto
description: Förstå tjänst-till-mätar-mappning för tjänster som ingår i det kostnadsfria kontot.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8022c065d73aafc53d3dcb77e79c3e6320e0ce39
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490411"
---
# <a name="understand-free-service-to-meter-mapping"></a>Förstå kostnadsfri tjänst-till-mätar-mappning

Varje Azure-tjänst skickar användning mot mätare, vilket faktureringssystemet i Azure använder för att debitera användare för tjänster. För att bättre förstå användning av kostnadsfria tjänster ska vi titta på tjänst-till-mätar-mappningen för dessa tjänster. Information om hur du skapar kostnadsfria tjänster finns i [Skapa kostnadsfria tjänster med kostnadsfritt Azure-konto](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Tjänst-till-mätar-mappning för berättigade tjänster

|    Tjänst   | Mätarnamn i Azure-portalen | Mätarnamn i användningsfil/API | Mätar-ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux-VM | Beräkningstimmar – Standard_B1-VM | Beräkningstimmar – Kostnadsfritt | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows-VM | Beräkningstimmar – Standard_B1-VM (Windows) | Beräkningstimmar – Kostnadsfritt | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S-VM – Offentliga IP-adresser  | IP-adresstimmar – Offentliga IP-adresser | IP-adresstimmar – Kostnadsfritt | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Lagring (GB) – Cosmos DB | Lagring (GB) – Kostnadsfritt | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 enheter för programbegäran (timmar) – Cosmos DB | 100 enheter för programbegäran (timmar) – Kostnadsfritt | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| File Storage | Standard-I/O – Filer (GB) – Lokalt redundant | Standard-I/O – Filer (GB) – Kostnadsfritt | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard-I/O – Enheter för filläsningsåtgärder (i 10 000-tal) | Standard-I/O – Enheter för filläsningsåtgärder (i 10 000-tal) – Kostnadsfritt | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard-I/O – Enheter för filskrivningsåtgärder (i 10 000-tal) | Standard-I/O – Enheter för filskrivningsåtgärder (i 10 000-tal) – Kostnadsfritt | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard-I/O – Enheter för filprotokollåtgärder (i 10 000-tal) | Standard-I/O – Enheter för filprotokollåtgärder (i 10 000-tal) – Kostnadsfritt | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard-I/O – Enheter för filliståtgärder (i 10 000-tal) | Standard-I/O – Enheter för filliståtgärder (i 10 000-tal) – Kostnadsfritt | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Blockblob för frekvent åtkomst | Standard-I/O – Åtkomståtgärder för frekvent blockblob (i 10 000-tal) | Standard-I/O – Åtkomståtgärder för frekvent blockblob (i 10 000-tal) – Kostnadsfritt |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Blockblob för frekvent åtkomst | Standard-I/O – Blockblob för frekvent åtkomst – Lokalt redundant | Standard-I/O – Blockblob för frekvent åtkomst – Kostnadsfritt | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Blockblob för frekvent åtkomst | Standard I/O – Skrivåtgärder för frekvent blockblob (i 10 000-tal) | Standard I/O – Skrivåtgärder för frekvent blockblob (i 10 000-tal) – Kostnadsfritt | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Blockblob för frekvent åtkomst  | Standard-I/O – Skriv-/liståtgärder för frekvent blockblob (i 10 000-tal) | Standard-I/O – Skriv-/liståtgärder för frekvent blockblob (i 10 000-tal) – Kostnadsfritt | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Hanterad disk <sup>1</sup>  | Standard – hanterad disk/ögonblicksbilder (GB) – Lokalt redundant | Standard – hanterad disk/ögonblicksbilder (GB) – Kostnadsfritt | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Hanterad disk <sup>1</sup>  | Standard – åtgärder för hanterad disk (i 10 000-tal) | Standard – åtgärder för hanterad disk (i 10 000-tal) – Kostnadsfritt | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Hanterad disk <sup>1</sup>  | Premium Storage – sidblob/P6 (enheter) – Lokalt redundant | Premium Storage – sidblob/P6 (enheter) – Kostnadsfritt | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standard S0 databasdagar – SQL Database | Standard S0 databasdagar – Kostnadsfritt | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Delad – Bandbredd <sup>2</sup> | Utgående dataöverföring (GB) | Utgående dataöverföring (GB) – Kostnadsfritt | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> Om du skapar en virtuell Windows-dator och väljer hanterad disk kommer du att förbruka mätare för hanterad disk som en del av den virtuella datorn.

<sup>2</sup> Delade mätare kan förbrukas via flera tjänster. Till exempel skickar både virtuella datorer och Storage användnings mot mätare för utgående dataöverföring (GB).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- [Uppgradera din prenumeration](billing-upgrade-azure-subscription.md)
