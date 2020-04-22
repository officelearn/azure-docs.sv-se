---
title: Ordlista med termer - IoT Plug and Play Preview | Microsoft-dokument
description: Begrepp - en ordlista med vanliga termer som rör IoT Plug and Play Preview.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767064"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Ordlista med termer för IoT Plug and Play Preview

Definitioner av vanliga termer som används i IoT Plug and Play artiklar.

## <a name="azure-certified-for-iot-portal"></a>Azure-certifierad för IoT-portal

Du kan använda [Azure Certified for IoT portal](https://aka.ms/ACFI) webbplats för att:

- Slutför [certifieringsprocessen](#device-certification) för din [IoT Plug and Play-enhet](#iot-plug-and-play-device).
- Hitta [modeller för enhetskapacitet](#device-capability-model).
- Publicera en enhetsfunktionsmodell i den [offentliga modelldatabasen](#public-model-repository).

## <a name="azure-cli"></a>Azure CLI

Azure CLI är ett kommandoradsverktyg över flera plattformar för hantering av Azure-resurser. Azure IoT-tillägget för Azure CLI är ett kommandoradsverktyg för interaktion med och testning av [IoT Plug and Play-enheter](#iot-plug-and-play-device). Du kan använda tillägget för att:

- Anslut till en IoT Plug and Play-enhet.
- Visa [den telemetri](#telemetry) som enheten skickar.
- Arbeta med [enhetsegenskaper](#properties).
- Anropa [enhetskommandon](#commands).
- Hantera [modelldatabaser,](#model-repository)gränssnitt och [enhetskapacitetsmodeller](#device-capability-model). [interfaces](#interface)

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central är en fullständigt hanterad lösning för programvara som en tjänst som gör det enkelt att ansluta, övervaka och hantera dina [IoT Plug and Play-enheter](#iot-plug-and-play-device). Du kan använda [enhetskapacitetsmodeller](#device-capability-model) för att automatiskt konfigurera ett IoT Central-program för att övervaka och hantera dina enheter.

## <a name="azure-iot-certification-service"></a>Azure IoT-certifieringstjänst

Azure IoT-certifieringstjänsten kör en uppsättning certifieringstester när du skickar in en [IoT Plug and Play-enhet](#iot-plug-and-play-device) för certifiering via [Azure Certified for IoT-portalen](#azure-certified-for-iot-portal). Innan du kan lägga till en enhet i [katalogen Certifierad för IoT-enhet](#certified-for-iot-device-catalog)måste enheten vara certifierad.

## <a name="azure-iot-tools-extension"></a>Azure IoT-verktygstillägg

Azure IoT Tools är en samling tillägg i [Visual Studio-kod](#visual-studio-code) som hjälper dig att interagera med IoT Hub och utveckla IoT-enheter. För utveckling av IoT Plug and Play-enheter hjälper det dig att:

- Författare [enhet kapacitet modeller](#device-capability-model) och [gränssnitt](#interface).
- Publicera till [modelldatabaser](#model-repository).
- Generera skelettkod för att implementera enhetsprogrammet.

## <a name="azure-iot-explorer-tool"></a>Verktyget Azure IoT Explorer

Azure IoT explorer är ett grafiskt verktyg som du kan använda för att interagera med och testa dina [IoT Plug and Play-enheter](#iot-plug-and-play-device). När du har installerat verktyget på den lokala datorn kan du använda det för att:

- Visa de enheter som är anslutna till [din IoT-hubb](#azure-iot-hub).
- Anslut till en IoT Plug and Play-enhet.
- Visa [den telemetri](#telemetry) som enheten skickar.
- Arbeta med [enhetsegenskaper](#properties).
- Anropa [enhetskommandon](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT-Hub är en hanterad tjänst som finns i molnet, och som fungerar som ett centralt meddelandenav för dubbelriktad kommunikation mellan din IoT-program och de enheter som den hanterar. [IoT Plug and Play-enheter](#iot-plug-and-play-device) kan ansluta till en IoT-hubb. En IoT-lösning använder en IoT-hubb för att aktivera:

- Enheter för att skicka telemetri till en molnbaserad lösning.
- En molnbaserad lösning för att hantera anslutna enheter.

## <a name="azure-iot-device-sdk"></a>SDK för Azure IoT-enhet

Det finns enhets-SDK:er för flera språk som du kan använda för att skapa IoT Plug and Play-enhetsklientprogram. Ett av kraven för [enhetscertifiering](#device-certification) är att enhetsklientkoden använder en av Azure IoT-enhetSDK:er.

## <a name="certified-for-iot-device-catalog"></a>Certifierad för IoT-enhetskatalog

[Katalogen Certifierad för IoT-enhet](https://catalog.azureiotsolutions.com/) listar [IoT Plug and Play-enheter](#iot-plug-and-play-device) som har klarat [enhetscertifieringstesterna.](#device-certification) [Enhetskapacitetsmodellerna](#device-capability-model) för IoT Plug and Play-enheterna i katalogen och publicerade i den offentliga modellarkivet.

## <a name="commands"></a>Kommandon

Kommandon som definieras i ett [gränssnitt](#interface) representerar metoder som kan köras på den [digitala tvillingen](#digital-twin). Ett kommando för att starta om en enhet.

## <a name="common-interface"></a>Gemensamt gränssnitt

Alla [IoT Plug and Play-enheter](#iot-plug-and-play-device) förväntas implementera några vanliga [gränssnitt](#interface). Enhetsinformationsgränssnittet definierar till exempel maskinvaru- och operativsystemsinformation om enheten. [Enhetscertifiering](#device-certification) kräver att enheten implementerar flera vanliga gränssnitt. Du kan hämta vanliga gränssnittsdefinitioner från den offentliga modelldatabasen.

## <a name="company-model-repository"></a>Databas för företagsmodell

En organisation kan använda en [företagsmodelldatabas](#model-repository) som en privat butik för [enhetskapacitetsmodeller](#device-capability-model) och [gränssnitt](#interface).

## <a name="connection-string"></a>Anslutningssträng

En anslutningssträng kapslar in den information som krävs för att ansluta till en slutpunkt. En anslutningssträng innehåller vanligtvis slutpunktens adress och säkerhetsinformation, men anslutningssträngformaten varierar mellan tjänster. Det finns två typer av anslutningssträng som är associerade med IoT Hub-tjänsten:

- Enhetsanslutningssträngar gör det möjligt för [IoT Plug and Play-enheter](#iot-plug-and-play-device) att ansluta till de enhetsvända slutpunkterna på en IoT-hubb. Klientkoden på en enhet använder anslutningssträngen för att upprätta en säker anslutning till en IoT-hubb.
- IoT Hub-anslutningssträngar möjliggör backend-lösningar och verktyg för att ansluta säkert till de tjänstinriktade slutpunkterna på en IoT-hubb. Dessa lösningar och verktyg hanterar IoT-hubben och de enheter som är anslutna till den.
- Anslutningssträngar för företagsmodelllagringsplats möjliggör backend-lösningar och verktyg för att ansluta säkert till en [företagsmodelldatabas](#company-model-repository). Dessa lösningar och verktyg förbrukar eller hanterar [enhetskapacitetsmodeller](#device-capability-model) och [gränssnitt](#interface) i databasen.

## <a name="device-capability-model"></a>Modell för enhetskapacitet

En enhetskapacitetsmodell beskriver en [IoT Plug and Play-enhet](#iot-plug-and-play-device) och definierar den uppsättning gränssnitt som [implementeras](#interface) av enheten. En enhetskapacitetsmodell motsvarar vanligtvis en fysisk enhet, produkt eller SKU. Du använder [digital twin definition språk](#digital-twin-definition-language) för att definiera en enhet kapacitetsmodell.

## <a name="device-certification"></a>Enhetscertifiering

Enhetscertifiering är processen att certifiera en [IoT Plug and Play-enhet](#iot-plug-and-play-device) så att den kan läggas till i [katalogen Certifierad för IoT-enhet](#certified-for-iot-device-catalog) och dess [enhetskapacitetsmodell](#device-capability-model) och [gränssnitt](#interface) som läggs till i den [offentliga modelldatabasen](#public-model-repository).

## <a name="device-developer"></a>Enhetsutvecklare

En enhetsutvecklare använder en [enhetskapacitetsmodell,](#device-capability-model) [gränssnitt](#interface)och en [Azure IoT-enhet SDK](#azure-iot-device-sdk) för att implementera kod som ska köras på en [IoT Plug and Play-enhet](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Enhetsmodellering

En [enhetsutvecklare](#device-developer) använder [digitalt dubbeldefinitionsspråk](#digital-twin-definition-language) för att modellera funktionerna i en [IoT Plug and Play-enhet](#iot-plug-and-play-device). Modellen kan delas med hjälp av en modelldatabas. En enhetsutvecklare kan generera programkod för skelett från modellen. En [lösningsutvecklare](#solution-developer) kan konfigurera en IoT-lösning från modellen.

## <a name="device-provisioning-service"></a>Tjänst för etablering av enhet

[Azure IoT Central](#azure-iot-central) använder enhetsetableringstjänsten för att hantera all enhetsregistrering och -anslutning. Mer information finns [i Enhetsanslutning i Azure IoT Central](../iot-central/core/concepts-get-connected.md). Du kan också använda enhetsetableringstjänsten för att hantera enhetsregistrering och anslutning till din IoT Hub-baserad IoT-lösning. Mer information finns i [Etablering av enheter med Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Enhetsregistrering

Innan en [IoT Plug and Play-enhet](#iot-plug-and-play-device) kan ansluta till en IoT-lösning måste den registreras med lösningen. [Azure IoT Central](#azure-iot-central) använder [enhetsetableringstjänsten](#device-provisioning-service) för att hantera enhetsregistrering. I en anpassad IoT-lösning kan du registrera enheter med din IoT-hubb i Azure-portalen eller programmässigt.

## <a name="device-first"></a>Enheten först

[Azure IoT Central](#azure-iot-central) stöder ett registrerings- och anslutningsscenario för enheten. I det här fallet kan en [IoT Plug and Play-enhet](#iot-plug-and-play-device) ansluta till ett IoT Central-program utan att registreras i förväg. Registrering sker automatiskt när en enhet först ansluter till programmet.

## <a name="digital-twin"></a>Digital tvilling

En digital tvilling är en modell av en [IoT Plug and Play-enhet.](#iot-plug-and-play-device) En digital tvilling modelleras med hjälp av [digital twin definition språk](#digital-twin-definition-language). Du kan använda [Azure IoT-enhetenSDK:er](#azure-iot-device-sdk) för att interagera med digitala tvillingar vid körning. Du kan till exempel ange ett egenskapsvärde i en digital tvilling på en enhet och SDK kommunicerar den här ändringen till din IoT-lösning i molnet.

## <a name="digital-twin-change-events"></a>Digitala twin förändring händelser

När en [IoT Plug and Play-enhet](#iot-plug-and-play-device) är ansluten till en [IoT-hubb](#azure-iot-hub)kan navet använda sin routningskapacitet för att skicka meddelanden om digitala tvillingändringar. När ett [egenskapsvärde](#properties) ändras på en enhet kan IoT Hub till exempel skicka ett meddelande till en slutpunkt, till exempel en servicebusskö.

## <a name="digital-twin-definition-language"></a>Digitalt dubbeldefinitionsspråk

Ett språk för att beskriva modeller och gränssnitt för [IoT Plug and Play-enheter](#iot-plug-and-play-device). Använd [digital twin definition språk](https://aka.ms/DTDL) för att beskriva en [digital tvilling](#digital-twin) kapacitet och göra det möjligt för IoT-plattformen och IoT-lösningar för att utnyttja semantik entiteten.

## <a name="digital-twin-route"></a>Digital tvillingväg

En väg som konfigurerats i en [IoT-hubb](#azure-iot-hub) för att leverera [digitala dubbla ändringshändelser](#digital-twin-change-events) till och slutpunkt, till exempel en servicebusskö.

## <a name="interface"></a>Gränssnitt

Ett gränssnitt beskriver relaterade funktioner som implementeras av en [IoT Plug and Play-enhet](#iot-plug-and-play-device) eller [digital tvilling](#digital-twin). Du kan återanvända gränssnitt över olika [enhetsfunktioner](#device-capability-model).

## <a name="iot-hub-query-language"></a>Frågespråk för IoT Hub

Frågespråket IoT Hub används för flera ändamål. Du kan till exempel använda språket för att söka efter [enheter som är registrerade](#device-registration) med din IoT-hubb eller förfina det digitala dubbla [routningsbeteendet.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play-enhet

En IoT Plug and Play-enhet är vanligtvis en småskalig, fristående datorenhet som samlar in data eller styr andra enheter och som kör programvara eller inbyggd programvara som implementerar en [enhetskapacitetsmodell](#device-capability-model).  En IoT Plug and Play-enhet kan till exempel vara en miljöövervakningsenhet eller en styrenhet för ett bevattningssystem för smarta jordbruk. Du kan skriva en molnbaserad IoT-lösning för att styra, styra och ta emot data från IoT Plug and Play-enheter. [Azure Certified for IoT-enhetskatalogen](#certified-for-iot-device-catalog) listar tillgängliga IoT Plug and Play-enheter. Varje IoT Plug and Play-enhet i katalogen har validerats och har en [enhetskapacitetsmodell](#device-capability-model).

## <a name="microsoft-partner-center"></a>Microsoft Partner Center

[Microsoft Partner Center](https://docs.microsoft.com/partner-center/) hanterar din organisation sin heltäckande relation med Microsoft. Du behöver ett Microsoft Partner Center-konto innan du kan certifiera din [IoT Plug and Play-enhet](#iot-plug-and-play-device) i [Azure Certified for IoT-portalen](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Modellidentifiering

När en [IoT Plug and Play-enhet](#iot-plug-and-play-device) ansluter till en IoT-lösning kan lösningen identifiera enhetens funktioner genom att hitta [enhetskapacitetsmodellen](#device-capability-model). En enhet kan skicka sin kapacitetsmodell till lösningen, eller så kan lösningen hitta en enhetskapacitetsmodell i en [modelldatabas](#model-repository).

## <a name="model-repository"></a>Modelldatabas

En modelldatabas lagrar [enhetskapacitetsmodeller](#device-capability-model) och [gränssnitt](#interface). Det finns ett enda [offentligt modellarkiv](#public-model-repository). Organisationer kan skapa sina egna organisationsmodelldatabaser.

## <a name="model-repository-rest-api"></a>REST-API för modelldatabas

Ett API för att hantera och interagera med modelldatabaser. Du kan till exempel använda API:et för att lägga till [enhetskapacitetsmodeller](#device-capability-model) och söka efter kapacitetsmodeller.

## <a name="properties"></a>Egenskaper

Egenskaper är datafält som definierats i ett [gränssnitt](#interface) som representerar ett visst tillstånd för en digital tvilling. Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Skrivskyddade egenskaper, till exempel serienummer, ställs in efter kod som körs på själva [IoT Plug and Play-enheten.](#iot-plug-and-play-device)  Skrivbara egenskaper, till exempel ett larmtröskel, ställs vanligtvis in från den molnbaserade IoT-lösningen.

## <a name="public-model-repository"></a>Databas för offentlig modell

Det finns en enda offentlig modelldatabas som lagrar [enhetskapacitetsmodeller](#device-capability-model) och [gränssnitt](#interface) för [certifierade enheter](#device-certification). Den offentliga modelldatabasen lagrar också gemensamma gränssnittsdefinitioner. [common interface](#common-interface)

## <a name="registration-id"></a>Registrerings-ID

Ett registrerings-ID identifierar unikt en enhet i [enhetsetableringstjänsten](#device-provisioning-service). Det här ID:t är inte samma sak som enhets-ID:t som är en unik identifierare för en enhet i en [IoT-hubb](#azure-iot-hub).

## <a name="scope-id"></a>Omfångs-ID

Scope-ID-scopet identifierar unikt en [enhetsetableringstjänstinstans.](#device-provisioning-service)

## <a name="shared-access-signature"></a>Signatur för delad åtkomst

Signaturer för delad åtkomst är en autentiseringsmekanism som baseras på SHA-256-secure hashar eller URI:er. Signaturautentisering med delad åtkomst har två komponenter: en princip för delad åtkomst och en signatur för delad åtkomst (kallas ofta token). En [IoT Plug and Play-enhet](#iot-plug-and-play-device) använder en signatur för delad åtkomst för att autentisera med en [IoT-hubb](#azure-iot-hub).

## <a name="solution-developer"></a>Lösningsutvecklare

En lösningsutvecklare skapar lösningens baksida. En lösningsutvecklare arbetar vanligtvis med Azure-resurser som [IoT Hub](#azure-iot-hub) och [modelldatabaser](#model-repository)eller arbetar med [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetri

Telemetrifält som definieras i ett [gränssnitt](#interface) representerar mått. Dessa mätningar är vanligtvis värden som sensoravläsningar som skickas av [IoT Plug and Play-enheten](#iot-plug-and-play-device) som en dataström.

## <a name="visual-studio-code"></a>Visual Studio-kod

Visual Studio-koden är en modern kodredigerare som är tillgänglig för flera plattformar. Tillägg, till exempel de i [Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Tools-paketet, gör att du kan anpassa redigeraren för att stödja ett brett spektrum av utvecklingsscenarier.
