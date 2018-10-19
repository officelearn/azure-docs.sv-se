---
title: Krav för Azure IoT Edge-modulen | Microsoft Docs
description: Förutsättningar för att publicera en IoT Edge-modul.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431652"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge-modulen publishing krav

Den här artikeln beskrivs kraven för att publicera ett erbjudande för IoT Edge-modulen.

Om du vill veta mer om IoT Edge-moduler och fördelarna med att publicera en modul i Azure Marketplace kan se den [IoT Edge-moduler publicerar guiden](https://docs.microsoft.com/azure/marketplace/iot-edge-module).

## <a name="publishing-prerequisites"></a>Krav för publicering

Om du vill publicera en IoT Edge-modul i Azure Marketplace, som du behöver uppfylla följande krav:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Åtkomst till den [Cloud Partner Portal](https://cloudpartner.azure.com/). Mer information finns i [publiceringsguide för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Avtal för den [villkor för Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Vara värd för din IoT Edge-modulen teknisk tillgång i ett Azure Container Registry.  Mer information finns i [hur du förbereder din IoT-Edge modulen teknisk tillgång](./cpp-create-technical-assets.md)
- Har metadata redo att använda din IoT Edge-modulen. Till exempel (ofullständig lista):
    - En rubrik
    - En beskrivning (i HTML-format)
    - En logotyp (PNG-format och fast bildstorleken inklusive 40x40px, 90x90px, 115x115px, 255x115px)
    - En period om och sekretesspolicyn princip
    - En standardkonfiguration för modulen som innehåller: vägar, twin önskade egenskaper, createOptions och miljövariabler.
    - Dokumentation
    - Supportkontakter

## <a name="next-steps"></a>Nästa steg

- [Förbereda din IoT-Edge modulen teknisk tillgång](./cpp-create-technical-assets.md)
- [Skapa ditt erbjudande för IoT Edge-modul](./cpp-create-offer.md)
