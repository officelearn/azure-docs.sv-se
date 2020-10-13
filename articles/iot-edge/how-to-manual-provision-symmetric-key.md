---
title: Etablera med symmetriska nycklar – Azure IoT Edge | Microsoft Docs
description: Efter installationen etablerar du en IoT Edge enhet med symmetriska nycklar med hjälp av anslutnings strängen och autentiserar till IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 9e288bcbebe4118bfc8cfa7cff46c79d7075555a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979714"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Konfigurera en Azure IoT Edge enhet med autentisering med symmetrisk nyckel

Den här artikeln innehåller anvisningar för att registrera en ny IoT Edge enhet i IoT Hub och konfigurera enheten för att autentisera med symmetriska nycklar.

Stegen i den här artikeln vägleder dig genom en process som kallas manuell etablering, där du ansluter varje enhet till IoT Hub manuellt. Alternativet är automatisk etablering med hjälp av IoT Hub Device Provisioning Service, vilket är användbart när du har många enheter att etablera.

<!--TODO: Add auto-provision info/links-->

För manuell etablering har du två alternativ för att autentisera IoT Edge enheter:

* **Symmetrisk nyckel**: när du skapar en ny enhets identitet i IoT Hub skapar tjänsten två nycklar. Du placerar en av nycklarna på enheten, och den presenterar nyckeln för att IoT Hub vid autentisering.

  Den här autentiseringsmetoden är snabbare för att komma igång, men inte lika säkert.

* **X. 509-egensignerat**: du skapar två X. 509-identitets certifikat och placerar dem på enheten. När du skapar en ny enhets identitet i IoT Hub ger du tumavtrycken från båda certifikaten. När enheten autentiserar till IoT Hub, visar den sina certifikat och IoT Hub kan kontrol lera att de matchar tumavtrycken.

  Den här autentiseringsmetoden är säkrare och rekommenderas för produktions scenarier.

Den här artikeln beskriver registrerings-och etablerings processen med symmetrisk nyckel autentisering. Om du vill lära dig hur du konfigurerar en enhet med X. 509-certifikat, se [Konfigurera en Azure IoT Edge enhet med 509 certifikatautentisering](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du följer anvisningarna i den här artikeln bör du ha en enhet med IoT Edge runtime installerad. Annars följer du stegen i [Installera eller avinstallera Azure IoT Edge runtime](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Registrera en ny enhet

Alla enheter som ansluter till en IoT Hub har ett enhets-ID som används för att spåra kommunikation från moln till enhet eller från enhet till moln. Du konfigurerar en enhet med dess anslutnings information, som innehåller IoT Hub-värdnamnet, enhets-ID: t och den information som enheten använder för att autentisera till IoT Hub.

För symmetrisk nyckel autentisering samlas den här informationen in i en *anslutnings sträng* som du kan hämta från IoT Hub och sedan placera på IoT Edge enheten.

Du kan använda flera verktyg för att registrera en ny IoT Edge enhet i IoT Hub och hämta dess anslutnings sträng, beroende på din preferens.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Krav för Azure Portal

En kostnads fri eller standard [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Skapa en IoT Edge enhet i Azure Portal

I IoT Hub i Azure Portal skapas och hanteras IoT Edge enheter separat från IOT-enheter som inte är Edge-aktiverade.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. Välj **IoT Edge** på menyn i den vänstra rutan och välj sedan **lägg till en IoT Edge enhet**.

   ![Lägg till en IoT Edge-enhet från Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. På sidan **skapa en enhet** anger du följande information:

   * Skapa ett beskrivande enhets-ID.
   * Välj **symmetrisk nyckel** som autentiseringstyp.
   * Använd standardinställningarna för att automatiskt generera autentiseringsinställningar och Anslut den nya enheten till hubben.

1. Välj **Spara**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visa IoT Edge enheter i Azure Portal

Alla Edge-aktiverade enheter som ansluter till din IoT Hub visas på sidan **IoT Edge** .

![Använd Azure Portal för att visa alla IoT Edge enheter i din IoT Hub](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Hämta anslutnings strängen i Azure Portal

När du är redo att konfigurera din enhet behöver du anslutnings strängen som länkar din fysiska enhet med dess identitet i IoT Hub.

1. På sidan **IoT Edge** i portalen klickar du på enhets-ID i listan över IoT Edge enheter.
2. Kopiera värdet för antingen **primär anslutnings sträng** eller **sekundär anslutnings sträng**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Krav för Visual Studio Code

* En kostnads fri eller standard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Logga in för att få åtkomst till din IoT Hub

Du kan använda Azure IoT-tillägg för Visual Studio Code för att utföra åtgärder med IoT Hub. För att dessa åtgärder ska fungera måste du logga in på ditt Azure-konto och välja IoT Hub.

1. Öppna vyn **Utforskare** i Visual Studio Code.
1. Expandera avsnittet **Azure IoT Hub** längst ned i Utforskaren.

   ![Avsnittet Expandera Azure IoT Hub-enheter](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. Klicka på **...** i avsnitts rubriken för **Azure IoT Hub** . Om du inte ser ellipsen klickar du på eller hovrar över sidhuvudet.
1. Välj **välj IoT Hub**.
1. Om du inte är inloggad på ditt Azure-konto följer du anvisningarna för att göra det.
1. Välj din Azure-prenumeration.
1. Välj din IoT-hubb.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Skapa en IoT Edge-enhet med Visual Studio Code

1. I VS Code-Utforskaren expanderar du avsnittet **Azure IoT Hub-enheter** .
1. Klicka på avsnittet **...** i avsnittet om **Azure IoT Hub-enheter** . Om du inte ser ellipsen klickar du på eller hovrar över sidhuvudet.
1. Välj **skapa IoT Edge enhet**.
1. Ge din enhet ett ID i text rutan som öppnas.

På skärmen utdata ser du resultatet av kommandot. Enhets informationen skrivs ut, som innehåller den **deviceId** som du har angett och **ConnectionString** som du kan använda för att ansluta den fysiska enheten till IoT-hubben.

På skärmen utdata ser du resultatet av kommandot. Enhets informationen skrivs ut, som innehåller den **deviceId** som du har angett och **ConnectionString** som du kan använda för att ansluta den fysiska enheten till IoT-hubben.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visa IoT Edge enheter med Visual Studio Code

Alla enheter som ansluter till din IoT Hub visas i avsnittet **Azure IoT Hub** i Visual Studio Code Explorer. IoT Edge enheter kan särskiljas från icke-Edge-enheter med en annan ikon, och det faktum att modulerna **$edgeAgent** och **$edgeHub** distribueras till varje IoT Edge enhet.

![Använd VS Code för att visa alla IoT Edge enheter i din IoT Hub](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Hämta anslutnings strängen med Visual Studio Code

När du är redo att konfigurera din enhet behöver du anslutnings strängen som länkar din fysiska enhet med dess identitet i IoT Hub.

1. Högerklicka på enhetens ID i **Azure IoT Hub** -avsnittet.
1. Välj **Kopiera enhets anslutnings sträng**.

   Anslutnings strängen kopieras till Urklipp.

Du kan också välja **Hämta enhets information** på snabb menyn för att se all enhets information, inklusive anslutnings strängen, i fönstret utdata.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Krav för Azure CLI

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) i din miljö. Din Azure CLI-version måste vara minst 2.0.70 eller senare. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Skapa en IoT Edge-enhet med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) för att skapa en ny enhets identitet i din IoT-hubb. Till exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

Det här kommandot innehåller tre parametrar:

* `--device-id` eller `-d` : Ange ett beskrivande namn som är unikt för din IoT-hubb.
* `hub-name` eller `-n` : Ange namnet på din IoT-hubb.
* `--edge-enabled` eller `--ee` : deklarera att enheten är en IoT Edge enhet.

   ![AZ IoT Hub-enhet – identitet skapa utdata](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visa IoT Edge enheter med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity List](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) för att visa alla enheter i din IoT Hub. Till exempel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Alla enheter som har registrerats som en IoT Edge enhet har egenskaps **funktionerna. iotEdge** inställt på **True**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Hämta anslutnings strängen med Azure CLI

När du är redo att konfigurera din enhet behöver du anslutnings strängen som länkar din fysiska enhet med dess identitet i IoT Hub. Använd kommandot [AZ IoT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) för att returnera anslutnings strängen för en enskild enhet:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

Värdet för `device-id` parametern är Skift läges känsligt. Kopiera inte citat tecknen runt anslutnings strängen.

---

## <a name="provision-an-iot-edge-device"></a>Etablera en IoT Edge-enhet

När IoT Edge-enheten har en identitet i IoT Hub och en anslutnings sträng som den kan använda för autentisering måste du etablera enheten med den här informationen.

På en Linux-enhet anger du anslutnings strängen genom att redigera en config. yaml-fil. På en Windows-enhet anger du anslutnings strängen genom att köra ett PowerShell-skript.

# <a name="linux"></a>[Linux](#tab/linux)

Öppna konfigurations filen på den IoT Edge enheten.

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till den **manuella etablerings konfigurationen med hjälp av en anslutnings sträng** avsnitt. 

Uppdatera värdet för **device_connection_string** med anslutnings strängen från IoT Edge enheten. Se till att alla andra etablerings avsnitt är kommenterade. Se till att **etableringen:** raden inte har några föregående blank steg och att kapslade objekt är indragna med två blank steg.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

Klistra in innehållet i Urklipp i nano `Shift+Right Click` eller tryck på `Shift+Insert` .

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen startar du om daemonen:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Kör PowerShell som administratör på IoT Edge-enheten.

2. Använd kommandot [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) för att konfigurera IoT Edge runtime på din dator. Kommandot är standardvärdet för manuell etablering med Windows-behållare.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Om du använder Linux-behållare lägger du till `-ContainerOs` parametern i flaggan. Var konsekvent med det behållar alternativ som du valde med det `Deploy-IoTEdge` kommando som du körde tidigare.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Om du har laddat ned IoTEdgeSecurityDaemon.ps1-skriptet till enheten för offline eller en speciell versions installation, måste du se till att referera till den lokala kopian av skriptet.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. När du uppmanas till det anger du enhets anslutnings strängen som du hämtade i föregående avsnitt. Enhets anslutnings strängen kopplar den fysiska enheten med ett enhets-ID i IoT Hub och ger autentiseringsinformation.

   Enhets anslutnings strängen har följande format och ska inte innehålla citat tecken: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

När du etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra processen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Deklarera en speciell avbildning av edgeAgent-behållaren och ange autentiseringsuppgifter om den finns i ett privat register

Mer information om dessa ytterligare parametrar finns i [PowerShell-skript för IoT Edge i Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt att [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md) och lär dig hur du distribuerar moduler till din enhet.
