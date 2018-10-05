---
title: IoT Edge-modulen Marketplace vanliga frågor och svar | Microsoft Docs
description: IoT Edge-modulen Marketplace vanliga frågor och svar.
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811393"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>Vanliga och frågor svar om IoT Edge-modulen Marketplace


## <a name="what-is-the-edge-module-marketplace"></a>Vad är Microsoft Edge-modulen Marketplace?


IoT Edge-modulen Marketplace är en lista över *certifierade* färdiga Edge-moduler som är *identifierbart* via Azure Marketplace.

![IoT Edge-moduler](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Där Edge-moduler som ska visas? 


I den [Azure portal marketplace](https://ms.portal.azure.com/) (autentiseras experience), under kategorin Internet of Things som taggats som ”IoT Edge-modul”.

I den [webbplatsen för Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1) (anonym experience) under kategorin Internet of Things som taggats som ”IoT Edge-modul”.

## <a name="is-it-open-to-partners"></a>Är den öppen för partner?


Inte ännu. För närvarande publiceras endast moduler som har skapats av Microsoft i IoT Edge-avsnittet i marketplace. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>Varför bör partner publicera sina IoT Edge-moduler?


-   Att öka deras produktsynligheten genom att lägga till den här kanalen på marknaden och upp sina lösningar.

-   Att hämta fler leads. Som en del av Azure Marketplace, kan de få information om vem är intresserad av sin lösning.

-   Ska vara bland de första att utnyttja mer intäkter-funktioner.

## <a name="what-is-the-onboarding-process"></a>Vad är onboarding-processen?


Även om inte ännu öppna görs onboarding-processen via Azure Marketplace. Detaljerade riktlinjer finns i den [publiceringsguide för Azure Marketplace och AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). Referera till transact listtyp för behållare. 

Partner börjar måste du blir utgivare på Azure Marketplace. Och sedan de kommer att kunna publicera sina Edge-moduler via den [Cloud Partner Portal](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md).

## <a name="are-there-any-monetization-capabilities"></a>Finns det några intäkter funktioner?


Inte direkt för tillfället. Vårt första mål är att öppna en marknadsplats med *kostnadsfria* eller *använda din egen licens* Edge-moduler. Vi ska lägga till mer intäkter-funktioner, till exempel en förbrukningsmodell för fakturering.

## <a name="what-is-bring-your-own-license-byol"></a>Vad är bring-your-own-license (BYOL)?


Den officiella definitionen i den [Deltagandepolicyer för Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) är:

- *Kunder erhåller utanför Azure Marketplace rätten att öppna eller använda erbjudandet och debiteras inte Azure Marketplace-avgifter för användning av erbjudandet på Azure Marketplace.*

Det är upp till partners licensierar sin programvara och samla in intäkter för den.

## <a name="can-partners-publish-a-freemium-module"></a>Kan partner publicera en ”freemium”-modul?


Ja betraktas freemium-moduler som är moduler som är tillgängliga kostnadsfritt men med vissa begränsningar, som andra publikationer.

## <a name="is-intellectual-property-protected"></a>Skyddas immateriell egendom?


En Edge-modul är en kompatibel Docker-behållare. Det är upp till partners för att skydda sina immateriell egendom (IP) paketeras i distribuerade behållare.

## <a name="where-will-the-modules-be-hosted"></a>Där kommer moduler finns?


IoT Edge-moduler kommer att finnas i ett Microsoft-ägda behållarregister som anonymt fråga-kan, t.ex. Docker Hub.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Vad är integration-avtal mellan Azure IoT-verktyg och Azure Marketplace?

Vi ska skapa närmare integration mellan Azure IoT-verktyg och Azure Marketplace. Exempel på vad vi har i åtanke är att aktivera vissa surfning av IoT Edge-modulen marketplace direkt från IoT Hub-portalen eller direkt från Visual Studio Code.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>Vilka operativsystem eller arkitekturen bör min behållare stöd för?

IoT Edge-moduler måste ha stöd för samma OS / arkitektur matrisen som IoT Edge i produktion. Den här listan underhålls i [Support för Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support). En modul måste till exempel för närvarande stöd för Linux x64- och Linux ARM32.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>Finns det andra certifiering villkor känna till?

Vi arbetar fortfarande med den exakta uppsättningen restriktioner för certifiering. Vår riktlinjerna är:

-   Uppgradera kvalitet över kvantitet.

-   IoT Edge-specifika behållare (inte slumpmässiga som).

-   Produktion i företagsklass.

-   1-distribution (minst med en standard konfigurationen värdena anges).

## <a name="any-other-questions"></a>Andra frågor?


Kontakta [Azure IoT Edge-registreringen av](mailto:azureiotedgeonboarding@service.microsoft.com).
