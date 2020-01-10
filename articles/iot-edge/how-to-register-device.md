---
title: Registrera en ny Azure IoT Edge enhet | Microsoft Docs
description: Använd IoT-tillägget för Azure CLI för att registrera en ny IoT Edge-enhet och hämta anslutningssträngen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 32121681b14989f23e29c3701826b4494988c263
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772439"
---
# <a name="register-an-azure-iot-edge-device"></a>Registrera en Azure IoT Edge-enhet

Innan du kan använda dina IoT-enheter med Azure IoT Edge måste du registrera dem med IoT Hub. När en enhet har registrerats kan du hämta en anslutnings sträng för att konfigurera enheten för IoT Edge arbets belastningar.

Du kan välja att registrera med något av följande verktyg:

* [Registrera en enhet i Azure Portal](#register-in-the-azure-portal) om du föredrar ett grafiskt användar gränssnitt för att skapa, Visa och hantera Azure-resurser.
* [Registrera en enhet med Visual Studio Code](#register-with-visual-studio-code) om du föredrar att hantera Azure IoT-resurser på samma plats där du utvecklar dina IoT-lösningar.
* [Registrera en enhet med Azure CLI](#register-with-the-azure-cli) om du föredrar kommando rads verktyg för att hantera Azure-resurser, eller om du vill automatisera uppgifter.

## <a name="register-in-the-azure-portal"></a>Registrera dig i Azure Portal

Du kan utföra alla registrerings uppgifter i Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Krav för Azure Portal

En kostnads fri eller standard [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Skapa en IoT Edge enhet i Azure Portal

I IoT Hub i Azure Portal skapas och hanteras IoT Edge enheter separat från IOT-enheter som inte är Edge-aktiverade.

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
2. Välj **IoT Edge** på menyn i den vänstra rutan.
3. Välj **Lägg till en IoT Edge enhet**.
4. Ange ett beskrivande enhets-ID. Använd standardinställningarna för att automatiskt generera autentiseringsinställningar och Anslut den nya enheten till hubben.
5. Välj **Spara**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visa IoT Edge enheter i Azure Portal

Alla edge-aktiverade enheter som ansluter till IoT-hubben visas på den **IoT Edge** sidan.

![Visa alla IoT Edge-enheter i IoT hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Hämta anslutnings strängen i Azure Portal

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub.

1. På sidan **IoT Edge** i portalen klickar du på enhets-ID i listan över IoT Edge enheter.
2. Kopiera värdet för antingen **primär anslutnings sträng** eller **sekundär anslutnings sträng**.

## <a name="register-with-visual-studio-code"></a>Registrera med Visual Studio Code

Det finns flera sätt att utföra de flesta åtgärder i VS Code. I den här artikeln används Utforskaren, men du kan också använda kommando-paletten för att köra stegen.

### <a name="prerequisites-for-visual-studio-code"></a>Krav för Visual Studio Code

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen
* [Visual Studio-kod](https://code.visualstudio.com/)
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Logga in att få åtkomst till din IoT-hubb

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT Hub. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja IoT Hub.

1. I Visual Studio Code, öppna den **Explorer** vy.
1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Expandera avsnittet Azure IoT Hub-enheter](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser de tre punkterna, klicka på eller hovra över rubriken.
1. Välj **Välj IoT Hub**.
1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.
1. Välj din Azure-prenumeration.
1. Välj din IoT-hubb.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Skapa en IoT Edge-enhet med Visual Studio Code

1. I VS Code-Utforskaren expanderar den **Azure IoT Hub-enheter** avsnittet.
1. Klicka på den **...**  i den **Azure IoT Hub-enheter** avsnittsrubrik. Om du inte ser de tre punkterna, klicka på eller hovra över rubriken.
1. Välj **skapa IoT Edge-enhet**.
1. I textrutan som öppnas, ge enheten ett ID.

På skärmen utdata visas resultatet av kommandot. Enhetsinformation skrivs ut, som innehåller den **deviceId** som du angav och **connectionString** att du kan använda för att ansluta den fysiska enheten till IoT hub.

På skärmen utdata visas resultatet av kommandot. Enhetsinformation skrivs ut, som innehåller den **deviceId** som du angav och **connectionString** att du kan använda för att ansluta den fysiska enheten till IoT hub.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visa IoT Edge enheter med Visual Studio Code

Alla enheter som ansluter till din IoT Hub visas i avsnittet **Azure IoT Hub** i Visual Studio Code Explorer. IoT Edge enheter kan särskiljas från icke-Edge-enheter med en annan ikon, och det faktum att modulerna **$edgeAgent** och **$edgeHub** distribueras till varje IoT Edge enhet.

![Visa alla IoT Edge-enheter i IoT hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Hämta anslutnings strängen med Visual Studio Code

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub.

1. Högerklicka på enhetens ID i **Azure IoT Hub** -avsnittet.
1. Välj **kopiera enhetens anslutningssträng**.

   Anslutningssträngen kopieras till Urklipp.

Du kan också välja **hämta enhetsinformation** från snabbmenyn att se alla enhetsinformation, inklusive anslutningssträngen i utdatafönstret.

## <a name="register-with-the-azure-cli"></a>Registrera dig med Azure CLI

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) är ett kommando rads verktyg med öppen källkod för flera plattformar för att hantera Azure-resurser som IoT Edge. Det gör det möjligt för dig att hantera Azure IoT Hub-resurser, tjänstinstanser för enhetsetablering och länkade hubbar rutan. Det nya IoT-tillägget får Azure CLI med funktioner som enhetshantering och full IoT Edge-funktion.

### <a name="prerequisites-for-the-azure-cli"></a>Krav för Azure CLI

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i Azure-prenumerationen.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Azure CLI-version måste minst vara 2.0.24 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* Den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Skapa en IoT Edge-enhet med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create) för att skapa en ny enhets identitet i din IoT-hubb. Ett exempel:

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:

* **id för enhet**: Ange ett beskrivande namn som är unik för din IoT-hubb.
* **hubbnamn**: Ange namnet på din IoT-hubb.
* **Edge-aktiverade**: den här parametern anger att enheten är för användning med IoT Edge.

   ![AZ iot hub-enhetsidentitet skapa utdata](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visa IoT Edge enheter med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-list) för att visa alla enheter i din IoT Hub. Ett exempel:

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som registreras som en IoT Edge-enhet har egenskapen **capabilities.iotEdge** inställd **SANT**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Hämta anslutnings strängen med Azure CLI

När du är redo att konfigurera din enhet, måste den anslutningssträng som länkar den fysiska enheten med sin identitet i IoT hub. Använd kommandot [AZ IoT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) för att returnera anslutnings strängen för en enskild enhet:

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Värdet för parametern `device-id` är Skift läges känsligt. Kopiera inte citattecknen runt anslutningssträngen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en enhets identitet registrerad i din IoT-hubb är du redo att installera IoT Edge runtime på dina enheter. Installera körningen enligt enhetens operativ system:

* [Installera Azure IoT Edge på Windows](how-to-install-iot-edge-windows.md)
* [Installera Azure IoT Edge runtime på Linux](how-to-install-iot-edge-linux.md)
