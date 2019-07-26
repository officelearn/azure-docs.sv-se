---
title: Bokning av program varu kostnader för Azure | Microsoft Docs
description: Lär dig vilka program varu mätare som inte ingår i kostnader för reserverade VM-instanser i Azure.
services: billing
documentationcenter: ''
author: yashar
manager: yashar
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 52e2e2503303c2a7525a3a6c156f648c097b27dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478613"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Program varu kostnader som inte ingår i Azure Reserved VM Instances

En reserverad instans och SQL reserverad kapacitets rabatt för virtuella datorer gäller endast för infrastruktur kostnaderna och inte för program kostnaderna. Om du använder en virtuell Windows-dator och inte har en Azure Hybrid-förmån på dina reserverade instanser av virtuella datorer debiteras du för de program varu mätare som anges i följande avsnitt. För SQL PaaS-distributioner fortsätter IP-kostnaden att debiteras med separat mätare om Azure Hybrid-förmån inte har valts.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows-programmätare som inte ingår i reservations kostnaden

| Mätar-ID | MeterName i användnings fil | Används av virtuell dator |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation – Windows Server instanser burst (1 kärna) | B-serien |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation – Windows Server instanser burst (2 kärnor) | B-serien |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation – Windows Server instanser burst (4 kärnor) | B-serien |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation – Windows Server instanser burst (8 kärnor) | B-serien |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation – Windows Server instanser (1 kärna) | Alla utom B-serien |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation – Windows Server instanser (2 kärnor) | Alla utom B-serien |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation – Windows Server instanser (4 kärnor) | Alla utom B-serien |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation – Windows Server instanser (6 kärnor) | Alla utom B-serien |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation – Windows Server instanser (8 kärnor) | Alla utom B-serien |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation – Windows Server instanser (12 kärnor) | Alla utom B-serien |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation – Windows Server instanser (16 kärnor) | Alla utom B-serien |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation – Windows Server instanser (20 kärnor) | Alla utom B-serien |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation – Windows Server instanser (24 kärnor) | Alla utom B-serien |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation – Windows Server instanser (32-kärna) | Alla utom B-serien |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation – Windows Server instanser (40-kärna) | Alla utom B-serien |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation – Windows Server instanser (64-kärna) | Alla utom B-serien |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation – Windows Server instanser (72-kärna) | Alla utom B-serien |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation – Windows Server instanser (128-kärna) | Alla utom B-serien |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation – Windows Server instanser (256-kärna) | Alla utom B-serien |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation – Windows Server instanser (96-kärna) | Alla utom B-serien |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Cloud Services-programmätare som inte ingår i reservations kostnaden

| Mätar-ID | MeterName i användnings fil |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU License|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU-licens|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU-licens|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU-licens|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU License|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU License|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU-licens|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU-licens|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU License|

## <a name="rates-for-azure-meters"></a>Priser för Azure-mätare

Du kan få kostnaden för var och en av dessa mätare via Azure RateCard-API: et. Information om hur du hämtar priserna för en Azure-mätare finns i [Hämta information om pris och metadata för resurser som används i en Azure-prenumeration](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Nästa steg
Mer information om reservationer för Azure finns i följande artiklar:

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reservationer för Azure](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservations rabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå reservations användningen för din prenumeration enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
- [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).
