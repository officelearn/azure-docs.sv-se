---
title: Installera och använda Azure IoT Explorer | Microsoft Docs
description: Installera Azure IoT Explorer-verktyget och Använd det för att interagera med IoT-Plug and Play förhands gransknings enheter anslutna till min IoT-hubb.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4e23a440f46b52633a88d0212e08c7b584f61a38
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932480"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installera och använda Azure IoT Explorer

Azure IoT Explorer är ett grafiskt verktyg för att interagera med och testa din IoT Plug and Play Preview-enheter. När du har installerat verktyget på den lokala datorn kan du använda det för att ansluta till en enhet. Du kan använda verktyget för att Visa telemetri som enheten skickar, arbeta med enhets egenskaper och anropa kommandon.

Den här artikeln visar hur du:

- Installera och konfigurera Azure IoT Explorer-verktyget.
- Använd verktyget för att interagera med och testa dina enheter.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure IoT Explorer-verktyget behöver du:

- En Azure IoT-hubb. Det finns många sätt att lägga till en IoT-hubb i din Azure-prenumeration, till exempel [skapa en IoT-hubb med hjälp av Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Du behöver den IoT Hub-anslutningssträngen för att köra Azure IoT Explorer-verktyget. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- En enhet som är registrerad i IoT Hub. Du kan registrera en enhet med hjälp av följande Azure CLI-kommando. Se till att ersätta `{YourIoTHubName}` plats hållarna och `{YourDeviceID}` med dina värden:

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

1. Gå till **inställningar**.
1. Välj **nytt** och välj källa.
1. Om du lägger till din företags modell databas anger du anslutnings strängen.

Så här tar du bort en källa:

1. Gå till **inställningar**.
1. Hitta källan som du vill ta bort.
1. Välj **X** för att ta bort den. Du kan inte ta bort lagrings platsen för den offentliga modellen eftersom de gemensamma gränssnitts definitionerna kommer från den här databasen.

Ändra käll prioriteringarna:

Du kan dra och släppa en av modell definitions källorna till en annan rangordning i listan. Om det uppstår en konflikt åsidosätter definitions källor med högre ranknings källor med lägre rangordning.

### <a name="overview-page"></a>Översikts sida

#### <a name="device-overview"></a>Enhets översikt

När verktyget har anslutit till din IoT-hubb visas en översikts sida med en lista över alla enhets identiteter som registrerats med Azure IoT Hub. Välj en enhet om du vill visa mer information.

#### <a name="device-management"></a>Enhetshantering

- Om du vill registrera en ny enhet med hubben väljer du **Lägg till**. Ange ett enhets-ID. Använd standardinställningarna för att automatiskt generera autentiseringsinställningar och aktivera anslutningen till hubben.
- Om du vill ta bort en enhets identitet väljer du **ta bort**. Granska enhets detaljerna innan du slutför den här åtgärden för att vara säker på att du tar bort rätt enhets identitet.
- Verktyget stöder fråga efter `capabilityID` och. `interfaceID` Lägg till antingen `capabilityID` din `interfaceID` eller som en parameter för att skicka frågor till dina enheter.

## <a name="interact-with-a-device"></a>Interagera med en enhet

Dubbelklicka på en enhet på översikts sidan för att visa nästa detalj nivå. Det finns två avsnitt: **Enhet** och **digital**.

### <a name="device"></a>Enhet

Det här avsnittet innehåller flikarna **enhets identitet**, **telemetri**och **enhets dubbla** .

- Du kan visa och uppdatera enhetens identitets information på fliken **enhets identitet** .
- Om en enhet är ansluten och aktivt skickar data, kan du Visa Telemetrin på fliken **telemetri** .
- Du kan komma åt enhetens dubbla information på fliken **enhets dubbla** .

### <a name="digital-twin"></a>Digital, dubbel

Du kan använda verktyget för att visa en digital, delad instans av enheten. För en IoT Plug and Play-enhet visas alla gränssnitt som är kopplade till enhets kapacitets modellen i den här artikeln. Välj ett gränssnitt för att expandera motsvarande [IoT plug and Play-primitiver](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

#### <a name="properties"></a>properties

Du kan visa de skrivskyddade egenskaperna som definierats i ett gränssnitt på sidan **Egenskaper** . Du kan uppdatera de skrivbara egenskaper som definierats i ett gränssnitt på sidan **skrivbara egenskaper** .

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
