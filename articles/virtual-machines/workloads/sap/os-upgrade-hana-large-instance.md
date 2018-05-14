---
title: Uppgraderingen av operativsystemet för SAP HANA i Azure (stora instanser) | Microsoft Docs
description: Uppgradera operativsystemet för SAP HANA i Azure (stora instanser)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="operating-system-upgrade"></a>Uppgraderingen av operativsystemet
Det här dokumentet beskriver vad på uppgradering av operativsystemet på HANA stora instanser.

>[!NOTE]
>OS-uppgraderingen är kunder ansvar, stöd för Microsoft kan hjälpa dig att viktiga områden att se upp under uppgraderingen. Innan du planerar en uppgradering bör du kontakta leverantören operativsystem.

Vid tidpunkten för HLI enheten etablering operations Microsoft-teamet att installera operativsystemet. Över tiden, måste du underhålla operativsystemet (exempel: korrigering, justera, uppgradera etc.) på HLI-enheten.

Innan du större ändringar av operativsystemet (till exempel, uppgradera ett operativsystem) du **måste** Överväg följande kompatibilitetsmatrix. Du **måste** också kontakta Microsoft Operations-teamet genom att öppna ett supportärende kontakta innan du börjar stora operativsystem aktiviteter som uppgraderingen.

Stöd matrix för de olika SAP HANA-versionerna med de olika versionerna av Linux, se [SAP Obs #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Följande kompatibilitet har testats för HLIs. Om HLI servern ligger utanför matrisen kompatibilitet, kontaktar du Microsoft-supporten för åtgärden.

## <a name="for-type-i-class-sku-category"></a>För typen klassen I SKU-kategori

| Konfiguration | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| Servern inbyggd programvara | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC Version | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC Version | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | Disabled | Disabled | Disabled | Disabled |
| Kernel-Version | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.X86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>För typ II klass SKU-kategori

| Konfiguration | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| RMC Version på inbyggd programvara | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| BMC Version på inbyggd programvara | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Programvaruversion Foundation Server (Sök Standardplatser) | 2,16    | 2,16    | 2.14/2.16   | 2,16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e Version    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| Kernel-Version    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>Kända problem

Följande är några vanliga kända problem under uppgraderingen:
- På SKU typ II klassen SKU tas foundation programvara (Sök Standardplatser) bort när du uppgraderar Operativsystemet. Du måste installera om kompatibel Sök Standardplatser när du uppgraderar Operativsystemet.
- Ethernet-drivrutiner (ENIC och FNIC) återställs till en äldre version. Du måste installera om kompatibel version av drivrutinerna efter uppgraderingen.

## <a name="next-steps"></a>Nästa steg
- Se [säkerhetskopiering och återställning](hana-overview-high-availability-disaster-recovery.md) för OS säkerhetskopiera typ I SKU-klassen.
- Se [OS säkerhetskopiering för typ II SKU: er](os-backup-type-ii-skus.md) för typ II SKU-klassen.