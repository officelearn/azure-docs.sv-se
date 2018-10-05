---
title: IoT Edge-modulen certifikatutfärdare | Microsoft Docs
description: Certifiera en IoT Edge-modul för Marketplace.
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
ms.openlocfilehash: c11995a3db1987de7969ee476a1c69a1025be5fd
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811293"
---
# <a name="the-iot-edge-module-certification-process"></a>Certifieringsprocessen IoT Edge-modul

Den här artikeln beskriver stegen och kraven för att certifiera en IoT Edge-modul för publicering på Azure Marketplace. 

>[!Note]
>Det här är ett tillfälligt dokument. IoT Edge-modulen Marketplace håller på att skapas parallellt och i den här artikeln kommer att ersättas av offentliga dokumentationen.

## <a name="understanding-an-iot-edge-module"></a>Förstå en IoT Edge-modul

Modulen terminologi:

-   En **modulen bild** är ett paket som innehåller den programvara som definierar en modul.

-   En **modulinstans** är den specifika beräkningsenhet körs modulen avbildningen på en IoT Edge-enhet. Modul-instansen har startats med IoT Edge-körningen.

Moduler kan också vara modulen IoT SDK, som använder följande termer:

-   En **modulen identitet** är en del av informationen (inklusive säkerhetsreferenser) lagras i IoT Hub som är associerat med varje modulinstans.

-   En **modultvilling** är ett JSON-dokument som lagras i IoT Hub, som innehåller information om tillstånd för en modulinstans, inklusive metadata, konfigurationer och villkor.

-   **SDK: er** används för att utveckla anpassade moduler på flera språk, till exempel: C\#, C, Python, Java och Node.JS.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>Onboarding-processen för en IoT Edge-modul

Följande skärmdump visar processen för en IoT Edge-modul blir offentliga i Azure Marketplace.

![Certifieringsprocessen](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Information om Onboarding-steg

-   **Steg 1** -Partners skicka sina erbjudanden med typ av erbjudande **IoT Edge-modul** i verktyget partnerportalen i molnet av Azure Marketplace-teamet. Du måste vara en officiell MS-partner att komma åt verktyget partnerportalen i molnet. Mer information finns i den [utgivarens guiden](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **Steg 2** – The Marketplace körs automatiskt skydd mot virus och skadlig kontrollerar. IoT-teamet kan köra testerna anpassade automatiska inmatning under det här steget.

-   **Steg 3** -modulen är offentlig. Det visas i marketplace och behållaren kan anonymt hämtas från en URL. Till exempel *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>IoT Edge-modulen certifieringskriterierna

Nedan visas de viktigaste krav som måste uppfyllas för en IoT Edge-modul certifierade och publiceras på Azure Marketplace.

>[!Note]
>Dessa krav kan ändras. Du kommer att meddelas i förväg och vara får tid att uppdatera dina behållare så att de kan möta de uppdaterade kraven.

### <a name="technical-requirements"></a>Tekniska krav

**Vara en IoT Edge-modul**

-   Endast docker-kompatibla behållare stöds för närvarande som IoT Edge-moduler. Modulen måste köras på [Moby](https://mobyproject.org/). 

    >[!Note]
    >Docker-behållare kommer troligen att fungera med Moby eftersom Moby är det underliggande projektet med öppen källkod för Docker.

-   Partnern måste ange följande standardinställningar: 

    -   En standard **taggen** (som måste anges.

    -   En standard **createOptions** (som kan vara tom.)

    -   Om du använder SDK för IoT-modulen, en standard **twin** (som kan vara tom.)

    -   Om du använder SDK för IoT-modulen, en standard **vägar** (som kan vara tom.)

**Stöd för enhetsplattform**

-   Plattformar som är **allmänt tillgänglig** på IoT Edge nivå 1 (mätt i [support för Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)) måste stödjas. Till exempel innebär detta för närvarande stöd för följande operativsystem och arkitektur kombinationer:

    -   Ubuntu Server 18.04 för x64

    -   Ubuntu Server 16.04 för x64

    -   Raspbian-stretch för arm32 (armhf)

**Enheten Måttsättning**

-   Alla enheter med motsvarande dimensioner (processor/RAM-minne/Storage/GPU/och osv) av en Raspberry Pi med eller större kan vara en IoT Edge-enhet. Om en modul fungerar bara i specifik dimension begränsningar, måste dessa begränsningar anges i beskrivningen för modulen.

**Standardinställningar**

-   En modul ska inledas med standardparametrarna direkt ur lådan för varje plattform som stöds (OS + arkitektur).

-   Konfigurationsinställningar ska vara tydligt dokumenterade (taggar, miljövariabler, twin, vägar.) Som en del av listan, kan partner definiera en beskrivning för sina modulen som stöder grundläggande HTML-kod eller pekar på en extern webbplats.

**Versionshantering**

-   Kunder måste kunna välja om de vill att automatiskt uppdatera en modul som kommer från marketplace eller om de vill använda en exakt version de har testat. Marketplace-moduler måste följa samma mönster för versionshantering som IoT Edge-körningen. Exempel:

    -   Löpande taggar, till exempel ”1.0”, kan uppdateras.

    -   Lägre version taggar, till exempel ”1.3.24” kan inte uppdateras.

**Telemetri**

-   Moduler med SDK för IoT-modul måste ange den unika modulen identifierare som tilldelats av marketplace för telemetri. På så sätt kan du i Azure Marketplace och identifiera antalet modulen instanser som körs. Den här unika identifierare finns behållarnamn som tilldelats av marketplace på inmatning. Med metoderna i de följande SDK: er anger följande identifierare:
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   För moduler som inte använder SDK för IoT-modul, är mindre exakt insikter tillgängliga via partnerportalen i molnet. Till exempel antalet nedladdningar.

**Säkerhet**

-   Behållare bör ha minst privilegierad åtkomst till värden som möjligt. ”Privilegierade” behållare ska vara mycket ovanligt.

-   Partners måste hålla sina modulen skydda som en del av det stöd som de tillhandahåller.

**Uppdateringar**

-   Partners måste hålla sina modulen uppdaterade och funktionella. De kommer att meddelas i förväg om det finns ändringar de senaste planerat för IoT Edge-körningen.

**Modulen IoT-SDK**

-   Inklusive IoT-SDK-modulen är inte ett krav för certifiering.
    Inklusive IoT-modulen SDK kan dock ge en bättre användarupplevelse. Till exempel stöd för routning, skicka meddelanden till molnet, och så vidare. SDK för IoT-modulen krävs för att hämta telemetri för antalet modulen instanser som körs.

**Högst subjektiv krav**

-   Måste uppfylla minst ett verkliga IoT Edge-användningsfall. En WordPress-behållare får inte ska publiceras, såvida inte en kund är villig att använda den från IoT Edge.

**Juridiska krav (från AMP princip)**

-   Modulen måste vara kompatibel med Microsoft Azure Marketplace-avtal och principer. Mer information finns i Publiceringsavtalet för anslutna och [deltagande princip](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

-   Förutom Azure Marketplace-avtal och principer, kan det finnas ytterligare juridiska krav för en IoT Edge-modul erbjudandet. Det här är för närvarande under granskning.

-   Modulen måste ha en *användningsvillkoren* och en *sekretesspolicy*.

-   Modulen bör även ha en från tredje part meddelande om modulen använder någon tredje part.

**Stöd för krav (från AMP princip)**

-   Partner ansvarar för att stödja sina IoT Edge-moduler. De säkerställer att supportalternativ som anges i beskrivningen IoT Edge-modul finnas tillgänglig och att minst en supportalternativ alltid är tillgänglig. (Finns i avsnitt 4 i Publiceringsavtalet för AMP för mer information.)

**Kategorisering**

-   Alla IoT Edge-moduler visas under kategorin **sakernas Internet \>IoT Edge-modul**. Det är upp till partner att välja den bästa underkategorin för sin produkt.
