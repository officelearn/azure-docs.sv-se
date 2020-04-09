---
title: Förutsättningar för Azure IoT Edge-modul | Azure Marketplace
description: Förutsättningar för publicering av en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9a54b31725d14a3ff54bd2d945cd69f4b8769b87
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983185"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Förutsättningar för publicering av IoT Edge-modul

>[!Important]
>Från och med den 13 april 2020 börjar vi flytta hanteringen av din IoT Edge-modul till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs förutsättningarna för att publicera ett IoT Edge-modulerbjudande.  Om du inte redan har gjort det läser du [publiceringsguiden för IoT Edge-moduler](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Publiceringsförutsättning

Om du vill publicera en IoT Edge-modul på Azure Marketplace måste du uppfylla följande förutsättningar:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Tillgång till [molnpartnerportalen](https://cloudpartner.azure.com/). Mer information finns i [Publiceringsguiden för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Avtal till Villkoren för [Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Vara värd för den tekniska tillgången för IoT Edge-modul i ett Azure-behållarregister.  Mer information finns i [hur du förbereder den tekniska tillgången för IoT Edge-modul](./cpp-create-technical-assets.md)
- Ha metadata för IoT Edge-modulen redo att användas. Förbered till exempel följande tillgångar:
    - En titel
    - En beskrivning (i HTML-format)
    - En logotypbild (PNG-format och fasta bildstorlekar inklusive 40x40px, 90x90px, 115x115px, 255x115px)
    - En användnings- och sekretesspolicy
    - En standardmodulkonfiguration som innehåller: vägar, dubbla önskade egenskaper, createOptions och miljövariabler.
    - Dokumentation för modul
    - Supportkontakter


## <a name="next-steps"></a>Nästa steg

När du har förberett din tekniska tillgång för [IoT Edge-modulen](./cpp-create-technical-assets.md)är du redo att skapa erbjudandet om [din IoT Edge-modul.](./cpp-create-offer.md) 
