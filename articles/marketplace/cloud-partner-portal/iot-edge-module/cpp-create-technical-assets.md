---
title: Skapa tekniska resurser för Azure IoT Edge-modul | Azure Marketplace
description: Skapa de tekniska tillgångarna för en IoT Edge-modul.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: dsindona
ms.openlocfilehash: 304ea31f2a6f9820226a6ad0db8bfc803e6fdb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285222"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Förbereda tekniska resurser för IoT Edge-modulen

I den här artikeln beskrivs de krav som dina tekniska resurser för IoT Edge-modulen måste uppfylla innan de publiceras på Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Förstå IoT Edge-moduler och komma igång

En IoT Edge-modul är en Docker-kompatibel behållare som är gjord för att köras på en IoT Edge-enhet.

- Mer information om IoT Edge-moduler finns i [Förstå Azure IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- För att komma igång med utvecklingen av din IoT Edge-modul, se [krav och verktyg för att utveckla IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Tekniska krav

Följande tekniska krav måste uppfyllas för att din IoT Edge-modul ska certifieras och publiceras på Azure Marketplace.

### <a name="platform-support"></a>Plattformssupport

Din IoT Edge-modul måste ha stöd för något av följande plattformsalternativ.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Tier 1-plattformar som stöds av IoT Edge

Stöd för alla Nivå 1-plattformar som stöds av IoT Edge (som registrerats i [Azure IoT Edge-stöd](https://docs.microsoft.com/azure/iot-edge/support)). Vi rekommenderar det här alternativet eftersom det ger en bättre kundupplevelse. Moduler som uppfyller dessa kriterier kommer att visas upp. En modul som använder det här plattformsalternativet måste:

- Ange `latest` en tagg och en versionstagg (till `1.0.1`exempel) som är manifesttaggar byggda med GitHub [manifest-verktyget](https://github.com/estesp/manifest-tool).
- Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att lägga till en länk till [kompatibla IoT Edge-certifierade enheter](https://aka.ms/iot-edge-certified). Den här länken `https://aka.ms/iot-edge-certified`matchas till , en webbplats där kunderna kan bläddra eller söka efter certifierade enheter. Den här webbplatsen kallas även [Azure IoT Edge](https://catalog.azureiotsolutions.com/) Certified-enhetskatalogen.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>En delmängd av Tier 1-plattformar som stöds av IoT Edge
  
Stöd för en delmängd (minst en) av nivå 1-plattformar som stöds av IoT Edge (som registrerats i [Azure IoT Edge-stöd](https://docs.microsoft.com/azure/iot-edge/support)). En modul som använder det här plattformsalternativet måste:

- Ange `latest` en tagg och en versionstagg (till exempel `1.0.1`) som är manifesttaggar som skapats med GitHub-manifestverktyget om mer än en plattform stöds. [manifest-tool](https://github.com/estesp/manifest-tool) Manifesttaggar är valfria endast när en plattform stöds.
- Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att tillhandahålla en länk till minst en IoT Edge-enhet i [Azure IoT Edge](https://catalog.azureiotsolutions.com/) Certified-enhetskatalogen.

### <a name="device-dimensions"></a>Enhetens dimensioner

IoT Edge-moduldimensioner (CPU/RAM/Storage/GPU/etc.) på riktade IoT Edge-enheter måste uppfylla följande krav:

- Modulen måste **fungera med minst en IoT Edge-certifierad** enhet i Azure [IoT Edge](https://catalog.azureiotsolutions.com/) Certified-enhetskatalogen.
- **Kraven på minsta maskinvara** måste dokumenteras som det sista stycket i beskrivningen av erbjudandet (under fliken [Marketplace).](./cpp-marketplace-tab.md) Du kan också ange de rekommenderade maskinvarukraven om de skiljer sig avsevärt. Lägg till exempel till följande avsnitt i slutet av erbjudandebeskrivningen:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguration

Den innehåller också standardkonfigurationsinställningar för att göra distributionen till en IoT Edge-enhet så rakt fram som möjligt. Behållaren kan också innehålla IoT Edge Module SDK för att aktivera kommunikation med edgeHub och IoT Hub.

#### <a name="default-configuration"></a>Standardkonfiguration

IoT Edge-moduler måste kunna börja med standardinställningarna i [fliken SKU på Cloud Partner-portalen](./cpp-skus-tab.md). Följande standardinställningar är tillgängliga:

- **Standardvägar**
- Standardegenskaper **för dubbla önskade egenskaper**
- **Standardmiljövariabler**
- Standard **createOptions**

I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds server) lägger du till en parameter som standardvärde. Detta värde är inneslutet inom parentes och i versaler. I det här exemplet ställer du in följande standardmiljövariabel:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentation för konfiguration

Alla konfigurationsinställningar för en IoT Edge-modul måste vara tydligt dokumenterade (hur du använder dess vägar, dubbla önskade egenskaper, miljövariabler, createOptions och så vidare.) Ange en länk till din dokumentation, eller dokumentationen måste vara en del av ditt erbjudande / sku beskrivning.

### <a name="tags-and-versioning"></a>Taggar och versionshantering

Kunderna måste enkelt kunna distribuera en modul och automatiskt få uppdateringar från marknadsplatsen (i ett utvecklarscenario.) De måste också kunna använda och frysa en exakt version som de har testat (i ett produktionsscenario.)

För att uppfylla dessa kunders förväntningar och publiceras på marknaden måste IoT Edge-moduler uppfylla följande krav:

- Inkludera en `latest` manifesttagg, som pekar den senaste versionen på alla plattformar som stöds.
- Versionstaggar måste vara av formuläret X.Y.Z, där X, Y och Z är heltal.
- Inkludera en "version"-tagg, som `1.0.1`pekar på en specifik version på alla plattformar som stöds.
- Uppdatera inte "version"-taggar, `1.0.1`som , eftersom de måste vara oföränderliga.

>[!Note]
>Du kan också ta med "rullande version"-taggar, till exempel `2.0` och `1.0`. Detta stöder att underhålla flera huvudversioner parallellt.

### <a name="telemetry"></a>Telemetri

Moduler som använder IoT-modul SDK måste `PublisherId.OfferId.SkuId` ange den unika modulidentifieraren till för telemetriändamål. En unik identifierare gör det möjligt för Azure Marketplace att identifiera antalet modulinstanser som körs.

 Använd följande metoder från IoT-modul-SDK:er för att ställa in identifieraren: `ProductInfo`

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

För moduler som inte använder IoT-modul SDK är mindre exakta insikter tillgängliga via Cloud Partner Portal, till exempel antalet nedladdningar.

### <a name="security"></a>Säkerhet

IoT Edge-moduler måste be om minst privilegierad åtkomst till värden som möjligt. [Privilegierade moduler](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) bör undvikas.

### <a name="module-iot-sdk"></a>Modul IoT SDK

Att inkludera IoT-modul SDK är inte en förutsättning för certifiering. Men inklusive IoT-modul SDK kan ge en bättre användarupplevelse. Till exempel för att stödja routning eller skicka meddelanden till molnet.

IoT-modul SDK krävs för att hämta telemetridata om antalet modulinstanser som körs.


## <a name="recertification-process"></a>Omcertifieringsprocess

<!-- Add legal time windows-->
Partner får ett meddelande när det sker en brytningsändring som påverkar deras moduler, till exempel:

- Nivå 1 os/bågstödmatris som stöds av IoT Edge
- IoT-modul SDK
- IoT Edge Runtime
- Riktlinjer för certifiering av IoT Edge-modul

Partner måste uppdatera sina moduler och certifiera om dem med verktyget Cloud Partner Portal.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Vara värd för din IoT Edge-modul i ett Azure-behållarregister

Om du vill ladda upp IoT Edge-modulen till Cloud Partner Portal måste du först vara värd för den i ett [AZURE-behållarregister](https://azure.microsoft.com/services/container-registry/) (ACR). Modulen måste innehålla alla taggar som du vill publicera, inklusive de bildtaggar som refereras av en manifesttagg.


## <a name="next-steps"></a>Nästa steg

- [Skapa ditt IoT Edge-modulerbjudande](./cpp-create-offer.md)
