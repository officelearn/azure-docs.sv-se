---
title: Lagringsnivåer och tillägget datanoder för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Lagringsnivåer och tillägget datanoder för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5868aaf29d6b57ea608763840a08f1991a982e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628599"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Använda SAP HANA-tillägget och lagringsnivåer datanoder

SAP har stöd för en datamodell för lagringsnivåer för SAP BW i olika utgåvor av SAP NetWeaver och SAP BW/4HANA. Mer information om lagringsnivåer datamodellen finns i SAP-dokumentet [SAP BW/4HANA och SAP BW på HANA med SAP HANA-tillägget noder](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Du kan använda alternativ 1 konfiguration av SAP HANA-tillägget noder som beskrivs i vanliga frågor och svar och SAP-bloggen dokument med stora HANA-instansen. Alternativ 2 konfigurationer kan ställas in med följande HANA stora instans SKU: er: S72m, S192, S192m, S384 och S384m. 

När du granskar dokumentationen kanske fördelen inte visas omedelbart. Men när du tittar på riktlinjer för SAP-storlek, du kan se en fördel med hjälp av alternativet-1 och alternativ 2 SAP HANA-tillägget noder. Här följer exempel:

- Riktlinjer för storleksändring av SAP HANA kräver vanligtvis dubbelt så mycket datavolym som minne. När du kör dina SAP HANA-instans med frekventa data du har bara 50 procent eller mindre minne fylld med data. Resten av minnet lagras helst för SAP HANA utför sitt arbete.
- Innebär det att i en HANA stora instans S192-enhet med 2 TB minne, köra ett SAP BW-databas du endast har 1 TB som datavolym.
- Om du använder en ny SAP HANA-tillägg-nod i alternativ 1, även en S192 HANA stora SKU instans, får du en ytterligare 2 TB kapacitet för datavolym. I alternativ 2-konfiguration får du en ytterligare 4 TB för varm datavolym. Jämfört med frekvent noden, kan fullständig minneskapaciteten för noden ”varma” tillägget användas för lagring av data för alternativ 1. Dubbla minnet som kan användas för datavolymen i alternativ 2 nodkonfiguration för SAP HANA-tillägget.
- Att avslutas med en kapacitet på 3 TB för dina data och ett hot att varmt förhållande på 1:2 för alternativ 1. Du har 5 TB med data och ett 1:4-förhållande med nodkonfiguration alternativ 2-tillägget.

Ju högre datavolym du jämfört med att minnet, desto högre risken är att frekventa data som du efterfrågar lagras på disklagring.

**Nästa steg**
- Se [SAP HANA (stora instanser)-arkitektur på Azure](hana-architecture.md)