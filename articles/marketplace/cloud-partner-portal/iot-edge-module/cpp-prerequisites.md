---
title: Krav för Azure IoT Edge-modulen | Azure Marketplace
description: Förutsättningar för att publicera en IoT Edge-modul.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a5d1d6fdaf07f8b27820021d4d2ac45ec67c9915
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942112"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>IoT Edge-modulen publishing krav

Den här artikeln beskrivs kraven för att publicera ett erbjudande för IoT Edge-modulen.  Om du inte redan har gjort det, kan du granska den [IoT Edge-moduler publicerar guiden](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Krav för publicering

Om du vill publicera en IoT Edge-modul i Azure Marketplace, som du behöver uppfylla följande krav:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Åtkomst till den [Cloud Partner Portal](https://cloudpartner.azure.com/). Mer information finns i [publiceringsguide för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Avtal för den [villkor för Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Vara värd för din IoT Edge-modulen teknisk tillgång i ett Azure Container Registry.  Mer information finns i [hur du förbereder din IoT-Edge modulen teknisk tillgång](./cpp-create-technical-assets.md)
- Har metadata redo att använda din IoT Edge-modulen. Till exempel förbereda följande resurser:
    - En rubrik
    - En beskrivning (i HTML-format)
    - En logotyp (PNG-format och fast bildstorleken inklusive 40x40px, 90x90px, 115x115px, 255x115px)
    - En period om och sekretesspolicyn princip
    - En standardkonfiguration för modulen som innehåller: vägar, twin önskade egenskaper, createOptions och miljövariabler.
    - Modul-dokumentation
    - Supportkontakter


## <a name="next-steps"></a>Nästa steg

När du har [förberett din IoT Edge-modulen teknisk tillgång](./cpp-create-technical-assets.md), kommer du vara redo att [skapa erbjudandet IoT Edge-modulen](./cpp-create-offer.md). 
