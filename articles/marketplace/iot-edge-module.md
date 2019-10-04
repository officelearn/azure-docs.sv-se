---
title: Azure IoT Edge moduler
description: IoT Edge modul erbjuder Azure Marketplace för app-och tjänst utgivare.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, IoT Edge module offer
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/22/2018
ms.author: pabutler
ms.openlocfilehash: 54369e0f2c943c146d186605833198253b960022
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949528"
---
# <a name="iot-edge-modules"></a>IoT Edge-moduler

[Azure IoT Edges](https://azure.microsoft.com/services/iot-edge/) plattformen backas upp av Azure-molnet.  Den här plattformen gör det möjligt för användare att distribuera moln arbets belastningar för att köra direkt på IoT-enheter.  En IoT Edge-modul kan köra offline-arbetsbelastningar och utföra data analys lokalt. Med den här typen av erbjudande kan du spara bandbredd, skydda lokala och känsliga data och erbjuda svars tid med låg latens.  Nu har du möjlighet att dra nytta av dessa förbyggda arbets belastningar. Fram till nu var bara en fåtal av lösningar från första part från Microsoft tillgängliga.  Du var tvungen att investera tiden och resurserna för att skapa dina egna anpassade IoT-lösningar.

Genom att introducera [IoT Edge modulerna på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)har vi nu ett enda mål för utgivare att exponera och sälja sina lösningar till IoT-publiken. IoT-utvecklare kan i slut ända hitta och köpa funktioner för att påskynda utvecklingen av lösningen.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Viktiga fördelar med IoT Edge moduler på Azure Marketplace:

| **För utgivare**    | **För kunder (IoT-utvecklare)**  |
| :------------------- | :-------------------|
| Uppnå miljon tals utvecklare som vill bygga och distribuera IoT Edge lösningar.  | Skapa en IoT Edge-lösning med förtroende för att använda säkra och testade komponenter. |
| Publicera en gång och kör på alla IoT Edge maskin vara som har stöd för behållare. | Minska tiden till marknaden genom att söka efter och distribuera 1 och tredje parts IoT Edge moduler för speciella behov. |
| Betala med flexibla fakturerings alternativ <ul> <li> Gratis och ta med din egen licens (BYOL). </li> </ul> | Gör inköp med ditt val av fakturerings modeller. <ul> <li> Gratis och ta med din egen licens (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Vad är en IoT Edge modul?

Med Azure IoT Edge kan du distribuera och hantera affärs logik på kanten i form av moduler. Azure IoT Edge moduler är de minsta beräknings enheter som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösnings-specifika kod. Mer information om IoT Edge moduler finns i [förstå Azure IoT Edge moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Vad är skillnaden mellan en behållar erbjudande typ och en IoT Edge moduls erbjudande typ?**

Typen IoT Edge module-erbjudande är en bestämd typ av behållare som körs på en IoT Edge enhet. Den innehåller standard konfigurations inställningar som ska köras i IoT Edge-kontexten, och kan också använda SDK för IoT Edge modul för att integreras med IoT Edge Runtime.

## <a name="publishing-your-iot-edge-module"></a>Publicera IoT Edge-modulen

**Välja rätt butik**

IoT Edge moduler publiceras bara på Azure Marketplace, AppSource gäller inte.  Mer information om skillnaderna och mål grupp för butiker finns i [fastställa publicerings alternativet för din lösning](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Fakturerings alternativ**

Marketplace har för närvarande stöd för **kostnads fria** och **använder dina egna licens alternativ för BYOL-** fakturering för IoT Edge moduler.
 
**Publicerings alternativ**

I samtliga fall bör IoT Edge moduler välja alternativet för **Transact** -publicering.  Se [Välj ett publicerings alternativ](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) för mer information om publicerings alternativ.  

## <a name="eligibility-criteria"></a>Kvalifikationsvillkor

Alla villkor för Microsoft Azure Marketplace-avtal och-principer gäller för IoT Edge modul-erbjudanden.  Dessutom finns det krav och tekniska krav för IoT Edge moduler.  

**Krav**

Om du vill publicera en IoT Edge-modul på Azure Marketplace måste du uppfylla följande krav:

- Åtkomst till Cloud Partner Portal (CPP). Mer information finns i [publicerings guiden för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Placera din IoT Edge-modul i en Azure Container Registry. 
- Låt din IoT Edge modul metadata vara klara som (ej uttömmande lista): 
    - En rubrik
    - En beskrivning (i HTML-format)
    - En logo typ bild (PNG-format och fasta bild storlekar, inklusive 40x40px, 90x90px, 115x115px, 255x115px)
    - Ett användnings villkor och en sekretess policy
    - Standardkonfiguration av modul (Route, dubbla önskade egenskaper, createOptions, miljövariabler)
    - Dokumentation
    - Support kontakter

**Tekniska krav**

De primära tekniska kraven för en IoT Edge modul, för att det ska bli certifierat och publicerat på Azure Marketplace, beskrivs i [förbereda din IoT Edge-modul tekniska till gångar](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Dokumentation och resurser

[Skapa en IoT Edge modul-erbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – stegen för att publicera ett nytt IoT Edge modul-erbjudande med moln publicerings portalen.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det,

- [Registrera dig](https://azuremarketplace.microsoft.com/sell) på Marketplace.

Om du har registrerat och skapar ett nytt erbjudande eller arbetar på ett befintligt,

- Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/) för att skapa eller slutföra ditt erbjudande.
- Mer information om hur du publicerar ett erbjudande för IoT Edge-modulen finns i avsnittet om [publicerings översikt för IoT Edge-modulen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) .
