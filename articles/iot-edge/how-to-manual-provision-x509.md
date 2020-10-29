---
title: Etablera med X. 509-certifikat – Azure IoT Edge | Microsoft Docs
description: Efter installationen etablerar du en IoT Edge enhet med sina enhets identitets certifikat och autentiserar till IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: abb3aa9ca7c9697fef1cf456964154249f0d69f3
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913984"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Konfigurera en Azure IoT Edge-enhet med X. 509 certifikatautentisering

Den här artikeln innehåller anvisningar för att registrera en ny IoT Edge enhet i IoT Hub och konfigurera enheten för att autentisera med X. 509-certifikat.

Stegen i den här artikeln vägleder dig genom en process som kallas manuell etablering, där du ansluter varje enhet till IoT Hub manuellt. Alternativet är automatisk etablering med hjälp av IoT Hub Device Provisioning Service, vilket är användbart när du har många enheter att etablera.

<!--TODO: Add auto-provision info/links-->

För manuell etablering har du två alternativ för att autentisera IoT Edge enheter:

* **Symmetrisk nyckel** : när du skapar en ny enhets identitet i IoT Hub skapar tjänsten två nycklar. Du placerar en av nycklarna på enheten, och den presenterar nyckeln för att IoT Hub vid autentisering.

  Den här autentiseringsmetoden är snabbare för att komma igång, men inte lika säkert.

* **X. 509-egensignerat** : du skapar två X. 509-identitets certifikat och placerar dem på enheten. När du skapar en ny enhets identitet i IoT Hub ger du tumavtrycken från båda certifikaten. När enheten autentiserar till IoT Hub, visar den sina certifikat och IoT Hub kan kontrol lera att de matchar tumavtrycken.

  Den här autentiseringsmetoden är säkrare och rekommenderas för produktions scenarier.

Den här artikeln beskriver registrerings-och etablerings processen med X. 509 certifikatautentisering. Om du vill lära dig hur du konfigurerar en enhet med symmetriska nycklar kan du läsa [Konfigurera en Azure IoT Edge enhet med symmetrisk nyckel autentisering](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du följer anvisningarna i den här artikeln bör du ha en enhet med IoT Edge runtime installerad. Annars följer du stegen i [Installera eller avinstallera Azure IoT Edge runtime](how-to-install-iot-edge.md).

Manuell etablering med X. 509-certifikat kräver IoT Edge version 1.0.10 eller senare.

## <a name="create-certificates-and-thumbprints"></a>Skapa certifikat och tumavtrycken

Enhetens identitets certifikat är ett löv certifikat som ansluter via en certifikat kedja till det översta X. 509 certifikat utfärdarens certifikat (CA). Enhetens identitets certifikat måste ha sitt eget namn (CN) inställt på det enhets-ID som du vill att enheten ska ha i IoT-hubben.

Enhets identitets certifikat används bara för att tillhandahålla IoT Edge enheten och autentisera enheten med Azure IoT Hub. De signerar inte certifikaten, till skillnad från de CA-certifikat som den IoT Edge enheten presenterar för moduler eller löv enheter för verifiering. Mer information finns i avsnittet [Azure IoT Edge information om certifikat användning](iot-edge-certs.md).

När du har skapat enhets identitets certifikatet bör du ha två filer: en. cer-eller. PEM-fil som innehåller den offentliga delen av certifikatet och en CER-eller PEM-fil med certifikatets privata nyckel.

Du behöver följande filer för manuell etablering med X. 509:

* Två uppsättningar enhets identitets certifikat och certifikat för privat nyckel. En uppsättning certifikat/nyckelfiler ges till IoT Edge Runtime.
* Tumavtrycken tas från både enhets identitets certifikat. Tumavtryck-värden är 40-hex-tecken för SHA-1-hashar eller 64-hex-tecken för SHA-256-hashar. Båda tumavtrycken tillhandahålls för att IoT Hub vid tidpunkten för enhets registrering.

Om du inte har tillgängliga certifikat kan du [skapa demonstrations certifikat för att testa IoT Edge enhets funktioner](how-to-create-test-certificates.md). Följ anvisningarna i artikeln för att konfigurera skript för att skapa certifikat, skapa ett rot certifikat för certifikat utfärdare och skapa sedan två IoT Edge enhets identitets certifikat.

Ett sätt att hämta tumavtrycket från ett certifikat är med följande openssl-kommando:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

## <a name="register-a-new-device"></a>Registrera en ny enhet

Alla enheter som ansluter till en IoT Hub har ett enhets-ID som används för att spåra kommunikation från moln till enhet eller från enhet till moln. Du konfigurerar en enhet med dess anslutnings information som innehåller IoT Hub-värdnamnet, enhets-ID: t och den information som enheten använder för att autentisera till IoT Hub.

För X. 509-certifikatautentisering tillhandahålls den här informationen i form av *tumavtrycken* som tas från dina enhets identitets certifikat. Dessa tumavtrycken ges för att IoT Hub vid tidpunkten för enhets registrering så att tjänsten kan identifiera enheten när den ansluts.

Du kan använda flera verktyg för att registrera en ny IoT Edge enhet i IoT Hub och ladda upp certifikatets tumavtrycken.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Krav för Azure Portal

En kostnads fri eller standard [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Skapa en IoT Edge enhet i Azure Portal

I IoT Hub i Azure Portal skapas och hanteras IoT Edge enheter separat från IOT-enheter som inte är Edge-aktiverade.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT-hubb.

1. Välj **IoT Edge** på menyn i den vänstra rutan och välj sedan **lägg till en IoT Edge enhet** .

   ![Lägg till en IoT Edge-enhet från Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. På sidan **skapa en enhet** anger du följande information:

   * Skapa ett beskrivande enhets-ID. Anteckna det här enhets-ID: t eftersom du kommer att använda det i nästa avsnitt.
   * Välj **X. 509 själv signerad** som autentiseringstyp.
   * Ange det primära och sekundära identitets certifikatets tumavtrycken. Tumavtryck-värden är 40-hex-tecken för SHA-1-hashar eller 64-hex-tecken för SHA-256-hashar.

1. Välj **Spara** .

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Visa IoT Edge enheter i Azure Portal

Alla Edge-aktiverade enheter som ansluter till din IoT Hub visas på sidan **IoT Edge** .

![Visa alla IoT Edge enheter i din IoT-hubb](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Krav för Azure CLI

* En [IoT-hubb](../iot-hub/iot-hub-create-using-cli.md) i din Azure-prenumeration.
* [Azure CLI](/cli/azure/install-azure-cli) i din miljö. Som minst måste din Azure CLI-version vara 2.0.70 eller högre. Validera med `az --version`. Den här versionen har stöd för az-tilläggskommandon och introducerar kommandoramverket Knack.
* [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Skapa en IoT Edge-enhet med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) för att skapa en ny enhets identitet i din IoT-hubb. Till exempel:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

Det här kommandot innehåller flera parametrar:

* `--device-id` eller `-d` : Ange ett beskrivande namn som är unikt för din IoT-hubb. Anteckna det här enhets-ID: t eftersom du kommer att använda det i nästa avsnitt.
* `hub-name` eller `-n` : Ange namnet på din IoT-hubb.
* `--edge-enabled` eller `--ee` : deklarera att enheten är en IoT Edge enhet.
* `--auth-method` eller `--am` : deklarera den typ av auktorisering som enheten ska använda. I det här fallet använder vi X. 509-certifikat tumavtrycken.
* `--primary-thumbprint` eller `--ptp` : Ange ett X. 509-tumavtryck för certifikat som ska användas som primär nyckel.
* `--secondary-thumbprint` eller `--stp` : Ange ett X. 509-tumavtryck för certifikat som ska användas som sekundär nyckel.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Visa IoT Edge enheter med Azure CLI

Använd kommandot [AZ IoT Hub Device-Identity List](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) för att visa alla enheter i din IoT Hub. Till exempel:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Lägg till flaggan `--edge-enabled` eller `--ee` för att endast Visa IoT Edge enheter i din IoT-hubb.

Alla enheter som har registrerats som en IoT Edge enhet har egenskaps **funktionerna. iotEdge** inställt på **True** .

--- 

## <a name="configure-an-iot-edge-device"></a>Konfigurera en IoT Edge-enhet

När IoT Edge-enheten har en identitet i IoT Hub måste du konfigurera enheten med dess moln identitet samt dess identitets certifikat.

På en Linux-enhet anger du den här informationen genom att redigera en config. yaml-fil. På en Windows-enhet anger du den här informationen genom att köra ett PowerShell-skript.

# <a name="linux"></a>[Linux](#tab/linux)

1. Öppna konfigurations filen på den IoT Edge enheten.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Hitta konfigurations avsnittet för etablering i filen. 

1. Kommentera ut **konfigurationen för manuell etablering med hjälp av avsnittet anslutnings sträng** .

1. Ta bort kommentaren till den **manuella etablerings konfigurationen med ett X. 509 Identity Certificate** -avsnitt. Se till att **etableringen:** raden inte har några föregående blank steg och att kapslade objekt är indragna med två blank steg.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Uppdatera följande fält:

   * **iothub_hostname** : värd namnet för den IoT-hubb som enheten ska ansluta till. Till exempel `{IoT hub name}.azure-devices.net`.
   * **device_id** : det ID som du angav när du registrerade enheten.
   * **identity_cert** : URI till ett identitets certifikat på enheten. Till exempel `file:///path/identity_certificate.pem`.
   * **identity_pk** : URI till den privata nyckel filen för det tillhandahållna identitets certifikatet. Till exempel `file:///path/identity_key.pem`.

1. Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

1. När du har angett etablerings informationen i konfigurations filen startar du om daemonen:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. Kör PowerShell som administratör på IoT Edge-enheten.

2. Använd kommandot [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) för att konfigurera IoT Edge runtime på din dator.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Om du använder Linux-behållare lägger du till `-ContainerOs` parametern i flaggan. Var konsekvent med det behållar alternativ som du valde med det `Deploy-IoTEdge` kommando som du körde tidigare.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Om du har laddat ned IoTEdgeSecurityDaemon.ps1-skriptet till enheten för offline eller en speciell versions installation, måste du se till att referera till den lokala kopian av skriptet.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. När du uppmanas anger du följande information:

   * **IotHubHostName** : värd namnet för den IoT-hubb som enheten ska ansluta till. Till exempel `{IoT hub name}.azure-devices.net`.
   * **DeviceID** : det ID som du angav när du registrerade enheten.
   * **X509IdentityCertificate** : absolut sökväg till ett identitets certifikat på enheten. Till exempel `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey** : absolut sökväg till den privata nyckel filen för det tillhandahållna identitets certifikatet. Till exempel `C:\path\identity_key.pem`.

När du etablerar en enhet manuellt kan du använda ytterligare parametrar för att ändra processen, inklusive:

* Direkt trafik för att gå igenom en proxyserver
* Deklarera en speciell avbildning av edgeAgent-behållaren och ange autentiseringsuppgifter om den finns i ett privat register

Mer information om dessa ytterligare parametrar finns i [PowerShell-skript för IoT Edge i Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Nästa steg

Fortsätt att [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md) och lär dig hur du distribuerar moduler till din enhet.