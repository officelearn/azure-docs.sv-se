---
title: Förstå SUSE plan rabatt och användning – Azure reservationer | Microsoft Docs
description: Lär dig hur SUSE plan rabatter tillämpas SUSE-programvara på virtuella datorer.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: cwatson
ms.openlocfilehash: 62f75be44ed92528b48fc0f093f5966284662312
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393697"
---
# <a name="understand-how-the-suse-linux-enterprise-software-plan-discount-is-applied"></a>Förstå hur SUSE Linux Enterprise software plan rabatten

När du köper en plan för SUSE Linux rabatten automatiskt till distribuerade SUSE virtuella datorer (VM) som matchar reservationen. En plan för SUSE Linux täcker kostnaden för att köra SUSE-programvara på en Azure VM.

Om du vill köpa en lämplig SUSE Linux-plan, måste du förstå vilka SUSE virtuella datorer som du kör och antalet virtuella processorer på de virtuella datorerna. Använd följande avsnitt för att identifiera från en CSV-fil från din användning vad planerar att köpa.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Rabatten gäller för olika storlekar på Virtuella datorer med flexibilitet för datorinstanser storlek

Som om reserverade VM-instanser, SUSE planera erbjuda inköp instans storlek flexibilitet. Det innebär att rabatten gäller även när du distribuerar en virtuell dator med ett antal olika virtuella processorer. Rabatten gäller för olika storlekar på Virtuella datorer i plan för programvara.

Rabatt mycket beror på det förhållande som visas i följande tabell. Förhållandet mellan jämför den relativa storleken för varje mätare i gruppen. Förhållandet mellan beror på VM-vcpu: er. Använd värdet för förhållande för att beräkna hur många Virtuella datorinstanser få rabatt för SUSE Linux-plan.

Om du köper en plan för SUSE Linux Enterprise Server för HPC prioritet för en virtuell dator med 3 eller 4 virtuella processorer kan är kvoten för den reserverade 2. Rabatten täcker kostnaden för SUSE-programvara för:

- 2 distribuerade virtuella datorer med 1 eller 2 virtuella processorer,
- 1 distribuerat med 3 eller 4 virtuella processorer,
- eller 0.77 eller om 77% av en virtuell dator med 5 eller flera virtuella processorer.

Kvoten för 5 eller flera virtuella processorer är 2.6. En reservation för SUSE med en virtuell dator med 5 eller flera virtuella processorer täcker så en enda del av kostnad för programvara med cirka 77%.

## <a name="understand-your-suse-vm-usage-before-buying-a-suse-linux-plan"></a>Förstå din SUSE VM-användning innan du köper en plan för SUSE Linux

Följande tabeller visar programvaruplaner som du kan köpa en reservation för, deras associerade användningsnivå mätare och förhållandena för var och en.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server för HPC-prioritet

Namn på Azure portal marketplace:

- SLES 12 SP3 för HPC (prioritet)

|SUSE-VM | Mätar-ID| Förhållandet mellan| Exempel VM-storlek|
| -------| ------------------------| --- |--- |
|SLES för HPC 1 – 2 virtuella processorer|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES för HPC 3 – 4 virtuella processorer|e531e1c0-09c9-4d83-B7D0-a2c6741faa22|2|D4s_v3|
|SLES för HPC 5 + vcpu: er|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server för HPC-Standard

Namn på Azure portal marketplace:

- SLES 12 SP3 för HPC

|SUSE-VM | Mätar-ID | Förhållandet mellan|Exempel VM-storlek|
| ------- | --- | ------------------------| --- | --- |
|SLES för HPC 1 – 2 virtuella processorer |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES för HPC 3 – 4 virtuella processorer|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES för HPC 5 + vcpu: er |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Azure portal marketplace-namn:

- SLES for SAP 15 (prioritet)
- SLES för SAP 12 SP3 (prioritet)
- SLES för SAP 12 SP2 (prioritet)

|SUSE-VM | Mätar-ID | Förhållandet mellan|Exempel VM-storlek|
| ------- |------------------------| --- | --- |
|SLES för SAP Priority 1 – 2 virtuella processorer|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES för SAP Priority 3 – 4 virtuella processorer |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES för SAP Priority 5 + vcpu: er |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise serverprioritet

Azure portal marketplace-namn:

- SLES 15 (PRIORITET)
- SLES 12 SP3 (prioritet)
- SLES 11 SP4 (prioritet)

|SUSE-VM | Mätar-ID | Förhållandet mellan|Exempel VM-storlek|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 – 4 virtuella processorer |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 – 4 virtuella processorer |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 virtuella processorer |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 virtuella processorer |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|Virtuella processorer SLES 12-kärnor |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 virtuella processorer |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 virtuella processorer |c5228804-1de6-4BD4-a61c-501d9003acc8|3.2| |
|Virtuella processorer SLES 24-kärnor |-005 d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 virtuella processorer |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|Virtuella processorer SLES 40-kärnor |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 virtuella processorer |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|Virtuella processorer SLES 72-kärnor |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|Virtuella processorer SLES 96-kärnor |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 kärnor vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Azure portal marketplace-namn:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|SUSE-VM | Mätar-ID | Förhållandet mellan|Exempel VM-storlek|
| ------- |------------------------| --- |--- |
|SLES 1 – 2 kärnor virtuella processorer |4b2fecfc-B110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3 – 4 kärnor virtuella processorer |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + vcpu: er |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="next-steps"></a>Nästa steg

Mer information om reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Betala i förskott för SUSE-programvaruplaner med Azure-reservationer](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Förskottsbetala för virtuella datorer med Azure reserverade VM-instanser](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.