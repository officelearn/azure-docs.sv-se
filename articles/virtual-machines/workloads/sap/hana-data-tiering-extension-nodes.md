---
title: Datanivå- och tilläggsnoder för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Datanivåindelade och tilläggsnoder för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617152"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Använda SAP HANA-datanivå och tilläggsnoder

SAP stöder en datanivåhanteringsmodell för SAP BW för olika SAP NetWeaver-versioner och SAP BW/4HANA. Mer information om datanivåmodellen finns i SAP-dokumentet [SAP BW/4HANA och SAP BW på HANA med SAP HANA-tilläggsnoder](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Med HANA Large Instance kan du använda alternativ-1-konfiguration av SAP HANA-tilläggsnoder som förklaras i bloggdokumenten för vanliga frågor och SAP. Alternativ-2-konfigurationer kan ställas in med följande SKU:er för stora instanser: S72m, S192, S192m, S384 och S384m. 

När du tittar på dokumentationen kanske fördelen inte syns omedelbart. Men när du tittar på SAP storlek riktlinjer, kan du se en fördel med hjälp av alternativ-1 och alternativ-2 SAP HANA förlängning noder. Här följer några exempel:

- SAP HANA storlek riktlinjer kräver vanligtvis dubbelt så mycket datavolym som minne. När du kör DIN SAP HANA-instans med heta data har du bara 50 procent eller mindre av minnet fyllt med data. Resten av minnet är idealiskt hålls för SAP HANA gör sitt arbete.
- Det innebär att i en HANA Large Instance S192-enhet med 2 TB minne, som kör en SAP BW-databas, har du bara 1 TB som datavolym.
- Om du använder en ytterligare SAP HANA-tilläggsnod med alternativ-1, även en S192 HANA Large Instance SKU, ger den dig ytterligare 2 TB kapacitet för datavolym. I alternativ 2-konfigurationen får du ytterligare 4 TB för varm datavolym. Jämfört med den heta noden kan den fullständiga minneskapaciteten för den "varma" förlängningsnoden användas för data som lagrar för alternativ-1. Dubbla minne kan användas för datavolym i alternativ-2 SAP HANA förlängning nod konfiguration.
- Du får en kapacitet på 3 TB för dina data och ett varmt till varmt förhållande på 1:2 för alternativ-1. Du har 5 TB data och ett 1:4-förhållande med alternativ-2-tilläggsnodkonfigurationen.

Ju högre datavolym jämfört med minnet är, desto större är chansen att de varma data du ber om lagras på disklagring.

**Nästa steg**
- Referera [till SAP HANA-arkitektur (stora instanser) på Azure](hana-architecture.md)