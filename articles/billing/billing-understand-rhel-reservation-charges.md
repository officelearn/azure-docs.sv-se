---
title: Rabatter för Red Hat-reservationsplan – Azure
description: Lär dig hur rabatter för Red Hat-planer tillämpas på Red Hat-programvara på virtuella datorer.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2019
ms.author: cwatson
ms.openlocfilehash: 7af9f6ae0c274f38798d96dd9228a68ce709686c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75388456"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Förstå hur rabatten för reservation med Red Hat Linux Enterprise-programvara tillämpas för Azure

När du har köpt en Red Hat Linux-plan tillämpas rabatten automatiskt på distribuerade virtuella Red Hat-datorer (VM) som matchar reservationen. En Red Hat Linux-plan omfattar kostnaden för att köra Red Hat-programvaran på en virtuell Azure-dator.

För att köpa rätt Red Hat Linux-plan behöver du veta vilka Red Hat-datorer du kör samt antalet vCPU:er på dessa virtuella datorer. Använd följande avsnitt för att välja vilken plan du ska köpa med hjälp av din CSV-fil för användning.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabatten tillämpas på olika VM-storlekar

Liksom reserverade VM-instanser ger köp av Red Hat-planer flexibel instansstorlek. Det innebär att rabatten gäller även när du distribuerar en virtuell dator med ett annat vCPU-antal. Rabatten gäller för olika VM-storlekar i programvaruplanen.

Rabattbeloppet beror på det förhållande som anges i följande tabeller. Förhållandet jämför det relativa fotavtrycket för varje mätare den i gruppen. Förhållandet beror på vCPU:erna för virtuell dator. Använd förhållandevärdet för att beräkna hur många VM-instanser som får rabatten för Red Hat Linux-plan.

Om du till exempel köper en plan för Red Hat Linux Enterprise Server för en virtuell dator med 3 eller 4 vCPU:er är förhållandet för den reservationen 2. Rabatten omfattar Red Hat-programvarukostnaden för:

- 2 distribuerade virtuella datorer med 1 eller 2 vCPU:er,
- 1 distribuerad virtuell dator med 3 eller 4 vCPU:er,
- eller 0,77 eller cirka 77 % av en virtuell dator med 5 eller fler vCPU:er.

Förhållandet för 5 eller fler vCPU:er är 2,6. En reservation för Red Hat med en virtuell dator med 5 eller fler vCPU:er omfattar därmed bara en del av programvarukostnaden, som är cirka 77 %.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Förstå Red Hat-VM-användning innan du köper

I följande tabeller visas de programvaruplaner som du kan köpa en reservation för, deras associerade användningsmätare samt förhållandet för var och en.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Namn på Azure-portalens marknadsplats:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (senaste lvm)

|Red Hat-VM | MeterId| Förhållande| Exempel på VM-storlek|
| -------| ------------------------| --- |--- |
|Licens för VM med 1–4 vCPU:er|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|Licens för VM med 1–4 vCPU:er|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|Licens för VM med 1–4 vCPU:er|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|Licens för VM med 5+ vCPU:er|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|Licens för VM med 5+ vCPU:er|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|Licens för VM med 44 vCPU:er|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|Licens för VM med 60 vCPU:er|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux för SAP med hög tillgänglighet

Namn på Azure-portalens marknadsplats:

|Red Hat-VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- | --- | ------------------------| --- | --- |
|Licens för VM med 1–4 vCPU:er |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|Licens för VM med 5+ vCPU:er|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux med hög tillgänglighet

Namn på Azure-portalens marknadsplats:

|Red Hat-VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- |------------------------| --- | --- |
|Licens för VM med 1–4 vCPU:er|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|Licens för VM med 5+ vCPU:er|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Namn på Azure-portalens marknadsplats:

- Red Hat Enterprise Linux 6.8 för SAP-företagsappar
- Red Hat Enterprise Linux 7.3 för SAP-företagsappar
- Red Hat Enterprise Linux 7.4 för SAP
- Red Hat Enterprise Linux 7.5 för SAP

|Red Hat-VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- |------------------------| --- |--- |
|Licens för VM med 1 vCPU|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|Licens för VM med 2 vCPU:er|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|Licens för VM med 4 vCPU:er|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|Licens för VM med 6 vCPU:er|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|Licens för VM med 8 vCPU:er|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|Licens för VM med 12 vCPU:er|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|Licens för VM med 16 vCPU:er|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|Licens för VM med 20 vCPU:er|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|Licens för VM med 24 vCPU:er|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|Licens för VM med 32 vCPU:er|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|Licens för VM med 40 vCPU:er|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|Licens för VM med 44 vCPU:er|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|Licens för VM med 60 vCPU:er|a22bb342-ba9a-4529-a178-39a92ce770b6|2,166666667||
|Licens för VM med 64 vCPU:er|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|Licens för VM med 72 vCPU:er|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|Licens för VM med 96 vCPU:er|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|Licens för VM med 128 vCPU:er|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Namn på Azure-portalens marknadsplats:

- Red Hat Enterprise Linux 6.7 för SAP HANA
- Red Hat Enterprise Linux 7.2 för SAP HANA
- Red Hat Enterprise Linux 7.3 för SAP HANA

|Red Hat-VM | MeterId | Förhållande|Exempel på VM-storlek|
| ------- |------------------------| --- |--- |
|Licens för VM med 1 vCPU|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|Licens för VM med 2 vCPU:er|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|Licens för VM med 4 vCPU:er|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|Licens för VM med 6 vCPU:er|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|Licens för VM med 8 vCPU:er|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|Licens för VM med 12 vCPU:er|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|Licens för VM med 16 vCPU:er|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|Licens för VM med 20 vCPU:er|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|Licens för VM med 24 vCPU:er|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|Licens för VM med 32 vCPU:er|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|Licens för VM med 40 vCPU:er|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|Licens för VM med 44 vCPU:er|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|Licens för VM med 60 vCPU:er|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|Licens för VM med 64 vCPU:er|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|Licens för VM med 72 vCPU:er|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|Licens för VM med 96 vCPU:er|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|Licens för VM med 128 vCPU:er|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>Nästa steg

Mer information om reservations finns i följande artiklar:

- [Vad är reservationer för Azure](billing-save-compute-costs-reservations.md)
- [Förskottsbetala för Red Hat-programvaruplaner med Azure-reservationer](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera reservationer för Azure](billing-manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](billing-understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
