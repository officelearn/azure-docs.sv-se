---
title: Reservationer kostnader för programvara för Azure | Microsoft Docs
description: Lär dig vilka programvara taxor ingår inte i kostnader för Azure reserverade VM-instanser.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 340cba65a1faac247678cd187f106157ba566f3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371180"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Kostnader för programvara som inte ingår i Azure Reserved VM Instances

Om du inte har en Azure Hybrid-förmånen på dina reserverade VM-instanser, är du debiteras för programvara som anges i följande avsnitt.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows software mätare som inte ingår i kostnaden för reservation

| Mätar-ID | MeterName i användningsfil | Används av en virtuell dator |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reserverade Windows Server-instanser Burst (1 kärna) | B-serien |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reserverade Windows Server-instanser Burst (2 kärnor) | B-serien |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reserverade Windows Server-instanser Burst (4 kärnor) | B-serien |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reserverade Windows Server-instanser Burst (8 kärnor) | B-serien |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reserverade Windows Server-instanser (1 kärna) | Alla utom B-serien |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reserverade Windows Server-instanser (2 kärnor) | Alla utom B-serien |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reserverade Windows Server-instanser (4 kärnor) | Alla utom B-serien |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reserverade Windows Server-instanser (6 kärnor) | Alla utom B-serien |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reserverade Windows Server-instanser (8 kärnor) | Alla utom B-serien |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reserverade Windows Server-instanser (12 kärnor) | Alla utom B-serien |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reserverade Windows Server-instanser (16 kärnor) | Alla utom B-serien |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reserverade Windows Server-instanser (20 kärnor) | Alla utom B-serien |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reserverade Windows Server-instanser (24 kärnor) | Alla utom B-serien |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reserverade Windows Server-instanser (32 kärnor) | Alla utom B-serien |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reserverade Windows Server-instanser (40 kärnor) | Alla utom B-serien |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reserverade Windows Server-instanser (64 kärnor) | Alla utom B-serien |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reserverade Windows Server-instanser (72 kärnor) | Alla utom B-serien |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reserverade Windows Server-instanser (128 kärnor) | Alla utom B-serien |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reserverade Windows Server-instanser (256 kärnor) | Alla utom B-serien |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reserverade Windows Server-instanser (96 kärnor) | Alla utom B-serien |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Programvara-taxor som inte ingår i reservationskostnad services cloud

| Mätar-ID | MeterName i användningsfil |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU License|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU License|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU License|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU License|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU License|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU License|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU License|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU License|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU License|

## <a name="rates-for-azure-meters"></a>Priser för Azure

Du kan få kostnaden för var och en av mätarnas via Azure RateCard-API. Information om hur du hämtar priserna för en mätare som azure finns i [få information om priset och metadata för resurser som används i en Azure-prenumeration](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Nästa steg
Mer information om reservationer för Azure finns i följande artiklar:

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reservationer för Azure](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
