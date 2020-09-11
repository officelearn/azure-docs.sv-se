---
title: Enhets utveckling för Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över hur du utvecklar enheter för att ansluta till ditt IoT Central-program.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 6fabd7d8cf5c19f05bd31c2d0b12863fd6e25382
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90017531"
---
# <a name="iot-central-device-development-overview"></a>Översikt över IoT Central enhets utveckling

*Den här artikeln gäller för enhets utvecklare.*

Med ett IoT Central program kan du övervaka och hantera miljon tals enheter under deras livs cykel. Den här översikten är avsedd för enhets utvecklare som implementerar kod som ska köras på enheter som ansluter till IoT Central.

Enheter interagerar med ett IoT Central program med hjälp av följande primitiver:

- _Telemetri_ är data som en enhet skickar till IoT Central. Till exempel en ström med temperatur värden från en onboard-sensor.
- _Egenskaper_ är tillstånds värden som en enhet rapporterar till IoT Central. Till exempel den aktuella versionen av enheten för inbyggd program vara. Du kan också ha skrivbara egenskaper som IoT Central kan uppdatera på enheten.
- _Kommandon_ anropas från IoT central för att kontrol lera beteendet för en enhet. Till exempel kan ditt IoT Central-program anropa ett kommando för att starta om en enhet.

En Solution Builder ansvarar för att konfigurera instrument paneler och vyer i IoT Central webb gränssnitt för att visualisera telemetri, hantera egenskaper och anropa kommandon.

## <a name="types-of-device"></a>Typer av enheter

I följande avsnitt beskrivs de huvudsakliga typer av enheter som du kan ansluta till ett IoT Central-program:

### <a name="standalone-device"></a>Fristående enhet

En fristående enhet ansluter direkt till IoT Central. En fristående enhet skickar vanligt vis telemetri från sina inbyggda eller anslutna sensorer till ditt IoT Central-program. Fristående enheter kan också rapportera egenskaps värden, ta emot skrivbara egenskaps värden och svara på kommandon.

### <a name="gateway-device"></a>Gateway-enhet

En gateway-enhet hanterar en eller flera underordnade enheter som ansluter till ditt IoT Central-program. Du använder IoT Central för att konfigurera relationerna mellan de efterföljande enheterna och gateway-enheten. Mer information finns i [definiera en ny enhets typ för IoT gateway i ditt Azure IoT Central-program](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Gräns enhet

En gräns enhet ansluter direkt till IoT Central, men fungerar som en mellanhand för andra enheter som kallas _löv enheter_. En gräns enhet finns vanligt vis nära de löv enheter som den agerar som mellanhand. Scenarier som använder gräns enheter är:

- Aktivera enheter som inte kan ansluta direkt till IoT Central för att ansluta via gräns enheten. En löv enhet kan till exempel använda Bluetooth för att ansluta till gräns enheten, som sedan ansluter via Internet till IoT Central.
- Aggregera telemetri innan det skickas till IoT Central. Den här metoden kan hjälpa till att minska kostnaderna för att skicka data till IoT Central.
- Kontrol lera löv enheter lokalt för att undvika svars tiden för anslutning till IoT Central via Internet.

En Edge-enhet kan också skicka sin egen telemetri, rapportera dess egenskaper och svara på skrivbara egenskaps uppdateringar och kommandon.

IoT Central ser bara gräns enheten, inte de löv enheter som är anslutna till gräns enheten.

Mer information finns i [lägga till en Azure IoT Edge-enhet till ditt Azure IoT Central-program](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Ansluta en enhet

Azure IoT Central använder [azure IoT Hub Device Provisioning service (DPS)](../../iot-dps/about-iot-dps.md) för att hantera all enhets registrering och anslutning.

Genom att använda DPS kan du:

- IoT Central för att stödja onboarding och ansluta enheter i stor skala.
- Du genererar autentiseringsuppgifter för enheten och konfigurerar enheterna offline utan att registrera enheterna via IoT Central användar gränssnitt.
- Du kan använda dina egna enhets-ID: n för att registrera enheter i IoT Central. Genom att använda dina egna enhets-ID: er blir det enklare att integrera med befintliga Back Office-system.
- Ett enda, konsekvent sätt att ansluta enheter till IoT Central.

Mer information finns i Anslut [till Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Säkerhet

Anslutningen mellan en enhet och ditt IoT Central program skyddas med hjälp av antingen [delade Access-signaturer](./concepts-get-connected.md#connect-devices-at-scale-using-sas) eller bransch standard [X. 509-certifikat](./concepts-get-connected.md#connect-devices-using-x509-certificates).

### <a name="communication-protocols"></a>Kommunikationsprotokoll

Kommunikations protokoll som en enhet kan använda för att ansluta till IoT Central inkluderar MQTT, AMQP och HTTPS. Internt IoT Central använder en IoT-hubb för att aktivera enhets anslutning. Mer information om de kommunikations protokoll som IoT Hub stöder för enhets anslutningar finns i [Välj ett kommunikations protokoll](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementera enheten

Använd en av [Azure IoT-enhetens SDK](#languages-and-sdks) : er för att implementera enhetens funktions sätt. Koden ska:

- Registrera enheten med DPS och Använd informationen från DPS för att ansluta till den interna IoT-hubben i ditt IoT Central-program.
- Skicka telemetri i det format som enhets mal len i IoT Central anger. IoT Central använder enhets mal len för att avgöra hur du använder telemetri för visualiseringar och analyser.
- Synkronisera egenskaps värden mellan enheten och IoT Central. Enhets mal len anger egenskaps namn och data typer så att IoT Central kan visa informationen.
- Implementera kommando hanterare för kommandona anger i enhets mal len. Enhets mal len anger kommando namn och parametrar som enheten ska använda.

Mer information om rollen hets mallar finns i [Vad är enhets mallar?](./concepts-device-templates.md).

En del exempel kod finns i [skapa och ansluta ett Node.js klient program](./tutorial-connect-device-nodejs.md) eller [skapa och ansluta ett python-klientprogram](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Språk och SDK: er

Mer information om språk och SDK: er som stöds finns i [förstå och använda Azure IoT Hub enhets-SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks): er.

## <a name="next-steps"></a>Nästa steg

Om du är en enhets utvecklare och vill gå in i viss kod, är det föreslagna nästa steg att [skapa och ansluta ett klient program till ditt Azure IoT Central-program](./tutorial-connect-device-nodejs.md).

Om du vill lära dig mer om hur du använder IoT Central, är de föreslagna nästa steg som du behöver för att testa snabb starterna, från och med [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).
