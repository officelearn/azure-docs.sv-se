---
title: Kostnader för Azure reservation Windows programvara | Microsoft Docs
description: Läs om vilken mätare för Windows-programvara ingår inte i kostnader för Azure reserverade VM-instanser.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: 8ff2d126fd491ddc40f6223b6d4ec1fc4505b8fb
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630119"
---
# <a name="windows-software-costs-not-included-with-azure-reservations"></a>Kostnader för Windows-programvara ingår inte i Azure-reservationer

Om du inte har en Azure Hybrid Use Benefit på dina reserverade VM-instanser kan sedan debiteras du för Windows-programvara som anges i följande avsnitt.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows software mätare som inte ingår i kostnaden för reservation

| Mätar-ID | MeterName i användningsfil | Används av en virtuell dator |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reserverade Windows Server-instanser Burst (1 kärna) | B-serien |
| cac255a2-9f0f-4c62-8BD6-f0fa449c5f76 | Reserverade Windows Server-instanser Burst (2 kärnor) | B-serien |
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

Du kan få kostnaden för var och en av mätarnas via Azure RateCard-API. Information om hur du hämtar priserna för en mätare som azure finns i [få information om priset och metadata för resurser som används i en Azure-prenumeration](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reservationer i Azure](billing-manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.



