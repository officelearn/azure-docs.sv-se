---
title: Installera och använda Azure IoT explorer | Microsoft-dokument
description: Installera Azure IoT explorer-verktyget och använd det för att interagera med IoT Plug and Play Preview-enheter som är anslutna till min IoT-hubb.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 963421fa1ef06599448c9a4197f0d7a6ad2e142d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159191"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installera och använda Azure IoT Explorer

Azure IoT Explorer är ett grafiskt verktyg för att interagera med och testa dina IoT Plug and Play Preview-enheter. När du har installerat verktyget på den lokala datorn kan du använda det för att ansluta till en enhet. Du kan använda verktyget för att visa den telemetri som enheten skickar, arbetar med enhetsegenskaper och samtalskommandon.

Den här artikeln visar hur du:

- Installera och konfigurera Azure IoT explorer-verktyget.
- Använd verktyget för att interagera med och testa dina enheter.

## <a name="prerequisites"></a>Krav

Om du vill använda Azure IoT explorer-verktyget behöver du:

- En Azure IoT-hubb. Det finns många sätt att lägga till en IoT-hubb i din Azure-prenumeration, till exempel [Skapa en IoT-hubb med hjälp av Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Du behöver anslutningssträngen för IoT-hubb för att kunna köra Azure IoT Explorer-verktyget. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
- En enhet som är registrerad i din IoT-hubb. Du kan använda följande Azure CLI-kommando för att registrera en enhet. Var noga med `{YourIoTHubName}` `{YourDeviceID}` att ersätta och platshållare med dina värden:

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Installera Azure IoT Explorer

Gå till [Azure IoT explorer-versioner](https://github.com/Azure/azure-iot-explorer/releases) och expandera listan över tillgångar för den senaste versionen. Hämta och installera den senaste versionen av programmet.

## <a name="use-azure-iot-explorer"></a>Använda Azure IoT Explorer

För en enhet kan du antingen ansluta din egen enhet eller använda en av våra exempelsimulerade enheter. Följ [dessa instruktioner](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) för att köra det simulerade enhetsprovet.

### <a name="connect-to-your-hub"></a>Anslut till navet

Första gången du kör Azure IoT Explorer uppmanas du att ange ioT-hubbens anslutningssträng. När du har lagt till anslutningssträngen väljer du **Anslut**. Du kan använda verktygets inställningar för att växla till en annan IoT-hubb genom att uppdatera anslutningssträngen.

Modelldefinitionen för en IoT Plug and Play-enhet lagras i antingen den offentliga databasen, en företagsdatabas eller i den anslutna enheten. Som standard söker verktyget efter modelldefinitionen i den offentliga modelldatabasen och den anslutna enheten. Du kan lägga till och ta bort källor eller konfigurera prioriteten för källorna i **Inställningar:**

Så här lägger du till en källa:

1. Gå till **Inställningar**.
1. Välj **Ny** och välj källa.
1. Om du lägger till företagets modelldatabas anger du anslutningssträngen.

Så här tar du bort en källa:

1. Gå till **Inställningar**.
1. Leta reda på den källa som du vill ta bort.
1. Välj **X** för att ta bort den. Du kan inte ta bort den offentliga modelldatabasen eftersom de vanliga gränssnittsdefinitionerna kommer från den här databasen.

Ändra källprioriteringar:

Du kan dra och släppa en av modelldefinitionskällorna till en annan rangordning i listan. Om det finns en konflikt åsidosätter definitionskällor med högre rankning källor med lägre rankning.

### <a name="view-devices"></a>Visa enheter

När verktyget har anslutits till IoT-hubben visas listsidan **Enheter** som visar enhetsidentiteterna som är registrerade med din IoT-hubb. Du kan expandera alla inlägg i listan om du vill se mer information.

På listsidan **Enheter** kan du:

- Välj **Lägg till** om du vill registrera en ny enhet med navet. Ange sedan ett enhets-ID. Använd standardinställningarna för att automatiskt generera autentiseringsnycklar och aktivera anslutningen till navet.
- Markera en enhet och välj sedan **Ta bort** om du vill ta bort en enhetsidentitet. Granska enhetsinformationen innan du slutför den här åtgärden för att vara säker på att du tar bort rätt enhetsidentitet.
- Fråga `capabilityID` efter `interfaceID`och . Lägg till `capabilityID` `interfaceID` antingen din eller som en parameter för att fråga dina enheter.

## <a name="interact-with-a-device"></a>Interagera med en enhet

På listsidan **Enheter** väljer du ett värde i kolumnen **Enhets-ID** för att visa detaljsidan för den registrerade enheten. För varje enhet finns två sektioner: **Device** och **Digital Twin**.

### <a name="device"></a>Enhet

Det här avsnittet innehåller flikarna **Enhetsidentitet,** **Enhetstvilling,** **Telemetri,** **Direkt och** **Meddelandeflikar från molnet till enhet.**

- Du kan visa och uppdatera [enhetsidentitetsinformationen](../iot-hub/iot-hub-devguide-identity-registry.md) på fliken **Enhetsidentitet.**
- Du kan komma åt [enhetstvillinginformationen](../iot-hub/iot-hub-devguide-device-twins.md) på fliken **Enhetstvilling.**
- Om en enhet är ansluten och aktivt skickar data kan du visa [telemetrin](../iot-hub/iot-hub-devguide-messages-read-builtin.md) på fliken **Telemetri.**
- Du kan anropa en [direkt metod](../iot-hub/iot-hub-devguide-direct-methods.md) på enheten på **fliken Direkt-metod.**
- Du kan skicka ett [meddelande från molnet till enheten](../iot-hub/iot-hub-devguide-messages-c2d.md) på fliken Meddelanden från molnet till **enheten.**

### <a name="digital-twin"></a>Digital tvilling

Du kan använda verktyget för att visa digital tvillinginstans av enheten. För en IoT Plug and Play-enhet visas alla gränssnitt som är associerade med enhetskapacitetsmodellen i det här avsnittet av verktyget. Välj ett gränssnitt för att utöka motsvarande [IoT Plug and Play primitiver](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

### <a name="interface"></a>Gränssnitt

På **sidan Gränssnitt** kan du visa JSON-definitionen av gränssnittet.

#### <a name="properties"></a>Egenskaper

Du kan visa de skrivskyddade egenskaper som definierats i ett gränssnitt på sidan **Egenskaper som inte kan skrivas.** Du kan uppdatera de skrivbara egenskaper som definierats i ett gränssnitt på sidan **Skrivbara egenskaper:**

1. Gå till sidan **Skrivbara egenskaper.**
1. Klicka på den egenskap som du vill uppdatera.
1. Ange det nya värdet för egenskapen.
1. Förhandsgranska nyttolasten som ska skickas till enheten.
1. Skicka ändringen.

När du har skickat in en ändring kan du spåra uppdateringsstatus: **synkronisering,** **framgång**eller **fel**. När synkroniseringen är klar visas det nya värdet för egenskapen i kolumnen **Rapporterad egenskap.** Om du navigerar till andra sidor innan synkroniseringen är klar meddelar verktyget dig fortfarande när uppdateringen är klar. Du kan också använda verktygets meddelandecenter för att se meddelandehistoriken.

#### <a name="commands"></a>Kommandon

Om du vill skicka ett kommando till en enhet går du till sidan **Kommandon:**

1. Expandera det kommando som du vill utlösa i listan med kommandon.
1. Ange eventuella värden som krävs för kommandot.
1. Förhandsgranska nyttolasten som ska skickas till enheten.
1. Skicka kommandot.

#### <a name="telemetry"></a>Telemetri

Om du vill visa telemetrin för det valda gränssnittet går du till sidan **Telemetri.**

## <a name="next-steps"></a>Nästa steg

I den här artikeln om hur du installerar och använder Azure IoT Explorer för att interagera med dina IoT Plug and Play-enheter. Ett förslag till nästa steg är att lära sig hur [du installerar och använder Azure CLI-tillägg](./howto-install-pnp-cli.md).
