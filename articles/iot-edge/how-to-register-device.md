---
title: Registrera en ny Azure IoT Edge-enhet | Microsoft-dokument
description: Använd IoT-tillägget för Azure CLI för att registrera en ny IoT Edge-enhet och hämta anslutningssträngen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fb776b4c1ff537401a23eb272526b3043fdb1e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235730"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrera en Azure IoT Edge-enhet

Innan du kan använda dina IoT-enheter med Azure IoT Edge måste du registrera dem med din IoT-hubb. När en enhet har registrerats kan du hämta en anslutningssträng för att konfigurera enheten för IoT Edge-arbetsbelastningar.

Du kan välja att registrera dig med hjälp av något av följande verktyg:

* [Registrera en enhet i Azure-portalen](#register-in-the-azure-portal) om du föredrar ett grafiskt användargränssnitt för att skapa, visa och hantera Azure-resurser.
* [Registrera en enhet med Visual Studio-kod](#register-with-visual-studio-code) om du föredrar att hantera Azure IoT-resurser på samma plats där du utvecklar dina IoT-lösningar.
* [Registrera en enhet med Azure CLI](#register-with-the-azure-cli) om du föredrar kommandoradsverktyg för hantering av Azure-resurser eller tänker automatisera uppgifter.

## <a name="register-in-the-azure-portal"></a>Registrera dig i Azure-portalen

Du kan utföra alla registreringsuppgifter i Azure-portalen.

### <a name="prerequisites-for-the-azure-portal"></a>Förutsättningar för Azure-portalen

En kostnadsfri eller vanlig [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Skapa en IoT Edge-enhet i Azure-portalen

I din IoT-hubb i Azure-portalen skapas IoT Edge-enheter separat från IOT-enheter som inte är edgeaktiverade.

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
2. Välj **IoT Edge** på menyn i den vänstra rutan.
3. Välj **Lägg till en IoT Edge-enhet**.
4. Ange ett beskrivande enhets-ID. Använd standardinställningarna för att automatiskt generera autentiseringsnycklar och ansluta den nya enheten till navet.
5. Välj **Spara**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visa IoT Edge-enheter i Azure-portalen

Alla edge-aktiverade enheter som ansluter till din IoT-hubb visas på **IoT** Edge-sidan.

![Visa alla IoT Edge-enheter i din IoT-hubb](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Hämta anslutningssträngen i Azure-portalen

När du är redo att konfigurera enheten behöver du anslutningssträngen som länkar din fysiska enhet med dess identitet i IoT-hubben.

1. Från **IoT Edge-sidan** i portalen klickar du på enhets-ID:t från listan över IoT Edge-enheter.
2. Kopiera värdet för antingen **primär anslutningssträng** eller **sekundär anslutningssträng**.

## <a name="register-with-visual-studio-code"></a>Registrera dig med Visual Studio-kod

Det finns flera sätt att utföra de flesta åtgärder i VS-kod. Den här artikeln använder Utforskaren, men du kan också använda kommandopaletten för att köra stegen.

### <a name="prerequisites-for-visual-studio-code"></a>Förutsättningar för Visual Studio-kod

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration
* [Visual Studio-kod](https://code.visualstudio.com/)
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio-kod

### <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att komma åt din IoT-hubb

Du kan använda Azure IoT-tilläggen för Visual Studio-kod för att utföra åtgärder med din IoT Hub. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja din IoT Hub.

1. Öppna **Utforskarvyn** i Visual Studio-kod.
1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Expandera avsnittet Azure IoT Hub Devices](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnittshuvudet för **Azure IoT Hub.** Om du inte ser ellipsen klickar du på eller hovrar över sidhuvudet.
1. Välj **Välj IoT Hub**.
1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.
1. Välj din Azure-prenumeration.
1. Välj din IoT-hubb.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Skapa en IoT Edge-enhet med Visual Studio-kod

1. Expandera avsnittet **Azure IoT Hub Devices** i VS-kodutforskaren.
1. Klicka på avsnittshuvudet **...** i avsnittet **Azure IoT Hub Devices.** Om du inte ser ellipsen klickar du på eller hovrar över sidhuvudet.
1. Välj **Skapa IoT Edge-enhet**.
1. Ge enheten ett ID i textrutan som öppnas.

På utdataskärmen visas resultatet av kommandot. Enhetsinformationen skrivs ut, vilket inkluderar **den deviceId** som du angav och **den anslutningSTRing** som du kan använda för att ansluta din fysiska enhet till din IoT-hubb.

På utdataskärmen visas resultatet av kommandot. Enhetsinformationen skrivs ut, vilket inkluderar **den deviceId** som du angav och **den anslutningSTRing** som du kan använda för att ansluta din fysiska enhet till din IoT-hubb.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visa IoT Edge-enheter med Visual Studio-kod

Alla enheter som ansluter till din IoT-hubb visas i avsnittet **Azure IoT Hub** i Utforskaren för Visual Studio-kod. IoT Edge-enheter kan skiljas från enheter som inte är Edge-enheter med en annan ikon och det faktum att **$edgeAgent** och **$edgeHub-modulerna** distribueras till varje IoT Edge-enhet.

![Visa alla IoT Edge-enheter i din IoT-hubb](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Hämta anslutningssträngen med Visual Studio-kod

När du är redo att konfigurera enheten behöver du anslutningssträngen som länkar din fysiska enhet med dess identitet i IoT-hubben.

1. Högerklicka på ID:t för din enhet i avsnittet **Azure IoT Hub.**
1. Välj **Kopiera enhetsanslutningssträng**.

   Anslutningssträngen kopieras till Urklipp.

Du kan också välja **Hämta enhetsinformation** på högerklicksmenyn för att se all enhetsinformation, inklusive anslutningssträngen, i utdatafönstret.

## <a name="register-with-the-azure-cli"></a>Registrera dig hos Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommandoradsverktyg med flera plattformar med öppen källkod för hantering av Azure-resurser som IoT Edge. Det gör att du kan hantera Azure IoT Hub-resurser, enhetsetablering tjänstinstanser och länkade hubbar ur lådan. IoT-tillägget berikar Azure CLI med funktioner som enhetshantering och fullständig IoT Edge-kapacitet.

### <a name="prerequisites-for-the-azure-cli"></a>Förutsättningar för Azure CLI

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-versionen måste vara minst 2.0.70 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Skapa en IoT Edge-enhet med Azure CLI

Använd kommandot [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) för att skapa en ny enhetsidentitet i IoT-hubben. Ett exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:

* **enhets-id:** Ange ett beskrivande namn som är unikt för din IoT-hubb.
* **hubbnamn**: Ange namnet på din IoT-hubb.
* **kantaktiverad**: Den här parametern försäkrar att enheten är avsedd att användas med IoT Edge.

   ![az iot hub device-identity skapa utdata](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visa IoT Edge-enheter med Azure CLI

Använd kommandot [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) för att visa alla enheter i IoT-hubben. Ett exempel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som är registrerade som en IoT Edge-enhet har **egenskapsfunktionerna.iotEdge** **inställda**på true .

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Hämta anslutningssträngen med Azure CLI

När du är redo att konfigurera enheten behöver du anslutningssträngen som länkar din fysiska enhet med dess identitet i IoT-hubben. Använd kommandot [az iot hub device-identity show-connection-string](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) för att returnera anslutningssträngen för en enda enhet:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Värdet för `device-id` parametern är skiftlägeskänsligt. Kopiera inte citattecknen runt anslutningssträngen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en enhetsidentitet registrerad i din IoT-hubb är du redo att installera IoT Edge-körningen på dina enheter. Installera körningen enligt enhetens operativsystem:

* [Installera Azure IoT Edge i Windows](how-to-install-iot-edge-windows.md)
* [Installera Azure IoT Edge-körningen på Linux](how-to-install-iot-edge-linux.md)
