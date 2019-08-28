---
title: Uppgradering av operativ system för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Utför uppgradering av operativ system för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6341f58791c2fad71a65650e32cff02fb52d78c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098687"
---
# <a name="operating-system-upgrade"></a>Uppgradering av operativ system
I det här dokumentet beskrivs information om uppgraderingar av operativ system på de stora och HANA-instanserna.

>[!NOTE]
>Uppgraderingen av operativ systemet är ett kundansvar, men Microsoft Operations support kan hjälpa dig med viktiga områden att titta på under uppgraderingen. Du bör kontakta operativ system leverantören och innan du planerar att uppgradera.

Vid tidpunkten för HLI enhets etablering installerar Microsoft Operations-teamet operativ systemet. Under tiden måste du underhålla operativ systemet (exempel: Korrigeringar, justering, uppgradering osv.) på HLI-enheten.

Innan du gör större ändringar i operativ systemet (till exempel uppgradera SP1 till SP2) måste du kontakta Microsoft Operations team genom att öppna ett support ärende för att konsultera.

Ta med i din biljett:

* Ditt HLI-prenumerations-ID.
* Ditt Server namn.
* Korrigerings nivån som du planerar att tillämpa.
* Det datum då du planerar den här ändringen. 

Vi rekommenderar att du öppnar den här biljetten minst en vecka före det önskvärda uppgraderings datumet på grund av att drifts teamet kontrollerar om en uppgradering av inbyggd program vara kommer att vara nödvändig på Server bladet.


En support mat ris för olika SAP HANA versioner med olika Linux-versioner finns i [SAP Obs! #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Kända problem

Följande är några vanliga kända problem under uppgraderingen:
- I SKU Type II klass-SKU: n för program varu Foundation (SFS) tas den bort efter uppgraderingen av operativ systemet. Du måste installera om den kompatibla SFS efter uppgraderingen av operativ systemet.
- Ethernet-kort driv rutiner (ENIC och FNIC) återställdes till en äldre version. Du måste installera om den kompatibla versionen av driv rutinerna efter uppgraderingen.

## <a name="next-steps"></a>Nästa steg
- Hänvisa till [säkerhets kopiering och återställning](hana-overview-high-availability-disaster-recovery.md) av säkerhets kopierings typ I SKU-klass.
- Se [säkerhets kopiering av OS för typ II SKU: er av revision 3](os-backup-type-ii-skus.md) -stämplar för typ II SKU-klass.
