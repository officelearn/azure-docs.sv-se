---
title: Rabatter för Red Hat-reservationsplan – Azure
description: Lär dig hur rabatter för Red Hat-planer tillämpas på Red Hat-programvara på virtuella datorer.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 8e735d623cc991d2e3720e0ec418eee77aa8c46c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350865"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Förstå hur rabatten för reservation med Red Hat Linux Enterprise-programvara tillämpas för Azure

När du har köpt en Red Hat Linux-plan tillämpas rabatten automatiskt på distribuerade virtuella Red Hat-datorer (VM) som matchar reservationen. En Red Hat Linux-plan omfattar kostnaden för att köra Red Hat-programvaran på en virtuell Azure-dator.

För att köpa rätt Red Hat Linux-plan behöver du veta vilka Red Hat-datorer du kör samt antalet vCPU:er på dessa virtuella datorer. Använd följande avsnitt för att välja vilken plan du ska köpa med hjälp av din CSV-fil för användning.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabatten tillämpas på olika VM-storlekar

Liksom reserverade VM-instanser ger köp av Red Hat-planer flexibel instansstorlek. Det innebär att rabatten gäller även när du distribuerar en virtuell dator med ett annat vCPU-antal. Rabatten gäller för olika VM-storlekar i programvaruplanen.

Rabattbeloppet beror på det förhållande som anges i följande tabeller. Förhållandet jämför det relativa fotavtrycket för varje VM-storlek i den gruppen. Förhållandet beror på vCPU:erna för virtuell dator. Använd förhållandevärdet för att beräkna hur många VM-instanser som får rabatten för Red Hat Linux-plan.

Om du till exempel köper en plan för Red Hat Linux Enterprise Server för en virtuell dator med 1 till 4 vCPU:er är förhållandet för den reservationen 1. Rabatten omfattar Red Hat-programvarukostnaden för:

- 1 distribuerad virtuell dator med 1 till 4 vCPU:er,
- eller 0,46 eller cirka 46 % av Red Hat Enterprise Linux-kostnaderna för en virtuell dator med 5 eller fler vCPU:er.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Namn på Azure-portalens marknadsplats:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Kontrollera mätare för Red Hat Enterprise Linux som planen gäller](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Nästa steg

Mer information om reservations finns i följande artiklar:

- [Vad är reservationer för Azure](save-compute-costs-reservations.md)
- [Förskottsbetala för Red Hat-programvaruplaner med Azure-reservationer](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Hantera reservationer för Azure](manage-reserved-vm-instance.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).