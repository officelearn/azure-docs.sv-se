---
title: Förutsättningar för Azure IoT Edge-modulen | Azure Marketplace
description: Krav för att publicera en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142369"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Förutsättningar för att publicera IoT Edge modul

>[!Important]
>Från och med 13 april 2020 kommer vi att börja flytta hantering av IoT Edge modul-erbjudanden till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett IoT Edge modul-erbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs kraven för att publicera ett IoT Edge-modul erbjudande.  Om du inte redan har gjort det går du igenom [publicerings guiden för IoT Edge-moduler](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publicerings krav

Om du vill publicera en IoT Edge-modul på Azure Marketplace måste du uppfylla följande krav:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Åtkomst till [Cloud Partner Portal](https://cloudpartner.azure.com/). Mer information finns i [publicerings guiden för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Avtal till [Azure Marketplace-villkoren](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Vara värd för din IoT Edge-modulens tekniska till gång i en Azure Container Registry.  Mer information finns i [så här förbereder du din tekniska till gång till IoT Edge-modul](./cpp-create-technical-assets.md)
- Ha din IoT Edge modul metadata redo att användas. Förbered till exempel följande till gångar:
    - En rubrik
    - En beskrivning (i HTML-format)
    - En logo typ bild (PNG-format och fasta bild storlekar, inklusive 40x40px, 90x90px, 115x115px, 255x115px)
    - Ett användnings villkor och en sekretess policy
    - En standardmoduls konfiguration som innehåller: vägar, dubbla önskade egenskaper, createOptions och miljövariabler.
    - Modul-dokumentation
    - Supportkontakter


## <a name="next-steps"></a>Nästa steg

När du har för [berett din IoT Edge-modul teknisk till gång](./cpp-create-technical-assets.md), är du redo att [skapa ditt IoT Edge-modul erbjudande](./cpp-create-offer.md). 
