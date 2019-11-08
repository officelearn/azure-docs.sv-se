---
title: Skapa Azure IoT Edge modul tekniska till gångar | Azure Marketplace
description: Skapa tekniska till gångar för en IoT Edge-modul.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 57bc2f789836a7d3453004cdacc59029c4b24129
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827627"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Förbered din IoT Edge module tekniska till gångar

I den här artikeln beskrivs de krav som din IoT Edge module tekniska till gångar måste uppfylla innan de publiceras på Azure Marketplace.

## <a name="understanding-iot-edge-modules-and-getting-started"></a>Att förstå IoT Edge moduler och komma igång

En IoT Edge modul är en Docker-kompatibel behållare som görs för körning på en IoT Edge enhet.

- Mer information om IoT Edge moduler finns i [förstå Azure IoT Edge moduler](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- För att komma igång med din IoT Edge modul utveckling, se [krav och verktyg för att utveckla IoT Edge moduler](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="technical-requirements"></a>Tekniska krav

Följande tekniska krav måste uppfyllas för att din IoT Edge-modul ska certifieras och publiceras på Azure Marketplace.

### <a name="platform-support"></a>Plattformssupport

Din IoT Edge-modul måste ha stöd för något av följande plattforms alternativ.

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Nivå 1-plattformar som stöds av IoT Edge

Stöd för alla nivå 1-plattformar som stöds av IoT Edge (som anges i [Azure IoT Edge support](https://docs.microsoft.com/azure/iot-edge/support)). Vi rekommenderar det här alternativet eftersom det ger en bättre kund upplevelse. Moduler som uppfyller detta kriterium visas. En modul som använder detta plattforms alternativ måste:

- Ange en `latest`-tagg och en versions tagg (till exempel `1.0.1`) som är manifestbaserade taggar som skapats med GitHub [manifest-Tool](https://github.com/estesp/manifest-tool).
- Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att lägga till en länk till [kompatibla IoT Edge certifierade enheter](https://aka.ms/iot-edge-certified). Den här länken matchar `https://aka.ms/iot-edge-certified`, en webbplats där kunder kan bläddra eller söka efter certifierade enheter. Den här webbplatsen kallas även för den [Azure IoT Edge certifierade](https://catalog.azureiotsolutions.com/) enhets katalogen.

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>En delmängd av nivå 1-plattformar som stöds av IoT Edge
  
Stöd för en delmängd (minst en) på nivå 1-plattformar som stöds av IoT Edge (som registrerats i [Azure IoT Edge support](https://docs.microsoft.com/azure/iot-edge/support)). En modul som använder detta plattforms alternativ måste:

- Ange en `latest`-tagg och en versions tagg (till exempel `1.0.1`) som är manifestbaserade taggar som skapats med GitHub [manifest-Tool](https://github.com/estesp/manifest-tool) om mer än en plattform stöds. Manifest taggar är valfria endast när en plattform stöds.
- Använd [fliken Marketplace](./cpp-marketplace-tab.md) för att ange en länk till minst en IoT Edge enhet i katalogen [Azure IoT Edge certifierad](https://catalog.azureiotsolutions.com/) enhet.

### <a name="device-dimensions"></a>Enhets dimensioner

IoT Edge modulens dimensioner (CPU/RAM/lagring/GPU/osv.) på riktade IoT Edge enheter måste uppfylla följande krav:

- Modulen måste **fungera med minst en IoT Edge certifierad** enhet i katalogen [Azure IoT Edge Certified](https://catalog.azureiotsolutions.com/) Device.
- De **minsta maskin varu kraven** måste dokumenteras som det sista stycket i beskrivningen av erbjudandet (på [fliken Marketplace](./cpp-marketplace-tab.md)). Alternativt kan du också ange de rekommenderade maskin varu kraven om de skiljer sig avsevärt. Lägg till exempel till följande avsnitt i slutet av din erbjudande Beskrivning:

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>Konfiguration

Den innehåller också standard konfigurations inställningar för att distribuera till en IoT Edge enhet så att den blir så vanlig som möjligt. Behållaren kan också innehålla IoT Edge module SDK för att aktivera kommunikation med edgeHub och IoT Hub.

#### <a name="default-configuration"></a>Standard konfiguration

IoT Edge moduler måste kunna starta med de standardinställningar som finns på [fliken SKU i Cloud Partner Portal](./cpp-skus-tab.md). Följande standardinställningar är tillgängliga:

- Standard **vägar**
- Standardvärden för **dubbla önskade egenskaper**
- Standardvariabler för **miljön**
- Standard **createOptions**

I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds Server) lägger du till en parameter som standardvärdet. Det här värdet omges av hakparenteser och i versaler. I det här exemplet skulle du ställa in följande standard miljö variabel:

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurations dokumentation

Alla konfigurations inställningar för en IoT Edge modul måste vara tydligt dokumenterade (hur du använder dess vägar, dubbla önskade egenskaper, miljövariabler, createOptions och så vidare). Ange en länk till din dokumentation eller så måste dokumentationen vara en del av din erbjudande-/SKU-beskrivning.

### <a name="tags-and-versioning"></a>Taggar och versions hantering

Kunderna måste enkelt kunna distribuera en modul och automatiskt hämta uppdateringar från Marketplace (i ett utvecklings scenario.) De måste också kunna använda och låsa en exakt version som de har testat (i ett produktions scenario.)

IoT Edge moduler måste uppfylla följande krav för att möta kundens förväntningar och publiceras på Marketplace:

- Ta med en manifest `latest`-tagg, som pekar den senaste versionen på alla plattformar som stöds.
- Versions Taggar måste vara av formatet X. Y. Z, där X, Y och Z är heltal.
- Ta med taggen "version", som `1.0.1`, som pekar på en angiven version på alla plattformar som stöds.
- Uppdatera inte "version"-taggar som `1.0.1`eftersom de måste vara oföränderliga.

>[!Note]
>Om du vill kan versions hantering inkludera Taggar med rullande version, till exempel `2.0` och `1.0`. Detta stöder underhåll av flera huvud versioner parallellt.

### <a name="telemetry"></a>Telemetri

Moduler som använder IoT module SDK måste ange den unika modulens identifierare för att `PublisherId.OfferId.SkuId` i telemetri syfte. Med en unik identifierare kan Azure Marketplace identifiera antalet instanser som körs.

 Använd följande metoder från IoT-modulen SDK: er för att ange `ProductInfo` till den här identifieraren:

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

För moduler som inte använder sig av IoT module SDK är mindre exakta insikter tillgängliga via Cloud Partner Portal, till exempel antalet hämtningar.

### <a name="security"></a>Säkerhet

IoT Edge-moduler måste be om att minst privilegie rad åtkomst till värden är möjlig. [Privilegierade moduler](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) bör undvikas.

### <a name="module-iot-sdk"></a>Modul IoT SDK

Att inkludera IoT module SDK är inte ett krav för certifiering. Men även i IoT module SDK kan du få en bättre användar upplevelse. Till exempel för att stödja routning eller sändning av meddelanden till molnet.

IoT module SDK krävs för att hämta telemetridata om antalet instanser som körs.


## <a name="recertification-process"></a>Återcertifierings process

<!-- Add legal time windows-->
Partners får ett meddelande när det finns en brytande ändring som påverkar deras moduler, till exempel:

- Stöd mat ris för nivå 1/båge stöds av IoT Edge
- IoT module SDK
- IoT Edge körning
- Rikt linjerna för IoT Edge modulens certifiering

Partner måste uppdatera sina moduler och certifiera dem igen med hjälp av Cloud Partner Portal-verktyget.

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>Vara värd för din IoT Edge-modul i en Azure Container Registry

För att ladda upp IoT Edge-modulen till Cloud Partner Portal måste du först vara värd för den i en [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR). Modulen måste innehålla alla Taggar som du vill publicera, inklusive de avbildnings taggar som en manifest tag refererar till.


## <a name="next-steps"></a>Nästa steg

- [Skapa ett erbjudande för IoT Edge-modulen](./cpp-create-offer.md)
