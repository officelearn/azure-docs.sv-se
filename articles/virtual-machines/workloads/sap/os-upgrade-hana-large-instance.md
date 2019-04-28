---
title: Uppgraderingen av operativsystemet för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Uppgradera operativsystemet för SAP HANA på Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7d451f3831309b4755170915b35a23da8910510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935644"
---
# <a name="operating-system-upgrade"></a>Uppgradering av operativsystemet
Det här dokumentet beskriver vad på uppgradering av operativsystemet på den stora HANA-instanser.

>[!NOTE]
>OS-uppgradering är kunder ansvar, stöd för Microsoft hjälper dig att viktiga områden att se upp under uppgraderingen. Du bör kontakta leverantören operativsystemet innan du planerar en uppgradering.

Vid tidpunkten för etablering HLI-enheter, installera Microsoft driftsteamet operativsystemet. Framöver kommer du måste bibehålla operativsystemet (exempel: Korrigeringar, justering, uppgradera osv.) på den HLI-enheten.

Innan du större ändringar för operativsystemet (till exempel uppgradera SP1 till SP2), måste du kontakta Microsoft Operations-teamet genom att öppna ett supportärende läsa.

Ange följande på din biljett:

* Ditt HLI prenumerations-ID.
* Namnet på servern.
* Korrigeringsnivå som du planerar att använda.
* Det datum som du planerar den här ändringen. 

Vi rekommenderar du öppnar den här biljett minst en vecka före önskvärt uppgradera datum på grund av att ha driftsteamet som kontrollerar om en uppgradering av inbyggd programvara kommer att vara på din serverbladet.


Stödmatris för de olika versionerna för SAP HANA med olika Linux-versioner, se [SAP Obs! #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Kända problem

Här följer några vanliga kända problem under uppgraderingen:
- Foundation-programvara (Sök Standardplatser) tas bort när Operativsystemet har uppgraderat SKU typ II klassen SKU. Du måste installera om den kompatibel Sök Standardplatser när OS-uppgradering.
- Drivrutiner för Ethernet-kort (ENIC och FNIC) återställdes till den äldre versionen. Du måste installera om en kompatibel version av drivrutinerna efter uppgraderingen.

## <a name="next-steps"></a>Nästa steg
- Se [säkerhetskopiering och återställning](hana-overview-high-availability-disaster-recovery.md) för OS säkerhetskopiera typ I SKU-klass.
- Se [säkerhetskopiering av operativsystem för typ II-SKU: er](os-backup-type-ii-skus.md) för typ II-SKU-klassen.
