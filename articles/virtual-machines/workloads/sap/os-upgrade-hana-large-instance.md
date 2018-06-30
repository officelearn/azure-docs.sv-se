---
title: Uppgraderingen av operativsystemet för SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Uppgradera operativsystemet för SAP HANA i Azure (stora instanser)
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
ms.openlocfilehash: cff9be3b074dde4a0335675663133a8df81ae62d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114600"
---
# <a name="operating-system-upgrade"></a>Uppgraderingen av operativsystemet
Det här dokumentet beskriver vad på uppgradering av operativsystemet på HANA stora instanser.

>[!NOTE]
>OS-uppgraderingen är kunder ansvar, stöd för Microsoft kan hjälpa dig att viktiga områden att se upp under uppgraderingen. Innan du planerar en uppgradering bör du kontakta leverantören operativsystem.

Vid tidpunkten för HLI enheten etablering operations Microsoft-teamet att installera operativsystemet. Över tiden, måste du underhålla operativsystemet (exempel: korrigering, justera, uppgradera etc.) på HLI-enheten.

Innan du större ändringar av operativsystemet (till exempel uppgradera SP1 till SP2), måste du kontakta Microsoft Operations-teamet genom att öppna ett supportärende kontakta.


Stöd matrix för de olika SAP HANA-versionerna med de olika versionerna av Linux, se [SAP Obs #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Kända problem

Följande är några vanliga kända problem under uppgraderingen:
- På SKU typ II klassen SKU tas foundation programvara (Sök Standardplatser) bort när du uppgraderar Operativsystemet. Du måste installera om kompatibel Sök Standardplatser när du uppgraderar Operativsystemet.
- Ethernet-drivrutiner (ENIC och FNIC) återställs till en äldre version. Du måste installera om kompatibel version av drivrutinerna efter uppgraderingen.

## <a name="next-steps"></a>Nästa steg
- Se [säkerhetskopiering och återställning](hana-overview-high-availability-disaster-recovery.md) för OS säkerhetskopiera typ I SKU-klassen.
- Se [OS säkerhetskopiering för typ II SKU: er](os-backup-type-ii-skus.md) för typ II SKU-klassen.