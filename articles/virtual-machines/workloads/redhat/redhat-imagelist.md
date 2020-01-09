---
title: Red Hat Enterprise Linux avbildningar i Azure | Microsoft Docs
description: Lär dig mer om Red Hat Enterprise Linux avbildningar i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 61991e271a68c9160a34d0de99fe4c9259ca41cb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486344"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux-avbildningar (RHEL) som är tillgängliga i Azure
Azure erbjuder en rad olika RHEL-avbildningar för olika användnings fall.

## <a name="list-of-rhel-images"></a>Lista över RHEL-avbildningar
Det här är en lista över RHEL-avbildningar som är tillgängliga i Azure. Om inget annat anges är alla avbildningar LVM och är kopplade till vanliga RHEL-databaser (inte EUS, inte E4S). Följande bilder är för närvarande tillgängliga för allmänt bruk:

Erbjudande| SKU | Partitionering | Etablering | Anteckningar
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Linux-agent |
|             | 6.8      | RAW    | Linux-agent |
|             | 6.9      | RAW    | Linux-agent |
|             | 6.10     | RAW    | Linux-agent |
|             | 7-RAW    | RAW    | Linux-agent | RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS).
|             | 7-LVM    | LVM    | Linux-agent | RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS).
|             | 7-RAW-CI | RAW-CI | init  | RHEL 7. x-serien med bilder. <br> Anslutet till vanliga databaser som standard (inte EUS).
|             | 7.2      | RAW    | Linux-agent |
|             | 7.3      | RAW    | Linux-agent |
|             | 7.4      | RAW    | Linux-agent | Anslutet till EUS-databaser som standard från april 2019.
|             | 7.5      | RAW    | Linux-agent | Anslutet till EUS-databaser som standard från och med juni 2019.
|             | 7.6      | RAW    | Linux-agent | Bifogad till EUS-databaser som standard från maj 2019.
|             | 7.7      | LVM    | Linux-agent | Ansluten till EUS-databaser som standard.
|             | 8        | LVM    | Linux-agent | RHEL 8. x-serien med bilder
|             | 8-Gen2   | LVM    | Linux-agent | Hyper-V generation 2-RHEL 8. x-serien med bilder.
RHEL-SAP      | 7.4      | LVM    | Linux-agent | RHEL 7,4 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 7.6      | LVM    | Linux-agent | RHEL 7,5 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
|             | 7.7      | LVM    | Linux-agent | RHEL 7,5 för SAP HANA-och Business-appar. Ansluten till E4S-lagringsplatser kommer att ta betalt för SAP och RHEL samt bas beräknings avgiften.
RHEL – SAP-HANA | 6.7      | RAW    | Linux-agent | RHEL 6,7 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningarna.
|             | 7.2      | LVM    | Linux-agent | RHEL 7,2 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningarna.
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningarna.
RHEL-SAP-APPS | 6.8      | RAW    | Linux-agent | RHEL 6,8 för SAP Business Applications. Föråldrad till förmån för RHEL-SAP-avbildningarna.
|             | 7.3      | LVM    | Linux-agent | RHEL 7,3 för SAP Business Applications. Föråldrad till förmån för RHEL-SAP-avbildningarna.
RHEL-HA       | 7.4      | LVM    | Linux-agent | RHEL 7,4 med HA-tillägget. Debiterar ett Premium för HA-och RHEL ovanpå bas beräknings avgiften.
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 med HA-tillägget. Debiterar ett Premium för HA-och RHEL ovanpå bas beräknings avgiften.
|             | 7.6      | LVM    | Linux-agent | RHEL 7,6 med HA-tillägget. Debiterar ett Premium för HA-och RHEL ovanpå bas beräknings avgiften.
RHEL – SAP-HA   | 7.4      | LVM    | Linux-agent | RHEL 7,4 för SAP med HA-tillägget. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 7.5      | LVM    | Linux-agent | RHEL 7,5 för SAP med HA-tillägget. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
|             | 7.6      | LVM    | Linux-agent | RHEL 7,6 för SAP med HA-tillägget. Ansluten till E4S-lagringsplatser. Debiterar ett Premium för SAP-och HA-databaser samt RHEL, ovanpå bas beräknings avgifterna.
RHEL – BYOS     |RHEL – lvm74| LVM    | Linux-agent | RHEL 7,4 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm75| LVM    | Linux-agent | RHEL 7,5 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm76| LVM    | Linux-agent | RHEL 7,6 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm77| LVM    | Linux-agent | RHEL 7,7 BYOS-avbildningar, som inte är kopplade till någon källa med uppdateringar, tar inte betalt för en RHEL Premium.
|             |RHEL – lvm8 | LVM    | Linux-agent | RHEL 8 BYOS-avbildningar (RHEL lägre version visas i avbildningens versions värde), som inte är kopplade till någon källa med uppdateringar, kommer inte att ta betalt för en RHEL Premium.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Red Hat-avbildningarna i Azure](./redhat-images.md).
* Läs mer om den [Red Hat-uppdateringen av infrastrukturen](./redhat-rhui.md).
* Läs mer om [RHEL BYOS-erbjudandet](./redhat-byos.md).
* Information om Red Hat support-principer för alla versioner av RHEL finns på sidan [Red Hat Enterprise Linux livs cykel](https://access.redhat.com/support/policy/updates/errata) .