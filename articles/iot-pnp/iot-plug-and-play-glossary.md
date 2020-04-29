---
title: Ord lista med termer – IoT Plug and Play Preview | Microsoft Docs
description: Koncept – en ord lista med vanliga villkor som rör IoT Plug and Play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767064"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Ord lista med villkor för för hands versionen av IoT Plug and Play

Definitioner av vanliga termer som används i IoT Plug and Play-artiklar.

## <a name="azure-certified-for-iot-portal"></a>Azure-certifierad för IoT-portalen

Du kan använda webbplatsen [Azure Certified för IoT Portal](https://aka.ms/ACFI) för att:

- Slutför [certifierings processen](#device-certification) för din [IoT plug and Play-enhet](#iot-plug-and-play-device).
- Hitta [enhets funktions modeller](#device-capability-model).
- Publicera en enhets kapacitets modell till den [offentliga modellens lagrings plats](#public-model-repository).

## <a name="azure-cli"></a>Azure CLI

Azure CLI är ett plattforms oberoende kommando rads verktyg för att hantera Azure-resurser. Azure IoT-tillägget för Azure CLI är ett kommando rads verktyg för att interagera med och testa [IoT plug and Play-enheter](#iot-plug-and-play-device). Du kan använda tillägget för att:

- Anslut till en IoT Plug and Play-enhet.
- Visa den [telemetri](#telemetry) som enheten skickar.
- Arbeta med enhets [Egenskaper](#properties).
- Anropa enhets [kommandon](#commands).
- Hantera [modell databaser](#model-repository), [gränssnitt](#interface)och [enhets kapacitets modeller](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central är en fullständigt hanterad lösning för program vara som en tjänst som gör det enkelt att ansluta, övervaka och hantera dina [IoT plug and Play-enheter](#iot-plug-and-play-device). Du kan använda [enhets kapacitets modeller](#device-capability-model) för att automatiskt konfigurera ett IoT Central program för att övervaka och hantera dina enheter.

## <a name="azure-iot-certification-service"></a>Azure IoT-certifikatutfärdare

Azure IoT-certifierings tjänsten kör en uppsättning certifierings test när du skickar en [IoT plug and Play-enhet](#iot-plug-and-play-device) för certifiering via [Azure-certifierad för IoT-portalen](#azure-certified-for-iot-portal). Innan du kan lägga till en enhet i [katalogen certifierad för IoT-enheter](#certified-for-iot-device-catalog)måste enheten vara godkänd.

## <a name="azure-iot-tools-extension"></a>Tillägg för Azure IoT-verktyg

Azure IoT-verktyg är en samling tillägg i [Visual Studio Code](#visual-studio-code) som hjälper dig att interagera med IoT Hub och utveckla IoT-enheter. För IoT Plug and Play enhets utveckling hjälper det dig att:

- Redigera [enhets kapacitets modeller](#device-capability-model) och [gränssnitt](#interface).
- Publicera på [modell databaser](#model-repository).
- Generera Skeleton-kod för att implementera enhets programmet.

## <a name="azure-iot-explorer-tool"></a>Verktyget Azure IoT Explorer

Azure IoT Explorer är ett grafiskt verktyg som du kan använda för att interagera med och testa dina [IoT plug and Play-enheter](#iot-plug-and-play-device). När du har installerat verktyget på den lokala datorn kan du använda det för att:

- Visa de enheter som är anslutna till din [IoT-hubb](#azure-iot-hub).
- Anslut till en IoT Plug and Play-enhet.
- Visa den [telemetri](#telemetry) som enheten skickar.
- Arbeta med enhets [Egenskaper](#properties).
- Anropa enhets [kommandon](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT-Hub är en hanterad tjänst som finns i molnet, och som fungerar som ett centralt meddelandenav för dubbelriktad kommunikation mellan din IoT-program och de enheter som den hanterar. [Iot plug and Play-enheter](#iot-plug-and-play-device) kan ansluta till en IoT-hubb. En IoT-lösning använder en IoT-hubb för att aktivera:

- Enheter för att skicka telemetri till en molnbaserad lösning.
- En molnbaserad lösning för att hantera anslutna enheter.

## <a name="azure-iot-device-sdk"></a>Azure IoT-enhetens SDK

Det finns enhets-SDK: er för flera språk som du kan använda för att bygga IoT Plug and Play enhets klient program. Ett av kraven för [enhets certifiering](#device-certification) är att enhets klient koden använder en av Azure IoT-enhetens SDK: er.

## <a name="certified-for-iot-device-catalog"></a>Certifierad för IoT-enhets katalog

Den [certifierade för IoT-katalogen](https://catalog.azureiotsolutions.com/) listar [IoT plug and Play enheter](#iot-plug-and-play-device) som har klarat [enhetens certifierings](#device-certification) test. [Enhets kapacitets modellerna](#device-capability-model) för IoT plug and Play-enheter i katalogen och publiceras i den offentliga modellens lagrings plats.

## <a name="commands"></a>Kommandon

Kommandon som definieras i ett [gränssnitt](#interface) representerar metoder som kan köras på den [digitala, dubbla](#digital-twin). Till exempel ett kommando för att starta om en enhet.

## <a name="common-interface"></a>Gemensamt gränssnitt

Alla [IoT plug and Play-enheter](#iot-plug-and-play-device) förväntas implementera några vanliga [gränssnitt](#interface). Till exempel definierar enhets informations gränssnittet maskinvaru-och operativ Systems information om enheten. [Enhets certifiering](#device-certification) kräver att enheten implementerar flera vanliga gränssnitt. Du kan hämta vanliga gränssnitts definitioner från den offentliga modellens lagrings plats.

## <a name="company-model-repository"></a>Företags modell databas

En organisation kan använda en företags [modell databas](#model-repository) som privat lagring för [enhets kapacitets modeller](#device-capability-model) och [gränssnitt](#interface).

## <a name="connection-string"></a>Anslutningssträng

En anslutnings sträng kapslar in den information som krävs för att ansluta till en slut punkt. En anslutnings sträng innehåller vanligt vis adressen till slut punkten och säkerhets informationen, men anslutnings sträng formaten varierar mellan olika tjänster. Det finns två typer av anslutnings strängar kopplade till den IoT Hub tjänsten:

- Anslutnings strängar för enheter gör att [IoT plug and Play-enheter](#iot-plug-and-play-device) kan ansluta till enhetens slut punkter på en IoT-hubb. Klient koden på en enhet använder anslutnings strängen för att upprätta en säker anslutning till en IoT-hubb.
- IoT Hub anslutnings strängar möjliggör backend-lösningar och-verktyg för att ansluta säkert till tjänsteorienterade slut punkter på en IoT-hubb. Dessa lösningar och verktyg hanterar IoT-hubben och de enheter som är anslutna till den.
- Anslutnings strängar för företags modellens databas möjliggör Server dels lösningar och verktyg för att ansluta säkert till en [företags modell databas](#company-model-repository). Dessa lösningar och verktyg använder eller hanterar [enhetens kapacitets modeller](#device-capability-model) och [gränssnitt](#interface) i lagrings platsen.

## <a name="device-capability-model"></a>Enhets kapacitets modell

En enhets kapacitets modell beskriver en [IoT plug and Play-enhet](#iot-plug-and-play-device) och definierar de [gränssnitt](#interface) som implementeras av enheten. En enhets funktions modell motsvarar vanligt vis en fysisk enhet, produkt eller SKU. Du använder det [digitala, dubbla definitions språket](#digital-twin-definition-language) för att definiera en enhets kapacitets modell.

## <a name="device-certification"></a>Enhets certifiering

Enhets certifiering är en process där [IoT plug and Play-enheten](#iot-plug-and-play-device) certifieras så att den kan läggas till i [katalogen Certified for IoT-enheter](#certified-for-iot-device-catalog) och dess [funktions modell](#device-capability-model) och [gränssnitt](#interface) läggs till i den [offentliga modellens lagrings plats](#public-model-repository).

## <a name="device-developer"></a>Enhetsutvecklare

En enhets utvecklare använder en [modell för enhets kapacitet](#device-capability-model), [gränssnitt](#interface)och en [Azure IoT-enhets-SDK](#azure-iot-device-sdk) för att implementera kod som ska köras på en [IoT plug and Play-enhet](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Enhets modellering

En [enhets utvecklare](#device-developer) använder ett [digitalt definitions språk](#digital-twin-definition-language) för att modellera funktionerna i en [IoT plug and Play-enhet](#iot-plug-and-play-device). Modellen kan delas med hjälp av en modell databas. En enhets utvecklare kan generera Skeleton enhets kod från modellen. En [lösnings utvecklare](#solution-developer) kan konfigurera en IoT-lösning från modellen.

## <a name="device-provisioning-service"></a>Enhets etablerings tjänst

[Azure IoT Central](#azure-iot-central) använder enhets etablerings tjänsten för att hantera all enhets registrering och anslutning. Mer information finns i [enhets anslutning i Azure IoT Central](../iot-central/core/concepts-get-connected.md). Du kan också använda enhets etablerings tjänsten för att hantera enhets registrering och anslutning till din IoT Hub-baserade IoT-lösning. Mer information finns i avsnittet [om att konfigurera enheter med Azure IoT Hub Device Provisioning service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Enhetsregistrering

Innan en [iot plug and Play-enhet](#iot-plug-and-play-device) kan ansluta till en IoT-lösning måste den registreras med lösningen. [Azure IoT Central](#azure-iot-central) använder [enhets etablerings tjänsten](#device-provisioning-service) för att hantera enhets registrering. I en anpassad IoT-lösning kan du registrera enheter med IoT-hubben i Azure Portal eller program mässigt.

## <a name="device-first"></a>Enhet-första

[Azure IoT Central](#azure-iot-central) har stöd för en enhets första registrerings-och anslutnings scenario. I det här scenariot kan en [IoT plug and Play-enhet](#iot-plug-and-play-device) ansluta till ett IoT Central program utan att registreras i förväg. Registreringen sker automatiskt när en enhet ansluter till programmet första gången.

## <a name="digital-twin"></a>Digital, dubbel

En digital enhet är en modell av en [IoT plug and Play-enhet](#iot-plug-and-play-device). En Digitals fläta modelleras med hjälp av det [digitala, dubbla definitions språket](#digital-twin-definition-language). Du kan använda SDK: er för [Azure IoT-enheter](#azure-iot-device-sdk) för att interagera med digitala dubbla i kör tid. Du kan till exempel ange ett egenskaps värde i en digital enhet på en enhet och SDK: n skickar den här ändringen till din IoT-lösning i molnet.

## <a name="digital-twin-change-events"></a>Digitala dubbla ändrings händelser

När en [iot plug and Play-enhet](#iot-plug-and-play-device) är ansluten till en [IoT-hubb](#azure-iot-hub)kan hubben använda sin routningsfunktioner för att skicka meddelanden om digitala dubbla ändringar. När ett [egenskaps](#properties) värde ändras på en enhet kan IoT Hub till exempel skicka ett meddelande till en slut punkt, till exempel en Service Bus kö.

## <a name="digital-twin-definition-language"></a>Digitalt elektroniskt definitions språk

Ett språk för att beskriva modeller och gränssnitt för [IoT plug and Play-enheter](#iot-plug-and-play-device). Använd det [digitala, dubbla definitions språket](https://aka.ms/DTDL) för att beskriva [digitala dubbla](#digital-twin) funktioner och aktivera IoT-plattformen och IoT-lösningarna för att dra nytta av entitetens semantik.

## <a name="digital-twin-route"></a>Digital, dubbel väg

En väg som kon figurer ATS i en [IoT-hubb](#azure-iot-hub) för att leverera [digitala dubbla ändrings händelser](#digital-twin-change-events) till och slut punkt, till exempel en Service Bus kö.

## <a name="interface"></a>Gränssnitt

Ett gränssnitt beskriver relaterade funktioner som implementeras av en [IoT plug and Play-enhet](#iot-plug-and-play-device) eller [Digitals](#digital-twin). Du kan återanvända gränssnitt över olika [enhets kapacitets modeller](#device-capability-model).

## <a name="iot-hub-query-language"></a>IoT Hub frågespråk

Det IoT Hub frågespråket används i flera olika syfte. Du kan till exempel använda språket för att söka efter enheter som är [registrerade](#device-registration) i IoT Hub eller förfina det [digitala dubbla routnings](#digital-twin-route) beteendet.

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play-enhet

En IoT Plug and Play-enhet är vanligt vis en småskalig, fristående data behandlings enhet som samlar in data eller styr andra enheter och som kör program vara eller inbyggd program vara som implementerar en [enhets kapacitets modell](#device-capability-model).  En IoT Plug and Play-enhet kan till exempel vara en miljö övervaknings enhet eller en kontrollant för ett bevattnings system med Smart-jordbruk. Du kan skriva en molnbaserad IoT-lösning i molnet för att kommando, kontrol lera och ta emot data från IoT Plug and Play-enheter. [Azure-certifierad för IoT-katalogen](#certified-for-iot-device-catalog) innehåller en lista över tillgängliga IoT plug and Play-enheter. Varje IoT Plug and Play-enhet i katalogen har verifierats och har en [enhets kapacitets modell](#device-capability-model).

## <a name="microsoft-partner-center"></a>Microsoft Partner Center

[Microsoft Partner Center](https://docs.microsoft.com/partner-center/) är den plats där din organisation hanterar sitt slut punkt till slut punkt med Microsoft. Du behöver ett Microsoft Partner Center-konto innan du kan certifiera din [IoT plug and Play-enhet](#iot-plug-and-play-device) i [Azure Certified för IoT-portalen](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Modellidentifiering

När en [iot plug and Play-enhet](#iot-plug-and-play-device) ansluter till en IoT-lösning kan lösningen identifiera funktionerna i enheten genom att hitta [enhetens kapacitets modell](#device-capability-model). En enhet kan skicka sin kapacitets modell till lösningen, eller så kan lösningen hitta en enhets kapacitets modell i en [modell lagrings plats](#model-repository).

## <a name="model-repository"></a>Modell databas

En modell databas lagrar [enhets kapacitets modeller](#device-capability-model) och [gränssnitt](#interface). Det finns en enda [offentlig modell databas](#public-model-repository). Organisationer kan skapa sina egna organisations modell databaser.

## <a name="model-repository-rest-api"></a>Modell databas REST API

Ett API för att hantera och interagera med modell databaser. Du kan till exempel använda API: et för att lägga till [enhets kapacitets modeller](#device-capability-model) och söka efter kapacitets modeller.

## <a name="properties"></a>Egenskaper

Egenskaper är data fält som definierats i ett [gränssnitt](#interface) och som representerar status för en digital, dubbel. Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Skrivskyddade egenskaper, till exempel serie nummer, anges med kod som körs på [IoT plug and Play själva enheten](#iot-plug-and-play-device) .  Skrivbara egenskaper, till exempel ett larm tröskelvärde, anges vanligt vis från den molnbaserade IoT-lösningen.

## <a name="public-model-repository"></a>Offentlig modell databas

Det finns en enda offentlig modell databas som lagrar [enhets kapacitets modeller](#device-capability-model) och [gränssnitt](#interface) för [certifierade enheter](#device-certification). Den offentliga modellens lagrings plats lagrar också [vanliga gränssnitts](#common-interface) definitioner.

## <a name="registration-id"></a>Registrerings-ID

Ett registrerings-ID identifierar unikt en enhet i [enhets etablerings tjänsten](#device-provisioning-service). Detta ID är inte samma som enhets-ID: t som är en unik identifierare för en enhet i en [IoT-hubb](#azure-iot-hub).

## <a name="scope-id"></a>Omfångs-ID

Omfattnings-ID: t identifierar unikt en instans av [enhets etablerings tjänsten](#device-provisioning-service) .

## <a name="shared-access-signature"></a>Signatur för delad åtkomst

Signaturer för delad åtkomst är en autentiseringsmekanism som baseras på SHA-256-säkra hash-värden eller URI: er. Autentiseringen av signaturen för delad åtkomst har två komponenter: en delad åtkomst princip och en signatur för delad åtkomst (kallas ofta för en token). En [iot plug and Play-enhet](#iot-plug-and-play-device) använder en signatur för delad åtkomst för att autentisera med en [IoT-hubb](#azure-iot-hub).

## <a name="solution-developer"></a>Lösnings utvecklare

En lösnings utvecklare skapar Server delen av lösningen. En lösnings utvecklare fungerar vanligt vis med Azure-resurser som [IoT Hub](#azure-iot-hub) och [modell databaser](#model-repository)eller fungerar med [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetri

Telemetri fält som definierats i ett [gränssnitt](#interface) representerar mått. Dessa mått är vanligt vis värden, till exempel sensor avläsningar som skickas av [IoT plug and Play-enheten](#iot-plug-and-play-device) som en data ström.

## <a name="visual-studio-code"></a>Visual Studio-kod

Visual Studio Code är en modern kod redigerare som är tillgänglig för flera plattformar. Tillägg, till exempel de som finns i [Azure IoT tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -paketet, gör att du kan anpassa redigeraren för att stödja ett brett utbud av utvecklings scenarier.
