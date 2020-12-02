---
title: Rabatt på programvaruplan – Azure | Microsoft Docs
description: Lär dig hur rabatter på programvaruplaner tillämpas på programvara på virtuella datorer.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: banders
ms.openlocfilehash: 2c17b2f9dc12fa7bff2dae8603bcf95a3a8ddfa0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350848"
---
# <a name="azure-software-plan-discount"></a>Rabatt på Azure-programvaruplan

Azure-programvaruplaner för SUSE och Red Hat är reservationer som tillämpas på distribuerade virtuella datorer. Rabatten för programvaruplan tillämpas på programvaruanvändningen för de distribuerade virtuella datorer som matchar reservationen.

När du stänger av en virtuell dator tillämpas rabatten automatiskt på en annan matchande virtuell dator om sådan finns. En programvaruplan omfattar kostnaden för att köra programvaran på en virtuell dator. Andra avgifter för till exempel beräkning, lagring och nätverk debiteras separat.

För att köpa rätt plan behöver du förstå din VM-användning och antalet vCPU:er på de virtuella datorerna. Använd följande avsnitt för att välja vilken plan du ska köpa, baserat på dina användningsdata.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Om du inte har några matchande resurser för en viss timme förlorar du därmed en reservationskvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du avslutar en resurs tillämpas reservationsrabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Granska Red Hat-VM-användning innan du köper

Hämta produktnamnet från dina användningsdata och köp Red Hat-planen med samma typ och storlek.

Om din användning till exempel har produkten **Red Hat Enterprise Linux – licens för virtuell dator med 1–4 vCPU:er** bör du köpa **Red Hat Enterprise Linux** för **virtuell dator med 1–4 vCPU:er**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Granska SUSE-VM-användning innan du köper

Hämta produktnamnet från dina användningsdata och köp SUSE-planen med samma typ och storlek.

Om din användning till exempel har produkten **SUSE Linux Enterprise Server Priority – stöd för virtuell dator med 2–4 vCPU:er** bör du köpa **SUSE Linux Enterprise Server Priority** för **virtuell dator med 2–4 vCPU:er**.

![Exempel på val av den produkt som ska köpas](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Rabatten gäller olika VM-storlekar för SUSE-planer

Liksom reserverade VM-instanser ger köp av SUSE-planer flexibel instansstorlek. Det innebär att rabatten gäller även när du distribuerar en virtuell dator med ett annat vCPU-antal. Rabatten gäller för olika VM-storlekar i programvaruplanen.

Rabattbeloppet beror på det förhållande som anges i följande tabeller. Förhållandet jämför det relativa fotavtrycket för varje mätare den i gruppen. Förhållandet beror på vCPU:erna för virtuell dator. Använd förhållandevärdet för att beräkna hur många VM-instanser som får rabatten för SUSE Linux-plan.

Om du till exempel köper en plan för SUSE Linux Enterprise Server for HPC Priority för en virtuell dator med 3 eller 4 vCPU:er är förhållandet för den reservationen 2. Rabatten omfattar SUSE-programvarukostnaden för:

- 2 distribuerade virtuella datorer med 1 eller 2 vCPU:er,
- 1 distribuerad virtuell dator med 3 eller 4 vCPU:er,
- eller 0,77 eller cirka 77 % av en virtuell dator med 5 eller fler vCPU:er.

Förhållandet för 5 eller fler vCPU:er är 2,6. En reservation för SUSE med en virtuell dator med 5 eller fler vCPU:er omfattar därmed bara en del av programvarukostnaden, som är cirka 77 %.

I följande tabeller visas de programvaruplaner som du kan köpa en reservation för, deras associerade användningsmätare samt förhållandet för var och en.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Namn på Azure-portalens marknadsplats:

- SLES 12 SP3 för HPC (prioritet)

|SUSE VM | MeterId| Förhållande| Exempel på VM-storlek|
| -------| ------------------------| --- |--- |
|SLES för HPC 1–2 vCPU:er|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES för HPC 3–4 vCPU:er|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES för HPC 5+ vCPU:er|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Namn på Azure-portalens marknadsplats:

- SLES 12 SP3 för HPC

|SUSE VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- | --- | ------------------------| --- |
|SLES för HPC 1–2 vCPU:er |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES för HPC 3–4 vCPU:er|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES för HPC 5+ vCPU:er |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Namn på Azure-portalens marknadsplats:

- SLES för SAP 15 (prioritet)
- SLES för SAP 12 SP3 (prioritet)
- SLES för SAP 12 SP2 (prioritet)

|SUSE VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority 1–2 vCPU:er|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority 3–4 vCPU:er |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority 5+ vCPU:er |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Namn på Azure-portalens marknadsplats:

- SLES 15
- SLES 15 (standard)
- SLES 12 SP3 (standard)

|SUSE VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- |------------------------| --- |--- |
|1–2-kärniga SLES-vCPU:er |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|3–4-kärniga SLES-vCPU:er |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES 5+ vCPU:er |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om reservations finns i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Förskottsbetala för SUSE-programvaruplaner med Azure-reservationer](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Hantera Azure Reservations](manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)