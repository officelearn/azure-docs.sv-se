---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 2af8b429b6addf6da32b34773525c51a36624e78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85242084"
---
På Azure erbjuder vi möjlighet att öka prestandan för disk lagring IOPS och MB/s, vilket kallas burst-överföring på både Virtual Machines och diskar. Busting är användbart i många fall, till exempel vid hantering av oväntad disk trafik eller bearbetning av batch-jobb. Du kan effektivt utnyttja VM och disknivå-överföring för att uppnå fantastiska bas linje-och burst-prestanda både på den virtuella datorn och på hård disken. På så sätt kan du få bra prestanda och prestanda för hög prestanda både för din virtuella dator och disk. 

Observera att bursting på diskar och virtuella datorer är oberoende av varandra. Om du har en burst-disk behöver du inte en virtuell dator med burst-överföring för att tillåta att disken får burst. Om du har en virtuell dator med burst-överföring behöver du ingen mellanliggande disk för att tillåta att din virtuella dator får burst. 