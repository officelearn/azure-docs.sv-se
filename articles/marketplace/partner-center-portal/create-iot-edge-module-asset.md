---
title: Förbereda tekniska resurser för IoT Edge-modulen – Azure Marketplace
description: Lär dig mer om de tekniska och konfigurationskrav som dina tekniska resurser för IoT-edge-modul (IoT) måste uppfylla innan du kan publicera dem på Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: e83c70424c131e5324192b07e65321d63bf06e2e
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674220"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Förbereda tekniska resurser för IoT Edge-modulen

> [!IMPORTANT]
> Vi flyttar hanteringen av dina IoT Edge-modulerbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i Förbered dina tekniska resurser för [IoT Edge-modulen](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs de krav som dina tekniska resurser för IoT-edge-modul (IoT) måste uppfylla innan de publiceras på Azure Marketplace.

## <a name="get-started"></a>Kom igång

En IoT Edge-modul är en Docker-kompatibel behållare som körs på en IoT Edge-enhet.

- Mer information om IoT Edge-moduler finns i [Förstå Azure IoT Edge-moduler](https://aka.ms/UnderstandAzureIoTEdgemodules).
- För att komma igång med din IoT Edge-modulutveckling, se [Utveckla dina egna IoT Edge-moduler](https://aka.ms/DevelopyourownIoTEdgemodules).

## <a name="technical-requirements"></a>Tekniska krav

Din IoT Edge-modul måste uppfylla följande tekniska krav för att certifieras och publiceras på Azure Marketplace.

### <a name="platform-support"></a>Plattformssupport

Din IoT Edge-modul måste ha stöd för något av följande plattformsalternativ:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Tier 1-plattformar som stöds av IoT Edge

Din modul måste stödja alla Tier 1-plattformar som stöds av IoT Edge (som registrerats i [Azure IoT Edge-stöd](https://aka.ms/AzureIoTEdgesupport)). Vi rekommenderar det här alternativet eftersom det ger en bättre kundupplevelse. Moduler som uppfyller dessa kriterier kommer att visas upp. En modul som använder det här plattformsalternativet måste:

- Ange en senaste tagg och en versionstagg (till exempel 1.0.1) som är manifesttaggar byggda med [GitHub Manifest-verktyget](https://aka.ms/GitHubmanifest-tool).

- Använd fliken erbjudandelistelista i [Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace) om du vill lägga till en länk under avsnittet **Användbara länkar** i [Azure IoT Edge Certified-enhetskatalogen](https://catalog.azureiotsolutions.com/). Du kan använda omdirigeringslänken https://aka.ms/iot-edge-certified som matchas till Azure [IoT Edge Device Catalog](https://catalog.azureiotsolutions.com/).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>En delmängd av Tier 1-plattformar som stöds av IoT Edge

Din modul måste ha stöd för en delmängd (minst en) av Nivå 1-plattformar som stöds av IoT Edge (som registrerats i [Azure IoT Edge-stöd](https://aka.ms/AzureIoTEdgesupport)). En modul som använder det här plattformsalternativet måste:

- Ange en senaste tagg och en versionstagg (till exempel 1.0.1) som är manifesttaggar som skapats med [GitHub-manifestverktyget](https://github.com/estesp/manifest-tool) om mer än en plattform stöds. Manifesttaggar är valfria endast när en plattform stöds.
- Använd fliken erbjudandelistelista i [Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace) om du vill lägga till en länk under avsnittet **Användbara länkar** till minst en IoT Edge-enhet från [Azure IoT Edge Certified-enhetskatalogen](https://catalog.azureiotsolutions.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Det här är en bild av avsnittet Erbjudandelista i Partnercenter":::

### <a name="device-dimensions"></a>Enhetens dimensioner

IoT Edge-moduldimensioner (till exempel CPU, RAM, lagring och GPU) på riktade IoT Edge-enheter måste uppfylla följande krav:

- Modulen måste fungera med minst en IoT Edge-enhet från [Azure IoT Edge Certified-enhetskatalogen](https://catalog.azureiotsolutions.com/).

- De lägsta maskinvarukraven måste dokumenteras som det sista stycket i beskrivningen av erbjudandet (under fliken erbjudandelistelista i [Partnercenter).](https://partner.microsoft.com/dashboard/commercial-marketplace) Du kan också ange de rekommenderade maskinvarukraven om de skiljer sig avsevärt. Lägg till exempel till följande avsnitt i slutet av erbjudandebeskrivningen:

Kopiera html-texten eller använd motsvarande RTF-funktioner i redigeringsfönstret.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfiguration

Modulen måste innehålla standardkonfigurationsinställningar för att göra distributionen till en IoT Edge-enhet så enkel som möjligt. Den här informationen kan anges på sidan **Teknisk konfiguration** för planen i [Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace). Behållaren kan också innehålla IoT Edge Module SDK för att möjliggöra kommunikation med kanthubben och IoT Hub.

#### <a name="default-configuration"></a>Standardkonfiguration

IoT Edge-moduler måste kunna börja med standardinställningarna på sidan **Teknisk konfiguration** för planen i [Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace). Följande standardinställningar är tillgängliga:

- **Standardvägar**
- Önskade **egenskaper för standardmodultvilling**
- **Standardmiljövariabler**
- Alternativ **för att skapa standardbehållare**

I ett scenario där en parameter som krävs för ett standardvärde inte är meningsfull (till exempel IP-adressen för en kunds server) lägger du till en parameter som standardvärde. Detta värde är versaler och inneslutna inom parentes. I det här exemplet ställer du in följande standardmiljövariabel:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Dokumentation för konfiguration

Alla konfigurationsinställningar för en IoT Edge-modul måste vara tydligt dokumenterade. Du måste till exempel dokumentera hur du använder dess vägar, dubbla önskade egenskaper, miljövariabler, createOptions och så vidare. Du måste antingen tillhandahålla en länk till din dokumentation eller göra den till en del av ditt erbjudande eller din planbeskrivning. Du kan ange den här informationen på sidan **Erbjudandelista** och **Planera listning** i [Partnercenter](https://partner.microsoft.com/dashboard/commercial-marketplace).

#### <a name="tags-and-versioning"></a>Taggar och versionshantering

Kunderna måste enkelt kunna distribuera en modul och automatiskt få uppdateringar från marknadsplatsen (i ett utvecklarscenario). De måste också kunna använda och frysa en exakt version som de har testat (i ett produktionsscenario).

För att uppfylla dessa kundförväntningar och publiceras på marknaden måste IoT Edge-moduler uppfylla följande krav

- Inkludera en senaste tagg som pekar på den senaste versionen på alla plattformar som stöds.
- Gör versionstaggar i formuläret X.Y.Z, där X, Y och Z är heltal.
- Inkludera en "version"-tagg, till exempel 1.0.1, som pekar på en viss version på alla plattformar som stöds.
- Uppdatera inte "version"-taggar, till exempel 1.0.1, eftersom de inte får ändras.

> [!NOTE]
> Du kan också ta med taggar för "rullande version", till exempel 2.0 och 1.0. Detta stöder att underhålla flera huvudversioner parallellt.

### <a name="telemetry"></a>Telemetri

Moduler som använder IoT-modul SDK måste ange den unika modulidentifieraren till PublisherId.OfferId.SkuId för telemetriändamål. En unik identifierare hjälper Azure Marketplace att identifiera antalet modulinstanser som körs.

Använd någon av följande metoder från IoT-modul-SDK:er för att ställa in ProductInfo till den här identifieraren:

- [C #](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

För moduler som inte använder IoT-modul SDK är mindre exakta insikter tillgängliga via Partner Center, till exempel antalet nedladdningar.

### <a name="security"></a>Säkerhet

IoT Edge-moduler måste undvika [privilegierade moduler](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Be i stället om minsta möjliga privilegierade åtkomst till värden.

### <a name="module-iot-sdk"></a>Modul IoT SDK

Att inkludera IoT-modul SDK är inte en förutsättning för certifiering. Men inklusive IoT-modul SDK kan ge en bättre användarupplevelse. Till exempel för att stödja routning eller skicka meddelanden till molnet.

IoT-modul SDK krävs för att hämta telemetridata om antalet modulinstanser som körs.

## <a name="recertification-process"></a>Omcertifieringsprocess

Partner meddelas när det finns en brytningsändring som påverkar deras moduler, till exempel:

- Nivå 1 OS/båge stöder matris som stöds av IoT Edge
- IoT-modul SDK
- IoT Edge-körning
- Riktlinjer för certifiering av IoT Edge-modul

Partner måste uppdatera och certifiera om sina erbjudanden genom att publicera dem på nytt i [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace).

Erbjudandet certifieras också om du uppdaterar det, till exempel att lägga till nya bildtaggar.

## <a name="host-module-in-azure-container-registry"></a>Värdmodul i Azure Container Registry

Om du vill överföra din IoT Edge-modul till Azure Marketplace måste du först vara värd för den i ett [AZURE-behållarregister](https://azure.microsoft.com/services/container-registry/) (ACR). Modulen måste innehålla alla taggar som du vill publicera, inklusive de bildtaggar som refereras av en manifesttagg. Mer information finns i självstudien [Skapa ett Azure-behållarregister och skicka en behållaravbildning](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr).

## <a name="next-steps"></a>Nästa steg

- [Skapa ditt IoT Edge-modulerbjudande](https://aka.ms/AzureCreateIoT)