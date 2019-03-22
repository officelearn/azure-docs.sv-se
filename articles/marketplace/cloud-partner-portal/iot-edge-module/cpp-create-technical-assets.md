---
title: Skapa Azure IoT Edge-modul tekniska resurser | Microsoft Docs
description: Skapa tekniska resurser för en IoT Edge-modul.
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
ms.openlocfilehash: 2ed4826eb47ab2fb13d312860475f9ec9b323bf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884162"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Förbereda din IoT Edge-modul tekniska resurser

Den här artikeln beskrivs de krav som din IoT Edge-modulen tekniska resurser måste uppfylla innan de kan publiceras på Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Förstå IoT Edge-moduler och komma igång

En IoT Edge-modul är en Docker-kompatibla behållare som har gjorts att köra på en IoT Edge-enhet.

- Läs mer om IoT Edge-moduler i [förstå Azure IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Kom igång med utvecklingen av din IoT Edge-modul, se [krav och verktyg för att utveckla IoT Edge-moduler](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Tekniska krav

Följande tekniska krav måste uppfyllas för att din IoT Edge-modul certifierade och publiceras på Azure Marketplace.

### <a name="platform-support"></a>Plattformssupport

IoT Edge-modul måste ha stöd för något av följande alternativ för plattformen.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Nivå 1-plattformar som stöds av IoT Edge

Stöd för alla nivå 1-plattformar som stöds av IoT Edge (mätt i [support för Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). Vi rekommenderar det här alternativet eftersom det ger en bättre kundupplevelse. Moduler som uppfyller det här villkoret kommer att visas. En modul med det här alternativet om plattform måste:

- Ange en `latest` tagg och en tagg för version (till exempel `1.0.1`) som är manifestet taggar som skapats med GitHub [manifest-verktyget](https://github.com/estesp/manifest-tool).
- Använd den [fliken Marketplace](./cpp-marketplace-tab.md) att lägga till en länk till [kompatibla IoT Edge certifierade enheter](https://aka.ms/iot-edge-certified). Den här länken som motsvarar `https://aka.ms/iot-edge-certified`, en webbplats där kunder kan bläddra eller Sök efter certifierade enheter. Den här webbplatsen är även känd som den [Azure IoT Edge-certifierade](https://catalog.azureiotsolutions.com/) enhetskatalog.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>En delmängd av nivå 1-plattformar som stöds av IoT Edge
  
Stöd för en delmängd av nivå 1-plattformar som stöds av IoT Edge (minst) (mätt i [support för Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/support)). En modul med det här alternativet om plattform måste:

- Ange en `latest` tagg och en tagg för version (till exempel `1.0.1`) som är manifestet taggar som skapats med GitHub [manifest-verktyget](https://github.com/estesp/manifest-tool) om det finns stöd för flera plattformar. Manifest taggar är valfria endast när en plattformen stöds.
- Använd den [Marketplace fliken](./cpp-marketplace-tab.md) att tillhandahålla en länk till minst en IoT Edge-enhet i den [Azure IoT Edge-certifierade](https://catalog.azureiotsolutions.com/) enhetskatalog.

### <a name="device-dimensions"></a>Enheten dimensioner

IoT Edge-modulen dimensioner (processor/RAM-minne/Storage/GPU/osv) på riktade IoT Edge-enheter måste uppfylla följande krav:

- Modulen måste **fungerar med minst en IoT Edge-certifierade** enhet i den [Azure IoT Edge-certifierade](https://catalog.azureiotsolutions.com/) enhetskatalog.
- Den **Minsta maskinvarukrav** måste dokumenteras som det sista stycket i beskrivningen av erbjudandet (under den [Marketplace fliken](./cpp-marketplace-tab.md)). Du kan också kan du också ange de rekommenderade maskinvarukraven om de skiljer sig avsevärt. Till exempel lägga till följande avsnitt i slutet av ditt erbjudande-beskrivning:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguration

Den innehåller också standardkonfigurationsinställningarna för att kontrollera distributionen till en IoT Edge-enhet som schemabaserad som möjligt. Behållaren också innefatta IoT Edge-modulen SDK för att aktivera kommunikation med edgeHub och IoT Hub.

#### <a name="default-configuration"></a>Standardkonfiguration

IoT Edge-moduler måste kunna börja med standardinställningarna i [SKU-fliken i Cloud Partner Portal](./cpp-skus-tab.md). Det finns följande standardinställningar:

- Standard **vägar**
- Standard **twin önskade egenskaper**
- Standard **miljövariabler**
- Standard **createOptions**

I ett scenario där en parameter som krävs för ett standardvärde inte meningsfullt (till exempel IP-adressen för en kunds server) kan du lägga till en parameter som standardvärde. Det här värdet är omsluten omges av hakparenteser och i versaler. I det här exemplet skulle du ställa in miljövariabeln följande standard:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentationen Configuration

Alla konfigurationsinställningar för en IoT Edge-modul måste dokumenteras tydligt (hur du använder dess vägar twin önskade egenskaper, miljövariabler, createOptions och så vidare). Ange en länk till dokumentationen eller dokumentationen måste vara en del av ditt erbjudande/sku-beskrivning.

### <a name="tags-and-versioning"></a>Taggar och versionshantering

Kunder måste kunna distribuera enkelt en modul och automatiskt få uppdateringar från marketplace (i ett developer-scenario.) De också kunna använda och låser en exakt version de har testat (i ett produktionsscenario.)

För att uppfylla dessa kundernas förväntningar och publiceras i marketplace, måste IoT Edge-moduler uppfylla följande krav:

- Inkludera ett manifest `latest` tagg som pekar den senaste versionen på alla plattformar som stöds.
- Version taggar måste vara i formatet X.Y.Z, där X, Y och Z är heltal.
- Inkludera en ”version”-tagg som `1.0.1`, som pekar på en specifik version på alla plattformar som stöds.
- Uppdatera inte ”version”-taggar som `1.0.1`, eftersom de måste vara inte kan ändras.

>[!Note]
>Du kan också versionshantering kan innehålla ”återställa version” taggar, till exempel `2.0` och `1.0`. Detta har stöd för att underhålla flera huvudversioner parallellt.

### <a name="telemetry"></a>Telemetri

Moduler med SDK för IoT-modul måste ange det unika modul-ID: t till `PublisherId.OfferId.SkuId` för telemetri. En unik identifierare kan i Azure Marketplace och identifiera antalet modulen instanser som körs.

 Använda följande metoder från SDK: er för IoT-modulen för att ange den `ProductInfo` till följande identifierare:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Mindre exakt insikter är tillgängliga via Cloud Partner Portal, till exempel antalet nedladdningar för moduler som inte använder SDK för IoT-modulen.

### <a name="security"></a>Säkerhet

IoT Edge-moduler måste be om minsta Privilegierade åtkomst till värden som möjligt. [Privilegierad moduler](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) bör undvikas.

### <a name="module-iot-sdk"></a>Modulen IoT-SDK

Inklusive IoT-SDK-modulen är inte ett krav för certifiering. Inklusive IoT-modulen SDK kan dock ge en bättre användarupplevelse. Till exempel stöd för routning eller skicka meddelanden till molnet.

SDK för IoT-modulen krävs för att hämta dessa data om antalet modulen instanser som körs.


## <a name="recertification-process"></a>Ny certifiering i enlighet process

<!-- Add legal time windows-->
Partner meddelas när det finns en stor förändring som påverkar deras moduler, till exempel:

- Stödmatris för nivå 1 os/arch stöds av IoT Edge
- IoT Module SDK
- IoT Edge-körningen
- Riktlinjer för IoT Edge-modulen certifikatutfärdare

Partners måste uppdatera sina moduler och certifiera om dem med hjälp av verktyget partnerportalen i molnet.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Vara värd för din IoT Edge-modul i ett Azure Container Registry

Om du vill överföra din IoT Edge-modul partnerportalen i molnet, måste du först lägga upp den i en [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Modulen måste innehålla alla taggar som du vill publicera, inklusive taggar som refereras till av en manifest tagg.


## <a name="next-steps"></a>Nästa steg

- [Skapa ditt erbjudande för IoT Edge-modul](./cpp-create-offer.md)
