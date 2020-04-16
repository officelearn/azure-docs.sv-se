---
title: Red Hat Enterprise Linux-avbildningar i Azure | Microsoft-dokument
description: Lär dig mer om Red Hat Enterprise Linux-avbildningar i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 40a715ea27be161f09dea59e57799b08406e8ca7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393762"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Red Hat Enterprise Linux (RHEL) avbildningar tillgängliga i Azure
Azure erbjuder en mängd olika RHEL-avbildningar för olika användningsfall.

> [!NOTE]
> Alla RHEL-avbildningar är tillgängliga i Azure public- och Azure Government-moln. De är inte tillgängliga i Azure China-moln.

## <a name="list-of-rhel-images"></a>Lista över RHEL-bilder
Det här är en lista över RHEL-avbildningar som är tillgängliga i Azure. Om inget annat anges är alla bilder LVM-partitionerade och kopplade till vanliga RHEL-databaser (inte EUS, inte E4S). Följande bilder är för närvarande tillgängliga för allmänt bruk:

Erbjudande| SKU | Partitionering | Etablering | Anteckningar
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | Raw    | Linux-agent |
|             | 6.8      | Raw    | Linux-agent |
|             | 6.9      | Raw    | Linux-agent |
|             | 6.10     | Raw    | Linux-agent |
|             | 7-RÅ    | Raw    | Linux-agent | RHEL 7.x bildfamilj. <br> Bifogas till vanliga databaser som standard (inte EUS).
|             | 7-LVM    | Lvm    | Linux-agent | RHEL 7.x bildfamilj. <br> Bifogas till vanliga databaser som standard (inte EUS). Om du letar efter en standard RHEL-avbildning att distribuera använder du den här uppsättningen avbildningar och/eller dess motsvarighet generation 2.
|             | 7lvm-gen2| Lvm    | Linux-agent | Generation 2, RHEL 7.x bildfamilj. <br> Bifogas till vanliga databaser som standard (inte EUS). Om du letar efter en standard RHEL-avbildning att distribuera använder du den här uppsättningen avbildningar och/eller dess generation 1-motsvarighet.
|             | 7-RAW-CI | RAW-CI | moln-init  | RHEL 7.x bildfamilj. <br> Bifogas till vanliga databaser som standard (inte EUS).
|             | 7.2      | Raw    | Linux-agent |
|             | 7.3      | Raw    | Linux-agent |
|             | 7.4      | Raw    | Linux-agent | Bifogat till EUS-arkiv som standard från och med april 2019.
|             | 74-gen2  | Raw    | Linux-agent | Som standard är de kopplade till EUS-arkiven.
|             | 7.5      | Raw    | Linux-agent | Bifogat till EUS-arkiv som standard från och med juni 2019.
|             | 75-gen2  | Raw    | Linux-agent | Som standard är de kopplade till EUS-arkiven.
|             | 7.6      | Raw    | Linux-agent | Bifogat till EUS-arkiv som standard från och med maj 2019.
|             | 76-gen2  | Raw    | Linux-agent | Som standard är de kopplade till EUS-arkiven.
|             | 7.7      | Lvm    | Linux-agent | Som standard är de kopplade till EUS-arkiven.
|             | 8        | Lvm    | Linux-agent | RHEL 8.x bildfamilj
|             | 8-gen2   | Lvm    | Linux-agent | Hyper-V Generation 2 - RHEL 8.x bildfamilj.
RHEL-SAP      | 7.4      | Lvm    | Linux-agent | RHEL 7.4 för SAP HANA och Business Apps. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
|             | 74sap-gen2| Lvm    | Linux-agent | RHEL 7.4 för SAP HANA och Business Apps. Bild av generation 2. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
|             | 7.5       | Lvm    | Linux-agent | RHEL 7.5 för SAP HANA och Business Apps. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
|             | 75sap-gen2| Lvm    | Linux-agent | RHEL 7.5 för SAP HANA och Business Apps. Bild av generation 2. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
|             | 7.6       | Lvm    | Linux-agent | RHEL 7.6 för SAP HANA och Business Apps. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
|             | 76sap-gen2| Lvm    | Linux-agent | RHEL 7.6 för SAP HANA och Business Apps. Bild av generation 2. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
|             | 7.7       | Lvm    | Linux-agent | RHEL 7.7 för SAP HANA och Business Apps. Bifogat till E4S-arkiv, kommer att debitera en premie för SAP och RHEL samt bas beräkningsavgiften.
RHEL-SAP-HANA | 6.7       | Raw    | Linux-agent | RHEL 6.7 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningar.
|             | 7.2       | Lvm    | Linux-agent | RHEL 7.2 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningar.
|             | 7.3       | Lvm    | Linux-agent | RHEL 7.3 för SAP HANA. Föråldrad till förmån för RHEL-SAP-avbildningar.
RHEL-SAP-APPAR | 6.8       | Raw    | Linux-agent | RHEL 6.8 för SAP-affärsprogram. Föråldrad till förmån för RHEL-SAP-avbildningar.
|             | 7.3       | Lvm    | Linux-agent | RHEL 7.3 för SAP-affärsprogram. Föråldrad till förmån för RHEL-SAP-avbildningar.
RHEL-HA       | 7.4       | Lvm    | Linux-agent | RHEL 7.4 med HA Add-On. Kommer att ta ut en premie för HA och RHEL utöver basberäkningsavgiften.
|             | 7.5       | Lvm    | Linux-agent | RHEL 7.5 med HA Add-On. Kommer att ta ut en premie för HA och RHEL utöver basberäkningsavgiften.
|             | 7.6       | Lvm    | Linux-agent | RHEL 7.6 med HA Add-On. Kommer att ta ut en premie för HA och RHEL utöver basberäkningsavgiften.
RHEL-SAP-HA   | 7.4          | Lvm    | Linux-agent | RHEL 7.4 för SAP med HA- och Update Services. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
|             | 74sapha-gen2 | Lvm    | Linux-agent | RHEL 7.4 för SAP med HA- och Update Services. Bild av generation 2. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
|             | 7.5          | Lvm    | Linux-agent | RHEL 7.5 för SAP med HA- och Update Services. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
|             | 7.6          | Lvm    | Linux-agent | RHEL 7.6 för SAP med HA- och Update Services. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
|             | 76sapha-gen2 | Lvm    | Linux-agent | RHEL 7.6 för SAP med HA- och Update Services. Bild av generation 2. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
|             | 7.7          | Lvm    | Linux-agent | RHEL 7.7 för SAP med HA- och Update Services. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
|             | 77sapha-gen2 | Lvm    | Linux-agent | RHEL 7.7 för SAP med HA- och Update Services. Bild av generation 2. Ansluten till E4S-förråd. Kommer att debitera en premie för SAP och HA-databaser samt RHEL, utöver de grundläggande beräkningsavgifterna.
rhel-byos     |rhel-lvm74| Lvm    | Linux-agent | RHEL 7.4 BYOS-bilder, som inte är kopplade till någon uppdateringskälla, debiterar inte en RHEL-premie.
|             |rhel-lvm75| Lvm    | Linux-agent | RHEL 7.5 BYOS-bilder, som inte är kopplade till någon uppdateringskälla, debiterar inte en RHEL-premie.
|             |rhel-lvm76| Lvm    | Linux-agent | RHEL 7.6 BYOS-bilder, som inte är kopplade till någon uppdateringskälla, debiterar inte en RHEL-premie.
|             |rhel-lvm77| Lvm    | Linux-agent | RHEL 7.7 BYOS-bilder, som inte är kopplade till någon uppdateringskälla, debiterar inte en RHEL-premie.
|             |rhel-lvm8 | Lvm    | Linux-agent | RHEL 8 BYOS-bilder (RHEL-delversionen visas i bildversionsvärdet), som inte är kopplad till någon uppdateringskälla, debiterar inte en RHEL-premie.

> [!NOTE]
> RHEL-SAP-HANA-produktutbudet anses vara uttjänta av Red Hat. Befintliga distributioner fortsätter att fungera normalt, men Red Hat rekommenderar att kunder migrerar från RHEL-SAP-HANA-avbildningarna till RHEL-SAP-HA-avbildningarna som innehåller SAP HANA-arkiven samt HA-tillägget. Mer information om Red Hat's SAP moln erbjudanden finns [här](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [Red Hat-avbildningarna i Azure](./redhat-images.md).
* Läs mer om [Red Hat Update Infrastructure](./redhat-rhui.md).
* Läs mer om [RHEL BYOS-erbjudandet](./byos.md).
* Information om Supportpolicyer för Red Hat för alla versioner av RHEL finns på [red hat Enterprise Linux life cycle-sidan.](https://access.redhat.com/support/policy/updates/errata)
