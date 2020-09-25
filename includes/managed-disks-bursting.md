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
ms.openlocfilehash: 634deb655620f99c7b7b2b55819c58308eaa5a92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275173"
---
På Azure erbjuder vi möjlighet att öka prestandan för disk lagring IOPS och MB/s, vilket kallas burst-överföring på både Virtual Machines och diskar. Bursting är användbart i många fall, till exempel vid hantering av oväntad disk trafik eller bearbetning av batch-jobb. Du kan effektivt utnyttja VM och disknivå-överföring för att uppnå fantastiska bas linje-och burst-prestanda både på den virtuella datorn och på hård disken. På så sätt kan du få bra prestanda och prestanda för hög prestanda både för din virtuella dator och disk. 

Observera att bursting på diskar och virtuella datorer är oberoende av varandra. Om du har en burst-disk behöver du inte en virtuell dator med burst-överföring för att tillåta att disken får burst. Om du har en virtuell dator med burst-överföring behöver du inte en mellanliggande disk för att tillåta att din virtuella dator får burst. 
