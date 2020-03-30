---
title: Azure IoT Edge-moduler
description: IoT Edge-modulerbjudandet på Azure Marketplace för app- och tjänstutgivare.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: dsindona
ms.openlocfilehash: aadbf33914f919e393a5ec88cf6fc0a6103911b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286157"
---
# <a name="iot-edge-modules"></a>IoT Edge-moduler

[Azure IoT Edge-plattformen](https://azure.microsoft.com/services/iot-edge/) backas upp av Azure Cloud.  Den här plattformen gör det möjligt för användare att distribuera molnarbetsbelastningar för att köra direkt på IoT-enheter.  En IoT Edge-modul kan köra offlinearbetsbelastningar och göra dataanalys lokalt. Den här erbjudandetypen hjälper till att spara bandbredd, skydda lokala och känsliga data och erbjuder svarstid med låg latens.  Du har nu möjlighet att dra nytta av dessa färdiga arbetsbelastningar. Hittills har endast en handfull förstapartslösningar från Microsoft varit tillgängliga.  Du var tvungen att investera tid och resurser för att bygga dina egna anpassade IoT-lösningar.

Genom att introducera [IoT Edge-modulerna på Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)har vi nu en enda destination där utgivare kan exponera och sälja sina lösningar till IoT-publiken. IoT-utvecklare kan i slutändan hitta och köpa funktioner för att påskynda sin lösningsutveckling.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Viktiga fördelar med IoT Edge-moduler på Azure Marketplace:

| **För utgivare**    | **För kunder (IoT-utvecklare)**  |
| :------------------- | :-------------------|
| Nå miljontals utvecklare som vill bygga och distribuera IoT Edge-lösningar.  | Komponera en IoT Edge-lösning med förtroende för att använda säkra och testade komponenter. |
| Publicera en gång och kör över alla IoT Edge-maskinvara som stöder behållare. | Minska tiden till marknaden genom att hitta och distribuera IoT Edge-moduler från första och tredje part för specifika behov. |
| Tjäna pengar på flexibla faktureringsalternativ <ul> <li> Gratis och ta med egen licens (BYOL). </li> </ul> | Gör inköp med ditt val av faktureringsmodeller. <ul> <li> Gratis och ta med egen licens (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Vad är en IoT Edge-modul?

Med Azure IoT Edge kan du distribuera och hantera affärslogik på gränsen i form av moduler. Azure IoT Edge-moduler är de minsta beräkningsenheterna som hanteras av IoT Edge och kan innehålla Microsoft-tjänster (till exempel Azure Stream Analytics), tjänster från tredje part eller din egen lösningsspecifika kod. Mer information om IoT Edge-moduler finns i [Förstå Azure IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Vad är skillnaden mellan en container erbjudandetyp och en IoT Edge-modul erbjudandetyp?**

IoT Edge-modulens erbjudandetyp är en viss typ av behållare som körs på en IoT Edge-enhet. Den levereras med standardkonfigurationsinställningar för att köras i IoT Edge-kontexten och använder eventuellt IoT Edge-modulen SDK för att integreras med IoT Edge-körningen.

## <a name="publishing-your-iot-edge-module"></a>Publicera din IoT Edge-modul

**Välja rätt skyltfönster**

IoT Edge-moduler publiceras endast på Azure Marketplace, AppSource gäller inte.  Mer information om skillnader och målgrupp över skyltfönster finns [i bestämma publiceringsalternativet för din lösning](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Faktureringsalternativ**

Marknaden stöder för närvarande **gratis** **och ta med din egen licens (BYOL)** faktureringsalternativ för IoT Edge moduler.
 
**Publiceringsalternativ**

I alla fall bör IoT Edge-moduler välja alternativet **Transact** publishing.  Mer information om publiceringsalternativ finns i [välja ett publiceringsalternativ.](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)  

## <a name="eligibility-criteria"></a>Behörighetskriterier

Alla villkor i Microsoft Azure Marketplace-avtal och principer gäller för IoT Edge-modulerbjudanden.  Dessutom finns det förutsättningar och tekniska krav för IoT Edge-moduler.  

**Förutsättningar**

Om du vill publicera en IoT Edge-modul på Azure Marketplace måste du uppfylla följande förutsättningar:

- Tillgång till Cloud Partner Portal (CPP). Mer information finns i [Publiceringsguiden för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Vara värd för din IoT Edge-modul i ett Azure-behållarregister. 
- Ha metadata för IoT Edge-modul redo, till exempel (icke uttömmande lista): 
    - En titel
    - En beskrivning (i HTML-format)
    - En logotypbild (PNG-format och fasta bildstorlekar inklusive 40x40px, 90x90px, 115x115px, 255x115px)
    - En användnings- och sekretesspolicy
    - Standardmodulkonfiguration (väg, dubbla önskade egenskaper, createOptions, miljövariabler)
    - Dokumentation
    - Supportkontakter

**Tekniska krav**

De primära tekniska kraven för en IoT Edge-modul för att den ska bli certifierad och publicerad på Azure Marketplace beskrivs i tekniska resurser för [förbereda din IoT Edge-modul](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets).  

## <a name="documentation-and-resources"></a>Dokumentation och resurser

[Skapa ett IoT Edge-modulerbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) – Stegen för att publicera ett nytt IoT Edge-modulerbjudande med Cloud Publishing Portal.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det,

- [Registrera dig](https://azuremarketplace.microsoft.com/sell) på marknaden.

Om du är registrerad och skapar ett nytt erbjudande eller arbetar med ett befintligt,

- Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/) för att skapa eller slutföra ditt erbjudande.
- Se [IoT Edge-modulen erbjuder publiceringsöversikt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) för information om hur du publicerar ett IoT Edge-modulerbjudande.
