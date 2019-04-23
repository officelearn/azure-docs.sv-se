---
title: Programvara plan rabatt – Azure | Microsoft Docs
description: Lär dig hur programvaran plan rabatter tillämpas på ett program på virtuella datorer.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: bcbf5ab48f3476a911fc4ade1eb0c395fb335d43
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002739"
---
# <a name="azure-software-plan-discount"></a>Azure-programvara plan rabatt

Azure-programvara är för SUSE och RedHat reservationer som gäller för distribuerade virtuella datorerna. Programvara rabatten plan till användningen av programvara över distribuerade virtuella datorer som matchar reservationen.

När du stänger en virtuell dator, används automatiskt rabatten till en annan matchande virtuell dator, om det är tillgängligt. En plan för programvara täcker kostnaden för att köra programvaran på en virtuell dator. Andra avgifter, till exempel beräkning, lagring och nätverk debiteras separat.

Om du vill köpa rätt plan, måste du förstå din VM-användning och antalet virtuella processorer på de virtuella datorerna. Använd följande avsnitt för att identifiera vad planerar att köpa, baserat på användningsdata.

## <a name="how-reservation-discount-is-applied"></a>Hur reservationsrabatten tillämpas

En reservationsrabatten är ”*användning – it-eller-förlora-it*”. Så om du inte har matchande resurser för en timme, förlorar sedan du en Reservationskvantitet för den timmen. Du kan inte utföra vidarebefordra oanvända reserverade timmar.

När du stänger en resurs kan tillämpas reservationsrabatten automatiskt till en annan matchande resurs i det specificerade omfånget. Om inga matchande resurser finns i det specificerade omfånget så är de reserverade timmarna *förlorad*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Granska RedHat VM användning innan du köper

Hämtar produktnamnet på från användningsdata och köp RedHat-plan med samma typ och storlek.

Exempel: om din användning har produkten **Red Hat Enterprise Linux - 1-4 vCPU VM-licens**, ska du köpa **Red Hat Enterprise Linux** för **1-4 vCPU VM**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Granska SUSE VM användning innan du köper

Hämtar produktnamnet på från användningsdata och köp SUSE-plan med samma typ och storlek.

Exempel: om din användning är för produkten **SUSE Linux Enterprise serverprioritet – 2 – 4 vCPU Support för virtuella datorer**, ska du köpa **SUSE Linux Enterprise serverprioritet** för **2 – 4 vCPU**.

![Exempel på hur du väljer att köpa produkten](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Rabatten gäller för olika VM-storlekar för SUSE-planer

Som om reserverade VM-instanser, SUSE planera erbjuda inköp instans storlek flexibilitet. Det innebär att rabatten gäller även när du distribuerar en virtuell dator med ett antal olika virtuella processorer. Rabatten gäller för olika storlekar på Virtuella datorer i plan för programvara.

Rabatt mycket beror på det förhållande som visas i följande tabell. Förhållandet mellan jämför den relativa storleken för varje mätare i gruppen. Förhållandet mellan beror på VM-vcpu: er. Använd värdet för förhållande för att beräkna hur många Virtuella datorinstanser få rabatt för SUSE Linux-plan.

Om du köper en plan för SUSE Linux Enterprise Server för HPC prioritet för en virtuell dator med 3 eller 4 virtuella processorer kan är kvoten för den reserverade 2. Rabatten täcker kostnaden för SUSE-programvara för:

- 2 distribuerade virtuella datorer med 1 eller 2 virtuella processorer,
- 1 distribuerat med 3 eller 4 virtuella processorer,
- eller 0.77 eller om 77% av en virtuell dator med 5 eller flera virtuella processorer.

Kvoten för 5 eller flera virtuella processorer är 2.6. En reservation för SUSE med en virtuell dator med 5 eller flera virtuella processorer täcker så en enda del av kostnad för programvara med cirka 77%.

Följande tabeller visar programvaruplaner som du kan köpa en reservation för, deras associerade användningsnivå mätare och förhållandena för var och en.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server för HPC-prioritet

Namn på Azure portal marketplace:

- SLES 12 SP3 för HPC (prioritet)

|SUSE-VM | Mätar-ID| Förhållandet mellan| Exempel VM-storlek|
| -------| ------------------------| --- |--- |
|SLES för HPC 1 – 2 virtuella processorer|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES för HPC 3 – 4 virtuella processorer|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES för HPC 5 + vcpu: er|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server för HPC-Standard

Namn på Azure portal marketplace:

- SLES 12 SP3 för HPC

|SUSE-VM | Mätar-ID | Förhållandet mellan|Exempel VM-storlek|
| ------- | --- | ------------------------| --- |
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

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

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
|SLES 20 virtuella processorer |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|Virtuella processorer SLES 24-kärnor |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
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
|SLES 1 – 2 kärnor virtuella processorer |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3 – 4 kärnor virtuella processorer |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + vcpu: er |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Betala i förskott för SUSE-programvaruplaner med Azure-reservationer](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
