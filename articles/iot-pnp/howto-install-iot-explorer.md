---
title: Installera och använda Azure IoT Explorer | Microsoft Docs
description: Installera Azure IoT Explorer-verktyget och Använd det för att interagera med IoT-Plug and Play förhands gransknings enheter anslutna till min IoT-hubb.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: fd180404ca18b5ea84c745a543ae7e87bf16c27d
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529634"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installera och använda Azure IoT Explorer

Azure IoT Explorer är ett grafiskt verktyg för att interagera med och testa din IoT Plug and Play Preview-enheter. När du har installerat verktyget på den lokala datorn kan du använda det för att ansluta till en enhet. Du kan använda verktyget för att Visa telemetri som enheten skickar, arbeta med enhets egenskaper och anropa kommandon.

Den här artikeln visar hur du:

- Installera och konfigurera Azure IoT Explorer-verktyget.
- Använd verktyget för att interagera med och testa dina enheter.

## <a name="prerequisites"></a>Krav

Om du vill använda Azure IoT Explorer-verktyget behöver du:

- En Azure IoT-hubb. Det finns många sätt att lägga till en IoT-hubb i din Azure-prenumeration, till exempel [skapa en IoT-hubb med hjälp av Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Du behöver den IoT Hub-anslutningssträngen för att köra Azure IoT Explorer-verktyget. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- En enhet som är registrerad i IoT Hub. Du kan registrera en enhet med hjälp av följande Azure CLI-kommando. Se till att ersätta `{YourIoTHubName}` och `{YourDeviceID}` plats hållarna med dina värden:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Installera Azure IoT Explorer

Gå till [Azure IoT Explorer-versioner](https://github.com/Azure/azure-iot-explorer/releases) och expandera listan med till gångar för den senaste versionen. Hämta och installera den senaste versionen av programmet.

## <a name="use-azure-iot-explorer"></a>Använda Azure IoT Explorer

För en enhet kan du antingen ansluta din egen enhet eller använda en av våra exempel simulerade enheter. Följ [de här anvisningarna](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) för att köra exemplet på simulerad enhet.

### <a name="connect-to-your-hub"></a>Anslut till hubben

Första gången du kör Azure IoT Explorer uppmanas du att ange anslutnings strängen för IoT Hub. När du har lagt till anslutnings strängen väljer du **Anslut**. Du kan använda verktygets inställningar för att växla till en annan IoT-hubb genom att uppdatera anslutnings strängen.

Modell definitionen för en IoT Plug and Play-enhet lagras antingen i det offentliga lagret, i företags databasen eller på den anslutna enheten. Som standard letar verktyget efter din modell definition i lagrings platsen för den offentliga modellen och den anslutna enheten. Du kan lägga till och ta bort källor eller konfigurera prioriteten för källorna i **inställningarna**:

Så här lägger du till en källa:

1. Gå till **Inställningar**.
1. Välj **nytt** och välj källa.
1. Om du lägger till din företags modell databas anger du anslutnings strängen.

Så här tar du bort en källa:

1. Gå till **Inställningar**.
1. Hitta källan som du vill ta bort.
1. Välj **X** för att ta bort den. Du kan inte ta bort lagrings platsen för den offentliga modellen eftersom de gemensamma gränssnitts definitionerna kommer från den här databasen.

Ändra käll prioriteringarna:

Du kan dra och släppa en av modell definitions källorna till en annan rangordning i listan. Om det uppstår en konflikt åsidosätter definitions källor med högre ranknings källor med lägre rangordning.

### <a name="view-devices"></a>Visa enheter

När verktyget har anslutit till din IoT-hubb visas **sidan enhets lista med** en lista över enhets identiteter som registrerats med IoT Hub. Du kan expandera alla poster i listan om du vill se mer information.

På sidan **enhets** lista kan du:

- Välj **Lägg till** för att registrera en ny enhet med hubben. Ange sedan ett enhets-ID. Använd standardinställningarna för att automatiskt generera autentiseringsinställningar och aktivera anslutningen till hubben.
- Välj en enhet och välj sedan **ta bort** för att ta bort en enhets identitet. Granska enhets detaljerna innan du slutför den här åtgärden för att vara säker på att du tar bort rätt enhets identitet.
- Fråga efter `capabilityID` och `interfaceID`. Lägg till antingen `capabilityID` eller `interfaceID` som en parameter för att skicka frågor till dina enheter.

## <a name="interact-with-a-device"></a>Interagera med en enhet

På sidan enhets **lista väljer** du ett värde i kolumnen **enhets-ID** för att Visa informations sidan för den registrerade enheten. Det finns två avsnitt: **enhet** och **digital**, för varje enhet.

### <a name="device"></a>Enhet

Det här avsnittet innehåller flikarna **enhets identitet**, **enhets**-ID, **telemetri**, **direkt metod** och **meddelande från moln till enhet** .

- Du kan visa och uppdatera [enhetens identitets](../iot-hub/iot-hub-devguide-identity-registry.md) information på fliken **enhets identitet** .
- Du kan komma åt [enhetens dubbla](../iot-hub/iot-hub-devguide-device-twins.md) information på fliken **enhets dubbla** .
- Om en enhet är ansluten och aktivt skickar data, kan du Visa [Telemetrin](../iot-hub/iot-hub-devguide-messages-read-builtin.md) på fliken **telemetri** .
- Du kan anropa en [direkt metod](../iot-hub/iot-hub-devguide-direct-methods.md) på enheten på fliken **direkt metod** .
- Du kan skicka ett [meddelande från moln till enhet](../iot-hub/iot-hub-devguide-messages-c2d.md) på fliken **meddelanden från molnet till enheten** .

### <a name="digital-twin"></a>Digital, dubbel

Du kan använda verktyget för att visa en digital, delad instans av enheten. För en IoT Plug and Play-enhet visas alla gränssnitt som är associerade med enhets kapacitets modellen i det här avsnittet av verktyget. Välj ett gränssnitt för att expandera motsvarande [IoT plug and Play-primitiver](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Gränssnitt

På sidan **gränssnitt** kan du Visa JSON-definitionen för gränssnittet.

#### <a name="properties"></a>Egenskaper

Du kan visa de skrivskyddade egenskaperna som definierats i ett gränssnitt på sidan **egenskaper som inte är skrivbara** . Du kan uppdatera de skrivbara egenskaper som definierats i ett gränssnitt på sidan **skrivbara egenskaper** :

1. Gå till sidan **skrivbara egenskaper** .
1. Klicka på den egenskap som du vill uppdatera.
1. Ange det nya värdet för egenskapen.
1. Förhandsgranska nytto lasten som ska skickas till enheten.
1. Skicka ändringen.

När du har skickat in en ändring kan du spåra uppdaterings statusen: **synkronisering**, **lyckades**eller **fel**. När synkroniseringen är klar visas det nya värdet för egenskapen i kolumnen **rapporterad egenskap** . Om du navigerar till andra sidor innan synkroniseringen är klar meddelar verktyget fortfarande dig när uppdateringen är klar. Du kan också använda verktygets meddelande Center för att se meddelande historiken.

#### <a name="commands"></a>Kommandon

Om du vill skicka ett kommando till en enhet går du till **kommando** sidan:

1. I listan med kommandon expanderar du kommandot som du vill utlösa.
1. Ange de värden som krävs för kommandot.
1. Förhandsgranska nytto lasten som ska skickas till enheten.
1. Skicka kommandot.

#### <a name="telemetry"></a>Telemetri

Om du vill visa telemetri för det valda gränssnittet går du till sidan **telemetri** .

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du installerar och använder Azure IoT Explorer för att interagera med IoT Plug and Play-enheter. Ett förslag till nästa steg är att lära dig hur du [installerar och använder Azure CLI-tillägg](./howto-install-pnp-cli.md).
